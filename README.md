My Finance App
Single-file personal finance app for the 2026 budget. The app runs from index.html in the browser and stores the household budget, income, expenses, cards, recurring bills, category caps, history, and cloud sync state.

How To Run
Open index.html directly in the browser:

file:///Users/gabrielladesouzaleandro/Desktop/My%20Finance%20App/index.html
After code changes, reload the page so the browser uses the latest JavaScript.

Core Rule
The app is organized around monthly cash flow, but card purchases follow the bill month, not necessarily the purchase month.

Summary:

Past and current months use confirmed spending: paid card bills, paid cash recurring bills, paid consorcios, and cash variable expenses.
Future months use the total Analytics cap as the projected spending amount.
The current real balance only uses money that actually happened: received income, paid recurring/cash items, cash expenses, and card bills marked as paid.
Data And Sync Rules
The app saves data in two places:

Browser localStorage, using the key budget-2026-v3.
Supabase table app_state, using the same app state payload.
Load priority:

On boot, the app compares local data and cloud data using savedAt or the latest activity timestamp.
The newest payload wins.
If the app repairs category data while loading, the repaired payload is saved back locally and to the cloud.
Save protection:

Before saving, the app checks whether the new payload has far fewer data points than the cloud payload.
If the cloud has more than 10 data points and the new payload has less than half of that, save is blocked to avoid accidental reset.
Manual cloud sync:

The "Enviar nuvem" button sends the current local state to Supabase.
Use it after important manual cleanup or after importing data.
Undo:

The app keeps one undo snapshot in local storage.
"Desfazer ultima atividade" restores the previous saved payload and writes it locally and to the cloud.
Naming Rules
Use Recorrente and Recorrentes everywhere.

Do not use the old name Conta fixa in visible app text or activity history.

History messages normalize old text:

"Conta fixa" becomes "Recorrente".
"conta fixa" becomes "recorrente".
"Contas fixas" becomes "Recorrentes".
Income Rules
Income is managed in the Receitas tab.

Income types:

Avulsa: one income entry in the selected month.
Recorrente: creates an income rule and projected entries for the configured months.
Income categories, called Origem in the UI:

Gabriella
Gabriel
Reembolso
Investimento
Income category rules:

Every income entry has a category.
Old income entries are auto-classified from the description when possible.
The income edit modal must allow editing description, origin/category, and value.
If an edited income came from a recurring rule, changing description or origin updates the linked rule.
Received income rules:

Only income marked as received affects the real account balance.
Unreceived income is still part of planned income and projections.
The income source cards show the received amount as the large number.
The smaller line shows "A receber" with the remaining planned amount for that source.
Annual income in Analytics:

The annual income block is titled "Recebimentos anuais".
It has an expandable category list with Gabriella, Gabriel, Reembolso, and Investimento.
The title must not include "(Meta 100% x Recebido)".
Expense Types
The app has four spending surfaces:

Variaveis
Recorrentes
Parcelados
Cartoes
Variable Expenses
Variable expenses are logged in the purchase month.

Rules:

A variable expense has category, description, value, day, payment method, purchase month, and optional bill month.
If payment is cash, bill month equals purchase month.
If payment is card, the item appears in the card bill month.
Variable expenses with cash payment count as confirmed spending immediately.
Variable expenses with card payment count in the real balance only when that card bill is marked as paid.
Recurring Expenses
Recurring expenses are recurring bills, not category budgets.

Rules:

Use "Recorrente" instead of "Conta fixa".
Recurring items may be cash or card.
Cash recurring items affect the real balance when marked as paid.
Card recurring items affect the real balance only when their card bill is marked as paid.
Recurring item categories must use the same subcategory list used by variable expenses.
Recurring category fields are subcategories, not parent categories.
Installments
Installments are purchases split across months.

Rules:

A parcelado item has total value, number of installments, category, day, purchase month, payment method, and bill start month when paid by card.
Card installments enter card bills by bill month.
Cash installments enter the corresponding months directly.
Editing an installment uses the same expense modal pattern as other expense edits.
Cards
Cards are bill containers.

Rules:

Card bills include variable card purchases, card installments, and recurring card items.
A card bill only affects real balance after the card is marked as paid for that month.
Card payment options are generated from the configured default cards plus custom cards.
Adding cards should stay available; it is useful configuration, separate from categories.
Category And Subcategory Rules
The app uses unified expense subcategories.

Parent categories include:

Casa
Alimentacao
Transporte
Educacao
Atividades
Saude
Contabilidade
Internet
Eventos/Viagens
Pessoal
Reembolsavel
Important subcategory rules:

