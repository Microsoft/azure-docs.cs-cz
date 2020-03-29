---
title: Odhad nákladů plánu spotřeby ve funkcích Azure
description: Zjistěte, jak lépe odhadnout náklady, které vám mohou vzniknout při spuštění aplikace funkce v plánu Spotřeba v Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963984"
---
# <a name="estimating-consumption-plan-costs"></a>Odhad nákladů plánu spotřeby

V současné době existují tři typy hostingových plánů pro aplikaci, která běží ve službě Azure Functions, přičemž každý plán má svůj vlastní cenový model: 

| Plánování | Popis |
| ---- | ----------- |
| [**Spotřeby**](functions-scale.md#consumption-plan) | Poplatky se vám účtují jenom za dobu, po kterou je aplikace pro funkci spuštěna. Tento plán zahrnuje[bezplatnou stránku s cenami] [grantů]na základě předplatného.|
| [**Premium**](functions-scale.md#premium-plan) | Poskytuje stejné funkce a mechanismus škálování jako plán spotřeby, ale s rozšířeným výkonem a přístupem k virtuální síti. Cena je založena na vámi zvolené cenové úrovni. Další informace najdete v [tématu Plán Azure Functions Premium](functions-premium-plan.md). |
| [**Vyhrazeno (služba aplikace)**](functions-scale.md#app-service-plan) <br/>(základní úroveň nebo vyšší) | Když potřebujete spustit ve vyhrazených virtuálních počítačích nebo izolovaně, použijte vlastní image nebo chcete použít nadbytečnou kapacitu plánu služby App Service. Používá [pravidelné fakturace plánu služby App Service](https://azure.microsoft.com/pricing/details/app-service/). Cena je založena na vámi zvolené cenové úrovni.|

Zvolili jste plán, který nejlépe podporuje výkon vaší funkce a požadavky na náklady. Další informace najdete v [tématu Škálování funkcí Azure a hostování](functions-scale.md).

Tento článek se zabývá pouze plánem Spotřebu, protože tento plán má za následek variabilní náklady. Tento článek nahrazuje článek [nejčastějších dotazů k fakturaci nákladů plánu spotřeby.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

Trvalé funkce lze také spustit v plánu spotřeby. Další informace o nákladech při používání trvalých funkcí naleznete v [tématu Durable Functions Billing](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Náklady plánu Consumption

Náklady *na* spuštění spuštění jedné funkce provádění se měří v *GB sekund*. Náklady na spuštění se vypočítá kombinací jeho využití paměti s jeho čas spuštění. Funkce, která běží déle, stojí více, stejně jako funkce, která spotřebovává více paměti. 

Zvažte případ, kdy množství paměti používané funkcí zůstane konstantní. V tomto případě je výpočet nákladů jednoduchým násobením. Řekněme například, že vaše funkce spotřebovává 0,5 GB po dobu 3 sekund. Pak náklady na `0.5GB * 3s = 1.5 GB-seconds`spuštění je . 

Vzhledem k tomu, že využití paměti se v průběhu času mění, výpočet je v podstatě nedílnou součástí využití paměti v čase.  Systém provádí tento výpočet vzorkováním využití paměti procesu (spolu s podřízenými procesy) v pravidelných intervalech. Jak je uvedeno na [stránce s cenami], využití paměti se zaokrouhluje nahoru na nejbližší 128 MB bloku. Pokud proces používá 160 MB, bude vám účtováno 256 MB. Výpočet bere v úvahu souběžnost, což je více souběžných spuštění funkce ve stejném procesu.

> [!NOTE]
> Zatímco využití procesoru není přímo považován o náklady na spuštění, může mít vliv na náklady, pokud ovlivňuje dobu provádění funkce.

## <a name="other-related-costs"></a>Ostatní související náklady

Při odhadu celkových nákladů na spuštění funkcí v libovolném plánu nezapomeňte, že runtime Funkce používá několik dalších služeb Azure, které se účtují zvlášť. Při výpočtu cen pro funkční aplikace vyžadují všechny aktivační události a vazby, které máte integrovat s jinými službami Azure, k vytvoření a platbě za tyto další služby. 

U funkcí spuštěných v plánu spotřeby jsou celkové náklady náklady na provádění vašich funkcí plus náklady na šířku pásma a další služby. 

Při odhadu celkových nákladů na aplikaci funkcí a souvisejících služeb použijte [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Související náklady | Popis |
| ------------ | ----------- |
| **Účet úložiště** | Každá aplikace funkcí vyžaduje, abyste měli přidružený [účet úložiště Azure Storage](../storage/common/storage-introduction.md#types-of-storage-accounts)pro obecné účely , který se [účtuje zvlášť](https://azure.microsoft.com/pricing/details/storage/). Tento účet se používá interně funkce runtime, ale můžete také použít pro storage aktivační události a vazby. Pokud nemáte účet úložiště, jeden se vytvoří pro vás při vytvoření aplikace funkce. Další informace najdete v [tématu Požadavky na účet úložiště](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Funkce závisí na [Application Insights](../azure-monitor/app/app-insights-overview.md) poskytovat vysoce výkonné monitorování prostředí pro vaše aplikace funkce. I když to není nutné, měli byste [povolit integraci Application Insights](functions-monitoring.md#enable-application-insights-integration). Každý měsíc je zahrnuto bezplatné udělení telemetrických dat. Další informace najdete [na stránce s cenami Azure Monitoru](https://azure.microsoft.com/pricing/details/monitor/). |
| **Šířka pásma sítě** | Neplatíte za přenos dat mezi službami Azure ve stejné oblasti. Můžete však vzniknout náklady na odchozí přenosy dat do jiné oblasti nebo mimo Azure. Další informace najdete v [tématu Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Chování ovlivňující čas spuštění

Následující chování funkcí může ovlivnit dobu provádění:

+ **Aktivační události a vazby**: Čas, který trvalo čtení vstupu a zápis výstupu do [vazby funkce](functions-triggers-bindings.md) se počítá jako čas spuštění. Například když vaše funkce používá výstupní vazbu k zápisu zprávy do fronty úložiště Azure, doba spuštění zahrnuje čas, který je čas přijatý k zápisu zprávy do fronty, která je zahrnuta ve výpočtu nákladů na funkci. 

+ **Asynchronní spuštění**: Čas, který vaše funkce čeká na výsledky`await` asynchronního požadavku ( v C#) se počítá jako čas spuštění. Výpočet GB-second je založen na počátečnía koncový čas funkce a využití paměti za toto období. Co se děje v průběhu této doby, pokud jde o aktivitu procesoru není zahrnuta do výpočtu. Je možné snížit náklady během asynchronních operací pomocí [trvalé funkce](durable/durable-functions-overview.md). Neúčtují vám čas strávený v orchestrátoru.

## <a name="view-execution-data"></a>Zobrazit data spuštění

Na [faktuře](/azure/billing/billing-download-azure-invoice)můžete zobrazit údaje související s náklady **celkových spuštění – funkce** a funkce spuštění **spolu**se skutečnými fakturovanými náklady. Tato data faktury jsou však měsíční agregací za minulé fakturační období. 

Chcete-li lépe pochopit dopad na náklady vašich funkcí, můžete pomocí Služby Azure Monitor zobrazit metriky související s náklady, které jsou aktuálně generovány vašimi funkčními aplikacemi. K získání těchto dat můžete použít [průzkumník metrik Azure Monitor](../azure-monitor/platform/metrics-getting-started.md) na webu Azure [Portal] nebo ROZHRANÍ API REST.

### <a name="monitor-metrics-explorer"></a>Průzkumník metrik sledování

Pomocí [průzkumníka metrik Azure Monitor](../azure-monitor/platform/metrics-getting-started.md) uobrazte data související s náklady pro aplikace funkce plánu spotřeby v grafickém formátu. 

1. V horní části [portálu Azure] ve **vyhledávacích službách, prostředcích a dokumentech** vyhledejte `monitor` a vyberte Sledovat **v** části **Služby**.

1. Vlevo vyberte **Metriky Vyberte** > **prostředek**, pak pomocí nastavení pod obrázkem zvolte aplikaci funkce.

    ![Výběr prostředku aplikace pro funkce](media/functions-consumption-costing/select-a-resource.png)

      
    |Nastavení  |Navrhovaná hodnota  |Popis  |
    |---------|---------|---------|
    | Předplatné    |  Vaše předplatné  | Předplatné s vaší funkční aplikací.  |
    | Skupina prostředků     | Vaše skupina prostředků  | Skupina prostředků, která obsahuje aplikaci funkce.   |
    | Typ prostředku     |  App Services | Aplikace funkcí se zobrazují jako instance služby App Services v monitoru. |
    | Prostředek     |  Vaše funkční aplikace  | Aplikace funkce pro sledování.        |

1. Vyberte **Použít,** chcete-li vybrat aplikaci funkce jako prostředek ke sledování.

1. Z **metriky**zvolte **Počet spuštění funkce** a **Součet** pro **agregaci**. Tím se do grafu přidá součet počtu spuštění během zvoleného období.

    ![Definování metriky aplikace funkcí, kterou chcete přidat do grafu](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Vyberte **Přidat metriku** a opakujte kroky 2-4 a přidejte do grafu **jednotky spuštění funkce.** 

Výsledný graf obsahuje součty pro obě metriky spuštění ve zvoleném časovém rozsahu, což je v tomto případě dvě hodiny.

![Graf počtu spuštění funkcí a jednotek provádění](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Vzhledem k tomu, že počet jednotek spuštění je mnohem větší než počet spuštění, graf pouze zobrazuje jednotky spuštění.

Tento graf ukazuje celkem 1,11 miliardy `Function Execution Units` spotřebovaných za dvě hodiny, měřeno v milisekundách MB. Chcete-li převést na GB sekund, vydělte 1024000. V tomto příkladu aplikace `1110000000 / 1024000 = 1083.98` funkce spotřebovává GB sekund. Tuto hodnotu můžete vzít a vynásobit aktuální cenou času spuštění na[stránce ceny] [stránky Funkce], která vám poskytne náklady na tyto dvě hodiny, za předpokladu, že jste již použili žádné volné granty času provádění. 

### <a name="azure-cli"></a>Azure CLI

Azure [CLI](/cli/azure/) má příkazy pro načítání metriky. ClI můžete použít z prostředí místního příkazu nebo přímo z portálu pomocí [Azure Cloud Shell](../cloud-shell/overview.md). Například následující příkaz [az monitor metriky vrátí](/cli/azure/monitor/metrics#az-monitor-metrics-list) hodinová data za stejné časové období, které bylo použito dříve.

Nezapomeňte nahradit `<AZURE_SUBSCRIPTON_ID>` ID předplatného Azure s příkazem.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Tento příkaz vrátí datovou část JSON, která vypadá jako následující příklad:

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
Tato konkrétní odpověď `2019-09-11T21:46` ukazuje, že z do `2019-09-11T23:18`, aplikace spotřebované 1110000000 MB-milisekund (1083.98 GB-seconds).

## <a name="determine-memory-usage"></a>Určení využití paměti

Jednotky spuštění funkce jsou kombinací doby provádění a využití paměti, což ztěžuje metriku pro pochopení využití paměti. Paměťová data není metrika, která je momentálně dostupná prostřednictvím Azure Monitoru. Pokud však chcete optimalizovat využití paměti vaší aplikace, můžete použít data čítače výkonu shromážděná application insights.  

Pokud jste tak ještě neučinili, [povolte Application Insights ve své aplikaci funkce](functions-monitoring.md#enable-application-insights-integration). Tato integrace je povolena, můžete [zadat dotaz na tato telemetrická data na portálu](functions-monitoring.md#query-telemetry-data).  

V části **Sledování**vyberte **Protokoly (Analytics)**, zkopírujte následující telemetrický dotaz a vložte ho do okna dotazu a vyberte **Spustit**. Tento dotaz vrátí celkové využití paměti v každém vzorkovaném čase.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Výsledky vypadají jako následující příklad:

| časové \[razítko UTC\]          | jméno          | value       |
|----------------------------|---------------|-------------|
| 12.9.2019, 1:05:14\.947 | Soukromé bajty | 209,932,288 |
| 9/12/2019, 1:06:14\.994 AM | Soukromé bajty | 212,189,184 |
| 12.9.2019, 1:06:30\.010 | Soukromé bajty | 231,714,816 |
| 12.9.2019, 1:07:15\.040 | Soukromé bajty | 210,591,744 |
| 9/12/2019, 1:12:16\.285 | Soukromé bajty | 216,285,184 |
| 9/12/2019, 1:12:31\.376 AM | Soukromé bajty | 235,806,720 |

## <a name="function-level-metrics"></a>Metriky na úrovni funkce

Azure Monitor sleduje metriky na úrovni prostředků, což pro funkce je aplikace funkce. Integrace Application Insights vyzařuje metriky pro funkci. Zde je příklad analytického dotazu, který získá průměrnou dobu trvání funkce:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| jméno                       | průměrná doba milisekund |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16\.087                     |
| Počet maxdurationů_ | 90\.249                     |
| FrontaMinDurationMs | 8\.522                      |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aplikacích funkcí monitoringu](functions-monitoring.md)

[cenová stránka]:https://azure.microsoft.com/pricing/details/functions/
[Portál Azure]: https://portal.azure.com
