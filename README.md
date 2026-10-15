# SOC-Analyst-Portfolio
Laboratórios práticos de cibersegurança, análise de tráfego, detecção de ameaças e resposta a incidentes.
# 🛡️ SOC Analyst Portfolio | Dellova

Bem-vindo ao meu repositório de documentação prática em Cibersegurança e Operações de Segurança (SOC). Aqui compartilho laboratórios, análises de logs, resposta a incidentes e configurações de hardening.

---

## 📌 Projetos & Casos Práticos

### 1. Resposta a Incidentes & Análise de Pacotes (tcpdump)
* **Cenário:** Investigação de comprometimento web com injeção de malware e redirecionamento de tráfego.
* **Ferramentas:** `tcpdump`, Wireshark, Bash.
* **Conceitos Aplicados:** Inspeção de requisições DNS/HTTP, análise de handshakes TCP e identificação de IOCs.

### 2. Mitigação de Ataque DoS Perimetral (NIST CSF)
* **Cenário:** Análise de saturação de rede corporativa por ICMP Flood e estruturação do plano de resposta.
* **Metodologia:** Alinhamento com as 5 funções do NIST Cybersecurity Framework (Identify, Protect, Detect, Respond, Recover).
* **Hardening:** Regras perimetrais de rate limiting, validação de IP de origem e segmentação de rede (VLANs).

### 3. Defesa Perimetral e Gerenciamento de Logs no Linux
* **Ferramentas:** UFW (Uncomplicated Firewall), `ss`, `journalctl`, `grep`, APT.
* **Prática:** Identificação de portas em escuta, monitoramento de falhas de autenticação (`su`/`sudo`) e criação de regras de descarte perimetral de IPs maliciosos.

---

## 🛠️ Tecnologias & Competências
* **Sistemas Operacionais:** Linux (Kali, Ubuntu, Debian), Windows (Event Viewer, PowerShell).
* **Redes & Análise:** TCP/IP, DNS, HTTP/S, ICMP, tcpdump, Nmap.
* **Governança & Práticas:** Princípio do Menor Privilégio (PoLP), RBAC, NIST CSF, Hardening de Rede.
