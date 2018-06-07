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
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: b7f654fb3b24954bb1c9222a4e97dc4b6844411c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619027"
---
# <a name="azure-data-factory-faq"></a>Nejčastější dotazy k Azure Data Factory
Tento článek se týká 2 verzi služby Azure Data Factory. Poskytuje odpovědi na nejčastější dotazy týkající se služby Data Factory.  

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [nejčastější dotazy pro vytváření dat verze 1](v1/data-factory-faq.md).

## <a name="what-is-azure-data-factory"></a>Co je služba Azure Data Factory? 
Objekt pro vytváření dat je plně spravovaná, cloudových dat integrační služba, která automatizuje přesouvání a transformaci dat. Objekt factory, který spouští zařízení k transformaci suroviny na hotové výroby, jako je Azure Data Factory orchestruje stávající služby, které sbírají nezpracovaná data a transformují je na informace připravené k použití. 

Pomocí Azure Data Factory můžete vytvořit datové pracovní postupy pro přesun dat mezi místními a cloudovými datová úložiště. A můžete zpracovat a transformace dat pomocí výpočetních služeb například runtime integrace Azure HDInsight, Azure Data Lake Analytics a integrační služby SSIS (SQL Server). 

Pomocí služby Data Factory můžete provést zpracování dat na základě Azure cloudové služby nebo ve svém vlastním vlastním hostováním výpočetní prostředí, například služby SSIS, SQL Server nebo Oracle. Jakmile vytvoříte kanál, který provede akci, které potřebujete, můžete naplánovat ji spouštět pravidelně (například hodinové, denní nebo týdenní) nebo aktivovat kanálu z výskytu události. Další informace najdete v tématu [Úvod do Azure Data Factory](introduction.md).

## <a name="whats-different-in-version-2"></a>Co je nového ve verzi 2?
Služba Azure Data Factory verze 2 staví na původní službě pro přesouvání a transformaci dat Azure Data Factory a rozšiřuje ji pro pestřejší škálu scénářů integrace dat s upřednostněním cloudu. Azure Data Factory verze 2 nabízí následující možnosti:

- Tok řízení a škálování
- Nasazení a spouštění balíčků služby SSIS v Azure

Po vydání verze 1 jsme rozpoznat, že zákazníci potřebují návrhu komplexní, hybridní scénáře integraci dat., které vyžadují přesun dat a zpracování v cloudu, na místních počítačích a v cloudu virtuálních počítačů (VM). Tyto požadavky přepněte potřeba přenosu a zpracování dat v rámci zabezpečené virtuální sítě v prostředích a škálování s na vyžádání výpočetní výkon.

Jako datových kanálů jsou čím dál víc důležitou součástí strategie obchodní analýzy, jsme zaznamenali, že tyto aktivity vyžadují flexibilní plánování pro podporu načítání Přírůstková data a události aktivované spuštěních. Jak zvyšuje složitost, takže příliš nemá požadavek na službu pro podporu běžných vzorů pracovního postupu, které patří vytvoření větve, opakování ve smyčce a podmíněného zpracování.

Ve verzi 2 můžete do cloudu migrovat také stávající balíčky služby SSIS. Tato akce zruší a posune SSIS jako služby Azure, který je spravován v rámci služby Data Factory, který využívá novou funkci integrace modulu runtime. Prostřednictvím roztočený až modulu runtime integrační služby SSIS v verze 2, můžete provést, správa, monitorování a sestavovat balíčky SSIS v cloudu.

### <a name="control-flows-and-scale"></a>Tok řízení a škálování 
Pro podporu různých integrace toky a vzory v moderní datového skladu, Data Factory aktivoval nové, flexibilní, datového modelu kanálu, který už je vázaný na data časové řady. V této verzi můžete model podmíněné příkazy, větve řízení toku dat kanál a explicitně předat parametry v rámci a přes tyto toky.

Nyní máte volného modelu všechny toku styl, který vyžaduje pro integraci dat a který může odeslat na vyžádání nebo opakovaně podle plánu. Mezi běžné toky, které dřív nebyly podporované, ale teď jsou, patří například tyto:   

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

### <a name="deploy-ssis-packages-to-azure"></a>Nasazení balíčků služby SSIS do Azure 
Pokud chcete přesunout úlohy služby SSIS, můžete vytvořit objekt pro vytváření dat verze 2 a zřídit modulu runtime integrace Azure SSIS. Modul runtime integrace Azure SSIS je plně spravovaný cluster Azure virtuálních počítačů (uzlů), které jsou vyhrazené pro spouštění vlastních balíčků SSIS v cloudu. Podrobné pokyny najdete v tématu [balíčky SSIS nasadit do Azure](tutorial-create-azure-ssis-runtime-portal.md) kurzu. 
 

