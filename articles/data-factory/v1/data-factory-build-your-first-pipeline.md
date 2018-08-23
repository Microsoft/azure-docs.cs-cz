---
title: 'Data Factory kurz: první datový kanál | Dokumentace Microsoftu'
description: Tento kurz služby Azure Data Factory se dozvíte, jak vytvářet a plánovat služby data factory, který zpracovává data pomocí skriptu Hive v clusteru Hadoop.
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
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8f86bcf5ecf38f0f1054fce82b66e63f0509f1c8
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42054264"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Kurz: Vytvoření prvního kanálu, který umožňuje transformovat data pomocí clusteru Hadoop
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Šablona Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, přečtěte si [Rychlý start: Vytvoření datové továrny pomocí Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

V tomto kurzu sestavíte svou první datovou továrnu Azure s datovým kanálem. Kanál transformuje vstupní data pomocí skriptu Hive v clusteru Azure HDInsight (Hadoop) a generuje výstupní data.  

Tento článek obsahuje přehled a požadavky pro tento kurz. Po dokončení požadavků můžete udělat kurz pomocí jedné z následujících nástrojů nebo sad SDK: Azure portal, sady Visual Studio, PowerShell, šablony Resource Manageru, rozhraní REST API. Vyberte jednu z možností v rozevíracím seznamu v odkazech na konci tohoto článku, pokud chcete udělat kurz pomocí jedné z těchto možností začátek (nebo).    

## <a name="tutorial-overview"></a>Přehled kurzu
V tomto kurzu budete provádět následující kroky:

1. Vytvoření **služby data factory**. Datová továrna může obsahovat jeden nebo více datových kanálů, které přesouvají a transformují data. 

    V tomto kurzu vytvoříte jeden kanál v datové továrně. 
2. Vytvoření **kanálu**. Kanál může mít jednu nebo více aktivit (příklady: aktivita kopírování, aktivita HDInsight Hive). Tato ukázka používá aktivitu HDInsight Hive, která spouští skript Hivu v clusteru HDInsight Hadoop. Skript nejprve vytvoří tabulku, která odkazuje nezpracovaná data webového protokolu uložená ve službě Azure blob storage a pak nezpracovaná data rozdělí podle roku a měsíce.

    V tomto kurzu kanál používá aktivitu Hive k transformaci dat spuštěním dotazu Hive v clusteru Azure HDInsight Hadoop. 
3. Vytvoření **propojené služby**. Vytvoříte propojenou službu, která spojuje úložiště dat nebo výpočetní službu s datovou továrnou. Úložiště dat, jako například Azure Storage, uchovává vstupní a výstupní data aktivit v kanálu. Výpočetní službu jako je cluster HDInsight Hadoop, zpracovává nebo transformuje data.

    V tomto kurzu vytvoříte dvě propojené služby: **služby Azure Storage** a **Azure HDInsight**. Azure Storage propojená služba propojuje účet úložiště Azure, který obsahuje vstupní a výstupní data do služby data factory. Azure HDInsight propojená služba propojuje cluster Azure HDInsight, který slouží k transformaci dat do služby data factory. 
3. Vytvoření vstupní a výstupní **datové sady**. Vstupní datová sada představuje vstup pro aktivitu v kanálu a výstupní datová sada představuje výstup pro aktivitu.

    V tomto kurzu zadejte vstupní a výstupní datovou sadu umístění vstupní a výstupní data ve službě Azure Blob Storage. Propojenou službu Azure Storage Určuje, co je účet služby Azure Storage používá. Vstupní datová sada Určuje, kde jsou uložené vstupní soubory a výstupní datovou sadu Určuje umístění výstupních souborů. 


Zobrazit [Úvod do služby Azure Data Factory](data-factory-introduction.md) článku podrobnější přehled služby Azure Data Factory.
  
Tady je **zobrazení diagramu** služby data factory ukázkový vytváříte v tomto kurzu. **MyFirstPipeline** má jednu aktivitu typu Hive, která využívá **AzureBlobInput** datovou sadu jako vstup a vytvoří **AzureBlobOutput** datovou sadu jako výstup. 

![Zobrazení diagramu v kurzu služby Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


V tomto kurzu **inputdata** složky **adfgetstarted** kontejner objektů blob v Azure obsahuje jeden soubor s názvem input.log. Tento soubor protokolu obsahuje položky za tři měsíce: leden, únor a březen 2016. Zde jsou řádky vzorku pro každý měsíc ve vstupním souboru. 

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

Z řádků vzorku uvedených výše, je první z nich (s 2016-01-01) zapsán do souboru 000000_0 ve měsíce = 1 složky. Podobně je druhý řádek zapsán do souboru ve složce month=2 a třetí řádek je zapsán do souboru ve složce month=3.  

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu splňovali následující požadavky:

1. **Předplatné Azure** – Pokud nemáte předplatné Azure, můžete si během několika minut vytvořit bezplatný zkušební účet. Postup při vytváření bezplatného zkušebního účtu najdete v článku [Bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/).
2. **Úložiště Azure** – Pro ukládání dat v rámci tohoto kurzu budete používat účet úložiště Azure. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md). Poznamenejte si po vytvoření účtu úložiště **název účtu** a **přístupový klíč**. Přečtěte si článek [Zobrazení, kopírování a opětovné vygenerování přístupových klíčů k úložišti](../../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).
3. Stáhnout a revidovat souboru dotazu Hive (**HQL**) umístění: [ https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql ](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Tento dotaz transformuje vstupní data a generuje výstupní data. 
4. Stáhnout a revidovat ukázkového vstupního souboru (**input.log**) umístění: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Vytvořte kontejner objektů blob s názvem **adfgetstarted** ve službě Azure Blob Storage. 
6. Nahrát **partitionweblogs.hql** do souboru **skript** složky **adfgetstarted** kontejneru. Pomocí nástrojů, jako [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 
7. Nahrát **input.log** do souboru **inputdata** složky **adfgetstarted** kontejneru. 

Po dokončení požadavků, vyberte jednu z následujících nástrojů nebo sad SDK chcete kurz absolvovat: 

- [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Šablona Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Azure portal a Visual Studio poskytují grafickým uživatelským rozhraním způsob vytváření datových továren. Vzhledem k tomu, možnosti prostředí PowerShell, šablony Resource Manageru a rozhraní REST API pro skriptování a programování způsob vytváření datových továren.

> [!NOTE]
> Datový kanál v tomto kurzu transformuje vstupní data, aby vytvořil výstupní data. Nekopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby kopírování dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz kopírování dat z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Podrobné informace najdete v tématu s popisem [plánování a provádění ve službě Data Factory](data-factory-scheduling-and-execution.md). 





  
