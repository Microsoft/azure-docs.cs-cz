---
title: Toky dat mapování
description: Přehled toků mapování dat v Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/19/2019
ms.openlocfilehash: 210c1814325e689dd70af9caa7fad08deed933e1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243793"
---
# <a name="what-are-mapping-data-flows"></a>Co jsou toky dat mapování?

Mapování datových toků je vizuálně navržené transformace dat v Azure Data Factory. Datové toky umožňují technikům pro transformaci dat pracovat s grafickými logikami bez psaní kódu. Výsledné toky dat se spouštějí jako aktivity v rámci Azure Data Factory kanálů, které používají clustery Spark se škálováním na více systémů. Aktivity toku dat je možné provozovat prostřednictvím stávajících Data Factory plánování, řízení, toku a monitorování.

Mapování toků dat poskytuje plně vizuální prostředí bez nutnosti kódování. Vaše toky dat se spustí ve vlastním clusteru spuštění pro zpracování dat se škálováním na více instancí. Azure Data Factory zpracovává všechny překlady kódu, optimalizaci cest a provádění úloh toku dat.

## <a name="getting-started"></a>Začínáme

Tok dat vytvoříte tak, že vyberete znaménko plus v části **prostředky výroby**a pak vyberete **tok dat**. 

![Nový tok dat](media/data-flow/newdataflow2.png "Nový tok dat")

Tím přejdete na plátno toku dat, kde můžete vytvořit logiku transformace. Pokud chcete začít konfigurovat transformaci zdrojového kódu, vyberte **Přidat zdroj** . Další informace najdete v tématu [transformace zdroje](data-flow-source.md).

## <a name="data-flow-canvas"></a>Plátno toku dat

Plátno toku dat je rozdělené na tři části: horní pruh, graf a panel konfigurace. 

![Kreslicí](media/data-flow/canvas1.png "Kreslicí")

### <a name="graph"></a>Graf

Graf zobrazí datový proud transformace. Ukazuje, že se při toku dat do jedné nebo více umyvadel zobrazuje čára. Chcete-li přidat nový zdroj, vyberte možnost **Přidat zdroj**. Chcete-li přidat novou transformaci, vyberte znaménko plus na pravé straně existující transformace.

![Kreslicí](media/data-flow/canvas2.png "Kreslicí")

### <a name="azure-integration-runtime-data-flow-properties"></a>Vlastnosti toku dat prostředí Azure Integration runtime

![Tlačítko ladit](media/data-flow/debugbutton.png "Tlačítko ladit")

Po zahájení práce s toky dat v ADF budete chtít zapnout přepínač ladit pro toky dat v horní části uživatelského rozhraní prohlížeče. Tím se uvolní cluster Azure Databricks, který se má použít pro interaktivní ladění, náhledy dat a spuštění ladění kanálu. Velikost používaného clusteru můžete nastavit tak, že vyberete vlastní [Azure Integration runtime](concepts-integration-runtime.md). Relace ladění zůstane aktivní po dobu až 60 minut po poslední ukázce náhledu dat nebo posledního spuštění kanálu ladění.

Při zprovozněníí kanálů s aktivitami toku dat použije ADF Azure Integration Runtime přidružený k [aktivitě](control-flow-execute-data-flow-activity.md) ve vlastnosti spustit na.

Výchozí Azure Integration Runtime je malý cluster jednoho pracovního procesu s jedním jádrem, který je určený k tomu, aby bylo možné zobrazovat náhled dat a rychle spouštět kanály ladění s minimálními náklady. Pokud provádíte operace s velkými datovými sadami, nastavte větší konfiguraci Azure IR.

Můžete nastavit, aby služba ADF udržovala fond prostředků clusteru (VM) nastavením hodnoty TTL ve vlastnostech toku dat Azure IR. Výsledkem bude rychlejší provádění úloh v následných aktivitách.

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Azure Integration runtime a strategie toku dat

##### <a name="execute-data-flows-in-parallel"></a>Paralelní spouštění toků dat

