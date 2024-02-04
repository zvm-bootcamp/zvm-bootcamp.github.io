---
order: 65
icon: list-unordered
---
# Directory Maintanace

Antes de criar nossa primeira máquina virtual (VM), vamos aprender
sobre DIRMAINT.

## DIRMAINT

IBM Directory Maintenance z/VM (DirMaint) é uma aplicação do Conversational Monitor
System (CMS) que ajuda você a gerenciar seu diretório VM.
A gestão de diretórios é simplificada pela interface de comando e pelas
facilidades automatizadas do DirMaint. Os comandos do DirMaint, que se assemelham a declarações de diretório, são
usados para adicionar, deletar ou alterar o diretório. A verificação de erros do DirMaint
garante que apenas mudanças válidas sejam feitas no diretório, e que apenas
pessoal autorizado seja capaz de fazer as mudanças solicitadas. Qualquer
transação que requer a alocação ou desalocação de extensões de minidiscos
pode ser tratada automaticamente. Todas as transações iniciadas pelo usuário são
controladas por senha e são registradas para fins de auditoria.

Características:

- Controle automatizado do diretório de usuários z/VM através de uma interface de comando 
- Auditoria e controles de acesso ao diretório 
- Verificação de erros & Facilidades automatizadas para alocação, desalocação e cópia de minidiscos. 
- A capacidade de trabalhar em conjunto com gerentes de segurança externos, como RACF.

Existem comandos DirMaint individuais para cada declaração de diretório.

O formato geral de um comando DirMaint é:

```
DIRMaint [ prefix ] command [ cmd_options ]
```

Onde:

*DIRMaint* é o nome do EXEC DirMaint.

*prefixo* é um prefixo de comando opcional e quaisquer operandos necessários para essa palavra-chave.

*comando* é o comando DirMaint.

*opcoes_cmd* são quaisquer opções a serem passadas para o comando.

Exemplo:


```
DIRM for linux1 REView
```


O comando DirMaint fornece a interface entre o emissor do comando e a máquina de serviço DirMaint. O emissor digita o comando DirMaint seguido por um operando de comando. O comando DirMaint encaminha o comando para a máquina de serviço DirMaint onde a máquina de serviço faz a verificação de validação, e processa a solicitação ou a rejeita com uma mensagem apropriada. A solicitação enviada para a máquina de serviço pode incluir palavras-chave prefixadas.

O comando DirMaint pode ser abreviado para **DIRM**.

## Extent Control

O arquivo EXTENT CONTROL define qualquer volume que está sendo usado para alocação de minidiscos e fornece um modelo, ou layout, de como o espaço deve ser usado. Além disso, também contém valores padrão do sistema e do dispositivo usados durante operações de alocação.

Este arquivo pode ser atualizado dinamicamente com o comando DIRM DASD.

Para verificar como o arquivo EXTENT CONTROL se parece, você pode usar:

```
===> DIRM SEND EXTENT CONTROL
```

Receba o arquivo e verifique o conteúdo:

```
===> X EXTENT CONTROL
```

Você verá algo como este arquivo:

```
* ********************************************************************
:REGIONS.
  *RegionId  VolSer    RegStart      RegEnd  Dev-Type  Comments
:END.
:GROUPS.
  *GroupName RegionList
:END.
:SSI_VOLUMES.
  *VolumeFamily     Member   VolSer
:END.
:DEFAULT_GROUPS.
  *GroupName Member
:END.
:EXCLUDE.
  * entry_name Address
  MAINT-*      0122
  MAINT-*      0123
  MAINT-*      0124
  MAINT-*      0125
  MAINT-*      0126
  MAINT630     0131
  MAINT630     0132
  PMAINT       0141
  PMAINT       0142
  SYSDMP-*     0123
:END.
:AUTOBLOCK.
  * IBM supplied defaults are contained in the AUTOBLK DATADVH file.
  * The following are customer overrides and supplements.
  *
  *DASDType BlockSize Blocks/Unit Alloc_Unit Architecture
:END.
:DEFAULTS.
  * IBM supplied defaults are contained in the DEFAULTS DATADVH file.
  * The following are customer overrides and supplements.
  *
  *DASDType Max-Size
:END.
```

Cada seção tem uma função especial:

- REGIONS: Define uma área ou região no seu volume DASD para uso durante a alocação automática do DirMaint. 
- GROUPS: Define um agrupamento de regiões para uso durante a alocação automática do DirMaint. 
- EXCLUDE: Define usuários ou combinações usuário/dispositivo que devem ser considerados como excluídos pelo subsistema DASD do DirMaint. 
- AUTOBLOCK: Define fatores de bloqueio e arquiteturas de dispositivos para vários tipos de dispositivos. Estes complementam ou substituem as definições fornecidas pela IBM no arquivo AUTOBLK DATADVH. 
- DEFAULTS: Define o tamanho máximo padrão para vários dispositivos DASD. Estes complementam ou substituem as definições fornecidas pela IBM no arquivo DEFAULT DATADVH. 
- END: Define a tag de encerramento para todas as seções.

