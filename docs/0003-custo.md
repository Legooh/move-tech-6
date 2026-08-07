# ADR 0003 — Definir teto de custos mensais máximo

- **Status:** aceito
- **Data:** 06/08/2026

## Contexto

A solução utilizava recursos pagos do Magalu Cloud (VMs para cluster, PostgreSQL gerenciado, IP público para LoadBalancer). Não havia orçamento formal definido, criando risco de "custo invisível" onde a infraescala silenciosamente (autoscaling descontrolado, storage crescendo sem monitoramento). O time precisava de uma referência clara para saber quando parar de escalar verticalmente e otimizar código primeiro, antes de pedir mais recursos. O requisito econômico estava implícito mas nunca quantificado.

## Decisão

Vamos estabelecer um teto máximo de R$ [INSERIR VALOR EX: 300,00] por mês como limite de orçamento para esta infraestrutura.

## Consequências

- Positivo: Força decisões conscientes sobre escalabilidade (otimizar queries antes de subir máquina maior); cria gatilho de alerta automático ao atingir 80% do teto; evita surpresas na fatura no final do mês.
- Negativo: Pode limitar experimentações rápidas se o teto for muito baixo; requer disciplina para medir custo real constantemente (calculadora MGC + monitoramento).
- Neutro: Define prioridade: performance e escala são importantes, mas têm preço. Se P95 estiver em 450ms e precisar de máquina maior que encareça, revisamos arquitetura (cache) antes de gastar.

## Alternativas consideradas

**Orçamento ilimitado ("deixa rodar e vemos"):** Rejeitada porque nuvem sem controle vira bomba-relógio financeira. Custo cresce exponencialmente com mau uso (storage não deletado, pods ociosos, IPs esquecidos).

**Orçamento zero (tudo self-hosted gratuito):** Inviável pois requereria operar banco de dados e manutenção de VMs manualmente, violando o princípio de focar em desenvolvimento de software (conforme ADR-0001 decidimos pagar por managed services para reduzir opex humano).
