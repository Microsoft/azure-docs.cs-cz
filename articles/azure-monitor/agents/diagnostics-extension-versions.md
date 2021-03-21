---
title: Historie verzí schématu konfigurace rozšíření Windows Azure Diagnostics (WAD)
description: Důležité pro shromažďování čítačů výkonu v Azure Virtual Machines, VM Scale Sets, Service Fabric a Cloud Services.
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 57ba1c760fe8919fff95493c5c81da4368894f73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719810"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Verze a historie schématu konfigurace rozšíření Windows Azure Diagnostics (WAD)
Tento článek uvádí historii verzí verzí schématu [rozšíření Azure Diagnostics pro Windows (WAD)](diagnostics-extension-overview.md) , která se dodává jako součást sady SDK Microsoft Azure.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Graf expedice a verze diagnostiky Azure SDK  

|Verze sady Azure SDK | Verze rozšíření diagnostiky | Modelování|  
|------------------|-------------------------------|------|  
|verze               |1.0                            |modul plug-in|  
|2,0 – 2,4         |1.0                            |modul plug-in|  
|2.5               |1.2                            |přípona|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2,8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2,96              |1.7                            |"|
|2,96              |1.8                            |"|
|2,96              |1.8.1                          |"|
|2,96              |1.9                            |"|
|2,96              |1,11                           |"|


 Azure Diagnostics první verze 1,0 byla dodávána v modelu modulu plug-in – to znamená, že když jste nainstalovali sadu Azure SDK, máte k ní dodanou verzi Azure Diagnostics.  

 Počínaje sadou SDK 2,5 (diagnostická verze 1,2) se Diagnostika Azure stala modelu rozšíření. Nástroje pro využívání nových funkcí byly dostupné jenom v novějších sadách Azure SDK, ale kterákoli služba využívající diagnostiku Azure by si z Azure vybrala nejnovější verzi expedice přímo z Azure. Například kdokoli, který dál používá sadu SDK 2,5, načte nejnovější verzi zobrazenou v předchozí tabulce bez ohledu na to, jestli používají novější funkce.  

## <a name="schemas-index"></a>Index schémat  
Různé verze diagnostiky Azure používají různá schémata konfigurace. Schéma 1,0 a 1,2 jsou zastaralé. Další informace o verzi 1,3 a novějších najdete v tématu věnovaném [konfiguračnímu schématu diagnostiky 1,3 a novější](diagnostics-extension-schema-windows.md) .  

## <a name="version-history"></a>Historie verzí

### <a name="diagnostics-extension-111"></a>Diagnostické rozšíření 1,11
Přidání podpory pro Azure Monitor jímku. Tato jímka se vztahuje pouze na čítače výkonu. Umožňuje odesílat čítače výkonu shromážděné na VIRTUÁLNÍm počítači, VMSS nebo cloudovou službu, aby se Azure Monitor jako vlastní metriky. Jímka Azure Monitor podporuje:
* Načítání všech čítačů výkonu odeslaných do Azure Monitor prostřednictvím [rozhraní API pro Azure monitor metriky.](/rest/api/monitor/metrics/list)
* Upozorňování na všechny čítače výkonu odesílané do Azure Monitor prostřednictvím nového [jednotného prostředí s výstrahami](../alerts/alerts-overview.md) v Azure monitor
* Zpracovávání operátoru zástupného znaku v čítačích výkonu jako dimenze instance v rámci metriky. Pokud jste si například shromáždili čítač "logický disk ( \* )/DiskWrites/SEC", mohli byste filtrovat a rozdělit na dimenzi "instance", která vykreslí nebo upozorní na zápisy disku/s pro každý logický disk (C:, D: atd.).

Definování Azure Monitor jako nové jímky v konfiguraci diagnostického rozšíření
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Konfigurace jímky Azure Monitor pro klasické virtuální počítače a klasický CLoudová služba vyžaduje, aby byly v privátní konfiguraci diagnostického rozšíření definované další parametry.
>
> Další podrobnosti najdete v dokumentaci k [schématu rozšíření pro diagnostiku.](diagnostics-extension-schema-windows.md)

Dále můžete nakonfigurovat čítače výkonu, které mají být směrovány do Azure Monitor jímky.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Diagnostické rozšíření 1,9
Přidala se podpora Docker.


