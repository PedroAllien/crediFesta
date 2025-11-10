
# Documento de Especificação de Requisitos (DER) — CrediFesta


## 1. Visão Geral
O **CrediFesta** é um aplicativo para gestão de créditos em eventos escolares, permitindo que **pais** recarreguem saldo para **alunos**, e que **atendentes de barraca** realizem vendas de forma rápida e rastreável. **Organizadores/Administradores** gerenciam eventos, produtos, equipes e relatórios.

---

## 2. Escopo (alto nível)
- Recarregar créditos (PIX/cartão), controle de saldo, limites e notificações.
- PDV (Ponto de Venda) simples para venda por barraca com identificação do aluno (QR/código).
- Administração de eventos, catálogos, usuários e relatórios com fechamento financeiro.

---

## 3. Atores
- **Pais/Responsáveis**
- **Alunos**
- **Atendentes de Barraca**
- **Organizadores/Administradores**

---

## 4. Requisitos Funcionais (RF) com prioridade MoSCoW
Legenda MoSCoW: MoSCoW é um método de priorização para gestão de projetos, onde as tarefas são classificadas em **M**ust have (tem que ter), **S**hould have (deveria ter), **C**ould have (poderia ter) e **W**on't have (não vai ter).

### Pais / Responsáveis
- **RF-PAI-01 (M)** Cadastrar conta de responsável (e-mail/senha); login, logout e redefinir senha.
- **RF-PAI-02 (M)** Vincular um ou mais perfis de alunos à conta do responsável.
- **RF-PAI-03 (M)** Adicionar créditos via **PIX** e **cartão** (confirmação e conciliação).
- **RF-PAI-04 (M)** Consultar saldo do(s) aluno(s) em tempo quase real.
- **RF-PAI-05 (S)** Consultar histórico de compras por período.
- **RF-PAI-06 (S)** Receber **notificação** de compra efetuada.
- **RF-PAI-07 (S)** Receber **alerta de saldo baixo** com limiar configurável.
- **RF-PAI-08 (C)** Visualizar cardápio/preços por evento/barraca.
- **RF-PAI-09 (S)** Definir **limite diário** de gastos por aluno.
- **RF-PAI-10 (M)** Gerenciar perfil (dados pessoais e preferências).

### Alunos
- **RF-ALU-01 (M)** Apresentar **código digital** (QR/código numérico) para compras.
- **RF-ALU-02 (M)** Realizar **compra rápida** (seleção → confirmação → débito).
- **RF-ALU-03 (S)** Visualizar saldo no app do aluno.

### Atendentes de Barraca
- **RF-ATE-01 (M)** **Escanear** código do aluno e recuperar saldo/identidade.
- **RF-ATE-02 (M)** Selecionar itens e visualizar **total** antes de confirmar.
- **RF-ATE-03 (M)** Verificar **saldo** e **bloquear** compra insuficiente.
- **RF-ATE-04 (M)** **Debitar** créditos imediatamente na confirmação.
- **RF-ATE-05 (S)** Cancelar item/venda e **estornar** crédito conforme regra.
- **RF-ATE-06 (C)** Consultar **histórico de vendas** da barraca (filtro por período).

### Organizadores / Administradores
- **RF-ADM-01 (M)** Cadastrar **evento** (datas/horários; rascunho/publicado).
- **RF-ADM-02 (M)** Gerenciar **produtos e preços** por barraca/evento.
- **RF-ADM-03 (S)** Gerenciar **barracas e equipes**; atribuir atendentes.
- **RF-ADM-04 (S)** Gerenciar **contas de usuários** (pais, alunos, atendentes).
- **RF-ADM-05 (S)** Gerar **relatórios** por barraca/produto/período; exportar CSV/PDF.
- **RF-ADM-06 (S)** Realizar **fechamento financeiro** do evento (conciliação).
- **RF-ADM-07 (C)** Configurar **métodos de pagamento** habilitados (PIX/cartão).
- **RF-ADM-08 (C)** Gerenciar **regras de repasse** e **suporte/ajuda**.

---

