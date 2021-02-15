---
title: 'Azure Data Factory: nejčastější dotazy '
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: a3b892b5d92feb743d9a538c4e418b1f47e39b03
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386301"
---
# <a name="azure-data-factory-faq"></a>Nejčastější dotazy ke službě Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Co je Azure Data Factory?

Data Factory je plně spravovaná cloudová služba ETL Integration data, která automatizuje přesun a transformaci dat. Podobně jako továrna, která spouští vybavení pro transformaci surovin na dokončené zboží, Azure Data Factory orchestruje existující služby, které shromažďují nezpracované údaje a transformují je na informace připravené k použití. 

Pomocí Azure Data Factory můžete vytvářet pracovní postupy řízené daty k přesouvání dat mezi místními a cloudovým úložištěm dat. A můžete zpracovávat a transformovat data pomocí toků dat. ADF také podporuje externí výpočetní moduly pro ručně kódované transformace pomocí výpočetních služeb, jako je Azure HDInsight, Azure Databricks a prostředí Integration runtime služba SSIS (SQL Server Integration Services) (SSIS).

Pomocí Data Factory můžete zpracování dat provádět buď v cloudové službě založené na Azure, nebo ve vlastním prostředí pro vlastní hostování, jako je například SSIS, SQL Server nebo Oracle. Po vytvoření kanálu, který provede akci, kterou potřebujete, můžete naplánovat jeho pravidelné spuštění (každou hodinu, každý den nebo každý týden), plánování časových oken nebo aktivovat kanál z výskytu události. Další informace najdete v tématu [Úvod do Azure Data Factory](introduction.md).

## <a name="compliance-and-security-considerations"></a>Požadavky na dodržování předpisů a zabezpečení

