<h1 align="center">Atividade prática AWS/LINUX ☁️ DOCUMENTAÇÃO</h1>
<h2>Descrição</h2>
A atividade tem como objetivo colocar em prática alguns conceitos e funcionalidades de serviços da Amazon AWS, assim como explorar o funcionamento de comandos Linux. Em linhas gerais, vamos criar uma instância EC2 em uma base Amazon Linux 2 e, dentro dela, executar alguns procedimentos, como a criação de um script para verificar o status online e offline do Apache.
<h2>Requisitos Iniciais</h2>
A execução do projeto se dará em duas partes: a primeira ocorrerá dentro do ambiente da AWS; a segunda, dentro de um ambiente Linux. Cada uma dessas etapas precisa atender a alguns requisitos básicos, sendo eles:
<h4>Requisitos AWS</h4>
<ul>
<li>Gerar uma chave pública para acesso ao ambiente;</li>
<li>Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 (Família t3.small, 16 GB SSD);</li>
<li>Gerar 1 elastic IP e anexar à instância EC2;</li>
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
<li>A tela de criação será aberta e nela você podera escolher um nome para o par de chaves. No meu caso, criei a chave "chave2_sergio_compass";</li>
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
<li>Em Tipo de instância, escolha t3.small;</li>
<li>Em Par de Chaves, selecione a chave criada anteriormente;</li>
<li>Em configurações de Rede, selecione a opção Criar Grupo de segurança;</li>
<li>Ainda nessa seção, deixe marcada a opção Permitir tráfego SSH de Qualquer Lugar (0.0.0.0/0);</li>
<li>Em Configurar Armazenamento, coloque 16gb de armazenamento gp2(SSD);</li>
<li>Revise as configurações e clique em Executar Instância.</li>
</ol>
