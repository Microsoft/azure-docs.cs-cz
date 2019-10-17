---
title: Mapování toků dat v Azure Data Factory | Microsoft Docs
description: Přehled toků mapování dat v Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: 37fec388acda78f3d13c8e85ddddf780ad099d69
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387995"
---
# <a name="what-are-mapping-data-flows"></a>Co jsou mapování toků dat?

Mapování datových toků je vizuálně navržené transformace dat v Azure Data Factory. Datové toky umožňují technikům pro transformaci dat pracovat s grafickými logikami bez psaní kódu. Výsledné toky dat se spouštějí jako aktivity v kanálech Azure Data Factory s využitím clusterů Spark se škálováním na více systémů. Aktivity toku dat je možné provozovat prostřednictvím stávajících Data Factory plánování, řízení, toků a monitorování.

Mapování toků dat poskytuje plně vizuální prostředí bez nutnosti kódování. Vaše toky dat budou spouštěny ve vlastním clusteru spouštění pro zpracování dat se škálováním na více instancí. Azure Data Factory zpracovává všechny překlady kódu, optimalizaci cest a provádění úloh toku dat.

## <a name="getting-started"></a>Začínáme

Tok dat vytvoříte kliknutím na znaménko plus v části prostředky továrny. 

![Nový tok dat](media/data-flow/newdataflow2.png "Nový tok dat")

Tím přejdete na plátno toku dat, kde můžete vytvořit logiku transformace. Kliknutím na pole přidat zdroj můžete začít konfigurovat transformaci zdroje. Další informace najdete v tématu [transformace zdroje](data-flow-source.md).

## <a name="data-flow-canvas"></a>Plátno toku dat

Plátno toku dat je rozdělené na tři části: horní pruh, graf a panel konfigurace. 

![Kreslicí](media/data-flow/canvas1.png "Kreslicí")

### <a name="graph"></a>Graf

Graf zobrazí datový proud transformace. Ukazuje, že se při toku dat do jedné nebo více umyvadel zobrazuje čára. Chcete-li přidat nový zdroj, klikněte na pole přidat zdroj. Chcete-li přidat novou transformaci, klikněte na znaménko plus v pravém dolním rohu existující transformace.

![Kreslicí](media/data-flow/canvas2.png "Kreslicí")

### <a name="configuration-panel"></a>Panel konfigurace

Panel konfigurace zobrazuje nastavení specifická pro aktuálně vybranou transformaci nebo, pokud není vybrána žádná transformace, tok dat. V celkové konfiguraci toku dat můžete upravit název a popis na kartě **Obecné** nebo přidat parametry přes kartu **parametry** . Další informace najdete v tématu [mapování parametrů toku dat](parameters-data-flow.md).

Každá transformace má alespoň čtyři karty konfigurace:

#### <a name="transformation-settings"></a>Nastavení transformace

První karta v podokně Konfigurace každé transformace obsahuje nastavení specifická pro tuto transformaci. Další informace najdete na stránce s dokumentací k této transformaci.

![Karta nastavení zdroje](media/data-flow/source1.png "Karta nastavení zdroje")

#### <a name="optimize"></a>Optimalizace

Karta _optimalizace_ obsahuje nastavení pro konfiguraci schémat dělení.

![Optimalizovat](media/data-flow/optimize1.png "Optimalizace")

Výchozí nastavení je "použít aktuální dělení na oddíly", který dává pokyn Azure Data Factory k použití schématu dělení do toků dat běžících na Sparku. Ve většině scénářů je toto nastavení doporučený postup.

V některých případech můžete chtít upravit dělení. Pokud například chcete transformovat transformace do jediného souboru v Lake, v transformaci jímky vyberte "jeden oddíl".

Dalším případem, kdy si možná budete chtít řídit schémata dělení, je optimalizace výkonu. Úpravy dělení poskytují kontrolu nad distribucí vašich dat mezi výpočetními uzly a optimalizací dat, které mohou mít jak pozitivní, tak negativní dopad na celkový výkon toku dat. Další informace najdete v tématu [Průvodce výkonem toku dat](concepts-data-flow-performance.md).

