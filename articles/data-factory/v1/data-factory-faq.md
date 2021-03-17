---
title: Azure Data Factory – často kladené otázky
description: Nejčastější dotazy týkající se Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 4ddadec327f1a4f6aa22d51ac073b9aebb0dee82
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383190"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory – často kladené otázky
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Nejčastější dotazy-Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-data-factory"></a>Co je Azure Data Factory?
Data Factory je cloudová služba pro integraci dat, která **automatizuje přesun a transformaci dat**. Stejně jako továrna, která používá zařízení k převzetí surovin a jejich transformaci na dokončené produkty, Data Factory orchestruje existující služby, které shromažďují nezpracovaná data a transformují je na informace připravené k použití.

Data Factory umožňuje vytvářet pracovní postupy řízené daty k přesouvání dat mezi místními i cloudovým úložištěm dat a také zpracovávat a transformovat data pomocí výpočetních služeb, jako je Azure HDInsight a Azure Data Lake Analytics. Po vytvoření kanálu, který provede akci, kterou potřebujete, můžete naplánovat jeho pravidelné spuštění (každou hodinu, každý den, každý týden atd.).   

Další informace najdete v tématu [přehled & klíčových konceptů](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Kde najdu informace o cenách pro Azure Data Factory?
Podrobnosti o cenách pro Azure Data Factory najdete na [stránce s podrobnostmi o cenách Data Factory][adf-pricing-details] .  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Návody začít s Azure Data Factory?
* Přehled Azure Data Factory najdete v tématu [Úvod do Azure Data Factory](data-factory-introduction.md).
* Kurz týkající se **kopírování a přesouvání dat** pomocí aktivity kopírování najdete v tématu [kopírování dat z Azure Blob Storage do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Návod, jak **transformovat data** pomocí aktivity podregistru HDInsight. Viz [procesní data spuštěním skriptu registru v clusteru Hadoop](data-factory-build-your-first-pipeline.md) .

### <a name="what-is-the-data-factorys-region-availability"></a>Jaká je dostupnost oblasti Data Factory?
Data Factory je k dispozici v **USA – západ** a **Severní Evropa**. Výpočetní služby a služby úložiště, které používají datové továrny, můžou být v jiných oblastech. Viz [podporované oblasti](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Jaká jsou omezení počtu datových továren/kanálů/aktivit/datových sad?
V článku limity [, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) najdete v části **omezení Azure Data Factory** .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Co je prostředí pro vytváření a vývojáře pomocí služby Azure Data Factory?
Pomocí jednoho z následujících nástrojů nebo sad SDK můžete vytvářet a vytvářet datové továrny:

* **Visual Studio** Pomocí sady Visual Studio můžete vytvořit datovou továrnu Azure. Podrobnosti najdete v tématu [Vytvoření prvního datového kanálu pomocí sady Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) .
* **Azure PowerShell** Kurz nebo návod k vytvoření datové továrny pomocí PowerShellu najdete v tématu věnovaném [Vytvoření a sledování Azure Data Factory používání Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) . Kompletní dokumentaci k Data Factory rutinám najdete v tématu [Data Factory obsahu rutiny][adf-powershell-reference] v knihovně MSDN.
* **Knihovna tříd .NET** Pomocí programu Data Factory .NET SDK můžete programově vytvářet datové továrny. Návod k vytvoření datové továrny pomocí sady .NET SDK najdete v tématu [Vytvoření, monitorování a Správa datových továren pomocí sady .NET SDK](data-factory-create-data-factories-programmatically.md) . Kompletní dokumentaci sady Data Factory .NET SDK naleznete v tématu [Data Factory reference knihovny tříd][msdn-class-library-reference] .
* **REST API** K vytváření a nasazování datových továren můžete použít taky REST API vystavené službou Azure Data Factory. Ucelenou dokumentaci k Data Factory REST API najdete v tématu [Data Factory REST API Reference][msdn-rest-api-reference] .
* **Šablona Azure Resource Manager** Viz [kurz: sestavení prvního objektu pro vytváření dat Azure pomocí Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) detailů šablony FO.

### <a name="can-i-rename-a-data-factory"></a>Můžu přejmenovat datovou továrnu?
No. Podobně jako u jiných prostředků Azure nejde změnit název objektu pro vytváření dat Azure.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Můžu datovou továrnu přesunout z jednoho předplatného Azure do jiného?
Ano. V okně Data Factory použijte tlačítko **přesunout** , jak je znázorněno na následujícím diagramu:

![Přesunout objekt pro vytváření dat](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Jaká jsou výpočetní prostředí podporovaná nástrojem Data Factory?
Následující tabulka uvádí seznam výpočetních prostředí podporovaných Data Factory a aktivit, které na nich můžou běžet.

| Výpočetní prostředí | activities |
| --- | --- |
| [Cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[Dotnet](data-factory-use-custom-activities.md), [podregistr](data-factory-hive-activity.md), [prase](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [streamování Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klasický)](data-factory-compute-linked-services.md#azure-machine-learning-studio-classic-linked-service) |[Aktivity studia (Classic): dávkové provádění a aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[U-SQL Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure Synapse Analytics](data-factory-compute-linked-services.md#azure-synapse-analytics-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Uložená procedura](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Jak Azure Data Factory porovnat s služba SSIS (SQL Server Integration Services) (SSIS)? 
Podívejte se na prezentaci [Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) z některého z našich odborníků MVP (nejvíc vyhodnocených profesionálů): Reza rad. Některé nedávné změny v Data Factory nemusí být uvedeny v balíčku snímků. Nepřetržitě přidáváme další možnosti pro Azure Data Factory. Nepřetržitě přidáváme další možnosti pro Azure Data Factory. Tyto aktualizace budeme začlenit do porovnání technologií pro integraci dat od Microsoftu po celý tento rok.   

## <a name="activities---faq"></a>Aktivity – Nejčastější dotazy
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Jaké jsou různé typy aktivit, které můžete použít v kanálu Data Factory?
* [Aktivity přesunu dat](data-factory-data-movement-activities.md) pro přesun dat.
* [Aktivity transformace dat](data-factory-data-transformation-activities.md) pro zpracování/transformaci dat.

### <a name="when-does-an-activity-run"></a>Kdy se spustí aktivita?
Nastavení konfigurace **dostupnosti** v tabulce výstupní data Určuje, kdy se aktivita spustí. Pokud jsou zadány vstupní datové sady, aktivita před spuštěním zkontroluje, jestli jsou všechny závislosti vstupních dat splněné (stav **připraveno** ).

## <a name="copy-activity---faq"></a>Aktivita kopírování – Nejčastější dotazy
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Je lepší mít pro každou aktivitu kanál s více aktivitami nebo samostatný kanál?
Kanály by měly seskupit související aktivity. Pokud datové sady, které je spojují, nespotřebovává žádná jiná aktivita mimo kanál, můžete aktivity uchovávat v jednom kanálu. Tímto způsobem nemusíte řetězit aktivní období kanálu, aby se vzájemně narovnaly. Integrita dat v tabulkách vnitřních s tímto kanálem je také při aktualizaci kanálu lépe zachovaná. Aktualizace kanálu v podstatě zastaví všechny aktivity v rámci kanálu, odebere je a znovu vytvoří. Z perspektivy vytváření obsahu může být také snazší sledovat tok dat v rámci souvisejících aktivit v jednom souboru JSON pro daný kanál.

### <a name="what-are-the-supported-data-stores"></a>Jaké jsou podporovaná úložiště dat?
Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Data Factory podporuje následující typy úložišť dat. Data z libovolného zdroje lze zapsat do libovolné jímky. Kliknutím na úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * mohou být místní nebo v Azure IaaS a vyžadují nainstalování [Brány správy dat](data-factory-data-management-gateway.md) na místním počítači nebo na počítači Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Jaké jsou podporované formáty souborů?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Kde se provádí operace kopírování?
Podrobnosti najdete v části [globálně dostupné přesuny dat](data-factory-data-movement-activities.md#global) . Pokud se jedná o místní úložiště dat, je operace kopírování prováděna Správa dat bránou ve vašem místním prostředí. A když přesun dat mezi dvěma cloudových úložišť, provede se operace kopírování v oblasti nejbližší k umístění jímky ve stejné geografické oblasti.

## <a name="hdinsight-activity---faq"></a>Aktivita HDInsight – Nejčastější dotazy
### <a name="what-regions-are-supported-by-hdinsight"></a>Které oblasti podporuje služba HDInsight?
Podívejte se na část geografické dostupnosti v následujícím článku: nebo v [podrobnostech o cenách HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Jakou oblast používá cluster HDInsight na vyžádání?
Cluster HDInsight na vyžádání se vytvoří ve stejné oblasti, ve které existuje úložiště, které jste zadali pro použití s clusterem.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Jak přidružit další účty úložiště ke clusteru HDInsight?
Pokud používáte vlastní cluster HDInsight (BYOC-Přineste si vlastní cluster), přečtěte si následující témata:

* [Použití clusteru HDInsight s alternativním účtem úložiště a Metaúložiště][hdinsight-alternate-storage]
* [Použití dalších účtů úložiště s podregistrem HDInsight][hdinsight-alternate-storage-2]

Pokud používáte cluster na vyžádání, který je vytvořený službou Data Factory, určete další účty úložiště pro propojenou službu HDInsight, aby je služba Data Factory mohla zaregistrovat vaším jménem. V definici JSON pro propojenou službu na vyžádání použijte vlastnost **additionalLinkedServiceNames** k určení alternativních účtů úložiště, jak je znázorněno v následujícím fragmentu kódu JSON:

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
V příkladu výše otherLinkedServiceName1 a otherLinkedServiceName2 představuje propojené služby, jejichž definice obsahují přihlašovací údaje, které cluster HDInsight potřebuje k přístupu k alternativním účtům úložiště.

## <a name="slices---faq"></a>Řezy – Nejčastější dotazy
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Proč nejsou moje vstupní řezy ve stavu připraveno?
Společná chyba nenastavuje vlastnost **External** na vstupní datovou sadu na **true** , když vstupní data jsou externí pro objekt pro vytváření dat (nevyráběné objektem pro vytváření dat).

V následujícím příkladu stačí nastavit vlastnost **External** na hodnotu true v **DataSet1.**.  

**DataFactory1** Kanál 1: DataSet1.-> Activity1-> Dataset2-> "Activity2"-> dataset3 Pipeline 2: dataset3-> activity3-> dataset4

Pokud máte jiný objekt pro vytváření dat s kanálem, který přebírá dataset4 (vyprodukovaný kanálem 2 ve službě Data Factory 1), označte dataset4 jako externí datovou sadu, protože datová sada je vytvořena jinou datovou továrnou (DataFactory1, ne DataFactory2).  

**DataFactory2**    
Kanál 1: dataset4->activity4->dataset5

Pokud je vlastnost External nastavena správně, ověřte, zda vstupní data existují v umístění zadaném v definici vstupní datové sady.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Jak spustit řez v jiné době než půlnoc, kdy se řez vyrábí každý den?
Pomocí vlastnosti **posunutí** určete čas, kdy se má řez vyrobit. Podrobnosti o této vlastnosti najdete v části [dostupnost datové sady](data-factory-create-datasets.md#dataset-availability) . Tady je rychlý příklad:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Denní řezy začínají v **6** , ale ne jako výchozí půlnoc.     

### <a name="how-can-i-rerun-a-slice"></a>Jak můžu znovu spustit řez?
Řez můžete znovu spustit jedním z následujících způsobů:

* Pomocí možnosti monitorování a Správa aplikace znovu spusťte okno nebo řez aktivity. Pokyny najdete v tématu opětovné [spuštění vybraných oken aktivity](data-factory-monitor-manage-app.md#perform-batch-actions) .   
* Na panelu příkazů v okně **datový řez** pro řez v Azure Portal klikněte na **Spustit** .
* Spusťte rutinu **set-AzDataFactorySliceStatus** se stavem nastaveným na **čekání** na řez.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Podrobnosti o této rutině naleznete v tématu [set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] .

### <a name="how-long-did-it-take-to-process-a-slice"></a>Jak dlouho trvá zpracování řezu?
Pomocí Průzkumníka oken aktivit v okně monitorování & spravovat aplikaci, abyste zjistili, jak dlouho trvalo zpracování datového řezu. Podrobnosti najdete v [Průzkumníkovi okna aktivity](data-factory-monitor-manage-app.md#activity-window-explorer) .

V Azure Portal můžete také provést následující akce:  

1. Klikněte na dlaždici datové **sady** v okně **objekt** pro vytváření dat pro vaši datovou továrnu.
2. V okně datové **sady** klikněte na konkrétní datovou sadu.
3. V seznamu **Poslední řezy** v okně **tabulka** Vyberte řez, který vás zajímá.
4. Klikněte na spuštění aktivit ze seznamu **spuštění aktivit** v okně **datový řez** .
5. V okně **Podrobnosti o spuštění aktivity** klikněte na dlaždici **vlastnosti** .
6. Mělo by se zobrazit pole **Trvání** s hodnotou. Tato hodnota je doba potřebná ke zpracování řezu.   

### <a name="how-to-stop-a-running-slice"></a>Jak zastavit běžící řez?
Pokud potřebujete zastavit spuštění kanálu, můžete použít rutinu [Suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) . V současné době pozastavení kanálu nezastaví provádění řezů, které probíhají. Po dokončení probíhajících spuštění se nevybere žádný další řez.

Pokud opravdu chcete okamžitě zastavit všechna spuštění, jediným způsobem by byl kanál odstranit a znovu ho vytvořit. Pokud se rozhodnete kanál odstranit, nemusíte odstraňovat tabulky a propojené služby používané kanálem.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: /archive/blogs/cindygross/use-additional-storage-accounts-with-hdinsight-hive