---
title: Přesun dat do nebo z Azure Blob Storage pomocí služby SSIS konektory | Microsoft Docs
description: Přesun dat do nebo z Azure Blob Storage pomocí služby SSIS konektory.
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
ms.openlocfilehash: 165180c49de75cf4b635a89dacec311d5370acd3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837741"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Přesun dat do nebo z Azure Blob Storage pomocí služby SSIS konektory
[SQL Server Integration Services Feature Pack pro Azure](https://msdn.microsoft.com/library/mt146770.aspx) poskytuje součásti pro připojení k Azure, přenos dat mezi Azure a místní zdroje dat a zpracování dat uložených v Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Jakmile zákazníci byl přesunut místní data do cloudu, můžete přístup z libovolnou službu Azure využít potenciál sada Azure technologií. Se může použít, například v Azure Machine Learning, nebo v clusteru služby HDInsight.

To je obvykle být prvním krokem pro [SQL](sql-walkthrough.md) a [HDInsight](hive-walkthrough.md) návody.

Informace kanonický scénářů, které využívají služby SSIS k dosažení obchodních potřeb, které jsou běžné při hybridní scénáře integrace dat, naleznete v [to více s SQL Server Integration Services Feature Pack pro Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blogu.

> [!NOTE]
> Dokončení Úvod do Azure blob storage, najdete v části [základy objektu Blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [služby objektů Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Požadavky
K provedení úlohy popsané v tomto článku, musíte mít předplatné Azure a účet úložiště Azure nastavit. Je nutné znát váš klíč účtu úložiště Azure název a účet k odeslání nebo stažení data.

* Nastavit **předplatného Azure**, najdete v části [bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytváření **účet úložiště** a získání účtu a klíč, najdete v části [účty Azure storage](../../storage/common/storage-create-storage-account.md).

Použít **SSIS konektory**, je nutné stáhnout:

* **SQL Server 2014 nebo 2016 Standard (nebo novější)**: instalace zahrnuje SQL Server Integration Services.
* **Microsoft SQL Server 2014 nebo 2016 Integration Services Feature Pack pro Azure**: tyto soubory můžete stáhnout, v uvedeném pořadí, z [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) a [SQL serveru 2016 integrace Služby](https://www.microsoft.com/download/details.aspx?id=49492) stránky.

> [!NOTE]
> Služby SSIS je nainstalovaná se systémem SQL Server, ale není zahrnut ve verzi Express. Informace, na které aplikace jsou zahrnuty v různých edicích systému SQL Server najdete v tématu [edice serveru SQL](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Výukové materiály v SSIS, najdete v části [rukou na školení pro SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Informace o tom, jak získat nahoru a spuštění použití SISS k sestavení jednoduchá extrakce, transformace a načítání (ETL) balíčky, viz [SSIS kurz: vytvoření jednoduchého balíčku ETL](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Stáhnout NYC taxíkem datové sady
Příklad popsané tady použít veřejně dostupné datovou sadu – [NYC taxíkem cest](http://www.andresmh.com/nyctaxitrips/) datovou sadu. Datová sada se skládá z o 173 milionů taxíkem lyžovačku v NYC roku 2013. Existují dva typy dat: cestě podrobnosti dat a tarif data. Protože soubor pro každý měsíc, máme 24 soubory ve všech, z nichž každý je přibližně 2GB nekomprimovaným.

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do Azure blob storage
Pro přesun dat pomocí služby SSIS balíček z místního do Azure blob storage funkcí, můžeme použít instanci systému [ **úloh nahrát objekt Blob Azure**](https://msdn.microsoft.com/library/mt146776.aspx), znázorněno zde:

![Konfigurace data-vědecké účely vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Parametry, které používá úlohy jsou popsány zde:

| Pole | Popis |
| --- | --- |
| **AzureStorageConnection** |Určuje existující Správce připojení úložiště Azure nebo vytvoří novou, která odkazuje na účet úložiště Azure, který odkazuje na hostitelem souborů objektů blob. |
| **BlobContainer** |Určuje název kontejneru objektů blob, který obsahovat odeslané soubory jako objekty BLOB. |
| **BlobDirectory** |Určuje adresář objektu blob se uloží nahrávaný soubor jako objekt blob bloku. Objekt blob adresář je hierarchická struktura virtuální. Pokud již existuje objekt blob, it ia nahradit. |
| **LocalDirectory** |Určuje místní adresář, který obsahuje soubory k odeslání. |
| **FileName** |Určuje název filtr pro výběr souborů pomocí vzoru zadaný název. Například MySheet\*.xls\* obsahuje soubory, jako jsou MySheet001.xls a MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Určuje časový rozsah filtr. Soubory upravené po *TimeRangeFrom* a před *TimeRangeTo* jsou zahrnuty. |

> [!NOTE]
> **AzureStorageConnection** přihlašovací údaje musí být správné a **BlobContainer** musí existovat před pokusem o přenos.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Stahování dat z Azure blob storage
Stahování dat z Azure blob storage do místního úložiště s SSIS použít instanci systému [úloh nahrát objekt Blob Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Pokročilejší scénáře SSIS Azure
Balíček funkcí služby SSIS umožňuje složitější toky zpracovávat úlohy balení společně. Data objektu blob může například kanálu přímo do clusteru HDInsight, jejíž výstup může stáhnout zpět do objektu blob a potom do místního úložiště. SSIS můžete spustit úlohy Hive a Pig v clusteru HDInsight pomocí dalších SSIS konektory:

* Chcete-li spustit skript Hive v clusteru Azure HDInsight s SSIS, použijte [Azure HDInsight Hive úloh](https://msdn.microsoft.com/library/mt146771.aspx).
* Chcete-li spustit skript Pig v clusteru Azure HDInsight s SSIS, použijte [Azure HDInsight Pig úloh](https://msdn.microsoft.com/library/mt146781.aspx).

