---
title: Porovnání služby Azure Data Factory s verzí 1 služby Data Factory | Microsoft Docs
description: Tento článek porovnává Azure Data Factory s verzí 1 služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 04/09/2018
ms.author: makromer
ms.openlocfilehash: f898b21e84cc57327991c97d2694e9e44e608417
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014892"
---
# <a name="compare-azure-data-factory-with-data-factory-version-1"></a>Porovnání služby Azure Data Factory s verzí 1 služby Data Factory
Tento článek porovnává Data Factory s verzí 1 služby Data Factory. Se službou Data Factory se můžete seznámit v článku [Úvod do Azure Data Factory](introduction.md). S verzí 1 této služby se můžete seznámit v článku [Úvod do Azure Data Factory](v1/data-factory-introduction.md). 

## <a name="feature-comparison"></a>Porovnání funkcí
V následující tabulce najdete porovnání funkcí služby Data Factory s funkcemi verze 1 služby Data Factory. 

| Funkce | Verze 1 | Aktuální verze | 
| ------- | --------- | --------- | 
| Datové sady | Pojmenované zobrazení dat s odkazem na data, která chcete ve svých aktivitách použít jako vstupy nebo výstupy. Datové sady identifikují data v rámci různých úložišť dat, jako jsou tabulky, soubory, složky a dokumenty. Například datová sada Azure Blob určuje kontejner objektů blob a složku úložiště Azure Blob, ze kterého má aktivita číst data.<br/><br/>**Dostupnost** definuje model dělení časového intervalu pro zpracování datové sady (například každou hodinu, každý den atd.). | V aktuální verzi jsou datové sady stejné. Nemusíte ale pro datové sady definovat plány **dostupnosti**. Můžete definovat aktivační prostředek, který může naplánovat kanály z paradigmatu plánovače hodin. Další informace najdete v tématech [Aktivační události](concepts-pipeline-execution-triggers.md#triggers) a [Datové sady](concepts-datasets-linked-services.md). | 
| Propojené služby | Propojené služby se velmi podobají připojovacím řetězcům. Tyto řetězce definují informace o připojení, které služba Data Factory potřebuje, aby se mohla připojit k externím prostředkům. | Propojené služby jsou stejné jako v Data Factory verze 1, ale mají novou vlastnost **connectVia**, která využívá výpočetní prostředí Integration Runtime aktuální verze služby Data Factory. Další informace najdete v tématech [Prostředí Integration Runtime v Azure Data Factory](concepts-integration-runtime.md) a [Vlastnosti propojených služeb pro Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties). |
| Kanály | Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které dohromady provádějí určitou úlohu. K naplánování a spuštění kanálů slouží vlastnosti startTime, endTime a isPaused. | Kanály jsou skupiny aktivit, které se provádějí s daty. Plánování aktivit v kanálu je ale rozdělené do nových prostředků aktivačních událostí. Kanály v aktuální verzi služby Data Factory si můžete představit spíše jako „jednotky pracovních postupů“, které plánujete samostatně prostřednictvím aktivačních událostí. <br/><br/>Kanály v aktuální verzi služby Data Factory nevyužívají spouštění v „časových intervalech“. Koncepce Data Factory verze 1 pro startTime, endTime a isPaused se v aktuální verzi Data Factory už nevyužívají. Další informace najdete v tématech [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md) a [Kanály a aktivity](concepts-pipelines-activities.md). |
| Aktivity | Aktivity definují akce, které se mají v kanálu provést s vašimi daty. Podporují se aktivity přesunu dat (aktivita kopírování) a transformace dat (jako je Hive, Pig a MapReduce). | I v aktuální verzi Data Factory jsou aktivity definovány v rámci kanálu. Aktuální verze Data Factory představuje nové [aktivity toku řízení](concepts-pipelines-activities.md#control-activities). Tyto aktivity používáte v toku řízení (smyčky a větvení). Aktivity přesunu a transformace dat podporované ve verzi 1 se podporují i v aktuální verzi. V aktuální verzi můžete definovat aktivity transformace bez použití datových sad. |
| Hybridní přesun dat a odesílání aktivit | Nově se používá označení Integration Runtime. [Brána správy dat](v1/data-factory-data-management-gateway.md) podporovala přesun dat mezi místním prostředím a cloudem.| Brána správy dat se teď nazývá modul Integration Runtime (v místním prostředí). Nabízí stejné funkce jako verze 1. <br/><br/> Azure-SSIS Integration Runtime v aktuální verzi Data Factory také podporuje nasazování a spouštění balíčků služby SQL Server Integration Services (SSIS) v cloudu. Další informace najdete v tématu [Prostředí Integration Runtime v Azure Data Factory](concepts-integration-runtime.md).|
| Parametry | Není k dispozici | Parametry jsou páry klíč-hodnota. Jsou určené jen ke čtení a slouží k nastavení konfigurace. Definují se v kanálech. Pokud kanál spouštíte ručně, můžete parametrům předat argumenty. Pokud používáte aktivační událost plánovače, může tato aktivační událost také předávat hodnoty parametrů. Aktivity v rámci kanálu využívají hodnoty parametrů.  |
| Výrazy | Data Factory V1 umožňuje používat funkce a systémové proměnné v dotazech pro výběr dat a ve vlastnostech aktivit nebo datových sad. | V aktuální verzi Data Factory můžete používat výrazy kdekoli v řetězcové hodnotě JSON. Další informace najdete v článku, který se věnuje [výrazům a funkcím v aktuální verzi Data Factory](control-flow-expression-language-functions.md).|
| Spuštění kanálu | Není k dispozici | Jedna instance spuštění kanálu. Například si představte, že máte kanál, který se spouští v 8:00, 9:00 a 10:00. V uvedeném případě existují tři samostatná spuštění kanálu. Každé spuštění kanálu má jedinečné ID spuštění. Tento identifikátor GUID jedinečným způsobem definuje konkrétní spuštění kanálu. Spuštění kanálu se obvykle inicializuje předáním argumentů parametrům definovaným v kanálech. |
| Spuštění aktivit | Není k dispozici | Instance spuštění aktivity v kanálu. | 
| Spuštění aktivačních událostí | Není k dispozici | Instance spuštění aktivační události. Další informace najdete v tématu [Aktivační události](concepts-pipeline-execution-triggers.md). |
| Plánování | Plánování je založené na počátečním/koncovém času kanálu a dostupnosti datové sady. | Aktivační událost plánovače nebo spuštění prostřednictvím externího plánovače. Další informace najdete v tématu [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md). |

V následujících oddílech najdete další informace o možnostech aktuální verze. 

## <a name="control-flow"></a>Tok řízení  
Kvůli podpoře různorodých integračních toků a vzorů moderního datového skladu nabízí aktuální verze služby Data Factory nový flexibilní model datového kanálu, který není spojený s časovými řadami dat. Teď je povoleno několik běžných toků, což dříve nebylo možné. Tyto toky jsou popsané v následujících oddílech.   

### <a name="chaining-activities"></a>Řetězení aktivit
Pokud jste chtěli aktivity řetězit, museli jste ve verzi 1 nakonfigurovat výstup jedné aktivity jako vstup druhé aktivity. V aktuální verzi můžete v rámci kanálu řetězit aktivity v sekvenci. Můžete v definici aktivity použít vlastnost **dependsOn** pro řetězení s upstreamovou aktivitou. Další informace a příklad najdete v článcích [Kanály a aktivity](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) a [Větvení a řetězení aktivit](tutorial-control-flow.md). 

### <a name="branching-activities"></a>Větvení aktivit
V aktuální verzi můžete v rámci kanálu aktivity větvit. [Aktivita podmínky If](control-flow-if-condition-activity.md) funguje stejně jako příkaz `if` v programovacích jazycích. Vyhodnotí sadu aktivit, když se podmínka vyhodnotí jako `true`, a jinou sadu aktivit, když se podmínka vyhodnotí jako `false`. Příklady větvení aktivit najdete v kurzu [Větvení a řetězení aktivit](tutorial-control-flow.md).

### <a name="parameters"></a>Parametry 
Parametry můžete definovat na úrovni kanálu a při volání kanálu na vyžádání nebo pomocí aktivační události potom předávat argumenty. Aktivity mohou využívat argumenty předávané do kanálu. Další informace najdete v tématu [Kanály a aktivační události](concepts-pipeline-execution-triggers.md). 

### <a name="custom-state-passing"></a>Předávání vlastního stavu
Výstupy aktivity včetně stavu může používat následující aktivita v kanálu. V definici JSON aktivity můžete například pomocí následující syntaxe získat přístup k výstupu předchozí aktivity: `@activity('NameofPreviousActivity').output.value`. Tato funkce umožňuje sestavovat pracovní postupy, ve kterých si aktivity předávají hodnoty.

### <a name="looping-containers"></a>Kontejnery smyček
[Aktivita ForEach](control-flow-for-each-activity.md) definuje opakovaný tok řízení ve vašem kanálu. Tato aktivita iteruje kolekcí a spouští určené aktivity ve smyčce. Smyčková implementace této aktivity se podobá struktuře smyčky Foreach používané v programovacích jazycích. 

Aktivita [Until](control-flow-until-activity.md) funguje stejně jako smyčka Do-Until v programovacích jazycích. Provádí ve smyčce sadu aktivit, dokud není podmínka spojená s aktivitou vyhodnocená jako `true`. Ve službě Data Factory můžete pro aktivitu Until určit hodnotu časového limitu.  

### <a name="trigger-based-flows"></a>Toky založené na aktivačních událostech
Kanály se můžou aktivovat na vyžádání (na základě události, tj. odeslání objektu blob) nebo v nastavenou hodinu. Podrobné informace o aktivačních událostech najdete v článku [Kanály a aktivační události](concepts-pipeline-execution-triggers.md). 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>Volání kanálu z jiného kanálu
[Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md) umožňuje kanálu služby Data Factory volat jiný kanál.

### <a name="delta-flows"></a>Toky rozdílů
Nejdůležitějším případem použití ve vzorech ETL je „rozdílové načtení“, kdy se načítají jen data, která se změnila od poslední iterace kanálu. Nové možnosti v aktuální verzi, jako jsou [aktivita vyhledávání](control-flow-lookup-activity.md), flexibilní plánování a tok řízení, přirozeným způsobem podporují tento případ použití. Kurz s podrobnými pokyny najdete v tématu [kurzu: Přírůstkové kopírování](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Další aktivity toku řízení
V dalším textu najdete popis několika dalších aktivit toku řízení podporovaných v aktuální verzi služby Data Factory. 

Aktivita řízení | Popis
---------------- | -----------
[Aktivita ForEach](control-flow-for-each-activity.md) | Definuje v kanálu opakovaný tok řízení. Tato aktivita se používá k iteraci v kolekci. Určené aktivity se spouští ve smyčce. Implementace smyčky této aktivity se podobá struktuře smyčky Foreach v programovacích jazycích.
[Webová aktivita](control-flow-web-activity.md) | Volá z kanálu Data Factory vlastní koncový bod REST. Můžete předávat datové sady a propojené služby, které má aktivita používat a ke kterým má mít přístup. 
[Aktivita Lookup](control-flow-lookup-activity.md) | Načte z externího zdroje (nebo v něm vyhledá) hodnotu názvu záznamu nebo tabulky. Na tento výstup mohou dále odkazovat následující aktivity. 
[Aktivita GetMetadata](control-flow-get-metadata-activity.md) | Načte metadata jakýchkoli dat v Azure Data Factory. 
[Aktivita Wait](control-flow-wait-activity.md) | Na určitou dobu pozastaví kanál.

## <a name="deploy-ssis-packages-to-azure"></a>Nasazení balíčků služby SSIS do Azure 
Služba Azure-SSIS se používá k přesunutí úloh SSIS do cloudu, k vytvoření datové továrny v aktuální verzi a ke zřízení prostředí Azure-SSIS Integration Runtime.

Azure-SSIS Integration Runtime je plně spravovaný cluster virtuálních počítačů Azure (uzlů) vyhrazených ke spouštění balíčků SSIS v cloudu. Po zřízení Azure-SSIS Integration Runtime můžete stejné nástroje, které jste použili k nasazení balíčků SSIS, použít i v místním prostředí SSIS. 

K nasazení balíčků SSIS do tohoto modulu runtime v Azure můžete použít třeba SQL Server Data Tools nebo SQL Server Management Studio. Podrobné pokyny najdete v kurzu [Nasazení balíčků služby SSIS (SQL Server Integration Services) do Azure](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="flexible-scheduling"></a>Flexibilní plánování
V aktuální verzi služby Data Factory nemusíte pro datové sady definovat plány dostupnosti. Můžete definovat aktivační prostředek, který může naplánovat kanály z paradigmatu plánovače hodin. Pokud chcete, aby byl plánovací a prováděcí model flexibilní, můžete kanálům předávat parametry aktivační událostí. 

Kanály v aktuální verzi služby Data Factory nevyužívají spouštění v „časových intervalech“. Koncepce Data Factory verze 1 pro startTime, endTime a isPaused se v aktuální verzi Data Factory už nevyužívají. Další informace o sestavení a naplánování kanálu v aktuální verzi služby Data Factory najdete v článku [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>Podpora pro další úložiště dat
Aktuální verze podporuje kopírování dat do více úložišť nebo z více úložišť dat než verze 1. Seznam podporovaných úložišť dat najdete v následujících článcích:

- [Verze 1 – Podporovaná úložiště dat](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Aktuální verze – Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Podpora pro cluster Spark na vyžádání
Aktuální verze podporuje vytvoření clusteru Azure HDInsight Spark na vyžádání. Pokud chcete vytvořit cluster Spark na vyžádání, zadejte v definici propojené služby HDInsight na vyžádání jako typ clusteru Spark. Potom můžete aktivitu Spark nakonfigurovat ve svém kanálu tak, aby používala tuto propojenou službu. 

Při spuštění této aktivity za běhu služba Data Factory automaticky vytvoří cluster Spark. Další informace najdete v následujících článcích:

- [Aktivita Spark v aktuální verzi služby Data Factory](transform-data-using-spark.md)
- [Propojená služba Azure HDInsight na vyžádání](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Vlastní aktivity
Ve verzi 1 implementujete (vlastní) kód aktivity DotNet tak, že projekt knihovny tříd .NET vytvoříte třídou, která implementuje metodu Execute rozhraní IDotNetActivity. Proto je potřeba napsat vlastní kód v .NET Frameworku 4.5.2 a spustit ho v uzlech služby Azure Batch Pool se systémem Windows. 

Ve vlastní aktivitě v aktuální verzi nemusíte implementovat rozhraní .NET. Můžete přímo spouštět příkazy, skripty a vlastní kód, který je zkompilovaný jako spustitelný soubor. 

Další informace najdete v článku, který se věnuje [rozdílu mezi vlastní aktivitou ve službě Data Factory a ve verzi 1](transform-data-using-dotnet-custom-activity.md#compare-v2-v1).

## <a name="sdks"></a>Sady SDK
 Aktuální verze služby Data Factory poskytuje širší nabídku sad SDK, které je možné využít k vytváření, správě a monitorování kanálů.

- **SADY .NET SDK**: Sada .NET SDK je aktualizovaná v aktuální verzi.

- **Prostředí PowerShell**: Rutiny Powershellu jsou aktualizované v aktuální verzi. Rutiny pro aktuální verzi mají **DataFactoryV2** název, například: Get-AzureRmDataFactoryV2. 

- **Python SDK**: Tato sada SDK je nového v aktuální verzi.

- **ROZHRANÍ REST API**: Rozhraní REST API se aktualizuje v aktuální verzi. 

Sady SDK aktualizované v aktuální verzi nejsou zpětně kompatibilní s klienty verze 1. 

## <a name="authoring-experience"></a>Prostředí pro vytváření

| &nbsp; | V2 | V1 |
| ------ | -- | -- | 
| portál Azure | [Ano](quickstart-create-data-factory-portal.md) | [Ano](data-factory-build-your-first-pipeline-using-editor.md) |
| Azure PowerShell | [Ano](quickstart-create-data-factory-powershell.md) | [Ano](data-factory-build-your-first-pipeline-using-powershell.md) |
| .NET SDK | [Ano](quickstart-create-data-factory-dot-net.md) | [Ano](data-factory-build-your-first-pipeline-using-vs.md) |
| REST API | [Ano](quickstart-create-data-factory-rest-api.md) | [Ano](data-factory-build-your-first-pipeline-using-rest-api.md) |
| Python SDK | [Ano](quickstart-create-data-factory-python.md) | Ne |
| Šablona Resource Manageru | [Ano](quickstart-create-data-factory-resource-manager-template.md) | [Ano](data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>Role a oprávnění

K vytváření a správě prostředků aktuální verze služby Data Factory je možné použít roli přispěvatele služby Data Factory verze 1. Další informace najdete v článku o [přispěvateli Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

## <a name="monitoring-experience"></a>Prostředí pro monitorování
V aktuální verzi můžete k monitorování datových továren použít také [Azure Monitor](monitor-using-azure-monitor.md). Nové rutiny PowerShellu podporují monitorování prostředí [Integration Runtime](monitor-integration-runtime.md). Verze 1 i 2 podporují vizuální monitorování prostřednictvím monitorovací aplikace, kterou můžete spustit z webu Azure Portal.


## <a name="next-steps"></a>Další postup
Další informace o vytvoření datové továrny pomocí podrobných pokynů v následujících úvodních příručkách: [Prostředí PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [rozhraní REST API](quickstart-create-data-factory-rest-api.md). 
