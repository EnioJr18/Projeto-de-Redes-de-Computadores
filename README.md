# Documentação do Projeto Final de Fundamentos de Redes de Computadores
**Turma:** BSI-2026.1 <br>
**Grupo:** 5 <br>
**Integrantes:** David Gabriel Lima Martins, Enio Eduardo Leão da Silva Junior, Entony Richard da Silva Santos e Igor Matheus da Silva Santos. <br>

## 1. Definição de Sub-rede (Grupo 5)
A turma utiliza o bloco principal 192.168.26.0/24. Seguindo a divisão de sub-redes com a máscara /28 (onde cada grupo possui um bloco de 16 endereços IP), o cálculo para o Grupo 5 resulta nos seguintes parâmetros:

**Endereço de Rede:** 192.168.26.64/28 <br>
**Máscara de Rede:** 255.255.255.240 ou /28 <br>
**Primeiro IP Útil:** 192.168.26.65 <br>
**Último IP Útil:** 192.168.26.78 <br>
**Endereço de Broadcast:** 192.168.26.79 <br>

## 2. Tabelas de Definições (Etapa 1 - Item A)
### Tabela 2.1 
Endereçamento IP, Nomenclatura e Domínio (FQDN) <br>
O domínio base adotado para o grupo é grupo5.bsi-26-1.maceio.lab. <br>



### Tabela 2.2
Configuração de Hardware das Máquinas VirtuaisTodas as 8 instâncias foram provisionadas de forma homogênea utilizando o sistema operacional Ubuntu Server. <br>
_______________________________________________________________________________________________________________________ <br>
| Instância    | Sistema Operacional   | Memória RAM      | Processadores (Cores)      | Espaço em Disco              | <br>
| MV1 a MV8    | Ubuntu Server         | 1024 MB (1 GB)   | 1vCPU                      | 20 GB (Dinamicamente Alocado)| <br>


## 3. Tutorial: Criação da Máquina Virtual (VirtualBox)
Para garantir a padronização do ambiente, todas as 8 máquinas virtuais devem ser criadas seguindo o processo detalhado abaixo, utilizando o Oracle VM VirtualBox.

### Passo 1: Nome da Máquina Virtual e Sistema Operacional
* Abra o VirtualBox e clique em "Novo".
* VM Name: Defina o nome da máquina (no exemplo da imagem, ubuntu_server9, mas lembre-se de seguir o padrão da Tabela 2.1, como servidor1, servidor2, etc.).
* Folder: Escolha o diretório onde os arquivos da VM serão salvos.
* ISO Image: Selecione a imagem ISO do sistema operacional (utilizamos a versão resolute-live-server-amd64.iso do Ubuntu).
* O VirtualBox detectará automaticamente que se trata de uma distribuição Linux Ubuntu (64-bit).

### Passo 2: Configuração de Instalação Desassistida (Unattended Install)
* Nesta etapa, o VirtualBox permite pré-configurar os dados de acesso para adiantar a instalação do SO.
* User Name: Defina o usuário inicial (no exemplo está vboxuser, mas posteriormente criaremos os usuários com os nomes dos integrantes do grupo e o admin.grupo5).
* Senha: Insira e confirme uma senha padrão para a máquina.
* Host Name: Defina o nome do host (no exemplo está ubuntu_server9).

### Passo 3: Especificação de Hardware Virtual (Memória e Processador)
* Na aba de hardware, aloque os recursos que a máquina poderá utilizar do host físico.
* Base Memory (Memória RAM): Arraste a barra ou digite 512 MB.
* Processors (CPUs): Mantenha em 1 CPU.
* Deixe a opção "Use EFI" desmarcada.

