# ADR 0001 — Usar PostgreSQL gerenciado fora do cluster

- **Status:** aceito
- **Data:** 06/08/2026

## Contexto

Eu precisava persistir dados (pedidos e itens) num banco relacional pra aplicação funcionar. Quando olhei o cluster, já tinha um PostgreSQL rodando como serviço gerenciado pela Magalu Cloud, acessível via aquele secret `db-secret`, lá fora do Kubernetes. Eu sou só eu fazendo isso, não tenho DBA pra me ajudar, e se eu configurar banco dentro do cluster e der problema, eu sozinho vou ter que dar conta do backup e restore. O requisito era manter 99,5% disponível mas sem virar noite operando infraestrutura.

## Decisão

Vamos usar o banco PostgreSQL gerenciado que já existe (DBaaS), mantendo ele fora do cluster.

## Consequências

- Positivo: Não preciso me preocupar com backup nem atualização do banco, o provedor faz isso; mesmo que eu apague o cluster por engano, os dados continuam lá seguros; é só focar em código.
- Negativo: Vai ter um custo mensal fixo pelo serviço; a latência vai ser um pouco maior (~1-3ms) porque o pedido atravessa rede externa; dependo da internet estar ligada pro app achar o banco.
- Neutro: A senha de conexão fica guardada no Secret `db-secret` do K8s; se um dia quiser mudar de provedor, dou um dump .sql normal e subi em qualquer outro Postgres.

## Alternativas consideradas

**Rodar Postgres dentro do cluster (StatefulSet + PVC):** Pensei nisso porque seria "de graça" (só usa recursos da VM) e super rápido. Mas descartei porque eu não sei configurar alta disponibilidade de banco direito, e se o nó do cluster cair eu perco acesso aos dados até consertar manualmente. Muito arriscado pra quem está aprendendo ainda.

**SQLite local:** Nem considerei direito porque não
