---
title: 'Kurz data factory: První datový kanál '
description: Tento kurz Azure Data Factory ukazuje, jak vytvořit a naplánovat toto datové továrně, která zpracovává data pomocí skriptu Hive v clusteru Hadoop.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966073"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Kurz: Vytvoření prvního kanálu pro transformaci dat pomocí clusteru Hadoop
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Šablona Správce prostředků](data-factory-build-your-first-pipeline-using-arm.md)
> * [ROZHRANÍ API PRO ODPOČINEK](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, přečtěte si [Rychlý start: Vytvoření datové továrny pomocí Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

V tomto kurzu vytvoříte svou první datovou továrnu Azure s datovým kanálem. Kanál transformuje vstupní data spuštěním skriptu Hive v clusteru Azure HDInsight (Hadoop) k vytvoření výstupních dat.  

Tento článek obsahuje přehled a předpoklady pro kurz. Po dokončení požadavků můžete kurz provést pomocí jednoho z následujících nástrojů/sad SDK: Visual Studio, PowerShell, šablona Správce prostředků, ROZHRANÍ REST API. Vyberte jednu z možností v rozevíracím seznamu na začátku (nebo) odkazy na konci tohoto článku k tomu kurz pomocí jedné z těchto možností.    

## <a name="tutorial-overview"></a>Přehled kurzu
V tomto kurzu budete provádět následující kroky:

1. Vytvořte **datovou továrnu**. Továrna dat může obsahovat jeden nebo více datových kanálů, které přesouvají a transformují data.

    V tomto kurzu vytvoříte jeden kanál v datové továrně.
2. Vytvořte **kanál**. Kanál může mít jednu nebo více aktivit (příklady: aktivita kopírování, aktivita HDInsight Hive). Tato ukázka používá aktivitu HDInsight Hive, která spouští skript Hive v clusteru HDInsight Hadoop. Skript nejprve vytvoří tabulku, která odkazuje na nezpracovaná data webového protokolu uložená v úložišti objektů blob Azure a pak rozdělí nezpracovaná data podle roku a měsíce.

    V tomto kurzu kanál používá Aktivita Hive k transformaci dat spuštěním dotazu Hive v clusteru Azure HDInsight Hadoop.
3. Vytvořit **propojené služby**. Vytvoříte propojenou službu, která spojuje úložiště dat nebo výpočetní službu s datovou továrnou. Úložiště dat, jako například Azure Storage, uchovává vstupní a výstupní data aktivit v kanálu. Výpočetní služba, jako je cluster HDInsight Hadoop, zpracovává/transformuje data.

    V tomto kurzu vytvoříte dvě propojené služby: **Azure Storage** a **Azure HDInsight**. Propojená služba Azure Storage propojuje účet úložiště Azure, který uchovává vstupní a výstupní data do datové továrny. Propojená služba Azure HDInsight propojuje cluster Azure HDInsight, který se používá k transformaci dat do datové továrny.
3. Vytvořte vstupní a výstupní **datové sady**. Vstupní datová sada představuje vstup pro aktivitu v kanálu a výstupní datová sada představuje výstup pro aktivitu.

    V tomto kurzu vstupní a výstupní datové sady určují umístění vstupních a výstupních dat v úložišti objektů blob Azure. Propojená služba Azure Storage určuje, jaký účet úložiště Azure se používá. Vstupní datová sada určuje, kde jsou umístěny vstupní soubory, a výstupní datová sada určuje, kam jsou umístěny výstupní soubory.


Podrobný přehled Azure Data Factory najdete v článku [Úvod do Azure Data Factory.](data-factory-introduction.md)

Tady je **zobrazení diagramu** ukázkové datové továrny, kterou vytvoříte v tomto kurzu. **MyFirstPipeline** má jednu aktivitu typu Hive, která spotřebovává **AzureBlobInput** datové sady jako vstup a vytváří **AzureBlobOutput** datové sady jako výstup.

![Zobrazení diagramu v kurzu služby Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


V tomto kurzu obsahuje složka **vstupních dat** kontejneru objektů blob **Azure adfgetstarted** jeden soubor s názvem input.log. Tento soubor protokolu obsahuje položky od tří měsíců: leden, únor a březen 2016. Zde jsou řádky vzorku pro každý měsíc ve vstupním souboru.

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

Z ukázkových řádků uvedených výše je první (s 2016-01-01) zapsán do souboru 000000_0 ve složce month=1. Podobně je druhý řádek zapsán do souboru ve složce month=2 a třetí řádek je zapsán do souboru ve složce month=3.  

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu splňovali následující požadavky:

1. **Předplatné Azure** – Pokud nemáte předplatné Azure, můžete si během několika minut vytvořit bezplatný zkušební účet. Postup při vytváření bezplatného zkušebního účtu najdete v článku [Bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/).
2. **Úložiště Azure** – Pro ukládání dat v rámci tohoto kurzu budete používat účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md). Po vytvoření účtu úložiště si poznamenejte **název účtu** a **přístupový klíč**. Informace o tom, jak načíst přístupové klíče účtu úložiště, najdete v [tématu Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).
3. Stáhněte si a zkontrolujte soubor dotazů [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql)Hive (**HQL**) umístěný na adrese: . Tento dotaz transformuje vstupní data k vytvoření výstupních dat.
4. Stáhněte a zkontrolujte ukázkový vstupní soubor **(input.log)** umístěný na adrese:[https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Vytvořte kontejner objektů blob s názvem **adfgetstarted** ve vašem Azure Blob Storage.
6. Nahrajte soubor **partitionweblogs.hql** do složky **skriptu** v kontejneru **adfgetstarted.** Používejte nástroje, jako je [Microsoft Azure Storage Explorer](https://storageexplorer.com/).
7. Nahrajte soubor **input.log** do složky **inputdata** v kontejneru **adfgetstarted.**

Po dokončení požadavků vyberte jeden z následujících nástrojů/sad SDK, které vám doponují kurz:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Šablona Správce prostředků](data-factory-build-your-first-pipeline-using-arm.md)
- [ROZHRANÍ API PRO ODPOČINEK](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio poskytuje gui způsob vytváření datových továren. Vzhledem k tomu, PowerShell, Resource Manager Šablony a REST API možnosti poskytuje skriptování a programování způsob vytváření vašich datových továren.

> [!NOTE]
> Datový kanál v tomto kurzu transformuje vstupní data, aby vytvořil výstupní data. Nekopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby kopírování dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz kopírování dat z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Podrobné informace najdete v tématu s popisem [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md).
