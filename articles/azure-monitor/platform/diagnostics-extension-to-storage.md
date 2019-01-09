---
title: Ukládání a zobrazení diagnostických dat v Azure Storage
description: Získání dat diagnostiky Azure do služby Azure Storage a jeho zobrazení
services: azure-monitor
author: jpconnock
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: jeconnoc
ms.component: diagnostic-extension
ms.openlocfilehash: f0d1a51a3c787bd4d9602eeaad9ac92eda7bacb7
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103736"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Ukládání a zobrazení diagnostických dat v Azure Storage
Diagnostická data nejsou ukládána trvale, pokud přenos pro emulátor úložiště Microsoft Azure nebo do úložiště Azure. Jednou v úložišti, bude možné dokument zobrazit s jedním z několika dostupných nástrojů.

## <a name="specify-a-storage-account"></a>Zadejte účet úložiště
Zadáte účet úložiště, který chcete použít v souboru ServiceConfiguration.cscfg souboru. Informace o účtu je definován jako připojovací řetězec v nastavení konfigurace. Následující příklad ukazuje výchozí připojovací řetězec pro nový projekt cloudové služby v sadě Visual Studio vytvoří:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Tento připojovací řetězec k poskytování informací o účtu pro účet úložiště Azure, můžete změnit.

V závislosti na typu diagnostická data, jež jsou shromažďována diagnostiky Azure používá službu Blob service nebo služby Table service. V následující tabulce jsou uvedeny zdroje dat, které jsou trvalé a jejich formátu.

| Zdroj dat | Formát úložiště |
| --- | --- |
| Protokoly Azure |Table |
| Protokoly služby IIS 7.0 |Objekt blob |
| Protokolů infrastruktury Azure Diagnostics |Table |
| Protokoly trasování požadavku se nezdařilo |Objekt blob |
| Protokoly událostí Windows |Table |
| Čítače výkonu |Table |
| Výpisy stavu systému |Objekt blob |
| Vlastní protokoly chyb |Objekt blob |

## <a name="transfer-diagnostic-data"></a>Přenos dat diagnostiky
Pro sadu SDK 2.5 nebo novější může dojít, požadavek na převod diagnostických dat pomocí konfiguračního souboru. Diagnostická data můžou přenášet v naplánovaných intervalech, jak je zadáno v konfiguraci.

Pro sadu SDK 2.4 a předchozí můžete požádat o převod diagnostických dat prostřednictvím konfiguračního souboru jako prostřednictvím kódu programu. Programový přístup také umožňuje provádět převody na vyžádání.

> [!IMPORTANT]
> Při přenosu dat diagnostiky do účtu úložiště Azure se vám účtovat náklady pro prostředky úložiště, které používá vaše diagnostická data.
> 
> 

## <a name="store-diagnostic-data"></a>Diagnostická data Store
Data protokolu se ukládají v úložišti objektů Blob nebo tabulek s následujícími názvy:

**Tabulky**

* **WadLogsTable** – protokoly, které jsou napsané v kódu s využitím naslouchací služby stopy.
* **WADDiagnosticInfrastructureLogsTable** -diagnostické sledování a změn konfigurace.
* **WADDirectoriesTable** – adresáře, které monitoruje monitorování diagnostiky.  Jedná se o protokoly služby IIS, služby IIS se nezdařilo, žádost o protokoly a vlastní adresáře.  Zadané umístění souboru protokolu objektů blob v kontejneru polí a název objektu blob je v poli RelativePath.  Pole Absolutní_cesta označuje umístění a název souboru, který existoval na virtuálním počítači Azure.
* **WADPerformanceCountersTable** – čítače výkonu.
* **WADWindowsEventLogsTable** – protokoly událostí Windows.

**Objekty blob**

* **kontejneru ovládacího prvku wad** – (jenom pro sadu SDK 2.4 a předchozí) obsahuje konfigurační soubory XML, které řídí diagnostiky Azure.
* **wad-iis-failedreqlogfiles** – obsahuje informace z protokolů neúspěšných požadavků služby IIS.
* **wad-iis-logfiles** – obsahuje informace o protokolech služby IIS.
* **"vlastní"** – vlastní kontejner podle konfigurace adresáře, které jsou monitorovány pomocí monitorování diagnostiky.  V WADDirectoriesTable bude zadán název tento kontejner objektů blob.

## <a name="tools-to-view-diagnostic-data"></a>Nástroje pro zobrazení diagnostických dat
Několik nástroje jsou k dispozici k zobrazení dat po převedení do úložiště. Příklad:

* Průzkumník serveru v sadě Visual Studio – Pokud jste nainstalovali nástroje Azure pro sadu Microsoft Visual Studio, můžete uzlu služby Azure Storage v Průzkumníku serveru k zobrazení jen pro čtení objektů blob a tabulky data z vašich účtů úložiště Azure. Můžete zobrazit data z vašeho účtu emulátor místního úložiště a také z účtů úložiště jste vytvořili pro Azure. Další informace najdete v tématu [procházení a Správa prostředků úložiště pomocí Průzkumníku serveru](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která umožňuje jednoduchá práci s daty Azure Storage ve Windows, os x a Linuxu.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) zahrnuje správce diagnostiky Azure, které umožňuje zobrazit, stáhnout a spravovat diagnostická data shromážděná z aplikací spuštěných v Azure.

## <a name="next-steps"></a>Další kroky
[Trasování toku do aplikace cloudové služby pomocí diagnostiky Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)

