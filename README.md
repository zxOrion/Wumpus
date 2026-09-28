# Wumpus

Traz de volta a transmissão de tela do Discord no Brasil, sem deixar você com
ping estrangeiro.

Você abre o Discord pelo Wumpus, e o botão de transmitir aparece. Seus jogos,
downloads e o resto da internet continuam com a sua conexão normal.

---

## Download

Baixe o **`Wumpus.exe`** na [página de releases](https://github.com/zxOrion/Wumpus/releases/latest)
e abra. Não tem instalador: na primeira vez ele se instala sozinho e deixa um
atalho na Área de Trabalho.

**Requisitos:** Windows 10 ou 11 e o Discord normal (não o PTB nem o Canary).

### Avisos do Windows na primeira vez

- **"O Windows protegeu o computador"**: clique em **Mais informações** →
  **Executar assim mesmo**. Aparece porque o programa não tem assinatura
  digital paga, e não porque tenha algo errado.
- **Pedido de permissão de administrador**: aceite. O Wumpus precisa dela para
  cuidar da conexão do Discord.
- **Antivírus**: alguns antivírus desconfiam de programas pequenos e sem
  assinatura. Se o seu bloquear, adicione o Wumpus às exceções.
- **WireGuard**: se o Wumpus pedir o WireGuard, instale pelo link que ele
  mostra. É um componente gratuito e oficial.

---

## Como usar

1. **Abra o Discord pelo Wumpus.** Se o Discord já estiver aberto, o Wumpus
   oferece fechar e reabrir.
2. **Antes de cada live**, seja para começar a sua ou para assistir a de
   alguém, clique em **Liberar live agora** ou aperte **Ctrl + Alt + L** de
   dentro do Discord.

A liberação vale por live, porque o Discord confere de novo sempre que uma
transmissão começa. Saiu de uma e vai entrar em outra? Aperte o atalho de novo.

O atalho pode ser trocado em **atalho**, no rodapé da janela.

### Iniciar com o Windows

Ligue a opção **Iniciar com o Windows** na janela. O Discord já abre
desbloqueado quando o PC liga, e o Wumpus fica recolhido na bandeja, perto do
relógio.

### Se o Discord recarregar

Deu **Ctrl + R** no Discord, ou o PC voltou da suspensão? O Wumpus percebe e
deixa o Discord desbloqueado de novo sozinho. Não precisa reabrir nada.

---

## Atualizações

O Wumpus avisa quando sai versão nova. Para procurar na mão, vá em **sobre** →
**Procurar atualizações**. Ele baixa, confere a integridade do arquivo e reabre
sozinho.

**O Discord continua aberto durante a atualização**, então dá para atualizar
no meio de uma call. Suas configurações são mantidas.

---

## Privacidade

- O Wumpus **não coleta dados** e não lê o que você digita.
- Para liberar, a conexão do Discord passa alguns segundos por um servidor no
  exterior. Ela é criptografada: o servidor não vê suas mensagens nem sua
  senha.
- Nada além do Discord passa pelo Wumpus.

---

## Problemas comuns

**O botão de transmitir não apareceu.**
Clique em **Fechar e reabrir o Discord** na janela do Wumpus.

**A live abriu com tela preta, mas com som.**
Aperte o atalho (ou **Liberar live agora**) e entre na live de novo.

**O Wumpus avisa que há outra VPN ligada.**
Desligue a outra VPN (Cloudflare WARP, Proton, Surfshark etc.) enquanto usa o
Wumpus. As duas brigam pela conexão.

**Nada disso resolveu.**
Clique em **logs** no rodapé da janela e mande o arquivo mais recente para
quem te passou o programa.

### Usar a sua própria VPN

Se você já tem uma VPN com suporte a WireGuard, ligue **Usar a minha própria
VPN** e importe o arquivo `.conf` dela (preferencialmente o da ProtonVPN). O Wumpus passa a usar só a sua.

---

## Desinstalar

Vá em **sobre** → **Desinstalar**. O Wumpus remove a inicialização automática,
o atalho e os próprios arquivos, e deixa o Discord como era antes.

---

## Apoie o projeto

O Wumpus é gratuito e feito no meu tempo livre. Se ele te ajuda, tem um botão
**Apoiar o projeto** no rodapé da janela, com Pix. Qualquer valor ajuda, e
nenhum é obrigatório.

---

<sub>Feito por **Orion**. Discord é marca da Discord Inc.</sub>
