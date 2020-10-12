---
title: 'Data Factory kurz: první datový kanál '
description: V tomto Azure Data Factory kurzu se dozvíte, jak vytvořit a naplánovat datovou továrnu, která zpracovává data pomocí skriptu podregistru v clusteru Hadoop.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 80644ed2d655544fa176a7be92aec3c01aa3bf14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75966073"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Kurz: vytvoření prvního kanálu pro transformaci dat pomocí clusteru Hadoop
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Šablona Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, přečtěte si [Rychlý start: Vytvoření datové továrny pomocí Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

V tomto kurzu sestavíte svůj první objekt pro vytváření dat Azure s datovým kanálem. Kanál transformuje vstupní data spuštěním skriptu podregistru v clusteru Azure HDInsight (Hadoop) a vytváří výstupní data.  

Tento článek poskytuje přehled a předpoklady pro tento kurz. Po dokončení požadavků můžete kurz udělat pomocí jednoho z následujících nástrojů nebo sad SDK: Visual Studio, PowerShell, Správce prostředků šablona REST API. Vyberte jednu z možností v rozevíracím seznamu na začátku (nebo) odkazech na konci tohoto článku, abyste mohli kurz používat jednou z těchto možností.    

## <a name="tutorial-overview"></a>Přehled kurzu
V tomto kurzu budete provádět následující kroky:

1. Vytvořte **datovou továrnu**. Datová továrna může obsahovat jeden nebo více datových kanálů, které přesouvají a transformují data.

    V tomto kurzu vytvoříte v datové továrně jeden kanál.
2. Vytvořte **kanál**. Kanál může mít jednu nebo více aktivit (příklady: aktivita kopírování, aktivita HDInsight Hive). V této ukázce se používá aktivita podregistru HDInsight, která spouští skript podregistru v clusteru HDInsight Hadoop. Skript nejprve vytvoří tabulku, která odkazuje na nezpracovaná data webového protokolu uložená v úložišti objektů BLOB v Azure, a potom oddělí nezpracovaná data po rocích a měsících.

    V tomto kurzu kanál používá aktivitu podregistru k transformaci dat spuštěním dotazu na podregistr v clusteru Azure HDInsight Hadoop.
3. Vytvořte **propojené služby**. Vytvoříte propojenou službu, která spojuje úložiště dat nebo výpočetní službu s datovou továrnou. Úložiště dat, jako například Azure Storage, uchovává vstupní a výstupní data aktivit v kanálu. Výpočetní služba, jako je cluster HDInsight Hadoop, zpracovává nebo transformuje data.

    V tomto kurzu vytvoříte dvě propojené služby: **Azure Storage** a **Azure HDInsight**. Propojená služba Azure Storage propojuje účet Azure Storage, který obsahuje vstupní a výstupní data do objektu pro vytváření dat. Propojená služba Azure HDInsight propojuje cluster Azure HDInsight, který slouží k transformaci dat do objektu pro vytváření dat.
3. Vytvoření vstupní a výstupní **datové sady**. Vstupní datová sada představuje vstup pro aktivitu v kanálu a výstupní datová sada představuje výstup pro aktivitu.

    V tomto kurzu vstupní a výstupní datové sady určují umístění vstupních a výstupních dat v Blob Storage Azure. Propojená služba Azure Storage určuje, jaký Azure Storage účet se používá. Vstupní datová sada určuje, kde jsou umístěny vstupní soubory a výstupní datová sada určuje, kde jsou umístěny výstupní soubory.


Podrobný přehled Azure Data Factory najdete v tématu [Úvod do Azure Data Factoryho](data-factory-introduction.md) článku.

Tady je **zobrazení diagramu** ukázkové datové továrny, kterou sestavíte v tomto kurzu. **MyFirstPipeline** má jednu aktivitu typu podregistr, která využívá **AzureBlobInput** datovou sadu jako vstup a vytváří datovou sadu **AzureBlobOutput** jako výstup.

![Zobrazení diagramu v kurzu služby Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


V tomto kurzu obsahuje složka **InputData** **adfgetstarted** kontejneru Azure Blob jeden soubor s názvem input. log. Tento soubor protokolu obsahuje položky ze tří měsíců: leden, únor a březen 2016. Zde jsou řádky vzorku pro každý měsíc ve vstupním souboru.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Při zpracování souboru pomocí kanálu s aktivitou HDInsight Hive aktivita spustí v klastru HDInsight skript Hive, který rozdělí vstupní data podle roku a měsíce. Skript vytvoří tři výstupní složky, které obsahují soubor s položkami za jednotlivé měsíce.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Z výše uvedených vzorových řádků je první z nich (s 2016-01-01) zapsán do souboru 000000_0 ve složce Month = 1. Podobně je druhý řádek zapsán do souboru ve složce month=2 a třetí řádek je zapsán do souboru ve složce month=3.  

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu splňovali následující požadavky:

1. **Předplatné Azure** – Pokud nemáte předplatné Azure, můžete si během několika minut vytvořit bezplatný zkušební účet. Postup při vytváření bezplatného zkušebního účtu najdete v článku [Bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/).
2. **Úložiště Azure** – Pro ukládání dat v rámci tohoto kurzu budete používat účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště si poznamenejte **název účtu** a **přístupový klíč**. Informace o tom, jak načíst přístupové klíče účtu úložiště, najdete v tématu [Správa přístupových](../../storage/common/storage-account-keys-manage.md)klíčů účtu úložiště.
3. Stáhněte a zkontrolujte soubor dotazu na podregistr (**HQL**) umístěný v umístění: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql) . Tento dotaz transformuje vstupní data a vytváří výstupní data.
4. Stáhněte si a zkontrolujte vzorový vstupní soubor (**input. log**) umístěný v: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Ve Blob Storage Azure vytvořte kontejner objektů BLOB s názvem **adfgetstarted** .
6. Nahrajte soubor **partitionweblogs. HQL** do složky **Script** v kontejneru **adfgetstarted** . Používejte nástroje, jako je [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/).
7. Odešlete soubor **input. log** do složky **InputData** v kontejneru **adfgetstarted** .

Po dokončení požadovaných součástí vyberte jeden z následujících nástrojů nebo sad SDK a proveďte tento kurz:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Šablona Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio poskytuje grafické uživatelské rozhraní a způsob vytváření datových továrn. Vzhledem k tomu, že PowerShell, Správce prostředků šablona a možnosti REST API poskytují skriptovací a programovací způsob vytváření datových továrn.

> [!NOTE]
> Datový kanál v tomto kurzu transformuje vstupní data, aby vytvořil výstupní data. Nekopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby kopírování dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz kopírování dat z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Podrobné informace najdete v tématu s popisem [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md).
