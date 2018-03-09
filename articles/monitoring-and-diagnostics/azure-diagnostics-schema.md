---
title: "Verze rozšíření schéma konfigurace Azure Diagnostics a historie | Microsoft Docs"
description: "Relevantní pro shromažďování čítače výkonu ve virtuálních počítačích Azure, škálovatelné sady virtuálních počítačů, Service Fabric a cloudové služby."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/16/2017
ms.author: robb
ms.openlocfilehash: 45a092452bc702a6a593cd6c21a8d2eaf4ff44bd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Verze rozšíření schéma konfigurace Azure Diagnostics a historie
Tato stránka indexy verze schématu rozšíření Azure Diagnostics dodávána jako součást nástroje Microsoft Azure SDK.  

> [!NOTE]
> Rozšíření diagnostiky Azure je komponenta, kterou používá ke shromažďování čítačů výkonu a další statistiky z:
> - Azure Virtual Machines 
> - Škálovací sady virtuálních počítačů
> - Service Fabric 
> - Cloud Services 
> - Network Security Groups (Skupiny zabezpečení sítě)
> 
> Tato stránka je pouze relevantní, pokud používáte některou z těchto služeb.

Rozšíření diagnostiky Azure se používá s dalšími produkty Microsoftu diagnostiky jako monitorování Azure, Application Insights a analýzy protokolů. Další informace najdete v tématu [přehled nástrojů pro monitorování Microsoft](monitoring-overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK a Diagnostika verze přesouvání grafu  

|Azure SDK verze | Verze rozšíření diagnostiky | Model|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |modul plug-in|  
|2.0 - 2.4         |1.0                            |modul plug-in|  
|2.5               |1.2                            |Rozšíření|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|



 Azure Diagnostics verze 1.0 nejprve poskytuje modul plug-in model – což znamená, že při instalaci sady Azure SDK vy máte verzi Azure diagnostics dodávané s ním.  

 Od verze 2.5 SDK (diagnostics verze 1.2), Azure diagnostics byl přesunut do model rozšíření. Abyste mohli využívat nové funkce nástroje byly pouze k dispozici v novější SDK služby Azure, ale všechny služby pomocí nástroje Azure diagnostics by vyzvedne, až na nejnovější verzi přesouvání přímo z Azure. Například každý, kdo stále pomocí sady SDK, 2.5 by být načítání na nejnovější verzi vidět v předchozí tabulce, bez ohledu na to, když používají funkce novější.  

## <a name="schemas-index"></a>Index schémat.  
Různé verze diagnostiky Azure používají schémata jinou konfiguraci. 

[Schéma konfigurace diagnostiky 1.0](azure-diagnostics-schema-1dot0.md)  

[Schéma konfigurace diagnostiky 1.2](azure-diagnostics-schema-1dot2.md)  

[Diagnostika 1.3 a novější schéma konfigurace](azure-diagnostics-schema-1dot3-and-later.md)  

## <a name="version-history"></a>Historie verzí


### <a name="diagnostics-extension-19"></a>Rozšíření diagnostiky 1.9 
Byla přidána podpora Docker.


### <a name="diagnostics-extension-181"></a>Rozšíření diagnostiky 1.8.1 
Můžete zadat token SAS místo klíč účtu úložiště v privátní konfigurace. Pokud je zadaný SAS token, klíče účtu úložiště se ignoruje.


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


### <a name="diagnostics-extension-18"></a>Rozšíření diagnostiky 1.8 
Typ úložiště přidané do PublicConfig. Může být StorageType *tabulky*, *Blob*, *TableAndBlob*. *Tabulka* je výchozí.


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


### <a name="diagnostics-extension-17"></a>Rozšíření diagnostiky 1.7 
Přidání možnosti trasy k centru EventHub.

### <a name="diagnostics-extension-15"></a>Rozšíření diagnostiky 1.5
Přidat element jímky a možnost odesílat data diagnostiky [Application Insights](../application-insights/app-insights-cloudservices.md) snadněji diagnostikovat problémy v aplikaci, jakož i úroveň systému a infrastruktury.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 a Diagnostika extension 1.3 
Pro cloudové služby projekty v sadě Visual Studio byly provedeny následující změny. (Tyto změny také použita na novější verzi sady Azure SDK.)

* Místní emulátoru nyní podporuje diagnostiku. Tato změna znamená můžete shromažďovat diagnostická data a ujistěte se, že aplikace je vytváření správné trasování, když jste vývoj a testování v sadě Visual Studio. Připojovací řetězec `UseDevelopmentStorage=true` umožňuje shromažďování dat diagnostiky, když spouštíte projekt cloudové služby v sadě Visual Studio pomocí emulátoru úložiště Azure. Všechny diagnostická data se shromažďují v účtu úložiště (vývoj pro úložiště).
* Řetězec připojení účtu úložiště diagnostiky (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) je znovu uložené v souboru (.csdef) služby. V souboru diagnostics.wadcfgx bylo zadáno v Azure SDK 2.5 účet úložiště diagnostiky.

Existují některé významné rozdíly mezi jak šlo připojovací řetězec v Azure SDK 2.4 a starší a jak to funguje v Azure SDK 2.6 nebo novější.

* Azure SDK 2.4 a starších verzích používal připojovací řetězec za běhu modulu plug-in diagnostiky se získat informace o účtu úložiště pro přenos diagnostické protokoly.
* V Azure SDK 2.6 nebo novější Visual Studio použije ke konfiguraci rozšíření diagnostiky s informace o účtu příslušné úložiště během publikování diagnostiky připojovací řetězec. Připojovací řetězec umožňuje definovat jiným účtům úložiště pro různé služby konfigurace, které Visual Studio budou používat při publikování. Ale vzhledem k tomu, že modul plug-in diagnostiky již není k dispozici (po Azure SDK 2.5), soubor .cscfg samostatně nelze povolit rozšíření diagnostiky. Je nutné povolit rozšíření, samostatně prostřednictvím nástrojů, jako je Visual Studio nebo prostředí PowerShell.
* Ke zjednodušení procesu konfigurace rozšíření diagnostiky pomocí prostředí PowerShell, balíček výstup ze sady Visual Studio také obsahuje veřejné konfigurace XML pro rozšíření diagnostiky pro každou roli. Visual Studio použije diagnostiky připojovací řetězec k naplnění ve veřejné konfigurace informace o účtu úložiště. Veřejné konfigurační soubory jsou vytvořeny ve složce rozšíření a postupujte podle vzoru PaaSDiagnostics. <RoleName>. PubConfig.xml. Všechna nasazení na základě prostředí PowerShell můžete použít tento vzor pro mapování každé konfiguraci k roli.
* Připojovací řetězec v souboru .cscfg je také používá portál Azure pro přístup k datům diagnostiky, může se objevit v **monitorování** kartě. Připojovací řetězec je potřeba ke konfiguraci službu, kterou chcete zobrazit podrobné údaje z monitorování na portálu.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrace projekty k Azure SDK 2.6 a novějším
Při migraci z Azure SDK 2.5 Azure SDK 2.6 nebo novější, pokud jste měli účet úložiště diagnostiky, který je zadaný v souboru .wadcfgx, pak zůstane existuje. Abyste mohli využívat flexibilitu používání jiným účtům úložiště pro jiné úložiště, budete muset ručně přidat připojovací řetězec do projektu. Pokud projekt jste migraci z Azure SDK 2.4 nebo starší na Azure SDK 2.6, se zachovají diagnostiky připojovací řetězce. Upozorňujeme ale, změny v tom, jak se připojovací řetězce považují ve verzi 2.6 SDK Azure uvedeného v předchozí části.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Určuje, jak Visual Studio účet úložiště diagnostiky
* Pokud diagnostiky připojovací řetězec je zadané v souboru .cscfg, Visual Studio se používá ke konfiguraci rozšíření diagnostiky při publikování a při generování souborů xml veřejné konfigurace během balení.
* Pokud žádný diagnostiky připojovací řetězec je zadané v souboru .cscfg, pak Visual Studio se vrátí k používání účtu úložiště, zadaný v souboru .wadcfgx ke konfiguraci rozšíření diagnostiky při publikování a generování veřejného konfigurační soubory xml při balení.
* Diagnostika připojovací řetězec v souboru .cscfg má přednost před účet úložiště v souboru .wadcfgx. Pokud diagnostiky připojovací řetězec je zadané v souboru .cscfg, Visual Studio použije tento a ignoruje .wadcfgx účet úložiště.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Jaké jsou "aktualizace vývoj úložiště připojovací řetězce..." zaškrtávací políčko dělat?
Zaškrtávací políčko pro **aktualizace vývoj úložiště připojovací řetězce pro diagnostiku a ukládání do mezipaměti pomocí přihlašovacích údajů účtu úložiště Microsoft Azure při publikování do služby Microsoft Azure** nabízí pohodlný způsob aktualizace jakékoli vývoj úložiště připojovací řetězce k účtu pomocí účtu úložiště Azure během publikování zadány.

Předpokládejme například, zaškrtněte toto políčko a diagnostiky připojovací řetězec Určuje `UseDevelopmentStorage=true`. Při publikování tohoto projektu v Azure, Visual Studio automaticky aktualizovat připojovací řetězec diagnostiky s účtem úložiště, který jste zadali v Průvodci publikovat. Pokud účet úložiště skutečné byl zadán jako připojovací řetězec diagnostiky, pak tento účet se ale používá místo.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnostické funkce rozdíly mezi Azure SDK 2.4 a starší a Azure SDK, 2.5 nebo novější
Pokud upgradujete projekt z Azure SDK 2.4 na Azure SDK 2.5 nebo novější, jste měli mít na paměti následující rozdíly funkce diagnostiky.

* **Rozhraní API pro konfiguraci jsou zastaralé** – Programová konfigurace diagnostiky je k dispozici v Azure SDK 2.4 nebo starší verze, ale je zastaralý v Azure SDK 2.5 nebo novější. Pokud vaše konfigurace diagnostiky je aktuálně definována v kódu, budete muset znovu nakonfigurujte tato nastavení ručně v migrovaných projektu, pro diagnostiku chcete pokračovat v práci. Diagnostika konfiguračního souboru pro Azure SDK 2.4 je diagnostics.wadcfg a diagnostics.wadcfgx pro Azure SDK 2.5 nebo novější.
* **Diagnostika pro cloudové aplikace, služby se dá nakonfigurovat jenom na úrovni role, ne na úrovni instance.**
* **Pokaždé, když nasadíte aplikaci, konfiguraci diagnostiky se aktualizuje** – to může způsobit problémy parita, pokud změníte konfiguraci diagnostiky z Průzkumníka serveru a pak znovu nasadit aplikace.
* **V Azure SDK 2.5 nebo novější, výpisy stavu systému jsou nakonfigurované v konfiguračním souboru diagnostics není v kódu** – Pokud máte nakonfigurované v kódu výpisy stavu systému, budete muset ručně přenos konfigurace z kódu do konfiguračního souboru, protože výpisy stavu systému nejsou přenášeny během migrace do Azure SDK 2.6.

