# Investigacao de Incidente: Analise de Trafego de Rede com tcpdump

## 1. Visao Geral do Incidente
Clientes da empresa yummyrecipesforme.com relataram que, ao carregar o site, o navegador exigia o download de um arquivo executavel para liberar acesso a receitas. Apos executar o arquivo, as maquinas apresentavam lentidao extrema e o navegador redirecionava os usuarios para um dominio desconhecido (greatrecipesforme.com).

Como analista de seguranca, reproduzi o incidente em ambiente isolado (sandbox), inspecionei os pacotes de rede via linha de comando e documentei a cadeia do ataque.

---

## 2. Metodologia e Ferramentas
* Ambiente: Sandbox isolada
* Ferramenta de Captura: tcpdump (CLI)
* Protocolos Analisados: DNS (porta 53), TCP e HTTP (porta 80)

---

## 3. Analise Tecnica dos Logs

### Fase 1: Acesso ao Site Legítimo
A maquina consulta o servidor DNS para obter o IP do site yummyrecipesforme.com (203.0.113.22):

14:18:32.192571 IP your.machine.52444 > dns.google.domain: 35084+ A? yummyrecipesforme.com. (24)
14:18:32.204388 IP dns.google.domain > your.machine.52444: 35084 1/0/0 A 203.0.113.22 (40)

Em seguida, o cliente realiza o handshake TCP (SYN, SYN-ACK, ACK) e envia a requisicao HTTP GET / na porta 80:

14:18:36.786501 IP your.machine.36086 > yummyrecipesforme.com.http: Flags [S], seq 2873951608...
14:18:36.786517 IP yummyrecipesforme.com.http > your.machine.36086: Flags [S.], ack 2873951609...
14:18:36.786529 IP your.machine.36086 > yummyrecipesforme.com.http: Flags [.], ack 1...
14:18:36.786589 IP your.machine.36086 > yummyrecipesforme.com.http: Flags [P.], length 73: HTTP: GET / HTTP/1.1

Evidencia: O servidor web entregou um codigo JavaScript malicioso embutido na pagina, forcando o download do arquivo executavel na maquina do visitante.

### Fase 2: Redirecionamento Automatico
Apos a execucao do arquivo, o computador dispara uma nova requisicao DNS para resolver o dominio greatrecipesforme.com, recebendo o IP 192.0.2.17:

14:20:32.192571 IP your.machine.52444 > dns.google.domain: 21899+ A? greatrecipesforme.com. (24)
14:20:32.204388 IP dns.google.domain > your.machine.52444: 21899 1/0/0 A 192.0.2.17 (40)

### Fase 3: Conexao com o Servidor Malicioso
Minutos depois, a maquina inicia comunicacao direta com a infraestrutura do invasor via HTTP:

14:25:29.576493 IP your.machine.56378 > greatrecipesforme.com.http: Flags [S], seq 1020702883...
14:25:29.576590 IP your.machine.56378 > greatrecipesforme.com.http: Flags [P.], length 73: HTTP: GET / HTTP/1.1

---

## 4. Causa Raiz
O atacante obteve acesso administrativo ao servidor web por meio de um ataque de forca bruta (Brute Force), explorando o uso de senhas padrao de fabrica (default passwords) na conta de administrador e a ausencia de bloqueio por tentativas incorretas.

---

## 5. Indicadores de Comprometimento (IOCs)
* Dominio Malicioso: greatrecipesforme.com
* IP do Atacante: 192.0.2.17
* Vetor de Ataque: Injecao de script JavaScript no endpoint raiz HTTP

---

## 6. Plano de Remediacao

### Acoes Imediatas:
* Redefinicao de todas as credenciais administrativas e encerramento de sessoes ativas.
* Restauracao do codigo-fonte limpo do site a partir de backup integro.
* Bloqueio do IP 192.0.2.17 e do dominio malicioso no firewall perimetral.

### Acoes Preventivas (Hardening):
* Implementacao obrigatoria de Autenticacao Multifator (MFA/2FA) para todas as contas administrativas.
* Politica de bloqueio de conta (Account Lockout) apos 3 a 5 tentativas incorretas de login.
* Monitoramento de integridade de arquivos web e implantacao de Web Application Firewall (WAF).
