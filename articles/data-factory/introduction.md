---
title: Úvod do služby Azure Data Factory | Dokumentace Microsoftu
description: Seznamte se se službou Azure Data Factory, cloudovou službou pro integraci dat, která orchestruje a automatizuje přesouvání a transformaci dat.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: shlo
ms.openlocfilehash: 89f5391cfbfd85f9f0715a8c7d288cc3981cc618
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30237001"
---
# <a name="introduction-to-azure-data-factory"></a>Úvod do Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-introduction.md)
> * [Verze 2 – Preview](introduction.md)

Ve světě velkých objemů dat jsou nezpracovaná, neuspořádaná data často uložená v relačních, nerelačních a jiných systémech úložiště. Nezpracovaná data sama o sobě ale nemají potřebný kontext ani význam, aby mohla analytikům, datovým vědcům nebo osobám rozhodujícím v rámci podniků poskytnout smysluplný přehled. 

Velké objemy dat vyžadují službu, která umožňuje orchestraci a operacionalizaci procesů, aby se tato obrovská úložiště nezpracovaných dat proměnila v obchodní informace, podle kterých se lze rozhodovat. Azure Data Factory je spravovaná cloudová služba vytvořená pro tyto komplexní projekty hybridní extrakce, transformace a načítání (ETL), extrakce, načítání a transformace (ELT) a integrace dat.

Představte si například společnost vyrábějící hry, která shromažďuje celé petabajty herních protokolů generovaných hrami v cloudu. Společnost chce tyto protokoly analyzovat, aby získala informace o preferencích zákazníků, demografických ukazatelích a způsobech používání. Zároveň chce identifikovat příležitosti křížového a následného prodeje, vyvíjet nové, zajímavé funkce, podpořit obchodní růst a zlepšovat zkušenosti zákazníků.

Aby společnost mohla tyto protokoly analyzovat, potřebuje použít referenční data, jako jsou informace o zákaznících, hrách a marketingových kampaních, které jsou uložené v místním úložišti dat. Společnost chce využít tato data z místního úložiště dat a zkombinovat je s dalšími daty protokolů, která má uložená v cloudovém úložišti dat. 

Za účelem získání informací hodlá zpracovat spojená data pomocí clusteru Spark v cloudu (Azure HDInsight) a transformovaná data publikovat do cloudového datového skladu, jako je služba Azure SQL Data Warehouse, aby z nich mohla snadno vytvářet sestavy. Tento pracovní postup chce automatizovat a provádět jeho monitorování a správu na každodenní bázi. Zároveň ho chce spouštět, jakmile se v kontejneru úložiště objektů blob objeví soubory.

Právě v takových scénářích práce s daty nachází uplatnění platforma Azure Data Factory. Je to *cloudová služba pro integraci dat umožňující vytváření pracovních postupů řízených daty v cloudu za účelem orchestrace a automatizace přesunu a transformace dat*. Pomocí služby Azure Data Factory můžete vytvářet a plánovat pracovní postupy řízené daty (označované jako kanály), které dokáží ingestovat data z různorodých zdrojů dat. Data se můžou zpracovávat a transformovat pomocí výpočetních služeb, jako je Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics a Azure Machine Learning. 

Výstupní data navíc můžete publikovat v úložištích dat, jako je Azure SQL Data Warehouse, a umožnit jejich využití v aplikacích business intelligence (BI). V neposlední řadě služba Azure Data Factory umožňuje uspořádání nezpracovaných dat do smysluplných úložišť dat a datových jezer, která pomáhají při obchodním rozhodování.

![Zobrazení nejvyšší úrovně služby Data Factory](media/introduction/big-picture.png)

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si téma [Úvod do služby Data Factory verze 1](v1/data-factory-introduction.md).

## <a name="how-does-it-work"></a>Jak to funguje?
Kanály (pracovní postupy řízené daty) ve službě Azure Data Factory obvykle provádí následující čtyři kroky:

