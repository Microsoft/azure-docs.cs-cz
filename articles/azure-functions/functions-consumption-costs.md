---
title: Odhad nákladů na plán spotřeby v Azure Functions
description: Naučte se, jak lépe odhadnout náklady, které vám mohou vzniknout při spuštění aplikace Function App v plánu spotřeby v Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 4967e0ff79a638891da4f784cf2f5f1ca4ddfe51
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578557"
---
# <a name="estimating-consumption-plan-costs"></a>Odhad nákladů na plán spotřeby

Existují tři typy hostujících plánů pro aplikaci, která běží v Azure Functions, přičemž každý plán má svůj vlastní cenový model: 

| Plánování | Description |
| ---- | ----------- |
| [**Využití**](consumption-plan.md) | Účtují se vám jenom čas, který aplikace Function App spouští. Tento plán zahrnuje[stránku s cenami] [bezplatného grantu]na jednotlivých předplatných.|
| [**Premium**](functions-premium-plan.md) | Poskytuje stejný mechanismus funkcí a škálování jako plán spotřeby, ale s vylepšeným výkonem a přístupem k virtuální síti. Náklady jsou založené na vaší zvolené cenové úrovni. Další informace najdete v tématu [plán Azure Functions Premium](functions-premium-plan.md). |
| [**Vyhrazeno (App Service)**](dedicated-plan.md) <br/>(úroveň Basic nebo vyšší) | Pokud potřebujete spustit na vyhrazených virtuálních počítačích nebo v izolaci, použijte vlastní image nebo využijte své nadměrné App Service plánování kapacity. Používá k [fakturaci pravidelného plánování App Service](https://azure.microsoft.com/pricing/details/app-service/). Náklady jsou založené na vaší zvolené cenové úrovni.|

Zvolili jste plán, který nejlépe podporuje výkon vaší funkce a požadavky na náklady. Další informace najdete v tématu [škálování Azure functions a hostování](functions-scale.md).

Tento článek se zabývá pouze plánem spotřeby, protože výsledkem tohoto plánu jsou variabilní náklady. Tento článek nahrazuje článek [Nejčastější dotazy týkající se fakturace za cenu plánu spotřeby](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) .

Durable Functions může také běžet v plánu spotřeby. Další informace o nákladových faktorech při použití Durable Functions najdete v tématu [Durable Functions fakturaci](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Náklady plánu Consumption

*Náklady* na spuštění jediného spuštění funkce se měří v *GB-s*. Náklady na spuštění jsou vypočítány kombinací využití paměti s dobou spuštění. Funkce, která se spouští s delšími náklady, jako funkce, která spotřebovává více paměti. 

Vezměte v úvahu případ, kdy velikost paměti, kterou funkce používá, zůstane konstantní. V tomto případě je výpočet nákladů jednoduchý násobení. Řekněme například, že funkce spotřebovaná 0,5 GB po dobu 3 sekund. Náklady na spuštění jsou pak `0.5GB * 3s = 1.5 GB-seconds` . 

Vzhledem k tomu, že se využití paměti mění v čase, výpočet je v podstatě celočíselné využití paměti v průběhu času.  Systém provede tento výpočet vzorkováním využití paměti procesem (spolu s podřízenými procesy) v pravidelných intervalech. Jak je uvedeno na [stránce s cenami], využití paměti se zaokrouhluje na nejbližší 128 MB kontejneru. Když váš proces používá 160 MB, bude se vám účtovat 256 MB. Výpočet vezme v úvahu souběžnost, což je více souběžných spuštění funkcí v rámci stejného procesu.

> [!NOTE]
> I když využití procesoru není přímo považováno za náklady na spuštění, může mít vliv na náklady, pokud má vliv na dobu provádění funkce.

U funkce aktivované protokolem HTTP dojde k chybě před tím, než se kód vaší funkce začne spouštět. neúčtují se za spuštění. To znamená, že 401 odezvy z platformy z důvodu ověření klíče rozhraní API nebo funkce App Service ověřování/autorizace se nepočítají na základě nákladů na spuštění. Podobně se nepočítají odpovědi stavového kódu 5xx, když k nim dojde na platformě před funkcí, která požadavek zpracovává. Odpověď 5XX vygenerovaná platformou po spuštění kódu funkce se pořád počítá jako vykonání, i když není chyba vyvolána vaším kódem funkce.

## <a name="other-related-costs"></a>Další související náklady

Při odhadování celkových nákladů na provoz vašich funkcí v jakémkoli plánu nezapomeňte, že modul runtime Functions používá několik dalších služeb Azure, které se účtují samostatně. Když počítáte ceny pro aplikace Function App, všechny triggery a vazby, které máte v integraci s dalšími službami Azure, vyžadují, abyste tyto další služby vytvořili a zaplatili. 

V případě funkcí spuštěných v plánu spotřeby jsou celkové náklady náklady na spuštění vašich funkcí a navíc náklady na šířku pásma a další služby. 

Při odhadování celkových nákladů na vaši aplikaci Function App a související služby použijte [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Související náklady | Popis |
| ------------ | ----------- |
| **Účet úložiště** | Každá aplikace Function App vyžaduje, abyste měli přidružený [účet Pro obecné účely Azure Storage](../storage/common/storage-introduction.md#types-of-storage-accounts), který se [fakturuje zvlášť](https://azure.microsoft.com/pricing/details/storage/). Tento účet se používá interně modulem runtime Functions, ale můžete ho použít i pro triggery a vazby úložiště. Pokud nemáte účet úložiště, vytvoří se pro vás při vytváření aplikace Function App. Další informace najdete v tématu [požadavky na účet úložiště](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Funkce spoléhá na [Application Insights](../azure-monitor/app/app-insights-overview.md) , aby poskytovala vysoce výkonné prostředí pro monitorování aplikací Function App. I když to není nutné, měli byste [Povolit integraci Application Insights](configure-monitoring.md#enable-application-insights-integration). K dispozici je bezplatný grant dat telemetrie každý měsíc. Další informace najdete [na stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). |
| **Šířka pásma sítě** | Neplatíte za přenos dat mezi službami Azure ve stejné oblasti. Můžete ale účtovat náklady na přenosy odchozích dat do jiné oblasti nebo mimo Azure. Další informace najdete v tématu [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Chování ovlivňující dobu provádění

Doba spuštění může ovlivnit následující chování vašich funkcí:

+ **Triggery a vazby**: doba potřebná ke čtení vstupu z a zápis výstupu do vašich [vazeb funkcí](functions-triggers-bindings.md) se počítá jako doba provádění. Pokud například funkce používá výstupní vazbu k zápisu zprávy do fronty služby Azure Storage, doba provádění zahrnuje dobu trvání zápisu zprávy do fronty, která je zahrnuta ve výpočtu nákladů na funkci. 

+ **Asynchronní spuštění**: doba, kterou vaše funkce čeká na výsledky asynchronního požadavku ( `await` v jazyce C#), se počítá jako doba provádění. Výpočet GB druhé je založen na počátečním a koncovém času funkce a využití paměti v tomto období. Co se v této době děje z důvodu aktivity procesoru, se do výpočtu nepočítá. Během asynchronních operací může být možné snížit náklady pomocí [Durable Functions](durable/durable-functions-overview.md). Neúčtuje se vám čas strávený za await ve funkcích Orchestrator.

## <a name="viewing-cost-related-data"></a>Zobrazení dat souvisejících s náklady

Ve [vaší faktuře](../cost-management-billing/understand/download-azure-invoice.md)si můžete zobrazit data související s náklady z celkového počtu **spuštění –** funkce a **čas spuštění – funkce** a také skutečné fakturované náklady. Tato data faktury jsou však za měsíc agregovaná za období minulé faktury. 

### <a name="function-app-level-metrics"></a>Funkce metrik na úrovni aplikace

Abyste lépe pochopili dopad vašich funkcí, můžete pomocí Azure Monitor zobrazit metriky související s náklady aktuálně vygenerované vašimi aplikacemi Function App. K získání těchto dat můžete použít buď [průzkumníka Azure monitor metriky](../azure-monitor/essentials/metrics-getting-started.md) v rozhraních API [Azure Portal] nebo REST.

#### <a name="monitor-metrics-explorer"></a>Monitorovat Průzkumníka metrik

Pomocí [Azure monitor Průzkumníku metrik](../azure-monitor/essentials/metrics-getting-started.md) můžete zobrazit data související s náklady pro aplikace funkcí plánu spotřeby v grafickém formátu. 

1. V horní části [Azure Portal] v části **Hledat služby, prostředky a hledání dokumentů** vyhledejte `monitor` a v části **služby** vyberte **monitor** .

1. Na levé straně vyberte **metriky**  >  **Vybrat prostředek** a pak pomocí nastavení pod imagí zvolte aplikaci Function App.

    ![Výběr prostředku Function App](media/functions-consumption-costing/select-a-resource.png)

      
    |Nastavení  |Navrhovaná hodnota  |Popis  |
    |---------|---------|---------|
    | Předplatné    |  Vaše předplatné  | Předplatné s vaší aplikací Function App  |
    | Skupina prostředků     | Vaše skupina prostředků  | Skupina prostředků, která obsahuje vaši aplikaci Function App.   |
    | Typ prostředku     |  App Services | Aplikace Function App se zobrazují jako instance App Services v monitorování. |
    | Prostředek     |  Vaše aplikace Function App  | Aplikace Function App, která se má monitorovat        |

1. Vyberte **použít** a zvolte svou aplikaci Function App jako prostředek, který chcete monitorovat.

1. Z **metriky** vyberte **počet spuštění funkce** a **součet** pro **agregaci**. Tím se do grafu přidá součet počtu spuštění během zvoleného období.

    ![Definování funkce Function App metriky, která se má přidat do grafu](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Vyberte **Přidat metriku** a opakováním kroků 2-4 přidejte do grafu **jednotky spuštění funkce** . 

Výsledný graf obsahuje součty pro metriky spuštění ve zvoleném časovém rozsahu, což je v tomto případě dvě hodiny.

![Graf počtu provedení a jednotek spuštění funkce](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Vzhledem k tomu, že počet jednotek spuštění je mnohem větší než počet spuštění, graf zobrazuje pouze jednotky spuštění.

Tento graf znázorňuje celkem 1 110 000 000 `Function Execution Units` spotřebovaných v období 2 hodiny, měřeno v MB-milisekundách. Chcete-li převést na GB-s, vydělte 1024000. V tomto příkladu aplikace Function App využila `1110000000 / 1024000 = 1083.98` GB-sekund. Tuto hodnotu můžete přijmout a vynásobit aktuální cenu za spuštění na[stránce] [Functions Price]Price Page, která vám poskytne náklady na tyto dvě hodiny za předpokladu, že jste už použili bezplatné granty na dobu spuštění. 

#### <a name="azure-cli"></a>Azure CLI

Rozhraní příkazového [řádku Azure CLI](/cli/azure/) obsahuje příkazy pro načítání metrik. Rozhraní příkazového řádku můžete použít z místního příkazového prostředí nebo přímo z portálu pomocí [Azure Cloud Shell](../cloud-shell/overview.md). Například následující příkaz [AZ monitor Metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) vrátí hodinová data ve stejném časovém období použitém dříve.

Nahraďte `<AZURE_SUBSCRIPTON_ID>` ID předplatného Azure, které spouští příkaz.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Tento příkaz vrátí datovou část JSON, která vypadá jako v následujícím příkladu:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Tato konkrétní odezva ukazuje, že z aplikace do jste vypnuli `2019-09-11T21:46` `2019-09-11T23:18` 1110000000 MB – milisekund (1083,98 GB-s).

### <a name="function-level-metrics"></a>Metriky na úrovni funkcí

Jednotky spuštění funkce jsou kombinací času spuštění a využití paměti. díky tomu je obtížné metriky pochopit využití paměti. Data paměti nejsou metrikou, která je aktuálně dostupná prostřednictvím Azure Monitor. Pokud ale chcete optimalizovat využití paměti ve vaší aplikaci, může použít data čítače výkonu shromažďovaná nástrojem Application Insights.  

Pokud jste to ještě neudělali, [povolte Application Insights ve vaší aplikaci Function App](configure-monitoring.md#enable-application-insights-integration). Když je tato integrace povolená, můžete na [portálu zadat dotaz na tato data telemetrie](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o monitorování aplikací Function App](functions-monitoring.md)

[Stránka s cenami]:https://azure.microsoft.com/pricing/details/functions/
[Azure Portal]: https://portal.azure.com
