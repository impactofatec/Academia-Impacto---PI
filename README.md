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


---

## Kanban (visual / fluxo)

Abaixo há uma representação: uma **kanban textual** (colunas com cartões).

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