Chcete-li změnit dělení na jakékoli transformaci, klikněte na kartu optimalizace a vyberte přepínač "nastavit dělení". Pak se zobrazí řada možností pro dělení. Nejlepší způsob dělení se bude lišit v závislosti na vašich datových svazcích, kandidátních klíčích, hodnotách null a mohutnosti. Osvědčeným postupem je začít s výchozím dělením a pak vyzkoušet různé možnosti dělení. Můžete testovat pomocí ladicích běhů kanálu a zobrazit dobu provádění a využití oddílů v jednotlivých seskupeních transformace v zobrazení monitorování. Další informace najdete v tématu [monitorování toků dat](concepts-data-flow-monitoring.md).

Níže jsou uvedené možnosti dělení k dispozici.

##### <a name="round-robin"></a>Kruhové dotazování. 

Kruhové dotazování je jednoduchý oddíl, který automaticky distribuuje data rovnoměrně mezi oddíly. Použijte kruhové dotazování, pokud nemáte vhodné klíčové kandidáty k implementaci ucelené strategie vytváření oddílů. Můžete nastavit počet fyzických oddílů.

##### <a name="hash"></a>Hodnota hash

Azure Data Factory vytvoří hodnotu hash sloupců pro vytvoření stejnorodých oddílů tak, aby řádky s podobnými hodnotami byly ve stejném oddílu. Při použití možnosti hash otestujte možný zkosený oddíl. Můžete nastavit počet fyzických oddílů.

##### <a name="dynamic-range"></a>Dynamický rozsah

Dynamický rozsah bude používat dynamické rozsahy Sparku na základě sloupců nebo výrazů, které zadáte. Můžete nastavit počet fyzických oddílů. 

##### <a name="fixed-range"></a>Pevný rozsah

Sestavte výraz, který poskytuje pevný rozsah pro hodnoty v rámci sloupců s dělenými daty. Předtím, než použijete tuto možnost, byste měli mít dobré znalosti o datech, abyste se vyhnuli zkosení oddílu. Hodnoty, které zadáte pro výraz, budou použity jako součást funkce oddílu. Můžete nastavit počet fyzických oddílů.

##### <a name="key"></a>Klíč

Pokud máte dobré znalosti o mohutnosti vašich dat, může být vytváření oddílů vhodné pro strategii. Při vytváření oddílů se vytvoří oddíly pro každou jedinečnou hodnotu ve sloupci. Počet oddílů nejde nastavit, protože číslo bude založené na jedinečných hodnotách v datech.

#### <a name="inspect"></a>Prohlížen

Karta _Kontrola_ poskytuje zobrazení metadat datového proudu, který transformuje. Můžete zobrazit počty sloupců, změněné sloupce, přidané sloupce, datové typy, řazení sloupců a odkazy na sloupce. Kontrola je zobrazení vašich metadat jen pro čtení. Není nutné mít povolen režim ladění, aby bylo možné zobrazit metadata v podokně kontrola.

![Prohlížen](media/data-flow/inspect1.png "Prohlížen")

Když změníte tvar dat prostřednictvím transformací, zobrazí se v podokně kontrola změny metadat. Pokud ve zdrojové transformaci není definované schéma, metadata se v podokně kontrola nezobrazí. Nedostatek metadat je běžné ve scénářích pro posun schématu.

#### <a name="data-preview"></a>Náhled dat

Pokud je režim ladění zapnutý, karta _Náhled dat_ vám poskytne interaktivní snímek dat při každé transformaci. Další informace naleznete v části [data Preview v režimu ladění](concepts-data-flow-debug-mode.md#data-preview).

### <a name="top-bar"></a>Horní panel

Horní panel obsahuje akce, které ovlivňují celý tok dat, jako je například ukládání a ověřování. Můžete také přepínat mezi režimy grafu a konfigurace pomocí tlačítek **Zobrazit graf** a **Skrýt graf** .

![Skrýt graf](media/data-flow/hideg.png "Skrýt graf")

Pokud graf skryjete, můžete později procházet uzly pro transformaci prostřednictvím **předchozích** a **dalších** tlačítek.

![Otevře](media/data-flow/showhide.png "Otevře")

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak vytvořit [transformaci zdroje](data-flow-source.md)
* Naučte se vytvářet toky dat v [režimu ladění](concepts-data-flow-debug-mode.md) .
