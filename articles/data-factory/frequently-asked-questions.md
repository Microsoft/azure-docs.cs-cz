---
title: 'Azure Data Factory: nejčastější dotazy | Microsoft Docs'
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: ee57d943016c2d166f3c8469b403b56b1009385c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387063"
---
# <a name="azure-data-factory-faq"></a>Nejčastější dotazy k Azure Data Factory
Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Co je služba Azure Data Factory? 
Data Factory je plně spravovaná cloudová služba pro integraci dat, která automatizuje přesun a transformaci dat. Podobně jako továrna, která spouští vybavení pro transformaci surovin na dokončené zboží, Azure Data Factory orchestruje existující služby, které shromažďují nezpracované údaje a transformují je na informace připravené k použití. 

Pomocí Azure Data Factory můžete vytvářet pracovní postupy řízené daty k přesouvání dat mezi místními a cloudovým úložištěm dat. A můžete zpracovávat a transformovat data pomocí výpočetních služeb, jako je Azure HDInsight, Azure Data Lake Analytics a prostředí Integration runtime služba SSIS (SQL Server Integration Services) (SSIS). 

Pomocí Data Factory můžete zpracování dat provádět buď v cloudové službě založené na Azure, nebo ve vlastním prostředí pro vlastní hostování, jako je například SSIS, SQL Server nebo Oracle. Po vytvoření kanálu, který provede akci, kterou potřebujete, můžete naplánovat jeho pravidelné spuštění (každou hodinu, každý den nebo každý týden), plánování časových oken nebo aktivovat kanál z výskytu události. Další informace najdete v tématu [Úvod do Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Řízení toků a škálování 
V rámci podpory různých toků a vzorů integrace v moderních datových skladech umožňuje Data Factory flexibilní modelování datových kanálů. To má za následek úplné řízení programovacích paradigmat, které zahrnují podmíněné spuštění, větvení v datových kanálech a schopnost explicitně předat parametry v těchto tocích a mezi nimi. Tok řízení také zahrnuje transformaci dat prostřednictvím odeslání aktivity do externích spouštěcích modulů a možností toku dat, včetně přesouvání dat ve velkém měřítku prostřednictvím aktivity kopírování.

Data Factory poskytuje volnost modelování veškerého stylu toku, který je vyžadován pro integraci dat a který je možné odeslat na vyžádání nebo opakovaně podle plánu. Mezi běžné toky, které tento model povoluje, patří:   

- Tok řízení:
    - Aktivity lze zřetězit v rámci kanálu v posloupnosti.
    - Aktivity se můžou rozvětvit v rámci kanálu.
    - Parametry:
        - Parametry se dají definovat na úrovni kanálu a argumenty je možné předat při vyvolání kanálu na vyžádání nebo z triggeru.
        - Aktivity mohou využívat argumenty předávané do kanálu.
    - Předání vlastního stavu:
        - Výstupy aktivit, včetně stavu, mohou být spotřebovány následnou aktivitou v kanálu.
    - Kontejnery smyček:
        - Aktivita foreach provede iteraci v zadané kolekci aktivit ve smyčce. 
- Toky založené na aktivačních událostech:
    - Kanály se můžou aktivovat na vyžádání nebo podle času na zdi.
- Rozdílové toky:
    - Parametry se dají použít k definování horní meze pro rozdílové kopírování při přesouvání tabulek dimenzí nebo odkazů z relačního úložiště v místním prostředí nebo v cloudu, aby se data načetla do jezera. 

Další informace najdete v tématu [kurz: řízení toků](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Data ve velkém měřítku s kanály bez kódu
Nové prostředí nástrojů založené na prohlížeči poskytuje vytváření a nasazování kanálů bez kódu pomocí moderního interaktivního webového prostředí.

Pro vývojáře a datové technici pro Visual data je Data Factory webové uživatelské rozhraní, které použijete k vytváření kanálů. Je plně integrovaná se sadou Visual Studio Online Git a poskytuje integraci pro CI/CD a iterativní vývoj s možnostmi ladění.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Rozšířené sady SDK pro více platforem pro pokročilé uživatele
Data Factory v2 poskytuje bohatou sadu sad SDK, které se dají použít k vytváření, správě a monitorování kanálů pomocí vašeho oblíbeného integrovaného vývojového prostředí (IDE), včetně:
* Python SDK
* PowerShell CLI
* C# SDK

Uživatelé můžou k rozhraní s Data Factory v2 použít taky dokumentované rozhraní REST API.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iterativní vývoj a ladění pomocí vizuálních nástrojů
Azure Data Factory Visual Tools umožňují iterativní vývoj a ladění. Můžete vytvářet kanály a provádět testovací běhy pomocí možnosti **ladění** na plátně kanálu bez psaní jediného řádku kódu. Výsledky testovacích běhů můžete zobrazit v okně **výstup** plátna kanálu. Po úspěšném spuštění testu můžete do kanálu přidat další aktivity a pokračovat v ladění iterativním způsobem. Můžete také zrušit spuštění testů poté, co probíhá. 

Před výběrem možnosti **ladit**není nutné publikovat změny ve službě Data Factory. To je užitečné ve scénářích, kde se chcete ujistit, že nové dodatky nebo změny budou fungovat podle očekávání, než provedete aktualizaci pracovních postupů služby Data Factory ve vývojovém, testovacím nebo produkčním prostředí. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Možnost nasadit balíčky SSIS do Azure 
Pokud chcete přesunout úlohy SSIS, můžete vytvořit Data Factory a zřídit prostředí Azure-SSIS Integration runtime. Prostředí Azure-SSIS Integration runtime je plně spravovaný cluster virtuálních počítačů Azure (uzlů) vyhrazených ke spouštění balíčků SSIS v cloudu. Podrobné pokyny najdete v kurzu [nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) . 
 
### <a name="sdks"></a>Sady SDK
Pokud jste pokročilý uživatel a hledáte programové rozhraní, Data Factory poskytuje bohatou sadu sad SDK, které můžete použít k vytváření, správě a monitorování kanálů pomocí vašeho oblíbeného integrovaného vývojového prostředí (IDE). Podpora jazyků zahrnuje .NET, PowerShell, Python a REST.

### <a name="monitoring"></a>Sledování
Můžete monitorovat datové továrny prostřednictvím PowerShellu, sady SDK nebo nástrojů pro monitorování vizuálů v uživatelském rozhraní prohlížeče. Efektivním a efektivním způsobem můžete monitorovat a spravovat vlastní toky na vyžádání, na základě triggeru a hodiny. Zrušit stávající úlohy, Projděte si přehled o selháních, přejděte k podrobnostem a Získejte podrobné chybové zprávy a Nalaďte problémy, a to vše z jednoho podokna skla bez kontextu přepínání nebo navigace mezi obrazovkami. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nové funkce pro SSIS v Data Factory
Od počáteční verze Public Preview v 2017 Data Factory přidali následující funkce pro SSIS:

-   Podpora tří dalších konfigurací/variant Azure SQL Database pro hostování databáze SSIS (SSISDB) projektů/balíčků:
-   SQL Database s koncovými body služby virtuální sítě
-   Spravovaná instance
-   Elastický fond
-   Podpora pro Azure Resource Manager virtuální sítě nad klasickými virtuálními sítěmi, která se v budoucnu už nepoužívá, umožňuje vložit/připojit Azure-SSIS Integration runtime k virtuální síti nakonfigurované pro SQL Database se službou Virtual Network. koncové body/MI/místní přístup k datům. Další informace najdete v tématu [připojení prostředí Azure-SSIS Integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).
-   Podpora ověřování pomocí Azure Active Directory (Azure AD) a ověřování SQL pro připojení k SSISDB, povolení ověřování Azure AD s vaší Data Factory spravovanou identitou pro prostředky Azure
-   Podpora pro uvedení vlastní místní licence SQL Server k získání výrazné úspory nákladů z možnosti Zvýhodněné hybridní využití Azure
-   Podpora Enterprise Edition prostředí Azure-SSIS Integration runtime, které umožňuje používat pokročilé a prémiové funkce, vlastní instalační rozhraní pro instalaci dalších komponent a rozšíření a partnerský ekosystém. Další informace najdete [v tématu edice Enterprise Edition, Custom Setup a rozšíření třetích stran pro SSIS v ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-   Hlubší integrace SSIS v Data Factory, která vám umožní vyvolat nebo aktivovat aktivity balíčku SSIS na první úrovni v kanálu Data Factory a naplánovat je prostřednictvím SSMS. Další informace najdete v tématu [modernizovat a rozšiřování pracovních postupů ETL/ELT s aktivitami SSIS v kanálech ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Co je prostředí Integration runtime?
Prostředí Integration runtime je výpočetní infrastruktura, kterou Azure Data Factory používá k tomu, aby poskytovala následující možnosti integrace dat napříč různými síťovými prostředími:

- **Pohyb dat**: Integration runtime při přesunu dat přesouvá data mezi zdrojovým a cílovým úložištěm dat a nabízí podporu integrovaných konektorů, převodu formátů, mapování sloupců a výkonného a škálovatelného přenosu dat.
- **Aktivity odeslání**: pro transformaci Integration runtime poskytuje možnost nativně spouštět balíčky SSIS.
- **Spouštění balíčků SSIS**: Integration runtime nativně spouští balíčky SSIS ve spravovaném prostředí Azure Compute. Prostředí Integration runtime také podporuje odesílání a monitorování transformačních aktivit, které běží na různých výpočetních službách, jako je Azure HDInsight, Azure Machine Learning, SQL Database a SQL Server.

Můžete nasadit jednu nebo více instancí prostředí Integration runtime, jak je potřeba k přesunutí a transformaci dat. Prostředí Integration runtime může běžet ve veřejné síti Azure nebo v privátní síti (místní, Azure Virtual Network nebo Amazon Web Services virtuální privátní cloud [VPC]). 

Další informace najdete v tématu [Prostředí Integration Runtime v Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Jaký je limit počtu prostředí Integration runtime?
Neexistuje žádné pevné omezení počtu instancí prostředí Integration runtime, které můžete mít v datové továrně. Existují však omezení počtu jader virtuálních počítačů, které může prostředí Integration runtime použít pro každé předplatné pro spuštění balíčku SSIS. Další informace najdete v tématu [omezení Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Jaké jsou koncepty Azure Data Factory na nejvyšší úrovni?
Předplatné Azure může obsahovat jednu nebo více instancí služby Azure Data Factory (neboli datových továren). Azure Data Factory obsahuje čtyři klíčové komponenty, které společně fungují jako platforma, na které můžete vytvářet pracovní postupy řízené daty s kroky pro přesun a transformaci dat.

### <a name="pipelines"></a>Kanály
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které provádí pracovní jednotku. Aktivity v kanálu společně provádí úlohy. Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure a pak spustí dotaz na podregistr v clusteru HDInsight, aby data mohla rozdělit. Výhodou je, že pomocí kanálu můžete spravovat aktivity jako sadu, aniž byste museli spravovat jednotlivé aktivity jednotlivě. Můžete zřetězit aktivity v kanálu, abyste je mohli postupně provozovat, nebo je můžete provozovat samostatně, paralelně.

### <a name="activities"></a>Aktivity
Aktivity představují krok zpracování v rámci kanálu. Aktivitu kopírování můžete například použít ke kopírování dat z jednoho úložiště dat do jiného úložiště dat. Podobně můžete použít aktivitu podregistru, která spustí dotaz na podregistr v clusteru Azure HDInsight, aby transformoval nebo analyzoval vaše data. Data Factory podporuje tři typy aktivit: aktivity přesunu dat, aktivity transformace dat a aktivity řízení.

### <a name="datasets"></a>Datové sady
Datové sady představují datové struktury v rámci úložišť dat, které jednoduše odkazují na data, která chcete ve svých aktivitách použít jako vstupy nebo výstupy. 

### <a name="linked-services"></a>Propojené služby
Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Zamyslete se jím takto: propojená služba definuje připojení ke zdroji dat a datová sada představuje strukturu dat. Například propojená služba Azure Storage Určuje připojovací řetězec pro připojení k účtu Azure Storage. A datová sada Azure Blob určuje kontejner objektů BLOB a složku obsahující data.

Propojené služby mají dva účely v Data Factory:

- Představuje *úložiště dat* , které zahrnuje, ale není omezené na místní instanci SQL Server, instanci databáze Oracle, sdílenou složku nebo účet úložiště objektů BLOB v Azure. Seznam podporovaných úložišť dat najdete [v tématu aktivita kopírování v Azure Data Factory](copy-activity-overview.md).
- Představují *výpočetní prostředek*, který může hostovat provádění aktivity. Například aktivita podregistru HDInsight běží v clusteru HDInsight Hadoop. Seznam aktivit transformace a podporovaných výpočetních prostředí najdete v tématu [transformace dat v Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Aktivační události
Triggery reprezentují jednotky zpracování, které určují, kdy se spustí spuštění kanálu. Pro různé typy událostí existují různé typy aktivačních událostí. 

### <a name="pipeline-runs"></a>Spuštění kanálu
Spuštění kanálu je instance provádění kanálu. Spuštění kanálu je obvykle možné vytvořit předáním argumentů parametrům, které jsou definovány v kanálu. Argumenty můžete předat ručně nebo v rámci definice triggeru.

### <a name="parameters"></a>Parametry
Parametry jsou páry klíč-hodnota v konfiguraci jen pro čtení. V kanálu definujete parametry a před spuštěním z kontextu spuštění předáte argumenty pro definované parametry. Kontext spuštění je vytvořen triggerem nebo z kanálu, který spustíte ručně. Aktivity v rámci kanálu využívají hodnoty parametrů.

Datová sada je parametr silného typu a entita, kterou můžete znovu použít nebo odkazovat. Aktivita může odkazovat na datové sady a může využívat vlastnosti, které jsou definovány v definici datové sady.

Propojená služba je také parametr silného typu, který obsahuje informace o připojení k úložišti dat nebo výpočetnímu prostředí. Je to také entita, kterou můžete znovu použít nebo odkazovat.

### <a name="control-flows"></a>Toky řízení
Kontrolní toky orchestrují aktivity kanálu, které zahrnují zřetězení aktivit v sekvenci, větvení, parametry, které definujete na úrovni kanálu, a argumenty, které předáte při vyvolání kanálu na vyžádání nebo z triggeru. Řídicí toky také zahrnují vlastní předávání stavů a kontejnery smyček (tj. iterátory foreach).


Další informace o konceptech služby Data Factory najdete v následujících článcích:

- [Datové sady a propojené služby](concepts-datasets-linked-services.md)
- [Kanály a aktivity](concepts-pipelines-activities.md)
- [Prostředí Integration Runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Jaký je cenový model pro Data Factory?
Podrobnosti o cenách Azure Data Factory najdete v článku [Podrobnosti o cenách Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Jak se dá stále aktuální informace o Data Factory?
Nejaktuálnější informace o Azure Data Factory najdete na následujících webech:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Domovská stránka dokumentace](/azure/data-factory)
- [Domovská stránka produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Technický hluboký podrobně 

### <a name="how-can-i-schedule-a-pipeline"></a>Jak můžu naplánovat kanál? 
K naplánování kanálu můžete použít aktivační událost plánovače nebo časový interval. Aktivační procedura používá plán kalendářních hodin, který umožňuje pravidelné naplánování kanálů nebo v rámci opakujících se vzorů založených na kalendáři (například v pondělí po 6:00 PM a čtvrtek na 9:00 odp.). Další informace najdete v tématu [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Můžu předat parametry spuštění kanálu?
Ano, parametry jsou první třídou, koncept nejvyšší úrovně v Data Factory. Můžete definovat parametry na úrovni kanálu a předat argumenty při spuštění kanálu na vyžádání nebo pomocí triggeru.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Můžu definovat výchozí hodnoty pro parametry kanálu? 
Ano. Můžete definovat výchozí hodnoty pro parametry v kanálech. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Může aktivita v kanálu spotřebovat argumenty, které se předávají do spuštění kanálu? 
Ano. Každá aktivita v kanálu může spotřebovat hodnotu parametru, která je předána kanálu a spuštěna s konstrukcí `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Je možné vlastnost výstup aktivity spotřebovat v jiné aktivitě? 
Ano. Výstup aktivity lze spotřebovat v následné aktivitě s konstrukcí `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Návody řádně zpracovat hodnoty null ve výstupu aktivity? 
Můžete použít konstrukci `@coalesce` ve výrazech k řádnému zpracování hodnot null. 

## <a name="mapping-data-flows"></a>Mapování toků dat

### <a name="which-data-factory-version-do-i-use-to-create-data-flows"></a>Kterou verzi Data Factory mám použít k vytváření toků dat?
Pro vytváření toků dat použijte verzi Data Factory v2.
  
### <a name="i-was-a-previous-private-preview-customer-who-used-data-flows-and-i-used-the-data-factory-v2-preview-version-for-data-flows"></a>Jsem předchozí zákazník verze Private Preview, který použil toky dat a používá verzi Data Factory v2 Preview pro toky dat.
Tato verze je nyní zastaralá. Pro toky dat použijte Data Factory v2.
  
### <a name="what-has-changed-from-private-preview-to-limited-public-preview-in-regard-to-data-flows"></a>Co se změnilo z verze Private Preview na omezené veřejné verze Preview v souvislosti s toky dat?
Nebudete už muset přinášet vlastní clustery Azure Databricks. Data Factory bude spravovat vytváření a roztrhnout clustery. Datové sady objektů BLOB a Azure Data Lake Storage Gen2 datové sady jsou rozdělené na oddělený text a sady Apache Parquet. K ukládání těchto souborů můžete dál používat Data Lake Storage Gen2 a BLOB Storage. Použijte pro tyto paměťové moduly příslušnou propojenou službu.

### <a name="can-i-migrate-my-private-preview-factories-to-data-factory-v2"></a>Můžu migrovat svoje vlastní továrny ve verzi Preview na Data Factory v2?

Ano. [Postupujte podle pokynů](https://www.slideshare.net/kromerm/adf-mapping-data-flow-private-preview-migration).

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Potřebuji pomoc s řešením logiky toku dat. Jaké informace potřebuji k získání nápovědu?

Když Microsoft poskytuje pomoc nebo řešení potíží s toky dat, zadejte prosím plán kódu DSL. Postupujte přitom takto:

1. V Návrháři toku dat v pravém horním rohu vyberte **kód** . Tím se zobrazí upravitelný kód JSON pro tok dat.
2. V zobrazení kód v pravém horním rohu vyberte **plán** . Tento přepínač přepne z formátu JSON do naformátovaného plánu skriptu DSL určeného jen pro čtení.
3. Tento skript zkopírujte a vložte nebo ho uložte do textového souboru.

### <a name="how-do-i-access-data-by-using-the-other-80-dataset-types-in-data-factory"></a>Návody přístup k datům pomocí dalších typů datových sad 80 v Data Factory?

Funkce mapování toku dat v současné době umožňuje Azure SQL Database, Azure SQL Data Warehouse, oddělený text soubory ze služby Azure Blob Storage nebo Azure Data Lake Storage Gen2 a Parquet soubory ze služby Blob Storage nebo nativně Data Lake Storage Gen2 pro zdroj a jímku. 

Použijte aktivitu kopírování pro přípravu dat z jiných konektorů a potom spusťte aktivitu toku dat pro transformaci dat po jejím přípravě. Například váš kanál se nejdřív nakopíruje do úložiště objektů BLOB a pak aktivita toku dat použije ve zdroji datovou sadu k transformaci těchto dat.

## <a name="next-steps"></a>Další kroky
Podrobné pokyny k vytvoření datové továrny najdete v následujících kurzech:

- [Rychlý Start: vytvoření datové továrny](quickstart-create-data-factory-dot-net.md)
- [Kurz: kopírování dat v cloudu](tutorial-copy-data-dot-net.md)
