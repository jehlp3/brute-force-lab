# brute-force-lab
Laboratório de ataque Brute Force utilizando Kali Linux, Medusa e Metasploitable 

### **OBJETIVO**

Implementar, documentar e compartilhar um projeto prático utilizando **Kali Linux** e a ferramenta **Medusa**, em conjunto com ambientes vulneráveis (por exemplo, **Metasploitable 2** e **DVWA**), para simular cenários de ataque de força bruta e exercitar medidas de prevenção.

- **Configurar o ambiente**: duas VMs (Kali Linux e Metasploitable 2) no VirtualBox, com rede interna (*host-only*).
- **Executar ataques simulados**: força bruta em **FTP**, automação de tentativas em **formulário web (DVWA)** e **password spraying** em **SMB** com enumeração de usuários.
- **Documentar os testes**: wordlists simples, comandos utilizados, validação de acessos e recomendações de mitigação.

### **PASSO A PASSO**

## Escopo
Ambiente exclusivamente local, em rede **host-only**, sem exposição à internet.

## Arquitetura
### Links para Download e Instalação

### VirtualBox
- [Baixe o VirtualBox](https://www.virtualbox.org/) para criar e gerenciar as máquinas virtuais.
- **Tutorial em vídeo**: [Como Instalar o VirtualBox - YouTube](https://www.youtube.com/watch?v=08kjtE9xjL8)
  
### Kali Linux
- [Baixe o Kali Linux](https://www.kali.org/get-kali/) para começar a instalação.
- **Tutorial em vídeo**: [Como Instalar o Kali Linux - YouTube](https://www.youtube.com/watch?v=YfDYMIbHb-0)

### Metasploitable 2
- [Baixe o Metasploitable 2](https://rapid7.com/download/metasploitable/) para criar seu ambiente vulnerável.
- **Tutorial em vídeo**: [Como Baixar e Instalar o Metasploitable 2 - YouTube](https://www.youtube.com/watch?v=aJvZaOn-92M)

## Ferramentas
- **Medusa**: Ferramenta de brute force
- **enum4linux**: Enumeração de informações do Samba
- **nmap**: Scans de rede
- **curl**: Automação de tentativas em formulários web
- **smbclient**: Acesso e enumeração SMB

## Endereçamento
### Configuração da Rede no VirtualBox, Inicialização e Consulta de IP

### 2. Configurar as Máquinas Virtuais

#### **Kali Linux:**
1. Selecione a VM do **Kali Linux**.
2. Vá em **Settings > Network** e configure o **Adapter 1** para **Host-only Adapter**.

#### **Metasploitable 2:**
1. Selecione a VM do **Metasploitable 2**.
2. Vá em **Settings > Network** e configure o **Adapter 1** para **Host-only Adapter**, escolhendo a mesma rede **vboxnet0**.

### 3. Inicializar as VMs
1. No **VirtualBox**, clique em **Start** para iniciar as VMs do **Kali Linux** e **Metasploitable 2**.

### 4. Consultar o IP

No terminal (de ambas as máquinas virtuais), execute:
ip a

### 4. Testas a conectividade
1. No terminal do **Kali Linux** digite o comando
ping <ip da máquina virtual Metasploitable2>
Valide a conexão

2. No terminal do **Metasploitable2** digite o comando
ping <ip da máquina virtual Kali Linux>
Valide a conexão

### Observação Importante sobre Rede Host-Only

Com a configuração de Host-Only, as máquinas virtuais podem se comunicar entre si, mas não têm acesso à internet. Isso ocorre porque a rede Host-Only no VirtualBox cria uma rede isolada, onde o host e as VMs podem se comunicar, mas sem conexão com redes externas, como a internet.

Portanto, se você tentar pingar o Google (ou qualquer outro domínio externo), o comando falhará, pois não há acesso ao gateway da internet. Para permitir acesso à internet, você precisaria configurar a VM para usar NAT ou Bridge Adapter, permitindo a comunicação com redes externas.

## Enumeração inicial
Realize um scan inicial para identificar os serviços expostos no Metasploitable 2:

```bash
nmap -sV -Pn -p 21,80,139,445 <ipMetasploitable2>

