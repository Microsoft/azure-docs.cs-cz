---
title: Přesun dat do nebo z Azure Blob Storage pomocí konektorů SSIS | Dokumentace Microsoftu
description: Přesun dat do nebo z Azure Blob Storage pomocí konektorů SSIS.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 96a1b5fb-34d1-4b9b-8d99-2bb8289e0398
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 2f73a08d14d02b4e4b441b6ac85c6ceb97b9f173
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004978"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Přesun dat do nebo z Azure Blob Storage pomocí konektorů SSIS
[SQL Server Integration Services Feature Pack for Azure](https://msdn.microsoft.com/library/mt146770.aspx) obsahuje součásti pro připojení k Azure, přenos dat mezi Azure a místních zdrojů dat a zpracování dat uložených v Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Po zákazníci bylo přesunuto místních dat do cloudu, jsou k ní mohou z libovolné služby Azure využívat výkon sady technologií Azure. To lze, třeba ve službě Azure Machine Learning nebo v clusteru HDInsight.

Obvykle je to být první krok pro [SQL](sql-walkthrough.md) a [HDInsight](hive-walkthrough.md) návody.

Diskuzi o canonical scénáře, které používají služby SSIS k dosažení obchodních potřeb běžné v scénáře integrace hybridních dat, naleznete v tématu [udělat více s SQL Server Integration Services Feature Pack for Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blogu.

> [!NOTE]
> Dokončení Úvod do Azure blob storage, najdete v tématu [základy Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [služby Azure Blob](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Požadavky
K provedení úloh popsaných v tomto článku, musíte mít předplatné Azure a účet úložiště Azure nastavit. Váš klíč účtu úložiště Azure název a účet k ukládání nebo stahování dat musíte znát.

* Nastavení **předplatného Azure**, naleznete v tématu [zkušební verze na měsíc zdarma](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření **účtu úložiště** a podívejte se, jak získat účet a informace o klíči, [účty Azure storage](../../storage/common/storage-create-storage-account.md).

Použít **konektorů SSIS**, je nutné stáhnout:

* **SQL Server 2014 nebo 2016 Standard (nebo novější)**: instalace zahrnuje SQL Server Integration Services.
* **Microsoft SQL Server 2014 nebo 2016 integrace služby Feature Pack for Azure**: tyto soubory můžete stáhnout, v uvedeném pořadí, z [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) a [integrace SQL serveru 2016 Služby](https://www.microsoft.com/download/details.aspx?id=49492) stránky.

> [!NOTE]
> Služby SSIS je nainstalovaná se systémem SQL Server, ale není zahrnutý ve verzi Express. Informace o tom, jaké aplikace jsou zahrnuté v různých edicích systému SQL Server, naleznete v tématu [edice SQL serveru](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Školicí materiály o prostředí SSIS, naleznete v tématu [rukou na školení pro službu SSIS](https://www.microsoft.com/sql-server/training-certification)

Informace o tom, jak získat nahoru a spuštění pomocí SISS vytvářet jednoduchá extrakce, transformace a načítání (ETL) balíčky, viz [SSIS kurz: vytvoření jednoduchého balíčku ETL](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Stáhnout NYC taxislužby datovou sadu
V příkladu je popsáno zde použít veřejně dostupné datové sady – [cesty taxíkem NYC](http://www.andresmh.com/nyctaxitrips/) datové sady. Datová sada se skládá z přibližně 173 milionů taxislužby lyžovačku v NYC v roce 2013. Existují dva druhy dat: Podrobnosti dat a tarif dat o jízdách. Je soubor pro každý měsíc máme 24 soubory ve všech, z nichž každý je přibližně 2GB nekomprimované.

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do úložiště objektů blob v Azure
Pro přesun dat pomocí služby SSIS balíček z místního do Azure blob storage funkcí, můžeme použít instanci [ **úkol nahrát objekt Blob Azure**](https://msdn.microsoft.com/library/mt146776.aspx), je vidět tady:

![Konfigurace data vědy vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Parametry, které používá úlohy jsou popsány zde:

| Pole | Popis |
| --- | --- |
| **AzureStorageConnection** |Určuje existující Správce připojení úložiště Azure nebo vytvoří nový, který odkazuje na účet úložiště Azure, která odkazuje na hostuje soubory objektů blob. |
| **BlobContainer** |Určuje název kontejneru objektů blob, který obsahovat nahrané soubory jako objekty BLOB. |
| **BlobDirectory** |Určuje adresář objektů blob, kde nahraný soubor je uložen jako objekt blob bloku. Adresáře objektů blob je virtuální hierarchickou strukturu. Pokud objekt blob již existuje, nahradí ia it. |
| **LocalDirectory** |Určuje místní adresář, který obsahuje soubory k odeslání. |
| **FileName** |Určuje filtr názvu a vyberte soubory se vzorem zadaným názvem. Například MySheet\*.xls\* obsahuje soubory, jako MySheet001.xls a MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Určuje časový filtr rozsahu. Upravené soubory po *TimeRangeFrom* a před *TimeRangeTo* jsou zahrnuty. |

> [!NOTE]
> **AzureStorageConnection** přihlašovací údaje musí být správná a **BlobContainer** musí existovat předtím, než dojde k pokusu o přenos.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Stáhnout data z úložiště objektů blob v Azure
Stáhnout data z úložiště objektů blob Azure do místního úložiště pomocí služby SSIS, použít instanci [úloha stažení objektů Blob v Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Pokročilejší scénáře SSIS Azure
Funkce balíčku služby SSIS umožňuje složitější toky zpracovávat úlohy balení společně. Například může přímo do clusteru služby HDInsight, jejíž výstup může stáhnout zpět do objektu blob a pak do úložiště v místním kanálu dat objektů blob. SSIS můžete spouštět úlohy Hive a Pig na clusteru služby HDInsight pomocí dalších konektorů SSIS:

* Pro spuštění skriptu Hive v clusteru Azure HDInsight pomocí služby SSIS, použít [Azure HDInsight Hive úloh](https://msdn.microsoft.com/library/mt146771.aspx).
* Chcete-li spustit skript Pig v clusteru Azure HDInsight pomocí služby SSIS, použijte [úlohy Pig Azure HDInsight](https://msdn.microsoft.com/library/mt146781.aspx).

