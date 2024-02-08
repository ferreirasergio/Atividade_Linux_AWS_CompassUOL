<h1 align="center">Atividade prática AWS/LINUX ☁️ DOCUMENTAÇÃO</h1>

<h2>Descrição</h2>
A atividade tem como objetivo colocar em prática alguns conceitos e funcionalidades de serviços da Amazon AWS, assim como explorar o funcionamento de comandos Linux. Em linhas gerais, vamos criar uma instância EC2 em uma base Amazon Linux 2, configurar sua rede para acesso público e, dentro dessa mesma instância, vamos executar alguns procedimentos, como a criação de um script para verificar o status online e offline do Apache.

<h2>Requisitos Iniciais</h2>
A execução do projeto se dará em duas partes: a primeira ocorrerá dentro do ambiente da AWS; a segunda, dentro de um ambiente Linux. Cada uma dessas etapas precisa atender a alguns requisitos básicos, sendo eles:

<h4>Requisitos AWS</h4>
<ul>
<li>Gerar uma chave pública para acesso ao ambiente;</li>
<li>Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 (Família t3.small, 16 GB SSD);</li>
<li>Gerar 1 Elastic IP e anexar à instância EC2;</li>
<li>Liberar as portas de comunicação para acesso público: (22/TCP, 111/TCP e UDP, 2049/TCP/UDP, 80/TCP, 443/TCP).</li>
</ul>

<h4>Requisitos Linux</h4>
<ul>
<li>Configurar o NFS entregue;</li>
<li>Criar um diretorio dentro do filesystem do NFS com seu nome;</li>
<li>Subir um apache no servidor - o apache deve estar online e rodando;</li>
<li>Criar um script que valide se o serviço esta online e envie o resultado da validação para o seu diretorio no nfs;</li>
<li>O script deve conter - Data HORA + nome do serviço + Status + mensagem personalizada de ONLINE ou offline;</li>
<li>O script deve gerar 2 arquivos de saida: 1 para o serviço online e 1 para o serviço OFFLINE;</li>
<li>Preparar a execução automatizada do script a cada 5 minutos.</li>
</ul>

<h2>Execução da atividade</h2>
⚠️Atenção! É importante lembrar que essa atividade está sendo documentada em fevereiro de 2024, sendo assim, dependendo do momento em que você esteja lendo a documentação, a disposição dos elementos pode ter sido modificada, assim como a disponibilização dos recursos AWS e comandos Linux⚠️

<h3>AWS >> Geração de chave pública para acesso ao ambiente 🔑</h3>
Inicialmente, devemos lembrar que é possível criar uma chave pública de duas formas no console AWS: na opção "Pares de Chaves", no menu Rede e Segurança do Painel EC2; e durante a criação de uma instância EC2, no momento de configurações da instância. Aqui, iremos criar a chave antes de criarmos a instância.
<ol>
<li>No console AWS, vá para o Painel EC2;</li>
<li>No menu posicionado ao lado esquerdo da tela, vá até a seção Rede e Segurança e clique em Pares de Chaves;</li>
<li>Na próxima tela, no lado superior direito, clique em Criar par de chaves;</li>
<li>A tela de criação será aberta e nela você poderá escolher um nome para o par de chaves. No meu caso, criei a chave "chave2_sergio_compass";</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/ed421a36-89ec-4a4b-8c67-1dec256dc4ec" alt="Print da tela de cadastro de par de chave">
<li>Mantenha as configurações restantes e na opção Formato de arquivo de chave privada, selecione o formato .ppk, necessário para acessar a instância via Putty, como iremos fazer;</li>
<li>Feito isso, basta clicar em Criar Par de Chaves;</li>
<li>Salve em lugar seguro o arquivo gerado e pronto, o par de chave estará disponível e listado no menu.</li>
</ol>

<h3>AWS >> Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 (Família t3.small, 16 GB SSD) 🖥️</h3>
<ol>
<li>No Painel EC2 no console AWS, clique em Instâncias no menu ao lado esquerdo da tela;</li>
<li>Na próxima tela, no lado superior direito, clique em Executar instância;</li>
<li>Agora é possível criar um nome para a instância e criar tags. No meu caso, criei as tags Nome, CostCenter e Project, de acordo com as recomendações dos instrutores.</li>
<li>Selecione o sistema Amazon Linux AWS e, em seguida a imagem Amazon Linux 2 AMI (HVM), SSD Volume Type;</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/f204dcb6-c005-4200-af02-a9d1d5308336" alt="Print da AMI">
<li>Em Tipo de instância, escolha t3.small;</li>
<li>Em Par de Chaves, selecione a chave criada anteriormente;</li>
<li>Em configurações de Rede, selecione a opção Criar Grupo de segurança;</li>
<li>Ainda nessa seção, deixe marcada a opção Permitir tráfego SSH de Qualquer Lugar (0.0.0.0/0);</li>
<li>Em Configurar Armazenamento, coloque 16gb de armazenamento gp2(SSD);</li>
<li>Revise as configurações e clique em Executar Instância.</li>
</ol>

⚠️Antes de gerarmos o IP elástico, é iportante criarmos um Gateway de Internet para garantirmos a conexão da rede com a internet.⚠️

