<h1 align="center">Atividade prática AWS/LINUX ☁️ DOCUMENTAÇÃO</h1>

<h2>Descrição</h2>
A atividade tem como objetivo colocar em prática alguns conceitos e funcionalidades de serviços da Amazon AWS, assim como explorar o funcionamento de comandos Linux. Em linhas gerais, vamos criar uma instância EC2 em uma base Amazon Linux 2, configurar sua rede para acesso público e, dentro dessa mesma instância, vamos executar alguns procedimentos, como a criação de um script para verificar o status online e offline do Apache.

<h2>Requisitos Iniciais</h2>
A execução do projeto se dará em duas partes: a primeira ocorrerá dentro do ambiente da AWS; a segunda, dentro de um ambiente Linux. Cada uma dessas etapas precisa atender a alguns requisitos básicos, sendo eles:

<h4>Requisitos AWS ✅ </h4>
<ul>
<li>Gerar uma chave pública para acesso ao ambiente;</li>
<li>Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 (Família t3.small, 16 GB SSD);</li>
<li>Gerar 1 Elastic IP e anexar à instância EC2;</li>
<li>Liberar as portas de comunicação para acesso público: (22/TCP, 111/TCP e UDP, 2049/TCP/UDP, 80/TCP, 443/TCP).</li>
</ul>

<h4>Requisitos Linux ✅ </h4>
<ul>
<li>Configurar o NFS entregue;</li>
<li>Criar um diretório dentro do filesystem do NFS com seu nome;</li>
<li>Subir um apache no servidor - o apache deve estar online e rodando;</li>
<li>Criar um script que valide se o serviço está online e envie o resultado da validação para o seu diretório no NFS;</li>
<li>O script deve conter - Data HORA + nome do serviço + Status + mensagem personalizada de ONLINE ou offline;</li>
<li>O script deve gerar 2 arquivos de saida: 1 para o serviço online e 1 para o serviço OFFLINE;</li>
<li>Preparar a execução automatizada do script a cada 5 minutos.</li>
</ul>

<h2>Execução da atividade</h2>
⚠️Atenção! É importante lembrar que essa atividade está sendo documentada em fevereiro de 2024, sendo assim, dependendo do momento em que você esteja lendo a documentação, a disposição dos elementos nas páginas pode ter sido modificada, assim como a disponibilização dos recursos AWS e comandos Linux⚠️

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
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/fabfc238-64b7-47bc-b5b8-71e2aed69da2" alt="Tráfego SSH">
<li>Em Configurar Armazenamento, coloque 16gb de armazenamento gp2(SSD);</li>
<li>Revise as configurações e clique em Executar Instância.</li>
</ol>

⚠️Antes de gerarmos o IP elástico, é importante criarmos um Gateway de Internet para garantirmos a conexão da rede com a internet.⚠️

<h3>AWS >> Criando Gateway de Internet</h3>
<ol>
<li>No console da AWS, acesse o painel do serviço VPC. Clique em Gateways de internet no menu lateral esquerdo;</li>
<li>Clique em Criar gateway de internet;</li>
<li>Defina um nome para o gateway e clique em Criar gateway de internet. Lembre-me desse nome, pois vamos precisar dele mais adiante;</li>
<li>Selecione o gateway criado na lista e depois clique em Ações, no menu superior, escolhendo a opção Associar à VPC;</li>
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
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/a4cc2779-bca8-4757-868f-675e3664dbc6" alt="Elastic IP">
</ol>

<h3>AWS >> Liberar as portas de comunicação para acesso público 🌐 </h3>
<ol>
<li>Na página do serviço EC2, no menu lateral esquerdo, em Rede e Segurança, clique em Security groups;</li>
<li>Selecione o grupo de segurança que foi criado com a instância EC2;</li>
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

⚠️Como iremos acessar a instância via PuTTY a partir de uma máquina Windows, deveremos configurar ainda a Tabela de rotas principal e da Sub-rede, caso contrário, o PuTTY poderá não acessar a instância, informando erro de conexão. ⚠️

<h3>AWS >> Configurar rota de internet 🔃 </h3>
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

<h3>AWS >> Configurar rota de sub-rede ↩️ </h3>
<ol>
<li>Antes de configurarmos a rota da sub-rede, é preciso ir até o Painel EC2, clicar na opção Instâncias, selecionar a instância criada na lista e verificar nos detalhes abaixo em qual sub-rede ela está localizada. Munidos dessa informação, retornamos para o Painel VPC, no mesmo campo Tabela de rotas que entramos na configuração anterior;</li>
<li>Selecione apenas e exatamente a sub-rede na qual a instância EC2 criada está localizada;</li>
<li>Clique em Ações, no menu superior e selecione a opção Editar rotas;</li>
<li>A partir de agora, faremos basicamente a mesma coisa que fizemos na configuração da VPC principal. Clique em Adicionar rota;</li>
<li>Configure da seguinte forma:<br>
    Destino: 0.0.0.0/0<br>
    Alvo: Escolha Gateway de internet e, abaixo, selecione o gateway que criamos anteriormente<br></li>