**Nós não vamos editar este arquivo manualmente**, vamos usar comandos DIRMAINT para evitar erros e typos. Você pode fechar o arquivo usando "QQUIT".

## Adicionando discos ao EXTENT CONTROL

Assumindo que você executou a formatação em uma sessão anterior, você tem os discos 301, 302 e 303 formatados como PERM. Esses 3 discos serão usados para criar nossas VMs, mas primeiro precisamos atribuí-los ao DIRMAINT. Vamos criar REGIONS (usando todos os cilindros disponíveis) e POOLs de discos. Vamos usar o rótulo de cada disco como o nome da REGION, ex: LNX301, LNX302 e LNX303.

O comando DIRMAINT que adiciona discos ao EXTENT CONTROL é:

```
===> DIRM DASD
```

Se você digitar DIRM DASD logado como maint, o seguinte painel abrirá para você:

```
 --------------------------------DirMaint DASD--------------------------------
 Add, delete or query DASD statement associated with Group, Region & Volume.
 Select one:   _ Add  _ Delete          _ Query  or _ FREExt  or _ USEDext
 Select one of the following for Add:
  Group  GroupName  ==>           _ (LINEAR) (or) _ (ROTATING) (Optional)
         Region1    ==>           Region2 ==>            Region3 ==>         
  Region RegionName ==>           Volid   ==>        Device Type ==>         
    Optionally you can fill one or all of the following:
     Size =>            Start =>            Comments =>                       
  Volume       Volid =>               Device Type =>         
    Optionally you can fill one or all of the following:
     Size =>            Start =>            Comments =>                       
 Select one of the following for Delete:
  Group  GroupName =>          Region1 =>          Region2  =>          or _ *
  Region Region1   =>          Region2 =>          Region3  =>         
  Volume Volid1    =>          Volid2  =>          Volid3   =>       
 Select one of the following for Query:
  Group  GrpName1 =>          GrpName2 =>          GrpName3 =>          or _ *
  Region Region1  =>          Region2  =>          Region3  =>          or _ *
  Volume Volid1   =>          Volid2   =>          Volid3   =>          or _ *



5741-A07 (c) Copyright IBM Corporation 1979, 2011.
   1= Help     2= Prefix Operands      3= Quit     5=Submit      12=Cursor
```

Vamos atribuir o DASD 301 (LNX301) à nova região LNX301. O painel abaixo é um exemplo:

!!! Nota
Tipos de Dispositivos Você precisa perguntar ao instrutor qual é o tipo de dispositivo para seus discos PERM. O exemplo abaixo usa 3390-09, mas existem outros como 3390-03 e 3390-27.
!!!



```
  --------------------------------DirMaint DASD--------------------------------
  Add, delete or query DASD statement associated with Group, Region & Volume.
  Select one:   (*|\textcolor{red}{X}|*) Add  _ Delete          _ Query  or _ FREExt  or _ USEDext
  Select one of the following for Add:
   Group  GroupName  ==>           _ (LINEAR) (or) _ (ROTATING) (Optional)
          Region1    ==>           Region2 ==>            Region3 ==>         
   Region RegionName ==> (*|\textcolor{red}{LNX301}|*)    Volid   ==> (*|\textcolor{red}{LNX301}|*) Device Type ==> (*|\textcolor{red}{3390-09}|*)  
     Optionally you can fill one or all of the following:
      Size => (*|\textcolor{red}{END}|*)       Start => (*|\textcolor{red}{1}|*)          Comments => (*|\textcolor{red}{linux1 vol}|*)            
   Volume       Volid =>               Device Type =>         
     Optionally you can fill one or all of the following:
      Size =>            Start =>            Comments =>                       
  Select one of the following for Delete:
   Group  GroupName =>          Region1 =>          Region2  =>          or _ *
   Region Region1   =>          Region2 =>          Region3  =>         
   Volume Volid1    =>          Volid2  =>          Volid3   =>       
  Select one of the following for Query:
   Group  GrpName1 =>          GrpName2 =>          GrpName3 =>          or _ *
   Region Region1  =>          Region2  =>          Region3  =>          or _ *
   Volume Volid1   =>          Volid2   =>          Volid3   =>          or _ *




 5741-A07 (c) Copyright IBM Corporation 1979, 2011.
    1= Help     2= Prefix Operands      3= Quit     (*|\textcolor{red}{5=Submit}|*)      12=Cursor
```

Quando você preencher conforme necessário, pressione **F5** para enviar sua solicitação ou F3 para cancelá-la.

O mesmo resultado acima poderia ser alcançado usando a seguinte linha de comando (para evitar o painel):


```
===> DIRM DASD ADD REGION LNX301 LNX301 3390-09 END 1
```


Para aplicar as alterações ao CONTROLE DE EXTENSÃO:


```
===> DIRM RLDE
```


