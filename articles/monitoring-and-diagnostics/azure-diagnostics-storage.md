---
title: Úložiště a zobrazení diagnostických dat v úložišti Azure | Microsoft Docs
description: Získat Azure diagnostická data do úložiště Azure a jeho zobrazení
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: adegeo
ms.openlocfilehash: 248e1c87ec244649a296233bf2aaa323d3475a79
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Úložiště a zobrazení diagnostických dat ve službě Azure Storage
Diagnostických dat není uložena trvale, pokud ho přenést na emulátor úložiště Microsoft Azure nebo do úložiště Azure. Jednou v úložišti, prohlížení s jedním z několika dostupných nástrojů.

## <a name="specify-a-storage-account"></a>Zadejte účet úložiště
Zadáte účet úložiště, který chcete použít v souboru ServiceConfiguration.cscfg souboru. Informace o účtu je definován jako připojovací řetězec v nastavení konfigurace. Následující příklad ukazuje výchozí připojovací řetězec vytvořený pro nový projekt cloudové služby v sadě Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Tento připojovací řetězec k poskytování informací o účtu pro účet úložiště Azure, můžete změnit.

V závislosti na typu diagnostických dat, jež jsou shromažďována používá Azure Diagnostics služby objektů Blob nebo služby Table. V následující tabulce jsou zdroje dat, které jsou nastavené jako trvalé a jejich formátu.

| Zdroj dat | Formát úložiště |
| --- | --- |
| Azure protokoly |Table |
| Protokoly služby IIS 7.0 |Objekt blob |
| Protokolů infrastruktury Azure Diagnostics |Table |
| Protokoly trasování požadavku se nezdařilo |Objekt blob |
| Protokoly událostí systému Windows |Table |
| Čítače výkonu |Table |
| Výpisy stavu systému |Objekt blob |
| Vlastní protokoly chyb |Objekt blob |

## <a name="transfer-diagnostic-data"></a>Přenos dat diagnostiky
Pro sadu SDK, 2.5 nebo novější může dojít, žádosti o převedení diagnostických dat pomocí konfiguračního souboru. Diagnostická data můžou přenášet v naplánovaných intervalech jako zadaný v konfiguraci.

SDK 2.4 a předchozí může požádat o přenos diagnostických dat prostřednictvím konfiguračním souboru jako prostřednictvím kódu programu. Programový přístup také umožňuje provádět přenosy na vyžádání.

> [!IMPORTANT]
> Při přenosu dat diagnostiky do účtu úložiště Azure, se vynakládá pro prostředky úložiště, které používá diagnostická data.
> 
> 

## <a name="store-diagnostic-data"></a>Ukládání diagnostických dat
Data protokolu se ukládají v úložišti objektů Blob nebo tabulky s těmito názvy:

**Tabulky**

* **WadLogsTable** – protokoly, které jsou napsané v kódu pomocí naslouchací proces trasování.
* **WADDiagnosticInfrastructureLogsTable** -diagnostiky změny monitorování a konfigurace.
* **WADDirectoriesTable** – adresáře, které monitoruje monitorování diagnostiky.  To zahrnuje protokoly služby IIS, služba IIS nezdařilo požadavek protokoly a vlastní adresářů.  Umístění souboru protokolu objektu blob je zadána v poli kontejneru a název objektu blob je v poli RelativePath.  Pole Absolutní_cesta označuje umístění a název souboru, který existoval na virtuální počítač Azure.
* **WADPerformanceCountersTable** – čítače výkonu.
* **WADWindowsEventLogsTable** – protokoly událostí systému Windows.

**Objekty blob**

* **kontejneru ovládacího prvku wad** – (jenom pro SDK 2.4 a předchozí) obsahuje konfigurační soubory XML, které se řídí Azure diagnostics.
* **wad. Služba iis failedreqlogfiles** – obsahuje informace z protokolů žádostí služby IIS se nezdařilo.
* **wad. Služba iis logfiles** – obsahuje informace o protokoly služby IIS.
* **"vlastní"** – vlastní kontejner podle konfigurace adresáře, které jsou monitorovány pomocí monitorování diagnostiky.  Název kontejneru objektu blob budou zadány v WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Nástroje pro zobrazení diagnostických dat
Několik nástrojů je možné zobrazit data, jakmile se přenese do úložiště. Příklad:

* Průzkumník serveru v sadě Visual Studio – Pokud jste nainstalovali nástroje Azure pro sadu Microsoft Visual Studio, můžete v uzlu úložiště Azure v Průzkumníku serveru k zobrazení jen pro čtení objektů blob a data tabulky z účtům Azure storage. Můžete zobrazit data z účtu emulátor místního úložiště a taky z účtů úložiště jste vytvořili pro Azure. Další informace najdete v tématu [procházení a Správa prostředků úložiště pomocí Průzkumníka serveru](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která umožňuje snadno pracovat s daty Azure Storage v systému Windows, na OSX a Linux.
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) zahrnuje Azure Diagnostics správce, který umožňuje zobrazit, stahování a správě diagnostiky data shromažďovaná společností aplikace běžící v Azure.

## <a name="next-steps"></a>Další kroky
[Trasování toku v aplikaci s Azure Diagnostics cloudové služby](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)

