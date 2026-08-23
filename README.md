# Wumpus

Libera a transmissão de tela do Discord no Brasil, sem deixar você com ping
estrangeiro.

## Como funciona

O Discord decide se libera a transmissão de tela **uma vez, no momento em que
o programa abre**. Depois disso a VPN pode cair — você continua transmitindo
normalmente.

O Wumpus automatiza exatamente essa janela: sobe um túnel, reinicia o Discord
por baixo dele, confirma que ele conectou pelo túnel, e derruba o túnel.

**O ciclo inteiro leva cerca de 12 segundos**, e acontece sozinho no boot. O
resto do tempo sua conexão é a normal, com o ping de sempre.

## Instalação

1. Baixe o `Wumpus.exe` na [página de releases](https://github.com/zxOrion/Wumpus/releases)
2. Abra e clique em **Configurar agora**
   - Ele instala o WireGuard sozinho (confirme a janela do Windows)
   - Siga o passo indicado para obter sua config
   - O download da config é detectado automaticamente
3. Marque ☑ **Iniciar com o Windows**

A partir do próximo boot funciona sozinho.

### Você precisa de uma conta de VPN própria

O **Proton VPN tem plano gratuito** e o cadastro leva uns 2 minutos — é
suficiente, porque o programa usa o túnel por poucos segundos de cada vez.

Não distribuo uma config pronta de propósito: o arquivo `.conf` contém uma
chave privada. Uma config compartilhada publicamente vira um túnel aberto
para qualquer um, no nome de quem paga a conta — e, como contas gratuitas
permitem só uma conexão simultânea, basta uma pessoa segurando a conexão
para o programa parar de funcionar para todo mundo.

Serve qualquer provedor que gere `.conf` do WireGuard: **Proton** (grátis),
**Windscribe** (10 GB/mês grátis), **Mullvad** (pago, sem cadastro).

## Atualizações

Abra o programa, clique em **sobre** (no rodapé) e depois em **Procurar
atualizações**. Se houver versão nova, ele mostra o que mudou, baixa, confere
a integridade do arquivo e se substitui sozinho — reabrindo em seguida.

Suas configurações e sua config de VPN são preservadas: elas ficam em
`%LOCALAPPDATA%\DiscordTunnelBoot`, fora do executável.

## Uso

Depois de instalado, nada. O Discord abre sozinho no boot já desbloqueado.

Para rodar na hora — depois de reiniciar o Discord manualmente, por exemplo —
abra o programa e ligue o botão principal.

## Se algo der errado

O túnel **sempre** cai, por cinco caminhos independentes: `try/finally`,
sinais, `atexit`, um processo guardião que vigia se o principal morrer à
força, e uma reconciliação no boot seguinte.

Se ainda assim travar:

```powershell
Wumpus.exe recover
```

**Logs:** clique em "logs" no rodapé da janela, ou vá em
`%LOCALAPPDATA%\DiscordTunnelBoot\logs`.

O log explica *por que* falhou. A linha mais útil é:

```
laddr mismatch: 192.168.15.51 != 10.2.0.5
```

Significa que o Discord conectou, mas pela conexão normal em vez do túnel —
geralmente porque outra VPN (Cloudflare WARP, Proton, Surfshark) capturou a
rota. Desligue as outras VPNs antes de rodar.

### Limitação conhecida

A negociação de vídeo acontece quando a call começa, horas depois do túnel ter
caído e fora do alcance do programa. Ocasionalmente isso causa live com tela
preta e áudio funcionando (erro 2012). Reiniciar o Discord resolve.

Não é corrigível sem manter o túnel ativo durante a call — o que traria de
volta o ping estrangeiro que o programa existe para evitar.

## Comandos (opcional)

A janela cobre o uso normal. Para diagnóstico:

| Comando | O que faz |
|---|---|
| `Wumpus.exe` | Abre a janela |
| `... doctor` | Diagnóstico completo do ambiente |
| `... run` | Executa o fluxo agora |
| `... run --dry-run` | Testa tudo sem mexer em rede |
| `... recover` | Destrava túnel preso |
| `... uninstall` | Remove o autostart |

## Ajustes finos

Crie `%LOCALAPPDATA%\DiscordTunnelBoot\config.toml`:

```toml
[detect]
settle_after_detect_s = 45   # padrão 20 — aumente se o desbloqueio falhar
timeout_s = 90               # padrão 60 — aumente se o boot for lento

[safety]
abort_if_other_vpn_active = true   # aborta se detectar outra VPN ativa
```

---

## Para desenvolvedores

```powershell
# rodar do fonte
python -m discord_tunnel_boot gui

# compilar
python build.py                 # -> dist\Wumpus.exe  (para a release)
python build.py --com-configs   # -> dist\Wumpus-pessoal.exe  (uso próprio)

# testes
python testes\test_versoes.py
python testes\test_rede.py
python testes\test_recuperacao.py
```

Encerre todos os processos Wumpus antes de compilar, ou o `.exe` fica travado
e o build falha com `PermissionError`.

**Estrutura:**

| Módulo | Responsabilidade |
|---|---|
| `orchestrator.py` | Máquina de estados, restauração em camadas |
| `detector.py` | Confirma conexão **através do túnel** (via `LocalAddress`) |
| `discord.py` | Localiza, encerra e lança a árvore Electron |
| `tunnel/` | Driver abstrato + WireGuard + dry-run |
| `guardian.py` | Watchdog externo contra `TerminateProcess` |
| `updater.py` | Atualização pelo GitHub Releases |
| `conf_pool.py` | Rodízio entre as configs disponíveis |
| `state_file.py` | Journal atômico |
| `setup_wizard.py` | Instalação do WireGuard + import do `.conf` |
| `gui.py` | Janela tkinter |
| `paths.py` | Resolução portátil de caminhos |

**Detalhes que não são óbvios:**

- A detecção compara o `LocalAddress` da conexão com o IP do túnel. Sem isso,
  o Discord conectando pela Ethernet daria falso positivo.
- O Discord precisa ser encerrado **antes** de subir o túnel, senão as
  conexões antigas (fora do túnel) enganam o detector.
- `DiscordSystemHelper.exe` é processo separado e **não** deve ser morto.
- A raiz da árvore Electron é identificada pela cmdline **sem `--type=`** — o
  processo pai já morreu, então parentesco não serve.
- O autostart usa Task Scheduler com `RunLevel=Highest`, não o registro Run:
  o registro não roda elevado, e o túnel exige admin.
- O guardião sobrevive ao programa por até 300s e **trava o `.exe`**. Por isso
  o updater encerra quem segura o arquivo antes de substituí-lo.
- As configs são copiadas para `%LOCALAPPDATA%\...\wg\pool\` na primeira
  execução, e a partir daí o programa ignora o que está embutido no `.exe` —
  é o que permite atualizar o binário sem perder o pool.
