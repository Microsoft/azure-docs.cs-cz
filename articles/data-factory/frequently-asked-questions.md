---
title: 'Azure Data Factory: Nejčastější dotazy | Dokumentace Microsoftu'
description: Získejte odpovědi na nejčastější dotazy ohledně služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: d704c32ee7417c6460ad6cc880e451adddfa61de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61345750"
---
# <a name="azure-data-factory-faq"></a>Nejčastější dotazy k Azure Data Factory
Tento článek obsahuje odpovědi na nejčastější dotazy ohledně služby Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Co je služba Azure Data Factory? 
Objekt pro vytváření dat je plně spravovaná, cloudových dat integrační služba, která automatizuje přesouvání a transformaci dat. Podobně jako objekt pro vytváření, na kterém běží zařízení pro transformaci suroviny na hotové výroby Azure Data Factory orchestruje stávající služby, které sbírají nezpracovaná data a transformují je na informace připravené k použití. 

Pomocí služby Azure Data Factory můžete vytvářet pracovní postupy řízené daty pro přesun dat mezi místním prostředím a cloudem datových úložišť. Můžete zpracovávat a transformovat data pomocí výpočetních služeb, jako je například Azure HDInsight, Azure Data Lake Analytics a modulu runtime integrace SQL Server Integration Services (SSIS). 

