---
title: Podrobný průvodce o tom, jak používat Azure Machine Learning Data přípravy | Microsoft Docs
description: Tento dokument obsahuje přehled a podrobnosti o tom, jak vyřešit problémy dat pomocí Azure Machine Learning Data přípravy
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 9fc0ae8929f12447123321a5c64ac89309c68dca
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="data-preparations-user-guide"></a>Uživatelská příručka k přípravě dat 
Azure Machine Learning Data přípravy prostředí nabízí spoustu bohaté funkce. Tento článek obsahuje informace o části nejhlubší prostředí.

### <a name="step-execution-history-and-caching"></a>Krok provádění, historie a ukládání do mezipaměti 
Data přípravy krok historie udržuje řadu mezipaměti z důvodů výkonu. Pokud vyberete krok a jeho přístupů do mezipaměti, nebude znovu spustit. Pokud máte blok zápisu na konci tohoto kroku historie a překlopit a zpět na kroky, ale žádné změny neprovádět, spustí není po prvním zápisu. Probíhá nový zápis a přepíše předchozímu, pokud je:

- Proveďte změny zápisu bloku.
- Přidat nový blok transformaci a přesuňte nad blok zápisu, který generuje zneplatnění mezipaměti.
- Změna vlastností blok výše blok zápisu, který generuje zneplatnění mezipaměti.
- Vyberte možnost obnovit na ukázku (tedy zneplatnění všechny mezipaměti).

### <a name="error-values"></a>Chybové hodnoty

Transformace dat může selhat pro vstupní hodnotu, protože tuto hodnotu nelze správně zpracovat. V případě operace převod typu, například převod selže, pokud vstupní řetězcovou hodnotu nelze převést na zadaný cílový typ. Typ převod operace může být převodu sloupec typu řetězec na číslo nebo logická hodnota typu nebo při pokusu o duplicitní sloupec, který neexistuje. (Tato chyba se vyskytuje v důsledku přesunu *odstranit sloupce X* fungování předtím, než *duplicitní sloupce X* operace.)

V těchto případech přípravy dat vytváří chybovou hodnotu jako výstup. Chyba hodnoty udávají, že předchozí operace se nezdařilo z dané hodnoty. Interně jsou považovány za typ hodnoty první třídy, ale jejich přítomnosti nemění základní typ sloupce, i pokud sloupec obsahuje zcela chybové hodnoty.

Chybové hodnoty se dají snadno identifikovat. Svém zvýrazněné červeně a čtení "Chyba". Pokud chcete zjistit příčinu chyby, pozastavte ukazatel myši nad chybovou hodnotu zobrazíte textový popis chyby.

Rozšíří chybové hodnoty. Po chybě se zobrazí, se rozšíří ve většině případů za chybu prostřednictvím většinu operací. Existují tři způsoby, jak nahradit nebo je odebrat:

* Nahradit
    -  Klikněte pravým tlačítkem na sloupec a vyberte **nahradí chybové hodnoty**. Potom můžete nahrazující hodnotou pro každou hodnotu Chyba nalezen ve sloupci.

* Odebrat
    - Přípravné kroky dat zahrnuje interaktivních filtrů zachovat nebo odebrat chybové hodnoty.
    - Klikněte pravým tlačítkem na sloupec a vyberte **sloupce filtru**. Pokud chcete zachovat nebo odebrat chybové hodnoty, vytvoření podmíněného s podmínkou *"je chyba"* nebo *"není chyba."*

