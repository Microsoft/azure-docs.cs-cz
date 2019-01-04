---
title: Azure Data Factory – nejčastější dotazy
description: Nejčastější dotazy ohledně služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 81c7c98f29c2e507e165a3943395e36a453cbf06
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024038"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory – nejčastější dotazy
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [časté otázky – Data Factory](../frequently-asked-questions.md).

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-data-factory"></a>Co je služba Azure Data Factory?
Data Factory je Cloudová služba integrace dat, která **automatizuje přesouvání a transformaci dat**. Stejně jako objekt factory, která spouští zařízení, ta vezmou suroviny a transformují je na hotové výroby Data Factory orchestruje stávající služby, které sbírají nezpracovaná data a transformují je na informace připravené k použití.

Data Factory umožňuje vytvářet pracovní postupy řízené daty pro přesun dat mezi s místními a cloudovými úložišti dat i zpracovat a transformovat data pomocí výpočetních služeb, jako je například Azure HDInsight a Azure Data Lake Analytics. Po vytvoření kanálu, který provádí akce, které potřebujete, můžete naplánovat jeho spuštění pravidelně (každou hodinu, denně, týdně atd.).   

Další informace najdete v tématu [přehled a klíčové koncepty](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Kde najdu informace o cenách pro Azure Data Factory?
Zobrazit [stránce s podrobnostmi ceny za Data Factory] [ adf-pricing-details] pro podrobnosti o cenách pro službu Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Jak můžu začít pracovat s Azure Data Factory?
* Přehled služby Azure Data Factory najdete v tématu [Úvod do služby Azure Data Factory](data-factory-introduction.md).
* Kurz o tom, jak **zkopírovat nebo přesunout data** pomocí aktivity kopírování, zobrazit [kopírování dat z Azure Blob Storage do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Kurz o tom, jak **transformovat data** pomocí aktivity Hivu HDInsight. Zobrazit [zpracovávat data pomocí skriptu Hive v clusteru Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Co je dostupnost oblastí služby Data Factory?
Data Factory je dostupná v **USA – západ** a **Severní Evropa**. Výpočetní a úložné služby používané objekty pro vytváření dat může být v jiných oblastech. Zobrazit [podporované oblasti](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Jaká jsou omezení na počet továren/kanály a aktivity/datové sady dat?
Zobrazit **omezení datové továrny Azure** část [předplatné Azure a limity, kvóty a omezení](../../azure-subscription-service-limits.md#data-factory-limits) článku.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Co je pro tvorbu/vývojářské prostředí pomocí služby Azure Data Factory?
Můžete vytvořit/vytvářet datové továrny pomocí jedné z následujících nástrojů nebo sad SDK:

* **Azure portal** okna Data Factory na webu Azure Portal poskytuje bohaté možnosti uživatelského rozhraní pro vytvoření datové továrny ad propojené služby. **Editoru služby Data Factory**, což je také součástí portálu, můžete snadno vytvářet propojené služby, tabulky, datové sady a kanály zadáním definice JSON pro tyto artefakty. Zobrazit [vytvoření prvního kanálu dat pomocí webu Azure portal](data-factory-build-your-first-pipeline-using-editor.md) příklad použití portal/editoru pro vytváření a nasazování datové továrny.
* **Visual Studio** sady Visual Studio můžete použít k vytvoření datové továrny Azure. Zobrazit [vytvoření prvního kanálu dat pomocí sady Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) podrobnosti.
* **Prostředí Azure PowerShell** naleznete v tématu [vytvořit a monitorovat datové továrny Azure pomocí Azure Powershellu](data-factory-build-your-first-pipeline-using-powershell.md) kurzu/návod pro vytvoření datové továrny pomocí prostředí PowerShell. Zobrazit [Reference k rutinám Data Factory] [ adf-powershell-reference] obsahu v knihovně MSDN úplnou dokumentaci o rutinách služby Data Factory.
* **Knihovna tříd rozhraní .NET** můžete programově vytvářet datové továrny pomocí Data Factory .NET SDK. Zobrazit [vytvářet, monitorovat a spravovat datové továrny pomocí sady .NET SDK](data-factory-create-data-factories-programmatically.md) návod k vytvoření datové továrny pomocí sady .NET SDK. Zobrazit [Reference knihovny tříd pro objekt pro vytváření dat] [ msdn-class-library-reference] úplnou dokumentaci o Data Factory .NET SDK.
* **Rozhraní REST API** rozhraní REST API, který je zveřejněn prostřednictvím služby Azure Data Factory můžete použít také k vytváření a nasazování datové továrny. Zobrazit [referencích rozhraní API REST služby Data Factory] [ msdn-rest-api-reference] úplnou dokumentaci o rozhraní REST API služby Data Factory.
* **Šablona Azure Resource Manageru** naleznete v tématu [kurzu: Sestavit svou první datovou továrnu Azure pomocí šablony Azure Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md) fo podrobnosti.

### <a name="can-i-rename-a-data-factory"></a>Můžete přejmenovat objekt pro vytváření dat?
Ne. Stejně jako ostatní prostředky Azure nelze změnit název datové továrny Azure.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Můžu přesunout objekt pro vytváření dat z jednoho předplatného Azure do jiného?
Ano. Použití **přesunout** tlačítka v okně data factory, jak je znázorněno v následujícím diagramu:

![Přesunutí objektu pro vytváření dat](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Co je výpočetní prostředí podporovaných službou Data Factory?
Následující tabulka obsahuje seznam podporovaných objektu pro vytváření dat a aktivity, které v nich dají spustit výpočetní prostředí.

| Výpočetní prostředí | activities |
| --- | --- |
| [Cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [streamování Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Služba Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Aktivity Machine Learning: Dávkové spouštění a aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[U-SQL Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [systému SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Uložená procedura](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Jak porovnat objektu pro vytváření dat Azure s SQL Server Integration Services (SSIS)? 
Zobrazit [vs Azure Data Factory. SSIS](http://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) prezentaci z jednoho z našich MVP (Most. Vážíme si toho Professionals): Reza Rad. Některé z nedávných změn ve službě Data Factory se nemusí nacházet na prezentace. Průběžně přidáváme další funkce do služby Azure Data Factory. Průběžně přidáváme další funkce do služby Azure Data Factory. Jsme bude obsahovat tyto aktualizace do porovnání dat integrace technologií od Microsoftu nějakou dobu konce tohoto roku.   

## <a name="activities---faq"></a>Aktivity – nejčastější dotazy
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Jaké jsou různé typy aktivit, které můžete použít v kanálech Data Factory?
* [Aktivity přesunu dat](data-factory-data-movement-activities.md) přesouvat data.
* [Aktivity transformace dat](data-factory-data-transformation-activities.md) aby zpracovala/transformovala data.

### <a name="when-does-an-activity-run"></a>Pokud aktivita spustit?
**Dostupnosti** určuje nastavení konfigurace ve výstupní tabulce dat při spuštění aktivity. Pokud jsou zadané vstupní datové sady, aktivita zkontroluje, zda jsou splněny všechny závislosti vstupní data (tedy **připravené** stavu) před spuštěním.

## <a name="copy-activity---faq"></a>Aktivita kopírování – nejčastější dotazy
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Je lepší mít kanál s více aktivit nebo samostatné kanálu pro každou aktivitu?
Kanály mají sadu souvisejících aktivit. Pokud nejsou spotřebované datové sady, které je propojují jinou aktivitu mimo kanálu, abyste mohli aktivity v jednom kanálu. Tímto způsobem, nebude potřeba aktivní období kanálu řetězec tak, aby jejich zarovnání bylo mezi sebou. Při aktualizaci kanálu navíc lépe zajištěná integrita dat v tabulkách interní do kanálu. Aktualizace kanálu v podstatě zastaví všechny aktivity v rámci kanálu, odebere a znovu vytvoří je. Z vývojového perspektivy, může také být snazší zjistit tok dat v rámci souvisejících činností v jednom souboru JSON pro příslušný kanál.

### <a name="what-are-the-supported-data-stores"></a>Co jsou podporovaných úložišť dat?
Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Data Factory podporuje následující typy úložišť dat. Data z libovolného zdroje lze zapsat do libovolné jímky. Kliknutím na úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * mohou být místní nebo v Azure IaaS a vyžadují nainstalování [Brány správy dat](data-factory-data-management-gateway.md) na místním počítači nebo na počítači Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Jaké jsou podporované formáty souborů?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Kde se provádí operaci kopírování?
Zobrazit [přesun globálně dostupné dat](data-factory-data-movement-activities.md#global) podrobné informace. Stručně řečeno když do místního úložiště dat je zahrnuta, operace kopírování je prováděno pomocí brány správy dat v místním prostředí. A při přesouvání dat mezi dvěma cloudovými úložišti operace kopírování se provádí v oblast nejbližší umístění jímky ve stejné zeměpisné oblasti.

## <a name="hdinsight-activity---faq"></a>Aktivita HDInsight – nejčastější dotazy
### <a name="what-regions-are-supported-by-hdinsight"></a>Které oblasti podporují HDInsight?
Naleznete v části s geografickou dostupností v následujícím článku: nebo [podrobnosti o cenách Azure HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Jaké oblasti se používá cluster HDInsight na vyžádání?
Cluster HDInsight na vyžádání se vytvoří ve stejné oblasti, kde úložiště, které jste zadali pro použití s clusteru existuje.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Postup přidružení dalších účtů úložiště pro váš cluster HDInsight?
Pokud používáte vlastní HDInsight Cluster (BYOC – přineste si vlastní Cluster), naleznete v následujících tématech:

* [Použití clusteru služby HDInsight s účty alternativní úložiště a Metaúložišti][hdinsight-alternate-storage]
* [Použití dalších účtů úložiště s HDInsight Hive][hdinsight-alternate-storage-2]

Pokud používáte cluster na vyžádání, který je vytvořen pomocí služby Data Factory, zadejte další účty úložiště pro HDInsight propojenou službu tak, aby služba Data Factory můžete zaregistrovat vaším jménem. V definici JSON propojené služby na vyžádání pomocí **additionalLinkedServiceNames** vlastnosti a určit alternativní účty, jak je znázorněno v následujícím fragmentu kódu JSON:

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
V předchozím příkladu představují otherLinkedServiceName1 a otherLinkedServiceName2 propojené služby, jejichž definice obsahuje přihlašovací údaje, které potřebuje přístup k účtům alternativní úložiště clusteru HDInsight.

## <a name="slices---faq"></a>Kolekce obsahuje nějaké řezy – nejčastější dotazy
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Proč je můj vstupních řezů není ve stavu Připraveno?
Běžnou chybou nenastavuje **externí** vlastnost **true** vstupní datové sady, pokud vstupní data jsou externí vzhledem k objektu pro vytváření dat (není vytvořený službou data factory).

V následujícím příkladu je jenom nutné nastavit **externí** na true **dataset1**.  

**DataFactory1** kanálu 1: dataset1 -> aktivity "activity1" -> dataset2 -> "activity2" -> kanál dataset3 2: dataset3 -> aktivita "activity3" -> dataset4

Pokud máte jinou datovou továrnu s kanálem, který přebírá dataset4 (vytvořený pomocí kanálu 2 ve službě data factory 1), označte dataset4 jako externí datové sady, protože datovou sadu vytváří různé datové továrny (DataFactory1, ne DataFactory2).  

**DataFactory2**    
Kanál 1: dataset4 -> aktivita "activity4" -> dataset5

Pokud je vlastnost external nastavená správně, ověřte, zda vstupní data existuje v umístění zadaném v definici vstupní datové sady.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Jak spustit určitý řez v jinou dobu, než když řez vytvářen každý den o půlnoci?
Použití **posun** vlastnosti a určit čas, který chcete řez bude vytvořen. Zobrazit [dostupnosti datové sady](data-factory-create-datasets.md#dataset-availability) podrobné informace o této vlastnosti. Tady je příklad:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Denní řezy začínají **6: 00** místo výchozí půlnoc.     

### <a name="how-can-i-rerun-a-slice"></a>Jak můžete znovu spustit určitý řez?
Můžete znovu spustit určitý řez v jednom z následujících způsobů:

* Pomocí monitorování a Správa aplikací spustit znovu okno aktivity nebo řezu. Zobrazit [znovu spustit vybranou okna aktivit](data-factory-monitor-manage-app.md#perform-batch-actions) pokyny.   
* Klikněte na tlačítko **spustit** panelu příkazů **datový ŘEZ** okno řezu na webu Azure Portal.
* Spustit **Set-AzureRmDataFactorySliceStatus** rutiny se stavem nastavena na **čekání** řez.   

    ```PowerShell
    Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
Zobrazit [Set-AzureRmDataFactorySliceStatus] [ set-azure-datafactory-slice-status] podrobné informace o rutině.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Jak dlouho to trvala zpracovat určitý řez?
Použití Průzkumníku okna aktivity v monitorování a Správa aplikace vědět, jak dlouho trvalo zpracování datový řez. Zobrazit [Průzkumníku okna aktivity](data-factory-monitor-manage-app.md#activity-window-explorer) podrobnosti.

Můžete také provést následující na webu Azure Portal:  

1. Klikněte na tlačítko **datových sad** na dlaždici **služby DATA FACTORY** okno datové továrny.
2. Kliknutím na konkrétní datové sady **datových sad** okno.
3. Vyberte řez, který vás zajímá z **poslední řezy** seznamu **tabulky** okno.
4. Klikněte na možnost spuštění z aktivit **spuštění aktivit** seznamu **datový ŘEZ** okno.
5. Klikněte na tlačítko **vlastnosti** na dlaždici **podrobnosti o spuštění aktivit** okno.
6. Měli byste vidět **doba trvání** pole s hodnotou. Tato hodnota je doba zpracování řezu.   

### <a name="how-to-stop-a-running-slice"></a>Postup zastavení spuštěného řez?
Pokud je třeba ukončit spuštění kanálu, můžete použít [Suspend-AzureRmDataFactoryPipeline](/powershell/module/azurerm.datafactories/suspend-azurermdatafactorypipeline) rutiny. V současné době pozastavení kanálu nezastaví řez spuštění, které jsou v průběhu. Po dokončení spuštění v průběhu je neexistoval žádný další řez.

Pokud Opravdu chcete okamžitě zabránit všech spuštění, jedině bude kanál odstranit a znovu vytvořit. Pokud se rozhodnete odstranit kanálu, není potřeba odstraňovat tabulky a propojené služby používané kanálu.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/azurerm.datafactories/
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/azurerm.datafactories/set-azurermdatafactoryslicestatus

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