<li>Clique em Salvar alterações.</li>
</ol>

<h3>AWS >> Servidor NFS a partir do Elastic File System (EFS) - Security Group 📂</h3>
Para configurarmos um servidor NFS na máquina Linux nos próximos passos, vamos utilizar o serviço EFS da própria AWS.<br>
Antes, vamos configurar um grupo de segurança que será utilizada para a rede do EFS mais adiante.
<ol>
<li> Vá até o Painel EC2 da AWS e clique em Security groups;</li>
<li>Clique em Criar grupo de segurança;</li>
<li>Atribua um nome;</li>
<li>Selecione a mesma VPC em que se encontra a instância. Ela aparecerá listada para você;</li>
<li>Em Regras de entrada adicione uma nova regra seguindo o modelo abaixo:</li>

| Tipo | Protocolo | Intervalo de portas | Origem | Descrição|
| --- | --- | --- | --- | --- |
| NFS | TCP | 2049 | Grupo de segurança da instância | NFS | 
<br>
<li>Quando for escolher o campo Origem, escolha a opção Personalizado e, na caixa ao lado, role a barra até encontrar o grupo de segurança que foi criado para a instância EC2 que vamos acessar. Dessa forma, os dois grupos de segurança estarão conectados, cada um com seu objetivo.</li>
<li>Clique em Criar grupo de segurança para finalizar.</li>
</ol>

<h3>AWS >> Criando o serviço de Elastic File System 🗄️ (EFS)</h3>
<ol>
<li>No console AWS, navegue até o serviço de EFS;</li>
<li>No menu lateral esquerdo, clique em Sistemas de arquivos e, na sequência, em Criar sistema de arquivos;</li>
<li>Adicione um nome para o sistema de arquivos e selecione a opção Personalizar;</li>
<li>Marque a opção One zone e selecione a mesma zona de disponibilidade em que sua instância foi criada e avance;</li>
<li>Mantenha as opções pré-definidas, altere apenas o grupo de segurança para o grupo que criamos para o serviço EFS;</li>
<li>Revise as informações e clique em Criar para terminar;</li>
<li>Na lista de sistemas criados, abra o sistema de arquivos recém-feito e clique no botão Anexar para visualizar as opções de montagem (IP ou DNS);</li>
<li>A AWS já nos apresenta comandos definidos de acordo com as opções escolhidas. Aqui, vamos utilizar a montagem via DNS usando o cliente do NFS. Copie-o e salve em um bloco de notas, pois irá precisar dele mais adiante. O comando segue o seguinte modelo:</li>
<code>sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 [DNS do EFS]:/ [caminho local]</code>
</ol>

<br>
👍 Pronto! Nossas configurações do ambiente AWS foram devidamente realizadas. Agora seguimos para as configurações da máquina Linux, o acesso da instância e a realização de alguns comandos. 👍
<br>

