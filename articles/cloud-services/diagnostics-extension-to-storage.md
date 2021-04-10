---
title: Ukládání a zobrazení diagnostických dat v Azure Storage
description: Naučte se shromažďovat diagnostická data Azure v účtu Azure Storage, abyste je mohli zobrazit pomocí některého z několika dostupných nástrojů.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 4962d5f048cf41eca50a77a0dedad3cef48ac1f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98740070"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Ukládání a zobrazení diagnostických dat v Azure Storage

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Data diagnostiky se neukládají trvale, pokud je nepřenášíte do Emulátor úložiště Microsoft Azure nebo Azure Storage. V úložišti se dá zobrazit s jedním z několika dostupných nástrojů.

## <a name="specify-a-storage-account"></a>Zadat účet úložiště
Zadejte účet úložiště, který chcete použít v souboru ServiceConfiguration. cscfg. Informace o účtu jsou v nastavení konfigurace definované jako připojovací řetězec. Následující příklad ukazuje výchozí připojovací řetězec vytvořený pro nový projekt cloudové služby v aplikaci Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Tento připojovací řetězec můžete změnit tak, aby poskytoval informace o účtu úložiště Azure.

V závislosti na typu diagnostických dat, která se shromažďují, Azure Diagnostics používá buď Blob service, nebo Table service. V následující tabulce jsou uvedeny zdroje dat, které jsou trvalé a jejich formát.

| Zdroj dat | Formát úložiště |
| --- | --- |
| Protokoly Azure |Tabulka |
| Protokoly IIS 7,0 |Objekt blob |
| Protokolů infrastruktury Azure Diagnostics |Tabulka |
| Protokoly trasování neúspěšných požadavků |Objekt blob |
| Protokoly událostí systému Windows |Tabulka |
| Čítače výkonu |Tabulka |
| Výpisy stavu systému |Objekt blob |
| Vlastní protokoly chyb |Objekt blob |

## <a name="transfer-diagnostic-data"></a>Přenos diagnostických dat
V případě sady SDK 2,5 a novějších verzí může být požadavek na přenos diagnostických dat proveden prostřednictvím konfiguračního souboru. Diagnostická data můžete přenést v naplánovaných intervalech, jak je uvedeno v konfiguraci.

Pro sadu SDK 2,4 a předchozí můžete požádat o přenos diagnostických dat prostřednictvím konfiguračního souboru a také programově. Programový přístup také umožňuje provádět přenosy na vyžádání.

> [!IMPORTANT]
> Při přenosu diagnostických dat do účtu služby Azure Storage se účtují náklady za prostředky úložiště, které vaše diagnostická data používá.
> 
> 

## <a name="store-diagnostic-data"></a>Ukládat diagnostická data
Data protokolu se ukládají buď v objektu blob, nebo v úložišti tabulek s následujícími názvy:

**Tabulky**

* **WadLogsTable** – protokoly napsané v kódu pomocí naslouchacího procesu trasování.
* **WADDiagnosticInfrastructureLogsTable** – monitorování diagnostiky a změny konfigurace.
* **WADDirectoriesTable** – adresáře, které monitoruje monitorování diagnostiky.  Patří sem protokoly IIS, protokoly neúspěšných požadavků služby IIS a vlastní adresáře.  Umístění souboru protokolu objektu BLOB je zadáno v poli kontejneru a název objektu BLOB je uveden v poli RelativePath.  Pole AbsolutePath označuje umístění a název souboru, který se nachází na virtuálním počítači Azure.
* **WADPerformanceCountersTable** – čítače výkonu.
* **WADWindowsEventLogsTable** – protokoly událostí systému Windows.

**Objekty blob**

* **wad-Control-Container** – (pouze pro sadu SDK 2,4 a předchozí) obsahuje konfigurační soubory XML, které řídí diagnostiku Azure.
* **wad-IIS-failedreqlogfiles** – obsahuje informace z protokolů neúspěšných požadavků služby IIS.
* **wad-IIS-LogFiles** – obsahuje informace o protokolech IIS.
* **Custom (vlastní)** – vlastní kontejner založený na konfiguraci adresářů monitorovaných monitorováním diagnostiky.  Název tohoto kontejneru objektů BLOB se určí v WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Nástroje pro zobrazení diagnostických dat
K dispozici je několik nástrojů, které vám umožní zobrazit data po přenosu do úložiště. Například:

* Průzkumník serveru v aplikaci Visual Studio – Pokud jste nainstalovali nástroje Azure pro Microsoft Visual Studio, můžete použít uzel Azure Storage v Průzkumník serveru k zobrazení dat objektů BLOB jen pro čtení a tabulek z vašich účtů úložiště Azure. Můžete zobrazit data z místního účtu emulátoru úložiště a taky z účtů úložiště, které jste vytvořili pro Azure. Další informace najdete v tématu [procházení a Správa prostředků úložiště pomocí Průzkumník serveru](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) je samostatná aplikace, která umožňuje snadnou práci s Azure Storagemi daty v systémech Windows, OSX a Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) zahrnuje správce Azure Diagnostics, který umožňuje zobrazit, stáhnout a spravovat diagnostická data shromážděná aplikacemi běžícími na Azure.

## <a name="next-steps"></a>Další kroky
[Trasování toku v aplikaci Cloud Services s Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


