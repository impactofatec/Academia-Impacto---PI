# Academia-Impacto---PI

> **Projeto universitário**: sistema para gestão de academia. Este repositório contém a UI, mocks e utilitários para simular operações comuns (alunos, aulas/agenda, check-in, financeiro, relatórios)..

---

## Sumário

* [Visão Geral](#visão-geral)
* [Funcionalidades principais](#funcionalidades-principais)

  * P0 (imediatas)
  * P1 (melhorias)
  * P2 (opcionais/avançadas)
* [Como rodar localmente](#como-rodar-localmente)
* [Estrutura do projeto](#estrutura-do-projeto)
* [Testes e utilitários](#testes-e-utilitários)
* [Patches / ZIPs gerados](#patches--zips-gerados)
* [Simulação: Kanban (visual/fluxo) — versão para Redmi](#simulação-kanban-visualfluxo--versão-para-redmi)
* [Notas de desenvolvimento / segurança](#notas-de-desenvolvimento--segurança)
* [Contribuição](#contribuição)

---

## Visão geral

E uma UI pensada para uso acadêmico / demonstrativo. O foco é prover um projeto facilmente integrável a um backend real, com:

* páginas para `Alunos`, `Aulas / Agenda`, `Check-in`, `Financeiro`, `Relatórios`, `Config`;
* mocks em `/mock/*.json` que permitem executar a UI sem backend;
* utilitários JS e testes mínimos para validar comportamento crítico.

---

## Funcionalidades principais

### P0 — Prioridade imediata (implementadas)

* Listagem de alunos com busca, paginação client-side e ordenação.
* Export CSV para Alunos/Financeiro com mitigação básica contra CSV injection (prefix `'` para valores começando com `=,+,-,@`).
* Check-in rápido na recepção (mock) + lista de últimos check-ins (localStorage) com "Desfazer".
* KPIs básicos na página de Relatórios (consome `/mock/dashboard.json`).
* Robustez em `renderTable()` (coerção de `rows` para array e uso de `textContent` por padrão).

### P1 — Melhorias de UX/fluxo (implementadas)

* Modal de edição de aluno (abrir/editar/salvar — simulado via `apiPost`).
* Criar/editar aulas via modal no calendário.
* Ações em pagamentos: Confirmar / Estornar (mock).
* Comparativo simples entre instrutores (gera tabela aproveitando mock de aulas).

### P2 — Funcionalidades avançadas (implementadas como mock)

* Seleção em massa (bulk actions) para alunos: Ativar, Inativar, Enviar e-mail (mock).
* Drag & drop com placeholder visual no calendário de aulas (reordenação client-side + `apiPost('/classes/drag')` mock).
* Gerador de link de pagamento (mock) com modal e cópia para clipboard.

---

## Como rodar localmente

1. Extraia o ZIP do projeto (ex.: `gym_repo_p0_p1_p2_phase2_applied.zip`).
2. No diretório do projeto, rode um servidor estático simples:

```bash
python -m http.server 8000
# ou
# npx http-server -p 8000
```

3. Acesse no navegador:

* `http://localhost:8000/students/list.html` — alunos
* `http://localhost:8000/classes/schedule.html` — agenda
* `http://localhost:8000/checkin/reception.html` — recepção
* `http://localhost:8000/payments/list.html` — financeiro
* `http://localhost:8000/reports/dashboard.html` — relatórios

---

## Estrutura do projeto (resumida)

```
/assets/
  css/
  js/
    api.js                # mock API helpers
    ui.js                 # helpers: renderTable, toasts, qs
    pages/
      students-list.js
      classes.js
      payments.js
      dashboard.js
      checkin.js
    utils/
      classes-utils.js    # moveItemInWeek
      bulk-utils.js
      payments-utils.js
/mock/                    # arquivos JSON de exemplo
/tests/additional/        # testes simples (ESM .mjs)
```

---

## Testes e utilitários

* Testes simples (executáveis com `node`):

  * `tests/additional/classes-utils.test.mjs` — valida `moveItemInWeek`.
  * `tests/additional/bulk-utils.test.mjs` — valida seleção em massa (usa jsdom).
  * `tests/additional/payments-utils.test.mjs` — testa geração de link (monkeypatch do mock api).

Executar manual (rápido):

```bash
node tests/additional/classes-utils.test.mjs
node tests/additional/bulk-utils.test.mjs
node tests/additional/payments-utils.test.mjs
```

Para CI/Jest, veja `TESTS_README.md` incluído no repositório.

---

## Patches / ZIPs gerados

Vários artefatos foram gerados durante a auditoria e as implementações. Exemplos:

* `gym_repo_p0_p1_p2_phase2_applied.zip` — ZIP com todas as mudanças.
* Pastas de patches: `/patches_generated`, `/patches_p1_generated`, `/patches_p2_generated`, `/patches_p2_phase2_generated`.

---

## Simulação: Kanban (visual / fluxo) — versão para Redmi

Abaixo há duas representações: uma **kanban textual** (colunas com cartões) e uma **simulação visual reduzida** pensada para como seria exibido em um Redmi (tela estreita). Use isto como roteiro/preview para importar tarefas em um verdadeiro board (Trello/Notion/GitHub Projects).

### Kanban (texto)

**Backlog**

* [ ] API real de pagamentos (integração gateway)
* [ ] Autenticação/roles (Admin/Staff/Recepção)
* [ ] Relatórios avançados (MRR, churn)

**Todo**

* [ ] Implementar endpoint `PATCH /students/:id` (persistência)
* [ ] Criar endpoint `POST /checkins/bulk` (eventos)

**In Progress**

* [ ] Calendar: suporte a conflitos de horário (validação)
* [ ] UX: mobile-first para calendário (toque)

**Review**

* [ ] Export CSV server-side (tratamento acentuação & escaping)

**Done**

* [x] Busca + paginação client-side (students)
* [x] Modal editar aluno
* [x] Bulk actions (students) — mock
* [x] Drag & drop com placeholder (classes)
* [x] Payment link mock + modal

---

### Simulação visual — **como apareceria em um Redmi** (representação ASCII, largura reduzida)

```
+-----------------------------+
| GymManager - Kanban (Redmi) |
+-----------------------------+
| > Backlog                  |
|  - API pagamentos         |
|  - Autenticação           |
|---------------------------|
| > Todo                    |
|  - PATCH /students/:id    |
|  - POST /checkins/bulk    |
|---------------------------|
| > In Progress             |
|  - Calendar conflicts     |
|  - Mobile calendar UX     |
|---------------------------|
| > Review                  |
|  - Export CSV server-side |
|---------------------------|
| > Done                    |
|  - Bulk actions (mock)    |
|  - Drag & drop aulas      |
+---------------------------+

// No Redmi, o fluxo costuma ser vertical (colunas empilhadas / swipe)
// Cada cartão pode abrir um modal com detalhes, comentários e checklist.
```

> Dica: para testar a usabilidade mobile, abra o DevTools do navegador, ative o modo de dispositivo móvel, escolha um perfil de tela (ex.: 360×800) e carregue o link do kanban (ou esta README como preview). A experiência de Kanban mobile tipicamente converte colunas em painéis empilhados ou implementa swipe horizontal.

---

## Notas de desenvolvimento & segurança

* **CSV**: client-side tem mitigação básica contra CSV injection; para produção, gere CSV no backend e sanitize com regras por coluna.
* **CSP**: o projeto não usa `eval()`; se o servidor setar uma política CSP restritiva, ajuste-a apenas com cautela. Evite `unsafe-eval` em produção.
* **Autenticação**: nenhuma alteração de auth foi feita; se seu backend usar JWT, não exponha endpoints sensíveis sem verificação.

---

## Como contribuir

1. Faça um fork e crie uma branch `feature/x`.
2. Aplique patches se necessário (há .patchs no diretório `patches_*`).
3. Abra PR descrevendo a feature e testes incluídos.

---

Se quiser, eu posso:

* Gerar um board real no Trello/Notion com as tarefas acima; ou
* Exportar o Kanban em formato JSON/CSV pronto para importar em ferramentas (Trello/GitHub Projects); ou
* Criar um arquivo `kanban.md` com templates de cartões e checklists para cada tarefa.

Diga qual desses você prefere que eu gere em seguida.
