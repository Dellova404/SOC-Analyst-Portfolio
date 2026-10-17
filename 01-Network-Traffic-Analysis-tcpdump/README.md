# 🔍 Investigação de Incidente: Análise de Tráfego de Rede com tcpdump

## 📌 Visão Geral do Incidente
Múltiplos clientes entraram em contato com o suporte da empresa de comércio eletrônico `yummyrecipesforme.com` informando que, ao acessar a página principal, o navegador solicitava o download imediato de uma suposta atualização de arquivo executável para liberar acesso a receitas. Os usuários relataram que, após a execução do arquivo, seus computadores apresentavam extrema lentidão e a barra de endereços do navegador redirecionava automaticamente para um domínio diferente (`greatrecipesforme.com`).

Como analista de segurança, fui encarregado de reproduzir o incidente em ambiente isolado (sandbox), inspecionar a camada de tráfego de rede e documentar o vetor de ataque e as medidas de remediação.

---

## 🛠️ Metodologia & Ferramentas
* **Ambiente de Análise:** Sandbox isolada para execução controlada de artefatos.
* **Captura de Pacotes:** `tcpdump` em linha de comando.
* **Camadas Analisadas:** Camada de Rede (IPv4), Camada de Transporte (TCP) e Camada de Aplicação (DNS, HTTP).

---

## 🔬 Análise Técnica dos Logs (tcpdump)

A inspeção do tráfego capturado revelou três fases distintas de comprometimento da sessão do usuário:

### Fase 1: Resolução de Nomes e Conexão ao Site Legítimo
A estação de trabalho inicia uma consulta DNS padrão ao servidor `dns.google` para resolver o domínio `yummyrecipesforme.com`, obtendo o IP `203.0.113.22`:

```text
14:18:32.192571 IP your.machine.52444 > dns.google.domain: 35084+ A? yummyrecipesforme.com. (24)
14:18:32.204388 IP dns.google.domain > your.machine.52444: 35084 1/0/0 A 203.0.113.22 (40)
