---
title: Ukládání a zobrazení diagnostických dat v Azure Storage
description: Zjistěte, jak shromažďovat diagnostická data Azure v účtu Azure Storage, abyste je mohli zobrazit pomocí jednoho z několika dostupných nástrojů.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 17430330d068fb55b45f073afecb8ba348286cb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472671"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Ukládání a zobrazení diagnostických dat v Azure Storage
Diagnostická data se netrvale ukládají, pokud je nepřenesete do emulátoru úložiště Microsoft Azure nebo do úložiště Azure. Jakmile je v úložišti, lze jej zobrazit pomocí jednoho z několika dostupných nástrojů.

## <a name="specify-a-storage-account"></a>Určení účtu úložiště
Účet úložiště, který chcete použít v souboru ServiceConfiguration.cscfg. Informace o účtu jsou definovány jako připojovací řetězec v nastavení konfigurace. Následující příklad ukazuje výchozí připojovací řetězec vytvořený pro nový projekt cloudové služby v sadě Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Tento připojovací řetězec můžete změnit a poskytnout informace o účtu účtu Azure.

V závislosti na typu shromažďovaných diagnostických dat používá Diagnostika Azure službu blob nebo službu Table. V následující tabulce jsou uvedeny zdroje dat, které jsou trvalé a jejich formát.

| Zdroj dat | Formát úložiště |
| --- | --- |
| Protokoly Azure |Table |
| Protokoly iis 7.0 |Objekt blob |
| Protokolů infrastruktury Azure Diagnostics |Table |
| Protokoly trasování neúspěšných požadavků |Objekt blob |
| Protokoly událostí systému Windows |Table |
| Čítače výkonu |Table |
| Výpisy stavu systému |Objekt blob |
| Vlastní protokoly chyb |Objekt blob |

## <a name="transfer-diagnostic-data"></a>Přenos diagnostických dat
Pro sdchartu 2.5 a novější může dojít k požadavku na přenos diagnostických dat prostřednictvím konfiguračního souboru. Diagnostická data můžete přenášet v naplánovaných intervalech, jak je uvedeno v konfiguraci.

Pro SDK 2.4 a předchozí můžete požádat o přenos diagnostických dat prostřednictvím konfiguračního souboru, stejně jako programově. Programový přístup také umožňuje provést převody na vyžádání.

> [!IMPORTANT]
> Při přenosu diagnostických dat do účtu úložiště Azure vám vzniknou náklady na prostředky úložiště, které vaše diagnostická data používají.
> 
> 

## <a name="store-diagnostic-data"></a>Ukládání diagnostických dat
Data protokolu jsou uložena v úložišti objektů Blob nebo Table s následujícími názvy:

**Tabulky**

* **WadLogsTable** - protokoly napsané v kódu pomocí naslouchací proces trasování.
* **WADDiagnosticInfrastructureLogsTable** - Diagnostické monitorování a změny konfigurace.
* **WADDirectoriesTable** – adresáře, které monitorování diagnostického monitoru.  To zahrnuje protokoly služby IIS, protokoly neúspěšných požadavků služby IIS a vlastní adresáře.  Umístění souboru protokolu objektu blob je určeno v poli Kontejner a název objektu blob je v poli RelativePath.  Pole AbsolutePath označuje umístění a název souboru tak, jak existoval ve virtuálním počítači Azure.
* **WadPerformanceCountersTable** – čítače výkonu.
* **WADWindowsEventLogsTable** – protokoly událostí systému Windows.

**Objekty blob**

* **wad-control-container** – (Pouze pro SDK 2.4 a předchozí) Obsahuje konfigurační soubory XML, které řídí diagnostiku Azure .
* **wad-iis-failedreqlogfiles** – Obsahuje informace z protokolů neúspěšných požadavků služby IIS.
* **wad-iis-logfiles** – Obsahuje informace o protokolech iis.
* **"vlastní"** – vlastní kontejner založený na konfiguraci adresářů, které jsou monitorovány diagnostickým monitorem.  Název tohoto kontejneru objektů blob bude určen v wadastable.

## <a name="tools-to-view-diagnostic-data"></a>Nástroje pro zobrazení diagnostických dat
K dispozici je několik nástrojů pro zobrazení dat po jejich přenosu do úložiště. Například:

* Průzkumník serveru v Visual Studiu – Pokud jste nainstalovali Nástroje Azure pro Microsoft Visual Studio, můžete pomocí uzlu Azure Storage v Průzkumníkovi serveru zobrazit data objektů blob a tabulky jen pro čtení z vašich účtů úložiště Azure. Můžete zobrazit data z účtu emulátoru místního úložiště a také z účtů úložiště, které jste vytvořili pro Azure. Další informace naleznete v [tématu Procházení a správa prostředků úložiště pomocí Průzkumníka serveru](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která vám umožní snadno pracovat s daty Azure Storage na Windows, OSX a Linuxu.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) obsahuje Azure Diagnostics Manager, který vám umožní zobrazit, stáhnout a spravovat diagnostická data shromážděná aplikacemi spuštěnými v Azure.

## <a name="next-steps"></a>Další kroky
[Sledování toku v aplikaci cloudových služeb pomocí Diagnostiky Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


