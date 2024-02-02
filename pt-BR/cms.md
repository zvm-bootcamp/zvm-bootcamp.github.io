---
order: 90
icon: terminal
---
# CMS

## Introdução

Assim como você pode interagir com Linux ou UNIX® através de um shell bash ou Korn (shell Unix popular), você pode interagir com o z/VM através do CMS. Da mesma forma que um shell, você pode usar o CMS para editar arquivos, executar EXECs (arquivos executáveis semelhantes a scripts) ou programas, modificar o ambiente da máquina virtual, ou modificar o próprio z/VM. O CMS é para o z/VM como um shell é para o Linux ou UNIX.

## HELP

O z/VM fornece ajuda online através do sistema de Ajuda do CMS. O comando HELP é como o comando man no Linux. Você pode encontrar descrições completas dos comandos z/VM usando o comando HELP.

## Minidisks e modo de acesso CMS

O CMS, como outros sistemas operacionais rodando em uma máquina virtual, pode acessar minidisks para armazenar e recuperar arquivos. Para o CMS, cada minidisk tem um modo de acesso representado por uma letra alfabética que determina como o CMS busca por arquivos. No Linux, variáveis de caminho definindo diretórios determinam a ordem de busca por arquivos. O CMS busca arquivos entre minidisks baseado na ordem alfabética do modo de acesso. Primeiro, o CMS procura no minidisk A, depois no minidisk B, e assim por diante. O minidisk 191 tem um lugar especial no CMS. Um minidisk 191 para um usuário CMS é como o diretório de arquivos principal para um usuário Linux. O CMS sempre tenta acessar o minidisk 191 de um usuário como modo de acesso A. O minidisk 191 do CMS é frequentemente chamado de "A-disk".

## Arquivos CMS

Arquivos CMS possuem um nome de arquivo (file name), tipo de arquivo (file type) e modo de arquivo (file mode). Nomes e tipos de arquivos podem ter até 8 caracteres de comprimento. O file mode corresponde ao modo de acesso do minidisk. Exemplos:


```
PROFILE  EXEC    A1
MYDOC    LISTING A1
DNFPFS   LISTPS  B1
```

Por convenção, alguns tipos de arquivos têm significados especiais. Por exemplo, EXEC é o file type para um arquivo que contém declarações executáveis, LISTING é o file type para arquivos de texto e LISTPS é o file type para arquivos PostScript. Para visualizar e manipular arquivos, use o comando FILELIST (ou comando LISTFILE). FILELIST é semelhante ao comando dir no Linux.

## PROFILE EXEC

O PROFILE EXEC é um arquivo executável especial análogo ao .profile (ou .bash_profile) no Linux e UNIX. Toda vez que um usuário CMS faz login, o CMS executa o PROFILE EXEC que reside no minidisk 191, file mode A. Você pode usar o PROFILE EXEC para configurar o ambiente da sua máquina virtual; por exemplo, acessar discos, configurar teclas PF especiais ou até mesmo carregar outro sistema operacional em sua máquina virtual.

