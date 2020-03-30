---
title: Azure Data Factory – nejčastější dotazy
description: Nejčastější dotazy týkající se Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 1b042c0279e458cf83bd5c81147f4b1f25d7687d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645136"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory – nejčastější dotazy
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, podívejte se na [často kladené otázky - Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-data-factory"></a>Co je služba Azure Data Factory?
Data Factory je cloudová služba integrace dat, která **automatizuje pohyb a transformaci dat**. Stejně jako továrna, která provozuje zařízení pro sběr surovin a jejich přeměnu na hotové zboží, aplikace Data Factory orchestruje stávající služby, které shromažďují nezpracovaná data a přeměňují je na informace připravené k použití.

Data Factory umožňuje vytvářet pracovní postupy řízené daty pro přesun dat mezi místními i cloudovými úložišti dat, stejně jako data procesů a transformace pomocí výpočetních služeb, jako je Azure HDInsight a Azure Data Lake Analytics. Po vytvoření kanálu, který provede akci, kterou potřebujete, můžete naplánovat jeho pravidelné spouštění (každou hodinu, denně, týdně atd.).   

Další informace naleznete v [tématu Přehled & klíčové koncepty](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Kde najdu podrobnosti o cenách pro Azure Data Factory?
Na [stránce Podrobnosti o cenách datové továrny][adf-pricing-details] najdete podrobnosti o cenách pro Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Jak začít s Azure Data Factory?
* Přehled Azure Data Factory najdete [v tématu Úvod do Azure Data Factory](data-factory-introduction.md).
* Návod, jak **kopírovat nebo přesouvat data** pomocí aktivity kopírování, najdete v [tématu Kopírování dat z Azure Blob Storage do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Pro návod, jak **transformovat data** pomocí HDInsight Hive activity. Viz [Zpracování dat spuštěním skriptu Hive v clusteru Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Jaká je dostupnost oblasti data factory?
Data Factory je k dispozici v západní a **severní Evropě**v **USA** . Výpočetní služby a služby úložiště používané datovými továrnami mohou být v jiných oblastech. Viz [Podporované oblasti](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Jaká jsou omezení počtu datových továren/kanálů/aktivit/datových sad?
V článku Omezení **datové továrny Azure** v článku [Limity předplatného a služeb Azure, kvóty a omezení.](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Jaké je vytváření a vývojářské prostředí se službou Azure Data Factory?
Můžete vytvářet nebo vytvářet továrny na data pomocí jednoho z následujících nástrojů/sad SDK:

* **Visual Studio** Visual Studio můžete použít k vytvoření azure factory dat. Podrobnosti najdete [v tématu Vytvoření prvního datového kanálu pomocí Sady Visual Studio.](data-factory-build-your-first-pipeline-using-vs.md)
* **Azure PowerShell** V [tématu Vytvoření a monitorování Azure Data Factory pomocí Azure PowerShellu](data-factory-build-your-first-pipeline-using-powershell.md) pro kurz nebo návod pro vytvoření datové továrny pomocí PowerShellu. Viz [Data Factory Cmdlet Referenční][adf-powershell-reference] obsah v knihovně MSDN pro komplexní dokumentaci rutin datové továrny.
* **Knihovna tříd .NET** Můžete programově vytvářet továrny dat pomocí data factory .NET SDK. V části [Vytváření, monitorování a správa datových továren pomocí sady .NET SDK](data-factory-create-data-factories-programmatically.md) naleznete v návodu k vytvoření datové továrny pomocí sady .NET SDK. Viz [Reference knihovny tříd datové továrny][msdn-class-library-reference] pro komplexní dokumentaci data factory .NET SDK.
* **ROZHRANÍ API PRO ODPOČINEK** Rozhraní REST API vystavené službou Azure Data Factory můžete také použít k vytváření a nasazování datových továren. Viz [Data Factory REST API Reference][msdn-rest-api-reference] pro komplexní dokumentaci rozhraní REST API datové továrny.
* **Šablona Správce prostředků Azure** Viz [kurz: Vytvoření první azure továrně dat pomocí šablony Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) pro podrobnosti.

### <a name="can-i-rename-a-data-factory"></a>Můžu přejmenovat datovou továrnu?
Ne. Stejně jako ostatní prostředky Azure, název azure datové továrny nelze změnit.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Můžu přesunout datovou továrnu z jednoho předplatného Azure do jiného?
Ano. Tlačítko **Přesunout** na okně datové továrny, jak je znázorněno na následujícím diagramu:

![Přesunout datovou továrnu](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Jaká výpočetní prostředí podporuje Data Factory?
Následující tabulka obsahuje seznam výpočetních prostředí podporovaných data factory a aktivity, které můžete spustit na nich.

| Výpočetní prostředí | activities |
| --- | --- |
| [Cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Úl](data-factory-hive-activity.md), [Prase](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[Dotnet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Aktivity Machine Learning: Dávkové spouštění a Aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[U-SQL Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Datový sklad Azure SQL](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), SQL [Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Uložená procedura](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Jak azure data factory porovnat se službami SQL Server Integration Services (SSIS)? 
Podívejte se na prezentaci [Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) od jednoho z našich MVP (Nejoceňovanější profesionálové): Reza Rad. Některé z nedávných změn v datové továrně nemusí být uvedeny v sadě snímků. Do Azure Data Factory neustále přidáváme další funkce. Do Azure Data Factory neustále přidáváme další funkce. Tyto aktualizace začleníme do porovnání technologií integrace dat od společnosti Microsoft někdy později v tomto roce.   

## <a name="activities---faq"></a>Aktivity - nejčastější dotazy
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Jaké jsou různé typy aktivit, které můžete použít v kanálu datové továrny?
* [Aktivity přesunu dat](data-factory-data-movement-activities.md) k přesunutí dat.
* [Aktivity transformace dat](data-factory-data-transformation-activities.md) ke zpracování/transformaci dat.

### <a name="when-does-an-activity-run"></a>Kdy se aktivita spustí?
Nastavení konfigurace **dostupnosti** v tabulce výstupních dat určuje, kdy je aktivita spuštěna. Pokud jsou zadány vstupní datové sady, aktivita zkontroluje, zda jsou splněny všechny závislosti vstupních dat (to znamená **stav Připraveno)** před spuštěním.

## <a name="copy-activity---faq"></a>Aktivita kopírování – nejčastější dotazy
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Je lepší mít kanál s více aktivitami nebo samostatný kanál pro každou aktivitu?
Kanály mají sdružovat související aktivity. Pokud datové sady, které je spojují, nejsou spotřebovány žádnou jinou aktivitou mimo kanál, můžete aktivity uchovávat v jednom kanálu. Tímto způsobem není nutné řetězit aktivní období potrubí tak, aby byly vzájemně zarovnány. Integrita dat v tabulkách interní kanálu je také lépe zachována při aktualizaci kanálu. Aktualizace kanálu v podstatě zastaví všechny aktivity v rámci kanálu, odebere je a znovu je vytvoří. Z hlediska vytváření může být také snazší zobrazit tok dat v rámci souvisejících aktivit v jednom souboru JSON pro kanál.

### <a name="what-are-the-supported-data-stores"></a>Jaká jsou podporovaná úložiště dat?
Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Data Factory podporuje následující typy úložišť dat. Data z libovolného zdroje lze zapsat do libovolné jímky. Kliknutím na úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * mohou být místní nebo v Azure IaaS a vyžadují nainstalování [Brány správy dat](data-factory-data-management-gateway.md) na místním počítači nebo na počítači Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Jaké jsou podporované formáty souborů?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Kde se provádí operace kopírování?
Podrobnosti najdete v části [Globálně dostupný přesun dat.](data-factory-data-movement-activities.md#global) Stručně řečeno, pokud se jedná o místní úložiště dat, operace kopírování se provádí bránou pro správu dat ve vašem místním prostředí. A když je přesun dat mezi dvěma úložišti cloudu, operace kopírování se provádí v oblasti, která je nejblíže umístění jímky ve stejné zeměpisné oblasti.

## <a name="hdinsight-activity---faq"></a>Aktivita HDInsight – nejčastější dotazy
### <a name="what-regions-are-supported-by-hdinsight"></a>Které oblasti hdinsight podporuje?
Viz část Geografická dostupnost v následujícím článku: nebo [podrobnosti o cenách HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Jakou oblast používá cluster HDInsight na vyžádání?
Cluster HDInsight na vyžádání je vytvořen ve stejné oblasti, kde existuje úložiště, které jste zadali pro použití s clusterem.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Jak přidružit další účty úložiště do clusteru HDInsight?
Pokud používáte vlastní cluster HDInsight (BYOC – přineste si vlastní cluster), přečtěte si následující témata:

* [Použití clusteru HDInsight s alternativními účty úložiště a metaobchody][hdinsight-alternate-storage]
* [Použití dalších účtů úložiště s hive HDInsight][hdinsight-alternate-storage-2]

Pokud používáte cluster na vyžádání, který je vytvořen službou Data Factory, zadejte další účty úložiště pro propojenou službu HDInsight, aby je služba Data Factory mohla zaregistrovat vaším jménem. V definici JSON pro propojenou službu na vyžádání použijte vlastnost **additionalLinkedServiceNames** k určení alternativních účtů úložiště, jak je znázorněno v následujícím fragmentu JSON:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
Ve výše uvedeném příkladu otherLinkedServiceName1 a otherLinkedServiceName2 představují propojené služby, jejichž definice obsahují pověření, která cluster HDInsight potřebuje pro přístup k účtům alternativního úložiště.

## <a name="slices---faq"></a>Řezy – nejčastější dotazy
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Proč nejsou vstupní řezy ve stavu Připraveno?
Častou chybou není nastavení **externí** vlastnosti na **hodnotu true** ve vstupní datové sadě, pokud jsou vstupní data externí pro datovou továrnu (nevytvářená výrobcem dat).

V následujícím příkladu stačí nastavit **externí** na hodnotu true na **dataset1**.  

**DataFactory1** Kanál 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3-> activity3 -> dataset4

Pokud máte jinou datovou továrnu s kanálem, který přebírá dataset4 (vytvořený kanálem 2 v datové továrně 1), označte dataset4 jako externí datovou sadu, protože datová sada je vytvářena jinou továrně dat (DataFactory1, ne DataFactory2).  

**DataFactory2**    
Kanál 1: dataset4->activity4->dataset5

Pokud je externí vlastnost správně nastavena, ověřte, zda vstupní data existují v umístění určeném v definici vstupní datové sady.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Jak spustit řez v jiném čase než půlnoc, kdy se řez vyrábí denně?
Pomocí vlastnosti **offset** určete čas, kdy chcete řez vytvořit. Podrobnosti o této vlastnosti najdete v části [Dostupnost datové sady.](data-factory-create-datasets.md#dataset-availability) Zde je rychlý příklad:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Denní řezy začínají v **6:00** namísto výchozí půlnoci.     

### <a name="how-can-i-rerun-a-slice"></a>Jak mohu znovu spustit plátek?
Řez můžete znovu spustit jedním z následujících způsobů:

* K opětovnému spuštění okna nebo řezu aktivity použijte Monitor a spravovat aplikaci. Pokyny naleznete [v tématu Opětovné spuštění vybraných oken aktivit.](data-factory-monitor-manage-app.md#perform-batch-actions)   
* Na panelu příkazů v okně **ŘEZ DATA** pro řez na webu Azure Portal klikněte na **Spustit.**
* Spusťte rutinu **Set-AzDataFactorySliceStatus** s nastaveným stavem **čekání** na řez.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Podrobnosti o rutině najdete v tématu [Set-AzDataFactorySliceStatus.][set-azure-datafactory-slice-status]

### <a name="how-long-did-it-take-to-process-a-slice"></a>Jak dlouho trvalo zpracovat plátek?
Pomocí Průzkumníka oken aktivit v aplikaci Monitor & Správa aplikace můžete zjistit, jak dlouho zpracování řezu dat trvalo. Podrobnosti najdete v [Průzkumníkovi aktivity.](data-factory-monitor-manage-app.md#activity-window-explorer)

Na webu Azure Portal můžete taky udělat toto:  

1. Klikněte na dlaždici **Datové sady** v okně **DATA FACTORY** pro vaši datovou továrnu.
2. Klepněte na konkrétní datovou sadu v okně **Datové sady.**
3. Vyberte řez, který vás zajímá, ze seznamu **Poslední řezy** v noži **TABLE.**
4. Klepněte na aktivitu spuštěnou ze seznamu **Počet aktivit** v okně **ŘEZ DATA.**
5. V okně **PODROBNOSTI SPUŠTĚNÍ AKTIVITY** klikněte na dlaždici **Vlastnosti.**
6. Mělo by se zobrazit pole **DURATION** s hodnotou. Tato hodnota je doba, která je čas emitovaná ke zpracování řezu.   

### <a name="how-to-stop-a-running-slice"></a>Jak zastavit běžící plátek?
Pokud potřebujete zastavit provádění kanálu, můžete použít rutinu [Suspend-AzDataFactoryPipeline.](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) V současné době pozastavení kanálu nezastaví spuštění řezu, které probíhají. Po dokončení probíhající spuštění, žádné další řez je zvedl.

Pokud opravdu chcete zastavit všechny spuštění okamžitě, jediný způsob, jak by bylo odstranit kanál a vytvořit jej znovu. Pokud se rozhodnete kanál odstranit, není nutné odstraňovat tabulky a propojené služby používané kanálem.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: https://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