![Čtyři kroky pracovního postupu řízeného daty](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Připojení a shromažďování

Podniky mají data různých typů, která jsou uložená v různorodých místních zdrojích nebo v cloudu, a data strukturovaná, nestrukturovaná i částečně strukturovaná, která přicházejí v různých intervalech a různou rychlostí. 

Prvním krokem při sestavování systému vytváření informací je připojení ke všem požadovaným zdrojům dat a zpracování, jako jsou například služby typu software jako služba (SaaS), databáze, sdílené složky a webové služby FTP. Dalším krokem je přesun dat podle potřeby do centralizovaného umístění pro následné zpracování.

Bez služby Data Factory musí podniky sestavovat vlastní komponenty pro přesun dat nebo vyvíjet vlastní služby pro integraci a zpracování těchto zdrojů dat. Integrace a údržba takových systémů je nákladná a složitá. Kromě toho často postrádají monitorování a upozorňování na podnikové úrovni a ovládací prvky, které může nabídnout plně spravovaná služba.

Se službou Data Factory můžete pomocí [aktivity kopírování](copy-activity-overview.md) v datovém kanálu přesouvat data z místních i cloudových úložišť dat do centralizovaného úložiště v cloudu pro účely další analýzy. Například můžete shromažďovat data v Azure Data Lake Store a později je transformovat pomocí výpočetní služby Azure Data Lake Analytics. Data můžete shromažďovat i ve službě Azure Blob Storage a později je transformovat pomocí clusteru Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformace a rozšíření
Jakmile budou data v centralizovaném úložišti dat v cloudu, můžete shromážděná data zpracovat nebo transformovat pomocí výpočetních služeb, jako je HDInsight Hadoop, Spark, Data Lake Analytics a Machine Learning. Budete chtít spolehlivě produkovat transformovaná data podle udržitelného a řízeného plánu, abyste mohli do produkčních prostředí dodávat důvěryhodná data.

### <a name="publish"></a>Publikování
Po převodu nezpracovaných dat do podoby, která umožňuje využití v rámci podniku, můžete tato data nahrát do služby Azure Data Warehouse, Azure SQL Database, Azure CosmosDB nebo jakéhokoli jiného analytického nástroje, na který se můžou vaši obchodní uživatelé nasměrovat ze svých nástrojů business intelligence.

### <a name="monitor"></a>Monitorování
Jakmile úspěšně sestavíte a nasadíte kanál integrace dat, který ze zpracovaných dat získává obchodní hodnotu, můžete monitorovat naplánované aktivity a kanály a jejich míru úspěšnosti a chyb. Služba Azure Data Factory obsahuje integrovanou podporu monitorování kanálů prostřednictvím služby Azure Monitor, rozhraní API, PowerShellu, Log Analytics a panelů stavu na webu Azure Portal.

## <a name="top-level-concepts"></a>Koncepty nejvyšší úrovně
Předplatné Azure může obsahovat jednu nebo více instancí služby Azure Data Factory (neboli datových továren). Azure Data Factory se skládá ze čtyř klíčových součástí. Tyto součásti společně poskytují platformu, na které můžete vytvářet pracovní postupy řízené daty s kroky pro přesun a transformaci dat.

### <a name="pipeline"></a>Kanál
Datová továrna může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které dohromady provádějí určitou jednotku práce. Aktivity v kanálu společně provádí úlohy. Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure a pak na clusteru HDInsight spustí dotaz Hivu pro rozdělení dat. 

Výhodou tohoto přístupu je, že vám kanál umožňuje spravovat aktivity jako sadu, a ne každou jednotlivě. Aktivity v kanálu je možné zřetězit, aby probíhaly postupně, nebo můžou probíhat souběžně a nezávisle na sobě.

### <a name="activity"></a>Aktivita
Aktivity představují krok zpracování v rámci kanálu. Například můžete použít aktivitu kopírování ke kopírování dat z jednoho úložiště dat do jiného. Podobně můžete použít aktivitu Hivu, která spustí dotaz Hivu na clusteru Azure HDInsight, aby transformoval a analyzoval vaše data. Data Factory podporuje tři typy aktivit: aktivity přesunu dat, aktivity transformace dat a aktivity řízení.

### <a name="datasets"></a>Datové sady
Datové sady představují datové struktury v rámci úložišť dat, které jednoduše odkazují na data, která chcete ve svých aktivitách použít jako vstupy nebo výstupy. 

### <a name="linked-services"></a>Propojené služby
Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Můžete si to představit tak, že propojená služba definuje připojení ke zdroji dat a datová sada představuje strukturu těchto dat. Například propojená služba Azure Storage určuje připojovací řetězec pro připojení k účtu služby Azure Storage. Datová sada objektu blob Azure navíc určuje kontejner objektů blob a složku obsahující data.

Propojené služby slouží ve službě Data Factory ke dvěma účelům:

- Představují **úložiště dat**, k nimž mimo jiné patří například místní databáze SQL Serveru, databáze Oracle, sdílená složka nebo účet úložiště objektů blob Azure. Seznam podporovaných úložišť dat najdete v článku [Aktivita kopírování](copy-activity-overview.md).

- Představují **výpočetní prostředek**, který může hostovat provádění aktivity. Například aktivita HDInsightHive se spouští na clusteru HDInsight Hadoop. Seznam aktivit transformace a podporovaných výpočetních prostředí najdete v článku o [transformaci dat](transform-data.md).

### <a name="triggers"></a>Triggery
Aktivační události jsou jednotkou zpracování, která určuje, kdy se má zahájit provádění kanálu. Pro různé typy událostí existují různé typy aktivačních událostí. Ve verzi Preview služba Data Factory podporuje aktivační událost plánovače s hodinovým plánem. 

### <a name="pipeline-runs"></a>Spuštění kanálu
Spuštění kanálu je instance provádění kanálu. Instance spuštění kanálu se obvykle vytvářejí předáváním argumentů do parametrů definovaných v kanálech. Argumenty je možné předat ručně nebo v rámci definice aktivační události.

### <a name="parameters"></a>Parametry
Parametry jsou dvojice klíčů a hodnot v rámci konfigurace jen pro čtení.  Parametry jsou definované v kanálu. Argumenty definovaných parametrů se předávají během provádění z kontextu spuštění vytvořeného aktivační událostí nebo ručně spuštěným kanálem. Aktivity v rámci kanálu využívají hodnoty parametrů.

Datová sada je parametr silného typu a opakovaně použitelná/odkazovatelná entita. Aktivita může odkazovat na datové sady a může využívat vlastnosti definované v definici datové sady.

Propojená služba je také parametr silného typu, který obsahuje informace o připojení k úložišti dat nebo výpočetnímu prostředí. Je to také opakovaně použitelná/odkazovatelná entita.

### <a name="control-flow"></a>Tok řízení
Tok řízení představuje orchestraci aktivit kanálu, která zahrnuje zřetězení aktivit v sekvenci, větvení, definování parametrů na úrovni kanálu, a předávání argumentů při volání kanálu na vyžádání nebo z aktivační události. Zahrnuje také předávání vlastního stavu a kontejnery smyček, tedy iterátory For-each.


Další informace o konceptech služby Data Factory najdete v následujících článcích:

- [Datové sady a propojené služby](concepts-datasets-linked-services.md)
- [Kanály a aktivity](concepts-pipelines-activities.md)
- [Prostředí Integration Runtime](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Podporované oblasti

V současné době můžete vytvářet datové továrny v oblastech Východní USA, Východní USA 2 a Západní Evropa. Objekt služby Data Factory nicméně může přistupovat k úložištím dat a výpočetním službám v jiných oblastech Azure za účelem přesouvání dat mezi úložišti dat nebo zpracování dat pomocí výpočetních služeb.

Samotná služba Azure Data Factory žádná data neuchovává. Umožňuje vytvářet pracovní postupy řízené daty k orchestraci přesouvání dat mezi podporovanými úložišti dat a zpracování dat pomocí výpočetních služeb v jiných oblastech nebo v místním prostředí. Také vám umožňuje monitorovat a spravovat pracovní postupy pomocí uživatelského prostředí nebo prostřednictvím kódu programu.

Přestože je služba Data Factory dostupná jenom v oblastech Východní USA, Východní USA 2 a Západní Evropa, služba pohánějící přesouvání dat ve službě Data Factory je dostupná globálně v několika oblastech. Pokud je úložiště dat za bránou firewall, o přesun dat se postará modul Integration Runtime (v místním prostředí) nainstalovaný ve vašem místním prostředí.

Předpokládejme například, že vaše výpočetní prostředí, jako je cluster Azure HDInsight nebo služba Azure Machine Learning, běží v oblasti Západní Evropa. Můžete vytvořit instanci služby Azure Data Factory v oblasti Východní USA a Východní USA 2 a použít ji k plánování úloh ve výpočetních prostředích v oblasti Západní Evropa. Trvá několik milisekund, než Data Factory aktivuje úlohu ve výpočetním prostředí, ale čas potřebný pro vykonání úlohy ve výpočetním prostředí se nemění.

## <a name="compare-with-version-2"></a>Porovnání s verzí 2
Seznam rozdílů mezi verzí 1 a verzí 2 služby Data Factory najdete v [porovnání s verzí 1](compare-versions.md). 

## <a name="next-steps"></a>Další kroky
Začněte s vytvářením kanálu služby Data Factory pomocí jednoho z následujících nástrojů nebo sad SDK: 

- [Uživatelské rozhraní Data Factory na webu Azure Portal](quickstart-create-data-factory-portal.md)
- [Nástroj pro kopírování dat na webu Azure Portal](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST](quickstart-create-data-factory-rest-api.md)
- [Šablona Azure Resource Manageru](quickstart-create-data-factory-resource-manager-template.md)
 