Pokud pracujete s paralelními toky dat v kanálu, vytvoří se ADF samostatné Azure Databricks clustery pro každé spuštění aktivity na základě nastavení Azure Integration Runtime připojených ke každé aktivitě. Chcete-li navrhovat paralelní spouštění v kanálech ADF, přidejte aktivity toku dat bez omezení priority v uživatelském rozhraní.

Z těchto tří možností se tato možnost bude v nejkratší době provádět. Každý paralelní tok dat se ale spustí ve stejnou dobu u samostatných clusterů, takže řazení událostí je nedeterministické.

Pokud spouštíte aktivity toku dat paralelně uvnitř vašich kanálů, doporučujeme nepoužívat hodnotu TTL. Je to proto, že paralelní spouštění datových toků současně pomocí stejné Azure Integration Runtime bude mít za následek více instancí teplého fondu pro vaši datovou továrnu.

##### <a name="overload-single-data-flow"></a>Jeden tok dat přetížení

Pokud vložíte veškerou logiku do jediného toku dat, spustí se ADF v rámci stejného kontextu spuštění úlohy v jedné instanci clusteru Spark.

Tato možnost může být obtížnější sledovat a řešit potíže, protože vaše obchodní pravidla a obchodní logika budou Jumble společně. Tato možnost také neposkytuje mnohem novou použitelnost.

##### <a name="execute-data-flows-serially"></a>Sériové provádění toků dat

Pokud provedete aktivity toku dat v rámci kanálu sériového portu a nastavíte hodnotu TTL pro konfiguraci Azure IR, pak ADF znovu použije výpočetní prostředky (virtuální počítače), což vede k rychlejšímu následnému spuštění. Pro každé spuštění se stále dostanete nový kontext Sparku.

Z těchto tří možností to může trvat delší dobu, než se provede celý až do konce. Ale poskytuje čisté oddělení logických operací v každém kroku toku dat.

### <a name="configuration-panel"></a>Panel konfigurace

Panel konfigurace zobrazuje nastavení specifická pro aktuálně vybranou transformaci. Pokud není vybraná žádná transformace, zobrazuje tok dat. V celkové konfiguraci toku dat můžete upravit název a popis na kartě **Obecné** nebo přidat parametry přes kartu **parametry** . Další informace najdete v tématu [mapování parametrů toku dat](parameters-data-flow.md).

Každá transformace má alespoň čtyři karty konfigurace.

#### <a name="transformation-settings"></a>Nastavení transformace

První karta v podokně Konfigurace každé transformace obsahuje nastavení specifická pro tuto transformaci. Další informace najdete na stránce s dokumentací k této transformaci.

![Karta nastavení zdroje](media/data-flow/source1.png "Karta nastavení zdroje")

#### <a name="optimize"></a>Optimalizace

Karta **optimalizace** obsahuje nastavení pro konfiguraci schémat dělení.

![Optimalizovat](media/data-flow/optimize1.png "Optimalizace")

Ve výchozím nastavení se **používá aktuální dělení**, které dává pokyn Azure Data Factory, aby používalo schéma dělení, které je nativní pro toky dat běžící na Sparku. Ve většině scénářů doporučujeme toto nastavení.

V některých případech můžete chtít upravit dělení. Pokud například chcete transformovat transformace do jednoho souboru v Lake, vyberte v transformaci jímky **jeden oddíl** .

Dalším případem, kdy byste mohli chtít řídit schémata dělení, je optimalizace výkonu. Úpravy dělení poskytují kontrolu nad distribucí vašich dat mezi výpočetními uzly a optimalizací dat, které mohou mít jak pozitivní, tak negativní dopad na celkový výkon toku dat. Další informace najdete v tématu [Průvodce výkonem toku dat](concepts-data-flow-performance.md).

Chcete-li změnit dělení na jakékoli transformaci, vyberte kartu **optimalizace** a vyberte přepínač **nastavit dělení** . Pak se zobrazí řada možností pro dělení. Nejlepší způsob dělení se bude lišit v závislosti na vašich datových svazcích, kandidátních klíčích, hodnotách null a mohutnosti. 