### <a name="sdks"></a>Sady SDK
Pokud jste pokročilé uživatele a hledá programové rozhraní, verze 2 poskytuje bohatou sadu SDK, které můžete vytvářet, spravovat nebo monitorování kanálů pomocí vašeho oblíbeného rozhraní IDE.

- **.NET SDK:** Sada .NET SDK je aktualizovaná pro verzi 2. 
- **PowerShell:** Rutiny PowerShellu jsou aktualizované pro verzi 2. Rutiny verze 2 mají v názvu *DataFactoryV2*. Například *Get-AzureRmDataFactoryV2*. 
- **Python SDK:** Tato sada SDK je ve verzi 2 nová.
- **REST API:** Rozhraní REST API je aktualizováno pro verzi 2.  

Sady SDK aktualizované pro verzi 2 nejsou zpětně kompatibilní s klienty verze 1. 

### <a name="monitoring"></a>Monitorování
Verze 2 v současné době podporuje monitorování datových továren pouze pomocí sad SDK. Portál ještě monitorování datových továren verze 2 nepodporuje. 

## <a name="what-is-integration-runtime"></a>Co je modul runtime integrace?
Modul runtime integrace je na výpočetní infrastruktuře používaný službou Azure Data Factory k zajištění následující funkce integrace dat v různých prostředích sítě:

- **Přesun dat**: Přesun dat mezi úložišti dat na veřejné sítě a datové úložiště v privátní síti (místně nebo virtuální privátní síť). Poskytuje podporu vestavěných konektorů, převodu formátu, mapování sloupců a výkonného a škálovatelného přenosu dat.
- **Odesílání aktivity**: odesílání a monitorování aktivit transformace, které jsou spuštěny na celou řadu výpočetní služby, jako je Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server a další.
- **Spuštění balíčky SSIS**: nativně provede v prostředí Azure spravovaná výpočetní balíčky SSIS.

Jedna či více instancí integrace runtime můžete nasadit jako potřebné k přesunutí a transformovat data. Modul runtime integrace lze spustit v Azure veřejné síti nebo v privátní síti (místní, Azure Virtual Network nebo virtuální privátní cloud Amazon Web Services [VPC]). 

