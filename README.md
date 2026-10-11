# Métricas das Redes do Criador - Sincronização Resiliente

Serviço desenvolvido em Dart para sincronização e agregação de métricas de criadores em redes sociais (Instagram, TikTok, YouTube e X), projetado para lidar com janelas de tempo sobrepostas, respostas duplicadas, timeouts e limites de taxa de requisição HTTP 429 (`Retry-After`).

---

## 🎯 O que olhar primeiro

1. **Idempotência e Deduplicação (`lib/src/domain/entities/post_matric.dart`):**
   * Cada snapshot de métrica gera uma `idempotencyKey` única baseada no identificador do post, plataforma e momento original de captura (`capturedAt`).
   * Sincronizações sucessivas ou com janelas de tempo sobrepostas não duplicam métricas nem inflam contadores.

2. **Resiliência e Controle de Taxa (`lib/src/services/metrics_sync_service.dart`):**
   * **Tratamento de 429 com Retry-After:** Quando o provedor responde com 429, o serviço suspende a execução respeitando o tempo estipulado pelo cabeçalho.
   * **Teto de Espera e Tentativas:** Limite configurável de tentativas (`maxRetries`) para evitar loops infinitos em caso de falha transitória ou timeout, além de abortar a sincronização caso o `Retry-After` ultrapasse o teto máximo permitido (`maxRetryAfterWait`).
   * **Auditoria Temporal (`fetchedAt`):** Registro explícito do instante em que a métrica foi efetivamente consultada pela aplicação.

3. **Simulação Temporal e Testes Unitários (`test/metrics_sync_test.dart`):**
   * Uso de abstrações de tempo (`ControlledClock` e `SimulatedSleeper`) para testar o comportamento do `Retry-After` de forma determinística e instantânea, sem atrasar a execução da suíte de testes.

---

## 🚀 Como Executar

### Pré-requisitos
* Dart SDK `^3.0.0` (ou Flutter SDK instalado).

### Instalação de Dependências
```bash
dart pub get
```

---
## 🤖 Declaração sobre Uso de IA

*Uso de IA:* A Inteligência Artificial foi utilizada exclusivamente para a identificação e levantamento das regras do problema (mapeamento das restrições de idempotência, interpretação das respostas 429 com Retry-After e definição dos cenários de teste necessários).

*Desenvolvimento e Revisão:* A concepção da arquitetura, a implementação de todas as classes de domínio, exceções, controle de relógio simulado, serviço de sincronização e testes unitários foram codificados e revisados manualmente.
