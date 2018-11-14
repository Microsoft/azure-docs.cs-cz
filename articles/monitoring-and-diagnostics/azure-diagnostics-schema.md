---
title: Azure konfigurace schématu historie verzí rozšíření diagnostiky
description: Relevantní pro shromažďování čítačů výkonu v Azure Virtual Machines, Škálovací sady virtuálních počítačů, Service Fabric a Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: dbe2619b99fdc5281375e2c6c1300432d9a4cbb9
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616125"
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Verze rozšíření schématu konfigurace Azure Diagnostics a historie
Tato stránka indexy verze schématu rozšíření Azure Diagnostics se dodávají jako součást sady Microsoft Azure SDK.  

> [!NOTE]
> Rozšíření Azure Diagnostics se používá ke shromažďování čítačů výkonu a další statistiky z komponenty:
> - Azure Virtual Machines
> - Virtual Machine Scale Sets
> - Service Fabric
> - Cloud Services
> - Network Security Groups (Skupiny zabezpečení sítě)
>
> Tato stránka je pouze relevantní, pokud použijete jednu z těchto služeb.

Rozšíření Azure Diagnostics se používá s dalšími produkty Microsoftu diagnostiky jako je Azure Monitor, Application Insights a Log Analytics. Další informace najdete v tématu [přehled nástrojů pro sledování Microsoft](../azure-monitor/overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK a Diagnostika verze přesouvání grafu  

|Verze Azure SDK | Verze rozšíření diagnostiky | Model|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |Modul plug-in|  
|2.0 - 2.4         |1.0                            |Modul plug-in|  
|2.5               |1.2                            |Rozšíření|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Azure Diagnostics verze 1.0 poprvé dodáváno v modulu plug-in model – to znamená, že při instalaci sady Azure SDK, jste získali verzí diagnostiky Azure se dodávají s ním.  

 Spuštění diagnostiky Azure pomocí sady SDK 2.5 (Diagnostika verze 1.2), se nepovedlo rozšíření modelu. Nástroje využívat nové funkce byly pouze k dispozici v novější sady SDK služby Azure, ale žádné služby pomocí diagnostiky Azure by vyberte nejnovější verzi přesouvání přímo z Azure. Například těm, kdo stále používají sadu SDK 2.5 by se načítají nejnovější verze uvedené v předchozí tabulce, bez ohledu na to, když používají novější funkce.  

## <a name="schemas-index"></a>Schémata indexu  
Různé verze diagnostiky Azure pomocí různých konfiguračních schémat.

[Schéma konfigurace diagnostiky 1.0](azure-diagnostics-schema-1dot0.md)  

[Schéma konfigurace diagnostiky 1.2](azure-diagnostics-schema-1dot2.md)  

[Diagnostics 1.3 a novější schéma konfigurace](azure-diagnostics-schema-1dot3-and-later.md)  

## <a name="version-history"></a>Historie verzí

### <a name="diagnostics-extension-111"></a>Diagnostické rozšíření 1.11
Přidání podpory pro jímka Azure monitoru. Tuto jímku platí pouze pro čítače výkonu. Povolí odesílání počítadla výkonu shromažďovaných na virtuálním počítači, VMSS nebo cloudové služby Azure monitor jako vlastní metriky. Jímka Azure monitoru podporuje:
* Načítání všech čítačů výkonu odesílat přes Azure Monitor [Azure Monitor metriky rozhraní API.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Upozorňuje na všechny čítače výkonu odeslané do Azure monitoru na novém [sjednocené prostředí upozornění](monitoring-overview-alerts.md) ve službě Azure Monitor
* Použít zástupný znak operátoru v čítače výkonu jako dimenze "Instance" na vaše metriky. Například pokud jste shromáždili "logický disk (\*) / DiskWrites za sekundu" čítače by být schopen filtr a rozdělit na dimenzi "Instanci" do diagramů nebo upozorňovat na zápis disku/s pro každý logický Disk (C:, D: atd.)

Definovat jako nové jímka v konfiguraci rozšíření diagnostiky Azure Monitor
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
> Konfigurace jímka Azure monitoru pro klasické virtuální počítače a cloudové služby Classic vyžaduje další parametry definované v privátní konfigurace rozšíření diagnostiky.
>
> Pro další podrobnosti prosím odkaz [podrobné dokumentaci schéma rozšíření diagnostiky.](azure-diagnostics-schema-1dot3-and-later.md)

Dále je nutné nakonfigurovat čítače výkonu má být směrována na jímka Azure monitoru.
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

### <a name="diagnostics-extension-19"></a>Diagnostické rozšíření 1.9
Přidání podpory Dockeru.


### <a name="diagnostics-extension-181"></a>Diagnostické rozšíření 1.8.1
Můžete zadat v privátní konfigurace tokenu SAS místo klíče účtu úložiště. Pokud není k dispozici SAS token, klíče účtu úložiště se ignoruje.


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


### <a name="diagnostics-extension-18"></a>Diagnostické rozšíření 1.8
Přidaným úložištím typ PublicConfig. Může být StorageType *tabulky*, *Blob*, *TableAndBlob*. *Tabulka* je výchozí nastavení.


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


### <a name="diagnostics-extension-17"></a>Diagnostické rozšíření 1.7
Přidali jsme možnost směrování do centra událostí.

### <a name="diagnostics-extension-15"></a>Diagnostické rozšíření 1.5
Přidá prvek jímek a schopnost posílat diagnostická data do [Application Insights](../application-insights/app-insights-cloudservices.md) což usnadňuje diagnostiku problémů napříč vaší aplikace a zároveň úrovně systému a infrastruktury.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 a Diagnostika extension 1.3
Pro projekty Cloudovou službu v sadě Visual Studio byly provedeny následující změny. (Tyto změny platí také pro novější verze sady Azure SDK.)

* Místní emulátor nyní podporuje diagnostiku. Tato změna znamená, že můžete shromažďovat diagnostická data a ujistěte se, že aplikace vytváří správné trasování při vývoji a testování v sadě Visual Studio. Připojovací řetězec `UseDevelopmentStorage=true` povolí shromažďování dat diagnostiky, zatímco spouštíte projekt cloudové služby v sadě Visual Studio pomocí emulátoru úložiště Azure. Všechna diagnostická data se shromažďují v účtu úložiště (vývoj pro úložiště).
* Řetězec připojení účtu úložiště diagnostiky (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) je znovu uložen v souboru (.csdef) služby. V Azure SDK 2.5 byl zadán účet úložiště diagnostiky ze souboru diagnostics.wadcfgx.

Existují některé důležité rozdíly mezi jak připojovací řetězec pracoval v Azure SDK 2.4 a starší a jak to funguje v Azure SDK 2.6 nebo novější.

* Azure SDK 2.4 a dřívějších verzí použil připojovací řetězec za běhu modulu plug-in diagnostiky se získat informace o účtu úložiště pro přenos diagnostické protokoly.
* V Azure SDK 2.6 nebo novější Visual Studio používá připojovací řetězec diagnostiky ke konfiguraci rozšíření diagnostiky s informacemi o příslušné úložiště účtu během publikování. Připojovací řetězec umožňuje definovat jiný účet úložiště pro jiné služby konfigurace, které Visual Studio používat při publikování. Ale protože Diagnostika modulu plug-in již není k dispozici (po Azure SDK 2.5), souboru .cscfg samostatně nejde povolit rozšíření diagnostiky. Je nutné povolit rozšíření samostatně prostřednictvím nástrojů, jako je Visual Studio nebo prostředí PowerShell.
* Pro zjednodušení procesu konfigurace rozšíření diagnostiky pomocí Powershellu, výstup balíčku ze sady Visual Studio také obsahuje veřejné konfigurace XML pro rozšíření diagnostiky pro každou roli. Visual Studio používá k naplnění informace o účtu úložiště k dispozici ve veřejné konfiguraci diagnostiky připojovací řetězec. Jsou vytvořena ve složce rozšíření a mají tvar PaaSDiagnostics veřejné konfigurační soubory. <RoleName>. PubConfig.xml. Všechna nasazení na základě prostředí PowerShell můžete použít tento model pro každou konfiguraci mapování k roli.
* Připojovací řetězec v souboru .cscfg slouží také pomocí webu Azure portal pro přístup k diagnostická data, může se objevit v **monitorování** kartu. Připojovací řetězec je potřeba ke konfiguraci služby, chcete-li zobrazit podrobné údaje o monitorování na portálu.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Projekty migrace do Azure SDK 2.6 a novější
Při migraci z Azure SDK 2.5 Azure SDK 2.6 nebo novější, pokud jste měli účet úložiště diagnostiky zadané v souboru .wadcfgx, pak zůstane k dispozici. Využít flexibilitu vycházející z používání různých účtů úložiště pro konfiguraci jiného úložiště, budete muset ručně přidat připojovací řetězec do projektu. Pokud migrujete projektu z Azure SDK 2.4 nebo starší na Azure SDK 2.6, jsou zachovány diagnostiky připojovací řetězce. Mějte však na paměti změny v jak připojovací řetězce se bude zacházet ve verzi Azure SDK 2.6 jak je uvedeno v předchozí části.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Určuje, jak Visual Studio účet úložiště diagnostiky
* Pokud v souboru .cscfg je zadaný připojovací řetězec diagnostiky, Visual Studio se používá ke konfiguraci rozšíření diagnostiky při publikování a při generování souborů xml veřejné konfigurace při vytváření balíčku.
* Pokud v souboru .cscfg není zadán žádný připojovací řetězec diagnostiky, pak Visual Studio přejde k použití účet úložiště zadaný v souboru .wadcfgx konfigurace při publikování a generují se soubory xml veřejné konfigurace rozšíření diagnostiky Při vytváření balíčku.
* Diagnostika připojovací řetězec v souboru .cscfg má přednost před účtu úložiště v souboru .wadcfgx. Pokud v souboru .cscfg je zadaný připojovací řetězec diagnostiky, Visual Studio, který používá a účtu úložiště v .wadcfgx ignoruje.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Co dělá zaškrtávací políčko "Aktualizovat připojovací řetězce úložiště vývoj..."?
Zaškrtávací políčko pro **při publikování do Microsoft Azure aktualizovat připojovací řetězce úložiště vývoj pro diagnostiku a ukládání do mezipaměti pomocí přihlašovacích údajů účtu úložiště Microsoft Azure** poskytuje pohodlný způsob, jak aktualizovat všechny vývoje připojení k účtu úložiště řetězců pomocí služby Azure storage účet zadaný během publikování.

Předpokládejme například, že zaškrtnete toto políčko a Diagnostika připojovací řetězec Určuje `UseDevelopmentStorage=true`. Při publikování projektu do Azure, Visual Studio automaticky aktualizovat připojovací řetězec diagnostiky s účtem úložiště, které jste zadali v Průvodci publikováním. Pokud účet úložiště skutečné byl zadán jako připojovací řetězec diagnostiky, pak tento účet se ale používá místo toho.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnostické funkce rozdíly mezi Azure SDK 2.4 a dříve a Azure SDK 2.5 nebo novější
Pokud provádíte upgrade projektu z Azure SDK 2.4 pro Azure SDK 2.5 nebo novější, byste měli mít na paměti následující rozdíly funkcí diagnostiky.

* **Rozhraní API pro konfiguraci se považují za zastaralé** – programovou konfiguraci diagnostiky je k dispozici v Azure SDK 2.4 nebo starší verze, ale je zastaralé v Azure SDK 2.5 nebo novější. Pokud vaše konfigurace diagnostiky je aktuálně definován v kódu, bude nutné znovu nakonfigurujte tato nastavení od nuly v migrovaného projektu v pořadí pro diagnostiku pokračovat v práci. Konfigurační soubor diagnostiky pro Azure SDK 2.4 je diagnostics.wadcfg a diagnostics.wadcfgx pro Azure SDK 2.5 nebo novější.
* **Diagnostika pro aplikace cloud service se dá nakonfigurovat jenom na úrovni role, ne na úrovni instance.**
* **Pokaždé, když nasadíte aplikaci, konfiguraci diagnostiky se aktualizuje** – to může způsobit problémy s paritou, pokud změníte konfiguraci diagnostiky z Průzkumníka serveru a poté znovu proveďte nasazení vaší aplikace.
* **V Azure SDK 2.5 nebo novější, výpisy stavu systému jsou nakonfigurované v konfiguračním souboru diagnostiky není v kódu** – Pokud máte výpisy při selhání nakonfigurovaný v kódu, budete muset ručně převést konfiguraci z kódu do konfiguračního souboru, protože výpisy stavu systému se nepřenesou během migrace do Azure SDK 2.6.