Se você precisar consultar informações sobre uma região específica, você pode executar:



```
===> DIRM DASD QUERY REGION LNX301
```


Isso fornecerá, dependendo do tipo de disco, o seguinte resultado:

```
DIRM DASD QUERY REGION LNX301
DVHXMT1191I Your DASD request has been sent for processing to DIRMAINT
DVHXMT1191I at ZVMWSXX.
Ready; T=0.01/0.01 13:08:35
 DVHREQ2288I Your DASD request for MAINT at * has been accepted.
 DVHDSD3563I REGION   VOLUME    START      SIZE        END    DEV-TYPE
 DVHDSD3563I COMMENTS
 DVHDSD3564I LNX301   LNX301          1       10016       10016 3390-09
 DVHREQ2289I Your DASD request for MAINT at * has completed; with RC = 0.
```

If the REGION is not valid, the following output will appear:

```
DIRM DASD QUERY REGION LNX301
DVHXMT1191I Your DASD request has been sent for processing to DIRMAINT
DVHXMT1191I at ZVMWSXX.
Ready; T=0.01/0.01 13:02:04
 DVHREQ2288I Your DASD request for MAINT at * has been accepted.
 DVHDSD3558W Region LNX301 cannot be queried, there is no region by
 DVHDSD3558W that name.
 DVHREQ2289I Your DASD request for MAINT at * has completed; with RC = 0.
```

### Exercício

- Adicione LNX302 e LNX303 ao EXTENT CONTROL usando o comando DIRM DASD.
  - Adicione LNX302 usando o painel
  - Adicione LNX303 usando a linha de comando
  - Verifique ambos usando o comando DIRM DASD QUERY.
  - Aplique as alterações usando DIRM RLDE

## Criando um pool de discos

Atribuímos LNX301, LNX302 e LNX303 ao DIRMAINT. Esta configuração nos permite atribuir discos a uma VM usando o nome da REGION, mas ainda não os agrupamos. Esses grupos de discos são chamados de POOLs, e é outra abstração que pode ser usada para atribuir discos a uma VM.

Usando o mesmo painel DIRM DASD, podemos criar nosso novo POOL, vamos chamá-lo de "LINUX":


```
  --------------------------------DirMaint DASD--------------------------------
  Add, delete or query DASD statement associated with Group, Region & Volume.
  Select one:   x Add  _ Delete          _ Query  or _ FREExt  or _ USEDext
  Select one of the following for Add:
   Group  GroupName  ==> LINUX     X (LINEAR) (or) _ (ROTATING) (Optional)
          Region1    ==> LNX301    Region2 ==> LNX302     Region3 ==> LNX303  
   Region RegionName ==>           Volid   ==>        Device Type ==>         
     Optionally you can fill one or all of the following:
      Size =>            Start =>            Comments =>                       
   Volume       Volid =>               Device Type =>         
     Optionally you can fill one or all of the following:
      Size =>            Start =>            Comments =>                       
  Select one of the following for Delete:
   Group  GroupName =>          Region1 =>          Region2  =>          or _ *
   Region Region1   =>          Region2 =>          Region3  =>         
   Volume Volid1    =>          Volid2  =>          Volid3   =>       
  Select one of the following for Query:
   Group  GrpName1 =>          GrpName2 =>          GrpName3 =>          or _ *
   Region Region1  =>          Region2  =>          Region3  =>          or _ *
   Volume Volid1   =>          Volid2   =>          Volid3   =>          or _ *




 5741-A07 (c) Copyright IBM Corporation 1979, 2011.
    1= Help     2= Prefix Operands      3= Quit     (*|\textcolor{red}{5=Submit}|*)      12=Cursor
```

Quando você preencher conforme necessário, pressione **F5** para enviar sua solicitação ou F3 para cancelá-la.

O mesmo resultado acima poderia ser alcançado usando a seguinte linha de comando (para evitar o painel):


```
DIRM DASD ADD GROUP LINUX LNX301 LNX302 LNX303
```

Para aplicar as alterações ao EXTENT CONTROL:


```
===> DIRM RLDE
```

Você pode consultar as informações do seu novo grupo usando o seguinte comando:

```
DIRM DASD QUERY GROUP LINUX
```

Isso fornecerá o seguinte resultado:

```
DIRM DASD QUERY GROUP LINUX
DVHXMT1191I Your DASD request has been sent for processing to DIRMAINT
DVHXMT1191I at ZVMWSXX.
Ready; T=0.01/0.01 14:13:19
 DVHREQ2288I Your DASD request for MAINT at * has been accepted.
 DVHDSD3561I GROUP=LINUX ALLOCATE=(LINEAR) REGIONS=3
 DVHDSD3562I GROUP=LINUX REGIONS=LNX301   LNX302   LNX303
 DVHREQ2289I Your DASD request for MAINT at * has completed; with RC = 0.
```

![Our Environment with
DIRMAINT](/imgs/workshop-dirmaint.png)