## 5. Regras de Negócio (RN)
- **RN-01** Proibir finalizar compra com **saldo insuficiente**.
- **RN-02** **Alerta de saldo baixo** quando saldo ≤ limiar definido pelo responsável (valor padrão ajustável).
- **RN-03** **Limite diário**: não permitir que compras em um mesmo dia superem o limite do responsável.
- **RN-04** **Identificador único do aluno** por evento; passível de revogação.
- **RN-05** **Estorno**: recreditar imediatamente; **cancelamento parcial** ajusta itens afetados; janela configurável por evento.
- **RN-06** **Fechamento** consolida recargas, vendas, cancelamentos, taxas do gateway, gerando relatório final.
- **RN-07** **Permissões**: organizador/administrador gerenciam eventos e repasses; atendente enxerga apenas sua barraca.

---

## 6. Requisitos Não Funcionais (RNF)
### Segurança & Conformidade
- **RNF-SEG-01** Políticas de senha e **MFA** Autenticação Multifator (Multi-Factor Authentication) opcional (pais/organizadores).
- **RNF-SEG-02** Criptografia **em repouso** AES-256 e **em trânsito** TLS 1.2+.
- **RNF-SEG-03** **LGPD**: consentimento, finalidade, retenção mínima, portabilidade, anonimização em relatórios, canal do titular.
- **RNF-SEG-04** RBAC(Role-Based Access Control)/ABAC(Attribute-Based Access Control) com segregação de papéis.

### Desempenho & Escala
- **RNF-DEP-01** PDV: leitura/validação/débito em **≤ 2 s (p95)** durante picos.
- **RNF-DEP-02** Throughput mínimo alvo por barraca (definir após piloto); gravação idempotente.
- **RNF-DEP-03** Escala horizontal para múltiplas barracas e milhares de usuários no mesmo evento.

### Confiabilidade & Operação
- **RNF-CON-01** **Disponibilidade 99,5%** em janelas de evento; **modo offline** no PDV com sincronização posterior.
- **RNF-CON-02** Observabilidade: logs estruturados, métricas de venda/erros, traços distribuídos e alarmes.
- **RNF-CON-03** Backup diário cifrado; **RPO(Recovery Point Objective) ≤ 24 h / RTO(Recovery Time Objective) ≤ 4 h**; testes de restauração semestrais.

### Usabilidade & Acessibilidade
- **RNF-USA-01** Fluxo de compra em **≤ 3 passos**; recarga em **≤ 2 passos**.
- **RNF-USA-02** **WCAG 2.1 AA**(padrão internacional de acessibilidade); contraste, navegação por teclado, leitores de tela.

### Integrações & Interoperabilidade
- **RNF-INT-01** Pagamentos: **PIX imediato** e **cartão** (PCI via provedor, antifraude).
- **RNF-INT-02** Notificações: push (Firebase/APNS) e e-mail transacional.
- **RNF-INT-03** QR/código: usar **nonce/assinatura** para evitar reuso.

### Manutenibilidade & Qualidade
- **RNF-MAN-01** API REST versionada; documentação **OpenAPI**; testes unitários, integração e contrato.
- **RNF-MAN-02** CI/CD e Infra como Código; feature flags para regras por evento.
- **RNF-MAN-03** Trilhas de auditoria imutáveis para eventos críticos.

---

