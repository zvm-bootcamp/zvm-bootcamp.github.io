---
order: 80
icon: ":globe_with_meridians:"
---
# Conceitos de Rede

A rede física no System z consiste em dispositivos conhecidos como Adaptadores de Sistemas Abertos (OSAs). Várias variedades estão disponíveis, como o OSA-Express4S e o OSA-Express5S. Eles são capazes de lidar com até 640 pilhas TCP/IP simultaneamente, incluindo HiperSockets para comunicação inter-LPAR. Um IBM System zEC12 fornece até 96 portas OSA-Express5S para comunicações de rede externa. O Adaptador de Sistemas Abertos suporta conexões Ethernet de cobre e fibra em velocidades de até 10 Gb.

Como era de se esperar, o recurso z/VM para acessar a rede de Protocolo de Internet é o TCP/IP para z/VM. Os dispositivos OSA-Express podem ser virtualizados por meio de um dispositivo de switch virtual (VSWITCH) para muitos convidados Linux. Ele está disponível usando máquinas z/VM especiais conhecidas como controladores VSWITCH. Cada convidado Linux se conecta usando um dispositivo virtual controlado pelo módulo qeth a um sistema de switch virtual em um LPAR z/VM.

Um benefício importante do sistema VSWITCH é que ele pode ser configurado com dispositivos OSA redundantes que fornecem um sistema de rede de failover no z/VM.

HiperSockets fornecem interconectividade de alta velocidade entre convidados em execução em um System z. Essa tecnologia não requer nenhuma configuração ou cabeamento físico especial. Os convidados simplesmente se comunicam internamente por meio das capacidades em memória do hipervisor PR/SM. No entanto, os HiperSockets não são destinados a serem usados para redes sofisticadas e não devem ser usados para tráfego externo.

Tanto o OSA-Express quanto os HiperSockets usam o mecanismo de E/S Direta de Fila (QDIO) para transferir dados. Esse mecanismo melhora o tempo de resposta usando filas de memória do sistema para gerenciar a fila de dados e a transferência entre z/VM e o dispositivo de rede.

## Recursos de Rede

No Mainframe, há uma série de dispositivos de rede diferentes disponíveis para uso. Muitos deles têm origem histórica e não devem ser usados para novas implementações. No entanto, eles continuam sendo usados para dar suporte a instalações anteriores em hardware mais recente. O Linux no System z pode operar usando todas as interfaces de rede comuns, mas para novas instalações, existem métodos recomendados para operação, dependendo do caso de uso.

A seguir, estão algumas tecnologias que você encontrará no mundo do System z que não são usadas ou nem mesmo vistas em sistemas x86. Esta seção esclarece algumas novas funcionalidades que você encontrará ao migrar do x86 para o System z. Fornecemos algumas informações breves que você pode usar para iniciar seu planejamento de rede. Em cada subseção, você pode encontrar uma referência para obter informações mais detalhadas.

### O Adaptador de Sistemas Abertos (OSA)

O Adaptador de Sistemas Abertos (OSA) é um controlador de rede de hardware. Ele é instalado em uma gaiola de E/S do Mainframe e fornece conectividade para clientes em redes locais (LANs) ou redes de longa distância (WANs). Ele pode ser conectado diretamente ao Linux, mas geralmente é conectado a switches virtuais (leia mais na seção "Switch virtual" abaixo). Você pode encontrar mais informações técnicas sobre os cartões OSA no "IBM zEnterprise EC12 Technical Guide", SG24-8049.

### OSA com Agregação de Links

Você pode agregar vários cartões OSA físicos em um único link lógico, chamado de grupo de agregação de links (LAG). Essa configuração aumenta a largura de banda e fornece failover sem interrupções. Como configurar isso é bem descrito no livro "Advanced Networking Concepts Applied Using Linux on IBM System z", SG24-7995.

### HiperSockets

HiperSockets é uma implementação de microcódigo que emula uma Camada de Controle de Link Lógico de uma interface OSA. HiperSockets fornece comunicação com latência próxima de zero e velocidade de memória entre servidores em execução em diferentes LPARs. Ao conectar um convidado Linux a um sistema IBM z/OS no Mainframe, a rede HiperSockets no modo de Camada 3 é o método a ser usado. Os HiperSockets devem ser configurados na configuração de E/S do Mainframe. Os HiperSockets não fornecem conexões externas. Se uma conexão externa for necessária, é necessário implementar uma ponte HiperSockets usando um VSWITCH ou configurar um convidado Linux como roteador.

HiperSockets fornecem uma conexão muito rápida entre LPARs. Eles fornecem uma maneira fácil de conectar muitos servidores Linux a um sistema z/OS no mesmo Mainframe. Essa conexão direta, sem envolver hardware real, é um fator importante para simplificar configurações com muitos sistemas Linux que precisam ser conectados ao z/OS. Alguns benefícios são explicados no livro "Set up Linux on IBM System z for Production", SG24-8137.

### Switch Virtual

Um switch virtual (VSWITCH) é um programa de software que permite que um host virtual se comunique com outro host virtual dentro de um sistema de computador. Switches virtuais normalmente emulam as funções de um switch Ethernet físico. No Linux no System z, um VSWITCH fornece conexão direta de convidados z/VM ao segmento de rede física local. O VSWITCH permite que arquitetos de rede IP e administradores de rede tratem os convidados z/VM como um servidor na rede.

A rede comutada dentro de um Sistema Operacional z/VM geralmente é implementada com um VSWITCH. Ao executar o VSWITCH como Camada 2, ele se comporta de maneira semelhante a um switch real entre máquinas virtuais.

A velocidade real de uma conexão com um VSWITCH depende de várias variáveis diferentes. O tipo de tráfego é tão importante quanto o hardware subjacente real e a unidade máxima de transmissão (MTU), que é o tamanho máximo (em bytes) de um pacote de dados que pode ser transferido em uma rede. Comum a todas essas soluções é que o VSWITCH é mais rápido do que um switch real conectado ao Mainframe seria.

Os VSWITCHes não precisam de uma conexão com um cartão OSA para operar. Eles também podem fornecer redes puramente virtuais. Isso também simplifica a configuração de interconexões privadas entre sistemas convidados. Ao criar interconexões privadas em um SSI com LGR habilitado, é recomendado o uso de VLANs dedicadas com interface externa. Isso é necessário para realizar a conexão privada entre convidados que são executados em nós diferentes no SSI.

A implementação de VLANs também ajuda se diferentes convidados forem executados em zonas de segurança de uma rede. É fácil configurar interfaces de rede para convidados Linux que fornecem apenas VLANs selecionadas ao convidado. Elas podem ser configuradas como VLANs marcadas ou como uma única VLAN não marcada em uma interface.

A infraestrutura VSWITCH oferece duas opções básicas de configuração. Uma configura o acesso baseado em usuário, a outra configura o acesso baseado em porta. Das possibilidades, ambas são equivalentes, apenas as configurações diferem.

Você pode ler mais sobre os benefícios do VSWITCH no livro "Set up Linux on IBM System z for Production", SG24-8137, e obter informações técnicas sobre "Advanced Networking Concepts Applied Using Linux on IBM System z", SG24-7995.