<h3>AWS >> Criando Gateway de Internet</h3>
<ol>
<li>No console da AWS, acesse o painel do serviço VPC. Clique em Gateways de internet no menu lateral esquerdo;</li>
<li>Clique em Criar gateway de internet;</li>
<li>Defina um nome para o gateway e clique em Criar gateway de internet. Lembre-me desse nome, pois vamos precisar dele mais adiante;</li>
<li>Selecioneo gateway criado na lista e depois clique em Ações, no menu superior, escolhando a opção Associar à VPC;</li>
<li>Selecione a VPC da instância EC2 criada anteriormente e clique em Associar. A VPC será listada automaticamente no campo, bastando confirmar;</li>
</ol>

<h3>AWS >> Gerar 1 Elastic IP e anexar à instância EC2</h3>
<ol>
<li>Na página do serviço EC2, no menu lateral esquerdo, em Rede e Segurança, clique em IPs elásticos;</li>
<li>Clique em Alocar endereço IP elástico;</li>
<li>Por padrão, o Grupo de borda de Rede já vem selecionado, assim como o Conjunto de endereços IPv4 públicos da Amazon;</li>
<li>Clique em Alocar;</li>
<li>Depois da criação, selecione o IP na lista, clique em Ações no menu superior e depois em Associar endereço IP elástico;</li>
<li>Selecione a instância EC2 criada anteriormente;</li>
<li>Depois de selecionar a instância será preciso selecionar o endereço IP privado, que será sugerido pela própria plataforma, bastando confirmar;</li>
<li>Marque a opção Permitir que o endereço IP elástico seja reassociado e clique em Associar.</li>
</ol>

<h3>AWS >> Liberar as portas de comunicação para acesso público</h3>
<ol>
<li>Na página do serviço EC2, no menu lateral esquerdo, em Rede e Segurança, clique em Security groups;</li>
<li>Selecione o grupo de segurança que foi criado juntamente com a instância EC2;</li>
<li>Clique em Regras de entrada, na parte inferior, e depois, do lado direito da tela, em Editar regras de entrada;</li>
<li>Por padrão, já temos uma regra de entrada, do Tipo SSH, no Intervalo de portas 22, Protocolo TCP. Essa regra será mantida;</li>
<li>Clique em Adicionar regras. Agora iremos acrescentar a liberação de outras portas, além da 22 que já consta, conforme indicado na tabela abaixo:</li>
<br>

| Tipo         | Protocolo    | Intervalo de portas | Origem    | Descrição  |
| ------------ | ------------ | ------------------- | --------- | ---------- |
| SSH          | TCP          | 22                  | 0.0.0.0/0 | SSH        |
| HTTP | TCP | 80 | 0.0.0.0/0 | HTTP |
| HTTPS | TCP | 443 | 0.0.0.0/0 | HTTPS |
| TCP personalizado | TCP | 111 | 0.0.0.0/0 | RPC |
| UDP personalizado | UDP | 111 | 0.0.0.0/0 | RPC |
| NFS | TCP | 2049 | 0.0.0.0/0 | NFS |
| UDP personalizado | UDP | 2049 | 0.0.0.0/0 | NFS |

<li> Clique em Salvar regras.</li>
</ol>

⚠️Como iremos acessar a instância via Putty a partir de uma máquina Windows, deveremos configurar ainda a Tabela de rotas principal e da Sub-rede, caso contrário, o Putty poderá não acessar a instância, informando erro de conexão. ⚠️

<h3>AWS >> Configurar rota de internet</h3>
<ol>
<li>Acesse o Painel do serviço VPC e clique em Tabelas de rotas, no menu lateral esquerdo;</li>
<li>Selecione a tabela de rotas principal da VPC da instância EC2 criada anteriormente. Geralmente é a primeira da lista;</li>
<li>Clique em Ações, no menu superior e selecione a opção Editar rotas;</li>
<li>Clique em Adicionar rota;</li>
<li>Configure da seguinte forma:<br>
    Destino: 0.0.0.0/0<br>
    Alvo: Escolha Gateway de internet e, abaixo, selecione o gateway que criamos anteriormente<br></li>
<li>Clique em Salvar alterações.</li>
</ol>

<h3>AWS >> Configurar rota de sub-rede</h3>
<ol>
<li>Antes de configurarmos a rota da sub-rede, é preciso ir até o Painel EC2, clicar na opção Intâncias, selecionar a instância criada na lista e verificar nos detalhes abaixo em qual sub-rede ela está localizada. Munidos dessa informação, retornamos para o Painel VPC, no mesmo campo Tabela de rotas que entramos na configuração anterior;</li>
<li>Selecione apenas e exatamente a sub-rede na qual a instância EC2 criada está localizada;</li>
<li>Clique em Ações, no menu superior e selecione a opção Editar rotas;</li>
<li>A partir de agora, faremos basicamente a mesma coisa que fizemos na configuração da VPC principal. Clique em Adicionar rota;</li>
<li>Configure da seguinte forma:<br>
    Destino: 0.0.0.0/0<br>
    Alvo: Escolha Gateway de internet e, abaixo, selecione o gateway que criamos anteriormente<br></li>
<li>Clique em Salvar alterações.</li>
</ol>
<br>
👍 Pronto! Nossas configurações do ambiente AWS estão prontas. Agora seguimos para as configurações da máquina Linux, o acesso da instância e a realização de alguns comandos. 👍



