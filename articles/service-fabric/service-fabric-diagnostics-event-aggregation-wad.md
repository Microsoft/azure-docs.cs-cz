---
title: Agregace událostí s Windows Azure Diagnostics
description: Naučte se agregovat a shromažďovat události pomocí WAD pro monitorování a diagnostiku clusterů Azure Service Fabric.
ms.topic: conceptual
ms.date: 04/03/2018
ms.openlocfilehash: bbc8efcb2600e1832ad8a37560ab231a4a7f3185
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626724"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregace a shromažďování událostí pomocí Azure Diagnostics Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Pokud používáte cluster Azure Service Fabric, je vhodné shromáždit protokoly ze všech uzlů v centrálním umístění. Protokoly v centrálním umístění vám pomůžou analyzovat a řešit problémy v clusteru nebo problémy s aplikacemi a službami běžícími v tomto clusteru.

Jedním ze způsobů, jak nahrávat a shromažďovat protokoly, je použít rozšíření Windows Azure Diagnostics (WAD), které nahrává protokoly do Azure Storage a má také možnost odesílat protokoly do Azure Application Insights nebo Event Hubs. Můžete také použít externí proces ke čtení událostí z úložiště a jejich umístění do produktu Analysis Platform, jako jsou [protokoly Azure monitor](./service-fabric-diagnostics-oms-setup.md) nebo jiné řešení pro analýzu protokolů.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
V tomto článku se používají následující nástroje:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/)
* [Šablona Azure Resource Manageru](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric události platformy
Service Fabric nastavíte v několika málo integrovaných [kanálech protokolování](service-fabric-diagnostics-event-generation-infra.md), u kterých jsou následující kanály předem nakonfigurované s příponou, aby odesílaly data monitorování a diagnostiky do tabulky úložiště nebo jinde:
  * [Provozní události](service-fabric-diagnostics-event-generation-operational.md): operace vyšší úrovně, které provádí Service Fabric platforma. Mezi příklady patří vytváření aplikací a služeb, změny stavu uzlu a informace o upgradu. Ty se generují jako protokoly ETW (Event Tracing for Windows).
  * [Reliable Actors události programovacího modelu](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services události programovacího modelu](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Nasazení diagnostického rozšíření prostřednictvím portálu
Prvním krokem při shromažďování protokolů je nasazení diagnostického rozšíření na uzlech sady škálování virtuálních počítačů v clusteru Service Fabric. Diagnostické rozšíření shromažďuje protokoly na každém virtuálním počítači a nahrává je do účtu úložiště, který zadáte. Následující kroky popisují, jak tuto akci provést pro nové a existující clustery prostřednictvím šablon Azure Portal a Azure Resource Manager.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Nasazení diagnostického rozšíření v rámci vytváření clusteru pomocí Azure Portal
Při vytváření clusteru rozbalte v kroku konfigurace clusteru volitelná nastavení a ujistěte se, že je diagnostika nastavená na **zapnuto** (výchozí nastavení).

![Nastavení Azure Diagnostics na portálu pro vytvoření clusteru](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Důrazně doporučujeme, abyste si šablonu stáhli předtím, než v posledním kroku **kliknete na vytvořit** . Podrobnosti najdete v tématu [nastavení Service Fabricho clusteru pomocí Azure Resource Manager šablony](service-fabric-cluster-creation-via-arm.md). Potřebujete šablonu k provedení změn v tom, jaké kanály (uvedené výše) mají shromažďovat data.

![Šablona clusteru](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Teď, když agregujete události v Azure Storage, můžete [nastavit protokoly Azure monitor](service-fabric-diagnostics-oms-setup.md) a získat přehledy a dotazovat je na portálu Azure monitor Logs.

>[!NOTE]
>V současné době neexistuje způsob, jak filtrovat nebo odfiltrovat události, které jsou odesílány do tabulek. Pokud neimplementujete proces pro odebrání událostí z tabulky, bude se tabulka dál zvětšovat (výchozí limit je 50 GB). Pokyny, jak to změnit, najdete [dál v tomto článku](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Kromě toho existuje příklad služby výmazu dat spuštěného v rámci [sledovacího](https://github.com/Azure-Samples/service-fabric-watchdog-service)procesu a doporučujeme, abyste si sami napsali, a to i v případě, že nebudete mít dobrý důvod na uložení protokolů mimo časový rámec 30 nebo 90 dnů.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Nasadit rozšíření diagnostiky prostřednictvím Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Vytvoření clusteru s diagnostickým rozšířením
Chcete-li vytvořit cluster pomocí Správce prostředků, je nutné přidat kód JSON konfigurace diagnostiky do úplné Správce prostředků šablony. V rámci našich ukázek šablon Správce prostředků poskytujeme ukázkovou šablonu Správce prostředků clusteru s pěti virtuálními počítači, které se do ní přidaly pomocí konfigurace diagnostiky. Můžete ji zobrazit v tomto umístění v galerii ukázek Azure: [cluster s pěti uzly s diagnostikou správce prostředků šablony ukázka](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Chcete-li zobrazit nastavení diagnostiky v šabloně Správce prostředků, otevřete azuredeploy.jsv souboru a vyhledejte **IaaSDiagnostics**. Pokud chcete vytvořit cluster pomocí této šablony, vyberte tlačítko **nasadit do Azure** , které je k dispozici na předchozím odkazu.

Alternativně můžete stáhnout ukázku Správce prostředků, provést v ní změny a vytvořit cluster s upravenou šablonou pomocí `New-AzResourceGroupDeployment` příkazu v okně Azure PowerShell. Podívejte se na následující kód parametrů, které předáte do příkazu. Podrobné informace o tom, jak nasadit skupinu prostředků pomocí PowerShellu, najdete v článku [nasazení skupiny prostředků pomocí šablony Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Přidání rozšíření diagnostiky do existujícího clusteru
Pokud máte existující cluster, který nemá nasazenou diagnostiku, můžete ho přidat nebo aktualizovat prostřednictvím šablony clusteru. Upravte Správce prostředků šablonu, která se používá k vytvoření existujícího clusteru, nebo Stáhněte šablonu z portálu, jak je popsáno výše. Upravte template.jsv souboru prováděním následujících úloh:

Přidejte do šablony nový prostředek úložiště přidáním do části Resources (prostředky).

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 V dalším kroku přidejte do oddílu Parameters hned za definice účtu úložiště mezi `supportLogStorageAccountName` . Nahraďte zástupný text *název účtu úložiště* místo pro název účtu úložiště, který chcete.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Pak aktualizujte `VirtualMachineProfile` část template.jsv souboru přidáním následujícího kódu do pole rozšíření. Nezapomeňte přidat čárku na začátek nebo konec, v závislosti na tom, kde je vložena.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Po úpravě template.jsv souboru, jak je popsáno, znovu publikujte šablonu Správce prostředků. Pokud byla šablona exportována, spuštěním souboru deploy.ps1 šablonu znovu publikujte. Po nasazení zajistěte, aby byla **ProvisioningState** **úspěšná**.

> [!TIP]
> Pokud budete do svého clusteru nasazovat kontejnery, povolte WAD, abyste si vybrali statistiku Docker tak, že ji přidáte do oddílu **WadCfg > DiagnosticMonitorConfiguration** .

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},
```

### <a name="update-storage-quota"></a>Aktualizovat kvótu úložiště

Vzhledem k tomu, že tabulky naplněné rozšířením roste až do dosažení kvóty, možná budete chtít zvážit snížení velikosti kvóty. Výchozí hodnota je 50 GB a v šabloně se dá nakonfigurovat pod `overallQuotaInMB` polem `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Konfigurace shromažďování protokolů
Protokoly z dalších kanálů jsou k dispozici také pro kolekci. zde jsou některé nejběžnější konfigurace, které můžete vytvořit v šabloně pro clustery běžící v Azure.

* Provozní kanál – základ: ve výchozím nastavení povolená operace vysoké úrovně, které provádí Service Fabric a cluster, včetně událostí pro uzel, který se připravuje, nově nasazené aplikace nebo vrácení upgradu, atd. Seznam událostí najdete v tématu [události provozního kanálu](./service-fabric-diagnostics-event-generation-operational.md).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Provozní kanál – podrobné: zahrnuje sestavy o stavu a rozhodnutí o vyrovnávání zatížení a také vše v základním provozním kanálu. Tyto události jsou generovány systémem nebo vaším kódem pomocí rozhraní API pro vytváření sestav stavu nebo načítání, jako je například [ReportPartitionHealth](/previous-versions/azure/reference/mt645153(v=azure.100)) nebo [ReportLoad](/previous-versions/azure/reference/mt161491(v=azure.100)). Chcete-li zobrazit tyto události v diagnostickém Prohlížeč událostí sady Visual Studio, přidejte do seznamu zprostředkovatelů ETW "Microsoft-ServiceFabric: 4:0x4000000000000008".

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Kanál dat a zasílání zpráv – základní: kritické protokoly a události generované v zasílání zpráv (aktuálně jenom ReverseProxy) a data a cesty k podrobným protokolům operačního kanálu. Tyto události jsou selhání zpracování požadavků a další kritické problémy v ReverseProxy a také zpracovávané požadavky. **Toto je naše doporučení pro komplexní protokolování**. Chcete-li zobrazit tyto události v diagnostickém Prohlížeč událostí sady Visual Studio, přidejte do seznamu zprostředkovatelů ETW "Microsoft-ServiceFabric: 4:0x4000000000000010".

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Kanál data & zasílání zpráv – podrobná: podrobný kanál, který obsahuje všechny nekritické protokoly z dat a zasílání zpráv v clusteru a podrobný provozní kanál. Podrobný Poradce při potížích se všemi událostmi reverzního proxy serveru najdete v [průvodci diagnostikou reverzního proxy](service-fabric-reverse-proxy-diagnostics.md).  Chcete-li zobrazit tyto události v prohlížeči diagnostických událostí aplikace Visual Studio, přidejte do seznamu zprostředkovatelů ETW "Microsoft-ServiceFabric: 4:0x4000000000000020".

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Tento kanál má velmi velký objem událostí. díky tomu shromažďování událostí z tohoto podrobného kanálu vede k rychlému generování mnoha trasování a může spotřebovávat kapacitu úložiště. Tuto funkci zapněte pouze v případě, že je nezbytně nutné.


Aby bylo možné **základní provozní kanál** povolit pro komplexní protokolování s minimálním objemem hluku, bude `EtwManifestProviderConfiguration` v `WadCfg` šabloně vypadat jako v následujícím příkladu:

```json
  "WadCfg": {
        "DiagnosticMonitorConfiguration": {
          "overallQuotaInMB": "50000",
          "EtwProviders": {
            "EtwEventSourceProviderConfiguration": [
              {
                "provider": "Microsoft-ServiceFabric-Actors",
                "scheduledTransferKeywordFilter": "1",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableActorEventTable"
                }
              },
              {
                "provider": "Microsoft-ServiceFabric-Services",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableServiceEventTable"
                }
              }
            ],
            "EtwManifestProviderConfiguration": [
              {
                "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Shromáždit z nových kanálů EventSource

Chcete-li aktualizovat diagnostiku pro shromažďování protokolů z nových kanálů EventSource, které reprezentují novou aplikaci, kterou se chystáte nasadit, proveďte stejný postup, jak je popsáno výše v tématu nastavení diagnostiky pro existující cluster.

Aktualizujte `EtwEventSourceProviderConfiguration` část template.jsv souboru, abyste mohli přidat položky pro nové kanály EventSource předtím, než použijete aktualizaci konfigurace pomocí `New-AzResourceGroupDeployment` příkazu PowerShellu. Název zdroje události je definován jako součást kódu v souboru ServiceEventSource. cs generovaného v rámci sady Visual Studio.

Pokud se například váš zdroj události jmenuje my-EventSource, přidejte následující kód, který umístí události z My-Eventsource do tabulky s názvem MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Chcete-li shromáždit čítače výkonu nebo protokoly událostí, upravte šablonu Správce prostředků pomocí příkladů uvedených v části [Vytvoření virtuálního počítače s Windows pomocí monitorování a diagnostiky pomocí Azure Resource Manager šablony](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json). Pak šablonu Správce prostředků znovu publikujte.

## <a name="collect-performance-counters"></a>Shromáždit čítače výkonu

Pokud chcete shromáždit metriky výkonu z clusteru, přidejte čítače výkonu do složky "WadCfg > DiagnosticMonitorConfiguration" v šabloně Správce prostředků pro váš cluster. V tématu [monitorování výkonu pomocí wad](service-fabric-diagnostics-perf-wad.md) najdete postup úpravy `WadCfg` a shromažďování konkrétních čítačů výkonu. Referenční informace [Service Fabric čítače výkonu](service-fabric-diagnostics-event-generation-perf.md) pro seznam čítačů výkonu, které doporučujeme shromažďovat.
  
Pokud používáte Application Insights jímka, jak je popsáno v následující části, a chcete, aby se tyto metriky zobrazovaly v Application Insights, nezapomeňte přidat název jímky do části "jímky", jak je uvedeno výše. Tím se automaticky odešlou čítače výkonu, které jsou pro váš Application Insights prostředek individuálně nakonfigurované.


## <a name="send-logs-to-application-insights"></a>Odeslat protokoly do Application Insights

### <a name="configuring-application-insights-with-wad"></a>Konfigurace Application Insights pomocí WAD

>[!NOTE]
>To platí jenom pro clustery s Windows.

Existují dva základní způsoby, jak odesílat data z WAD do Azure Application Insights, což se dosahuje přidáním jímky Application Insights do konfigurace WAD, prostřednictvím Azure Portal nebo prostřednictvím šablony Azure Resource Manager.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Přidání klíče instrumentace Application Insights při vytváření clusteru v Azure Portal

![Přidání AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Pokud je při vytváření clusteru zapnutá Diagnostika zapnutá, volitelné pole, které zadáte Application Insights klíč instrumentace, se zobrazí. Pokud sem vložíte svůj Application Insights klíč, Application Insights jímka se automaticky nakonfiguruje v šabloně Správce prostředků, která se používá k nasazení clusteru.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Přidání jímky Application Insights do šablony Správce prostředků

V části "WadCfg" šablony Správce prostředků přidejte "jímku", včetně následujících dvou změn:

1. Přidejte konfiguraci jímky přímo po `DiagnosticMonitorConfiguration` dokončení deklarace:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Zahrňte jímku do v rozhraní `DiagnosticMonitorConfiguration` přidáním následujícího řádku do části `DiagnosticMonitorConfiguration` `WadCfg` (napravo před `EtwProviders` deklarací jsou deklarovány):

    ```json
    "sinks": "applicationInsights"
    ```

V předchozích fragmentech kódu byl použit název "applicationInsights" k popisu jímky. Nejedná se o požadavek a pokud je název jímky obsažený v části "jímky", můžete nastavit název libovolného řetězce.

V současné době se protokoly z clusteru zobrazují jako **trasování** v prohlížeči protokolu Application Insights. Vzhledem k tomu, že většina trasování přicházejících z platformy je úrovně "informativní", můžete také zvážit změnu konfigurace jímky jenom na Odeslat protokoly typu "upozornění" nebo "Chyba". To lze provést přidáním "kanálů" do jímky, jak je znázorněno v [tomto článku](../azure-monitor/agents/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Pokud používáte nesprávný Application Insights klíč buď na portálu, nebo v šabloně Správce prostředků, budete muset ručně změnit klíč a aktualizovat cluster nebo ho znovu nasadit.

## <a name="next-steps"></a>Další kroky

Po správném nakonfigurování diagnostiky Azure se v tabulkách úložiště zobrazí data z protokolů ETW a EventSource. Pokud se rozhodnete použít protokoly Azure Monitor Kibana nebo jakoukoli jinou platformu pro analýzu dat a vizualizaci, která není přímo nakonfigurovaná v šabloně Správce prostředků, ujistěte se, že jste si nastavili platformu, kterou si zvolíte pro čtení dat z těchto tabulek úložiště. V případě Azure Monitor protokolů je poměrně triviální a je vysvětleno v tématu [Analýza událostí a protokolů](service-fabric-diagnostics-event-analysis-oms.md). Application Insights je v tomto smyslu trochu speciální případ, protože může být nakonfigurovaný jako součást konfigurace rozšíření diagnostiky, takže pokud se rozhodnete použít AI, přečtěte si [příslušný článek](service-fabric-diagnostics-event-analysis-appinsights.md) .

>[!NOTE]
>V současné době neexistuje způsob, jak filtrovat nebo povýmazovat události, které se odesílají do tabulky. Pokud neimplementujete proces odebrání událostí z tabulky, bude tabulka dál rozrůstat. V současné době existuje příklad služby výmazu dat spuštěného v rámci [sledovacího](https://github.com/Azure-Samples/service-fabric-watchdog-service)procesu a doporučujeme, abyste si sami napsali, pokud nemůžete mít dobrý důvod ukládat protokoly nad rámec 30 nebo 90 dne.

* [Naučte se shromažďovat čítače výkonu nebo protokoly pomocí rozšíření diagnostiky.](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)
* [Analýza a vizualizace událostí pomocí Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analýza a vizualizace událostí pomocí protokolů Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Analýza a vizualizace událostí pomocí Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analýza a vizualizace událostí pomocí protokolů Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
