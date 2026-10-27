# DevDoctor 🩺

**Assistente de depuração de APIs.** Testa endpoints automaticamente, identifica erros comuns e explica a causa provável e como corrigi-los, com comandos simples e relatórios fáceis de compartilhar.

![license](https://img.shields.io/badge/license-MIT-0b6b63) ![typescript](https://img.shields.io/badge/TypeScript-5-3178c6) ![node](https://img.shields.io/badge/node-%E2%89%A518-339933)

## Por que usar?

Sua API retorna erro 500, demora para responder ou falha na autenticação. Em vez de investigar tudo manualmente, o DevDoctor executa as verificações e mostra **o problema, a provável causa e os próximos passos**.

## Recursos

| Recurso | O que faz |
|---|---|
| **Diagnóstico automático** | Identifica erros HTTP (4xx/5xx), timeouts, conexão recusada, DNS e problemas de certificado HTTPS. |
| **Teste de endpoints** | Verifica status, tempo de resposta, formato (JSON válido) e status esperado. |
| **Segurança básica (passiva)** | Alerta sobre CORS mal configurado, stack traces e possíveis credenciais na resposta, cabeçalhos que revelam tecnologia, ausência de HTTPS. **Não realiza ataques**: só analisa a resposta de requisições normais. |
| **Relatório compreensível** | Terminal colorido, JSON ou HTML autônomo para compartilhar. |
| **Execução local** | Roda no terminal, sem conta, sem servidor e sem plataforma paga. |
| **Dashboard** | `index.html` abre qualquer relatório JSON do DevDoctor no navegador. |

## Instalação

Requer Node.js 18 ou superior.

```bash
git clone https://github.com/seu-usuario/devdoctor.git
cd devdoctor
npm install
npm run build
npm link        # opcional: habilita o comando global "devdoctor"
```

Sem `npm link`, use `node dist/cli.js` no lugar de `devdoctor`.

## Uso rápido

```bash
# Diagnóstico simples
devdoctor check https://api.exemplo.com/users

# Com autenticação e JSON obrigatório
devdoctor check https://api.exemplo.com/users \
  -H "Authorization: Bearer $TOKEN" --expect-json

# POST com corpo e status esperado
devdoctor check https://api.exemplo.com/users -m POST \
  -H "Content-Type: application/json" -d '{"name":"Ada"}' --expect-status 201

# Testar CORS para o seu front-end (inclui preflight OPTIONS)
devdoctor check https://api.exemplo.com/users --origin http://localhost:5173

# Gerar relatório HTML para compartilhar
devdoctor check https://api.exemplo.com/users --format html -o relatorio.html
```

### Vários endpoints de uma vez

Copie `devdoctor.config.example.json` para `devdoctor.config.json`, ajuste e execute:

```bash
devdoctor run
devdoctor run --format html
```

Valores como `${API_TOKEN}` são lidos das variáveis de ambiente, então segredos não ficam no arquivo.

## Opções

| Opção | Descrição |
|---|---|
| `-m, --method` | Método HTTP (padrão `GET`) |
| `-H, --header "K: V"` | Cabeçalho; repita para vários |
| `-d, --data` | Corpo da requisição |
| `--timeout <ms>` | Tempo limite (padrão `10000`) |
| `--slow <ms>` | Limite para considerar a resposta lenta (padrão `1000`) |
| `--origin <url>` | Testa CORS para essa origem |
| `--expect-status <n>` | Status esperado |
| `--expect-json` | Exige resposta JSON |
| `--format text\|json\|html` | Formato do relatório (padrão `text`) |
| `-o, --out <arquivo>` | Salva em arquivo |
| `--no-color` | Desativa cores |

**Códigos de saída:** `0` sem erros · `1` com erros (ideal para CI) · `2` uso incorreto.

## Exemplo de saída

```text
DevDoctor v0.1.0
GET https://api.exemplo.com/users  →  500 Internal Server Error · 1840 ms · 412 B

✖ ERRO  Erro interno do servidor (500)
   Causa provável: Exceção não tratada no servidor: bug, banco indisponível, variável de ambiente ausente ou dado inesperado.
   Como corrigir:
     1. Veja os logs do servidor no horário da requisição.
     2. Reproduza com o mesmo payload em ambiente local.
▲ AVISO  Resposta lenta: 1840 ms
▲ AVISO  Possível stack trace ou erro interno exposto
✔ OK  JSON válido

Resumo: 1 erro(s) · 2 aviso(s) · 0 info · 1 ok
```

## Dashboard

Abra `index.html` no navegador (ou publique no GitHub Pages). Gere um relatório com `--format json -o relatorio.json` e arraste o arquivo para a área do dashboard. Nada é enviado a servidores: tudo roda no seu navegador.

## Estrutura

```
src/
  cli.ts        # comandos e opções
  checks.ts     # requisição, análises (status, formato, segurança, CORS)
  diagnose.ts   # base de conhecimento: causas e correções
  report.ts     # saída em texto, JSON e HTML
  types.ts
index.html      # página do projeto + dashboard
```

## Uso responsável

O DevDoctor faz requisições comuns (e, com `--origin`, um OPTIONS de preflight) e analisa as respostas. Não faz varredura de portas, força bruta, injeção ou qualquer ataque. Use apenas em APIs suas ou com autorização.

## Roadmap

- [ ] Teste de carga leve (p50/p95)
- [ ] Validação de resposta contra OpenAPI/JSON Schema
- [ ] Importar coleções do Postman/Insomnia
- [ ] Modo `--watch`
- [ ] Testes automatizados e CI

## Contribuindo

Contribuições são bem-vindas. Para adicionar um diagnóstico, edite `src/diagnose.ts` (tabela de status) ou `src/checks.ts` (novas verificações), rode `npm run check` e abra um pull request.

## Licença

[MIT](LICENSE)