Azure Data Factory je certifikovaný pro řadu certifikace dodržování předpisů, včetně _SOC 1, 2, 3_, _HIPAA Baa_ a _HiTRUST_. Úplný a rostoucí seznam certifikací najdete [tady](data-movement-security-considerations.md). Digitální kopie sestav auditu a certifikace dodržování předpisů najdete v [Centru zabezpečení služby](https://servicetrust.microsoft.com/) .

### <a name="control-flows-and-scale"></a>Řízení toků a škálování

V rámci podpory různých toků a vzorů integrace v moderních datových skladech umožňuje Data Factory flexibilní modelování datových kanálů. To má za následek úplné řízení programovacích paradigmat, které zahrnují podmíněné spuštění, větvení v datových kanálech a schopnost explicitně předat parametry v těchto tocích a mezi nimi. Tok řízení také zahrnuje transformaci dat prostřednictvím odeslání aktivity do externích spouštěcích modulů a možností toku dat, včetně přesouvání dat ve velkém měřítku prostřednictvím aktivity kopírování.

Data Factory poskytuje volnost modelování veškerého stylu toku, který je vyžadován pro integraci dat a který je možné odeslat na vyžádání nebo opakovaně podle plánu. Mezi běžné toky, které tento model povoluje, patří:

- Tok řízení:
    - Aktivity lze zřetězit v rámci kanálu v posloupnosti.
    - Aktivity se můžou rozvětvit v rámci kanálu.
    - Parametry:
        * Parametry se dají definovat na úrovni kanálu a argumenty je možné předat při vyvolání kanálu na vyžádání nebo z triggeru.
        * Aktivity mohou využívat argumenty předávané do kanálu.
    - Předání vlastního stavu:
        * Výstupy aktivit, včetně stavu, mohou být spotřebovány následnou aktivitou v kanálu.
    - Kontejnery smyček:
        * Aktivita foreach provede iteraci v zadané kolekci aktivit ve smyčce. 
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

Před výběrem možnosti **ladit** není nutné publikovat změny ve službě Data Factory. To je užitečné ve scénářích, kde se chcete ujistit, že nové dodatky nebo změny budou fungovat podle očekávání, než provedete aktualizaci pracovních postupů služby Data Factory ve vývojovém, testovacím nebo produkčním prostředí.

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Možnost nasadit balíčky SSIS do Azure

Pokud chcete přesunout úlohy SSIS, můžete vytvořit Data Factory a zřídit prostředí Azure-SSIS Integration runtime. Prostředí Azure-SSIS Integration runtime je plně spravovaný cluster virtuálních počítačů Azure (uzlů) vyhrazených ke spouštění balíčků SSIS v cloudu. Podrobné pokyny najdete v kurzu [nasazení balíčků SSIS do Azure](./tutorial-deploy-ssis-packages-azure.md) . 

### <a name="sdks"></a>Sady SDK

Pokud jste pokročilý uživatel a hledáte programové rozhraní, Data Factory poskytuje bohatou sadu sad SDK, které můžete použít k vytváření, správě a monitorování kanálů pomocí vašeho oblíbeného integrovaného vývojového prostředí (IDE). Podpora jazyků zahrnuje .NET, PowerShell, Python a REST.

### <a name="monitoring"></a>Monitorování

Můžete monitorovat datové továrny prostřednictvím PowerShellu, sady SDK nebo nástrojů pro monitorování vizuálů v uživatelském rozhraní prohlížeče. Efektivním a efektivním způsobem můžete monitorovat a spravovat vlastní toky na vyžádání, na základě triggeru a hodiny. Zrušit stávající úlohy, Projděte si přehled o selháních, přejděte k podrobnostem a Získejte podrobné chybové zprávy a Nalaďte problémy, a to vše z jednoho podokna skla bez kontextu přepínání nebo navigace mezi obrazovkami.

### <a name="new-features-for-ssis-in-data-factory"></a>Nové funkce pro SSIS v Data Factory

Od počáteční verze Public Preview v 2017 Data Factory přidali následující funkce pro SSIS:

-    Podpora tří dalších konfigurací/variant Azure SQL Database pro hostování databáze SSIS (SSISDB) projektů/balíčků:
-    SQL Database s koncovými body služby virtuální sítě
-    Spravovaná instance SQL
-    Elastický fond
-    Podpora pro Azure Resource Manager virtuální sítě nad klasickými virtuálními sítěmi, která se v budoucnu už nepoužívá, umožňuje vložit nebo připojit Azure-SSIS Integration runtime k virtuální síti nakonfigurované pro SQL Database pomocí koncových bodů služby virtuální sítě/MI/místních přístupových dat. Další informace najdete v tématu [připojení prostředí Azure-SSIS Integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).
-    Podpora ověřování pomocí Azure Active Directory (Azure AD) a ověřování SQL pro připojení k SSISDB, povolení ověřování Azure AD s vaší Data Factory spravovanou identitou pro prostředky Azure
-    Podpora pro uvedení stávající licence SQL Server k získání výrazné úspory nákladů z možnosti Zvýhodněné hybridní využití Azure
-    Podpora Enterprise Edition prostředí Azure-SSIS Integration runtime, které umožňuje používat pokročilé a prémiové funkce, vlastní instalační rozhraní pro instalaci dalších komponent a rozšíření a partnerský ekosystém. Další informace najdete [v tématu edice Enterprise Edition, Custom Setup a rozšíření třetích stran pro SSIS v ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Hlubší integrace SSIS v Data Factory, která vám umožní vyvolat nebo aktivovat aktivity balíčku SSIS na první úrovni v kanálu Data Factory a naplánovat je prostřednictvím SSMS. Další informace najdete v tématu [modernizovat a rozšiřování pracovních postupů ETL/ELT s aktivitami SSIS v kanálech ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).

## <a name="what-is-the-integration-runtime"></a>Co je prostředí Integration runtime?

Prostředí Integration runtime je výpočetní infrastruktura, kterou Azure Data Factory používá k tomu, aby poskytovala následující možnosti integrace dat napříč různými síťovými prostředími:

- **Pohyb dat**: Integration runtime při přesunu dat přesouvá data mezi zdrojovým a cílovým úložištěm dat a nabízí podporu integrovaných konektorů, převodu formátů, mapování sloupců a výkonného a škálovatelného přenosu dat.
- **Aktivity odeslání**: pro transformaci Integration runtime poskytuje možnost nativně spouštět balíčky SSIS.
- **Spouštění balíčků SSIS**: Integration runtime nativně spouští balíčky SSIS ve spravovaném prostředí Azure Compute. Prostředí Integration runtime také podporuje odesílání a monitorování transformačních aktivit, které běží na různých výpočetních službách, jako je Azure HDInsight, Azure Machine Learning, SQL Database a SQL Server.

Můžete nasadit jednu nebo více instancí prostředí Integration runtime, jak je potřeba k přesunutí a transformaci dat. Prostředí Integration runtime může běžet ve veřejné síti Azure nebo v privátní síti (místní, Azure Virtual Network nebo Amazon Web Services virtuální privátní cloud [VPC]).

Další informace najdete v tématu [Prostředí Integration Runtime v Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Jaký je limit počtu prostředí Integration runtime?

Neexistuje žádné pevné omezení počtu instancí prostředí Integration runtime, které můžete mít v datové továrně. Existují však omezení počtu jader virtuálních počítačů, které může prostředí Integration runtime použít pro každé předplatné pro spuštění balíčku SSIS. Další informace najdete v tématu [omezení Data Factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Jaké jsou koncepty Azure Data Factory na nejvyšší úrovni?

Předplatné Azure může obsahovat jednu nebo více instancí služby Azure Data Factory (neboli datových továren). Azure Data Factory obsahuje čtyři klíčové komponenty, které společně fungují jako platforma, na které můžete vytvářet pracovní postupy řízené daty s kroky pro přesun a transformaci dat.

### <a name="pipelines"></a>Pipelines

Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které provádí pracovní jednotku. Aktivity v kanálu společně provádějí úlohu. Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure a pak spustí dotaz na podregistr v clusteru HDInsight, aby data mohla rozdělit. Výhodou je, že pomocí kanálu můžete spravovat aktivity jako sadu, aniž byste museli spravovat jednotlivé aktivity jednotlivě. Můžete zřetězit aktivity v kanálu, abyste je mohli postupně provozovat, nebo je můžete provozovat samostatně, paralelně.

### <a name="data-flows"></a>Toky dat

Toky dat jsou objekty, které sestavíte vizuálně v Data Factory které transformují data ve velkém měřítku na back-endové služby Spark. Nemusíte porozumět programování nebo interním seznámení s Sparkem. Stačí navrhnout svůj záměr transformace dat pomocí grafů (mapování) nebo tabulek (tahání).

### <a name="activities"></a>Aktivity

Aktivity představují krok zpracování v rámci kanálu. Aktivitu kopírování můžete například použít ke kopírování dat z jednoho úložiště dat do jiného úložiště dat. Podobně můžete použít aktivitu podregistru, která spustí dotaz na podregistr v clusteru Azure HDInsight, aby transformoval nebo analyzoval vaše data. Data Factory podporuje tři typy aktivit: aktivity přesunu dat, aktivity transformace dat a aktivity řízení.

### <a name="datasets"></a>Datové sady

Datové sady představují datové struktury v rámci úložišť dat, které jednoduše odkazují na data, která chcete ve svých aktivitách použít jako vstupy nebo výstupy. 

### <a name="linked-services"></a>Propojené služby

Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Zamyslete se jím takto: propojená služba definuje připojení ke zdroji dat a datová sada představuje strukturu dat. Například propojená služba Azure Storage Určuje připojovací řetězec pro připojení k účtu Azure Storage. A datová sada Azure Blob určuje kontejner objektů BLOB a složku obsahující data.

Propojené služby mají dva účely v Data Factory:

- Představuje *úložiště dat* , které zahrnuje, ale není omezené na, instance SQL Server, instanci databáze Oracle, sdílenou složku nebo účet úložiště objektů BLOB v Azure. Seznam podporovaných úložišť dat najdete [v tématu aktivita kopírování v Azure Data Factory](copy-activity-overview.md).
- Představují *výpočetní prostředek*, který může hostovat provádění aktivity. Například aktivita podregistru HDInsight běží v clusteru HDInsight Hadoop. Seznam aktivit transformace a podporovaných výpočetních prostředí najdete v tématu [transformace dat v Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Aktivační události

Triggery reprezentují jednotky zpracování, které určují, kdy se spustí spuštění kanálu. Pro různé typy událostí existují různé typy aktivačních událostí. 

### <a name="pipeline-runs"></a>Spuštění kanálu

Spuštění kanálu je instance provádění kanálu. Spuštění kanálu je obvykle možné vytvořit předáním argumentů parametrům, které jsou definovány v kanálu. Argumenty můžete předat ručně nebo v rámci definice triggeru.

### <a name="parameters"></a>Parametry

Parametry jsou páry klíč-hodnota v konfiguraci jen pro čtení.V kanálu definujete parametry a před spuštěním z kontextu spuštění předáte argumenty pro definované parametry. Kontext spuštění je vytvořen triggerem nebo z kanálu, který spustíte ručně. Aktivity v rámci kanálu využívají hodnoty parametrů.

Datová sada je parametr silného typu a entita, kterou můžete znovu použít nebo odkazovat. Aktivita může odkazovat na datové sady a může využívat vlastnosti, které jsou definovány v definici datové sady.

Propojená služba je také parametr silného typu, který obsahuje informace o připojení k úložišti dat nebo výpočetnímu prostředí. Je to také entita, kterou můžete znovu použít nebo odkazovat.

### <a name="control-flows"></a>Tok řízení

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
- [Domovská stránka dokumentace](./index.yml)
- [Domovská stránka produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Technický hluboký podrobně

### <a name="how-can-i-schedule-a-pipeline"></a>Jak můžu naplánovat kanál?

K naplánování kanálu můžete použít aktivační událost plánovače nebo časový interval. Aktivační procedura používá plán kalendářních hodin, který umožňuje pravidelné naplánování kanálů nebo v rámci opakujících se vzorů založených na kalendáři (například v pondělí po 6:00 PM a čtvrtek na 9:00 odp.). Další informace najdete v tématu [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Můžu předat parametry spuštění kanálu?

Ano, parametry jsou první třídou, koncept nejvyšší úrovně v Data Factory. Můžete definovat parametry na úrovni kanálu a předat argumenty při spuštění kanálu na vyžádání nebo pomocí triggeru.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Můžu definovat výchozí hodnoty pro parametry kanálu?

Ano. Můžete definovat výchozí hodnoty pro parametry v kanálech.

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Může aktivita v kanálu spotřebovat argumenty, které se předávají do spuštění kanálu?

Ano. Každá aktivita v kanálu může spotřebovat hodnotu parametru, která je předána kanálu a spouštěna s `@parameter` konstrukcí. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Je možné vlastnost výstup aktivity spotřebovat v jiné aktivitě?

Ano. Výstup aktivity lze spotřebovat v následné aktivitě s `@activity` konstrukcí.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Návody řádně zpracovat hodnoty null ve výstupu aktivity?

`@coalesce`Konstrukce ve výrazech slouží k řádnému zpracování hodnot null.

## <a name="mapping-data-flows"></a>Toky dat mapování

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Potřebuji pomoc s řešením logiky toku dat. Jaké informace potřebuji k získání nápovědu?

Když Microsoft poskytuje pomoc nebo řešení potíží s toky dat, zadejte prosím skript toku dat. Toto je skript kódu na pozadí z vašeho grafu toku dat. V uživatelském rozhraní ADF otevřete tok dat a pak klikněte na tlačítko "skript" v pravém horním rohu. Tento skript zkopírujte a vložte nebo ho uložte do textového souboru.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Návody přístup k datům pomocí dalších typů datových sad 90 v Data Factory?

Funkce mapování toku dat v současné době umožňuje Azure SQL Database, Azure synapse Analytics, soubory s oddělenými textem ze služby Azure Blob Storage nebo Azure Data Lake Storage Gen2 a soubory Parquet ze služby Blob Storage nebo Data Lake Storage Gen2 nativně pro zdroj a jímku. 

Použijte aktivitu kopírování pro přípravu dat z jiných konektorů a potom spusťte aktivitu toku dat pro transformaci dat po jejím přípravě. Například váš kanál se nejdřív nakopíruje do úložiště objektů BLOB a pak aktivita toku dat použije ve zdroji datovou sadu k transformaci těchto dat.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Je místně hostovaný modul Integration runtime dostupný pro toky dat?

Místní prostředí IR je konstrukce kanálu ADF, kterou můžete použít s aktivitou kopírování k získání nebo přesunu dat do a z Prem nebo datových zdrojů a jímky založených na virtuálních počítačích. Nejprve Připravte data s kopírováním, potom toku dat pro transformaci a následným zkopírováním, pokud potřebujete přesunout tato transformovaná data zpět do úložiště on-Prem.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Slouží výpočetní modul toku dat více tenantů?

Clustery nejsou nikdy sdíleny. Pro každou úlohu spuštění v produkčním běhu garantujeme izolaci. V případě scénáře ladění získá jedna osoba jeden cluster a všechny ladění budou přejít do clusteru, který iniciuje tento uživatel.

## <a name="wrangling-data-flows"></a>Toky dat tahání

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Jaké jsou podporované oblasti pro tok dat tahání?

Tok dat tahání se v tuto chvíli podporuje v datových továrnách vytvořených v následujících oblastech:

* Austrálie – východ
* Střední Kanada
* Indie – střed
* East US
* USA – východ 2
* Japonsko – východ
* Severní Evropa
* Jihovýchodní Asie
* Středojižní USA
* Spojené království – jih
* USA – středozápad
* West Europe
* USA – západ
* Západní USA 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Jaká jsou omezení a omezení pomocí toku dat tahání?

Názvy datových sad můžou obsahovat jenom alfanumerické znaky. Podporují se následující úložiště dat:

* DelimitedText datová sada v Azure Blob Storage pomocí ověřování klíčů účtu
* DelimitedText datová sada ve službě Azure Data Lake Storage Gen2 pomocí ověřování klíče účtu nebo instančního objektu
* DelimitedText datová sada ve službě Azure Data Lake Storage Gen1 pomocí ověřování instančního objektu
* Azure SQL Database a datový sklad pomocí ověřování SQL. Další informace najdete níže v části Podporované typy SQL. Pro datový sklad neexistuje žádná podpora základní ani pracovní.

V tuto chvíli se integrace propojených služeb Key Vault nepodporuje v toku dat tahání.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Jaký je rozdíl mezi mapováním a toky dat tahání?

Mapování toků dat poskytuje způsob, jak transformovat data ve velkém měřítku bez nutnosti jakéhokoli kódování. Můžete navrhnout úlohu transformace dat v plátně toku dat vytvořením řady transformací. Začněte s libovolným počtem zdrojových transformací následovaných kroky transformace dat. Dokončete tok dat pomocí jímky, aby se vaše výsledky vyplochy v cíli. Mapování toku dat je skvělé při mapování a transformace dat se známými i neznámými schématy v jímkach a zdrojích.

Tahání data flows vám umožní provádět agilní přípravu a průzkum dat pomocí Power Queryho online editoru hybridních webových aplikací ve velkém měřítku prostřednictvím spuštění Sparku. V případě, že se vám podíváme na datové jezera, někdy stačí prozkoumat datovou sadu nebo vytvořit datovou sadu v Lake. Nejste mapováni na známý cíl. Toky dat tahání se používají pro méně formální a modelově založené analytické scénáře.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Jaký je rozdíl mezi datovými proudy Power Platform a toky dat tahání?

Datové toky Power Platform umožňují uživatelům import a transformaci dat z široké škály zdrojů dat do Common Data Service a Azure Data Lake k sestavování aplikací PowerApps, Power BI sestav nebo automatizace toků. Datové toky Power Platform využívají vytvořená prostředí pro přípravu Power Query dat, podobně jako Power BI a Excel. Obslužné rutiny dat Power Platform také umožňují snadné opakované použití v rámci organizace a automatické zpracování orchestrace (například automatické aktualizace toku dat, které závisí na jiném toku dat při obnovení původní verze).

Azure Data Factory (ADF) je spravovaná služba pro integraci dat, která umožňuje, aby data technici a data občana vytvořila komplexní pracovní postupy hybridní extrakce, transformace a načítání (ETL) a extrakce (ELT). Tahání tok dat v ADF umožňuje uživatelům používat prostředí bez kódu bez serveru, které zjednodušuje přípravu dat v cloudu a škáluje se na velikost dat, a to bez nutnosti správy infrastruktury. Používá technologii pro přípravu dat Power Query (používá se také v datových proudech Power Platform, Excel, Power BI) k přípravě a tvarování dat. Tahání datové toky, které jsou vytvořené pro zpracování všech komplexních a škálovatelných problémů s integrací velkých objemů dat, umožňují uživatelům rychle připravovat data ve velkém měřítku prostřednictvím spuštění Sparku. Uživatelé můžou vytvářet odolné datové kanály v přístupném vizuálním prostředí pomocí našeho rozhraní založeného na prohlížeči a nechat si ADF složitosti spuštění Sparku. Plány sestavení pro vaše kanály a monitorování provádění toků dat z portálu monitorování ADF. Snadná Správa dostupnosti dat SLA Díky monitorování a výstrahám s bohatou dostupností ADF a využívají integrované možnosti průběžné integrace a nasazování pro ukládání a správu toků ve spravovaném prostředí. Navažte výstrahy a zobrazte plány provádění, abyste ověřili, jestli vaše logika funguje tak, jak plánujete při ladění toků dat.

### <a name="supported-sql-types"></a>Podporované typy SQL

Tok dat tahání podporuje v SQL následující datové typy. Při použití datového typu, který není podporován, se zobrazí chyba ověřování.

* short
* double
* real
* float
* char
* nchar
* varchar
* nvarchar
* integer
* int
* bit
* boolean
* smallint
* tinyint
* bigint
* long
* text
* date
* datetime
* datetime2
* smalldatetime
* časové razítko
* uniqueidentifier
* xml

Další datové typy budou v budoucnu podporovány.

## <a name="next-steps"></a>Další kroky

Podrobné pokyny k vytvoření datové továrny najdete v následujících kurzech:

- [Rychlý Start: vytvoření datové továrny](quickstart-create-data-factory-dot-net.md)
- [Kurz: kopírování dat v cloudu](tutorial-copy-data-dot-net.md)
