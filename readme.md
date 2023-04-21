# Curso Ansible



O DevOps busca maneiras de encurtar o seu ciclo de feedbacks entre os times de desenvolvimento e estrutura, tornar o processo com repetições tanto do lado no lado da infraestrutura - como já é feito no lado do desenvolvimento. Se pensarmos em uma forma simples de definir DevOps, trata-se de levarmos a cultura ágil para a cultura de infraestrutura e integrá-las.


> **O nome vem exatamente da junção dos mundos "Dev" e "Ops", transformando-os em um único (ou o mais próximo disso).**

É natural que exista uma diferença entre a maneira como as empresas trabalham essa ideia, de como ela é aplicada, não existe uma "bala de prata" ou um formato fixo e perfeito. Cada cultura organizacional entenderá qual é a melhor forma de aplicar os princípios de DevOps.

Ao falarmos de DevOps, conseguiremos **automatizar** tudo aquilo que é feito na infraestrutura e garantir o **estado dos servidores**.

Como isso era feito antes de se falar de infraestrutura no código? O profissional de infra criava diversos scripts para modificar o ambiente ou fazia configurações manuais. Imagine a situação de lidar com centenas de servidores e ter que configurar todos eles.

Era difícil garantir o estados das máquinas da mesma forma. O princípio de infraestrutura como código nasce para descrevermos o estado no qual precisamos que os servidores estejam. O Ansible é uma ferramenta para garantirmos esse estado.

Nós escreveremos uma série de scripts que garantem que os servidores controlados ficarão em um determinado estado. Vamos analisar superficialmente a topologia do Ansible.

