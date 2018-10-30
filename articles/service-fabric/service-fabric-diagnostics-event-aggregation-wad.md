---
title: Agregace událostí infrastruktury služby Azure s Windows Azure Diagnostics | Dokumentace Microsoftu
description: Další informace o agregaci a shromažďování událostí pomocí WAD pro monitorování a diagnostiku clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: d670b90404d441876727336fc50a848965082de5
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232490"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregace událostí a kolekce pomocí Windows Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Když používáte cluster Azure Service Fabric, je vhodné pro shromažďování protokolů ze všech uzlů v centrálním umístění. S protokoly v centrálním umístění vám pomáhají analyzovat a řešit problémy ve vašem clusteru nebo problémy v aplikace a služby běžící v tomto clusteru.

Jeden způsob, jak nahrát a shromažďovat protokoly je použít rozšíření Windows Azure Diagnostics (WAD), nahraje protokoly do služby Azure Storage, který má také možnost odeslat protokoly do služby Azure Application Insights nebo Center událostí. Externí proces lze také použít ke čtení události ze služby storage a umístit je do o produkt poskytovaný analýzy platformy, jako [Log Analytics](../log-analytics/log-analytics-service-fabric.md) nebo jiné řešení analýzy protokolů.

## <a name="prerequisites"></a>Požadavky
Tyto nástroje se používají v tomto článku:

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Šablona Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Události platformy Service Fabric
Service Fabric nastaví můžete s několika [out-of-the-box protokolování kanály](service-fabric-diagnostics-event-generation-infra.md), které jsou k dispozici následující kanály jsou předem nakonfigurovaným rozhraním rozšíření k odeslání, sledování a diagnostická data do tabulky úložiště, nebo jinde:
  * [Provozní události](service-fabric-diagnostics-event-generation-operational.md): Chcete získat podrobnější popis operace, které provádí platformu Service Fabric. Mezi příklady patří vytváření aplikací a služeb, změny stavu uzlu a informace o upgradu. Tyto jsou emitovány jako protokoly trasování událostí pro Windows (ETW)
  * [Programovacího modelu Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Programovacího modelu Reliable Services](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Nasazení rozšíření diagnostiky na portálu
Prvním krokem při shromažďování protokolů je k nasazení rozšíření diagnostiky na uzlech škálovací sady virtuálního počítače v clusteru Service Fabric. Rozšíření diagnostiky shromažďuje protokoly na každém virtuálním počítači a odesílá je do účtu úložiště, který zadáte. Následující kroky popisují, jak to provést pro nové i stávající clusterů pomocí webu Azure portal a šablon Azure Resource Manageru.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Nasazení rozšíření diagnostiky jako součást vytvoření clusteru pomocí webu Azure portal
Při vytváření clusteru, v kroku konfigurace clusteru, rozbalte volitelná nastavení a ujistěte se, že Diagnostika je nastavena na **na** (výchozí nastavení).

![Nastavení Azure Diagnostics na portálu pro vytváření clusteru](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Důrazně doporučujeme, stáhněte si šablonu **před kliknutím na vytvořit** v posledním kroku. Podrobnosti najdete v [nastavení clusteru Service Fabric pomocí šablony Azure Resource Manageru](service-fabric-cluster-creation-via-arm.md). Je nutné šablonu, kterou chcete provést změny na jaké kanálů (jsou uvedené výše) pro shromažďování dat z.

![Šablony clusteru](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Teď, když máte agregaci událostí ve službě Azure Storage [nastavení Log Analytics](service-fabric-diagnostics-oms-setup.md) k získání přehledu a jejich dotazování na portálu Log Analytics

>[!NOTE]
>Aktuálně neexistuje žádný způsob, jak filtrovat nebo mazání událostí, které se odesílají do tabulky. Pokud implementujete není procesu odebrání události z tabulky, bude dál rozšiřovat tabulky (výchozí limit je 50 GB). Návod, jak změnit jsou [níže v tomto článku](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Kromě toho je příkladem data výmazu dat služby spuštěné [sledovacích ukázka](https://github.com/Azure-Samples/service-fabric-watchdog-service), a je doporučeno, že napíšete nějakou, pokud není dobrý důvod pro ukládání protokolů nad rámec 30 nebo 90 den časový rámec.

## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Nasazení rozšíření diagnostiky prostřednictvím Azure Resource Manageru

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Vytvoření clusteru s diagnostickým rozšířením
K vytvoření clusteru pomocí Resource Manageru, musíte přidat konfiguraci diagnostiky JSON do úplné šablony Resource Manageru, před vytvořením clusteru. Poskytujeme Ukázka šablony Resource Manageru cluster pět virtuálních počítačů s konfiguraci diagnostiky, které jsou přidány jako součást naší ukázkové šablony Resource Manageru. Zobrazí se na této pozici ve Galerii ukázek Azure: [cluster s pěti uzly s ukázkou šablony Resource Manageru diagnostiky](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Pokud chcete zobrazit nastavení diagnostiky v šabloně Resource Manageru, otevřete soubor azuredeploy.json a vyhledejte **IaaSDiagnostics**. Pokud chcete vytvořit cluster pomocí této šablony, vyberte **nasadit do Azure** tlačítko, které jsou k dispozici na na předchozí odkaz.

Alternativně můžete stáhnout ukázku Resource Manageru, provádět změny a vytvořit cluster s upravenou šablonu pomocí `New-AzureRmResourceGroupDeployment` příkazu v okně Azure PowerShell. Viz následující kód pro parametry, které je předáno příkazu. Podrobné informace o tom, jak nasadit skupinu prostředků pomocí Powershellu najdete v článku [nasazení skupiny prostředků pomocí šablony Azure Resource Manageru](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Přidat diagnostické rozšíření do existujícího clusteru
Pokud máte existující cluster, který nemá diagnostiky nasazený, můžete přidat nebo aktualizovat prostřednictvím šablona clusteru. Upravte šablonu Resource Manageru, který se používá k vytvoření stávajícího clusteru nebo stáhněte šablonu z portálu, jak je popsáno výše. Upravte soubor template.json provedením následujících úloh:

Přidáte nový prostředek úložiště tak, že přidáte do části prostředky k šabloně.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 V dalším kroku přidejte do části parametrů ihned za definici účtu úložiště mezi `supportLogStorageAccountName`. Nahraďte zástupný text *místo pro název účtu úložiště* s názvem účtu úložiště, který chcete.

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
Potom aktualizujte `VirtualMachineProfile` část soubor template.json přidáním následujícího kódu v rámci pole přípon. Ujistěte se, že přidejte čárku na začátku nebo konci, v závislosti na tom, kde je vložen.

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

Když upravíte soubor template.json, jak je popsáno, znovu publikujte šablony Resource Manageru. Pokud jste exportovali šablonu, spuštěním deploy.ps1 souboru znovu publikuje uzamkl šablony. Po nasazení, ujistěte se, že **ProvisioningState** je **Succeeded**.

> [!TIP]
> Pokud se chystáte nasazení kontejnerů do clusteru, povolte WAD ke sbírání statistiky dockeru tak, že přidáte tuto hodnotu na vaše **WadCfg > DiagnosticMonitorConfiguration** oddílu.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Aktualizace kvóty úložiště

Od tabulky vyplněn rozšíření roste až do dosažení této kvóty, můžete chtít zvážit snížení velikosti kvóty. Výchozí hodnota je 50 GB a je možné konfigurovat v šabloně v části `overallQuotainMB` pole `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Konfigurace shromažďování protokolů
Protokoly z další kanály jsou také k dispozici pro kolekci, tady jsou některé z nejběžnějších konfiguracích, které provedete v šabloně pro clustery spuštěné v Azure.

* Provozní kanál - Base: Povolená ve výchozím nastavení, vysoké úrovně operací provedených metodou Service Fabric a clusteru, včetně událostí pro uzel naráželi, nové aplikace nasazuje nebo upgradu vrácení zpět, atd. Seznam událostí, najdete v tématu [provozní události kanálu](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Provozní kanál – podrobné: Jedná se o sestav o stavu a vyrovnávání zátěže rozhodnutí plus všechno, co v základní provozní kanál. Tyto události jsou generovány v systému nebo kódu s použitím stavu nebo načíst rozhraní API pro generování sestav, jako [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) nebo [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Chcete-li zobrazit tyto události v prohlížeči diagnostických událostí v sadě Visual Studio přidejte "Microsoft-ServiceFabric:4:0x4000000000000008" do seznamu zprostředkovatelů trasování událostí pro Windows.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Data a zasílání zpráv v kanálu - Base: kritické protokolů a událostí vygenerovaných při zasílání zpráv (aktuálně pouze ReverseProxy) a cesta k datům, kromě do protokolů podrobné provozní kanál. Tyto události jsou zpracování chyb a další důležité problémy v ReverseProxy požadavku, jakož i zpracovaných požadavků. **Toto je naše doporučení pro komplexní protokolování**. Chcete-li tyto události zobrazit v prohlížeči diagnostických událostí v sadě Visual Studio, přidejte "Microsoft-ServiceFabric:4:0x4000000000000010" do seznamu zprostředkovatelů trasování událostí pro Windows.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Data a kanálů zasílání zpráv – podrobné: podrobné kanál, který obsahuje všechny protokoly, které jsou méně náročné z dat a zasílání zpráv v clusteru a podrobné provozní kanál. Podrobné řešení potíží se všechny události reverzního proxy serveru, najdete [Průvodce Diagnostika reverzního proxy serveru](service-fabric-reverse-proxy-diagnostics.md).  Chcete-li tyto události zobrazit v prohlížeči diagnostických událostí v sadě Visual Studio, přidejte "Microsoft-ServiceFabric:4:0x4000000000000020" do seznamu zprostředkovatelů trasování událostí pro Windows.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Tento kanál má velmi velký objem událostí, povolení shromažďování událostí z této podrobné výsledky kanálu v spoustu trasování rychle vytvořit a může využívat kapacitu úložiště. Pouze zapněte Pokud je to nezbytně nutné.


Povolit **základní provozní kanál** Naše doporučení pro komplexní protokolování s minimem šumu `EtwManifestProviderConfiguration` v `WadCfg` šablony by vypadat nějak takto:

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
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Shromažďovat z nových kanálů EventSource

Aktualizovat diagnostiku, která shromažďování protokolů z nových kanálů EventSource, které představují novou aplikaci, že jste o nasazení, provádět stejné kroky, jak je uvedeno výše pro nastavení diagnostiky pro existující cluster.

Aktualizovat `EtwEventSourceProviderConfiguration` oddílu v souboru template.json mohli přidat záznamy pro nové kanály EventSource před použít danou konfiguraci aktualizovat pomocí `New-AzureRmResourceGroupDeployment` příkaz prostředí PowerShell. Název zdroje událostí je definován jako část kódu v souboru ServiceEventSource.cs generovaný sady Visual Studio.

Například pokud váš zdroj událostí název mé Eventsource, přidejte následující kód do tabulky s názvem MyDestinationTableName umístit události z mé Eventsource.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Pokud chcete shromáždit čítače výkonu nebo protokoly událostí, upravte šablonu Resource Manageru pomocí příkladů v [vytvořit virtuální počítač Windows s monitorováním a diagnostikou pomocí šablony Azure Resource Manageru](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Potom znovu publikujte šablony Resource Manageru.

## <a name="collect-performance-counters"></a>Shromažďování čítačů výkonu

Ke shromažďování metrik výkonu z vašeho clusteru, přidejte čítače výkonu pro vaše "WadCfg > DiagnosticMonitorConfiguration" v šabloně Resource Manageru pro váš cluster. Naleznete v tématu [Performance monitoring pro aplikace s využitím WAD](service-fabric-diagnostics-perf-wad.md) pokyny, jak upravit vaše `WadCfg` ke shromažďování konkrétních čítačů výkonu. Referenční dokumentace [čítače výkonu služby Service Fabric](service-fabric-diagnostics-event-generation-perf.md) pro seznam výkonu čítače, které doporučujeme shromažďování.
  
Pokud používáte jímky Application Insights, jak je popsáno v následující části a chcete tyto metriky se zobrazí ve službě Application Insights, pak Ujistěte se, že přidáte název jímky v části "jímky", jak je znázorněno výše. To bude automaticky posílat čítačů výkonu, které konfigurují jednotlivě do prostředku Application Insights.


## <a name="send-logs-to-application-insights"></a>Odeslání protokolů s Application Insights

Jako součást konfigurace WAD lze provést odeslání dat monitorování a diagnostiky k Application Insights (AI). Pokud se rozhodnete využít AI k události analýzu a vizualizaci, přečtěte si [nastavení jímky AI](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-application-insights-sink-to-the-resource-manager-template) jako součást vaší "WadCfg".

## <a name="next-steps"></a>Další postup

Po nakonfigurování Azure diagnostics správně, zobrazí se data v tabulkách úložiště z protokolů trasování událostí pro Windows a EventSource. Pokud se rozhodnete používat Log Analytics, Kibana nebo jakékoli jiné datové analýzy a vizualizace platformě, která není nakonfigurovaná přímo v šabloně Resource Manageru, ujistěte se, že nastavení platformy podle vašeho výběru, pokud chcete číst data z těchto tabulek úložiště. Tím ke službě Log Analytics je poměrně jednoduchá a je podrobně [analýzy událostí a protokolů](service-fabric-diagnostics-event-analysis-oms.md). Application Insights je hodně ve speciálním případě v tomto smyslu, protože je možné nakonfigurovat jako součást konfigurace diagnostického rozšíření, naleznete [příslušný článek](service-fabric-diagnostics-event-analysis-appinsights.md) Pokud budete chtít využít AI.

>[!NOTE]
>Aktuálně neexistuje žádný způsob, jak filtrovat nebo mazání událostí, které se odesílají do tabulky. Pokud implementujete není procesu odebrání události z tabulky, bude růst v tabulce. V současné době je příkladem data výmazu dat služby spuštěné [sledovacích ukázka](https://github.com/Azure-Samples/service-fabric-watchdog-service), a je doporučeno, že napíšete nějakou, pokud není dobrý důvod pro ukládání protokolů nad rámec 30 nebo 90 den časový rámec.

* [Zjistěte, jak shromáždit čítače výkonu nebo protokoly pomocí diagnostického rozšíření](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Události analýzy a vizualizace pomocí Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Události analýzy a vizualizace pomocí Log Analytics](service-fabric-diagnostics-event-analysis-oms.md)
