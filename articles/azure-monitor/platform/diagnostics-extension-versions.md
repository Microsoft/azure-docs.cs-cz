---
title: Historie verzí verze verze schématu konfigurace rozšíření Diagnostika Windows Azure (WAD)
description: Relevantní pro shromažďování čítačů perf ve virtuálních počítačích Azure, škálovacích sadách virtuálních počítačů, service fabric a cloudových službách.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 4dd91363cdebf18e6303238816e8269065a6a317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672238"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Verze a historie schématu konfigurace rozšíření diagnostiky Windows Azure (WAD)
Tento článek obsahuje historii verzí [rozšíření Diagnostika Azure pro Windows (WAD)](diagnostics-extension-overview.md) verze schématu dodané jako součást sady Microsoft Azure SDK.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK a diagnostika verze přepravní graf  

|Verze sady Azure SDK | Verze rozšíření diagnostiky | Model|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |Zásuvný modul|  
|2.0 - 2.4         |1.0                            |Zásuvný modul|  
|2.5               |1.2                            |přípona|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2,8               |1,5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Azure Diagnostics verze 1.0 nejprve dodáno v modelu plug-in – což znamená, že při instalaci sady Azure SDK, máte verzi diagnostiky Azure dodávány s ním.  

 Počínaje sadou SDK 2.5 (diagnostická verze 1.2) přešla diagnostika Azure na model rozšíření. Nástroje pro využití nových funkcí byly dostupné jenom v novějších sadách Azure SDK, ale všechny služby využívající diagnostiku Azure by vyzvedly nejnovější verzi pro odesílání přímo z Azure. Například každý, kdo stále používá sdk 2.5 by se načítání nejnovější verze uvedené v předchozí tabulce, bez ohledu na to, zda používají novější funkce.  

## <a name="schemas-index"></a>Index schémat  
Různé verze diagnostiky Azure používají různá schémata konfigurace. Schéma 1.0 a 1.2 bylo zastaralé. Další informace o verzi 1.3 a novější, naleznete [v tématu Diagnostika 1.3 a novější schéma konfigurace](diagnostics-extension-schema-windows.md)  

## <a name="version-history"></a>Historie verzí

