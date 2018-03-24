---
title: 'Data Factory kurz: první kanál dat | Microsoft Docs'
description: V tomto kurzu Azure Data Factory se dozvíte, jak vytvořit a plán objekt pro vytváření dat, která zpracovává data pomocí skriptu Hive v clusteru Hadoop.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 2fc9c0a823f48446f98fe9686242b8500462427b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Kurz: Sestavení svůj první kanál, který umožňuje transformovat data pomocí clusteru Hadoop
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Šablona Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verzi 2 služby Data Factory, který je ve verzi Preview, přečtěte si [Rychlý start: Vytvoření datové továrny pomocí Azure Data Factory verze 2](../quickstart-create-data-factory-dot-net.md).

V tomto kurzu vytvoříte vaše první pro vytváření dat Azure s datovém kanálu. Kanál transformuje vstupní data pomocí skriptu Hive v clusteru Azure HDInsight (Hadoop) nevytvořila výstupní data.  

Tento článek obsahuje přehled a požadavky pro tento kurz. Po dokončení požadavky, můžete provést kurzu pomocí jedné z následujících nástrojů nebo sady SDK: portál Azure, Visual Studio, prostředí PowerShell, šablony Resource Manageru, REST API. V rozevíracím seznamu na začátku (nebo) odkazy na konci tohoto článku udělat kurz pomocí jedné z těchto možností vyberte jednu z možností.    

## <a name="tutorial-overview"></a>Přehled kurzu
V tomto kurzu budete provádět následující kroky:

1. Vytvoření **objekt pro vytváření dat**. Objekt pro vytváření dat může obsahovat jeden nebo více datových kanálů, které přesunout a transformovat data. 

    V tomto kurzu vytvoříte jeden kanál v datové továrně. 
2. Vytvoření **kanálu**. Kanál může mít jednu nebo více aktivit (příklady: aktivita kopírování, aktivita HDInsight Hive). Tato ukázka používá aktivitu HDInsight Hive, která spouští skript Hive v clusteru HDInsight Hadoop. Skript nejprve vytvoří tabulku, která odkazuje na nezpracovaná webové protokolu data uložená v Azure blob storage a potom oddíly nezpracovaná data podle roku a měsíce.

    V tomto kurzu používá kanál Hive aktivity transformace dat pomocí dotazů Hive v clusteru Azure HDInsight Hadoop. 
3. Vytvoření **propojené služby**. Vytvoříte propojenou službu, která spojuje úložiště dat nebo výpočetní službu s datovou továrnou. Úložiště dat, jako například Azure Storage, uchovává vstupní a výstupní data aktivit v kanálu. Výpočetní služba například cluster HDInsight Hadoop procesy nebo transformace dat.

    V tomto kurzu vytvoříte dvě propojené služby: **Azure Storage** a **Azure HDInsight**. Azure Storage propojená služba propojuje účet úložiště Azure, který obsahuje vstupní a výstupní data do služby data factory. Azure HDInsight propojená služba propojuje clusteru služby Azure HDInsight, který se používá k transformaci dat do služby data factory. 
3. Vytvoření vstupní a výstupní **datové sady**. Vstupní datová sada představuje vstup pro aktivitu v kanálu a výstupní datová sada představuje výstup pro aktivitu.

    Vstupní a výstupní datové sady v tomto kurzu, zadejte umístění vstupní a výstupní data ve službě Azure Blob Storage. Služby Azure Storage, propojené Určuje účet úložiště Azure používají. Vstupní datové sady Určuje, kde vstupní soubory jsou umístěny a výstupní datové Určuje, kde jsou umístěny výstupní soubory. 


V tématu [Úvod do Azure Data Factory](data-factory-introduction.md) článku podrobnější přehled Azure Data Factory.
  
Tady je **zobrazení diagramu** objektu pro vytváření dat ukázkové v tomto kurzu vytvoříte. **MyFirstPipeline** má jednu aktivitu typu Hive, který využívá **AzureBlobInput** datovou sadu jako vstup a vytváří **AzureBlobOutput** datovou sadu jako výstup. 

![Zobrazení diagramu v kurzu služby Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


V tomto kurzu **inputdata** složky **adfgetstarted** kontejner objektů blob v Azure obsahuje jeden soubor s názvem input.log. Tento soubor protokolu obsahuje záznamy z tři měsíce: leden, února a března 2016. Zde jsou řádky vzorku pro každý měsíc ve vstupním souboru. 

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

Z řádků ukázka výše uvedeném, první z nich (s 2016-01-01) je zapsán do souboru 000000_0 v měsíci = 1 složky. Podobně je druhý řádek zapsán do souboru ve složce month=2 a třetí řádek je zapsán do souboru ve složce month=3.  

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu splňovali následující požadavky:

1. **Předplatné Azure** – Pokud nemáte předplatné Azure, můžete si během několika minut vytvořit bezplatný zkušební účet. Postup při vytváření bezplatného zkušebního účtu najdete v článku [Bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/).
2. **Úložiště Azure** – Pro ukládání dat v rámci tohoto kurzu budete používat účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Poznamenejte si po vytvoření účtu úložiště **název účtu** a **přístupový klíč**. Přečtěte si článek [Zobrazení, kopírování a opětovné vygenerování přístupových klíčů k úložišti](../../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).
3. Stáhnout a revidovat soubor dotazů Hive (**HQL**) nachází v: [ https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql ](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Tento dotaz transformuje vstupní data nevytvořila výstupní data. 
4. Stáhnout a revidovat ukázkový vstupní soubor (**input.log**) na: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Vytvořte kontejner objektů blob s názvem **adfgetstarted** ve službě Azure Blob Storage. 
6. Nahrát **partitionweblogs.hql** do souboru **skriptu** složku **adfgetstarted** kontejneru. Pomocí nástrojů, jako [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 
7. Nahrát **input.log** do souboru **inputdata** složku **adfgetstarted** kontejneru. 

Po dokončení požadavky, vyberte jednu z následujících nástrojů nebo SDK udělat tento kurz: 

- [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Šablona Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Portál Azure a Visual Studio poskytují grafickým uživatelským rozhraním způsob vytváření datové továrny. Vzhledem k tomu, prostředí PowerShell, šablony Resource Manageru a REST API možnosti skriptování a programování způsob vytváření datové továrny.

> [!NOTE]
> Datový kanál v tomto kurzu transformuje vstupní data, aby vytvořil výstupní data. Nekopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby kopírování dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz kopírování dat z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Podrobné informace najdete v tématu s popisem [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md). 





  