### Passo 4: Especificação do Disco Rígido Virtual
* Selecione a opção "Create a New Virtual Hard Disk".
* Disk Size: Defina o tamanho do disco para 32,00 GB.
* Hard Disk File Type and Format: Escolha o formato VDI (VirtualBox Disk Image) e marque a caixa "Pré-alocar Tamanho Total" para garantir que o espaço de 32 GB seja reservado imediatamente no disco físico, melhorando o desempenho da VM.
* Clique em "Finalizar" para criar a máquina virtual. Repita o processo para as demais 7 instâncias necessárias.

## 4. Roteiro Passo-a-Passo de Configuração (Etapa 2)
Este guia prático descreve os procedimentos realizados em cada uma das 8 máquinas virtuais do ambiente.

### Passo 4.1
Alteração do Hostname do Sistema
Em cada máquina virtual, execute o comando abaixo para definir o respectivo nome único:
```bash
sudo hostnamectl set-hostname servidor1
```

Para validar a alteração, utilize:
```bash
hostname
```

### Passo 4.2: Configuração de Endereçamento IP Estático
No Ubuntu Server, a configuração de rede é gerenciada pelo Netplan. Acesse o diretório de arquivos de configuração:

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
Insira a estrutura estática correspondente à interface de rede (exemplo para o Servidor 1):

```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3: # Verifique o nome correto da interface usando 'ip a'
      dhcp4: no
      addresses:
        - 192.168.26.65/28
      routes:
        - to: default
          via: 192.168.26.64 # Ajustar conforme o gateway da infraestrutura de laboratório
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
```

Aplique as novas diretivas de rede:
```bash
sudo netplan apply
```

### Passo 4.3: Mapeamento de IP e Nomes Local (/etc/hosts)
Para permitir a resolução de nomes interna sem a necessidade imediata de um servidor DNS ativo, edite o arquivo /etc/hosts de todas as 8 VMs:

```bash
sudo nano /etc/hosts
```

Adicione o seguinte bloco idêntico no final do arquivo de cada máquina:
```
# Mapeamento do Grupo 5 - bsi-26-1
192.168.26.65  servidor1.grupo5.bsi-26-1.maceio.lab  servidor1  s1
192.168.26.66  servidor2.grupo5.bsi-26-1.maceio.lab  servidor2  s2
192.168.26.67  servidor3.grupo5.bsi-26-1.maceio.lab  servidor3  s3
192.168.26.68  servidor4.grupo5.bsi-26-1.maceio.lab  servidor4  s4
192.168.26.69  servidor5.grupo5.bsi-26-1.maceio.lab  servidor5  s5
192.168.26.70  servidor6.grupo5.bsi-26-1.maceio.lab  servidor6  s6
192.168.26.71  servidor7.grupo5.bsi-26-1.maceio.lab  servidor7  s7
192.168.26.72  servidor8.grupo5.bsi-26-1.maceio.lab  servidor8  s8
```

### Passo 4.4: Gerenciamento de Usuários do Grupo
Conforme a exigência de conter os usuários com privilégios administrativos e os integrantes do grupo baseados na chamada oficial, execute os seguintes comandos em cada uma das máquinas para criar as contas:

```bash
# Criação do usuário administrador do projeto
sudo adduser admin.grupo5
sudo usermod -aG sudo admin.grupo5

# Criação das contas dos integrantes do grupo
sudo adduser david.nome
sudo adduser enio.junior
sudo adduser entony.nome
sudo adduser igor.nome
```

## 5. Plano de Testes e Validação (Etapa 2 - Apresentação)
Para a entrega final, este espaço será preenchido com as capturas de tela e saídas de terminal coletadas pelo grupo.

### 5.1 Testes de Conectividade ICMP (Ping)
Exemplo de rotina a ser executada partindo do servidor1 em direção aos demais aliases:

(Inserir aqui os prints dos resultados de ping bem-sucedidos)

### 5.2 Testes de Acesso Remoto (SSH)
Validação de autenticação cruzada utilizando os usuários criados:

(Inserir aqui os prints demonstrando o acesso SSH ativo entre os nós)
