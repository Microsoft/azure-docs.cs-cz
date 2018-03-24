---
title: Pro vytváření dat Azure – nejčastější dotazy
description: Časté otázky k Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 387b5e944b0fd198e37a35d7bf1c54f7795c9f21
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Pro vytváření dat Azure – nejčastější dotazy
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [často kladené otázky - Data Factory verze 2](../frequently-asked-questions.md).

## <a name="general-questions"></a>Obecné otázky
### <a name="what-is-azure-data-factory"></a>Co je služba Azure Data Factory?
Data Factory je cloudových dat integrační služby, který **automatizuje přesouvání a transformaci dat**. Stejně jako objekt factory, který spouští zařízení, ta vezmou suroviny a transformují je na hotové výroby Data Factory orchestruje stávající služby, které sbírají nezpracovaná data a transformují je na informace připravené k použití.

Objekt pro vytváření dat můžete vytvořit datové pracovní postupy pro přesun dat mezi místní a cloudové úložiště dat jak procesu nebo transformace dat pomocí výpočetních služeb například Azure HDInsight a Azure Data Lake Analytics. Jakmile vytvoříte kanál, který provede akci, které potřebujete, můžete naplánovat jeho spuštění pravidelně (každou hodinu, denně, týdně atd.).   

Další informace najdete v tématu [přehled & klíčové koncepty](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Kde můžete najít podrobnosti o cenách pro vytváření dat Azure?
V tématu [stránku Podrobnosti o cenách na Data Factory] [ adf-pricing-details] pro podrobnosti o cenách služby Azure Data Factory.  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Jak můžu začít pracovat s Azure Data Factory?
* Přehled Azure Data Factory najdete v tématu [Úvod do Azure Data Factory](data-factory-introduction.md).
* Kurz o tom, jak **zkopírovat nebo přesunout data** pomocí aktivity kopírování, najdete v tématu [kopírování dat z Azure Blob Storage do Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Kurz o tom, jak **transformovat data** pomocí aktivity HDInsight Hive. V tématu [zpracování dat pomocí skriptu Hive v clusteru Hadoop](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Co je dostupnost služby Data Factory v oblastech?
Objekt pro vytváření dat je k dispozici v **USA – západ** a **Severní Evropa**. Výpočetní a úložné služby používá objekty pro vytváření dat může být v jiných oblastech. V tématu [podporované oblasti](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Jaká jsou omezení počtu data Factory/kanálů nebo aktivity nebo datových sad?
V tématu **Azure Data Factory omezení** části [předplatné Azure a omezení služby, kvóty a omezení](../../azure-subscription-service-limits.md#data-factory-limits) článku.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Co je rozhraní k vytváření/vývojáře službou Azure Data Factory?
Můžete vytvořit nebo vytvořit datové továrny pomocí jedné z následujících nástrojů nebo sady SDK:

* **Portál Azure** okna Data Factory na webu Azure portal poskytují bohaté uživatelské rozhraní pro vytvoření datové továrny ad propojené služby. **Editoru služby Data Factory**, což je také součástí portálu umožňuje snadno vytvářet propojené služby, tabulky, datových sad a kanálů zadáním definice JSON pro tyto artefakty. V tématu [sestavit svůj první kanál dat pomocí portálu Azure](data-factory-build-your-first-pipeline-using-editor.md) příklad k vytvoření a nasazení služby data factory pomocí portálu nebo editoru.
* **Visual Studio** Visual Studio můžete použít k vytvoření služby Azure data factory. V tématu [sestavit svůj první kanál dat pomocí sady Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) podrobnosti.
* **Prostředí Azure PowerShell** najdete v části [vytvořit a monitorování Azure Data Factory pomocí Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) kurzu nebo návod pro vytvoření objekt pro vytváření dat pomocí prostředí PowerShell. V tématu [referenční Data Factory] [ adf-powershell-reference] obsahu v knihovně MSDN úplnou dokumentaci o rutinách služby Data Factory.
* **Knihovna tříd rozhraní .NET** prostřednictvím kódu programu můžete vytvořit datové továrny pomocí .NET SDK služby Data Factory. V tématu [vytvořit, sledovat a spravovat data Factory pomocí sady .NET SDK](data-factory-create-data-factories-programmatically.md) pro návod, jak vytvořit objekt pro vytváření dat pomocí sady .NET SDK. V tématu [Reference knihovny tříd pro objekt pro vytváření dat] [ msdn-class-library-reference] úplnou dokumentaci o .NET SDK služby Data Factory.
* **Rozhraní REST API** rozhraní REST API, který je zveřejněný prostřednictvím služby Azure Data Factory můžete také použít vytvořit a nasadit datové továrny. V tématu [referenci rozhraní API REST pro vytváření dat] [ msdn-rest-api-reference] úplnou dokumentaci o REST API služby Data Factory.
* **Šablona Azure Resource Manageru** najdete v části [kurz: vytvoření vaší první pro vytváření dat Azure pomocí šablony Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) fo podrobnosti.

### <a name="can-i-rename-a-data-factory"></a>Můžete přejmenovat objekt pro vytváření dat?
Ne. Jako další prostředky Azure nelze změnit název služby Azure data factory.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Lze přesunout objekt pro vytváření dat z jedno předplatné do jiného?
Ano. Použití **přesunout** tlačítko na vaše okno objekt pro vytváření dat, jak je znázorněno v následujícím diagramu:

![Přesunout objekt pro vytváření dat](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Jaké jsou výpočetních prostředích podporovaných službou Data Factory?
Následující tabulka obsahuje seznam výpočetních prostředích nepodporuje objekt pro vytváření dat a aktivity, které můžete spustit na ně.

| Výpočetní prostředí | activities |
| --- | --- |
| [Cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) nebo [vlastní cluster HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [streamování Hadoop](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Aktivity Machine Learning: Dávkové spouštění a Aktualizace prostředku](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[U-SQL Data Lake Analytics](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL Data Warehouse](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [systému SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Uložená procedura](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Jak Azure Data Factory porovnat s integrační služby SSIS (SQL Server)? 
Najdete v článku [vs Azure Data Factory. SSIS](http://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) prezentace z jednoho z našich MVP (většina cenná Odborníci v oblasti): Reza Rad. Některé z posledních změn v datové továrně nemusí být uvedena v balíček snímků. Další možnosti nepřetržitě přidáváme do Azure Data Factory. Další možnosti nepřetržitě přidáváme do Azure Data Factory. Jsme se začlenit tyto aktualizace do porovnání technologiemi společnosti Microsoft pro integraci dat zopakovat později tohoto roku.   

## <a name="activities---faq"></a>Aktivity – nejčastější dotazy
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Jaké jsou různé typy aktivit, které můžete použít v kanálu pro vytváření dat?
* [Aktivity přesunu dat](data-factory-data-movement-activities.md) pro přesun dat.
* [Aktivity transformace dat](data-factory-data-transformation-activities.md) procesu nebo transformovat data.

### <a name="when-does-an-activity-run"></a>Spuštění aktivity
**Dostupnosti** nastavení konfigurace do výstupní tabulky dat určuje spuštění aktivity. Pokud jsou zadané vstupní datové sady, aktivity kontroluje, zda jsou splněny všechny závislosti vstupní data (tedy **připraven** stavu) předtím, než je spuštěn.

## <a name="copy-activity---faq"></a>Kopie aktivity – nejčastější dotazy
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Je lepší kanál s více aktivit nebo samostatné kanálu pro každou aktivitu?
Kanály by mělo sady souvisejících činností. Pokud každá další aktivita mimo kanálu nejsou spotřebované datové sady, které je propojují, můžete ponechat aktivity v jeden kanál. Tímto způsobem, nebude potřebujete aktivní období kanálu řetěz, aby mezi sebou. Při aktualizaci kanálu také lépe zajištěná integrita dat v tabulkách interní do kanálu. Kanálu aktualizace v podstatě zastaví všechny aktivity v rámci kanálu, je odstraní a vytvoří je znovu. Z vytváření perspektivy, může také být snazší zjistit tok dat v rámci souvisejících činností v jednom souboru JSON pro kanál.

### <a name="what-are-the-supported-data-stores"></a>Jaké jsou podporované datové úložiště?
Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Data Factory podporuje následující typy úložišť dat. Data z libovolného zdroje lze zapsat do libovolné jímky. Kliknutím na úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Úložiště dat s * mohou být místní nebo v Azure IaaS a vyžadují nainstalování [Brány správy dat](data-factory-data-management-gateway.md) na místním počítači nebo na počítači Azure IaaS.

### <a name="what-are-the-supported-file-formats"></a>Jaké jsou podporované formáty souborů?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Kde se provádí operace kopírování?
V tématu [přesun dat globálně dostupnou](data-factory-data-movement-activities.md#global) podrobnosti. Stručně řečeno když je potřebný místnímu úložišti dat, operace kopírování provádí Brána pro správu dat ve vašem místním prostředí. A pokud přesun dat mezi dvěma úložiště cloudu, operace kopírování se provádí v nejblíže k umístění podřízený ve stejné geografické oblasti.

## <a name="hdinsight-activity---faq"></a>Aktivita HDInsight – nejčastější dotazy
### <a name="what-regions-are-supported-by-hdinsight"></a>Které oblasti jsou podporovány v prostředí HDInsight?
Najdete v části s geografickou dostupností v následujícím článku: nebo [podrobnosti o cenách prostředí HDInsight][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Jaké oblasti je používán clusteru HDInsight na vyžádání?
Cluster HDInsight na vyžádání se vytvoří ve stejné oblasti, kde úložiště, které jste zadali pro použití s clusteru existuje.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Postup přidružení dalších účtů úložiště ke svému clusteru HDInsight?
Pokud používáte vlastní HDInsight Cluster (BYOC – přineste si vlastní Cluster), najdete v následujících tématech:

* [Použití clusteru služby HDInsight s účty alternativní úložiště a Metaúložišti][hdinsight-alternate-storage]
* [Použití dalších účtů úložiště s HDInsight Hive][hdinsight-alternate-storage-2]

Pokud používáte cluster služby na vyžádání, který vytváří služba Data Factory, zadejte další účty úložiště pro HDInsight propojená služba tak, aby služba Data Factory můžete zaregistrovat vaším jménem. V definici JSON pro propojenou službu na vyžádání pomocí **additionalLinkedServiceNames** vlastnost zadejte účty, alternativní úložiště, jak je znázorněno v následujícím fragmentu kódu JSON:

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
V předchozím příkladu otherLinkedServiceName1 a otherLinkedServiceName2 představují propojené služby, jejichž definice obsahovat přihlašovací údaje, které potřebuje přístup k účtům alternativní úložiště clusteru HDInsight.

## <a name="slices---faq"></a>Řezy – nejčastější dotazy
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Proč je můj vstupní řezy není ve stavu Připraveno?
Obvyklou chybou není nastavení **externí** vlastnost **true** na vstupní datovou sadu, když jsou vstupní data externí k objektu pro vytváření dat (není vyprodukované objektu pro vytváření dat).

V následujícím příkladu, stačí nastavit **externí** na hodnotu true na **dataset1**.  

**DataFactory1** kanálu 1: dataset1 -> aktivity "activity1" -> dataset2 -> "activity2" -> dataset3 kanálu 2: dataset3 -> aktivita "activity3" -> dataset4

Pokud máte jiný objekt pro vytváření dat s kanál, který přebírá dataset4 (vyprodukované kanálem 2 v datové továrně 1), protože datovou sadu je produkovaný jiný objekt pro vytváření dat (DataFactory1, není DataFactory2) označte dataset4 jako externí datové sady.  

**DataFactory2**    
Kanál 1: dataset4 -> aktivita "activity4" -> dataset5

Pokud je vlastnost external nastavená správně, ověřte, zda vstupní data existuje v umístění zadané v definici vstupní datové sady.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Jak spustit řez v jiné době než půlnoc, kdy řez vytvářen denně?
Použití **posun** vlastnosti a určit čas, kdy chcete, aby se řez se vytváří. V tématu [datovou sadu dostupnosti](data-factory-create-datasets.md#dataset-availability) část Podrobnosti o této vlastnosti. Tady je zběžný příklad:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Denní řezy začínají **6: 00** místo výchozí půlnoci.     

### <a name="how-can-i-rerun-a-slice"></a>Jak můžete znovu spustit řez?
Můžete znovu spustit řez v jednom z následujících způsobů:

* Znovu spustit období aktivity nebo řez pomocí monitorování a správě aplikací. V tématu [znovu spustit vybranou aktivity windows](data-factory-monitor-manage-app.md#perform-batch-actions) pokyny.   
* Klikněte na tlačítko **spustit** na panelu příkazů na **datový ŘEZ** pro příslušný řez na portálu Azure.
* Spustit **Set-AzureRmDataFactorySliceStatus** rutiny se stavem nastavena na **čekání** řez.   

    ```PowerShell
    Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
V tématu [Set-AzureRmDataFactorySliceStatus] [ set-azure-datafactory-slice-status] podrobnosti o rutině.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Jak dlouho trvalo zpracování řez?
Pomocí Průzkumníka okno aktivity v spravovat aplikace pro monitorování a potřebujete vědět, jak dlouho trvalo zpracování dat řezu. V tématu [aktivity okno Průzkumníka](data-factory-monitor-manage-app.md#activity-window-explorer) podrobnosti.

Můžete také provést následující na portálu Azure:  

1. Klikněte na tlačítko **datové sady** na dlaždici **DATA FACTORY** okno objektu pro vytváření dat.
2. Klikněte na konkrétní datové sady **datové sady** okno.
3. Vyberte řez, který se zajímáte z **poslední řezy** na seznamu **tabulky** okno.
4. Klikněte na aktivity při spuštění z **běh aktivit** na seznamu **datový ŘEZ** okno.
5. Klikněte na tlačítko **vlastnosti** na dlaždici **podrobnosti o spuštění aktivit** okno.
6. Měli byste vidět **doba trvání** pole s hodnotou. Tato hodnota je čas potřebný k zpracování řezu.   

### <a name="how-to-stop-a-running-slice"></a>Postup zastavení spuštěných řez?
Pokud je třeba ukončit kanál spuštění, můžete použít [Suspend-AzureRmDataFactoryPipeline](/powershell/module/azurerm.datafactories/suspend-azurermdatafactorypipeline) rutiny. V současné době pozastavení kanálu nezastaví spuštěních řez, které jsou v průběhu. Po spuštění v průběhu dokončit, je převzata žádné další řez.

Pokud Opravdu chcete zastavit všechny spuštěních okamžitě, jediným způsobem, jak by kanálu odstranit a vytvořit znovu. Pokud zvolíte možnost odstranit kanál, není potřeba odstranit tabulky a propojené služby v kanálu použije.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/azurerm.datafactories
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/set-azurermdatafactoryslicestatus

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
