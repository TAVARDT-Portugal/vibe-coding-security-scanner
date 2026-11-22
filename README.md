# Vibe Coding Security Scanner & Auditor de Aplicações Geradas por IA | TAVARDT Portugal

[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Vibe Coding Security](https://img.shields.io/badge/Vibe%20Coding-Security%20Hardened-00d26a.svg)](README.md)
[![Language](https://img.shields.io/badge/Language-Português%20(Portugal)-green.svg)](README.md)
[![Engineering](https://img.shields.io/badge/TAVARDT-Cybersecurity-5b3cf5.svg)](https://ag.tavardt.com/pt/ciberseguranca/)

Scanner de código aberto para auditoria de segurança em aplicações desenvolvidas via vibe coding (Cursor, Lovable, Bolt, v0). Deteção de credenciais expostas no frontend, falhas de RLS e sourcemaps.

## Aplicação Web Interativa

Acesse a ferramenta online e audite em tempo real:

- [https://tavardt-portugal.github.io/vibe-coding-security-scanner/](https://tavardt-portugal.github.io/vibe-coding-security-scanner/)

---

## 1. Visão Geral do Scanner de Segurança Vibe Coding

O ecossistema de desenvolvimento assistido por inteligência artificial (*vibe coding*) possibilita conceber e publicar plataformas funcionais com ferramentas como **Cursor, Lovable.dev, Bolt.new, v0 e Replit**.

Todavia, a inexistência de validação de segurança expõe amiúde segredos de infraestrutura, bases de dados acessíveis sem restrições e credenciais de produção no cliente.

Este repositório disponibiliza um utilitário em Python para inspecionar **a superfície pública de qualquer aplicação web em produção**, identificando falhas críticas de forma determinística.

---

## 2. Vulnerabilidades e Vetores Auditados

| Vetor de Risco | Origem no Vibe Coding | Impacto Técnico |
| :--- | :--- | :--- |
| **Chaves de API no Frontend** | Configuração de credenciais OpenAI (`sk-...`), Stripe ou AWS no `.env` do cliente. | Qualquer visitante descarrega os scripts e acede à cota faturada. |
| **Supabase RLS Desativado** | Criação de tabelas no Supabase sem ativar o *Row Level Security* (RLS). | A `anon_key` pública possibilita extrair ou apagar dados diretamente via REST. |
| **Sourcemaps em Produção** | Publicação da aplicação com ficheiros `.js.map` habilitados. | O código-fonte original completo fica disponível para descarregamento. |
| **Ficheiros Críticos Abertos** | Rotas como `/.env` ou `/.git/HEAD` expostas no servidor web. | Exfiltração de variáveis de ambiente e ficheiros de configuração. |
| **Ausência de Cabeçalhos** | Omissão de diretivas CSP, HSTS e X-Frame-Options. | Exposição a Clickjacking e injeção de scripts maliciosos. |

---

## 3. Guia de Instalação e Execução

### Instalação Rápida

```bash
git clone https://github.com/TAVARDT-Portugal/vibe-coding-security-scanner.git
cd vibe-coding-security-scanner
```

### Executar Auditoria numa Aplicação

```bash
python scanner.py https://a-sua-aplicacao.vercel.app
```

### Opções Disponíveis

```bash
# Exportar relatório estruturado em formato JSON
python scanner.py https://a-sua-aplicacao.vercel.app --json

# Ignorar inspeção de ficheiros sourcemap
python scanner.py https://a-sua-aplicacao.vercel.app --no-sourcemaps
```

---

## 4. Blindagem Profissional e Apoio Técnico

Caso a auditoria tenha revelado credenciais expostas, falhas de RLS no Supabase/Firebase ou necessidade de arquitetura de segurança perimetral:

* **Portal Corporativo:** [TAVARDT Portugal](https://ag.tavardt.com/pt/ciberseguranca/)
* **Contacto Técnico Direto:** [Entrar em Contacto com a Engenharia](https://ag.tavardt.com/pt/contactos/)

---

## Licença

Distribuído sob a licença de código aberto MIT. Consulte o ficheiro [`LICENSE`](LICENSE) para mais detalhes.  
Copyright (c) 2026 TAVARDT Portugal.
