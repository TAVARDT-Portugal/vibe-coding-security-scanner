# TAVARDT Vibe Coding Security & AI Architecture Auditor (Portugal)

Guia corporativo de arquitetura defensiva, mitigação de vazamento de credenciais e ferramental CLI determinístico para inspeção de segurança em aplicações desenvolvidas com ferramentas de IA (Vibe Coding).

## Superfície de Risco em Aplicações Criadas por IA

O desenvolvimento acelerado através de IA (Cursor, Lovable, v0, Bolt) introduz três vulnerabilidades estruturais recorrentes:

1. **Vazamento de Sourcemaps (.js.map):** Compilações de produção que mantêm mapas de código ativos viabilizam a descompactação integral da lógica de negócio e modelos de dados originais em TypeScript.
2. **Exposição de Chaves de IA em Bundles Client-Side:** Chaves da OpenAI, Anthropic ou tokens `service_role` do Supabase prefixadas com `NEXT_PUBLIC_` ou `VITE_` são baixadas por qualquer visitante no navegador.
3. **Ausência de Row Level Security (RLS):** Bancos relacionais acessados via clientes REST/GraphQL sem políticas de RLS ativas permitem a extração não autorizada de registros de múltiplos usuários.

***

## Ferramenta Oficial de Auditoria CLI

Para contornar restrições de sandbox de navegadores e políticas de CORS, a TAVARDT disponibiliza um utilitário em Python para auditoria direta via terminal ou pipeline de CI/CD:

```bash
# 1. Obter o script oficial
curl -sO https://raw.githubusercontent.com/TAVARDT-Portugal/vibe-coding-security-scanner/main/audit_vibe_security.py

# 2. Executar auditoria direta contra a aplicacao
python audit_vibe_security.py https://sua-aplicacao.com
```

### Capacidades de Inspeção do Utilitário CLI

* **Auditoria de Sourcemaps:** Varredura automática em rotas convencionais de build para detectar arquivos `.js.map` expostos publicamente.
* **Inspeção de Cabeçalhos HTTP:** Validação rigorosa de políticas `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options` e `X-Frame-Options`.
* **Detecção de Segredos Compilados:** Expressões regulares avançadas para identificar tokens de provedores de IA e credenciais administrativas em bundles estáticos.

***

## Blueprints de Mitigação Defensiva

O repositório fornece configurações homologadas para proteger o ecossistema da aplicação:

* **Vite (`vite.config.ts`):** Desativação estrita de sourcemaps de produção e sanitização de nomes de arquivos.
* **Next.js (`next.config.js`):** Desativação de `productionBrowserSourceMaps` e imposição de cabeçalhos de segurança perimetral.
* **Supabase SQL (`supabase-rls.sql`):** Ativação mandatória de `ROW LEVEL SECURITY` em todas as tabelas e revogação de acessos indiscriminados da role `anon`.
* **Cloudflare WAF:** Regras perimetrais de bloqueio imediato para requisições direcionadas a `.js.map`, `.env` e `.git`.

***

## Repositório & Documentação Oficial

* **Página Oficial:** [https://tavardt-portugal.github.io/vibe-coding-security-scanner/](https://tavardt-portugal.github.io/vibe-coding-security-scanner/)
* **Portal TAVARDT:** [https://ag.tavardt.com/pt/](https://ag.tavardt.com/pt/)
* **Ferramentas de Cibersegurança:** [https://tools.tavardt.com/pt/](https://tools.tavardt.com/pt/)

&copy; 2026 TAVARDT. Todos os direitos reservados.
