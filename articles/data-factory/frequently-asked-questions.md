---
title: 'Azure Data Factory: Nejčastější dotazy | Microsoft Docs'
description: Získejte odpovědi na časté otázky o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: ebe8745db06113d0508d86554bf031a4235c8e44
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045945"
---
# <a name="azure-data-factory-faq"></a>Nejčastější dotazy k Azure Data Factory
Tento článek obsahuje odpovědi na nejčastější dotazy k Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Co je služba Azure Data Factory? 
Objekt pro vytváření dat je plně spravovaná, cloudových dat integrační služba, která automatizuje přesouvání a transformaci dat. Objekt factory, který spouští zařízení k transformaci suroviny na hotové výroby, jako je Azure Data Factory orchestruje stávající služby, které sbírají nezpracovaná data a transformují je na informace připravené k použití. 

Pomocí Azure Data Factory můžete vytvořit datové pracovní postupy pro přesun dat mezi místními a cloudovými datová úložiště. A můžete zpracovat a transformace dat pomocí výpočetních služeb například runtime integrace Azure HDInsight, Azure Data Lake Analytics a integrační služby SSIS (SQL Server). 

Pomocí služby Data Factory můžete provést zpracování dat na základě Azure cloudové služby nebo ve svém vlastním vlastním hostováním výpočetní prostředí, například služby SSIS, SQL Server nebo Oracle. Po vytvoření kanálu, který provede akci, která je nutné, můžete ji naplánovat na spuštění pravidelně (například hodinové, denní nebo týdenní), časové okno plánování nebo aktivační událost kanálu z výskytu události. Další informace najdete v tématu [Úvod do Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Tok řízení a škálování 
Pro podporu různých integrace toky a vzory v moderní datového skladu, Data Factory umožňuje modelování kanálu flexibilní dat, která zahrnuje úplné řízení toku programování vzorů včetně podmíněného spuštění, vytvoření větve v datových kanálech a explicitně předejte parametry v rámci a přes tyto toky. Tok řízení také zahrnuje transformace dat pomocí aktivity odeslání do externího provádění moduly a funkce toku dat, včetně přesun dat ve velkém měřítku, pomocí aktivity kopírování.

Data Factory poskytuje volného modelu všechny toku styl, který vyžaduje pro integraci dat a který může odeslat na vyžádání nebo opakovaně podle plánu. Několik běžných toky, které tento model umožňuje jsou:   

- Tok řízení:
    - Řetězec činnosti v pořadí v rámci kanálu.
    - Větev aktivity v rámci kanálu.
    - Parametry
        - Zadejte parametry na úrovni kanálu a předání argumentů při vyvolání kanálu na vyžádání nebo z aktivační událost.
        - Aktivity mohou využívat argumenty předávané do kanálu.
    - Předávání vlastního stavu
        - Výstupy aktivity včetně stavu může používat následující aktivita v kanálu.
    - Kontejnery smyček
        - For-each 
- Na základě aktivační událost toky:
    - Kanály můžete spouštět na vyžádání nebo podle času wall hodin.
- Toky rozdílů:
    - Použijte parametry a definovat vaše horní mez pro rozdílové kopii při přesouvání dimenze nebo referenční tabulky z relační úložiště, buď místní nebo v cloudu, a načíst data do jezera. 

Další informace najdete v tématu [kurz: řízení toky](tutorial-control-flow.md).

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>Transformovat data ve velkém měřítku se zřetězením příkazů volné kódu
Nové prostředí založené na prohlížeči nástrojů poskytuje bez kódu kanálu vytváření a nasazování moderních, interaktivní webové prostředí.

ADF webového uživatelského rozhraní pro vizuální data vývojáři a techniky dat, je prostředí bez kódu návrhu, které budete používat k vytvoření kanálů. Ho jsou plně integrované s Visual Studio Online Git a poskytuje integraci se pro CI/CD a iterativní vývoj s možností ladění.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Bohaté křížové platformy sady SDK pro pokročilé uživatele
Pokud pro pokročilé uživatele a hledá programové rozhraní, ADF V2 poskytuje bohatou sadu SDK, které lze využít k vytváření, správu, monitorování kanálů pomocí vašeho oblíbeného rozhraní IDE
1.  Python SDK
2.  Rozhraní příkazového řádku prostředí PowerShell
3.  Uživatelé SDK jazyka C# můžete využít i zdokumentovaných rozhraní REST API rozhraní s ADF V2

### <a name="iterative-development-and-debugging-using-visual-tools"></a>Iterační vývoji a ladění pomocí nástrojů visual
Vizuální nástroje Azure Data Factory (ADF) umožňují udělat iterativní vývoj a ladění. Můžete vytvořit kanály a testování spuštění pomocí funkce ladění na plátno kanálu bez nutnosti napsat jediný řádek kódu. V okně výstupu z vašeho kanálu plátno můžete zobrazit výsledky testovací spouští. Po úspěšné spustit test, můžete přidat více aktivit do kanálu a pokračovat, ladění iterativní způsobem. Jakmile se v průběhu, můžete zrušit testovací spouští. Není nutné před kliknutím na tlačítko ladění publikování změn do služby data factory. To je užitečné v situacích, kde chcete Ujistěte se, že nový pracovní dodatky nebo změny podle očekávání, než budete aktualizovat vaše pracovní postupy objekt pro vytváření dat v vývoj, testování, nebo produkčního prostředí. 

### <a name="deploy-ssis-packages-to-azure"></a>Nasazení balíčků služby SSIS do Azure 
Pokud chcete přesunout úlohy služby SSIS, můžete vytvořit objekt pro vytváření dat a zřídit modulu runtime integrace Azure SSIS. Modul runtime integrace Azure SSIS je plně spravovaný cluster Azure virtuálních počítačů (uzlů), které jsou vyhrazené pro spouštění vlastních balíčků SSIS v cloudu. Podrobné pokyny najdete v tématu [balíčky SSIS nasadit do Azure](tutorial-create-azure-ssis-runtime-portal.md) kurzu. 
 
### <a name="sdks"></a>Sady SDK
Pokud jste pokročilé uživatele a hledá programové rozhraní, ADF poskytuje bohatou sadu SDK, které můžete vytvářet, spravovat nebo monitorování kanálů pomocí vašeho oblíbeného rozhraní IDE. Jazyková podpora zahrnuje rozhraní .NET, prostředí PowerShell, Python a REST.

### <a name="monitoring"></a>Monitorování
Můžete sledovat vaše Data Factory pomocí prostředí PowerShell, sady SDK nebo Visual monitorování nástroje v uživatelském rozhraní prohlížeče. Můžete sledovat a spravovat na vyžádání, aktivační události na základě a hodiny vlastní toky řízené efektivní a nejpřívětivějším způsobem. Zrušit existující úlohy, viz selhání na první pohled, k podrobnostem a získat podrobné chybové zprávy a ladění problémů vše z jedné podokně pohotovostní bez kontextu přepínání nebo navigace mezi obrazovky a zpět. 

### <a name="new-features-for-ssis-in-adf"></a>Nové funkce služby SSIS v ADF
Od počáteční verze Public Preview v 2017 přidal objekt pro vytváření dat pro služby SSIS následující funkce:

-   Podpora pro tři další konfigurace nebo variant z Azure SQL databáze (Database) hostitele služby SSIS katalogu projekty nebo balíčků (SSISDB):
-   Azure SQL DB s koncové body služby virtuální sítě
-   Spravované Instance (MI)
-   Elastický fond
-   Podpora pro Azure Resource Manager virtuální síť (VNet) nad klasické virtuální sítě, která bude v budoucnu – zastaralá tímto způsobem můžete vložit nebo připojení k vaší Azure SSIS integrace modulu Runtime (IR) k virtuální síti, který je nakonfigurován pro databázi SQL Azure s virtuální sítě služby koncových bodů nebo MI nebo místní přístup k datům, najdete v části: https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   Podpora pro ověřování Azure Active Directory (AAD) nad ověřování SQL pro připojení k vaší SSISDB – to vám umožní používat ověřování AAD s vaší ADF spravované služby Identity (MSI)
-   Podpora pro přinesou vlastní licenci na SQL Server místní získat úsporu nákladů významné z možnosti Azure hybridní Benefit (AHB)
-   Podpora pro Enterprise Edition z Azure SSIS reakcí na Incidenty, vám umožní používat funkce Rozšířené nebo premium, vlastní instalace nainstalujte další součásti nebo rozšíření a 3. stran ekosystém, najdete v článku: https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   Hlubší integrace služby SSIS v ADF, která umožňuje vyvolání nebo aktivační událost prvotřídní spuštění balíčku služby SSIS aktivity v ADF kanály a jejich plánování přes SSMS, najdete v části: https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>Co je modul runtime integrace?
Modul runtime integrace je na výpočetní infrastruktuře používaný službou Azure Data Factory k zajištění následující funkce integrace dat v různých prostředích sítě:

- **Přesun dat**: pro přesun dat, integraci Runtime přesouvá data mezi zdrojovým a cílovým úložištích dat, při poskytování podpory předdefinované konektory, převod formátu, mapování sloupců a původce a přenos dat škálovatelné.
- **Odesílání aktivity**: pro transformaci, integrace Runtime zajistit možnost nativně spustit balíčky SSIS.
- **Spuštění balíčky SSIS**: nativně provede v prostředí Azure spravovaná výpočetní balíčky SSIS. Prostředí IR podporuje také volání a monitorování aktivit transformace spuštěných v různých výpočetních službách, jako je Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server a další.

Jedna či více instancí integrace runtime můžete nasadit jako potřebné k přesunutí a transformovat data. Modul runtime integrace lze spustit v Azure veřejné síti nebo v privátní síti (místní, Azure Virtual Network nebo virtuální privátní cloud Amazon Web Services [VPC]). 

Další informace najdete v tématu [Prostředí Integration Runtime v Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Co je limit počtu integrační moduly runtime?
Neexistuje žádné pevný limit počtu instancí integrace modulu runtime, může být v datové továrně. Existuje ale omezení počtu jader virtuálního počítače, které modul runtime integrace můžete použít jedno předplatné pro spouštění balíčku služby SSIS. Další informace najdete v tématu [omezuje služby Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Jaké jsou nejvyšší úrovně koncepty objektu pro vytváření dat Azure?
Předplatné Azure může obsahovat jednu nebo více instancí služby Azure Data Factory (neboli datových továren). Azure Data Factory obsahuje čtyři klíčové komponenty, které vzájemně spolupracují jako platformu, na které můžete vytvořit datové pracovní postupy a kroky k přesunutí a transformovat data.

### <a name="pipelines"></a>Kanály
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit k provedení pracovní jednotka. Aktivity v kanálu společně provádí úlohy. Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure a pak spusťte dotaz Hive v clusteru HDInsight k oddílu dat. Výhodou je, že kanál můžete spravovat aktivity jako sada místo nutnosti lze spravovat jednotlivě každou aktivitu. Můžete společně zřetězit aktivity v kanálu je postupně pracovat, nebo můžete pracovat nezávisle paralelně.

### <a name="activity"></a>Aktivita
Aktivity představují krok zpracování v rámci kanálu. Například můžete použít *kopie* aktivity ke zkopírování dat z jednoho datového úložiště do jiného úložiště dat. Podobně můžete použít aktivitu Hive, která spustí dotaz Hive v clusteru Azure HDInsight aby transformoval a analyzoval vaše data. Data Factory podporuje tři typy aktivit: aktivity přesunu dat, aktivity transformace dat a aktivity řízení.

### <a name="datasets"></a>Datové sady
Datové sady představují datové struktury v rámci úložišť dat, které jednoduše odkazují na data, která chcete ve svých aktivitách použít jako vstupy nebo výstupy. 

### <a name="linked-services"></a>Propojené služby
Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Považujte ho takto: propojené služby definuje připojení ke zdroji dat, a představuje strukturu dat datové sady. Například služby Azure Storage, propojené Určuje připojovací řetězec pro připojení k účtu úložiště Azure. A sadu dat objektů Blob v Azure Určuje kontejner objektů blob a složky, která obsahuje data.

Propojené služby mají dva účely v objektu pro vytváření dat:

- Představují *úložiště dat* , zahrnuje, avšak není omezeno na místní instance systému SQL Server, instanci databáze Oracle, sdílené složky nebo účet služby Azure Blob storage. Seznam podporovaných datová úložiště najdete v tématu [aktivitu kopírování v Azure Data Factory](copy-activity-overview.md).
- Představují *výpočetní prostředek*, který může hostovat provádění aktivity. Například spuštění aktivity HDInsight Hive v clusteru HDInsight Hadoop. Seznam aktivit transformace i podporované výpočetní prostředí najdete v tématu [transformace dat v Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Triggery
Aktivační události představují jednotky zpracování, které určují, kdy je spuštěna provádění zřetězeného příkazu. Pro různé typy událostí existují různé typy aktivačních událostí. 

### <a name="pipeline-runs"></a>Spuštění kanálu
Spustit kanál je instance provádění zřetězeného příkazu. Obvykle vytvoří instanci kanálu spustit pomocí předání argumentů parametry, které jsou definovány v kanálu. Argumenty, které můžete předat ručně nebo v rámci definice aktivační události.

### <a name="parameters"></a>Parametry
Parametry jsou páry klíč hodnota v konfiguraci jen pro čtení. Můžete definovat parametry v kanálu a předání argumentů pro definovaných parametrů při spuštění z kontextu spuštění. Kontext spuštění se vytvoří aktivační událost nebo z kanál, který můžete spustit ručně. Aktivity v rámci kanálu využívají hodnoty parametrů.

Datové sady je parametr silného typu a entity, která můžete znovu použít, nebo odkaz. Aktivita, můžete odkazovat datových sad a spotřebovat vlastnosti, které jsou definovány v definici datové sady.

Propojené služby je také parametr silného typu, který obsahuje informace o připojení k úložišti dat nebo výpočetní prostředí. Je také entita, která můžete znovu použít, nebo odkaz.

### <a name="control-flows"></a>Tok řízení
Tok řízení orchestraci kanálu aktivity, které zahrnují řetězení aktivity v sekvenci, vytvoření větve, parametry, které definujete na úrovni kanálu a argumenty, které se předávají jako můžete vyvolat kanálu na vyžádání nebo z aktivační událost. Tok řízení také obsahovat vlastní stav předávání a opakování ve smyčce kontejnery (tj. pro každou iterátory).


Další informace o konceptech služby Data Factory najdete v následujících článcích:

- [Datové sady a propojené služby](concepts-datasets-linked-services.md)
- [Kanály a aktivity](concepts-pipelines-activities.md)
- [Prostředí Integration Runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Co je cenový model pro vytváření dat?
Podrobnosti o cenách Azure Data Factory, najdete v části [podrobnosti o cenách služby Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Jak se může vždy aktuální informace o objektu pro vytváření dat?
Nejaktuálnější informace o Azure Data Factory přejděte na následující servery:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Domovská stránka dokumentace](/azure/data-factory)
- [Domovská stránka produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Podrobné technické informace 

### <a name="how-can-i-schedule-a-pipeline"></a>Plánování kanálu 
Při plánování kanálu můžete použít Plánovač aktivační události nebo aktivační událost okna čas. Aktivační událost používá wall hodiny plánu kalendáře a ve kterém můžete naplánovat kanály pravidelně nebo pomocí kalendářové opakující vzory (například týdně v pondělí na 18: 00 a čtvrtek v 21: 00). Další informace najdete v tématu [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Můžete předat parametry pro spuštění kanálu?
Ano, parametry jsou koncept první třídy, nejvyšší úrovně ve službě ADF. Můžete definovat parametry na úrovni kanálu a předání argumentů při spuštění kanálu spustit na vyžádání nebo pomocí aktivační událost.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Můžete definovat výchozí hodnoty pro parametry kanálu? 
Ano. Můžete definovat výchozí hodnoty pro parametry v kanálů. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Můžete využívat aktivitu v kanálu argumenty, které se předávají do kanálu, spustit? 
Ano. Každá aktivita v rámci kanálu spotřebovat hodnotu parametru, který má být předána do kanálu a spustit s `@parameter` vytvořit. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Mohou být využívány vlastnost výstup aktivity v jiné aktivitě? 
Ano. Výstup aktivity mohou být využívány následné aktivity `@activity` vytvořit.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak můžu pohodlné zpracování hodnot null ve výstup aktivity? 
Můžete použít `@coalesce` vytvořit ve výrazech pro pohodlné zpracování hodnot null. 

## <a name="next-steps"></a>Další postup
Podrobné pokyny týkající se vytvořit objekt pro vytváření dat viz následující kurzy:

- [Rychlý úvod: Vytvořte objekt pro vytváření dat](quickstart-create-data-factory-dot-net.md)
- [Kurz: Kopírování dat v cloudu](tutorial-copy-data-dot-net.md)
