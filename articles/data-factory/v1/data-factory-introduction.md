---
title: Úvod do Data Factory, služba pro integraci dat
description: Dozvíte se, že Data Factory je cloudová služba pro integraci dat, která orchestruje a automatizuje přesouvání a transformaci dat.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: overview
ms.date: 01/22/2018
ms.openlocfilehash: 1cee5f92af4e23a6bc0d195bc13bc3f11f53443d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382969"
---
# <a name="introduction-to-azure-data-factory"></a>Úvod do služby Azure Data Factory 
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-introduction.md)
> * [Verze 2 (aktuální verze)](../introduction.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si článek [Úvod do Data Factory V2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>Co je Azure Data Factory?
Jak se ve světě velkých objemů dat využívají existující data ve firmách? Je možné rozšířit data generovaná v cloudu pomocí referenčních dat z místních zdrojů dat nebo jiných různorodých zdrojů dat? 

Například herní společnost shromažďuje protokoly generované hrami v cloudu. Tyto protokoly chce analyzovat, aby získala informace o preferencích zákazníků, demografických ukazatelích, způsobech používání atd. Společnost zároveň chce identifikovat příležitosti křížového a následného prodeje, vyvíjet nové, zajímavé funkce, podpořit obchodní růst a zlepšovat zkušenosti zákazníků. 

Aby společnost mohla tyto protokoly analyzovat, potřebuje použít referenční data, jako jsou informace o zákaznících, hrách a marketingových kampaních, které jsou uložené v místním úložišti dat. Společnost proto chce ingestovat data z cloudového úložiště dat a referenční data z místního úložiště dat. 

Dále chce data zpracovat pomocí Hadoopu v cloudu (Azure HDInsight). Chtějí publikovat výsledná data do cloudového datového skladu, jako je Azure synapse Analytics nebo do místního úložiště dat, jako je SQL Server. Společnost chce, aby se tento pracovní postup spouštěl jednou týdně. 

Společnost potřebuje platformu, na které může vytvořit pracovní postup, který bude ingestovat data z místních i cloudových úložišť dat. Společnost také musí mít možnost transformovat nebo zpracovávat data pomocí stávajících výpočetních služeb, jako je Hadoop, a publikovat výsledky do místního nebo cloudového úložiště dat pro využití v aplikacích BI. 

![Přehled služby Data Factory](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory je platforma pro tyto druhy scénářů. Je to *cloudová služba pro integraci dat umožňující vytváření pracovních postupů řízených daty v cloudu za účelem orchestrace a automatizace přesunu a transformace dat*. Pomocí služby Azure Data Factory můžete provádět následující úlohy: 

- Vytvářet a plánovat pracovní postupy řízené daty (označované jako kanály), které dokáží ingestovat data z různorodých zdrojů dat.

- Zpracovávat a transformovat data pomocí výpočetních služeb, jako je Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics a Azure Machine Learning.

-  Publikujte výstupní data do úložišť dat, jako je Azure synapse Analytics pro aplikace business intelligence (BI), která se mají spotřebovat.  

Spíše než o tradiční platformu extrakce, transformace a načítání (ETL) se jedná o platformu extrakce a načítání (EL) a následné transformace a načtení (TL). Transformace zpracovávají data pomocí výpočetních služeb namísto přidávání odvozených sloupců, počítání řádků, řazení dat atd. 

V současné době pracovní postupy ve službě Azure Data Factory využívají a produkují *data dělená podle času* (po hodinách, dnech, týdnech atd.). Kanál například může číst vstupní data, zpracovat je a vyprodukovat výstupní data jednou denně. Pracovní postup můžete také spustit jenom jednou.  
  

## <a name="how-does-it-work"></a>Jak to funguje? 
Kanály (pracovní postupy řízené daty) ve službě Azure Data Factory obvykle provádí následující tři kroky:

![Tři fáze služby Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Připojení a shromažďování
Podniky mají data různých typů, která se nachází v různorodých zdrojích. Prvním krokem při sestavování systému vytváření informací je připojení ke všem požadovaným zdrojům dat a zpracování. Mezi tyto zdroje patří služby SaaS, sdílené složky, služby protokolu FTP a webové služby. Data se pak podle potřeby přesunou do centralizovaného umístění pro následné zpracování.

Bez služby Data Factory musí podniky sestavovat vlastní komponenty pro přesun dat nebo vyvíjet vlastní služby pro integraci a zpracování těchto zdrojů dat. Integrace a údržba takových systémů je nákladná a složitá. Kromě toho často postrádají monitorování a upozorňování na podnikové úrovni a ovládací prvky, které může nabídnout plně spravovaná služba.

Se službou Data Factory můžete použít aktivitu kopírování v datovém kanálu pro přesun dat z místních i cloudových zdrojů dat do centralizovaného úložiště v cloudu pro další analýzu. 

Například můžete shromažďovat data v Azure Data Lake Store a později je transformovat pomocí výpočetní služby Azure Data Lake Analytics. Data můžete shromažďovat také v úložišti objektů blob Azure a později je transformovat pomocí clusteru Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformace a rozšíření
Jakmile budou data v centralizovaném úložišti dat v cloudu, můžete je zpracovat nebo transformovat pomocí výpočetních služeb, jako je HDInsight Hadoop, Spark, Data Lake Analytics nebo Machine Learning. Budete chtít spolehlivě produkovat transformovaná data podle udržitelného a řízeného plánu, abyste mohli do produkčních prostředí dodávat důvěryhodná data. 

### <a name="publish"></a>Publikovat 
Přenášejte transformovaná data z cloudu do místních zdrojů, jako je SQL Server. Případně je můžete uchovávat ve zdrojích cloudového úložiště, aby je mohly využívat nástroje BI, analytické nástroje a další aplikace.

## <a name="key-components"></a>Klíčové komponenty
Předplatné Azure může obsahovat jednu nebo více instancí služby Azure Data Factory (neboli datových továren). Azure Data Factory se skládá ze čtyř klíčových součástí. Tyto součásti společně poskytují platformu, na které můžete vytvářet pracovní postupy řízené daty s kroky pro přesun a transformaci dat. 

### <a name="pipeline"></a>Kanál
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je skupina aktivit. Aktivity v kanálu společně provádějí úlohu. 

Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure a pak na clusteru HDInsight spustí dotaz Hivu pro rozdělení dat. Výhodou tohoto přístupu je, že vám kanál umožňuje spravovat aktivity jako sadu, a ne každou jednotlivě. Například můžete nasadit a naplánovat kanál namísto plánování jednotlivých aktivit. 

### <a name="activity"></a>Aktivita
Kanál může obsahovat jednu nebo víc aktivit. Aktivity definují akce, které se mají provést na vašich datech. Například můžete použít aktivitu kopírování ke kopírování dat z jednoho úložiště dat do jiného. Podobně můžete použít aktivitu Hivu. Aktivit Hivu spustí dotaz Hivu na clusteru Azure HDInsight, aby transformoval a analyzoval vaše data. Data Factory podporuje dva typy aktivit: aktivity přesunu dat a transformace dat.

### <a name="data-movement-activities"></a>Aktivity přesunu dat
Aktivita kopírování ve službě Data Factory kopíruje data ze zdrojového úložiště dat do úložiště dat jímky. Data z libovolného zdroje lze zapsat do libovolné jímky. Výběrem úložiště dat se dozvíte, jak kopírovat data z a do daného úložiště. Data Factory podporuje následující úložiště dat:

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Další informace najdete v tématu [Přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Aktivity transformace dat
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Další informace najdete v tématu [Přesun dat pomocí aktivity kopírování](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Vlastní aktivity .NET
Vlastní aktivitu .NET vytvořte v případě, že potřebujete přesunout data do nebo z úložiště dat, které aktivita kopírování nepodporuje, nebo potřebujete transformovat data pomocí vlastní logiky. Podrobnosti o vytvoření a používání vlastní aktivity najdete v tématu [Použití vlastních aktivit v kanálu služby Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Datové sady
Aktivita přijímá jako vstup nula nebo více datových sad a produkuje jednu nebo více datových sad jako výstup. Datové sady představují datové struktury v úložištích dat. Tyto struktury odkazují na data, která chcete použít ve svých aktivitách (například vstupy nebo výstupy). 

Datová sada objektu blob Azure například určuje kontejner objektů blob a složku v úložišti objektů blob Azure, ze kterých by měl kanál číst data. Nebo datová sada tabulky SQL Azure určuje tabulku, do které aktivita zapisuje výstupní data. 

### <a name="linked-services"></a>Propojené služby
Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Můžete si to představit tak, že propojená služba definuje připojení ke zdroji dat a datová sada reprezentuje strukturu těchto dat. 

Například propojená služba Azure Storage určuje připojovací řetězec pro připojení k účtu služby Azure Storage. Datová sada objektu blob Azure určuje kontejner objektů blob a složku obsahující data.   

Propojené služby slouží ve službě Data Factory ke dvěma účelům:

* Představuje *úložiště dat* , které zahrnuje, ale není omezené na, SQL Server databázi, databázi Oracle, sdílenou složku nebo účet úložiště objektů BLOB v Azure. Seznam podporovaných úložišť dat najdete v oddílu [Aktivity přesunu dat](#data-movement-activities).

* Představují *výpočetní prostředek*, který může hostovat provádění aktivity. Například aktivita HDInsightHive se spouští na clusteru HDInsight Hadoop. Seznam podporovaných výpočetních prostředí najdete v části [Aktivity transformace dat](#data-transformation-activities).

### <a name="relationship-between-data-factory-entities"></a>Vztah mezi entitami služby Data Factory

![Diagram: Data Factory, služba pro integraci dat v cloudu – klíčové koncepty](./media/data-factory-introduction/data-integration-service-key-concepts.png)

## <a name="supported-regions"></a>Podporované oblasti
V současné době můžete vytvářet datové továrny v oblastech USA – západ, USA – východ a Severní Evropa. Datová továrna nicméně může přistupovat k úložištím dat a výpočetním službám v jiných oblastech Azure za účelem přesouvání dat mezi úložišti dat nebo zpracování dat pomocí výpočetních služeb.

Samotná služba Azure Data Factory žádná data neuchovává. Umožňuje vytvářet pracovní postupy řízené daty k orchestraci přesouvání dat mezi [podporovanými úložišti dat](#data-movement-activities). Umožňuje také zpracovávat data pomocí [výpočetních služeb](#data-transformation-activities) v jiných oblastech nebo v místním prostředí. Také vám umožňuje [monitorovat a spravovat pracovní postupy](data-factory-monitor-manage-pipelines.md) pomocí uživatelského prostředí nebo prostřednictvím kódu programu.

Služba Data Factory je dostupná pouze v oblastech USA – západ, USA – východ a Severní Evropa. Služba pohánějící přesouvání dat ve službě Data Factory je dostupná [globálně](data-factory-data-movement-activities.md#global) v několika oblastech. Pokud je úložiště dat za bránou firewall, pak [Správa dat bránu](data-factory-move-data-between-onprem-and-cloud.md) , která je nainstalovaná ve vašem místním prostředí, místo toho přesune data.

Předpokládejme například, že vaše výpočetní prostředí, jako je cluster Azure HDInsight nebo služba Azure Machine Learning, se nacházejí v oblasti Západní Evropa. Můžete vytvořit a používat instanci služby Azure Data Factory v oblasti Severní Evropa. Pak ji můžete použít k naplánování úloh ve vašich výpočetních prostředích v oblasti Západní Evropa. Trvá několik milisekund, než Data Factory aktivuje úlohu ve výpočetním prostředí, ale čas potřebný pro vykonání úlohy ve výpočetním prostředí se nemění.

## <a name="get-started-with-creating-a-pipeline"></a>Začínáme s vytvořením kanálu
Datové kanály ve službě Azure Data Factory můžete vytvořit pomocí některého z těchto nástrojů nebo rozhraní API: 

- Visual Studio
- PowerShell
- .NET API
- REST API
- Šablona Azure Resource Manageru

Pokud chcete zjistit, jak sestavit datové továrny s datovými kanály, postupujte podle podrobných pokynů v následujících kurzech:

| Kurz | Description |
| --- | --- |
| [Přesun dat mezi dvěma cloudovými úložišti dat](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Vytvořte datovou továrnu s kanálem, který přesouvá data z úložiště objektů blob do SQL Database. |
| [Transformace dat pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md) |Sestavíte svou první datovou továrnu Azure s datovým kanálem, který zpracovává data pomocí skriptu Hive v clusteru Azure HDInsight (Hadoop). |
| [Přesun dat mezi místním úložištěm dat a cloudovým úložištěm dat pomocí Systému sledování a správy dat](data-factory-move-data-between-onprem-and-cloud.md) |Vytvořte datovou továrnu s kanálem, který přesouvá data z databáze SQL Server do objektu blob Azure. Jako součást návodu nainstalujete a nakonfigurujete ve svém počítači Bránu správy dat. |