![](https://s3.amazonaws.com/caelum-online-public/746-ansible/01/1.2_1_topologia+Ansible.png)


Nós temos uma máquina de controle, que é o servidor onde o Ansible será executado. Basta tê-lo instalado, lembrando que este é baseado em Python (que deve estar igualmente presente na sua máquina). Temos ainda os hosts que devem ser controlados.

Diferente de algumas tecnologias de Infraestrutura como código, o Ansible não é dependente da instalação nas máquinas que devem ser configuradas. Desde que elas tenham uma conexão SSH funcional e que tenham **Python instalado**, a maioria dos servidores que você usa em produção e desenvolvimento (sempre encontraremos os dois, Python e SSH).

Principalmente quando falamos em Linux, isso se torna obrigatório. Conseguimos **alterar o estado da máquina via conexão SSH**, sem que seja necessário entrar nelas e digitar algum comando. O Ansible será responsável por fazer isso.

O seu trabalho é desenvolver os scripts que o Ansible usa - neste caso, falamos de três modelos de **script**:

- ***Arquivo de inventário***: que irá **levantar as máquinas** que serão configuradas;
- ***Playbook***: **"receita"** que nos diz o que devemos fazer;
- ***Roles***: permite **modularizar o código**.

Além disso, não existe um número limite de máquinas. É indiferente se você tem no seu inventário uma ou mil máquinas, existem diferentes formas de montar esse inventário (que se trata da lista de máquinas a serem configuradas).

### O que é o Ansible? E para que serve?

O Ansible é uma ferramenta de automação de TI de código aberto que é usada para gerenciar a configuração, implantação e orquestração de aplicativos em vários servidores. Ele foi desenvolvido pela Red Hat em 2012 e atualmente é mantido pela Ansible, Inc. (uma subsidiária da Red Hat).

O Ansible permite que os administradores de sistemas automatizem tarefas repetitivas em vários servidores, incluindo a configuração de software, a implantação de aplicativos, a manutenção de sistemas, a gestão de configurações e a orquestração de infraestruturas complexas. Ele usa a linguagem YAML para definir tarefas e playbooks, que são arquivos que definem um conjunto de tarefas a serem executadas em vários servidores.

Algumas das principais vantagens do Ansible incluem:

- Simplicidade: o Ansible é fácil de aprender e usar, mesmo por aqueles sem conhecimento avançado em programação.

- Eficiência: o Ansible é projetado para ser rápido e escalável, permitindo que os usuários gerenciem milhares de servidores com facilidade.

- Flexibilidade: o Ansible suporta uma ampla variedade de plataformas e pode ser usado para gerenciar infraestruturas em nuvem, local ou híbridas.

- Segurança: o Ansible utiliza SSH e SSL para proteger a comunicação entre os servidores, além de criptografar senhas e chaves privadas.

Em resumo, o Ansible é uma ferramenta de automação de TI poderosa e flexível que permite que os administradores de sistemas gerenciem facilmente a configuração, implantação e orquestração de aplicativos em vários servidores.

### O primeiro playbook

Um dos propósitos do Ansible é automatizar a infraestrutura e tornar simples a reprodução e a realização de testes. Desta forma, teremos a opção de colarmos em um controle de versão, trataremos o que fazemos na infraestrutura da mesma maneira como fazemos com o código de um serviço de uma aplicação normal.

O formato ```yml``` é o padrão adotado pleo Ansible, para a criação dos arquivos de configuração. É um derivado de como criamos dicionários em Python

```yml
---
- hosts: all
  tasks: 
   - shell: 'echo hello > /vagrant/world.txt'

```
**vagrant up** - Subir máquina virtual com vagrant
**vagrant ssh** - entrar no modo ssh na máquina virtual

**Comando para executar o Ansible:**

```shell
$ ansible wordpress -u vagrant --private-key .vagrant/machines/wordpress/virtualbox/private_key -i hosts -m shell -a 'echo Hello, World'
```

**Resultado:**
```shell
172.17.177.40 | SUCCESS | rc=0 >>
Hello, World
```
Esse retorno do Ansible significa que ele rodou um comando no servidor `172.17.177.40`, e que a execução foi bem-sucedida. A saída foi `Hello, World`.

**Nós passamos:**

- grupo de hosts que queremos rodar o comando;
- qual nome do usuário;
- a chave privada (e evitar passar senha na linha de comando);
- informamos o arquivo de inventário para ele conferir os hosts que configuraremos;
- informamos o módulo que executaremos: shell;
- quais os argumentos que estamos passando: echo Hello, World (comando que eu executaria direto no bash).

No entanto, é possível compreender se ele fez uma conexão SSH? Adicionaremos o parâmetro -vvvv na execução, assim iremos os assegurar. O Ansible vai ficar mais verboso quando for executado, mas conseguiremos ver exatamente o que está acontecendo. Isto é bastante útil quando estamos depurando ou desenvolvendo um script'.

```shell
$ ansible -vvvv wordpress -u vagrant --private-key .vagrant/machines/wordpress/virtualbox/private_key -i hosts -m shell -a 'echo Hello, World'
```

Se você tiver alguma dúvida sobre o que aconteceu do ponto de vista de conectividade no Ansible, caso tenha algum problema para logar, um usuário em que antes conseguimos acessar a máquina, mas não é mais possível, ou outras informações sobre problemas, o padrão -vvvv pode ser útil para ajudar a depurar.

**hosts** - lista de filtro de hosts
O filtro para você indicar quais hosts foram colocados no inventário, que será trabalhado pelo Ansible.

**tasks** - Lista de comandos que serão aplicados nesses hosts filtrados

**Rodando o playbook com o comando:**

```shell
ansible-playbook provisioning.yml -u vagrant -i hosts --private-key .vagrant/machines/wordpress/virtualbox/private_key 
```

### Instalando Dependências

```yml
---
- hosts: all
  tasks:
    - name: 'Instala o PHP5'
      apt:
        name: php5
        state: latest
      become: yes
    - name: 'Instala o Apache2'
      apt:
        name: apache2
        state: latest
      become: yes
```

### Simplificando o playbook

```yml
---
- hosts: all
  tasks:
    - name: 'Instala pacotes de dependencia do sistema operacional'
      apt:
        name: "{{ item }}"
        state: latest
      become: yes
      with_items:
        - php5
        - apache2
        - libapache2-mod-php5
        - php5-gd
        - libssh2-php
        - php5-mcrypt
        - mysql-server-5.6
        - python-mysqldb
        - php5-mysql
```

Este é um arquivo YAML que define um playbook do Ansible para instalar uma série de pacotes de software em um grupo de hosts. Aqui está uma explicação de cada linha:

- **A primeira linha, "---"**, é um separador YAML.

- **A segunda linha, "- hosts: all"**, indica que esta tarefa será executada em todos os hosts do inventário Ansible.

- **A terceira linha, "tasks:"**, indica que a lista de tarefas segue.

- **A partir da quarta linha, começa a definição da tarefa "Instala pacotes de dependencia do sistema operacional"**.

- **A linha 4, "- name:"**, define um nome descritivo para a tarefa.

- **A linha 5, "apt:"**, especifica o módulo "apt" do Ansible, que é usado para instalar pacotes em sistemas baseados em Debian ou Ubuntu.

- **A linha 6, "name: "{{ item }}"**, especifica o nome do pacote a ser instalado, usando a sintaxe de interpolação do Ansible para obter o valor do item atual da lista "with_items".

- **A linha 7, "state: latest"**, especifica que o pacote deve ser atualizado para a versão mais recente.

- **A linha 8, "become: yes"**, indica que a tarefa será executada com privilégios de superusuário.

- **A partir da linha 9**, "with_items:", começa a lista de pacotes a serem instalados. Cada item da lista será passado para o campo "name" da tarefa "apt" como um argumento separado. Neste exemplo, a lista contém nove pacotes diferentes, incluindo PHP, Apache, MySQL e outros.

### Dica

```yml
    - name: 'Instala pacotes do sistema operacional'
      apt:
        name: '{{ item }}'
        state: latest
      become: yes
      with_items:
        - php5
        - apache2
        - libapache2-mod-php5
```

As versões mais recentes do Ansible descontinuaram (deprecated) essa forma de laço a favor de um nova sintaxe mais enxuta.

___

Abaixo o exemplo do mesmo laço mas agora listando os pacotes pelo `name`:

```yml
    - name: 'Instala pacotes do sistema operacional'
      apt:
        name:
        - php5
        - apache2
        - libapache2-mod-php5
        state: latest
      become: yes
```
### Trabalhando com Vários Logins

```host
[wordpress]
172.17.177.40 ansible_user=vagrant ansible_ssh_private_key_file="/home/wolf/MinhasFormacoes/FormacaoAnsible/mod1/.vagrant/machines/wordpress/virtualbox/private_key"
```

```shell
ansible-playbook provisioning.yml -i hosts
```

### Configuração do banco de dados

```yml
    - name: 'Cria o banco do MySQL'
      mysql_db:
        name: wordpress_db
        login_user: root
        state: present
```

Neste arquivo YAML, há um módulo chamado "mysql_db" sendo usado para criar um banco de dados MySQL com o nome "wordpress_db" e o usuário "root" com permissão para acessá-lo. O campo "state" indica o estado desejado para o banco de dados.

Existem três possíveis valores que podem ser definidos para o campo "state":

1. **"present":** indica que o recurso deve existir, ou seja, o Ansible deve criar o banco de dados se ele não existir.

2. **"absent":** indica que o recurso não deve existir, ou seja, o Ansible deve excluir o banco de dados se ele existir.

3. **"dumped":** indica que o recurso deve ser salvo em um arquivo de backup, que pode ser usado posteriormente para restaurar o estado do recurso.

No exemplo apresentado, o valor **"present"** está sendo usado, o que significa que o Ansible deve criar o banco de dados **"wordpress_db"** se ele ainda não existir no servidor. Se o banco de dados já existir, o **Ansible não fará nenhuma alteração**.

### Criação do usuário no banco

```yml
    - name: 'Cria o usuario do MySQL'
      mysql_user:
        login_user: root
        name: wordpress_user
        password: 12345
        priv: 'wordpress_db.*:ALL'
        state: present
```

**OBS!!!** O formato da string de privilégio é: base_de_dados.tabela:privilegio. A base de dados é alura_ansible, as tabelas são todas (logo, utiliza-se **`*`**) e para o usuário poder fazer todas as operações comuns no banco de dados, usa-se **`ALL`**.
