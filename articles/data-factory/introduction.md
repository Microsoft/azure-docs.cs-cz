---
title: Seznámení s Azure Data Factory | Microsoft Docs
description: Seznamte se s Azure Data Factory, cloudovou službou pro integraci dat, která orchestruje a automatizuje přesun a transformaci dat.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: overview
ms.date: 09/30/2019
ms.openlocfilehash: 7bc03e80fc49756d19677edbef6bd8d372849732
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937259"
---
# <a name="what-is-azure-data-factory"></a>Co je Azure Data Factory?

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-introduction.md)
> * [Aktuální verze](introduction.md)

Ve světě velkých objemů dat jsou nezpracované, neuspořádaná data často uložená v relačních, nerelačních a jiných systémech úložiště. Nezpracované data ale nemají na svou vlastní kontext ani význam, aby bylo možné poskytnout smysluplné poznatky analytikům, datovým vědcům nebo tvůrcům podnikových rozhodnutí. 

Pro velké objemy dat je potřeba služba, která dokáže orchestrovat a zprovoznění procesy pro upřesnění těchto mimořádných úložišť nezpracovaných dat na užitečné obchodní přehledy. Azure Data Factory je spravovaná cloudová služba vytvořená pro tyto komplexní projekty hybridní extrakce, transformace a načítání (ETL), extrakce, načítání a transformace (ELT) a integrace dat.

Představte si například herní společnost, která shromažďuje petabajty herních protokolů vytvořených hrami v cloudu. Společnost chce tyto protokoly analyzovat, aby získala přehled o preferencích zákazníků, demografických údajích a způsobu použití. Také chce identifikovat příležitosti pro prodej a vzájemné prodeje, vyvíjet působivé nové funkce, řídit růst a poskytovat zákazníkům lepší zkušenosti.

Aby mohla společnost tyto protokoly analyzovat, potřebuje použít referenční data, jako jsou informace o zákaznících, herních informacích a marketingových kampaních, které jsou uložené v místním úložišti dat. Společnost chce využít tato data z místního úložiště dat a zkombinovat je s dalšími daty protokolů, která má v cloudovém úložišti dat. 

Aby bylo možné extrahovat přehledy, je hodlá zpracovávat připojená data pomocí clusteru Spark v cloudu (Azure HDInsight) a publikovat transformovaná data do cloudového datového skladu, jako je například Azure SQL Data Warehouse, abyste mohli snadno sestavit sestavu nad ní. Chtějí tento pracovní postup automatizovat a monitorovat a spravovat podle denního plánu. Chtějí je také spustit, když se soubory nacházejí v kontejneru úložiště objektů BLOB.

Azure Data Factory je platforma, která tyto scénáře dat řeší. Je to *cloudová služba pro integraci dat, která umožňuje vytvářet pracovní postupy řízené daty v cloudu za účelem Orchestrace a automatizace přesunu dat a transformace dat*. Pomocí Azure Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (nazývané kanály), které mohou ingestovat data z různorodých úložišť dat. Může zpracovávat a transformovat data pomocí výpočetních služeb, jako jsou Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics a Azure Machine Learning. 

Kromě toho můžete publikovat výstupní data do úložišť dat, jako jsou Azure SQL Data Warehouse pro aplikace business intelligence (BI), které se mají spotřebovat. V konečném důsledku můžete prostřednictvím Azure Data Factory organizovat nezpracovaná data v smysluplných úložištích dat a datových lakůch pro lepší obchodní rozhodnutí.

![Zobrazení nejvyšší úrovně Data Factory](media/introduction/big-picture.png)

## <a name="how-does-it-work"></a>Jak to funguje?
Kanály (pracovní postupy řízené daty) v Azure Data Factory obvykle provádějí tyto čtyři kroky:

