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
ms.openlocfilehash: be0cdeed81c66e1a848b44d2429c1c67bce9b4f3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024089"
---
# <a name="azure-data-factory-faq"></a>Nejčastější dotazy k Azure Data Factory
Tento článek obsahuje odpovědi na nejčastější dotazy ohledně služby Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Co je služba Azure Data Factory? 
Objekt pro vytváření dat je plně spravovaná, cloudových dat integrační služba, která automatizuje přesouvání a transformaci dat. Podobně jako objekt pro vytváření, na kterém běží zařízení pro transformaci suroviny na hotové výroby Azure Data Factory orchestruje stávající služby, které sbírají nezpracovaná data a transformují je na informace připravené k použití. 

Pomocí služby Azure Data Factory můžete vytvářet pracovní postupy řízené daty pro přesun dat mezi místním prostředím a cloudem datových úložišť. Můžete zpracovávat a transformovat data pomocí výpočetních služeb, jako je například Azure HDInsight, Azure Data Lake Analytics a SQL Server Integration Services (SSIS) modulu integration runtime. 

Pomocí služby Data Factory můžete spustit zpracování dat na základě Azure cloudové službě nebo ve vlastní v místním prostředí výpočetní prostředí, například služby SSIS, SQL Server nebo Oracle. Jakmile vytvoříte kanál, který provede akci, kterou potřebujete, můžete ji naplánovat ke spuštění pravidelně (například hodinové, denní nebo týdenní), plánování okno čas nebo aktivační událost kanálu z výskytu události. Další informace najdete v tématu [Úvod do Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Toků řízení a škálování 
K podpoře různorodých integračních toků a vzorů moderního datového skladu, Data Factory umožňuje modelování kanál flexibilní dat, obsahující úplné řízení toku programovací modely podmíněného spuštění, včetně větvení v datových kanálech a explicitně předejte parametry v těchto tocích a mezi. Tok řízení také zahrnuje transformace dat pomocí aktivity odeslání do externí provádění modulů a možnosti toku dat, včetně přesouvání dat ve velkém měřítku pomocí aktivity kopírování.

Data Factory poskytuje svobodu modelovat jakýkoli styl toku, který je potřebný pro integraci dat a který může odesílat na vyžádání nebo opakovaně podle plánu. Mezi běžné toky, které tento model umožňuje jsou:   

- Toků řízení:
    - Řetězec aktivit v sekvenci v rámci kanálu.
    - Větev aktivity v rámci kanálu.
    - Parametry
        - Definování parametrů na úrovni kanálu a předejte argumenty při volání kanálu na vyžádání nebo pomocí aktivační události.
        - Aktivity mohou využívat argumenty předávané do kanálu.
    - Předávání vlastního stavu
        - Výstupy aktivity včetně stavu může používat následující aktivita v kanálu.
    - Kontejnery smyček
        - For-each 
- Toky založené na aktivačních:
    - Kanály se můžou spouštět na vyžádání nebo podle času plánovače.
- Toky rozdílů:
    - Použití parametrů a definovat vaše horní mez pro rozdílové kopírování při přesouvání tabulek dimenzí nebo referenčních tabulek z relačního úložiště, ať už místní nebo v cloudu, k načtení dat do jezera. 

Další informace najdete v tématu [kurzu: Řízení toků](tutorial-control-flow.md).

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>Transformujte svá data ve velkém měřítku s bezplatnou kanály kódu
Nové prostředí využívající prohlížeč nástroje obsahuje bez použití kódu kanálu pro vytváření a nasazování moderních, interaktivních webových prostředí s.

Pro vývojáře vizuálních dat a datoví architekti je ADF webové uživatelské rozhraní bez kódu návrhové prostředí, který budete používat a vytvořit tak kanály. Je plně integrována s Git služby Visual Studio Online a zajišťuje integraci pro CI/CD a iterativního vývoje s možností ladění.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Bohaté různé sady SDK platformy pro pokročilé uživatele
Pokud jste pokročilý uživatel a hledáte programové rozhraní, ADF V2 poskytuje bohatou sadu SDK, které můžete využít k vytváření, správy, monitorování kanálů pomocí vašeho oblíbeného integrovaného vývojového prostředí
1.  Python SDK
2.  Rozhraní příkazového řádku PowerShellu
3.  C# SDK uživatelům také můžete využít zdokumentovaných rozhraní REST API pro rozhraní s ADF V2

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Iterativní vývoj a ladění pomocí visual tools
Azure Data Factory (ADF) visual tools umožňují provést iterativní vývoj a ladění. Můžete vytvořit kanály a testovacích běhů pomocí funkce ladění na plátně kanálu, aniž byste museli napsat jediný řádek kódu. V okně výstupu plátna kanálu můžete zobrazit výsledky testovacích běhů. Po úspěšném vašeho testovacího běhu, můžete do kanálu přidat další aktivity a pokračovat v ladění iteračním způsobem. Můžete také zrušit testovacích běhů, jakmile jsou v průběhu. Není nutné publikovat provedené změny do služby data factory před kliknutím na tlačítko ladění. To je užitečné ve scénářích, kde chcete, abyste měli jistotu, že novou práci dodatky nebo změny podle očekávání, než budete aktualizovat pracovní postupy objekt pro vytváření dat v vývoj, testování, nebo produkční prostředí. 

### <a name="deploy-ssis-packages-to-azure"></a>Nasazení balíčků služby SSIS do Azure 
Pokud chcete přesunout úlohy služby SSIS, můžete vytvořit datovou továrnu a zřízení prostředí Azure-SSIS integration runtime. Prostředí Azure-SSIS integration runtime je plně spravovaný cluster virtuálních počítačů Azure (uzlů) vyhrazených ke spouštění balíčků služby SSIS v cloudu. Podrobné pokyny najdete v tématu [balíčků služby SSIS nasazovat do Azure](tutorial-create-azure-ssis-runtime-portal.md) kurzu. 
 
### <a name="sdks"></a>Sady SDK
Pokud jste pokročilý uživatel a hledáte programové rozhraní, ADF poskytuje pestrou nabídku sad SDK, která vám pomůže vytvářet, spravovat nebo monitorování kanálů pomocí vašeho oblíbeného integrovaného vývojového prostředí. Podpora jazyků zahrnuje .NET, PowerShell, Python a REST.

### <a name="monitoring"></a>Monitorování
Můžete monitorovat vaší datové továrny pomocí prostředí PowerShell, sady SDK nebo Visual Tools sledování v uživatelském rozhraní prohlížeče. Můžete monitorovat a spravovat na vyžádání, na základě aktivační události a hodiny vlastní toky řízené účinný a efektivní způsobem. Zrušit existující úlohy, viz chyby na první pohled, k podrobnostem a získat podrobné chybové zprávy a ladit problémy z podokně ze skla bez kontextu přepínání nebo přejdete vpřed a zpět mezi obrazovkami. 

### <a name="new-features-for-ssis-in-adf"></a>Nové funkce pro službu SSIS ve službě ADF
Od původní verze Public Preview v 2017 Data Factory přináší následující funkce pro službu SSIS:

-   Podpora pro tři další konfigurace/varianty z Azure SQL Database (databáze) do hostitele katalogu služby SSIS (SSISDB) projektů/balíčků:
-   Azure SQL Database s koncovými body služby virtuální sítě
-   Spravované Instance (IU)
-   Elastický fond
-   Podpora pro Azure Resource Manageru Virtual Network (VNet) nad klasickou virtuální síť, která se nepoužívají v budoucnu – díky tomu můžete vložit/spojení vašeho prostředí Azure-SSIS Integration Runtime (IR) k virtuální síti, který je nakonfigurovaný pro službu Azure SQL DB pomocí koncových bodů služby virtuální sítě nebo MI / místní přístup k datům, naleznete v tématu: https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   Podpora pro ověřování Azure Active Directory (AAD) nad rámec ověřování SQL pro připojení k vaší databáze SSISDB – díky tomu můžete použít ověřování AAD pomocí vaší ADF spravované identity pro prostředky Azure
-   Podpora pro přenesení svoji vlastní licenci systému SQL Server v místním získat značné úspory z možnosti Azure Hybrid Benefit (AHB)
-   Podporu pro Enterprise Edition z Azure-SSIS IR, která vám umožní používat pokročilé/prémiové funkce, vlastní nastavení, chcete-li nainstalovat další součásti nebo rozšíření a 3. stran ekosystému, naleznete v tématu: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   Hlubší integrace služby SSIS ve službě ADF, která umožňuje vyvolání nebo triggeru prvotřídní spuštění balíčku služby SSIS aktivit v kanálech ADF a naplánují je prostřednictvím aplikace SSMS, naleznete v tématu: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>Co je IR?
Prostředí IR je výpočetní infrastruktura, která se službou Azure Data Factory používá k zajištění následujících funkcí integrace dat v různých síťových prostředích:

- **Přesun dat**: V případě přesunu dat prostředí IR přesouvá data mezi zdrojovým a cílovým úložištěm dat a přitom poskytuje podporu integrovaných konektorů, převodu formátů, mapování sloupců a výkonného a škálovatelného přenosu dat.
- **Odeslání aktivity**: V případě transformace umožňuje prostředí IR nativní spouštění balíčků SSIS.
- **Spouštění balíčků služby SSIS**: Nativně spustí balíčků služby SSIS v prostředí spravované výpočetní prostředky Azure. Prostředí IR podporuje také volání a monitorování aktivit transformace spuštěných v různých výpočetních službách, jako je Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server a další.

Pro přesun a transformaci dat podle potřeby můžete nasadit jednu nebo více instancí modulu integration runtime. Prostředí Integration runtime můžete spustit ve veřejné síti Azure nebo v privátní síti (místní, virtuální sítě Azure nebo Amazon Web Services virtuálním privátním cloudu [VPC]). 

Další informace najdete v tématu [Prostředí Integration Runtime v Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Jaký je limit počtu prostředí integration runtime?
Neexistuje žádné vynucené omezení na počet instancí modulu runtime integrace, může být v datové továrně. Existuje ale omezení na počet jader virtuálního počítače, které prostředí integration runtime můžete použít jedno předplatné, pro spouštění balíčku služby SSIS. Další informace najdete v tématu [omezuje služby Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Co jsou koncepty nejvyšší úrovně služby Azure Data Factory?
Předplatné Azure může obsahovat jednu nebo více instancí služby Azure Data Factory (neboli datových továren). Azure Data Factory obsahuje čtyř klíčových komponent, které budou spolupracovat jako platformu, na které můžete vytvářet pracovní postupy řízené daty s kroky pro přesun a transformaci dat.

### <a name="pipelines"></a>Kanály
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit, které provádějí určitou jednotku práce. Aktivity v kanálu společně provádí úlohy. Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure a pak spustit dotaz Hive v clusteru služby HDInsight pro rozdělení dat. Výhodou je, že vám pomůže kanálu spravovat aktivity jako sadu, a ne by bylo potřeba spravovat jednotlivé aktivity jednotlivě. Můžete společně řetězit aktivity v kanálu pro jejich probíhaly postupně, nebo můžete provozovat nezávisle na sobě paralelně.

### <a name="activity"></a>Aktivita
Aktivity představují krok zpracování v rámci kanálu. Například můžete použít *kopírování* aktivitu ke kopírování dat z jednoho úložiště dat do jiného. Podobně můžete použít aktivitu Hivu, která spustí dotaz Hivu na clusteru Azure HDInsight, aby transformoval a analyzoval vaše data. Data Factory podporuje tři typy aktivit: aktivity přesunu dat, aktivity transformace dat a aktivity řízení.

### <a name="datasets"></a>Datové sady
Datové sady představují datové struktury v rámci úložišť dat, které jednoduše odkazují na data, která chcete ve svých aktivitách použít jako vstupy nebo výstupy. 

### <a name="linked-services"></a>Propojené služby
Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Můžete ho chápat takto: propojená služba definuje připojení ke zdroji dat, a reprezentuje strukturu dat datové sady. Například propojenou službu Azure Storage Určuje připojovací řetězec pro připojení k účtu Azure Storage. A datovou sadu Azure Blob Určuje kontejner objektů blob a složku obsahující data.

Propojené služby mají dva účely ve službě Data Factory:

- K reprezentaci *úložiště dat* , který obsahuje, ale není omezena pouze na místní instance systému SQL Server, instanci Oracle database, sdílené složky nebo účtu služby Azure Blob storage. Seznam podporovaných úložišť dat najdete v tématu [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md).
- Představují *výpočetní prostředek*, který může hostovat provádění aktivity. Například aktivita HDInsight Hive spustí v clusteru HDInsight Hadoop. Seznam aktivit transformace a podporovaných výpočetních prostředí najdete v tématu [transformace dat ve službě Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Aktivační události
Aktivační události představují jednotky zpracování, které určují, kdy je spuštěna provádění zřetězeného příkazu. Pro různé typy událostí existují různé typy aktivačních událostí. 

### <a name="pipeline-runs"></a>Spuštění kanálu
Spuštění kanálu je instance provádění kanálu. Obvykle vytvoříte instanci kanálu předáváním argumentů do parametrů, které jsou definované v kanálu. Argumenty můžete předat ručně nebo v rámci definice aktivační události.

### <a name="parameters"></a>Parametry
Parametry jsou páry klíč hodnota v konfiguraci jen pro čtení. Definovat parametry v kanálu a předejte argumenty definovaných parametrů během provádění z kontextu spuštění. Vytvoření kontextu spuštění pomocí aktivační události nebo z kanálu, který můžete spustit ručně. Aktivity v rámci kanálu využívají hodnoty parametrů.

Datové sady je parametr silného typu a entita, která můžete opakovaně používat nebo odkazovat. Aktivita může odkazovat na datové sady a ho může využívat vlastnosti definované v definici datové sady.

Propojená služba je také parametr silného typu, který obsahuje informace o připojení k úložišti dat nebo výpočetnímu prostředí. Také je entita, která můžete opakovaně používat nebo odkazovat.

### <a name="control-flows"></a>Řízení toků
Řízení toků orchestraci aktivit kanálu, které zahrnují řetězení aktivit v sekvenci, větvení, parametry, které můžete definovat na úrovni kanálu a argumenty předávané při volání kanálu na vyžádání nebo pomocí aktivační události. Ovládací prvek toky také předávání vlastního stavu a kontejnery (tedy iterátory for-each) smyček.


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
Aktivační událost plánovače nebo aktivační událost okna čas můžete použít k naplánování kanálu. Trigger použije hodinového plánu kalendáře a použít ji k plánování kanálů, pravidelně nebo s použitím na základě kalendáře opakující se vzory (například každý týden v pondělí v 18: 00 a čtvrtek ve 21: 00). Další informace najdete v tématu [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Můžete předat parametry pro spuštění kanálu
Ano, parametry jsou koncept první třídy, nejvyšší úrovně ve službě ADF. Můžete definovat parametry na úrovni kanálu a předejte argumenty při spuštění kanálu spouštěly na vyžádání nebo pomocí aktivační události.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Můžete definovat výchozí hodnoty pro parametry kanálu? 
Ano. Výchozí hodnoty pro parametry můžete definovat v kanálech. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Aktivita v kanálu mohou využívat argumenty předávané do kanálu? 
Ano. Každá aktivita v rámci kanálu využívají hodnotu parametru, který je předaný kanálu a spuštění pomocí `@parameter` vytvořit. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Mohou být spotřebovány vlastnost výstup aktivity v jiné aktivitě? 
Ano. Výstup aktivity mohou být spotřebovány v následné aktivity s `@activity` vytvořit.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak můžu řádně zpracování hodnot null ve výstup aktivity? 
Můžete použít `@coalesce` vytvořit ve výrazech řádně zpracování hodnot null. 

## <a name="next-steps"></a>Další postup
Podrobné pokyny pro vytváření dat najdete v následujících kurzech:

- [Rychlý start: Vytvoření datové továrny](quickstart-create-data-factory-dot-net.md)
- [Kurz: Kopírování dat v cloudu](tutorial-copy-data-dot-net.md)