## 7. Rastreabilidade (HU → RF)
| HU | Descrição (resumo) | RF relacionados |
|---|---|---|
| HU1 | Cadastro de pai | RF-PAI-01 |
| HU2 | Vincular filho | RF-PAI-02 |
| HU3 | Adicionar créditos (PIX/cartão) | RF-PAI-03; RF-ADM-07 |
| HU4 | Visualizar saldo | RF-PAI-04; RF-ALU-3 |
| HU5 | Histórico de compras | RF-PAI-05 |
| HU6 | Notificação de compra | RF-PAI-06 |
| HU7 | Alerta de saldo baixo | RF-PAI-07 |
| HU8 | Cardápio e preços | RF-PAI-08; RF-ADM-02 |
| HU9 | Limite diário | RF-PAI-09 |
| HU10 | Código digital (QR/código) | RF-ALU-01 |
| HU11 | Compra rápida | RF-ALU-02; RF-ATE-02 |
| HU12 | Escanear código | RF-ATE-01 |
| HU13 | Selecionar produtos / total | RF-ATE-02 |
| HU14 | Debitar créditos | RF-ATE-04 |
| HU15 | Histórico de vendas da barraca | RF-ATE-06 |
| HU16 | Verificar saldo | RF-ATE-03 |
| HU17 | Cadastrar evento | RF-ADM-01 |
| HU18 | Gerenciar produtos/preços | RF-ADM-02 |
| HU19 | Relatórios | RF-ADM-05 |
| HU20 | Gerenciar contas | RF-ADM-04 |
| HU21 | Fechamento financeiro | RF-ADM-06 |
| HU22 | Métodos de pagamento | RF-ADM-07 |

---

## 8. Protótipos de API (esboço REST)
**Autenticação & Perfis**
- `POST /v1/auth/signup` — cria conta (pais/atendentes/organizadores).
- `POST /v1/auth/login` — autentica e retorna token.
- `POST /v1/auth/forgot` — inicia redefinição de senha.
- `GET /v1/me` / `PATCH /v1/me` — perfil do usuário.

**Alunos & Vínculos**
- `POST /v1/responsaveis/{{id}}/alunos` — vincular aluno.
- `GET /v1/responsaveis/{{id}}/alunos` — listar alunos do responsável.

**Recargas & Saldo**
- `POST /v1/alunos/{{id}}/recargas` — cria ordem de recarga (PIX/cartão).
- `GET /v1/alunos/{{id}}/saldo` — saldo atual.
- `GET /v1/alunos/{{id}}/compras?from=&to=` — histórico de compras.

**PDV / Vendas**
- `POST /v1/pdv/vendas` — cria venda (itens, aluno_codigo).
- `POST /v1/pdv/vendas/{{id}}/estorno` — estorna venda/itens.
- `GET /v1/pdv/alunos/{{codigo}}/verificar` — retorna identificação e saldo.

**Administração**
- `POST /v1/eventos` / `GET /v1/eventos` — CRUD básico de eventos.
- `POST /v1/eventos/{{id}}/barracas` — cria barraca e atribui equipe.
- `POST /v1/eventos/{{id}}/produtos` — cria/atualiza catálogo.
- `GET /v1/relatorios/vendas?evento=&barraca=&produto=&from=&to=&format=csv|pdf` — relatórios.

**Notificações**
- `POST /v1/notificacoes/test` — dispara teste (uso interno).
- Webhooks de gateway para confirmação de pagamento.

**Observações**
- Tokens JWT; versionamento `v1` no prefixo; idempotência em vendas; auditoria por correlação de requisições.

---

## 9. Critérios de Aceite (amostras)
- **CA-01** (Compra com saldo suficiente): ao confirmar a venda, débito efetuado e notificação enviada em até 10 s.
- **CA-02** (Saldo insuficiente): tentativa de compra retorna mensagem de bloqueio sem registrar venda.
- **CA-03** (Limite diário): ao exceder limite, novas compras do dia são bloqueadas.
- **CA-04** (Estorno): venda dentro da janela configurada retorna crédito ao saldo e registra auditoria.
- **CA-05** (Relatórios): após encerramento do evento, organizador exporta CSV/PDF com vendas por barraca/produto/período.

---

## 10. Riscos & Mitigações
- **Integração de pagamentos**: atrasos de confirmação → usar webhooks/filas e retentativas.
- **Conectividade no evento**: instabilidade de rede → modo **offline** no PDV e sincronização.
- **Fraude/reuso de QR**: uso de **nonce**/assinatura e expirabilidade curta; verificação de sessão.
- **Picos de uso**: sobrecarga no PDV → cache de catálogo, endpoints enxutos, pré-carga e escalonamento horizontal.

---

## 11. Premissas & Restrições
- Coleta mínima de dados pessoais, seguindo LGPD.
- Particionamento lógico por **evento**.
- Controle de acesso por **papéis**.

---

