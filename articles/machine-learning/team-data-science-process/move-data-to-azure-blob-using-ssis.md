---
title: Přesunutí dat služby Blob Storage pomocí konektorů SSIS – vědecké zpracování týmových dat
description: Naučte se, jak přesunout data do a z Azure Blob Storage pomocí sady funkcí služba SSIS (SQL Server Integration Services) Feature Pack pro Azure.
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
ms.openlocfilehash: ad87272749011c81c1040825da3f3c53858a55bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85322857"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Přesun dat do a z Azure Blob Storage pomocí konektorů SSIS
[Služba SSIS (SQL Server Integration Services) Feature Pack pro Azure](https://msdn.microsoft.com/library/mt146770.aspx) poskytuje komponenty pro připojení k Azure, přenos dat mezi Azure a místními zdroji dat a zpracování dat uložených v Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Jakmile si zákazníci přesunou místní data do cloudu, budou mít přístup ke svým datům z jakékoli služby Azure, aby využili plnou sílu sady technologií Azure. Data mohou být následně použita například v Azure Machine Learning nebo v clusteru HDInsight.

Příklady používání těchto prostředků Azure najdete v návodech k [SQL](sql-walkthrough.md) a [HDInsight](hive-walkthrough.md) .

Diskuzi o normativních scénářích, které používají SSIS k tomu, aby se v hybridních scénářích integrace dat prováděly běžné obchodní potřeby, najdete v článku [Další informace o služba SSIS (SQL Server Integration Services) balíčku funkcí pro blog Azure](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238) .

> [!NOTE]
> Úplný Úvod do úložiště objektů BLOB v Azure najdete v tématu [základy Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) a [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Požadavky
Abyste mohli provádět úkoly popsané v tomto článku, musíte mít předplatné Azure a nastavený účet Azure Storage. Pro nahrání nebo stažení dat budete potřebovat název účtu Azure Storage a klíč účtu.

* Pokud chcete nastavit **předplatné Azure**, přečtěte si [bezplatnou měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Pokyny k vytvoření **účtu úložiště** a získání informací o účtu a klíči najdete v tématu [informace o Azure Storagech účtech](../../storage/common/storage-create-storage-account.md).

Pokud chcete používat **konektory SSIS**, musíte si stáhnout:

* **SQL Server 2014 nebo 2016 Standard (nebo vyšší)**: Install zahrnuje služba SSIS (SQL Server Integration Services).
* **Balíček funkcí integračních služeb Microsoft SQL Server 2014 nebo 2016 pro Azure**: tyto konektory si můžete stáhnout na stránkách [SQL Server 2014 integrační služby](https://www.microsoft.com/download/details.aspx?id=47366) a [SQL Server služby 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [!NOTE]
> SSIS se instaluje s SQL Server, ale není součástí verze Express. Informace o tom, jaké aplikace jsou zahrnuté v různých edicích SQL Server, najdete v tématu [SQL Server Editions](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/) .
> 
> 

Výukové materiály na SSIS najdete v tématu [školení pro SSIS](https://www.microsoft.com/sql-server/training-certification) .

Informace o tom, jak pomocí SISS vytvořit jednoduché balíčky pro extrakci, transformaci a načítání (ETL), najdete v tématu [SSIS kurz: vytvoření jednoduchého balíčku ETL](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Stáhnout datovou sadu taxislužby NYC
V příkladu popsaném tady můžete použít veřejně dostupnou datovou sadu – datovou sadu [NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/) . Datová sada se skládá z přibližně 173 000 000 taxislužby jezdí v NYC v roce 2013. Existují dva typy dat: údaje o cestách a data tarifů. Vzhledem k tomu, že každý měsíc je soubor, máme 24 souborů, z nichž každý má přibližně 2 GB nekomprimovaných.

## <a name="upload-data-to-azure-blob-storage"></a>Nahrání dat do služby Azure Blob Storage
K přesunu dat pomocí balíčku funkcí SSIS z místního prostředí do úložiště objektů BLOB v Azure používáme instanci [**úlohy nahrávání objektů BLOB v Azure**](https://msdn.microsoft.com/library/mt146776.aspx), která se tady zobrazuje:

![Konfigurace – data-věda-VM](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Parametry, které používá úkol, jsou popsány zde:

| Pole | Popis |
| --- | --- |
| **AzureStorageConnection** |Určuje existujícího Správce připojení Azure Storage nebo vytvoří nový, který odkazuje na účet Azure Storage, který odkazuje na místo, kde jsou hostované soubory objektů BLOB. |
| **BlobContainer** |Určuje název kontejneru objektů blob, který obsahuje nahrané soubory jako objekty blob. |
| **BlobDirectory** |Určuje adresář objektů blob, ve kterém se nahraný soubor uloží jako objekt blob bloku. Adresář objektů BLOB je virtuální hierarchická struktura. Pokud objekt BLOB již existuje, nahrazuje ho. |
| **LocalDirectory** |Určuje místní adresář, který obsahuje soubory, které se mají nahrát. |
| **Bitmap** |Určuje filtr názvu pro výběr souborů se zadaným vzorem názvu. Například MySheet \* . xls \* obsahuje soubory, například MySheet001.xls a MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Určuje filtr časového rozsahu. Soubory upravené po *TimeRangeFrom* a před zahrnutím *TimeRangeTo* . |

> [!NOTE]
> Přihlašovací údaje **AzureStorageConnection** musí být správné a před pokusem o přenos musí existovat **BlobContainer** .
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Stažení dat ze služby Azure Blob Storage
Pokud chcete stáhnout data ze služby Azure Blob Storage do místního úložiště pomocí SSIS, použijte instanci [úlohy stažení objektu BLOB v Azure](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Pokročilejší SSIS-Azure scénáře
SSIS Feature Pack umožňuje zpracování složitějších toků sečtením úloh sbalení. Data objektů BLOB by se například mohla předávat přímo do clusteru HDInsight, jehož výstupy se můžou stáhnout zpátky do objektu BLOB a potom do místního úložiště. SSIS může spouštět úlohy podregistru a vepřového sádla v clusteru HDInsight pomocí dalších konektorů SSIS:

* Pokud chcete spustit skript podregistru v clusteru Azure HDInsight s SSIS, použijte [úlohu pro podregistr Azure HDInsight](https://msdn.microsoft.com/library/mt146771.aspx).
* Pokud chcete spustit skript prasete v clusteru Azure HDInsight s SSIS, použijte [úlohu Azure HDInsight pro prasečí](https://msdn.microsoft.com/library/mt146781.aspx).

