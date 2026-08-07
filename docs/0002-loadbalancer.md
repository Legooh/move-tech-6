# ADR 0002 — Expor aplicação via Service LoadBalancer

- **Status:** aceito
- **Data:** 06/08/2026

## Contexto

Minha aplicação FastAPI roda na porta 8000, mas isso é só interno. Quando eu ou alguém tentava acessar de fora, não conseguia nada. Eu vi que o ambiente já tinha um Service LoadBalancer do Magalu Cloud configurado, com IP público na porta 80, jogando tráfego pra porta 8000 dos meus 2 Por enquanto só tenho esse serviço cloud-application mesmo, então não precisava de nada complexo ainda. Só queria que funcionasse quando eu digitasse o IP ou chamasse via API/Slack.

## Decisão

Vamos usar esse Service LoadBalancer (:80) como entrada principal, jogando pros pods na :8000.

## Conseqüências

- Positivo: Configuração bem simples (é só marcar type=LoadBalancer); o IP não muda se eu reiniciar os pods; se algum pod cai, o LoadBalancer para de mandar tráfego pra ele sozinho; todas minhas rotas (/orders, /health, /metrics) ficam acessíveis num lugar só.
- Negativo: Cobra taxa mensal por esse IP público; everything fica exposto na mesma porta (até o /metrics que talvez devesse ser só interno); se o LoadBalancer cai, cai tudo junto (mas isso é raro e tem SLA do provedor).
- Neutro: Se amanhã eu precisar colocar outro serviço online (tipo o Grafana ou um admin panel), daí sim vou ter que repensar e talvez usar Ingress; separar portas (:80 externo vs :8000 interno) já deixa certa camada de segurança básica.

## Alternativas consideradas

**NodePort:** Fiquei tentado porque é gratuito (abre uma porta tipo 30123 direto no servidor). Mas achei feio e instável — se reinicia o nó, a porta pode mudar, e deixar portas altas abertas assim parece meio gambiarra de produção.

**Ingress Controller (Traefik/Nginx):** Pesquisei e parece profissional, usam muito em empresas grandes. Mas pra 1 serviço só é muito burocrático — tenho que