Osvědčeným postupem je začít s výchozím dělením a pak vyzkoušet různé možnosti dělení. Můžete testovat pomocí ladicích běhů kanálu a zobrazit dobu provádění a využití oddílů v každém seskupení transformace v zobrazení monitorování. Další informace najdete v tématu [monitorování toků dat](concepts-data-flow-monitoring.md).

K dispozici jsou následující možnosti dělení.

##### <a name="round-robin"></a>Kruhové dotazování 

Kruhové dotazování je jednoduchý oddíl, který automaticky distribuuje data rovnoměrně mezi oddíly. Použijte kruhové dotazování, pokud nemáte vhodné klíčové kandidáty k implementaci ucelené strategie vytváření oddílů. Můžete nastavit počet fyzických oddílů.

##### <a name="hash"></a>Hash

Azure Data Factory vytvoří hodnotu hash sloupců pro vytvoření stejnorodých oddílů tak, aby řádky s podobnými hodnotami byly ve stejném oddílu. Když použijete možnost hash, otestujete možnou hodnotu zešikmení oddílu. Můžete nastavit počet fyzických oddílů.

##### <a name="dynamic-range"></a>Dynamický rozsah

Dynamický rozsah bude používat dynamické rozsahy Sparku na základě sloupců nebo výrazů, které zadáte. Můžete nastavit počet fyzických oddílů. 

##### <a name="fixed-range"></a>Pevný rozsah

Sestavte výraz, který poskytuje pevný rozsah pro hodnoty v rámci sloupců s dělenými daty. Abyste se vyhnuli zkosení oddílu, měli byste před použitím této možnosti dobře pochopit svá data. Hodnoty, které zadáte pro výraz, budou použity jako součást funkce oddílu. Můžete nastavit počet fyzických oddílů.

##### <a name="key"></a>Klíč

Pokud máte dobré znalosti o mohutnosti vašich dat, může být vytváření oddílů dobrým zvykem. Při vytváření oddílů se vytvoří oddíly pro každou jedinečnou hodnotu ve sloupci. Počet oddílů nejde nastavit, protože číslo bude založené na jedinečných hodnotách v datech.

#### <a name="inspect"></a>Prohlížen

Karta **Kontrola** poskytuje zobrazení metadat datového proudu, který transformuje. Můžete zobrazit počty sloupců, změněné sloupce, přidané sloupce, datové typy, řazení sloupců a odkazy na sloupce. **Kontrola** je zobrazení vašich metadat jen pro čtení. Není nutné mít povolen režim ladění, aby bylo možné zobrazit metadata v podokně **Kontrola** .

![Prohlížen](media/data-flow/inspect1.png "Prohlížen")

Když změníte tvar dat prostřednictvím transformací, v podokně **Kontrola** se zobrazí tok změn metadat. Pokud ve zdrojové transformaci není definované schéma, metadata se v podokně **Kontrola** nezobrazí. Nedostatek metadat je běžné ve scénářích pro posun schématu.

#### <a name="data-preview"></a>Náhled dat

Pokud je režim ladění zapnutý, karta **Náhled dat** vám poskytne interaktivní snímek dat při každé transformaci. Další informace naleznete v části [data Preview v režimu ladění](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Horní panel

Horní panel obsahuje akce, které ovlivňují celý tok dat, jako je ukládání a ověřování. Můžete také přepínat mezi režimy grafu a konfigurace pomocí tlačítek **Zobrazit graf** a **Skrýt graf** .

![Skrýt graf](media/data-flow/hideg.png "Skrýt graf")

Pokud graf skryjete, můžete později procházet uzly pro transformaci prostřednictvím **předchozích** a **dalších** tlačítek.

![Tlačítka předchozí a další](media/data-flow/showhide.png "tlačítka předchozí a další")

## <a name="next-steps"></a>Další kroky

* Naučte se vytvořit [zdrojovou transformaci](data-flow-source.md).
* Naučte se vytvářet toky dat v [režimu ladění](concepts-data-flow-debug-mode.md).