### <a name="diagnostics-extension-181"></a>1.8.1 rozšíření diagnostiky
V privátní konfiguraci můžete místo klíče účtu úložiště zadat token SAS. Pokud je zadaný token SAS, klíč účtu úložiště se ignoruje.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Diagnostické rozšíření 1,8
Do PublicConfig se přidal typ úložiště. StorageType může být *Table*, *BLOB*, *TableAndBlob*. *Tabulka* je výchozí.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Diagnostické rozšíření 1,7
Byla přidána možnost směrování do EventHub.

### <a name="diagnostics-extension-15"></a>Diagnostické rozšíření 1,5
Přidání prvku jímky a možnosti odesílání diagnostických dat, aby bylo možné [Application Insights](../app/cloudservices.md) usnadnit diagnostiku problémů v rámci aplikace a také úrovně systému a infrastruktury.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Sada Azure SDK 2,6 a rozšíření diagnostiky 1,3
Pro projekty cloudových služeb v aplikaci Visual Studio byly provedeny následující změny. (Tyto změny platí také pro novější verze sady Azure SDK.)

* Místní emulátor teď podporuje diagnostiku. Tato změna znamená, že můžete shromažďovat diagnostická data a zajistit, aby vaše aplikace vytvářela správné trasování při vývoji a testování v aplikaci Visual Studio. Připojovací řetězec `UseDevelopmentStorage=true` umožňuje shromažďování diagnostických dat při spuštění projektu cloudové služby v sadě Visual Studio pomocí emulátoru Azure Storage. Všechna diagnostická data se shromažďují v účtu úložiště (úložiště pro vývoj).
* Připojovací řetězec účtu úložiště diagnostiky (Microsoft. WindowsAzure. plugins. Diagnostics. ConnectionString) se do konfiguračního souboru služby (. cscfg) uloží znovu. V sadě Azure SDK 2,5 byl v souboru Diagnostics. wadcfgx zadaný účet úložiště diagnostiky.

Existují významné rozdíly mezi tím, jak připojovací řetězec pracoval v sadě Azure SDK 2,4 a starší, a jak funguje v Azure SDK 2,6 a novějším.

