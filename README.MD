# Linux SSH Lab

Laboratório prático para configuração e utilização de SSH entre um computador Windows e uma máquina virtual Ubuntu.

O objetivo foi entender na prática como funciona a autenticação SSH utilizando chaves públicas e privadas, além da utilização do `ssh-agent` e do acesso remoto através do VS Code.

## Ambiente

### Cliente

* Sistema operacional: Windows
* Usuário: Dyego
* PowerShell
* OpenSSH Client

### Servidor

* Sistema operacional: Ubuntu 26.04 LTS
* Hostname: VMLinux
* Usuário: vboxuser
* SSH na porta 22

## 1. Servidor SSH

O Ubuntu possui o serviço SSH configurado e em execução.

O serviço responsável por receber as conexões SSH é o `sshd`.

A configuração do servidor fica localizada em:

```text
/etc/ssh/sshd_config
```

A porta utilizada neste laboratório foi a porta padrão 22.

## 2. Geração das chaves SSH

No Windows foi gerado um par de chaves utilizando Ed25519:

```powershell
ssh-keygen -t ed25519
```

O comando gera dois arquivos:

```text
id_ed25519
id_ed25519.pub
```

### Chave privada

```text
id_ed25519
```

A chave privada permanece no computador cliente e foi protegida por uma passphrase.

Ela não deve ser compartilhada.

### Chave pública

```text
id_ed25519.pub
```

A chave pública foi adicionada ao servidor Ubuntu no arquivo:

```text
~/.ssh/authorized_keys
```

## 3. Autenticação por chave pública

O arquivo `authorized_keys` contém as chaves públicas autorizadas para o usuário.

Durante a autenticação, o cliente utiliza a chave privada correspondente para provar que possui a chave autorizada pelo servidor.

A chave privada não é enviada para o servidor.

## 4. Configuração do SSH

Foi configurado o arquivo de configuração do SSH no Windows:

```text
~/.ssh/config
```

Configuração utilizada:

```text
Host vmlinux
    HostName 192.168.0.11
    User vboxuser
    IdentityFile ~/.ssh/id_ed25519
```

Com essa configuração, posso acessar a VM utilizando:

```powershell
ssh vmlinux
```

em vez de informar o usuário, IP e chave manualmente.

## 5. ssh-agent

O `ssh-agent` foi utilizado para manter a chave privada desbloqueada em memória.

A chave foi adicionada ao agente com:

```powershell
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

Para verificar se a chave estava carregada:

```powershell
ssh-add -l
```

Depois disso, foi possível realizar a conexão:

```powershell
ssh vmlinux
```

sem precisar informar novamente a passphrase da chave.

## 6. Acesso pelo VS Code

Também utilizei o VS Code com a extensão Remote SSH para acessar a VM Ubuntu.

Com isso, foi possível trabalhar diretamente na máquina Ubuntu através do VS Code instalado no Windows.

## Conceitos praticados

* SSH
* ssh
* sshd
* Chaves SSH
* Ed25519
* Chave pública
* Chave privada
* authorized_keys
* ssh-agent
* ssh-add
* ~/.ssh/config
* PowerShell
* Linux
* Ubuntu
* VS Code Remote SSH

## O que aprendi

O principal objetivo deste laboratório foi entender como o SSH funciona na prática, e não apenas executar os comandos.

Um dos pontos mais importantes foi entender a diferença entre a chave pública e a chave privada.

A chave privada permanece no computador cliente, enquanto a chave pública é instalada no servidor.

Também entendi a função da passphrase e como o `ssh-agent` pode manter a chave desbloqueada em memória, evitando a necessidade de informar a passphrase em todas as conexões.

## Próximos passos

* Configurar autenticação SSH somente por chave.
* Desabilitar autenticação por senha.
* Criar usuários específicos para acesso SSH.
* Praticar permissões em `.ssh` e `authorized_keys`.
* Explorar configurações de segurança do `sshd`.
* Utilizar Ansible para automatizar a configuração.
