# Scanner de Segurança e Auditoria de Código Gerado por IA

[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-5b3cf5.svg)](https://tavardt-portugal.github.io/vibe-coding-security-scanner/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![Software Supply Chain](https://img.shields.io/badge/Security-Supply%20Chain-red.svg)](https://ag.tavardt.com/pt/)
[![SLSA Level](https://img.shields.io/badge/SLSA-Level%203%20Ready-blue.svg)](https://slsa.dev/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Especificação técnica de engenharia defensiva, mitigação de fuga de credenciais e utilitário determinístico de auditoria em linha de comandos para aplicações desenvolvidas com recurso a ferramentas de inteligência artificial (Vibe Coding).

***

## Enquadramento Executivo: A Realidade da Segurança no Vibe Coding

A rápida adoção do desenvolvimento de software assistido por inteligência artificial (com ferramentas como Cursor, Lovable, v0, Bolt, Windsurf, Replit, Claude Code e GitHub Copilot) permitiu a equipas de desenvolvimento e fundadores a criação célere de soluções completas. Contudo, os geradores automáticos de código focam essencialmente a validação funcional primária, descuidando o isolamento perimetral, as políticas de privilégio mínimo e o saneamento de artefactos de compilação.

Quando os engenheiros solicitam modelos de inteligência artificial para entrega rápida de funcionalidades, as soluções herdam frequentemente vulnerabilidades estruturais:
* Configurações de desenvolvimento são transpostas para ambientes produtivos sem salvaguardas de compilação.
* Chaves de administração de bases de dados e tokens de IA são expostos em ficheiros estáticos descarregados pelo navegador.
* Tabelas de dados relacionais são instanciadas sem regras imperativas de controlo de acesso (Row Level Security).
* Cabeçalhos essenciais de defesa perimetral são omitidos das respostas web.

Este repositório estabelece uma norma de arquitetura defensiva corporativa, fornecendo modelos de configuração testados para os principais ambientes de desenvolvimento e um utilitário determinístico em Python (`audit_vibe_security.py`) para identificar e mitigar estas vulnerabilidades antes da colocação em produção.

***

## Superfície de Risco em Aplicações Criadas por IA

### 1. Fuga de Sourcemaps em Produção (.js.map)
Os assistentes de IA configuram tipicamente os sistemas de compilação frontend (Vite, Webpack, Turbopack, Rollup) com parâmetros padrão que geram ficheiros de mapeamento a par dos ficheiros JavaScript finais. Quando colocados em redes públicas de distribuição de conteúdos (CDNs), os ficheiros `.js.map` permitem a reversão integral da lógica de negócio:
* Reconstituição precisa do código fonte original em TypeScript e JavaScript.
* Mapeamento integral de estruturas de dados internas, esquemas e interfaces de comunicação.
* Identificação de rotas administrativas confidenciais e anotações técnicas deixadas no desenvolvimento.

### 2. Chaves de Fornecedores de IA e Tokens BaaS Compilados no Cliente
Devido à ausência de contexto sobre a separação física entre o servidor e o navegador do utilizador, os geradores de IA colocam frequentemente chaves de acesso no código cliente. Quando variáveis com prefixos como `NEXT_PUBLIC_` ou `VITE_` são utilizadas, os seus valores literais são embutidos diretamente nos ficheiros públicos:
* Chaves de API da OpenAI, Anthropic e Google Gemini sujeitas a consumo não autorizado.
* Chaves mestras `service_role` e tokens JWT do Supabase que sobrepõem todas as permissões de tabela.
* Chaves de acesso a infraestruturas na nuvem AWS, credenciais do Google Cloud e webhooks.

### 3. Falta de Row Level Security (RLS) em Bases de Dados Relacionais
A abordagem de Vibe Coding promove frequentemente o acesso direto do cliente ao banco de dados através de bibliotecas modernas. Todavia, os modelos de IA instanciam tabelas PostgreSQL sem a instrução `ALTER TABLE ... ENABLE ROW LEVEL SECURITY;`. Sem políticas de RLS ativas:
* Qualquer utilizador que consulte a rota do projeto no navegador pode executar pesquisas REST e GraphQL indiscriminadas.
* Bases de dados com registos de clientes, dados pessoais sujeitos ao RGPD e históricos comerciais ficam acessíveis a pedidos anónimos.

### 4. Omissão de Cabeçalhos HTTP de Proteção Web
Servidores configurados com base em instruções automáticas de IA raramente incluem cabeçalhos de segurança web essenciais:
* `Content-Security-Policy` (CSP): Evita a injeção e execução arbitrária de código malicioso no cliente.
* `Strict-Transport-Security` (HSTS): Garante ligações exclusivamente cifradas via HTTPS.
* `X-Frame-Options` e `X-Content-Type-Options`: Protegem contra enquadramentos hostis (Clickjacking) e confusão de tipos MIME.

### 5. Exposição de Ficheiros de Ambiente (.env)
Diretórios de raiz com permissões inadequadas permitem o acesso público a ficheiros como `/.env`, `/.env.local` e `/.env.production`, possibilitando a recolha direta de segredos corporativos através de um simples pedido HTTP.

***

## Utilitário Determinístico de Auditoria CLI

As aplicações em navegadores estão submetidas a controlos estritos de isolamento e regras de CORS que impedem a validação de cabeçalhos e a análise direta de nós externos. Para disponibilizar diagnósticos técnicos fiáveis e sem envio de dados para servidores terceiros, a TAVARDT fornece um utilitário em Python para execução local no terminal ou integração em sistemas de CI/CD.

### Fases da Inspecção Determinística

O utilitário (`audit_vibe_security.py`) concretiza cinco fases de análise:
1. **Auditoria de Perímetro Raiz:** Efetua ligação TLS, valida códigos de resposta e afere a presença de cabeçalhos defensivos.
2. **Deteção de Scripts no DOM:** Examina o código HTML para listar todos os blocos de código carregados pela aplicação.
3. **Verificação de Sourcemaps:** Investiga a existência de ficheiros `.js.map` associados a cada bloco de código identificado.
4. **Inspeção de Segredos em Ficheiros Compilados:** Aplica filtros com expressões regulares rigorosas para localizar chaves ativas de inteligência artificial e credenciais de nuvem.
5. **Rastreio de Ficheiros de Ambiente:** Confirma se ficheiros estruturais de ambiente (`/.env`, `/.env.local`, `/.env.production`) se encontram devidamente protegidos.

### Modo de Utilização

Transfira e execute o script diretamente no terminal contra o endereço visado:

```bash
# 1. Transferir o utilitario oficial
curl -sO https://raw.githubusercontent.com/TAVARDT-Portugal/vibe-coding-security-scanner/main/audit_vibe_security.py

# 2. Executar inspecao deterministica
python audit_vibe_security.py https://sua-aplicacao.com
```

***

## Modelos de Mitigação Defensiva em Ambientes de Produção

O repositório inclui modelos testados para reforçar a segurança do ecossistema tecnológico:

### Configuração Vite (`vite.config.ts`)
Desativa os sourcemaps em compilações finais e normaliza a geração de pacotes:

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  build: {
    sourcemap: false,
    chunkSizeWarningLimit: 1000,
    rollupOptions: {
      output: {
        manualChunks: undefined
      }
    }
  }
});
```

### Configuração Next.js (`next.config.js`)
Inibe mapas de código no cliente e adiciona cabeçalhos defensivos corporativos:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  productionBrowserSourceMaps: false,
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          { key: 'X-Frame-Options', value: 'DENY' },
          { key: 'X-Content-Type-Options', value: 'nosniff' },
          { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
          { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains; preload' }
        ]
      }
    ];
  }
};

module.exports = nextConfig;
```

### Ativação de Row Level Security no Supabase (`supabase-rls.sql`)
Garante o isolamento dos dados de cada utilizador e revoga acessos anónimos:

```sql
/* 1. Ativacao obrigatoria de Row Level Security nas tabelas publicas */
ALTER TABLE public.profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.organizations ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.transactions ENABLE ROW LEVEL SECURITY;

/* 2. Revogacao de permissoes de execucao publica da role anon */
REVOKE ALL ON ALL TABLES IN SCHEMA public FROM anon;

/* 3. Definicao de regra restrita de consulta associada ao utilizador autenticado */
CREATE POLICY "Utilizadores consultam apenas os seus proprios registos" 
ON public.profiles FOR SELECT 
TO authenticated 
USING (auth.uid() = id);
```

### Regra de Proteção Perimetral no Cloudflare WAF
Regra perimetral com rejeição imediata de tentativas de recolha de ficheiros confidenciais:

```text
(http.request.uri.path contains ".js.map") or 
(http.request.uri.path contains ".env") or 
(http.request.uri.path contains ".git")
Acao: Block (Rejeitar ligacao imediatamente)
```

***

## Questões Frequentes sobre Arquitetura (FAQ Técnico)

### Quais os riscos operacionais decorrentes da geração automática de código por IA?
Os assistentes de IA operam através de previsão estatística de padrões de código e não por análise estruturada de risco. Esta característica conduz frequentemente à inserção de credenciais confidenciais no cliente, ao esquecimento da ativação de Row Level Security e à permanência de configurações de depuração em produção.

### Porque não devem os ficheiros sourcemap (.js.map) ser colocados em produção?
Os ficheiros sourcemap contêm a correspondência matemática direta entre o código compilado e o código fonte original. A sua presença em ambientes públicos permite a terceiros inspecionar a arquitetura da aplicação, analisar contratos de dados e identificar fragilidades estruturais sem dificuldade.

### Como devem as aplicações frontend comunicar com APIs de inteligência artificial de forma segura?
O cliente web nunca deve invocar diretamente fornecedores de IA com recurso a credenciais de administração. Todos os pedidos devem transitar por serviços intermédios de backend ou funções de borda (edge functions) que validem a autenticação da sessão, apliquem controlos de frequência e mantenham as credenciais seguras no servidor.

### O utilitário determinístico em Python interfere com o funcionamento do serviço inspecionado?
Não. O programa executa apenas diagnósticos de leitura passivos. Emite pedidos HTTP GET e HEAD normais para verificar os cabeçalhos e a existência de ficheiros públicos, sem alterar estados nem degradar o desempenho da infraestrutura inspecionada.

***

## Enquadramento de Conformidade e Normas Corporativas

Arquitetura defensiva em conformidade com o Regulamento Geral sobre a Proteção de Dados (RGPD, Regulamento UE 2016/679), com as orientações técnicas da CNPD e com as diretrizes do Centro Nacional de Cibersegurança (CNCS).

***

## Centro de Ferramentas e Documentação Oficial

* **Aplicação Web:** [https://tavardt-portugal.github.io/vibe-coding-security-scanner/](https://tavardt-portugal.github.io/vibe-coding-security-scanner/)
* **Portal Institucional:** [https://ag.tavardt.com/pt/](https://ag.tavardt.com/pt/)
* **Centro de Ferramentas:** [https://tools.tavardt.com/pt/](https://tools.tavardt.com/pt/)

***

## Ligações Úteis

* **Portal Corporativo:** https://ag.tavardt.com/pt/
* **Suíte de Diagnósticos:** https://tools.tavardt.com/pt/
* **Contacto Técnico:** https://ag.tavardt.com/pt/contacto/

***

© 2026 TAVARDT. Todos os direitos reservados.