Pode haver momentos em que você não deseja que o PROFILE EXEC seja executado ao fazer login. Por exemplo, suponha que seu PROFILE EXEC carrega automaticamente o Linux. Se você acabou de desligar o Linux e deseja iniciar o CMS, mas impedir que o Linux seja carregado novamente, você pode impedir que o CMS execute o PROFILE EXEC emitindo **access (noprof**. Ao carregar (IPL) o CMS, você vê uma linha identificadora exibida e o CMS pausa com VM READ no canto inferior direito da tela.

Exemplo (fazendo login com o parâmetro NOPROF)


```
z/VM ONLINE                                                                    
                                                                                
                                                                                
                                                                                
                                 / VV          VVV MM       MM                  
                                /  VV        VVV  MMM     MMM                   
                    ZZZZZZ     /   VV      VVV   MMMM   MMMM                    
                       ZZ     /    VV    VVV    MM MM MM MM                     
                      ZZ     /     VV  VVV     MM  MMM  MM                      
                     ZZ     /      VVVVV      MM   M   MM                       
                    ZZ     /       VVV       MM       MM                        
                   ZZZZZZ /        V        MM       MM                         
                                                                                
                     built on IBM Virtualization Technology                     
                                                                                
                                                                                
                                                                                
 Fill in your USERID and PASSWORD and press ENTER                               
 (Your password will not appear when you type it)                               
 USERID   ===>  LINUX1                                                          
 PASSWORD ===>                                                                  
                                                                                
 COMMAND  ===>  ACCESS (NOPROF                                                  
                                                            RUNNING   ZVMWSXX   
```

## O editor de arquivos CMS XEDIT

O CMS fornece um editor de arquivos chamado XEDIT, que não é apenas um editor de tela cheia, mas também uma poderosa ferramenta de programação. O XEDIT possui funções semelhantes ao vi no Linux.

## Exercícios

### HELP

Faça login como MAINT. Pressione a tecla enter até ver o estado RUNNING no canto inferior direito da tela.

Para ver uma lista dos comandos CMS:


```
===> help 
```

Coloque o cursor em "CMS" e pressione a tecla ENTER

O que você vê aqui é um subconjunto dos comandos CMS disponíveis no z/VM.

Exiba o texto de ajuda para o comando QUERY. Você pode ter que rolar para baixo (F8) para encontrá-lo.

Coloque o cursor em "Query" e pressione a tecla ENTER

A parte inferior da tela lista as teclas PF para opções adicionais que estão disponíveis para o comando selecionado. Para ver informações relacionadas a este comando:

Pressione a tecla F11

Quando terminar de navegar, saia da utilidade de ajuda.

Pressione a tecla F3 repetidamente até retornar ao estado "RUNNING"

Outra maneira de ir diretamente para o menu de comandos CMS é com o seguinte comando de ajuda:

```
===> help cms menu 
```

Saia da tela de ajuda e retorne ao prompt READY: Pressione a tecla F3 repetidamente até retornar ao estado "RUNNING"

### QUERY

O comando QUERY do CMS é usado para exibir informações sobre sua máquina virtual. Você pode obter informações sobre:

- A operação da sua máquina virtual.
- O status dos seus arquivos e diretórios de pool de arquivos.
- Informações sobre como sua máquina virtual está configurada.

Use o comando QUERY ACCESSED para exibir o status dos seus discos acessados. Note que o disco 191 é acessado como "A".

```
===> q accessed
```

```
q userid                                    
MAINT    AT ZVMWSXX                         
Ready; T=0.01/0.01 18:42:50                 

q accessed                                  
Mode  Stat     Files  Vdev  Label/Directory 
A      R/W         3  191   MNT191          
B      R/O       134  5E5   MNT5E5          
C      R/W         4  2CC   MNT2CC          
D      R/O       293  51D   MNT51D          
E      R/O         8  551   PMT551          
S      R/O       696  190   MNT190          
Y/S    R/O      1123  19E   MNT19E          
Ready; T=0.01/0.01 18:42:54                 
```

Use o comando QUERY IMPCP para descobrir a configuração da função CP implícita para sua máquina virtual.

```
===> q impcp
```

*Para a máquina virtual MAINT, a função IMPCP está configurada para:* ____

Você aprenderá mais sobre essa função em um momento.

```
set impcp off              
Ready; T=0.01/0.01 14:52:34
q impcp                    
IMPCP    = OFF             
Ready; T=0.01/0.01 14:52:37

set impcp on               
Ready; T=0.01/0.01 14:52:42
q impcp                    
IMPCP    = ON              
Ready; T=0.01/0.01 14:52:44
```

### ACCESS

O comando ACCESS é usado para:

- Identificar um minidisk para o CMS.
- Tornar uma lista dos arquivos no minidisk especificado ou diretório disponível para sua máquina virtual.
- Estabelecer uma letra de file mode para os arquivos em um minidisk ou em um diretório.

Use o comando ACCESS para acessar o minidisk 191 como "Z".

```
===> access 191 z
```

O que aconteceu com o A-disk?

Quais implicações essa mudança tem?

Reacesso ao minidisk 191 como "A". Qual é o comando? ===>

```
q disk                                                                          
LABEL  VDEV M  STAT   CYL TYPE BLKSZ   FILES  BLKS USED-(%) BLKS LEFT  BLK TOTAL
MNT191 191  A   R/W   175 3390 4096       27        172-01      31328      31500
MNT5E6 5E6  B   R/W     9 3390 4096      134       1304-80        316       1620
MNT2CC 2CC  C   R/W    10 3390 4096        3        104-06       1696       1800
MNT51D 51D  D   R/W    26 3390 4096      293       1796-38       2884       4680
PMT551 551  E   R/W    40 3390 4096       10        119-02       7081       7200
MNT190 190  S   R/O   207 3390 4096      696      18085-49      19175      37260
MNT19E 19E  Y/S R/O   500 3390 4096     1123      30403-34      59597      90000
Ready; T=0.01/0.01 14:53:37                                                     

access 191 z                                                                    
DMSACP726I 191 A released                                                       
Ready; T=0.01/0.01 14:53:46                                                     
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

### COPYFILE

O comando COPYFILE é usado para copiar e modificar arquivos nos minidisks CMS. Você pode:

- Combinar dois ou mais arquivos em um único arquivo.
- Copiar múltiplos arquivos de entrada em múltiplos arquivos de saída.
- Mudar características do arquivo (como número de file mode e formato de registro) e/ou modificar conteúdos do arquivo.

Faça login como MAINT Copie o arquivo PROFILE EXEC A para um novo arquivo chamado PROFILE EXECSAVE A.

```
===> copy profile exec a = execsave =
```

O que os sinais "=" significam?

### CP

Há várias maneiras de emitir comandos CP no CMS dependendo da configuração da opção CP implícita (IMPCP):

- Se IMPCP estiver configurado para ON, o CMS tentará interpretar um comando "desconhecido" como um comando CP.
- Se IMPCP estiver configurado para OFF, você deve preceder quaisquer comandos CP com "CP"

Ou você pode preceder qualquer comando CP com "#CP" para enviar o comando diretamente ao programa de controle.

Descubra sua configuração de opção IMPCP atual:

```
===> q impcp
```

Deveria dizer 'ON'. Este é o padrão. Significa que, se o CMS não reconhecer um comando, ele será enviado ao CP para processamento. Tente um comando CP (enquanto você está no CMS):

```
===> q dasd
```

Com certeza, o comando funciona e você vê uma lista de DASDs atualmente anexados à sua máquina virtual. Em seguida, defina a opção IMPCP para off:

```
===> set impcp off
```

Tente o mesmo comando CP:

```
===> q dasd
```

Desta vez, o comando falhou. Com a função IMPCP desligada, o CMS não redirecionou o comando desconhecido para o CP para processamento.

Você está atualmente executando no ambiente CMS. Para entrar no modo CP, digite:

```
===> CP
```

O status do console no canto inferior direito da tela agora indica "CP READ".

Tente o mesmo comando CP:

```
===> q dasd
```

Para retornar ao CMS, digite:

```
===> begin (ou somente 'b' para um atalho) 
```

O status no canto inferior direito do console retorna para "RUNNING".

Outra maneira de emitir comando CP é prefixá-lo com '#CP'. Tente isso:

```
===> #cp q dasd 
```

Redefina a opção IMPCP de volta para ON:

```
===> set impcp on
```

### FILELIST

O comando FILELIST é usado para exibir uma lista de informações sobre arquivos CMS residindo em discos acessados. No ambiente FILELIST, as informações são exibidas sob o controle do XEDIT. Você pode usar subcomandos XEDIT paramanipular a própria lista. Você também pode emitir comandos CMS diretamente contra os arquivos a partir da lista exibida.

1. Faça logout como MAINT
2. Faça login como MAINT630
3. Exiba uma lista de arquivos no seu A-disk.

===\> filel \* \* a

Os arquivos são classificados por data e hora, do mais novo para o mais antigo.

```
MAINT630 FILELIST A0  V 169  Trunc=169 Size=27 Line=1 Col=1 Alt=9              
Cmd   Filename Filetype Fm Format Lrecl    Records     Blocks   Date     Time   
*     $VMFP2P  $MSGLOG  A1 V         80        381          6  8/31/13 21:25:26 
      MAINT630 NETLOG   A0 V        104         11          1  8/31/13 21:25:26 
      PUT2PROD $CONS    A1 V        132        361         12  8/31/13 21:25:26 
      SETUP    $LINKS   A1 V         26         67          1  8/31/13 21:25:26 
      $VMFBLD  $MSGLOG  A1 V         80        432          6  8/31/13 21:25:21 
      $VMFMRD  $MSGLOG  A1 V         80        303          5  8/31/13 21:24:57 
      $VMFSRV  $MSGLOG  A1 V         83        821         11  8/31/13 21:24:24 
      SERVICE  $CONS    A1 V        132        790         26  8/31/13 21:24:24 
      $VMFAPP  $MSGLOG  A1 V         80        208          3  8/31/13 21:23:51 
      $VMFINS  $MSGLOG  A1 V         80        198          3  8/31/13 21:23:51 
      6VMPTK30 PSUPLAN  A1 V         72         33          1  8/31/13 21:23:49 
      6VMCPR30 PSUPLAN  A1 V         72         33          1  8/31/13 21:23:26 
      ZCMSLOAD $NUCEXEC A1 F         80       1675         33  8/31/13 21:23:22 
      CMSLOAD  $NUCEXEC A1 F         80       1675         33  8/31/13 21:23:18 
      6VMCMS30 PSUPLAN  A1 V         72         35          1  8/31/13 21:23:07 
      6VMLEN20 PSUPLAN  A1 V         72         33          1  8/31/13 21:22:55 
      6VMSES30 PSUPLAN  A1 V         72         33          1  8/31/13 21:22:47 
      VMFINS   PRODLIST A1 V        101         10          1  8/31/13 21:22:45 
      INS      6301     A1 V         39         14          1  6/28/13 15:01:51 
      6VMCPR30 $INS6301 A1 V          8         10          1  6/28/13 15:01:51 
      6VMPTK30 $INS6301 A1 V          8          7          1  6/28/13 15:01:51 
      6VMCMS30 $INS6301 A1 V          7          8          1  6/28/13 15:01:50 
      6VMSES30 $INS6301 A1 V          7          9          1  6/28/13 15:01:49 
      6VMLEN20 $INS6301 A1 V          7          7          1  6/28/13 15:01:48 
      PROFILE  EXEC     A1 V         71         37          1  4/25/13  8:00:57 
1= Help       2= Refresh  3= Quit   4= Sort(type)  5= Sort(date)  6= Sort(size) 
7= Backward   8= Forward  9= FL /n 10=            11= XEDIT/LIST 12= Cursor     
                                                                                
====>                                                                           
                                                            X E D I T  1 File   
```

Copie o arquivo PROFILE EXEC para PROFILE EXECBKP. Pressione a tecla TAB para avançar o cursor para a coluna CMD do arquivo PROFILE EXEC

Digite "COPY / = EXECBKP =" e pressione a tecla ENTER

O comando pode ser lido como "copie o arquivo atual (/) para um arquivo com o mesmo file name PROFILE (=), um file type EXECBKP e o mesmo file mode A (=)".

Atualize a exibição FILELIST e você deve ver a nova cópia do perfil.

```
===> Press the F2 key 
```

Saia do FILELIST.

```
===> Press the F3 key
```

### XEDIT

#### Configure o PERFIL XEDIT

O comando XEDIT procura pelo arquivo de configuração PERFIL XEDIT quando é invocado. Nem todas as máquinas virtuais CMS têm uma cópia deste arquivo, então as sessões XEDIT parecem e se comportam de maneira diferente. O disco MAINT 191 (A) tem um PERFIL XEDIT então quando você está editando arquivos no MAINT, os valores neste perfil geralmente estão em vigor.

Para configurar o perfil XEDIT no cluster SSI, execute os seguintes passos:


1. Faça login no MAINT se você ainda não fez. 
2. Uma configuração padrão que pode ser perigosa, especialmente se você usar F12 para recuperar comandos, é que PF12 está configurado para o subcomando FILE. Às vezes você pode não querer salvar suas alterações com o toque de uma tecla. É recomendado que você configure PF12 para o subcomando ?, que tem o efeito de uma tecla de recuperação:
 

```
==> copy profile xedit a profile xediorig a (oldd
==> x profile xedit
```

**Antes:**

SET PF12 FILE

**Depois:**

SET PF12 ?

3. Salve suas alterações com o subcomando FILE. 
4. Torne o arquivo modificado disponível para outras máquinas virtuais copiando-o para o disco MAINT 19E com o sufixo de file mode 2: 

      4.1 Libere o disco 19E atual:

```
==> rel 19e
```

      4.2 Link o disco MAINT 19E com acesso de leitura/escrita:

```
==> link * 19e 19e mr
DASD 019E LINKED R/W; R/O BY 10 USERS
```

      4.3 Accesse o disco como file mode F:

```
==> acc 19e f
```

      4.4 Copie para o disco MAINT 19E (F) com o sufixo de file mode 2 (porque o disco MAINT 19E é comumente acessado com um sufixo de file mode de 2, arquivos não serão vistos por outras máquinas virtuais a menos que tenham este sufixo de file mode):

```
==> copy profile xedit a = = f2
```

      4.5 Salve o segmento nomeado do CMS com os seguintes comandos:

```
==> acc 193 g
==> sampnss cms
HCPNSD440I The Named Saved System (NSS) CMS was successfully defined in fileid 0002.
==> ipl 190 parm savesys cms
HCPNSS440I Named Saved System (NSS) CMS was successfully saved in fileid 0002.
```
:::

O mesmo PERFIL XEDIT agora deve ser acessível a todas as máquinas virtuais no sistema z/VM.

#### Criando uma EXEC

Vamos criar um arquivo EXEC REXX simples com uma sessão de edição usando o comando XEDIT.

1. Faça logoff do MAINT630
2. Faça logon como MAINT
3. Inicie uma sessão XEDIT e crie um novo arquivo HELLO EXEC A.

```
===> xedit hello exec
```

4. Entre no modo de entrada e pressione Enter:

```
===> i
```

5. Insira o seguinte texto. Consulte o guia de referência rápida do XEDIT se precisar de ajuda.


```
/* A sample REXX exec */
say 'Welcome to the z/VM and Linux on z Workshop'
say 'Hello World'
```

6. Retorne ao modo de Edição:

Pressione a tecla ENTER duas vezes

7. Salve suas alterações e saia do arquivo.


```
===> file
```

8. Um EXEC REXX é similar a um script Unix/Linux. Vamos executar o exec e ver o que acontece:


```
===> hello
```

9. Agora atualize o arquivo HELLO EXEC:

```
===> x hello exec 
```

10. Insira uma nova linha:

Digite um 'i' na linha número 3

Entre com o texto 'q time'

```
00000 * * * Top of File * * *                             
00001 /* A sample REXX exec */                            
00002 say 'Welcome to the z/VM and Linux on z - Workshop'
00003 'query time'                                        
00004 * * * End of File * * *    
```

11. Execute o arquivo novamente. Você criou com sucesso um arquivo executável para executar comandos e exibir mensagens!

```
===> hello
```

Próximo passo, modifique o arquivo PROFILE EXEC para que você tenha acesso a um disco de utilidades compartilhado que contém scripts úteis para o workshop.

1. Edite o arquivo "PROFILE EXEC".

```
===> x profile exec 
```

2. Adicione as seguintes declarações (em maiúsculas) após a linha 'ACCESS 551 E':

```
'CP ATTACH 0192 *'
'ACCESS 0192 Z'
```

10. Salve suas mudanças e saia do arquivo.

```
===> file
===> profile
```

11. Execute o profile para aplicar as alterações. Você deverá ver uma mensagem informando que o dispositivo 192 está anexado.

```
Ready; T=0.01/0.01 14:25:42                                                     
profile                                                                         
DMSACC724I 5E5 replaces B (5E5)                                                 
DMSACP723I B (5E5) R/O                                                          
DMSACC724I 2CC replaces C (2CC)                                                 
DMSACC724I 51D replaces D (51D)                                                 
DMSACP723I D (51D) R/O                                                          
DMSACC724I 551 replaces E (551)                                                 
DMSACP723I E (551) R/O                                                          
DASD 0192 ATTACHED TO MAINT 0192 WITH DEVCTL                                    
                                                                                
******************************************************************              
                                                                                
THE MAINT630 USER ID **MUST** BE USED INSTEAD OF MAINT                          
WHEN INSTALLING SERVICE.                                                        
                                                                                
******************************************************************              
                                                                                
PRESS ENTER TO CONTINUE                                                         
                                                                                
                                                          VM READ   ZVMWSXX   
```
