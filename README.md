# Servidor-intranet-openredu
Repositório que contém a documentação para a instalação e execução do Openredu em uma intranet.

Inicialmente, é necessário deixar claro que o Ubuntu Server pode ser utilizado na Oracle VM VirtualBox ou em outros ambientes de máquinas virtuais, precisando somente garantir que seja disponibilizado ao menos 2 GB de memória RAM, 15 GB de armazenamento e configurar na aba “Rede” da VMBox, colocando a placa de rede em modo “Bridge”.

Instalação do Ubuntu Server:
Acesse o site: https://ubuntu.com/download/server 
Faça o download da versão LTS mais recente do Ubuntu Server.
Após o download, faça a instalação do sistema operacional diretamente na máquina através de um pendrive bootável ou diretamente em um ambiente de máquinas virtuais (VMBox);
Após abrir a máquina virtual ou iniciar o computador, selecione a opção “Try or Install Ubuntu Server”;
Selecione a linguagem e em seguida selecione a opção “Continue without updating”;
Se necessário, configure o teclado utilizando a opção “Identify keyboard” e digitando os caracteres que forem pedidos pelo sistema. Após isso, clique em “Concluído”;
Selecione “Concluído” em todas as próximas etapas, na última delas ele perguntará se você está certo que quer continuar, selecione “Continue”;
Configure o nome do servidor, login, senha e selecione “Continue” (lembre-se: você não pode esquecer o usuário e a senha, eles serão utilizados para ter acesso ao servidor);
Selecione “Install OpenSSH server” e clique em “Concluído”;
Clique em “Concluído” novamente;
Após concluída a instalação, clique em “Reboot Now” (se estiver utilizando a VMBox, feche a máquina após o erro de cdrom e abra novamente);
Faça login e instale as ferramentas de rede com o comando “sudo apt install net-tools”.
Siga para a configuração do endereço ip do servidor.

Observação: O próximo passo foi criado com o intuito de transformar o endereço ip do servidor em estático. Porém, durante os testes foi percebido que há um problema de conexão após fazer esse procedimento, então recomendo que você pule diretamente para a instalação do Openredu e utilize o endereço de ip padrão. Sugestões para resolver esse e qualquer outro problema encontrado são e sempre serão bem vindas.

Lembre-se de usar o comando “ifconfig” para descobrir o endereço ip do seu servidor. O ip que você vai copiar é o do “inet do enp0s3”.

Caso o “ifconfig” retorne informações criptografadas ou confusas, você pode seguir com a instalação do Openredu até o fim e após carregar o Openredu pela primeira vez com o “sudo docker-compose up”, aperte “Ctrl + C”, após o docker finalizar completamente utilize o “ifconfig” novamente.

Configuração de endereço do servidor:
Acesse a pasta /etc/netplan/ utilizando o comando “cd” e use o comando “ls” para mostrar o arquivo que existe dentro desse diretório;
Após isso, utilize o comando “cat [nome do arquivo]” para verificar as informações contidas no arquivo. Você verá algo parecido com isso:
 Utilize o comando “sudo nano [nome do arquivo]” para editar o conteúdo contido nele e adicione as linhas (lembrar das indentações corretas conforme a imagem abaixo): 
renderer: networkd
addresses: 
192.168.121.221/24
routes: 
to: default
via: 192.168.121.1
nameservers:
addresses: [8.8.8.8, 1.1.1.1]


Não esqueça de trocar o “dhcp4” para “no”;
Utilize “Ctrl + X”, aperte Y e aperte a tecla Enter;
Logo após, utilize o comando “sudo netplan apply” para aplicar as modificações;





Instalação Openredu:
Para começar instale o docker “sudo apt-get install docker”;
Depois, instale o docker-compose “sudo apt-get install docker-compose”;
Clone o repositório do Openredu “git clone https://github.com/Openredu/Openredu”;
Após o clone, acesse o diretório Openredu e acesse a pasta _nginx;
Edite o arquivo “Dockerfile” usando “sudo nano Dockerfile”, troque “python” por “python3” e salve as alterações;
Volte para a pasta do Openredu e utilize o comando “sudo docker-compose build”;
Execute: “sudo docker-compose run --rm app bundle exec rake db:setup”;
Se o passo anterior não funcionar corretamente execute: “sudo docker-compose run --rm app bundle exec rake db:setup RAILS_ENV=test”;
Se ocorrer erros de network, utilize os comandos solicitados pelos erros;
Se após a utilização do comando de “docker-compose run…” ainda existirem erros, modifique o arquivo .env.example com o comando “cp .env.example .env” e utilize um dos comandos de “docker-compose run…” novamente;
Finalize colocando o container no ar com “sudo docker-compose up”. Depois disso, você poderá acessar o openredu através do endereço IP do servidor.

Observação: Sempre que você quiser abrir o servidor é necessário entrar na pasta do Openredu e usar o “sudo docker-compose up” para que o container do docker possa ficar ativo.