* Použijte výraz Python podmíněně pracovat na chybové hodnoty. Další informace najdete v tématu [část Python rozšíření](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Vzorkování
Soubor zdroje dat přebírá nezpracovaná data z jednoho nebo více zdrojů, buď z místního systému souborů nebo ze vzdáleného umístění. Ukázka bloku umožňuje určit, zda chcete pracovat s podmnožinu dat pomocí generování ukázky. Pracující na ukázková data, nikoli velké datové sady často vede k lepší výkon, když provádíte operace v dalších krocích.

Pro každý soubor zdroje dat může generovat několik ukázky a uložené. Pouze jeden vzorek však lze nastavit jako aktivní ukázka. Můžete vytvořit, upravit nebo odstranit ukázky v Průvodci zdroje dat nebo úpravou ukázka bloku. Všechny soubory přípravy dat, které ze své podstaty odkaz na zdroj dat použijte ukázku, zadaný v souboru zdroje dat.

Existuje několik strategií vzorkování k dispozici, každý s odlišnými parametry konfigurovatelné.

#### <a name="top"></a>Horní
Tuto strategii je použít pro místní nebo vzdálené soubory. Do zdroje dat trvá první N řádků (určena podle počtu).

#### <a name="random-n"></a>Náhodné N 
Tuto strategii můžete použít pouze pro místní soubory. Do zdroje dat trvá náhodných N řádků (určena podle počtu). Můžete zadat konkrétní počáteční hodnoty zajistit, že se generuje vzorek stejné, za předpokladu, že počet je také stejné.

#### <a name="random-"></a>Náhodné % 
Tuto strategii je použít pro místní nebo vzdálené soubory. V obou případech pravděpodobnost a počáteční hodnoty musí být zadaná, podobně jako náhodné N strategie.

Ukázky vzdálených souborů musí být zadané dalších parametrů:

- Ukázka generátor 
  - Vyberte Spark cluster nebo vzdálené Docker výpočetní cíl, který se má použít pro generování ukázka. Výpočetní cíl musí být vytvořeny pro projekt předem pro zobrazí v tomto seznamu. Postupujte podle kroků v části "vytvořit nový cíl výpočetní" v [použití GPU v Azure Machine Learning](how-to-use-gpu.md) vytvořit výpočetní cíle.
- Ukázka úložiště 
  - Zadejte umístění zprostředkující úložiště k ukládání vzdáleného vzorku. Tato cesta musí být na jiný adresář z umístění vstupního souboru.

#### <a name="full-file"></a>Celého souboru 
Tuto strategii můžete použít pouze k místním souborům, trvá celého souboru do zdroje dat. Pokud soubor je příliš velký, tato možnost může zpomalit budoucí operace v aplikaci. Může být vhodnější použít jiné vzorkování strategie.


### <a name="fork-merge-and-append"></a>Rozvětvit, sloučení a připojení

Když použijete filtr přes datovou sadu, operaci rozdělí data na dvou sad výsledků dotazu: představuje jednu sadu záznamů, které úspěšné ve filtru a jiná sada je pro záznamy, které nesplní. V obou případech může uživatel vybrat, které výsledek nastavit pro zobrazení. Uživatel může zahodit jiné datové sady nebo umístit do nové datový tok. Druhou možnost se označuje jako větvení.

Chcete-li rozvětvit: 
1. Vyberte sloupce, klikněte pravým tlačítkem a vyberte **filtru** sloupce.

2. V části **I má**, vyberte **zachovat řádky** zobrazíte sadu výsledků dotazu, který předává filtru.

3. Vyberte **odebrat řádky** zobrazíte sady se nezdařila.

4. Po **podmínky**, vyberte **vytvořit toku dat obsahující řádky filtrované Out** Chcete-li rozvětvit sady do nové toku dat bez zobrazení výsledků.


Tento postup se často používá k oddělení se sada dat, která vyžaduje další přípravy. Poté, co jste připravili forked datovou sadu, je běžné slučování dat s do sady výsledků v původní datový tok. Pokud chcete provést sloučení (zpětného operace rozvětvení), použijte jednu z následujících akcí:

- **Připojit řádky**. Sloučit dva nebo více toků dat svisle (row-wise). 
- **Připojit sloupce**. Sloučit dva nebo více toků dat vodorovně (column-wise).


>[!NOTE]
>Připojte sloupce selže, pokud dojde ke kolizi sloupce.


Po operaci sloučení jeden nebo více toků dat odkazují zdrojový datový tok. Data přípravy upozorňuje oznámení v pravém dolním rohu aplikace, pod seznam kroků.


Všechny operace na odkazovaný datový tok vyžaduje tok dat nadřazené aktualizace ukázkové použití z odkazovaný datový tok. V tomto případě nahrazuje dialogové okno potvrzení oznámení odkaz toku dat v pravém dolním rohu. Toto okno zobrazené potvrdí, budete muset aktualizovat tok dat pro synchronizaci se službou změny všechny toky dat závislostí.

### <a name="list-of-appendices"></a>Seznam přílohy 
* [Podporované zdroje dat](data-prep-appendix2-supported-data-sources.md)  
* [Podporované transformací](data-prep-appendix3-supported-transforms.md)  
* [Podporované kontroly](data-prep-appendix4-supported-inspectors.md)  
* [Podporované cíle](data-prep-appendix5-supported-destinations.md)  
* [Ukázky filtr výrazů v Pythonu](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Ukázka transformace datového toku výrazy v Pythonu](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Ukázka zdroje dat v Pythonu](data-prep-appendix8-sample-source-connections-python.md)  
* [Ukázka připojení cílů v Pythonu](data-prep-appendix9-sample-destination-connections-python.md)  
* [Ukázka sloupec transformuje v Pythonu](data-prep-appendix10-sample-custom-column-transforms-python.md)  
