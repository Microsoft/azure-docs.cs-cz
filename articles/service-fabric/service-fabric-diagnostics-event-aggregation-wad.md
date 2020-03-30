---
title: Agregace událostí pomocí diagnostiky Windows Azure
description: Další informace o agregaci a shromažďování událostí pomocí WAD pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: b9a448ff41c66fa3a38c124f7acde062bacbe9ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282494"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregace a shromažďování událostí pomocí diagnostiky Windows Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Když používáte cluster Azure Service Fabric, je vhodné shromažďovat protokoly ze všech uzlů v centrálním umístění. S protokoly v centrálním umístění vám pomůže analyzovat a řešit problémy v clusteru nebo problémy v aplikacích a službách spuštěných v tomto clusteru.

Jedním ze způsobů, jak nahrávat a shromažďovat protokoly, je použití rozšíření Diagnostika Windows Azure (WAD), které nahrává protokoly do Služby Azure Storage a má také možnost odesílat protokoly do Azure Application Insights nebo Event Hubs. Můžete také použít externí proces ke čtení událostí z úložiště a umístit je do produktu [platformy analýzy,](../log-analytics/log-analytics-service-fabric.md) jako jsou protokoly Azure Monitor nebo jiné řešení analýzy protokolů.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
V tomto článku se používají následující nástroje:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Šablona Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Události platformy Service Fabric
Service Fabric vás nastaví s několika [out-of-the-box kanálů protokolování](service-fabric-diagnostics-event-generation-infra.md), z nichž následující kanály jsou předem nakonfigurovány s rozšířením pro odesílání dat monitorování a diagnostiky do tabulky úložiště nebo jinde:
  * [Provozní události](service-fabric-diagnostics-event-generation-operational.md): operace vyšší úrovně, které provádí platforma Service Fabric. Mezi příklady patří vytváření aplikací a služeb, změny stavu uzlu a informace o upgradu. Ty jsou vydávány jako protokoly trasování událostí pro Windows (ETW)
  * [Události programovacího modelu spolehlivých herců](service-fabric-reliable-actors-diagnostics.md)
  * [Události programovacího modelu spolehlivých služeb](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Nasazení rozšíření Diagnostika prostřednictvím portálu
Prvním krokem při shromažďování protokolů je nasazení rozšíření Diagnostika na uzlech škálovací sady virtuálních strojů v clusteru Service Fabric. Rozšíření Diagnostika shromažďuje protokoly na každém virtuálním počítači a odešle je do účtu úložiště, který zadáte. Následující kroky popisují, jak toho dosáhnout pro nové a existující clustery prostřednictvím portálu Azure a šablony Azure Resource Manager.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Nasazení rozšíření Diagnostika jako součást vytváření clusteru prostřednictvím portálu Azure
Při vytváření clusteru rozbalte v kroku konfigurace clusteru volitelná nastavení a ujistěte se, že je diagnostika nastavena **na zapnuto** (výchozí nastavení).

![Nastavení diagnostiky Azure na portálu pro vytváření clusterů](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Důrazně doporučujeme stáhnout šablonu **před kliknutím na vytvořit** v posledním kroku. Podrobnosti najdete v části [Nastavení clusteru Service Fabric pomocí šablony Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Šablonu potřebujete k provádění změn na jakých kanálech (uvedených výše) pro shromažďování dat.

![Šablona clusteru](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Teď, když agregujete události ve Službě Azure Storage, [nastavte protokoly Azure Monitoru,](service-fabric-diagnostics-oms-setup.md) abyste získali přehledy a dotazovali se jich na portálu protokolů Azure Monitoru.

>[!NOTE]
>V současné době neexistuje žádný způsob, jak filtrovat nebo ženich události, které jsou odesílány do tabulek. Pokud neimplementujete proces odebrání událostí z tabulky, tabulka bude nadále růst (výchozí limit je 50 GB). Pokyny, jak to změnit, jsou [dále v tomto článku](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Kromě toho je příklad služby péče o data spuštěné v [watchdog vzorku](https://github.com/Azure-Samples/service-fabric-watchdog-service)a je doporučeno napsat jeden pro sebe také, pokud existuje dobrý důvod pro uložení protokolů za časový rámec 30 nebo 90 dnů.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Nasazení rozšíření Diagnostika prostřednictvím Správce prostředků Azure

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Vytvoření clusteru s rozšířením diagnostiky
Chcete-li vytvořit cluster pomocí Správce prostředků, je třeba přidat konfiguraci JSON diagnostiky do úplné šablony Správce prostředků. Poskytujeme ukázkovou šablonu Správce prostředků clusteru pěti virtuálních počítačů s přidanou konfigurací Diagnostiky jako součást ukázek šablon Správce prostředků. Můžete vidět na tomto místě v galerii Ukázky Azure: [Cluster pěti uzlů s ukázkou šablony Správce prostředků diagnostiky](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Nastavení Diagnostika v šabloně Správce prostředků zobrazíte v souboru azuredeploy.json a vyhledejte **iaaSDiagnostics**. Chcete-li vytvořit cluster pomocí této šablony, vyberte tlačítko **Nasazení do Azure, které je** k dispozici na předchozím odkazu.

Případně můžete stáhnout ukázku Správce prostředků, provést v ní změny a vytvořit cluster `New-AzResourceGroupDeployment` s upravenou šablonou pomocí příkazu v okně Azure PowerShell. Naleznete v následujícím kódu parametry, které předáte příkazu. Podrobné informace o tom, jak nasadit skupinu prostředků pomocí PowerShellu, najdete v článku [Nasazení skupiny prostředků pomocí šablony Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Přidání rozšíření diagnostiky do existujícího clusteru
Pokud máte existující cluster, ve kterých není nasazena diagnostika, můžete jej přidat nebo aktualizovat prostřednictvím šablony clusteru. Upravte šablonu Správce prostředků, která se používá k vytvoření existujícího clusteru nebo ke stažení šablony z portálu, jak je popsáno výše. Upravte soubor template.json provedením následujících úkolů:

Přidejte do šablony nový prostředek úložiště přidáním do oddílu prostředků.

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

 Dále přidejte do části parametry hned za definice `supportLogStorageAccountName`účtu úložiště, mezi . Nahraďte *zástupný název účtu textového úložiště sem* s názvem účtu úložiště, který chcete.

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
Potom aktualizujte `VirtualMachineProfile` část souboru template.json přidáním následujícího kódu v rámci pole rozšíření. Nezapomeňte přidat čárku na začátku nebo na konec, v závislosti na tom, kam je vložena.

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

Po úpravě souboru template.json, jak je popsáno, znovu publikujte šablonu Správce prostředků. Pokud byla šablona exportována, spuštění souboru deploy.ps1 šablonu znovu publikuje. Po nasazení se ujistěte, že **zřizováníState** je **úspěšné**.

> [!TIP]
> Pokud se chystáte nasadit kontejnery do clusteru, povolte WAD vyzvednout docker statistiky přidáním tohoto do oddílu **WadCfg > DiagnosticMonitorConfiguration.**
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Aktualizovat kvótu úložiště

Vzhledem k tomu, že tabulky nabyté rozšířením se zvětšují, dokud není kvóta přístupová, můžete zvážit snížení velikosti kvóty. Výchozí hodnota je 50 GB a je konfigurovatelná v šabloně pod polem `overallQuotaInMB` pod`DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Konfigurace kolekce protokolů
Protokoly z dalších kanálů jsou také k dispozici pro shromažďování, tady jsou některé z nejběžnějších konfigurací, které můžete provést v šabloně pro clustery spuštěné v Azure.

* Provozní kanál – základna: Ve výchozím nastavení povolena operace na vysoké úrovni prováděné service fabric a clusteru, včetně událostí pro uzel přichází, nové aplikace se nasazuje nebo upgrade vrácení zpět, atd. Seznam událostí naleznete v části [Události operačního kanálu](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Provozní kanál – podrobný: To zahrnuje zprávy o stavu a rozhodnutí o vyrovnávání zatížení a vše, co je v základním provozním kanálu. Tyto události jsou generovány systémem nebo kód pomocí stav nebo zatížení vykazování api, jako je [Například ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) nebo [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Chcete-li zobrazit tyto události v Prohlížeči diagnostických událostí sady Visual Studio, přidejte do seznamu zprostředkovatelů ETW "Microsoft-ServiceFabric:4:0x4000000000000000000000.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Kanál dat a zpráv - Base: Důležité protokoly a události generované v zasílání zpráv (aktuálně pouze ReverseProxy) a datové cestě, kromě podrobných protokolů provozních kanálů. Tyto události jsou selhání zpracování požadavků a další kritické problémy v ReverseProxy, stejně jako zpracované požadavky. **Toto je naše doporučení pro komplexní protokolování**. Chcete-li zobrazit tyto události v Prohlížeči diagnostických událostí sady Visual Studio, přidejte do seznamu zprostředkovatelů ETW "Microsoft-ServiceFabric:4:0x4000000000000000000010".

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Kanál & zasílání zpráv dat – podrobný: Podrobný kanál, který obsahuje všechny nekritické protokoly z dat a zpráv v clusteru a podrobný provozní kanál. Podrobné informace o řešení všech reverzních událostí proxy naleznete v [průvodci diagnostikou reverzního proxy serveru](service-fabric-reverse-proxy-diagnostics.md).  Chcete-li zobrazit tyto události v prohlížeči diagnostických událostí sady Visual Studio, přidejte do seznamu zprostředkovatelů ETW "Microsoft-ServiceFabric:4:0x4000000000000000020".

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Tento kanál má velmi velký objem událostí, povolení shromažďování událostí z tohoto podrobného kanálu má za následek velké množství trasování jsou generovány rychle a může spotřebovat kapacitu úložiště. Zapněte to pouze v případě, že je to nezbytně nutné.


Chcete-li povolit **základní operační kanál** naše doporučení pro `EtwManifestProviderConfiguration` komplexní `WadCfg` protokolování s nejmenším množstvím hluku, v šabloně bude vypadat takto:

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

## <a name="collect-from-new-eventsource-channels"></a>Sbírat z nových kanálů EventSource

Chcete-li aktualizovat diagnostiku pro shromažďování protokolů z nových kanálů EventSource, které představují novou aplikaci, kterou se chystáte nasadit, proveďte stejné kroky, které byly popsány pro nastavení diagnostiky pro existující cluster.

Před `EtwEventSourceProviderConfiguration` použitím příkazu `New-AzResourceGroupDeployment` PowerShell aktualizujte oddíl v souboru template.json a přidejte položky pro nové kanály EventSource. Název zdroje událostí je definován jako součást kódu v souboru ServiceEventSource.cs generovaném visual studio.

Pokud má například zdroj události název My-Eventsource, přidejte následující kód pro umístění událostí z zdroje my-eventsource do tabulky s názvem MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Chcete-li shromažďovat čítače výkonu nebo protokoly událostí, upravte šablonu Správce prostředků pomocí příkladů uvedených v části [Vytvoření virtuálního počítače s Windows s monitorováním a diagnostikou pomocí šablony Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Potom znovu publikujte šablonu Správce prostředků.

## <a name="collect-performance-counters"></a>Sbírat čítače výkonu

Chcete-li shromažďovat metriky výkonu z clusteru, přidejte čítače výkonu do "WadCfg > DiagnosticMonitorConfiguration" v šabloně Správce prostředků pro váš cluster. Postup úpravy `WadCfg` čítačů výkonu najdete v [tématu Sledování výkonu pomocí nástroje WAD.](service-fabric-diagnostics-perf-wad.md) [Reference Service Fabric čítače výkonu](service-fabric-diagnostics-event-generation-perf.md) pro seznam čítačů výkonu, které doporučujeme shromažďovat.
  
Pokud používáte jímky Application Insights, jak je popsáno v části níže a chcete, aby se tyto metriky zobrazovaly v Application Insights, nezapomeňte přidat název jímky v části "jímky", jak je znázorněno výše. Tím se automaticky odešlou čítače výkonu, které jsou individuálně nakonfigurovány pro váš prostředek Application Insights.


## <a name="send-logs-to-application-insights"></a>Odeslání protokolů do přehledů aplikací

### <a name="configuring-application-insights-with-wad"></a>Konfigurace přehledů aplikací pomocí služby WAD

>[!NOTE]
>To platí pouze pro clustery systému Windows v současné době.

Existují dva primární způsoby odesílání dat z WAD do Azure Application Insights, což je dosaženo přidáním jímky Application Insights do konfigurace WAD, prostřednictvím portálu Azure nebo prostřednictvím šablony Azure Resource Manager.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Přidání klíče instrumentace Přehledy aplikací při vytváření clusteru na webu Azure Portal

![Přidání klíče AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Při vytváření clusteru, pokud je diagnostika zapnuta "Zapnuto", zobrazí se volitelné pole pro zadání klíče Instrumentace Přehledy aplikací. Pokud sem vložíte klíč Application Insights, jímka Application Insights se automaticky nakonfiguruje v šabloně Správce prostředků, která se používá k nasazení clusteru.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Přidání jímky Přehledů aplikací do šablony Správce prostředků

V šabloně "WadCfg" správce prostředků přidejte "Umyvadlo" zahrnutím následujících dvou změn:

1. Přidejte konfiguraci jímky bezprostředně `DiagnosticMonitorConfiguration` po dokončení deklarování je dokončena:

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

2. Zahrnout jímky `DiagnosticMonitorConfiguration` v přidáním následující `DiagnosticMonitorConfiguration` řádek `WadCfg` v (right `EtwProviders` před jsou deklarovány):

    ```json
    "sinks": "applicationInsights"
    ```

V obou předchozích fragmentů kódu byl název "applicationInsights" použit k popisu jímky. To to není požadavek a tak dlouho, dokud název jímky je součástí "jímky", můžete nastavit název libovolný řetězec.

V současné době protokoly z clusteru zobrazit jako trasování v prohlížeči protokolu Application Insights.Currently, logs from the cluster show up as **traces** in Application Insights' log viewer. Vzhledem k tomu, že většina trasování pocházejících z platformy jsou na úrovni "Informační", můžete také zvážit změnu konfigurace jímky pouze odeslat protokoly typu "Upozornění" nebo "Chyba". To lze provést přidáním "Kanály" do jímky, jak je znázorněno v [tomto článku](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Pokud použijete nesprávný klíč Application Insights buď na portálu, nebo v šabloně Správce prostředků, budete muset ručně změnit klíč a aktualizovat cluster / znovu jej nasadit.

## <a name="next-steps"></a>Další kroky

Po správné konfiguraci diagnostiky Azure, uvidíte data v tabulkách úložiště z protokolů ETW a EventSource. Pokud se rozhodnete použít protokoly Azure Monitor, Kibana nebo jakoukoli jinou analýzu dat a vizualizační platformu, která není přímo nakonfigurovaná v šabloně Správce prostředků, ujistěte se, že nastavíte platformu podle vašeho výběru pro čtení v datech z těchto tabulek úložiště. To pro protokoly Azure Monitor je relativně triviální a je vysvětleno v [event a log analýzy](service-fabric-diagnostics-event-analysis-oms.md). Application Insights je trochu zvláštní případ v tomto smyslu, protože jej lze nakonfigurovat jako součást konfigurace rozšíření diagnostiky, proto se podívejte na [příslušný článek,](service-fabric-diagnostics-event-analysis-appinsights.md) pokud se rozhodnete použít AI.

>[!NOTE]
>V současné době neexistuje žádný způsob, jak filtrovat nebo ženich události, které jsou odeslány do tabulky. Pokud neimplementujete proces odebrání událostí z tabulky, tabulka bude nadále růst. V současné době je příklad služby péče o data spuštěné v [ukázce Watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service)a doporučuje se napsat jeden pro sebe také, pokud existuje dobrý důvod pro uložení protokolů za časový rámec 30 nebo 90 dnů.

* [Zjistěte, jak shromažďovat čítače výkonu nebo protokoly pomocí rozšíření Diagnostika](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Analýza událostí a vizualizace s přehledy aplikací](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analýza událostí a vizualizace pomocí protokolů Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Analýza událostí a vizualizace s přehledy aplikací](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analýza událostí a vizualizace pomocí protokolů Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
