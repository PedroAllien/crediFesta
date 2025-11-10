# CrediFesta

## Visão rápida

CrediFesta é um projeto para gestão de créditos em eventos escolares. Permite que responsáveis recarreguem saldo (PIX/cartão), que alunos apresentem um código/QR para compra rápida e que atendentes realizem vendas no PDV de forma idempotente e auditável. O projeto contém documentação de requisitos, diagramas de arquitetura (C4/PlantUML) e esboços de API.

## O que há neste repositório

- `DER_CrediFesta.md` — Documento de Especificação de Requisitos (visão funcional, RN, atores, critérios de aceite e protótipos de API).
- `diagramas/` — Diagramas em PlantUML (contexto, containers e componentes). Caminho: `diagramas/contexto/`, `diagramas/container/`, `diagramas/componentes/`.
  - `diagramas/contexto/CrediFesta-contexto.puml`
  - `diagramas/container/CrediFesta-container.puml`
  - `diagramas/componentes/CrediFesta-componentes.puml`

## Arquitetura (resumo)

Baseado no DER e nos diagramas, as decisões arquiteturais principais são:

- Frontends: apps móveis (Responsável / Aluno), PDV web/tablet e painel admin (web).
- Backend: API REST (esboço indicado como Django + DRF nos diagramas) com autenticação JWT, regras de negócio (vendas, limites, estornos), geração/validação de QR com assinatura HMAC e controle anti-replay (nonce).
- Persistência: PostgreSQL para dados transacionais; Redis para nonces e idempotency keys.
- Workers: filas (Celery/RQ) para conciliação, geração de relatórios e envio de notificações.
- Integrações externas: PSP (PIX/cartão) via webhooks, serviço de push/e-mail, S3/MinIO para arquivos e KMS/Vault para chaves HMAC.

Esses detalhes estão modelados nos diagramas dentro de `diagramas/` e no `DER_CrediFesta.md`.

## Documentação disponível

- Requisitos e regras de negócio: `DER_CrediFesta.md` (pontos importantes, critérios de aceite e protótipos de API).
- Diagramas C4 (PlantUML): dentro de `diagramas/` — contexto, containers e componentes.
- Protótipos de API (esboço): seção "Protótipos de API (esboço REST)" em `DER_CrediFesta.md`.

## Como visualizar os diagramas PlantUML


Recomenda-se usar VS Code com a extensão PlantUML (ou outra ferramenta de visualização PlantUML). Opções rápidas:

1. VS Code (recomendado)

   - Instale a extensão "PlantUML" (jwbennett/PlantUML ou PlantUML by jebbs) e abra os arquivos `.puml` em `diagramas/`.

2. CLI (macOS) — via Homebrew

```bash
# Instalar (se necessário)
brew install plantuml

# Renderizar um diagrama (gera PNG/SVG no mesmo diretório)
plantuml diagramas/contexto/CrediFesta-contexto.puml
```

Nota: o PlantUML necessita de Java; se usar o `plantuml.jar` ou um container Docker, ajuste conforme seu ambiente.

## Endpoints (esboço)

O DER contém um esboço de API (prefixo `/v1`), com rotas principais como:

- `POST /v1/auth/signup`, `POST /v1/auth/login`, `POST /v1/auth/forgot` — autenticação e perfis
- `POST /v1/alunos/{id}/recargas`, `GET /v1/alunos/{id}/saldo` — recargas e consulta de saldo
- `POST /v1/pdv/vendas` — criar venda (idempotente)
- `POST /v1/pdv/vendas/{id}/estorno` — estornar venda
- `GET /v1/pdv/alunos/{codigo}/verificar` — validar QR/código do aluno

Consulte a seção "Protótipos de API (esboço REST)" em `DER_CrediFesta.md` para detalhes.

## Estrutura sugerida para desenvolvimento

Um esqueleto possível (conforme diagramas):

- `backend/` — API (Django + DRF sugerido)
- `frontend/admin/` — painel admin (React/Next)
- `frontend/pdv/` — SPA para PDV
- `mobile/` — apps Flutter (responsável e aluno)
- `infra/` — IaC, Helm / Dockerfiles / compose
- `scripts/` — helpers para gerar diagramas, seeds e migrações

Esta organização não está implementada neste repositório (a estrutura acima é sugestão baseada na arquitetura); use-a como referência ao iniciar implementações.

## Próximos passos recomendados

1. Criar skeleton do backend (ex.: `backend/`) com OpenAPI e testes básicos (endpoint health + create sale flow).
2. Implementar scripts de CI (lint, tests) e um esquema básico de deploy/containers.
3. Automatizar geração/preview dos diagramas (ex.: GitHub Actions que gera PNG/SVG a partir dos `.puml`).
4. Adicionar um README mais detalhado por subprojeto quando os diretórios de código forem criados.

## Contribuição

Abra issues para discutir mudanças de requisitos, diagramas ou especificações. Se for contribuir com código, adicione PRs pequenos com testes e atualização do README de submódulos.

---
Arquivo atualizado automaticamente: resumo da arquitetura e onde encontrar a documentação (DER e diagramas). Se quiser, eu posso:

- Gerar previews PNG/SVG dos PlantUML aqui.
- Criar um esqueleto inicial do backend com Docker + Django/DRF.
- Gerar um arquivo OpenAPI a partir dos protótipos de API do DER.

Diga o que prefere que eu faça em seguida.
