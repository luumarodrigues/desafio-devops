# desafio-devops

## Antes de tudo é necessário configurar o proxy para evitar inconvenientes


### 1. Configurando o proxy da máquina virtual para utilizar as ferramentas e desativando o firewall

    Digite estes comandos no terminal da vm (substitua $proxyUser e $proxyPassword pelo seu usuário e senha do proxy e trocar o endereço do proxy pelo proxy da sua empresa)

```shell
git config --global http.proxy http://$proxyUser:$proxyPassword@proxy.com:porta
export http_proxy=http://$proxyUser:$proxyPassword@proxy.com:porta
export https_proxy=http://$proxyUser:$proxyPassword@proxy.com:porta
export ftp_proxy=http://$proxyUser:$proxyPassword@proxy.com:porta

systemctl disable firewalld
systemctl stop firewalld
dhclient
```

    após isso acessar a pasta etc e criar um arquivo chamado environment e escrever nele o seguinte texto:

```shell
export http_proxy="http://usuario:%senha@proxy.com:porta"
export https_proxy="http://usuario:%senha@proxy.com:porta"
export ftp_proxy="http://usuario:%senha@proxy.com:porta"
export no_proxy="localhost,127.0.0.0,192.168.56.101,172.20.148.72"
export JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre/bin/java"
```


### 2. Instalando e Configurando o docker

    digitar no terminal

```shell
setenforce 0
vi /etc/selinux/config
```

    Procurar por SELINUX e alterar para:
    
    SELINUX=disabled

    digitar no terminal

```shell
dhclient
vi /etc/sysconfig/network-scripts/ifcfg-*
```
    Procurar por ONBOOT e alterar para:
    
    ONBOOT=yes

    Após isso instalar e iniciar o docker usando os seguintes comandos:

```shell
curl -s https://get.docker.com | bash
systemctl enable docker.service
systemctl start docker.service
usermod -a -g docker root
```

    Para configurar o proxy do docker usar o seguinte comando:

```shell
mkdir /etc/systemd/system/docker.service.d
cd /etc/systemd/system/docker.service.d
vi http-proxy.conf
```

    Digitar o seguinte texto e salvar o arquivo
    [Service]
    Environment="HTTP_PROXY=http://usuario:%senha@proxy.com:porta"
    Environment="NO_PROXY=localhost,172.20.148.72,10.130.214.119"

    Para testar digite o seguinte comando no terminal:

```shell
docker run hello-world
```

    Esse comando deve retornar a seguinte mensagem

    Hello from Docker!
    This message shows that your installation appears to be working correctly.


### 3. Instalando e configurando o JDK

    Executar no terminal o seguinte comando:

```shell
yum install java-1.8.0-openjdk-devel
export JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre/bin/java"
```

    Para testar a instalação execute o comando 

```shell
java -version
```

    Deve retornar o seguinte texto:

    java version "1.8.0_181"
    Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
    Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)

### 4. Instalando e Configurando o MAVEN

    Executar os seguintes comandos no terminal:

```shell
wget http://www.eu.apache.org/dist/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
tar xzf apache-maven-3.3.9-bin.tar.gz
mkdir /usr/local/maven
mv apache-maven-3.3.9/ /usr/local/maven/
alternatives --install /usr/bin/mvn mvn /usr/local/maven/apache-maven-3.3.9/bin/mvn 1
alternatives --config mvn
```

    Para testar a instalação executar o seguinte comando:

```shell
mvn --version
```

    Deve retornar o seguinte texto:

    Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-10T14:41:47-02:00)
    Maven home: /usr/local/maven/apache-maven-3.3.9
    Java version: 1.8.0_181, vendor: Oracle Corporation
    Java home: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.181-3.b13.el7_5.x86_64/jre
    Default locale: en_US, platform encoding: UTF-8
    OS name: "linux", version: "3.10.0-862.11.6.el7.x86_64", arch: "amd64", family: "unix"

### 5. Instalando e Configurando o Jenkins

    Para instalar digitar o seguinte comando no terminal:

```shell
yum install jenkins
systemctl enable jenkins
systemctl start jenkins jenkins
```

    Digite no navegador o ip ou dns do servidor e a porta da seguinte forma http://ip:8080 e seguir os passos da instalação

    + no primeiro passo será solicitado o secret, acessar o endereço informado na tela para pegar esse secret

    + configurar proxy passando as credenciais o link deve ser sem http://

    + selecionar a opção de instalar suggestd plugins

    +  configurar usuário admin (anotar este usuário e senha, pois eles serão necessários para usar a ferramenta.)

    Adicionar o jenkins no grupo do docker para que o mesmo tenha permissão de utiliza-lo, execute no terminal:

```shell
usermod -aG docker jenkins
```