Analytics and expense forms use subcategories.
Parent categories are containers; spending is assigned to subcategories.
Contabilidade is only a parent category; it must not exist as its own subcategory.
The Contabilidade subcategories are DAS, INSS, and Contador.
Variable, recurring, and installment entries should all use the same unified subcategory list.
Custom subcategories created in Settings must appear in variable and installment expense dropdowns.
Custom category IDs must never collide with built-in IDs like mercado.
Custom categories should map to themselves, not fall back to mercado.
Known built-in subcategories include, but are not limited to:

Mercado
Restaurante
Suplementacao
Limpeza
Conforto
Piscina
Farmacia
Consulta
Exames
Manutencao
Gasolina
Cursos
Natacao
Gym Gabriel
Gym Gabriella
Pet
Assinaturas
Eventos
Viagens
Casamento
Viagem EUA
Brasilia
Ribeirao Preto
Foz do Iguacu
Aniversario Eva
Bem-Estar
Roupas Gabriel
Roupas Gabriella
Roupas Eva
Extras
Cartao Emprestado
Category repair rules:

Mercado must remain Mercado in Alimentacao.
Olympia must be its own custom category in Eventos/Viagens.
If old data tries to rename Mercado as Olympia, the app repairs Mercado and keeps only one Olympia.
Duplicate custom category names should be merged.
Reserved built-in IDs must not be used for custom categories.
Analytics Rules
Analytics is the source of spending caps.

Monthly Analytics:

Shows parent categories and subcategories for the selected month.
Each subcategory compares current value against its cap.
Clicking a category opens the list of entries in that category.
Editing an entry from Analytics must open the same edit modal used by the Gastos tab.
Annual Analytics:

Shows annual category totals and caps across all months.
Bars include month tick marks.
Parent categories expand to show subcategories.
Recebimentos anuais expands to show income categories.
Cap rules:

Caps are stored by month and subcategory.
The total monthly Analytics cap is the sum of subcategory caps without double-counting duplicates.
The cap is used for future-month projections.
Current and past month spending in the Summary should use confirmed real spending, not cap.
Current value rules:

Analytics current value for a subcategory includes:
variable entries mapped to that subcategory,
recurring entries mapped to that subcategory,
consorcios where applicable.
Recurring paid items use actual paid value.
Unpaid recurring items use base value in Analytics.
Cash expenses must be counted correctly in Analytics.
Summary Rules
The Resumo tab calculates the year timeline.

Income:

If a month has income entries, the month uses those entries.
If a month has no income entries, projected recurring income entries are used.
Received income is real.
Unreceived income is planned.
Expenses:

Future months use total Analytics cap.
Current and past months use confirmed spending.
Confirmed spending is:
paid cash recurring bills,
paid consorcios,
cash variable spending,
card bills marked as paid.
Real account balance:

Real balance only counts received income.
It subtracts paid recurring cash items, paid consorcios, cash variable expenses, and paid card bills.
Unpaid card bills do not affect real balance until marked paid.
Consorcios And IPVA
IPVA:

IPVA values are configured in Settings.
They appear as recurring entries in the selected month.
Consorcios:

Monthly and weekly consorcios are configured in Settings.
Paid consorcios affect real balance.
Consorcios also appear in Analytics under Reembolsavel/Consorcio.
Consorcio receipts are tracked separately from expense payments.
Activity History Rules
The History tab logs important app actions.

History should record:

added income,
edited income,
income marked received/unreceived,
added/edited/deleted variable expenses,
added/edited/deleted recurring expenses,
category changes,
card and sync operations where relevant.
History text should be readable and should use current terminology.

Import And Export Rules
Full backup:

Export creates a full JSON payload with savedAt.
Importing a full backup replaces current app data.
Monthly JSON:

Monthly exports contain shared settings and one month of data.
Monthly imports merge shared configuration and replace month-scoped data for that month.
Partial expense import:

If a JSON only contains expenses, the app merges new expense entries by ID.
Existing expense IDs are not duplicated.
Development Rules
This app is intentionally a single-file app.

When editing:

Keep changes scoped to index.html unless adding documentation.
Use existing helpers and state shape before introducing new structures.
Avoid changing user data semantics without documenting it here.
Preserve backward compatibility by normalizing old data in applyPayload or through repair helpers.
After JavaScript edits, validate the script with:
node -e "const fs=require('fs');const html=fs.readFileSync('index.html','utf8');const match=html.match(/<script>([\s\S]*)<\/script>/);if(!match) throw new Error('script tag not found');new Function(match[1]);console.log('script ok');"
Current Design Decisions
Keep these decisions unless deliberately changed:

Do not remove custom categories.
Do not remove custom cards.
Category creation should be fixed and reliable, not hidden.
Income origin categories are fixed to Gabriella, Gabriel, Reembolso, and Investimento for now.
Delete buttons should stay inside edit modals where accidental deletion is risky.
Analytics edit buttons should close the Analytics entries modal and open the regular edit modal.
The app should favor real confirmed cash flow in Summary and cap-based planning in future projections.
