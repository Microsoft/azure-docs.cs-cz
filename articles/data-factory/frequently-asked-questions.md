---
title: 'Azure Data Factory: Nejčastější dotazy '
description: Získejte odpovědi na nejčastější dotazy týkající se Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 8d0b49b73ef6b67653fbf32db1174880a51d432d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81412947"
---
# <a name="azure-data-factory-faq"></a>Nejčastější dotazy ke službě Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Co je služba Azure Data Factory? 
Data Factory je plně spravovaná cloudová služba ETL integrace dat, která automatizuje pohyb a transformaci dat. Stejně jako továrna, která provozuje zařízení pro transformaci surovin na hotové zboží, Azure Data Factory orchestruje stávající služby, které shromažďují nezpracovaná data a transformují je na informace připravené k použití. 

Pomocí Azure Data Factory můžete vytvářet pracovní postupy řízené daty pro přesun dat mezi místními a cloudovými úložišti dat. A data můžete zpracovávat a transformovat pomocí toků dat. ADF také podporuje externí výpočetní moduly pro ručně kódované transformace pomocí výpočetních služeb, jako je Azure HDInsight, Azure Databricks a sql server integration services (SSIS) integrace runtime. 

Pomocí Data Factory můžete zpracování dat provádět buď v cloudové službě založené na Azure, nebo ve vlastním vlastním výpočetním prostředí, jako je SSIS, SQL Server nebo Oracle. Po vytvoření kanálu, který provede akci, kterou potřebujete, můžete naplánovat jeho pravidelné spouštění (například každou hodinu, denně nebo týdně), plánování časového okna nebo spuštění kanálu ze výskytu události. Další informace najdete v tématu [Úvod do Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Řízení toků a škálování 
Pro podporu různých integračních toků a vzorů v moderním datovém skladu umožňuje Data Factory flexibilní modelování datových kanálů. To zahrnuje úplné řízení paradigmatprogramování toku, které zahrnují podmíněné provádění, větvení v datových kanálech a schopnost explicitně předat parametry v rámci těchto toků a napříč. Tok řízení zahrnuje také transformaci dat prostřednictvím odeslání aktivity do externích prováděcích modulů a možností toku dat, včetně přesunu dat ve velkém měřítku prostřednictvím aktivity Copy.

Data Factory poskytuje svobodu modelovat jakýkoli styl toku, který je nutný pro integraci dat a který lze odeslat na vyžádání nebo opakovaně podle plánu. Několik běžných toků, které tento model umožňuje, jsou:   

- Řídicí toky:
    - Aktivity lze zřetězit v pořadí v rámci kanálu.
    - Aktivity lze rozvětvené v rámci kanálu.
    - Parametry:
        - Parametry lze definovat na úrovni kanálu a argumenty mohou být předány při vyvolání kanálu na vyžádání nebo z aktivační události.
        - Aktivity mohou využívat argumenty předávané do kanálu.
    - Vlastní stav předávání:
        - Výstupy aktivity, včetně stavu, mohou být spotřebovány následnou aktivitou v kanálu.
    - Smyčkové nádoby:
        - Foreach aktivita bude iterate přes zadanou kolekci aktivit ve smyčce. 
- Toky založené na aktivačních událostech:
    - Potrubí lze spustit na vyžádání nebo podle času nástěnných hodin.
- Delta toky:
    - Parametry lze použít k definování vysoké značky pro rozdílovou kopii při přesouvání dimenzí nebo referenčních tabulek z relačního úložiště, a to buď v místním prostředí, nebo v cloudu, pro načtení dat do jezera. 

Další informace naleznete v [tématu Výuka: Řízení toků](tutorial-control-flow.md).

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Data transformovaná ve velkém měřítku pomocí kanálů bez kódu
Nové prostředí nástrojů založených na prohlížeči poskytuje vytváření a nasazování kanálu bez kódu s moderním interaktivním webovým prostředím.

Pro vývojáře vizuálních dat a datové inženýry je webové uživatelské rozhraní Datové továrny návrhové prostředí bez kódu, které budete používat k vytváření kanálů. Je plně integrovaný s Visual Studio Online Git a poskytuje integraci pro CI/CD a iterativní vývoj s možnostmi ladění.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Bohaté sady SDK pro různé platformy pro pokročilé uživatele
Data Factory V2 poskytuje bohatou sadu sad SDK, které lze použít k vytváření, správě a monitorování kanálů pomocí vašeho oblíbeného ide, včetně:
* Python SDK
* PowerShell CLI
* C# SDK

Uživatelé mohou také použít zdokumentované rozhraní REST API pro rozhraní s Dat Factory V2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iterativní vývoj a ladění pomocí vizuálních nástrojů
Vizuální nástroje Azure Data Factory umožňují iterativní vývoj a ladění. Můžete vytvořit kanály a provést testovací spuštění pomocí funkce **ladění** na plátně kanálu bez psaní jediného řádku kódu. Výsledky testovacích běhů můžete zobrazit v okně **Výstup** na plátně kanálu. Po úspěšném spuštění testu můžete přidat další aktivity do kanálu a pokračovat v ladění iterativním způsobem. Můžete také zrušit zkušební jízdy poté, co jsou v průběhu. 

Před výběrem **možnosti Ladění**není nutné publikovat změny ve službě datové továrny . To je užitečné ve scénářích, kde se chcete ujistit, že nové přírůstky nebo změny budou fungovat podle očekávání před aktualizací pracovních postupů datové továrny ve vývojovém, testovacím nebo provozním prostředí. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Možnost nasazení balíčků SSIS do Azure 
Pokud chcete přesunout úlohy SSIS, můžete vytvořit data factory a zřídit runtime integrace Azure-SSIS. Modul runtime integrace Azure-SSIS je plně spravovaný cluster virtuálních počítačů (uzlů) Azure, které jsou vyhrazené pro spouštění balíčků SSIS v cloudu. Podrobné pokyny najdete v tématu [nasazení balíčků SSIS do kurzu Azure.](tutorial-create-azure-ssis-runtime-portal.md) 
 
### <a name="sdks"></a>Sady SDK
Pokud jste pokročilý uživatel a hledáte programové rozhraní, data factory poskytuje bohatou sadu sad SDK, které můžete použít k vytváření, správu nebo monitorování kanálů pomocí oblíbeného rozhraní IDE. Jazyková podpora zahrnuje rozhraní .NET, PowerShell, Python a REST.

### <a name="monitoring"></a>Monitorování
Továren dat můžete sledovat prostřednictvím prostředí PowerShell, SDK nebo nástroje vizuálního monitorování v uživatelském rozhraní prohlížeče. Můžete sledovat a spravovat vlastní toky na vyžádání, založené na aktivačních událostech a řízené hodinami efektivním a efektivním způsobem. Zrušte existující úkoly, podívejte se na selhání na první pohled, přejděte k podrobnostem a získejte podrobné chybové zprávy a laděte problémy, to vše z jednoho podokna skla bez přepínání kontextu nebo navigace mezi obrazovkami. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nové funkce pro SSIS v datové továrně
Od počáteční verze Public Preview v 2017, Data Factory přidal následující funkce pro SSIS:

-    Podpora dalších tří konfigurací/variant databáze Azure SQL database pro hostování databáze SSIS (SSISDB) projektů/balíčků:
-    Databáze SQL s koncovými body služby virtuální sítě
-    Spravovaná instance
-    Elastický fond
-    Podpora virtuální sítě Azure Resource Manager u klasické virtuální sítě, která se má v budoucnu zanesávat, což vám umožní vložit nebo připojit modul runtime integrace Azure-SSIS do virtuální sítě nakonfigurované pro SQL Database s koncovými body služby VIRTUÁLNÍ SÍTĚ/přístup k místním datům služby MI/místní. Další informace najdete v [tématu také připojit k prostředí integrace Azure-SSIS do virtuální sítě](join-azure-ssis-integration-runtime-virtual-network.md).
-    Podpora ověřování Azure Active Directory (Azure AD) a ověřování SQL pro připojení k SSISDB, povolení ověřování Azure AD s vaší identitou spravované službou Data Factory pro prostředky Azure
-    Podpora pro získání vlastní místní licence SQL Serveru, která umožňuje dosáhnout značných úspor nákladů díky možnosti Hybridní výhoda Azure
-    Podpora modulu runtime integrace Azure-SSIS pro Enterprise Edition, která umožňuje používat pokročilé/prémiové funkce, vlastní instalační rozhraní pro instalaci dalších komponent/rozšíření a partnerský ekosystém. Další informace naleznete také [v tématu Enterprise Edition, Custom Setup a 3rd Party Rozšiřitelnost pro SSIS v ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/). 
-    Hlubší integrace SSIS v datové továrně, která vám umožní vyvolat/aktivovat prvotřídní aktivity balíčku SSIS v kanálech Data Factory a naplánovat je přes SSMS. Další informace naleznete také [v tématu Modernizace a rozšíření pracovních postupů ETL/ELT s aktivitami SSIS v kanálech ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/).


## <a name="what-is-the-integration-runtime"></a>Co je integrační runtime?
Integrační runtime je výpočetní infrastruktura, kterou Azure Data Factory používá k poskytování následujících možností integrace dat v různých síťových prostředích:

- **Přesun dat**: Pro přesun dat, integrační runtime přesune data mezi zdrojovým a cílovým úložištěm dat a zároveň poskytuje podporu pro vestavěné konektory, převod formátu, mapování sloupců a výkonný a škálovatelný přenos dat.
- **Aktivity odeslání**: Pro transformaci umožňuje integrační runtime nativně spouštět balíčky SSIS.
- **Spouštět balíčky SSIS:** Integrační runtime nativně spouští balíčky SSIS ve spravovaném výpočetním prostředí Azure. Modul runtime integrace také podporuje dispečink a monitorování transformačních aktivit spuštěných na různých výpočetních službách, jako je Azure HDInsight, Azure Machine Learning, SQL Database a SQL Server.

Můžete nasadit jednu nebo více instancí integračního běhu podle potřeby pro přesun a transformaci dat. Prostředí integrace runtime lze spustit ve veřejné síti Azure nebo v privátní síti (místní, virtuální síť Azure nebo virtuální privátní cloud Amazon Web Services [VPC]). 

Další informace najdete v tématu [Prostředí Integration Runtime v Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Jaký je limit počtu integračních runčasů?
Počet instancí prostředí runtime integrace, které můžete mít v datové továrně, není nijak omezen. Existuje však omezení počtu jader virtuálních počítačů, které může integrační runtime použít na jedno předplatné pro spuštění balíčku SSIS. Další informace naleznete v [tématu Data Factory limity](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Jaké jsou koncepty nejvyšší úrovně Azure Data Factory?
Předplatné Azure může obsahovat jednu nebo více instancí služby Azure Data Factory (neboli datových továren). Azure Data Factory obsahuje čtyři klíčové součásti, které spolupracují jako platforma, na které můžete vytvořit pracovní postupy řízené daty s kroky pro přesun a transformaci dat.

### <a name="pipelines"></a>Kanály
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit k provedení jednotky práce. Aktivity v kanálu společně provádí úlohy. Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure a pak spustí dotaz Hive v clusteru HDInsight k rozdělení dat. Výhodou je, že můžete použít kanál ke správě aktivit jako sadu namísto nutnosti spravovat každou aktivitu jednotlivě. Můžete zřetězit aktivity v kanálu pro jejich provoz postupně, nebo je můžete provozovat nezávisle, paralelně.

### <a name="data-flows"></a>Toky dat
Toky dat jsou objekty, které vytváříte vizuálně v Datové továrně, které transformují data ve velkém měřítku na back-endových službách Spark. Nemusíte rozumět programování nebo Vnitřní sparku. Stačí navrhnout záměr transformace dat pomocí grafů (Mapování) nebo tabulek (Hádání).

### <a name="activities"></a>Aktivity
Aktivity představují krok zpracování v rámci kanálu. Aktivitu Kopírování můžete například použít ke kopírování dat z jednoho úložiště dat do jiného úložiště dat. Podobně můžete použít aktivitu Hive, která spouští dotaz Hive v clusteru Azure HDInsight k transformaci nebo analýze dat. Data Factory podporuje tři typy aktivit: aktivity přesunu dat, aktivity transformace dat a aktivity řízení.

### <a name="datasets"></a>Datové sady
Datové sady představují datové struktury v rámci úložišť dat, které jednoduše odkazují na data, která chcete ve svých aktivitách použít jako vstupy nebo výstupy. 

### <a name="linked-services"></a>Propojené služby
Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Představte si to takto: Propojená služba definuje připojení ke zdroji dat a datová sada představuje strukturu dat. Například propojené služby Azure Storage určuje připojovací řetězec pro připojení k účtu Azure Storage. A azure blob datová sada určuje kontejner objektů blob a složku, která obsahuje data.

Propojené služby mají v Datové továrně dva účely:

- Chcete-li reprezentovat *úložiště dat,* které zahrnuje, ale není omezeno na místní instanci SQL Serveru, databázovou instanci Oracle, sdílenou složku nebo účet úložiště azure blob. Seznam podporovaných úložišť dat najdete [v tématu Kopírování aktivity v Azure Data Factory](copy-activity-overview.md).
- Představují *výpočetní prostředek*, který může hostovat provádění aktivity. Například hdinsight hive aktivita běží na HDInsight Hadoop clusteru. Seznam transformačních aktivit a podporovaných výpočetních prostředí najdete [v tématu Transformace dat v Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Aktivační události
Aktivační události představují jednotky zpracování, které určují, kdy je spuštění kanálu zahájeno. Pro různé typy událostí existují různé typy aktivačních událostí. 

### <a name="pipeline-runs"></a>Spuštění kanálu
Spuštění kanálu je instancí instance spuštění kanálu. Obvykle konsitujete kanálu spustit předáním argumenty na parametry, které jsou definovány v kanálu. Argumenty můžete předat ručně nebo v rámci definice aktivační události.

### <a name="parameters"></a>Parametry
Parametry jsou dvojice klíč hodnota v konfiguraci jen pro čtení.Definujete parametry v kanálu a předat argumenty pro definované parametry během provádění z kontextu spuštění. Kontext spuštění je vytvořen aktivační událostí nebo z kanálu, který spustíte ručně. Aktivity v rámci kanálu využívají hodnoty parametrů.

Datová sada je parametr silného typu a entita, kterou můžete znovu použít nebo na ni odkazovat. Aktivita může odkazovat na datové sady a může využívat vlastnosti, které jsou definovány v definici datové sady.

Propojená služba je také silně zadaný parametr, který obsahuje informace o připojení k úložišti dat nebo výpočetnímu prostředí. Je to také entita, kterou můžete znovu použít nebo odkazovat.

### <a name="control-flows"></a>Řídicí toky
Toky řízení orchestrují aktivity kanálu, které zahrnují zřetězení aktivity v pořadí, větvení, parametry, které definujete na úrovni kanálu a argumenty, které předáte při vyvolání kanálu na vyžádání nebo z aktivační události. Toky řízení také zahrnují vlastní stav předávání a opakování kontejnery (to znamená foreach iterátory).


Další informace o konceptech služby Data Factory najdete v následujících článcích:

- [Datové sady a propojené služby](concepts-datasets-linked-services.md)
- [Kanály a aktivity](concepts-pipelines-activities.md)
- [Prostředí Integration Runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Jaký je cenový model pro data factory?
Podrobnosti o cenách Azure Data Factory najdete v [tématu Podrobnosti o cenách Datové továrny](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Jak můžu mít aktuální informace o datové továrně?
Nejaktuálnější informace o Azure Data Factory najdete na následujících webech:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Domovská stránka dokumentace](/azure/data-factory)
- [Domovská stránka produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Technické hloubkové potápění 

### <a name="how-can-i-schedule-a-pipeline"></a>Jak lze naplánovat kanál? 
Aktivační událost plánovače nebo aktivační událost časového okna můžete naplánovat kanál. Aktivační událost používá plán kalendáře nástěnných hodin, který může pravidelně plánovat kanály nebo v opakujících se vzorcích založených na kalendáři (například v pondělí v 18:00 a ve čtvrtek ve 21:00). Další informace najdete v tématu [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Je možné předat parametry spuštění kanálu?
Ano, parametry jsou prvotřídní koncept nejvyšší úrovně v datové továrně. Můžete definovat parametry na úrovni kanálu a předat argumenty při spuštění kanálu spustit na vyžádání nebo pomocí aktivační události.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Je možné definovat výchozí hodnoty parametrů kanálu? 
Ano. Můžete definovat výchozí hodnoty pro parametry v potrubí. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Může aktivita v kanálu spotřebovávat argumenty, které jsou předány spuštění kanálu? 
Ano. Každá aktivita v rámci kanálu může spotřebovat hodnotu parametru, která je předána do kanálu a spustit s konstrukcí. `@parameter` 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Může být vlastnost výstupu aktivity spotřebována v jiné aktivitě? 
Ano. Výstup aktivity může být spotřebován v následné aktivitě s konstrukcí. `@activity`
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak lze řádně zpracovat hodnoty null ve výstupu aktivity? 
Konstrukce ve `@coalesce` výrazech můžete použít k řádnému zpracování hodnot null. 

## <a name="mapping-data-flows"></a>Toky dat mapování

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Potřebuji pomoc při řešení potíží s logikou toku dat. Jaké informace musím poskytnout, abych mohl získat pomoc?

Pokud společnost Microsoft poskytuje pomoc nebo řešení potíží s toky dat, zadejte skript toku dat. Toto je skript s kódem na pozadí z grafu toku dat. V uzd adf otevřete tok dat a klikněte na tlačítko Skript v pravém horním rohu. Zkopírujte a vložte tento skript nebo jej uložte do textového souboru.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Jak lze získat přístup k datům pomocí dalších 90 typů datových sad v datové tísmě?

Funkce toku dat mapování aktuálně umožňuje Azure SQL Database, Azure SQL Data Warehouse, oddělené textové soubory z úložiště objektů Blob Azure nebo Azure Data Lake Storage Gen2 a parketové soubory z úložiště objektů Blob nebo Data Lake Storage Gen2 nativně pro zdroj a jímku. 

Pomocí aktivity Copy můžete vytvořit data z libovolného jiného konektoru a poté spusťte aktivitu toku dat k transformaci dat po jejich fázi. Například váš kanál se nejprve zkopíruje do úložiště objektů Blob a pak aktivita toku dat použije datovou sadu ve zdroji k transformaci těchto dat.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Je pro toky dat k dispozici vlastní hostovaný zaběhový čas integrace?

Samoobslužné infračervené ovládání je konstrukce kanálu ADF, kterou můžete použít s aktivitou kopírování k získání nebo přesunutí dat do a z zdrojů dat a jímek založených na vozena na základě vina nebo z virtuálního počítače. Nejprve zinscenujte data pomocí kopie, potom tok dat pro transformaci a potom následující kopii, pokud potřebujete přesunout transformovaná data zpět do úložiště on-prem.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Slouží výpočetní modul toku dat více klientům?
Clustery jsou nikdy sdíleny. Garantujeme izolaci pro každou úlohu spuštěnou v produkčním běhu. V případě scénáře ladění jedna osoba získá jeden cluster a všechny debugy přejdou do tohoto clusteru, které jsou iniciovány tímto uživatelem.

## <a name="wrangling-data-flows"></a>Hašteření datových toků

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Jaké jsou podporované oblasti pro hádání toku dat?

Hašteření toku dat je aktuálně podporováno v továrnách na data vytvořených v následujících oblastech:

* Austrálie – východ
* Střední Kanada
* Indie – střed
* USA – východ
* USA – východ 2
* Japonsko – východ
* Severní Evropa
* Jihovýchodní Asie
* USA – středojih
* Spojené království – jih
* USA – středozápad
* Západní Evropa
* USA – západ
* USA – západ 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Jaká jsou omezení a omezení při hádání datového toku?

Názvy datových sad mohou obsahovat pouze alfanumerické znaky. Jsou podporována následující úložiště dat:

* Datová sada DelimitedText ve službě Azure Blob Storage pomocí ověřování pomocí klíče účtu
* Datová sada DelimitedText v gen2 služby Azure Data Lake Storage pomocí klíče účtu nebo ověřování instančního objektu
* Datová sada DelimitedText v gen1 služby Azure Data Lake Storage pomocí ověřování instančního objektu
* Azure SQL Database a datový sklad pomocí ověřování SQL. Viz podporované typy SQL níže. Neexistuje žádná polybase nebo pracovní podpora pro datový sklad.

V současné době integrace úložiště klíčů propojené služby není podporována v tahácích datových toků.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Jaký je rozdíl mezi mapováním a hádavým datovým tokem?

Mapování datových toků poskytuje způsob transformace dat ve velkém měřítku bez nutnosti kódování. Úlohu transformace dat můžete navrhnout na plátně toku dat vytvořením řady transformací. Začněte s libovolným počtem zdrojových transformací následovaných kroky transformace dat. Dokončete tok dat jímkou, abyste výsledky získali v cíli. Mapování toku dat je skvělé při mapování a transformaci dat se známými i neznámými schématy v jímky a zdroje.

Hašteření datových toků vám umožní provést agilní přípravu a průzkum dat pomocí editoru mashup Power Query Online ve velkém měřítku prostřednictvím spuštění jiskry. S nárůstem datových jezer někdy stačí prozkoumat datovou sadu nebo vytvořit datovou sadu v jezeře. Nemapujete na známý cíl. Hašteření datových toků se používá pro méně formální a modelové analytické scénáře.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Jaký je rozdíl mezi toky dat na platformě Power Platform a tahanicemi datových toků?

Toky dat na platformě Power Platform umožňují uživatelům importovat a transformovat data z široké škály zdrojů dat do common data service a Azure Data Lake a vytvářet aplikace PowerApps, sestavy Power BI nebo automatizaci toku. Toky dat na Power Platform využívají zavedené prostředí přípravy dat Power Query, podobně jako Power BI a Excel. Toky dat na platformě Power Platform také umožňují snadné opakované použití v rámci organizace a automaticky zpracovávají orchestraci (např. automatické aktualizace toků dat, které závisí na jiném toku dat při aktualizaci prvního).

Azure Data Factory (ADF) je služba spravované integrace dat, která umožňuje datovým inženýrům a integrátoru dat občanů vytvářet složité hybridní pracovní postupy etl a přeměnit zatížení (ELT). Hašteření toku dat v ADF umožňuje uživatelům s kódem-free, bez serveru prostředí, které zjednodušuje přípravu dat v cloudu a škáluje na libovolnou velikost dat bez nutnosti správy infrastruktury. K přípravě a tvarování dat používá technologii přípravy dat Power Query (používanou také v tocích dat Power Platform, Excelu, Power BI). Hádání datových toků, které je navrženo tak, aby zvládlo všechny složitosti a škálování výzev integrace velkých objemů dat, umožňuje uživatelům rychle připravit data ve velkém měřítku prostřednictvím spuštění jiskry. Uživatelé mohou vytvářet odolné datové kanály v přístupném vizuálním prostředí s naším rozhraním založeným na prohlížeči a nechat ADF zpracovávat složitosti spuštění Spark. Vytvářejte plány pro vaše kanály a sledujte spouštění toku dat z monitorovacího portálu ADF. Snadno spravujte sla s dostupností dat pomocí bohatého monitorování dostupnosti a výstrah a využijte integrované možnosti průběžné integrace a nasazení k ukládání a správě toků ve spravovaném prostředí. Vytvořte výstrahy a zobrazit plány spuštění k ověření, že vaše logika funguje podle plánu při ladění toků dat.

### <a name="supported-sql-types"></a>Podporované typy SQL

Hašteření toku dat podporuje následující datové typy v SQL. Zobrazí se chyba ověření pro použití datového typu, který není podporován.

* short
* double
* reálná
* float
* char
* Nchar
* varchar
* nvarchar
* celé číslo
* int
* bitové
* Boolean
* smallint
* tinyint
* bigint
* long
* text
* date
* datetime
* datetime2
* Smalldatetime
* časové razítko
* uniqueidentifier
* xml

Další datové typy budou podporovány v budoucnu.

## <a name="next-steps"></a>Další kroky
Podrobné pokyny k vytvoření datové továrny najdete v následujících kurzech:

- [Úvodní příručka: Vytvoření datové továrny](quickstart-create-data-factory-dot-net.md)
- [Kurz: Kopírování dat v cloudu](tutorial-copy-data-dot-net.md)
