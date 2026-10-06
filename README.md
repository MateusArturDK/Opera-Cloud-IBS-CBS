# Opera-Cloud-IBS-CBS
Implementação do imposto IBS e CBS para o sistema Opera Cloud

O **IBS** (Imposto sobre Bens e Serviços) e a **CBS** (Contribuição sobre Bens e Serviços) formam o novo IVA Dual (Imposto sobre Valor Agregado). com a obrigatoriedade prevista para, 1º de janeiro de 2027, esse repositório ira demonstrar como configurei esses impostos para o sistema **Opera Cloud Hospitality**.



Resumo

Abaixo estão detalhados os problemas enfrentados durante a implementação e as respectivas soluções aplicadas, em ordem cronológica de configuração:

  1. Ajuste do XML no OFIS (Payload `FEDERAL_TAX`)
Para que o OPERA conseguisse enviar as informações do novo escopo tributário, foi necessário intervir na estrutura gerada pelo conector local.
  Ação: Ajuste no arquivo de configuração/XSLT do adaptador OFIS (FLIPTemplateGenericJSON).
  Motivo: Garantir que o payload de `FEDERAL_TAX` (onde trafegam os dados do IBS/CBS) fosse montado e entregue corretamente na API da Inventti, em conformidade com o   novo schema exigido pela prefeitura.

### 2. Mapeamento de Revenue Buckets (Erro de XSD `IndDest`)
Durante os testes de checkout, o sistema apresentava o erro de validação: `The element 'IBSCBS' has invalid child element 'IndDest'. List of possible elements expected: 'IndDoacao', 'cIndOp'`.
* **Causa:** O *Revenue Bucket* de Transaction Codes secundários (Taxa de Serviço, Minibar, Restaurante) estava preenchido apenas com `X` ou em branco, quebrando a estrutura de 5 posições obrigatórias do IBS/CBS.
* **Solução:** Configuração rigorosa da string separada por *pipes* (`|`) nos mapeamentos fiscais do OPERA:
  * **Sintaxe Exigida:** `Código LC 116 | NBS | cIndOp | cClassTrib | Tipo`
  * **Exemplo Diárias / Serviços (`S`):** `09.01|1.0303.90.00|30101|200048|S`
  * **Exemplo Taxa de Serviço / Repasse (`X`):** `09.01|1.0303.90.00|30101|200048|X`
  * **Exemplo F&B / Mercadorias (`G`):** `09.01|1.0303.90.00|30101|200048|G`
