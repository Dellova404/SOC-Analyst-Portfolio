# 🔍 SSH Brute-Force Incident Analysis & Log Triage

## 📌 Visão Geral

Simulação prática de triagem de segurança (SOC N1 / Threat Hunting) em ambiente Kali Linux, analisando logs de autenticação do serviço OpenSSH (`sshd`) para identificar padrões de ataque de força bruta, mapear contas visadas e confirmar comprometimento de credenciais privilegiadas.

---

## 🎯 Objetivos Técnicos

* Inspecionar e filtrar arquivos de log de autenticação (`auth.log`) via terminal Linux.
* Empregar pipes (`|`), `grep`, `awk`, `sort` e `uniq` para parsing e contagem estruturada de eventos.
* Correlacionar timestamps, endereços IP de origem e contas de usuário para confirmar a severidade da intrusão.
* Documentar artefatos e evidências da intrusão para embasamento de regras de contenção.

---

## 🛠️ Comandos Executados & Investigação

### 1. Mensuração de Tentativas Falhas e Alvos ([Ver Print 1](https://www.google.com/search?q=./prints/print_bash_1.png))

Filtro de contagem rápida de falhas de autenticação:

```bash
grep -c "Failed" auth_simulado.log

```

* **Resultado:** 5 tentativas registradas em uma janela de 13 segundos.

Mapeamento dos usuários visados via parsing da coluna 9:

```bash
grep "Failed password" auth_simulado.log | awk '{print $9}' | sort | uniq -c

```

* **Contas atacadas:** `admin` (1x), `support` (1x), `root` (3x).

---

### 2. Identificação do Atacante e Comprometimento ([Ver Print 2](https://www.google.com/search?q=./prints/print_bash_2.png))

Isolamento da coluna de endereços IP (coluna 11) com geração de arquivo de evidência:

```bash
grep "Failed password" auth_simulado.log | awk '{print $11}' | sort | uniq -c > top_atacantes.txt
cat top_atacantes.txt

```

* **Indicador de Comprometimento (IoC):** `192.168.1.105` (5 tentativas de login não autorizado).

Extração de logins autenticados para confirmar invasão:

```bash
grep "Accepted password" auth_simulado.log > sucesso_logins.txt
cat sucesso_logins.txt

```

* **Veredito da extração:** Sessão autenticada obtida para `root` a partir do IP atacante.

---

## 🚨 Conclusão do Incidente & Veredito

* **Severidade:** Crítica (Acesso não autorizado obtido na conta de maior privilégio).
* **Diagnóstico:** O IP `192.168.1.105` executou força bruta direcionada a contas administrativas e, às `22:01:22`, obteve sucesso autenticando-se diretamente como **`root`** (sessão aberta com `uid=0`).
* **Ações Recomendadas (Contenção & Hardening):**
1. Bloqueio imediato do IP `192.168.1.105` nas regras de firewall (`iptables` / perímetro).
2. Desativação do login direto de root no arquivo `/etc/ssh/sshd_config` (`PermitRootLogin no`).
3. Implementação de autenticação exclusiva por chaves criptográficas (SSH Keys) e ferramenta de contenção automática (`fail2ban`).
