---
title: Přesunutí dat úložiště objektů blob pomocí konektorů SSIS – proces vědecké ho zpracování týmových dat
description: Zjistěte, jak přesunout data do nebo z Azure Blob Storage pomocí sady FEATURE Pack služby SQL Server Integration Services pro Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 77bfd9d5bcae7bedd673354e32464d5f59bdc9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720867"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Přesunutí dat do úložiště objektů blob Azure nebo z ní pomocí konektorů SSIS
[Sada feature pack služby SQL Server Integration Services pro Azure](https://msdn.microsoft.com/library/mt146770.aspx) poskytuje komponenty pro připojení k Azure, přenos dat mezi Azure a místními zdroji dat a zpracování dat uložených v Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Jakmile zákazníci přesunou místní data do cloudu, mají přístup ke svým datům z libovolné služby Azure a využijí plný výkon sady technologií Azure. Data mohou být následně použita, například v Azure Machine Learning nebo v clusteru HDInsight.

Příklady použití těchto prostředků Azure jsou v sql [a](sql-walkthrough.md) [HDInsight](hive-walkthrough.md) návody.

Diskuse o kanonických scénářích, které používají SSIS k plnění obchodních potřeb běžných ve scénářích integrace hybridních dat, najdete v [tématu Další práce s balíčkem funkcí služby SQL Server Integration Services pro](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blog Azure.

> [!NOTE]
> Úplný úvod do úložiště objektů blob Azure najdete v části [Základy objektů blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Požadavky
K provedení úloh popsaných v tomto článku musíte mít předplatné Azure a účet Azure Storage nastavit. K nahrání nebo stahování dat potřebujete název účtu Azure Storage a klíč účtu.

* Pokud chcete nastavit **předplatné Azure,** přečtěte si [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření **účtu úložiště** a k získání informací o účtu a klíči najdete v [tématu O účtech Azure Storage](../../storage/common/storage-create-storage-account.md).

Chcete-li používat **konektory SSIS**, musíte stáhnout:

* **SQL Server 2014 nebo 2016 Standard (nebo vyšší):** Instalace zahrnuje sql server integration services.
* **Sada Feature Pack pro integrační služby Microsoft SQL Server 2014 nebo 2016 pro Azure**: Tyto konektory lze stáhnout ze stránek [integračních služeb SQL Serveru 2014](https://www.microsoft.com/download/details.aspx?id=47366) a [integračních služeb SQL Server 2016.](https://www.microsoft.com/download/details.aspx?id=49492)

> [!NOTE]
> SSIS je nainstalován s SQL Server, ale není součástí verze Express. Informace o tom, které aplikace jsou zahrnuty v různých edicích serveru SQL Server, naleznete v [tématu SQL Server Editions](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Školicí materiály k SSIS najdete [v tématu Hands On Training for SSIS](https://www.microsoft.com/sql-server/training-certification)

Informace o tom, jak získat up-and-running pomocí SISS k vytvoření jednoduché extrakce, transformace a zatížení (ETL) balíčky, naleznete v [tématu SSIS Tutorial: Vytvoření jednoduchého balíčku ETL](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Stáhnout NYC Taxi datový set
Zde popsaný příklad používá veřejně dostupnou datovou sadu – datovou sadu [NYC Taxi Trips.](https://www.andresmh.com/nyctaxitrips/) Datová sada se skládá z asi 173 milionů jízd taxíkem v New Yorku v roce 2013. Existují dva typy dat: údaje o podrobnostech cesty a údaje o tarifech. Vzhledem k tomu, že je soubor pro každý měsíc, máme 24 souborů, z nichž každý je asi 2 GB nekomprimované.

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů blob Azure
Chcete-li přesunout data pomocí sady funkcí SSIS z místního úložiště objektů blob Azure, použijeme instanci [**úlohy nahrávání objektů blob Azure**](https://msdn.microsoft.com/library/mt146776.aspx), která je zobrazena zde:

![konfigurace-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Parametry, které úloha používá, jsou popsány zde:

| Pole | Popis |
| --- | --- |
| **AzureStorageConnection** |Určuje existující Azure Storage Connection Manager nebo vytvoří nový, který odkazuje na účet úložiště Azure, který odkazuje na místo, kde jsou hostované soubory objektů blob. |
| **Kontejner objektů Blob** |Určuje název kontejneru objektů blob, který obsahuje nahrané soubory jako objekty BLOB. |
| **Adresář objektů BlobDirectory** |Určuje adresář objektů blob, ve kterém je nahraný soubor uložen jako objekt blob bloku. Adresář objektů blob je virtuální hierarchická struktura. Pokud objekt blob již existuje, ia nahrazen. |
| **LocalDirectory** |Určuje místní adresář, který obsahuje soubory, které mají být odeslány. |
| **Název_souboru** |Určuje filtr názvů pro výběr souborů se zadaným vzorem názvu. Například mysheet\*xls\* obsahuje soubory, jako je MySheet001.xls a MySheetABC.xlsx |
| **Časový rozsahod/časový rozsah** |Určuje filtr časového rozsahu. Soubory změněné po *TimeRangeFrom* a před *TimeRangeTo* jsou zahrnuty. |

> [!NOTE]
> Přihlašovací údaje **AzureStorageConnection** musí být správné a **blobContainer** musí existovat před pokusem o přenos.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Stahování dat z úložiště objektů blob Azure
Chcete-li stahovat data z úložiště objektů blob Azure do místního úložiště s SSIS, použijte instanci [úlohy stahování objektů blob Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Pokročilejší scénáře SSIS-Azure
Sada funkcí SSIS umožňuje, aby složitější toky byly řešeny úlohami balení společně. Data objektů blob mohou být například vstupní můře přímo do clusteru HDInsight, jehož výstup se dá stáhnout zpět do objektu blob a pak do místního úložiště. SSIS může spouštět úlohy Hive a Pig v clusteru HDInsight pomocí dalších konektorů SSIS:

* Chcete-li spustit skript Hive v clusteru Azure HDInsight s SSIS, použijte [úlohu Azure HDInsight Hive .](https://msdn.microsoft.com/library/mt146771.aspx)
* Chcete-li spustit skript Pig v clusteru Azure HDInsight s SSIS, použijte [úlohu Pig Azure HDInsight](https://msdn.microsoft.com/library/mt146781.aspx).