Pomocí služby Data Factory můžete spustit zpracování dat na základě Azure cloudové službě nebo ve vlastní v místním prostředí výpočetní prostředí, například služby SSIS, SQL Server nebo Oracle. Po vytvoření kanálu, který provádí akce, které potřebujete, můžete naplánovat pravidelné spouštění časový interval (hodinový, denní nebo týdenní, například), plánování, nebo aktivace kanálu, který z výskytu události. Další informace najdete v tématu [Úvod do Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Toků řízení a škálování 
K podpoře různorodých integračních toků a vzorů v moderních datových skladech, umožňuje objektu pro vytváření dat kanál flexibilní datová modelování. To zahrnuje úplné řízení toku programovací modely, které zahrnují podmíněného spuštění, větvení v datových kanálech a schopnost explicitně předávat parametry v těchto tocích a mezi. Tok řízení také zahrnuje transformace dat pomocí aktivity odeslání do externí provádění modulů a možnosti toku dat, včetně přesouvání dat ve velkém měřítku pomocí aktivity kopírování.

Data Factory poskytuje svobodu modelovat jakýkoli styl toku, který je potřebný pro integraci dat a který může odesílat na vyžádání nebo opakovaně podle plánu. Mezi běžné toky, které tento model umožňuje jsou:   

- Toků řízení:
    - Aktivity můžete zřetězit v pořadí, v rámci kanálu.
    - Je možné větvit aktivity v rámci kanálu.
    - Parametry:
        - Parametry se dají definovat na úrovni kanálu a argumenty je možné předat při volání kanálu na vyžádání nebo pomocí aktivační události.
        - Aktivity mohou využívat argumenty předávané do kanálu.
    - Předávání vlastního stavu:
        - Výstupy aktivity včetně stavu, mohou být spotřebovány následující aktivita v kanálu.
    - Kontejnery smyček:
        - Aktivita foreach provádí iterace nad zadanou kolekcí v aktivity ve smyčce. 
- Toky založené na aktivačních:
    - Kanály se můžou spouštět na vyžádání nebo podle času plánovače.
- Toky rozdílů:
    - Parametry můžete použít k definování vašich horní mez pro rozdílové kopírování při přesouvání tabulek dimenzí nebo referenčních tabulek z relačního úložiště, ať už místní nebo v cloudu, k načtení dat do jezera. 

Další informace najdete v tématu [kurzu: Řízení toků](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Ve velkém měřítku bez použití kódu kanály transformovaných dat
Nové prostředí využívající prohlížeč nástroje obsahuje bez použití kódu kanálu pro vytváření a nasazování moderních, interaktivních webových prostředí s.

Pro vývojáře vizuálních dat a datoví architekti webové uživatelské rozhraní služby Data Factory je bez použití kódu návrhové prostředí, který budete používat a vytvořit tak kanály. Je plně integrována s Git služby Visual Studio Online a zajišťuje integraci pro CI/CD a iterativní vývoj aplikací pomocí možnosti ladění.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Bohaté multiplatformní sady SDK pro pokročilé uživatele
Data Factory verze 2 nabízí bohatou sadu SDK, které můžete použít k vytváření, správě a monitorování kanálů pomocí vašeho oblíbeného prostředí IDE, včetně:
* Python SDK
* PowerShell CLI
* C# SDK

Uživatele můžete také zdokumentovaných rozhraní REST API pro rozhraní s Data Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iterativní vývoj a ladění pomocí visual tools
Azure Data Factory visual tools umožňují iterativní vývoj a ladění. Můžete vytvořit kanály a testovacích běhů pomocí **ladění** schopností na plátně kanálu, aniž byste museli napsat jediný řádek kódu. Můžete zobrazit výsledky testovacích běhů v **výstup** okno plátně kanálu. Po úspěšném provedení testovacího běhu, můžete přidat více aktivit do kanálu a pokračovat v ladění iteračním způsobem. Můžete také zrušit testovacích běhů, jakmile jsou v průběhu. 

Není nutné publikovat provedené změny do služby data factory před výběrem **ladění**. To je užitečné ve scénářích, kde chcete, abyste měli jistotu, že nový dodatky nebo změny budou fungovat podle očekávání, než aktualizujete pracovní postupy objekt pro vytváření dat v vývojové, testovací nebo produkční prostředí. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Schopnost nasadit balíčky SSIS do Azure 
Pokud chcete přesunout úlohy služby SSIS, můžete vytvořit datovou továrnu a zřízení prostředí Azure-SSIS integration runtime. Prostředí Azure-SSIS integration runtime je plně spravovaný cluster virtuálních počítačů Azure (uzlů) vyhrazených ke spouštění balíčků služby SSIS v cloudu. Podrobné pokyny najdete v tématu [balíčků služby SSIS nasazovat do Azure](tutorial-create-azure-ssis-runtime-portal.md) kurzu. 
 
### <a name="sdks"></a>Sady SDK
Pokud jste pokročilý uživatel a hledáte programové rozhraní, služby Data Factory poskytuje pestrou nabídku sad SDK, který můžete použít k vytváření, správě nebo monitorování kanálů pomocí vašeho oblíbeného integrovaného vývojového prostředí. Podpora jazyků zahrnuje .NET, PowerShell, Python a REST.

### <a name="monitoring"></a>Monitorování
Můžete monitorovat vaší datové továrny pomocí prostředí PowerShell, sady SDK nebo Visual Tools sledování v uživatelském rozhraní prohlížeče. Můžete monitorovat a spravovat vlastní toky na vyžádání, na základě aktivační události a hodiny řízené účinný a efektivní způsobem. Zrušit existující úlohy, viz chyby na první pohled, k podrobnostem a získat podrobné chybové zprávy a laděním problémů s vše z jednoho podokně ze skla. Díky bez kontextu přepínání nebo přejdete vpřed a zpět mezi obrazovkami. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nové funkce pro službu SSIS ve službě Data Factory
Protože se počáteční veřejná verze preview verze 2017, Data Factory přináší následující funkce pro službu SSIS:

-   Podpora pro tři další konfigurace/varianty služby Azure SQL Database pro hostování databáze služby SSIS (SSISDB) projektů/balíčků:
-   SQL Database s koncovými body služby virtuální sítě
-   Spravovaná instance
-   Elastický fond
-   Podpora pro virtuální síť Azure Resource Manageru nad klasickou virtuální síť k přestanou používat v budoucnosti, které umožňuje vložit/spojení vašeho prostředí Azure-SSIS integration runtime k virtuální síti nakonfigurované pro službu SQL Database s službu virtual network přístup k datům koncové body/MI/místní. Další informace najdete v tématu taky [připojit k prostředí Azure-SSIS integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).
-   Podpora pro ověřování Azure Active Directory (Azure AD) a ověřování SQL pro připojení do databáze SSISDB, umožňuje ověřování Azure AD s vaší identity spravované služby Data Factory pro prostředky Azure
-   Podpora pro přenesení svoji vlastní licenci systému SQL Server v místním získat značné úspory z možnosti programu zvýhodněné hybridní využití Azure
-   Podporu pro Enterprise Edition prostředí Azure-SSIS integration runtime, která vám umožní používat pokročilé/prémiové funkce, na vlastní instalační program rozhraní nainstalovat další součásti nebo rozšíření a ekosystém partnerů. Další informace najdete v tématu taky [Enterprise Edition, vlastní nastavení a 3. stran rozšíření pro službu SSIS ve službě ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Hlubší integrace služby SSIS v datové továrně, který umožňuje vyvolání nebo triggeru prvotřídní spuštění balíčku služby SSIS aktivit v kanálech Data Factory a naplánují je prostřednictvím aplikace SSMS. Další informace najdete v tématu taky [Modernize a Rozšiřte své pracovní postupy ETL/ELT pomocí služby SSIS aktivit v kanálech ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Co je IR?
Prostředí IR je výpočetní infrastruktura, která Azure Data Factory používá k zajištění následujících funkcí integrace dat v různých síťových prostředích:

- **Přesun dat**: V případě přesunu dat prostředí IR přesouvá data mezi zdrojovým a cílovým úložištěm dat a přitom poskytuje podporu integrovaných konektorů, převodu formátu, mapování sloupců a výkonného a škálovatelného přenosu dat.
- **Odeslání aktivity**: V případě transformace prostředí integration runtime poskytuje schopnost nativní spouštění balíčků služby SSIS.
- **Spouštění balíčků služby SSIS**: Prostředí integration runtime nativně spustí balíčků služby SSIS v prostředí spravované výpočetní prostředky Azure. Prostředí IR podporuje také odesílání a monitorování aktivit transformace spuštěných v různých výpočetních službách, jako je například Azure HDInsight, Azure Machine Learning, SQL Database a SQL Server.

Pro přesun a transformaci dat podle potřeby můžete nasadit jednu nebo více instancí modulu runtime integrace. Prostředí integration runtime můžete spustit ve veřejné síti Azure nebo v privátní síti (místní, virtuální sítě Azure nebo Amazon Web Services virtuálním privátním cloudu [VPC]). 

Další informace najdete v tématu [Prostředí Integration Runtime v Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Jaký je limit počtu prostředí integration runtime?
Neexistuje žádné vynucené omezení na počet instancí modulu runtime integrace, může být v datové továrně. Existuje ale omezení na počet jader virtuálního počítače, které prostředí integration runtime můžete použít jedno předplatné, pro spouštění balíčku služby SSIS. Další informace najdete v tématu [omezuje služby Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Co jsou koncepty nejvyšší úrovně služby Azure Data Factory?
Předplatné Azure může obsahovat jednu nebo více instancí služby Azure Data Factory (neboli datových továren). Azure Data Factory obsahuje čtyř klíčových komponent, které budou spolupracovat jako platformu, na které můžete vytvářet pracovní postupy řízené daty s kroky pro přesun a transformaci dat.

### <a name="pipelines"></a>Kanály
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které provádějí určitou jednotku práce. Aktivity v kanálu společně provádí úlohy. Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure a pak spustit dotaz Hive v clusteru služby HDInsight pro rozdělení dat. Výhodou je, že vám pomůže kanálu spravovat aktivity jako sadu, a ne by bylo potřeba spravovat jednotlivé aktivity jednotlivě. Můžete společně řetězit aktivity v kanálu pro jejich probíhaly postupně, nebo můžete provozovat nezávisle na sobě paralelně.

### <a name="activities"></a>Činnosti
Aktivity představují krok zpracování v rámci kanálu. Například můžete použít aktivitu kopírování ke kopírování dat z jednoho úložiště dat do jiného. Podobně můžete použít aktivitu Hivu, která spustí dotaz Hivu na clusteru Azure HDInsight, aby transformoval a analyzoval vaše data. Data Factory podporuje tři typy aktivit: aktivity přesunu dat, aktivity transformace dat a aktivity řízení.

### <a name="datasets"></a>Datové sady
Datové sady představují datové struktury v rámci úložišť dat, které jednoduše odkazují na data, která chcete ve svých aktivitách použít jako vstupy nebo výstupy. 

### <a name="linked-services"></a>Propojené služby
Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Můžete ho chápat takto: Propojená služba definuje připojení ke zdroji dat a datová sada reprezentuje strukturu těchto dat. Například propojenou službu Azure Storage Určuje připojovací řetězec pro připojení k účtu Azure Storage. A datová sada služby Azure blob Určuje kontejner objektů blob a složku obsahující data.

Propojené služby mají dva účely ve službě Data Factory:

- K reprezentaci *úložiště dat* , který obsahuje, ale není omezena pouze na místní instance systému SQL Server, instanci Oracle database, sdílené složky nebo účtu služby Azure Blob storage. Seznam podporovaných úložišť dat najdete v tématu [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md).
- Představují *výpočetní prostředek*, který může hostovat provádění aktivity. Například aktivita HDInsight Hive spustí v clusteru HDInsight Hadoop. Seznam aktivit transformace a podporovaných výpočetních prostředí najdete v tématu [transformace dat ve službě Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Aktivační procedury
Aktivační události představují jednotky zpracování, které určují, kdy je spuštěna provádění zřetězeného příkazu. Pro různé typy událostí existují různé typy aktivačních událostí. 

### <a name="pipeline-runs"></a>Spuštění kanálu
Spuštění kanálu je instance provádění kanálu. Obvykle vytvoříte instanci kanálu předáváním argumentů do parametrů, které jsou definované v kanálu. Argumenty můžete předat ručně nebo v rámci definice aktivační události.

### <a name="parameters"></a>Parametry
Parametry jsou páry klíč hodnota v konfiguraci jen pro čtení. Definovat parametry v kanálu a předejte argumenty definovaných parametrů během provádění z kontextu spuštění. Vytvoření kontextu spuštění pomocí aktivační události nebo z kanálu, který můžete spustit ručně. Aktivity v rámci kanálu využívají hodnoty parametrů.

Datová sada je parametr silného typu a entita, která můžete opakovaně používat nebo odkazovat. Aktivita může odkazovat na datové sady a ho může využívat vlastnosti definované v definici datové sady.

Propojená služba je také parametr silného typu, který obsahuje informace o připojení k úložišti dat nebo výpočetnímu prostředí. Také je entita, která můžete opakovaně používat nebo odkazovat.

### <a name="control-flows"></a>Toky řízení
Řízení toků orchestraci aktivit kanálu, které zahrnují řetězení aktivit v sekvenci, větvení, parametry, které můžete definovat na úrovni kanálu a argumenty předávané při volání kanálu na vyžádání nebo pomocí aktivační události. Řízení toků zahrnovat také předávání vlastního stavu a kontejnery smyček (tedy iterátory foreach).


Další informace o konceptech služby Data Factory najdete v následujících článcích:

- [Datové sady a propojené služby](concepts-datasets-linked-services.md)
- [Kanály a aktivity](concepts-pipelines-activities.md)
- [Prostředí Integration Runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Co je cenovém modelu pro služby Data Factory?
Podrobnosti o cenách Azure Data Factory najdete v tématu [podrobnosti o cenách služby Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Jak se můžou zůstat aktuální informace o službě Data Factory?
Nejnovější informace o službě Azure Data Factory najdete na následujících webech:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Domovská stránka dokumentace](/azure/data-factory)
- [Domovská stránka produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Podrobné technické informace 

### <a name="how-can-i-schedule-a-pipeline"></a>Jak lze naplánovat kanál? 
Aktivační událost plánovače nebo aktivační událost okna čas můžete použít k naplánování kanálu. Trigger použije hodinového plánu kalendář, který kanály můžete plánovat pravidelné nebo na základě kalendáře opakující se vzory, třeba na (pondělí na 18:00:00) a čtvrtek ve 21:00:00. Další informace najdete v tématu [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Můžete předat parametry pro spuštění kanálu
Ano, parametry jsou koncept první třídy, nejvyšší úrovně ve službě Data Factory. Můžete definovat parametry na úrovni kanálu a předejte argumenty při spuštění kanálu spouštěly na vyžádání nebo pomocí aktivační události.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Můžete definovat výchozí hodnoty pro parametry kanálu? 
Ano. Výchozí hodnoty pro parametry můžete definovat v kanálech. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Aktivita v kanálu mohou využívat argumenty předávané do kanálu? 
Ano. Každá aktivita v rámci kanálu využívají hodnotu parametru, který je předaný kanálu a spuštění pomocí `@parameter` vytvořit. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Mohou být spotřebovány vlastnost výstup aktivity v jiné aktivitě? 
Ano. Výstup aktivity mohou být spotřebovány v následné aktivity s `@activity` vytvořit.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak můžu řádně zpracování hodnot null ve výstup aktivity? 
Můžete použít `@coalesce` vytvořit ve výrazech řádně zpracování hodnot null. 

## <a name="mapping-data-flows"></a>Mapování datových toků

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>Kterou verzi Data Factory pomocí můžete vytvářet toky dat?
Pomocí Data Factory V2 verze můžete vytvářet toky data.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>Jsem byl předchozí verzi private preview zákazníky, kteří používají datové toky a můžu použít verzi Data Factory V2 ve verzi preview pro datové toky.
Tato verze je zastaralá. Pomocí Data Factory verze 2 pro datové toky.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>Co se změnilo z verze private preview k omezené veřejné verzi preview ve vztahu toků dat?
Už máte zpřístupnit vlastní clustery Azure Databricks. Vytvoření clusteru a vše bude spravovat služby Data Factory. Datové sady objektů BLOB a Azure Data Lake Storage Gen2 datové sady jsou rozdělené na text s oddělovači a Apache Parquet datové sady. Stále můžete Data Lake Storage Gen2 a Blob storage k ukládání těchto souborů. Použijte odpovídající propojené služby pro tyto moduly úložiště.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>Můžete migrovat Moje továrny ve verzi private preview pro Data Factory V2?

Ano. [Postupujte podle pokynů](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Potřebuji pomoc s řešením potíží Moje data toku logiku. Jaké informace je nutné poskytnout pomoc?

Když společnost Microsoft poskytuje pomoc nebo řešení potíží s toky dat, zadejte prosím plán kódu DSL. Postupujte přitom takto:

1. V Návrháři toku dat vyberte **kód** v pravém horním rohu. Tato akce zobrazí upravitelný kód JSON pro datový tok.
2. Zobrazení kódu vyberte **plánování** v pravém horním rohu. Tento přepínač se přepne z JSON na jen pro čtení formátovaného DSL skript plánu.
3. Zkopírujte a vložte tento skript nebo uložte ho do textového souboru.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Přístup dat s použitím jiných typů 80 datové sady ve službě Data Factory

Funkce mapování toku dat aktuálně umožňuje že Azure SQL Database, Azure SQL Data Warehouse, oddělených textových souborů z Azure Blob storage nebo Azure Data Lake Storage Gen2 a soubory Parquet z úložiště objektů Blob nebo Data Lake Storage Gen2 nativně pro zdroje a jímky. 

Použije aktivitu kopírování k připravit data z některého z jiných konektorů a následné provádění aktivitu toku dat pro transformaci dat po je připravené. Například váš kanál se nejprve zkopírovat do úložiště objektů Blob, a pak aktivitu toku dat použije datovou sadu ve zdroji transformovat tato data.

## <a name="next-steps"></a>Další postup
Podrobné pokyny pro vytváření dat najdete v následujících kurzech:

- [Rychlé zprovoznění: Vytvoření datové továrny](quickstart-create-data-factory-dot-net.md)
- [Kurz: Kopírování dat v cloudu](tutorial-copy-data-dot-net.md)
