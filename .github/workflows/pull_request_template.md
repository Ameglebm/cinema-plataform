# ⚙️ PR #1 – Estrutura base do projeto e pipeline CI/CD
> `Infra` · `CI/CD` · `GitHub Actions`

Adiciona a estrutura completa de infraestrutura do projeto e o pipeline de CI/CD com GitHub Actions. Os arquivos de infra (Docker, K8s, Terraform, scripts) foram criados vazios e serão implementados em PRs futuras conforme a evolução do projeto.

---

## 🧠 1. Decisões Tomadas

**Por quê criar os arquivos de infra vazios agora?**
Para garantir que a estrutura de diretórios do projeto já reflita a arquitetura final desde o início. Facilita a navegação, documenta as intenções e evita criar pastas avulsas no futuro.

**Por quê CI separado de CD?**
São responsabilidades distintas: o CI valida o código (lint, testes, build) em qualquer PR. O CD só roda na `main` e entrega a imagem Docker. Separar os workflows deixa cada um com escopo claro e permite evoluir independentemente.

**Por quê GHCR (GitHub Container Registry)?**
Já integrado ao repositório via `GITHUB_TOKEN`, sem custo extra e sem configuração de secrets adicionais para o registry.

**Por quê o job de deploy está comentado no CD?**
K8s e Terraform ainda não estão implementados. O placeholder já documenta a intenção e facilita a ativação futura sem reescrever o workflow.

---

## 📁 2. Arquivos Modificados

```
.github/
├── pull_request_template.md      ← template padrão de PR do projeto
└── workflows/
    ├── ci.yml                    ← pipeline de validação do código
    └── cd.yml                    ← pipeline de entrega da imagem Docker

docker/
├── docker-compose.yml            ← orquestração do ambiente local de desenvolvimento
└── docker-compose.prod.yml       ← orquestração do ambiente de produção

k8s/
├── configmap.yaml                ← variáveis de configuração do cluster
├── deployment.yaml               ← define como a aplicação sobe no cluster
├── ingress.yaml                  ← roteamento de tráfego externo para os serviços
├── namespace.yaml                ← isolamento do projeto dentro do cluster
├── secret.yaml                   ← variáveis sensíveis (senhas, tokens)
└── service.yaml                  ← expõe os pods internamente no cluster

scripts/
└── entrypoint.sh                 ← script de inicialização do container em produção

terraform/
├── main.tf                       ← define toda a infraestrutura cloud (VPC, instâncias, banco)
├── outputs.tf                    ← exporta valores gerados pelo Terraform (IPs, URLs)
├── terraform.tfvars.example.tf   ← exemplo de variáveis para configurar o ambiente
└── variables.tf                  ← declaração das variáveis usadas no main.tf
```

---

## 🔍 3. O que mudou

**`.github/workflows/ci.yml`**
Pipeline de integração contínua com 4 jobs em sequência: `lint` → `unit-tests` (com upload de coverage) e `e2e-tests` (com postgres:15, redis:7 e rabbitmq:3 como service containers) → `build`. Roda em push para `main`/`develop` e em PRs de qualquer branch.

**`.github/workflows/cd.yml`**
Pipeline de entrega contínua. Roda apenas em push para `main`. Faz build da imagem Docker e push para o GHCR com tags `latest` e `sha-<commit>`. Job de deploy para K8s está comentado aguardando implementação do Terraform e manifests.

**`.github/pull_request_template.md`**
Template padrão de PR adotado pelo projeto. Toda PR nova abre automaticamente com essa estrutura.

**`docker/`, `k8s/`, `scripts/`, `terraform/`**
Estrutura de diretórios criada com os arquivos que serão implementados nas próximas PRs, cada um responsável por uma camada da infraestrutura.

---

## ✅ 4. Checklist

- [x] CI roda em PRs de qualquer branch
- [x] CD roda apenas na `main`
- [x] Jobs do CI em ordem correta (lint → testes → build)
- [x] Service containers do E2E espelham o docker-compose local
- [x] Imagem Docker publicada no GHCR
- [x] Job de deploy comentado com instruções claras para ativação futura
- [x] PR template adicionado em `.github/`
- [ ] Arquivos de infra implementados (próximas PRs)

---

> _infra · ci/cd · status: aguardando revisão_