### <a name="diagnostics-extension-111"></a>Rozšíření diagnostiky 1.11
Přidána podpora pro jímky Azure Monitor. Tato jímka je použitelná pouze pro čítače výkonu. Umožňuje odesílání čítačů výkonu shromážděných na vašem virtuálním počítači, VMSS nebo cloudové službě do Azure Monitoru jako vlastní metriky. Jímka Azure Monitor podporuje:
* Načítání všech čítačů výkonu odeslaných do Azure Monitoru prostřednictvím [metrik Azure Monitor u virtuálních metrik.](https://docs.microsoft.com/rest/api/monitor/metrics/list)
* Upozorňování na všechny čítače výkonu odeslané do Azure Monitoru prostřednictvím nového [jednotného prostředí výstrah](../../azure-monitor/platform/alerts-overview.md) ve službě Azure Monitor
* Považovat operátor zástupných symbolů v čítačích výkonu jako dimenzi "Instance" v metrike. Například pokud jste shromáždili\*čítač "LogicalDisk( )/DiskWrites/sec,, budete moci filtrovat a rozdělit na dimenzi "Instance" pro vykreslení nebo upozornění na zápisy na disku/s pro každý logický disk (C:, D:, atd.)

Definujte Azure Monitor jako novou dřez v konfiguraci rozšíření diagnostiky
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
> Konfigurace jímky Azure Monitor pro klasické virtuální počítače a klasické služby CLoud vyžaduje další parametry, které mají být definovány v rozšíření diagnostiky soukromé konfigurace.
>
> Další podrobnosti naleznete v [podrobné dokumentaci schématu rozšíření diagnostiky.](diagnostics-extension-schema-windows.md)

Dále můžete nakonfigurovat čítače výkonu tak, aby byly směrovány do jímky monitorování Azure.
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

### <a name="diagnostics-extension-19"></a>Rozšíření diagnostiky 1,9
Přidána podpora Dockeru.


### <a name="diagnostics-extension-181"></a>Rozšíření diagnostiky 1.8.1
Můžete zadat token SAS namísto klíče účtu úložiště v privátní konfigurace. Pokud je k dispozici token SAS, klíč účtu úložiště je ignorována.


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


### <a name="diagnostics-extension-18"></a>Rozšíření diagnostiky 1,8
Přidán typ úložiště do PublicConfig. StorageType může být *Tabulka*, *Blob*, *TableAndBlob*. *Tabulka* je výchozí.


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


### <a name="diagnostics-extension-17"></a>Rozšíření diagnostiky 1,7
Přidána možnost trasy na EventHub.

### <a name="diagnostics-extension-15"></a>Rozšíření diagnostiky 1,5
Přidán prvek jímky a možnost odesílat diagnostická data do [Application Insights](../../azure-monitor/app/cloudservices.md) usnadňuje diagnostiku problémů v celé vaší aplikaci, stejně jako úroveň systému a infrastruktury.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 a rozšíření diagnostiky 1.3
Pro projekty cloudové služby v sadě Visual Studio byly provedeny následující změny. (Tyto změny platí také pro novější verze sady Azure SDK.)

* Místní emulátor nyní podporuje diagnostiku. Tato změna znamená, že můžete shromažďovat diagnostická data a zajistit, aby vaše aplikace vytvářela správné trasování při vývoji a testování v sadě Visual Studio. Připojovací řetězec `UseDevelopmentStorage=true` umožňuje shromažďování diagnostických dat při spuštění projektu cloudové služby ve Visual Studiu pomocí emulátoru úložiště Azure. Všechna diagnostická data jsou shromažďována v účtu úložiště (Vývojové úložiště).
* Připojovací řetězec účtu úložiště diagnostiky (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) je znovu uložen v souboru konfigurace služby (.cscfg). V azure sdchartě 2.5 byl účet úložiště diagnostiky zadán v souboru diagnostics.wadcfgx.

Existují některé významné rozdíly mezi jak fungoval připojovací řetězec v Azure SDK 2.4 a starší a jak to funguje v Azure SDK 2.6 a novější.

* V Azure SDK 2.4 a starší připojovací řetězec byl použit za běhu modulu plug-in diagnostiky získat informace o účtu úložiště pro přenos protokolů diagnostiky.
* V sadě Azure SDK 2.6 a novější visual studio používá připojovací řetězec diagnostiky ke konfiguraci rozšíření diagnostiky s příslušnými informacemi o účtu úložiště během publikování. Připojovací řetězec umožňuje definovat různé účty úložiště pro různé konfigurace služeb, které bude visual studio používat při publikování. Protože však modul plug-in diagnostiky již není k dispozici (po azure sdcharty 2.5), soubor .cscfg sám o sobě nemůže povolit rozšíření diagnostiky. Je třeba povolit rozšíření samostatně prostřednictvím nástrojů, jako je například Visual Studio nebo PowerShell.
* Chcete-li zjednodušit proces konfigurace rozšíření diagnostiky pomocí prostředí PowerShell, výstup balíčku z sady Visual Studio obsahuje také veřejný konfigurační XML pro rozšíření diagnostiky pro každou roli. Visual Studio používá připojovací řetězec diagnostiky k naplnění informací o účtu úložiště, které jsou k dispozici ve veřejné konfiguraci. Veřejné konfigurační soubory jsou vytvořeny ve složce Rozšíření a postupujte podle vzoru `PaaSDiagnostics.<RoleName>.PubConfig.xml`. Všechna nasazení založená na Prostředí PowerShell u tohoto vzoru můžete mapovat každou konfiguraci na roli.
* Připojovací řetězec v souboru .cscfg používá také portál Azure pro přístup k diagnostickým datům, aby se mohl zobrazit na kartě **Monitorování.** Připojovací řetězec je potřeba nakonfigurovat službu tak, aby zobrazovala podrobná data monitorování na portálu.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrace projektů do sady Azure SDK 2.6 a novějších
Při migraci z Azure SDK 2.5 na Azure SDK 2.6 nebo novější, pokud jste měli účet úložiště diagnostiky zadaný v souboru .wadcfgx, pak tam zůstane. Chcete-li využít flexibilitu používání různých účtů úložiště pro různé konfigurace úložiště, budete muset ručně přidat připojovací řetězec do projektu. Pokud migrujete projekt z Azure SDK 2.4 nebo starší na Azure SDK 2.6, pak se zachovají připojovací řetězce diagnostiky. Všimněte si však změny v tom, jak se zachází s připojovacími řetězci v azure sdk 2.6, jak je uvedeno v předchozí části.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Jak Visual Studio určuje účet úložiště diagnostiky
* Pokud je v souboru .cscfg zadán připojovací řetězec diagnostiky, sada Visual Studio jej používá ke konfiguraci rozšíření diagnostiky při publikování a při generování veřejných konfiguračních souborů XML během balení.
* Pokud v souboru .cscfg není zadán žádný připojovací řetězec diagnostiky, potom visual studio přejde zpět k použití účtu úložiště určeného v souboru WADCFGX ke konfiguraci rozšíření diagnostiky při publikování a generování veřejných konfiguračních souborů XML při balení.
* Připojovací řetězec diagnostiky v souboru .cscfg má přednost před účtem úložiště v souboru WADCFGX. Pokud je v souboru .cscfg zadán připojovací řetězec diagnostiky, použije jej visual studio a ignoruje účet úložiště v .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Co znamená "Aktualizovat připojovací řetězce vývojového úložiště..." zaškrtávací políčko dělat?
Zaškrtávací políčko pro **připojovací řetězce úložiště pro vývoj aktualizací pro diagnostiku a ukládání do mezipaměti s přihlašovacími údaji účtu úložiště Microsoft Azure při publikování do Microsoft Azure** poskytuje pohodlný způsob, jak aktualizovat všechny řetězce připojení účtu úložiště pro vývoj pomocí účtu úložiště Azure určeného během publikování.

Předpokládejme například, že zaškrtnete toto `UseDevelopmentStorage=true`políčko a připojovací řetězec diagnostiky určuje . Při publikování projektu do Azure Visual Studio automaticky aktualizuje připojovací řetězec diagnostiky s účtem úložiště, který jste zadali v průvodci publikováním. Pokud však byl jako připojovací řetězec diagnostiky zadán skutečný účet úložiště, použije se místo toho tento účet.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnostika funkce rozdíly mezi Azure SDK 2.4 a starší a Azure SDK 2.5 a novější
Pokud upgradujete projekt z Azure SDK 2.4 na Azure SDK 2.5 nebo novější, měli byste mít na paměti následující rozdíly v diagnostice.

* **Rozhraní API konfigurace jsou zastaralé** – programová konfigurace diagnostiky je k dispozici v Azure SDK 2.4 nebo starší verze, ale je zastaralé v Azure SDK 2.5 a novější. Pokud je konfigurace diagnostiky aktuálně definována v kódu, budete muset tato nastavení v migrovaném projektu překonfigurovat od začátku, aby diagnostika pokračovala v práci. Konfigurační soubor diagnostiky pro Azure SDK 2.4 je diagnostics.wadcfg a diagnostics.wadcfgx pro Azure SDK 2.5 a novější.
* **Diagnostiku pro aplikace cloudových služeb lze konfigurovat pouze na úrovni role, nikoli na úrovni instance.**
* **Pokaždé, když nasadíte aplikaci, konfigurace diagnostiky se aktualizuje** – To může způsobit problémy s paritou, pokud změníte konfiguraci diagnostiky z Průzkumníka serveru a potom znovu nasadíte aplikaci.
* **V Azure SDK 2.5 a novější výpisy stavu systému jsou nakonfigurovány v konfiguračním souboru diagnostiky, nikoli v kódu** – Pokud máte výpisy stavu systému nakonfigurované v kódu, budete muset ručně přenést konfiguraci z kódu do konfiguračního souboru, protože výpisy stavu systému nejsou přeneseny během migrace do sady Azure SDK 2.6.