![Čtyři kroky pracovního postupu na základě dat](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Připojit a shromáždit

Podniky mají data různých typů, která se nacházejí v různorodých místních zdrojích, v cloudu, strukturovaná, nestrukturovaná a částečně strukturovaná, a to vše v různých intervalech a rychlostech. 

Prvním krokem při sestavování systému vytváření informací je připojení ke všem požadovaným zdrojům dat a zpracování, jako jsou například služby typu software jako služba (SaaS), databáze, sdílené složky a webové služby FTP. Dalším krokem je přesun dat podle potřeby do centralizovaného umístění pro následné zpracování.

Bez Data Factory musí podniky vytvářet vlastní součásti pro přesun dat nebo psát vlastní služby pro integraci těchto zdrojů a zpracování dat. Integrace a údržba takových systémů je nákladná a obtížná. Kromě toho často postrádají monitorování, upozorňování na podnikové úrovni a ovládací prvky, které může nabídnout plně spravovaná služba.

Pomocí Data Factory můžete [aktivitu kopírování](copy-activity-overview.md) v datovém kanálu použít k přesunu dat z místních i cloudových úložišť dat do centralizovaného úložiště dat v cloudu pro další analýzu. Data můžete například shromažďovat v Azure Data Lake Storage a později je transformovat pomocí výpočetní služby Azure Data Lake Analytics. Můžete také shromažďovat data v úložišti objektů BLOB v Azure a později je transformovat pomocí Azure HDInsight Hadoopho clusteru.

### <a name="transform-and-enrich"></a>Transformace a obohacení
Po zobrazení dat v centralizovaném úložišti dat v cloudu zpracujte nebo Transformujte shromážděná data pomocí výpočetních služeb, jako je HDInsight Hadoop, Spark, Data Lake Analytics a Machine Learning. Chcete spolehlivě vytvořit transformovaná data s udržovatelným a kontrolovaným plánem, který bude zajišťovat produkční prostředí s důvěryhodnými daty.

### <a name="publish"></a>Opětovn
Po přepracování nezpracovaných dat do spotřebního formuláře připraveného pro podnikání načtěte data do služby Azure Data Warehouse, Azure SQL Database, Azure CosmosDB nebo podle jakéhokoli analytického modulu, na který můžou vaše firemní uživatelé nasměrovat ze svých business intelligencech nástrojů.

### <a name="monitor"></a>Sledován
Po úspěšném vytvoření a nasazení kanálu pro integraci dat, který poskytuje obchodní hodnotu z rafinovaných dat, Sledujte naplánované aktivity a kanály pro úspěšnost a rychlost selhání. Azure Data Factory obsahuje integrovanou podporu monitorování kanálů prostřednictvím Azure Monitor, rozhraní API, PowerShellu, protokolů Azure Monitor a panelů stavu na Azure Portal.

## <a name="top-level-concepts"></a>Koncepce nejvyšší úrovně
Předplatné Azure může mít jednu nebo víc instancí Azure Data Factory (nebo datových továren). Azure Data Factory se skládá ze čtyř klíčových komponent. Tyto součásti společně poskytují platformu, na které můžete vytvářet pracovní postupy řízené daty s kroky pro přesun a transformaci dat.

### <a name="pipeline"></a>Kanálu
Datová továrna může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které provádí pracovní jednotku. Aktivity v kanálu společně provádějí úlohu. Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure, a potom spustí dotaz na podregistr v clusteru HDInsight, aby data mohla rozdělit na oddíly. 

Výhodou je to, že kanál umožňuje spravovat aktivity jako sadu, a nikoli jednotlivě spravovat. Aktivity v kanálu je možné zřetězit, aby fungovaly sekvenčně, nebo můžou pracovat nezávisle.

### <a name="activity"></a>Aktivita
Aktivity reprezentují krok zpracování v kanálu. Aktivitu kopírování můžete například použít ke kopírování dat z jednoho úložiště dat do jiného úložiště dat. Podobně můžete použít aktivitu podregistru, která spouští dotaz na podregistr v clusteru Azure HDInsight, aby bylo možné transformovat nebo analyzovat vaše data. Data Factory podporuje tři typy aktivit: aktivity přesunu dat, aktivity transformace dat a aktivity řízení.

### <a name="datasets"></a>Datové sady
Datové sady reprezentují datové struktury v rámci úložišť dat, které jednoduše odkazují na data, která chcete ve svých aktivitách použít jako vstupy nebo výstupy, a odkazují na ně. 

### <a name="linked-services"></a>Propojené služby
Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které jsou potřebné pro Data Factory připojení k externím prostředkům. Zamyslete se jím takto: propojená služba definuje připojení ke zdroji dat a datová sada představuje strukturu dat. Například propojená služba Azure Storage specifikuje připojovací řetězec pro připojení k účtu Azure Storage. Datová sada objektů BLOB v Azure navíc určuje kontejner objektů BLOB a složku obsahující data.

Propojené služby se používají pro dva účely v Data Factory:

- Představuje **úložiště dat** , které zahrnuje, ale není omezené na místní SQL Server databázi, Oracle Database, sdílenou složku nebo účet Azure Blob Storage. Seznam podporovaných úložišť dat najdete v článku o [aktivitě kopírování](copy-activity-overview.md) .

- Představuje **výpočetní prostředek** , který může hostovat provádění aktivity. Například aktivita HDInsightHive běží v clusteru HDInsight Hadoop. Seznam aktivit transformace a podporovaných výpočetních prostředí najdete v článku věnovaném [transformaci dat](transform-data.md) .

### <a name="triggers"></a>Aktivační procedury
Triggery reprezentují jednotku zpracování, která určuje, kdy musí být spuštěno spuštění kanálu. Existují různé typy triggerů pro různé typy událostí.

### <a name="pipeline-runs"></a>Spuštění kanálu
Spuštění kanálu je instance provádění kanálu. Spuštění kanálu jsou obvykle vytvořena pomocí předání argumentů parametrům, které jsou definovány v kanálech. Argumenty je možné předat ručně nebo v rámci definice triggeru.

### <a name="parameters"></a>Parametry
Parametry jsou páry klíč-hodnota konfigurace jen pro čtení.  Parametry jsou definované v kanálu. Argumenty pro definované parametry jsou předány během provádění z kontextu spuštění, který byl vytvořen triggerem nebo kanálem, který byl proveden ručně. Aktivity v kanálu využívají hodnoty parametrů.

Datová sada je parametr silného typu a opakovaně použitelná/odkazovaná entita. Aktivita může odkazovat na datové sady a může využívat vlastnosti, které jsou definovány v definici datové sady.

Propojená služba je také parametr silného typu, který obsahuje informace o připojení k úložišti dat nebo výpočetnímu prostředí. Je to také opakovaně použitelná nebo odkazovaná entita.

### <a name="control-flow"></a>Tok řízení
Tok řízení je orchestrace aktivit kanálu, která zahrnuje zřetězení aktivit v sekvenci, větvení, definování parametrů na úrovni kanálu a předávání argumentů při volání kanálu na vyžádání nebo z aktivační události. Zahrnuje také vlastní průchozí a kontejnery pro vytváření smyček, tj. pro každý iterátor.


Další informace o Data Factory konceptech najdete v následujících článcích:

- [Datová sada a propojené služby](concepts-datasets-linked-services.md)
- [Kanály a aktivity](concepts-pipelines-activities.md)
- [Prostředí Integration runtime](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Podporované oblasti

Seznam oblastí Azure, ve kterých je Data Factory aktuálně k dispozici, vyberte oblasti, které vás zajímají na následující stránce, a pak rozbalte položku **Analytics** a vyhledejte **Data Factory**: [produkty dostupné v oblasti](https://azure.microsoft.com/global-infrastructure/services/). Datová továrna ale může získat přístup k úložištím dat a výpočetním službám v jiných oblastech Azure za účelem přesouvání dat mezi úložišti dat nebo zpracování dat pomocí výpočetních služeb.

Azure Data Factory sám o sobě neukládá žádná data. Umožňuje vytvářet pracovní postupy řízené daty k orchestraci přesouvání dat mezi podporovanými úložišti dat a zpracování dat pomocí výpočetních služeb v jiných oblastech nebo v místním prostředí. Umožňuje taky monitorovat a spravovat pracovní postupy pomocí mechanismů programování a uživatelského rozhraní.

I když je Data Factory k dispozici pouze v určitých oblastech, služba, která slouží k přesunu dat v Data Factory je dostupná globálně v několika oblastech. Pokud je úložiště dat za bránou firewall, místo toho se data v místním prostředí Integration Runtime nainstalovaná ve vašem místním prostředí přesunou.

Předpokládejme například, že vaše výpočetní prostředí, jako je cluster Azure HDInsight a Azure Machine Learning, běží mimo Západní Evropa oblast. Můžete vytvořit a použít instanci Azure Data Factory v Východní USA nebo Východní USA 2 a použít ji k plánování úloh ve výpočetních prostředích v Západní Evropa. Data Factory pro aktivaci úlohy ve výpočetním prostředí trvá několik milisekund, ale čas potřebný ke spuštění úlohy na výpočetním prostředí se nemění.

## <a name="accessibility"></a>Usnadnění

Činnost Data Factory uživatele v Azure Portal je přístupná.

## <a name="compare-with-version-1"></a>Porovnat s verzí 1
Seznam rozdílů mezi verzí 1 a aktuální verzí služby Data Factory najdete v tématu [porovnání s verzí 1](compare-versions.md). 

## <a name="next-steps"></a>Další kroky
Začněte vytvářením kanálu Data Factory pomocí jednoho z následujících nástrojů nebo sad SDK: 

- [Data Factory uživatelské rozhraní v Azure Portal](quickstart-create-data-factory-portal.md)
- [Nástroj Kopírování dat v Azure Portal](quickstart-create-data-factory-copy-data-tool.md)
- [Prostředí](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Šablona Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md)
 