Antes de iniciarmos os próximos passos será preciso:
<ul>
<li>Baixar e instalar a versão mais atualizada do PuTTY, de preferência diretamente da página oficial. O PuTTY é um cliente SSH gratuito para Windows.</li>
<li>Ir até os detalhes da sua instância EC2, no console AWS, e copiar as informações do DNS público. Esse informação geralmente termina com os termos "amazonaws.com".</li>
</ul>
Estando com o PuTTY instalado:
<h3>PuTTY >> Acessando a instância via PuTTY</h3>
<ol>
<li>Inicie o PuTTY em sua máquina;</li>
<li>No painel Category, no lado esquerdo, selecione Session;</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/fbf451d4-8364-409a-af81-421db224156c" alt="Tela inicial do PuTTY">
<li>Na caixa Host Name digite o nome de usuário da instância (por padrão, nas máquinas Amazon Linux, é ec2-user), seguido de @ e o DNS público da instância (aquele que termina com "amazonaws.com.</li>
<li>A estrutura desse comando é "instance-user-name@instance-public-dns-name";</li>
<li>Verifique se o valor do Port está em 22;</li>
<li>Em Tipo de conexão, selecione SSH;</li>
<li>No painel do lado esquerdo, expanda o menu Connection, depois faça o mesmo com o menu SSH e o menu Auth. Escolha a opção Credentials</li>
<li>Em Public-key authentication, na caixa Private Key file for authentication, clique em Browse;</li>
<li>Basta selecionar o arquivo do par de chaves em formato .ppk que salvamos anteriormente;</li>
<li>Clique em Open, na parte inferior e pronto, o PuTTY fará o acesso à instância EC2 criada, caso as informações de DNS público e o arquivo .ppk estejam corretos;</li>
<li>Se essa for a primeira vez que você se conectou a essa instância, o PuTTY exibirá uma caixa de diálogo de alerta de segurança perguntando se você confia no host ao qual está se conectando. Escolha Accept;</li>
<li>Em seguida, será aberta a tela do terminal da máquina Windows da instância.</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/98cb43a4-9c71-4cd0-908e-342f885559c6" alt="Texto Alternativo">
</ol>

<h3>Linux >> Montando o sistema de arquivos do EFS na máquina 📄 </h3>
A partir de agora nossas ações serão feitas no terminal Linux da instância EC2 que o PuTTY nos conectou.<br>
Caso necessário, entre com o comando <code>sudo su</code> para ganhar privilégios administrativos.
<ol>
<li>Execute o comande de atualização do sistema <code>sudo yum update -y</code> antes de iniciar instalações, para garantir que serão sempre as versões mais atualizadas dos arquivos Linux que rodarão;</li>
<li>Com o comando <code>sudo yum install -y amazon-efs-utils</code> instale o pacote para suporte ao NFS. É um protocolo que permite compartilhar diretórios e arquivos entre sistemas operacionais em uma rede.;</li>
<li>Utilize o comando <code>sudo mkdir /mnt/efs</code> para criar um diretório local que servirá como ponto de montagem;</li>
<li>Agora vamos montar o sistema de arquivos. Para isso, é preciso utilizar o comando que foi copiado anteriormente, <code>sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 [DNS do EFS]:/ [caminho local]</code>, lembrando que cada um terá o seu próprio DNS, disponibilizado nos detalhes do serviço na AWS, e caminho local, que aqui foi nomeado como /mnt/efs;</li>
    <h4>Configurando montagem automática</h4>
    Também é possível realizar uma configuração para que a montagem ocorra automaticamente.
    <ol>
    <li>Digite o comando <code>sudo nano /etc/fstab</code> para abrir o arquivo para edição;</li>
    <li>Dentro do arquivo, acrescente a linha <code>[ID do sismeta de arquivos]:/ [caminho local] nfs4 nfsvers=4.1,rsize=1048576wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 0</code>. Note que é basicamente o comando que já utilizamos, mas com os blocos invertidos.</li>
    <li>Substitua o ID do sistema e o caminho local pelas suas próprias credenciais</li>
    <li>Confirme se o sistema de arquivos EFS está montado corretamente usando o comando <code>df -h</code></li>
    </ol>
</ol>

<h3>Linux >> Configurando Apache 📤 </h3>
<ol>
<li>Atualize os pacotes do sistema com o comando <code>sudo yum update -y</code>;</li>
<li>Instale o Apache com o comando <code>sudo yum install httpd -y</code>;</li>
<li>Inicie o Apache no sistema com o comando <code>sudo systemctl start httpd</code> ou ainda o <code>sudo /bin/systemctl start httpd.service</code>;</li>
<li>Para o Apache iniciar automaticamente, execute o comando <code>sudo systemctl enable httpd</code>;</li>
<li>Verifique se o apache está em execução através do comando <code>sudo systemctl status httpd</code>;</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/afeb0b4e-569d-4861-a127-9646ab44a87c" alt="Texto Alternativo">
<li>O Apache já vem com uma página inicial padrão que pode ser acessada através da digitação do IP público na barra de endereço de um navegador. Mas também é possível editar essa página HTML para que exiba o que você quiser. Isso é feito a partir de um arquivo index que pode ser criado dentro do diretório do Apache;</li>
<li>Para criar/editar esse arquivo, digite o comando <code>sudo nano index.html</code>. O arquivo HTML que você digitar nesse documento é o que será mostrado na página acessada pelo IP público. Veja a seguir um exemplo de documento HTML para o serviço:</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/8e9df0a5-8920-4ad7-b661-7cc3b7367887" alt="Texto Alternativo">
<li>Para salvar o documento no editor nano, aperte ctrl+x, depois y e confirme apertando enter;</li>
<li>Para acessar a página e ver se funcionou, basta colar o IP público da instância (informação disponível nos detalhes da instância na AWS) na barra de endereço de um navegador.</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/90610cad-f8c4-44fb-81d3-393939ef543e" alt="Texto Alternativo">
</ol>

<h3>LINUX >> Criando um script que valide se o serviço está online ou offline e envie o resultado da validação para o seu diretório no NFS 📃 </h3>
Para criar um script será necessário utilizar um editor de texto (utilizaremos o nano) e, ao final do nome do arquivo, devemos atribuir a extensão .sh.<br>
Devemos lembrar que, para essa atividade, o script deve conter data, hora, nome do serviço, status e mensagem personalizada de ONLINE ou OFFLINE.<br>
O script também deve gerar 2 arquivos de saída: um para o serviço online e outro para o serviço offline.
<ol>
<li>Execute o comando <code>nano service_status.sh</code> para criar e abrir o arquivo do script. É importante criar o script dentro do diretório EFS. Aqui vamos salvá-lo no caminho /mnt/efs/sergio;</li>
<li>Dentro do arquivo, digite o script desejado. O script criado para essa atividade pode ser observado na imagem a seguir:</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/6c623fa7-81b9-4474-9c4a-6eacf97a7ff0" alt="Exemplo de script">
<li>Note que, no exemplo acima, dentro do esquema "if/else", já indicamos que a operação deve criar, no caminho do diretório indicado, e enviar dois arquivos em formato .txt com os resultados da verificação. Sendo um arquivo para o resultado online e outro para o resultado offline;</li>
<li>Salve o arquivo do script;</li>
<li>Para tornar o arquivo do script executável digite o comando <code>sudo chmod +x [nome do script]</code>, sendo, nesse caso, <code>sudo chmod +x service_status.sh</code>;</li>
<li>Estando no diretório onde o script foi criado e ativado, execute o comando <code>./service_status.sh</code> para executá-lo. Caso esteja funcionando corretamente e o serviço esteja online, o script vai criar o documento .txt que guarda as informações da validação online;</li>
<li>Esse documento pode ser lido com o comando cat + nome do documento: <code>cat httpd-online.txt</code>. É possível verificar o funcionamento do script na imagem abaixo:</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/9dcaacab-8097-46bb-bd77-eee725b9f1c0" alt="Script em funcionamento">
<li>Note que o documento informa a data e a hora em que a verificação foi feita, assim como o nome do serviço verificado e uma mensagem indicando que o mesmo está online.</li>
</ol>

<h3>Linux >> Preparando a execução automatizada do script a cada 5 minutos 🤖 </h3>
Para o agendamento da execução do script vamos utilizar o comando crontab. Normalmente o crontab abre um arquivo com o programa vi de edição de texto. Sendo o vi não muito prático, é possível modificar para que a abertura ocorra com o nano, muito mais intuitivo e semelhante aos editores de texto convencionais.
<ol>
<li>Digite o comando <code>EDITOR=nano crontab -e</code>, para que o nano abra o arquivo crontab;</li>
<li>Dentro do arquivo digite a linha <code>*/5 * * * * /[caminho de onde está o script/nome do script]</code>. Em nosso caso, ficou dessa forma: <code>*/5 * * * * /mnt/efs/sergio/service_status.sh</code></li>
<li>Salve o arquivo e feche o editor.</li>
<li>Para verificar se a automatização está funcionando, é preciso abrir os arquivos .txt que foram programados para serem criados e guardar as informações da verificação do serviço online e offline. Como a automatização faz com que a verificação programada pelo script ocorra a cada 5 minutos, dê algum tempo para que o arquivo .txt seja atualizado algumas vezes;</li>
<li>Na imagem abaixo temos a demonstração do arquivo httpd-online.txt exibindo as informações da validação online após o crontab realizar a automatização algumas vezes:</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/1ef9f8f9-a835-428d-aacd-b5c4d24ff001" alt="Demonstração do funcionamento do crontab online">
<li>Para fazermos a confirmação de que o script realiza a verificação do serviço offline é preciso interromper o Apache com o comando <code>sudo systemctl stop httpd</code>. Dessa forma, basta aguardar alguns minutos para que o crontap continue a executar o script a cada 5 minutos e poderemos ver a criação do arquivo httpd-offline.txt, que exibe os momentos em que o status do serviço estava offline, conforme imagem abaixo:</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/7f8aeded-b35b-4d15-abd7-c977d4aeb7fb" alt="Demonstração do funcionamento do crontab offline">
<li>Ainda, é possível verificarmos que os arquivos .txt foram criados dentro do diretório indicado no script:</li>
<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/86e61ae4-e027-45a6-a360-5075ec43f803" alt="Demonstração dos arquivos de texto no diretório">
</ol>

<h3>Referências para a realização da atividade 📚 </h3>
Documentação oficial Amazon AWS: https://docs.aws.amazon.com/pt_br/ <br>
Guia Linux Unirio: https://guialinux.uniriotec.br/ <br>

<br>

Esta documentação foi formulada por Sérgio Luiz Ferreira de Freitas a partir da atividade realizada e proposta no contexto do Programa de Bolsas e Estágio AWS e DecSecOps na Compass UOL no primeiro semestre de 2024.

<img src="https://github.com/ferreirasergio/Atividade_Linux_AWS_CompassUOL/assets/105258064/191d63f4-4eb3-4780-b902-aae6fc8aa56d" alt="Compass Uol logo">
