# dio-lab-bruteforce-medusa
Simulação de ataques de Força Bruta e Password Spraying com Kali Linux e Medusa em ambiente controlado - Bootcamp Santander DIO.

Sobre o Projeto
Este projeto foi desenvolvido como desafio prático do **Bootcamp Santander Cibersegurança** na plataforma da **DIO (Digital Innovation One)**.

O objetivo principal foi simular, em ambiente controlado e isolado via VirtualBox, cenários reais de ataques de força bruta (*Brute Force*) e pulverização de senhas (*Password Spraying*) utilizando o **Kali Linux** e a ferramenta **Medusa**, auditando três serviços expostos na máquina vulnerável **Metasploitable 2**:
1. **FTP (File Transfer Protocol - Porta 21)**
2. **Formulário Web de Autenticação (DVWA - Porta 80)**
3. **SMB / Compartilhamento de Arquivos Windows-Linux (Porta 445)**

---

##  Topologia do Laboratório

```
+------------------------------------+          +------------------------------------+
|            KALI LINUX              |          |          METASPLOITABLE 2          |
|             (Atacante)             | <======> |           (Alvo Vulnerável)        |
|          IP: X.X.X.X               | Host-Only|          IP: Y.Y.Y.Y               |
| Ferramentas: Medusa, Nmap, Firefox |          | Serviços: vsftpd, Apache/DVWA, SMB |
+------------------------------------+          +------------------------------------+
```

* **Hipervisor:** Oracle VirtualBox
* **Configuração de Rede:** Modo Host-Only (Placa de rede exclusiva do hospedeiro), garantindo 100% de isolamento da rede doméstica e da Internet.

---

## ⚡ Execução dos Cenários de Teste

### 1. Cenário 1: Força Bruta em Serviço FTP (Porta 21)
* **Objetivo:** Descobrir credenciais de acesso ao servidor `vsftpd 2.3.4`.
* **Comando Utilizado:**
  ```bash
  medusa -h <IP_ALVO> -u msfadmin -P senhas.txt -M ftp
  ```
* **Evidência:**
  *(Insira o print da tela do terminal mostrando `ACCOUNT FOUND` aqui)*
  `![FTP Brute Force](images/cenario1_ftp.png)`

---

### 2. Cenário 2: Ataque em Formulário Web (DVWA)
* **Objetivo:** Efetuar bypass de autenticação no módulo *Brute Force* da aplicação *Damn Vulnerable Web Application* configurada no nível de segurança **Low**.
* **Comando Utilizado:**
  ```bash
  medusa -h <IP_ALVO> -u admin -P senhas.txt -M web-form -m FORM:"vulnerabilities/brute/":USER:password:DENY:"incorrect"
  ```
* **Evidência:**
  *(Insira o print da tela do DVWA logado aqui)*
  `![DVWA Login](images/cenario2_web.png)`

---

### 3. Cenário 3: Password Spraying & Brute Force em SMB (Porta 445)
* **Objetivo:** Testar senhas padrão e credenciais fracas contra múltiplos usuários de sistema identificados.
* **Comando Utilizado:**
  ```bash
  medusa -h <IP_ALVO> -U usuarios.txt -p msfadmin -M smbnt
  ```
* **Evidência:**
  *(Insira o print do terminal com o sucesso da autenticação SMB)*
  `![SMB Password Spraying](images/cenario3_smb.png)`

---

## 🛡️ Análise Defensiva & Medidas de Mitigação (Blue Team)

| Vulnerabilidade / Vetor | Impacto | Medida de Mitigação Recomendada |
| :--- | :--- | :--- |
| **Ausência de limite de tentativas (No Rate-Limit)** | Permite milhares de requisições de autenticação automatizadas por segundo. | Implementar **Fail2ban** ou regras de firewall dinâmicas bloqueando IPs que errarem $\ge 3$ tentativas consecutivas. |
| **Senhas fracas e previsíveis** | Sucesso quase imediato de dicionários padrão (*rockyou*, senhas padrão de fornecedores). | Imposição de política de complexidade de senhas (mínimo de 12 caracteres, alfanumérico e símbolos). |
| **Falta de 2FA/MFA** | O comprometimento da senha garante acesso total ao invasor. | Habilitação obrigatória de **Autenticação em Duas Etapas** para logins administrativos e remotos. |
| **Protocolos em texto claro (FTP / HTTP)** | Credenciais podem ser interceptadas na rede via sniffer (Wireshark/tcpdump). | Descontinuação de FTP e HTTP em favor de **SFTP/SSH** e **HTTPS com certificados TLS válidos**. |

---

## 👨‍💻 Autor
* **Aluno:** TEÓFILO HENRIQUE DAMAZIO FIUMARI (MALZS)
* **Trilha:** Bootcamp Santander Cibersegurança - DIO
* **Foco:** Defesa e Ataque Ético (Red & Blue Team)
