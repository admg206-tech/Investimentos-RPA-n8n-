# **🤖 Assistente de Investimentos RPA com IA (n8n)**

## **📝 Descrição do Projeto**

Este projeto é a entrega do laboratório **"Criação de Assistentes de Investimento com IA"** da Digital Innovation One (DIO). O objetivo é construir uma automação (RPA) utilizando o **n8n** para coletar dados financeiros, processar análises utilizando Agentes de Inteligência Artificial (LLMs) e disparar um briefing diário de investimentos diretamente no Gmail do usuário.

A arquitetura foi desenhada focando em **segurança de webhooks, resiliência de requisições HTTP e tratamento de dados**.

## **⚙️ Arquitetura do Fluxo (n8n Pipeline)**

O fluxo de automação segue exatamente a jornada estruturada no MVP:

1. **Trigger (Webhook):** Ponto de entrada da automação. Recebe a solicitação para iniciar a análise.  
2. **Coleta de Dados (HTTP Request):** Consulta APIs de mercado financeiro para buscar cotações e indicadores.  
3. **Processamento (Code \+ Merge):**  
   * Transforma as strings JSON recebidas das APIs em objetos nativos manipuláveis.  
   * O nó de Merge unifica diferentes fontes de dados (ex: Ações e Fundos Imobiliários) em um único payload.  
4. **Análise IA (Agentes / LLM):** Envia o payload unificado para uma IA (ex: OpenAI, Gemini, Claude) com um *prompt* focado em análise de investimentos. A IA atua como o assistente, gerando um resumo e recomendações.  
5. **Notificação (Gmail):** O resultado da LLM é formatado em HTML e enviado por e-mail, entregando o briefing final ao investidor.

## **🛡️ Boas Práticas e Resiliência Implementadas**

Para garantir que a automação seja robusta e à prova de falhas (evitando interrupções por erros 400, 401 ou 500), as seguintes configurações foram aplicadas:

* **Segurança do Webhook:** O nó de Webhook utiliza autenticação por cabeçalho (`Authorization: Bearer <token>`) e *Paths* ofuscados. Opcionalmente, aplica-se *Whitelist* de IP para garantir que apenas fontes autorizadas disparem o fluxo.  
* **Tolerância a Falhas (Retry on Fail):** Os nós de `HTTP Request` estão configurados com limite de **5 tentativas (Retries)** para contornar instabilidades temporárias nas APIs financeiras.  
* **Tratamento de Erros (Continue on Fail):** Caso um ativo específico retorne erro (ex: `404 Not Found` ou `400 Bad Request`), o fluxo está configurado para continuar a execução e gerar o briefing com os demais ativos, enviando um log do erro de forma graciosa.  
* **Data Parsing no Code Node:** Validação estrita para garantir que dados recebidos em formato texto sejam convertidos corretamente (`JSON.parse()`) antes de serem enviados para a IA.

## **🚀 Como Executar o Projeto**

### **Pré-requisitos**

* Instância do **n8n** rodando (Local via Docker, n8n Cloud ou VPS).  
* Conta de desenvolvedor nas APIs financeiras (ex: Alpha Vantage, Yahoo Finance, ou HG Brasil).  
* Chave de API de um provedor LLM (OpenAI, Anthropic ou Google Gemini).  
* Credencial do Google ativada no n8n para o nó do **Gmail**.