# Importador de Leads → Bitrix24

Abra `importador-leads.html` no navegador (duplo clique). Não precisa instalar nada; só precisa de internet.

## Integração com o Bitrix24 (uma vez)
1. Bitrix24 → **Aplicativos → Desenvolvedores → Outros → Webhook de entrada**.
2. Permissões: **CRM (crm)** e **Usuários (user)**.
3. Copie a URL (`https://SEUPORTAL.bitrix24.com.br/rest/ID/TOKEN/`) e cole no passo 1 da tela. Trate essa URL como senha.

Documentação usada: [crm.lead.add](https://apidocs.bitrix24.com/api-reference/crm/leads/crm-lead-add.html), `crm.contact.add`, `batch` (até 50 comandos por chamada), `crm.lead.list`, `user.get`, `crm.status.list`.

## Regras aplicadas
- Empresas × contatos cruzados pelo **CNPJ** (pontuação ignorada).
- Contatos = somente **pessoas físicas (CPF)**; telefones da própria empresa (linha de CNPJ) são ignorados.
- **Empresa sem pessoa física com telefone não é cadastrada.**
- Telefones no formato `55` + DDD + número, sem `+`.
- Distribuição igualitária (rodízio) entre os IDs de vendedores informados; contatos recebem o mesmo responsável do lead.
- CNPJs que já existem como lead (campo "CNPJ do Cliente:") não são recadastrados. Os telefones dos **contatos vinculados a esse lead no Bitrix** entram nas Listas A/B (1º telefone de cada contato → A, demais → B) do **responsável atual** do lead; se ele não estiver entre os vendedores informados, recebe listas próprias. Lead existente sem contato com telefone não entra nas listas.
- **Lista A** = 1º telefone de cada sócio; **Lista B** = demais telefones dos sócios. Um arquivo por vendedor, em CSV e TXT:
  `Lista A - Vendedor - dd-mm-aaaa.csv` / `.txt` — mais um relatório da importação.

## Mapeamento dos campos
| CSV empresas | Bitrix24 |
|---|---|
| Fantasia (ou Razão) | Título do lead |
| Razão | Nome da empresa + "Razão Social do Cliente:" |
| Fantasia | "Nome Fantasia do Cliente:" |
| CNPJ | "CNPJ do Cliente:" |
| Regime Tributário | "Optante do Simples Nacional?" (Sim/Não) |
| Data Início Atv. | "Data de Fundação da Empresa do Cliente:" |
| Endereço/Número/Complemento/Bairro/Cidade/UF/CEP | Endereço do lead |
| Porte, faturamento, funcionários, capital, natureza, situação | Comentário |
| 1º sócio | "Nome/CPF/Celular do Sócio Principal:" |

Os códigos dos campos personalizados ficam no topo do script (`const CAMPOS`), caso mudem no portal.

## Dica
Use **Simulação** para conferir números e listas sem gravar, e **Limite de leads** = 1 ou 2 para um teste real antes da importação completa.
