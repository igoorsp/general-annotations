# Resumo de Custos: AWS CloudWatch (sa-east-1)

Este documento detalha os custos mensais estimados para a camada de observabilidade da solução, considerando a região de São Paulo e o volume de 1.554.435 requisições anuais (média de 129.536 requisições mensais). O Free Tier foi desconsiderado.

---

## 1. Resumo Consolidado

| Categoria | Detalhe do Serviço | Custo Mensal (USD) |
| :--- | :--- | :--- |
| **CloudWatch Logs** | Ingestão e Armazenamento (14,83 GB) | $ 9,06 |
| **CloudWatch Metrics** | 10 Métricas Customizadas + API Grafana | $ 7,32 |
| **CloudWatch Insights** | Consultas sob demanda (Est. 10 queries/mês) | $ 0,89 |
| **TOTAL MENSAL ESTIMADO** | **Apenas Observabilidade** | **$ 17,27** |

---

## 2. Memória de Cálculo Detalhada

### CloudWatch Logs
O custo é baseado no volume de dados gerados (Ingestão) e no tempo que permanecem guardados (Retenção).

*   **Volume de Ingestão Mensal:**
    *   Step Functions: 129.536 req × 10 KB = 1,24 GB
    *   Lambdas (9x): (9 × 129.536 req) × 10 KB = 11,12 GB
    *   EKS (2x): (2 × 129.536 req) × 10 KB = 2,47 GB
    *   **Total Ingestão:** 14,83 GB
*   **Custo de Ingestão (sa-east-1):** 14,83 GB × $ 0,60 = **$ 8,90**
*   **Custo de Armazenamento:** Baseado no volume médio retido conforme as políticas de 5 dias (Lambda/EKS) e 60 dias (Step Functions) a $ 0,033/GB.
    *   **Total Armazenamento:** **$ 0,16**

### CloudWatch Metrics
Custo referente à manutenção das métricas e ao consumo de dados pelo Grafana via API.

*   **Métricas Customizadas:** 10 métricas × $ 0,30 = **$ 3,00**
*   **API GetMetricData (Grafana):** Estimativa de 1 dashboard atualizando 10 métricas a cada 1 minuto durante 30 dias.
    *   Total de requisições de métrica: 432.000 chamadas.
    *   Custo (sa-east-1): (432.000 / 1.000) × $ 0,01 = **$ 4,32**

### CloudWatch Logs Insights
Custo variável baseado na análise de dados para troubleshoot e auditoria.

*   **Preço por Scan (sa-east-1):** $ 0,006 por GB scanneado.
*   **Cenário Estimado:** 10 queries mensais varrendo todo o volume de logs do mês (14,83 GB).
    *   Cálculo: 10 × 14,83 GB × $ 0,006 = **$ 0,89**

---

## 3. Racional Técnico para o Time

*   **Impacto da Região:** O custo de ingestão em São Paulo ($ 0,60/GB) é o principal componente da fatura. Qualquer aumento no nível de verbosidade dos logs (ex: mudar de INFO para DEBUG) terá impacto direto e proporcional neste valor.
*   **Eficiência de Retenção:** A estratégia de manter apenas 5 dias de logs para Lambda e EKS é fundamental para manter o custo de armazenamento próximo de zero, focando o investimento apenas na ingestão.
*   **Otimização de Dashboards:** O custo de API ($ 4,32) é superior ao custo das métricas em si ($ 3,00). Isso ocorre devido à frequência de refresh do Grafana. Manter o intervalo de atualização em 1 minuto é o recomendado para equilibrar visibilidade e custo.
*   **Logs Insights:** É a ferramenta mais eficiente para análise de erros. Como o volume de dados é baixo (~15 GB), o uso da ferramenta é extremamente barato, permitindo debug profundo sem preocupações orçamentárias imediatas.