* V sadě Azure SDK 2,4 a starších verzích byl připojovací řetězec použit modulem plug-in diagnostiky k získání informací o účtu úložiště pro přenos diagnostických protokolů.
* Sada Visual Studio v sadě Azure SDK 2,6 a novějších používá připojovací řetězec diagnostiky ke konfiguraci rozšíření diagnostiky s příslušnými informacemi o účtu úložiště během publikování. Připojovací řetězec umožňuje definovat různé účty úložiště pro různé konfigurace služby, které bude Visual Studio používat při publikování. Vzhledem k tomu, že modul plug-in diagnostiky již není k dispozici (po sadě Azure SDK 2,5), nemůže soubor. cscfg sám povolit rozšíření diagnostiky. Toto rozšíření je nutné povolit samostatně prostřednictvím nástrojů, jako je například Visual Studio nebo PowerShell.
* Z důvodu zjednodušení procesu konfigurace diagnostického rozšíření pomocí prostředí PowerShell obsahuje výstup balíčku ze sady Visual Studio také kód XML pro veřejnou konfiguraci pro rozšíření diagnostiky pro každou roli. Visual Studio používá připojovací řetězec pro diagnostiku k naplnění informací o účtu úložiště přítomných ve veřejné konfiguraci. Veřejné konfigurační soubory se vytvoří ve složce rozšíření a postupuje podle vzoru `PaaSDiagnostics.<RoleName>.PubConfig.xml` . Jakékoli nasazení založené na prostředí PowerShell může použít tento model k mapování jednotlivých konfigurací na roli.
* Připojovací řetězec v souboru. cscfg je také používán Azure Portal pro přístup k diagnostickým datům, aby se mohl zobrazit na kartě **monitorování** . Připojovací řetězec je potřeba ke konfiguraci služby pro zobrazení podrobných dat monitorování na portálu.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrace projektů do sady Azure SDK 2,6 a novější
Pokud v souboru. wadcfgx migrujete ze sady Azure SDK 2,5 na sadu Azure SDK 2,6 nebo novější, v případě, že jste v souboru. zadali účet diagnostiky úložiště, zůstane tam. Pokud chcete využít flexibilitu při používání různých účtů úložiště pro různé konfigurace úložiště, budete muset připojovací řetězec přidat do projektu ručně. Pokud migrujete projekt ze sady Azure SDK 2,4 nebo starší do sady Azure SDK 2,6, pak jsou zachovány připojovací řetězce pro diagnostiku. Upozorňujeme ale na změny v způsobu, jakým se v sadě Azure SDK 2,6 zpracovávají připojovací řetězce, jak je uvedeno v předchozí části.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Jak Visual Studio Určuje účet úložiště diagnostiky
* Pokud je v souboru. cscfg zadán připojovací řetězec diagnostiky, Visual Studio ho použije ke konfiguraci rozšíření diagnostiky při publikování a při generování souborů XML veřejných konfigurací během balení.
* Pokud v souboru. cscfg není zadaný žádný připojovací řetězec diagnostiky, pak se Visual Studio vrátí k použití účtu úložiště zadaného v souboru. wadcfgx ke konfiguraci rozšíření diagnostiky při publikování a vygenerování souborů XML veřejné konfigurace při balení.
* Připojovací řetězec diagnostiky v souboru. cscfg má přednost před účtem úložiště v souboru. wadcfgx. Pokud je v souboru. cscfg zadán připojovací řetězec diagnostiky, aplikace Visual Studio používá a ignoruje účet úložiště v souboru. wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Co jsou připojovací řetězce pro aktualizaci vývoje pro vývoj... má zaškrtávací políčko?
Zaškrtávací políčko pro **aktualizace vývojových řetězců úložiště pro diagnostiku a ukládání do mezipaměti s Microsoft Azure přihlašovací údaje účtu úložiště při publikování do Microsoft Azure** poskytuje pohodlný způsob, jak aktualizovat všechny připojovací řetězce v účtu úložiště Azure, které jste zadali během publikování.

Předpokládejme například, že zaškrtnete toto políčko a určíte připojovací řetězec diagnostiky `UseDevelopmentStorage=true` . Při publikování projektu do Azure bude Visual Studio automaticky aktualizovat připojovací řetězec diagnostiky s účtem úložiště, který jste zadali v Průvodci publikováním. Pokud se ale skutečný účet úložiště zadal jako připojovací řetězec diagnostiky, použije se místo toho tento účet.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Rozdíly v diagnostických funkcích mezi Azure SDK 2,4 a starším a Azure SDK 2,5 a novějším
Pokud upgradujete projekt ze sady Azure SDK 2,4 na sadu Azure SDK 2,5 nebo novější, měli byste mít na paměti následující rozdíly v těchto diagnostických funkcích.

* **Rozhraní API pro konfiguraci jsou zastaralá** – Programová konfigurace diagnostiky je k dispozici v sadě azure SDK 2,4 nebo starších verzích, ale je zastaralá v sadě azure SDK 2,5 a novější. Pokud je konfigurace diagnostiky aktuálně definovaná v kódu, budete muset tato nastavení znovu nakonfigurovat od nuly v migrovaném projektu, aby Diagnostika fungovala. Konfigurační soubor diagnostiky pro sadu Azure SDK 2,4 je Diagnostics. wadcfg a Diagnostics. wadcfgx pro sadu Azure SDK 2,5 a novější.
* **Diagnostiku aplikací cloudových služeb lze konfigurovat pouze na úrovni role, nikoli na úrovni instance.**
* Při **každém nasazení aplikace se konfigurace diagnostiky aktualizuje** – to může způsobit problémy s paritou, pokud změníte konfiguraci diagnostiky z Průzkumník serveru a pak znovu nasadíte aplikaci.
* **V sadě Azure SDK 2,5 a novějších jsou výpisy stavu systému nakonfigurovány v konfiguračním souboru diagnostiky, nikoli v kódu** – Pokud máte výpisy stavu systému v kódu, bude nutné ručně přenést konfiguraci z kódu do konfiguračního souboru, protože výpisy stavu systému nebudou přeneseny během migrace do sady Azure SDK 2,6.