Další informace najdete v tématu [Prostředí Integration Runtime v Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Co je limit počtu integrační moduly runtime?
Neexistuje žádné pevný limit počtu instancí integrace modulu runtime, může být v datové továrně. Existuje ale omezení počtu jader virtuálního počítače, které modul runtime integrace můžete použít jedno předplatné pro spouštění balíčku služby SSIS. Další informace najdete v tématu [omezuje služby Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>Kdy použít verze 2 místo verze 1? 
Pokud jste nový do Azure Data Factory, spusťte přímo s verze 2. Pokud už používáte verze 1, znovu sestavte vaše datové továrny na verze 2.

> [!WARNING]
> Verze 2 objektu pro vytváření dat je ve verzi preview, a není obecné dostupnosti (GA). Proto není spadají pod stejnou smlouvu o úrovni (SLA) závazky služba Azure jako verze 1 objektu pro vytváření dat, která je v všeobecné 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>Jaké jsou nejvyšší úrovně koncepty verze 2?
Předplatné Azure může obsahovat jednu nebo více instancí služby Azure Data Factory (neboli datových továren). Azure Data Factory obsahuje čtyři klíčové komponenty, které vzájemně spolupracují jako platformu, na které můžete vytvořit datové pracovní postupy a kroky k přesunutí a transformovat data.

### <a name="pipelines"></a>Kanály
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál je logické seskupení aktivit k provedení pracovní jednotka. Aktivity v kanálu společně provádí úlohy. Kanál může například obsahovat skupinu aktivit, které ingestují data z objektu blob Azure a pak spusťte dotaz Hive v clusteru HDInsight k oddílu dat. Výhodou je, že kanál můžete spravovat aktivity jako sada místo nutnosti lze spravovat jednotlivě každou aktivitu. Můžete společně zřetězit aktivity v kanálu je postupně pracovat, nebo můžete pracovat nezávisle paralelně.

### <a name="activity"></a>Aktivita
Aktivity představují krok zpracování v rámci kanálu. Například můžete použít *kopie* aktivity ke zkopírování dat z jednoho datového úložiště do jiného úložiště dat. Podobně můžete použít aktivitu Hive, která spustí dotaz Hive v clusteru Azure HDInsight aby transformoval a analyzoval vaše data. Data Factory podporuje tři typy aktivit: aktivity přesunu dat, aktivity transformace dat a aktivity řízení.

### <a name="data-sets"></a>Datové sady
Datové sady představují datové struktury v rámci úložišť dat, které jednoduše přejděte na příkaz, nebo odkaz, který chcete použít své aktivity jako vstupní nebo výstupní data. 

### <a name="linked-services"></a>Propojené služby
Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Považujte ho takto: propojené služby definuje připojení ke zdroji dat, a představuje strukturu dat datové sady. Například služby Azure Storage, propojené Určuje připojovací řetězec pro připojení k účtu úložiště Azure. A sadu dat objektů Blob v Azure Určuje kontejner objektů blob a složky, která obsahuje data.

Propojené služby mají dva účely v objektu pro vytváření dat:

- Představují *úložiště dat* , zahrnuje, avšak není omezeno na místní instance systému SQL Server, instanci databáze Oracle, sdílené složky nebo účet služby Azure Blob storage. Seznam podporovaných datová úložiště najdete v tématu [aktivitu kopírování v Azure Data Factory](copy-activity-overview.md).
- Představují *výpočetní prostředek*, který může hostovat provádění aktivity. Například spuštění aktivity HDInsight Hive v clusteru HDInsight Hadoop. Seznam aktivit transformace i podporované výpočetní prostředí najdete v tématu [transformace dat v Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Triggery
Aktivační události představují jednotky zpracování, které určují, kdy je spuštěna provádění zřetězeného příkazu. Pro různé typy událostí existují různé typy aktivačních událostí. Pro verzi preview podporujeme aktivační událost scheduler wall hodiny. 


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

## <a name="what-regions-support-azure-data-factory-version-2"></a>Jaké oblasti podpory Azure Data Factory verze 2?
V současné době můžete vytvořit datové továrny verze 2 v oblasti Východ USA, Východ USA 2 a západní Evropa. Však můžete objekt pro vytváření dat využití integrace modulu runtime v jiné oblasti pro přesun dat mezi úložišti dat, odesílání aktivit výpočetní služby nebo balíčky SSIS odesílání. Další informace najdete v tématu [umístění služby Data Factory](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Jak se může zůstat aktuální informace o objektu pro vytváření dat?
Nejaktuálnější informace o Azure Data Factory přejděte na následující servery:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Domovská stránka dokumentace](/azure/data-factory)
- [Domovská stránka produktu](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Podrobné technické informace 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>Můžete verze 1 a verze 2 kanálů spustit souběžně?
Ne. Verze 2 a verze 1 datové továrny nemůže obsahovat entity (například propojené služby, datové sady nebo kanály) jinou verzi.   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>Je potřeba definování datových sad v verze 2?
Datové sady je už povinné entity pro většinu aktivit. Je vyžadována pro kopírování, machine learning, vyhledávání, ověřování a vlastních aktivit, které používají schématu a další metadata v sadě dat pro transformaci. Zbytek aktivity už nebudou potřebovat datových sad.

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>Můžete řetězu dvě aktivity bez datové sady v verze 2?
Ano. Aktivity v verze 2 můžete zřetězené bez nutnosti datových sad. Zřetězené aktivity pomocí **dependsOn** vlastnost v definici JSON vaší kanálu. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>Jsou všechny verze 1 aktivity, které jsou podporovány v verze 2? 
Ano, jsou podporovány všechny verze 1 aktivity v verze 2.

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>Plánování kanálu verze 2 
Při plánování kanálu verze 2 můžete použít Plánovač aktivační událost. Aktivační událost používá wall hodiny plánu kalendáře a ve kterém můžete naplánovat kanály pravidelně nebo pomocí kalendářové opakující vzory (například týdně v pondělí na 18: 00 a čtvrtek v 21: 00). Další informace najdete v tématu [Spouštění kanálů a aktivační události](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>Můžete předat parametry do kanálu, spusťte v verze 2?
Ano, parametry jsou koncept první třídy, nejvyšší úrovně v verze 2. Můžete definovat parametry na úrovni kanálu a předání argumentů při spuštění kanálu spustit na vyžádání nebo pomocí aktivační událost.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Můžete definovat výchozí hodnoty pro parametry kanálu? 
Ano. Můžete definovat výchozí hodnoty pro parametry v kanálů. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Můžete využívat aktivitu v kanálu argumenty, které se předávají do kanálu, spustit? 
Ano. Každá aktivita v rámci kanálu spotřebovat hodnotu parametru, který má být předána do kanálu a spustit s `@parameter` vytvořit. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Mohou být využívány vlastnost výstup aktivity v jiné aktivitě? 
Ano. Výstup aktivity mohou být využívány následné aktivity `@activity` vytvořit.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Jak můžu pohodlné zpracování hodnot null ve výstup aktivity? 
Můžete použít `@coalesce` vytvořit ve výrazech pro pohodlné zpracování hodnot null. 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>Můžete použít opakování a časový limit na úrovni aktivity v verze 2?
Ano. K řízení provádění aktivit v verze 2 jako v verze 1, můžete nakonfigurovat opakování a časový limit na úrovni aktivity. 

## <a name="next-steps"></a>Další postup
Podrobné pokyny pro vytváření dat verze 2 najdete v následujících kurzech:

- [Rychlý úvod: Vytvořte objekt pro vytváření dat](quickstart-create-data-factory-dot-net.md)
- [Kurz: Kopírování dat v cloudu](tutorial-copy-data-dot-net.md)

