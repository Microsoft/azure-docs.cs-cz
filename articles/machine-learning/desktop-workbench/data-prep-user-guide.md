---
title: Podrobná příručka, jak používat Azure Machine Learning Data přípravy | Dokumentace Microsoftu
description: Tento dokument obsahuje přehled a podrobnosti o tom, jak řešit problémy dat pomocí Azure Machine Learning Data přípravy
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 7536e67d0ae4973008c8acc91a99a7d0d286f9b8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988729"
---
# <a name="data-preparations-user-guide"></a>Uživatelská příručka k přípravy dat. 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Azure Machine Learning Data přípravy prostředí nabízí spoustu celou řadu funkcí. Tento článek dokumenty nejhlubší části prostředí.

### <a name="step-execution-history-and-caching"></a>Krok spuštění, historie a ukládání do mezipaměti 
Historie krok přípravy dat udržuje řada mezipamětí z důvodů výkonu. Pokud vyberete některý krok a volání do mezipaměti, nelze znovu provést. Pokud máte bloku zápisu na konci historie kroku a překlopit vpřed a zpět na kroky, ale žádné změny neprovádět, zápis neaktivuje po prvním spuštění. Vyvolá nový zápis a přepíše předchozí, pokud jste:

- Změny bloku zápisu.
- Přidat nový blok transformace a přesunout nad zápisu blok, který generuje zneplatnění mezipaměti.
- Změňte vlastnosti blok nad zápisu blok, který generuje zneplatnění mezipaměti.
- Zvolit položku obnovit v ukázce (tedy uzamkněte všechny mezipaměti).

### <a name="error-values"></a>Chybových hodnot

Transformace dat může selhat pro vstupní hodnotu, protože tuto hodnotu nemůže být zpracováno odpovídajícím způsobem. V případě operace konverze typu, například vynucení selže, pokud vstupní řetězcovou hodnotu nelze přetypovat na typ zadaný cíl. Operaci konverze typu může být sloupec typu řetězec převod na typ s číselnou nebo logickou nebo pokusu duplikovat sloupec, který neexistuje. (Tato chyba se vyskytuje v důsledku přechodu *odstranit sloupec X* fungování předtím, než *duplicitní sloupec X* operace.)

Příprava dat v těchto případech se vytvoří chybovou hodnotu jako výstup. Chyba hodnoty označují, že předchozí operace se nezdařila pro dané hodnoty hodnotu. Interně má zato, že jste jako typ hodnoty první třídy, ale jejich přítomnost nemění základní typ pro sloupec, i v případě, že sloupec je tvořen výhradně chybovými hodnotami.

Chyba hodnoty jsou snadno identifikovat. Jsou zvýrazněné červeně a čtení "Chyba". Chcete-li zjistit příčinu chyby, podržte ukazatel myši nad chybovou hodnotu zobrazíte textový popis chyby.

Šíření chyb hodnoty. Po chybě hodnota se vyskytuje, ji postoupí ve většině případů jako chyba prostřednictvím většinu operací. Existují tři způsoby, jak nahradit nebo je odebrat:

* Nahradit
    -  Klikněte pravým tlačítkem na sloupec a vyberte **nahradí chybovými hodnotami**. Potom můžete nahrazující hodnotou pro každou hodnotu Chyba nalezen ve sloupci.

* Odebrat
    - Příprava dat zahrnuje interaktivních filtrů, které chcete zachovat nebo odebrat chybových hodnot.
    - Klikněte pravým tlačítkem na sloupec a vyberte **sloupec filtru**. Pokud chcete zachovat nebo odebrat chybové hodnoty, vytvořte s podmínkou s podmínkou *"je chyba"* nebo *"není chyba."*

* Pomocí výrazu Python podmíněně pracovat s chybovými hodnotami. Další informace najdete v tématu [část věnovanou rozšířením Python](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Vzorkování
Soubor zdroje dat přijímá nezpracovaných dat z jednoho nebo více zdrojů, buď z místního systému souborů nebo ze vzdáleného umístění. Ukázka bloku umožňuje určit, zda chcete pracovat s použitím podmnožiny dat vygenerováním ukázky. Práce na ukázková data a ne velké datové sady často vede k lepší výkon při provádění operace v dalších krocích.

Pro každý soubor zdroje dat může generovat více ukázek a uložené. Pouze jeden vzorek však lze nastavit jako aktivní vzorek. Můžete vytvořit, upravit nebo odstranit ukázky v Průvodci zdroje dat nebo úpravou bloku vzorku. Příprava dat soubory, které odkazují na zdroje dat ze své podstaty použít ukázkový zadané v souboru zdroje dat.

Existuje několik strategií vzorkování je k dispozici, každý s různými konfigurovatelné parametry.

#### <a name="top"></a>Top
Tato strategie můžete použít pro místní nebo vzdálené soubory. Do zdroje dat trvá prvních N řádků (určeného počtu).

#### <a name="random-n"></a>Náhodné N 
Tato strategie může použít pouze pro místní soubory. Do zdroje dat trvá náhodné N řádků (určeného počtu). Můžete zadat konkrétní počáteční hodnoty k zajištění, že se vygeneruje stejného vzorku, za předpokladu, že počet je také stejný.

#### <a name="random-"></a>Náhodné % 
Tato strategie můžete použít pro místní nebo vzdálené soubory. V obou případech se pravděpodobnost a základní hodnota musí být zadaná, podobně jako N náhodné strategie.

Ukázky vzdálených souborů musí se poskytnout další parametry:

- Generátor vzorku 
  - Vyberte Spark cluster nebo cílové má být použit pro generování ukázka výpočetní prostředí vzdálené Dockeru. Cílové výpočetní prostředí musí být vytvořeny pro projekt předem pro něj se zobrazí v tomto seznamu. Postupujte podle pokynů v části "Vytvoření nové cílové výpočetní prostředí" v [použití GPU ve službě Azure Machine Learning](how-to-use-gpu.md) vytvoření cílových výpočetních prostředí.
- Ukázky úložiště 
  - Zadejte umístění služby sloužící jako přechodné úložiště k ukládání vzdálené vzorku. Tato cesta musí být ze vstupního souboru umístění do jiného adresáře.

#### <a name="full-file"></a>Úplný soubor 
Tato strategie lze použít pouze k místním souborům, zdroj dat s ohledem úplný soubor. Pokud soubor je příliš velký, tato možnost může zpomalit budoucí operace v aplikaci. Možná pro vás bude vhodnější použít vyvolávajících strategie.


### <a name="fork-merge-and-append"></a>Rozvětvení, sloučení a připojení

Když filtr použijete v datové sadě, operaci rozdělí data do dvou sad výsledků: představuje jednu sadu záznamů, které úspěšná ve filtru, a jinou sadu pro záznamy, které nesplní. V obou případech se uživatel může zvolit sadu které výsledků k zobrazení. Uživatele můžete zrušit další datové sady nebo umístěte ho do nového toku dat. Druhá možnost se označuje jako větvení.

Chcete-li rozvětvit: 
1. Vyberte sloupce, klikněte pravým tlačítkem a vyberte **filtr** sloupce.

2. V části **I Want To**vyberte **zachovat řádky** zobrazíte sadu výsledků, které předává filtru.

3. Vyberte **odebrat řádky** zobrazíte sady se nezdařilo.

4. Po **podmínky**vyberte **vytvořit tok dat obsahující řádky filtrované si** rozvětvit bez zobrazení sady výsledků do nového toku dat.


Tento postup se často používá k tomu oddělit sady dat, který vyžaduje další přípravy. Poté, co jste si připravili rozvětveného datovou sadu, je běžné sloučit s původní tok dat. sadu výsledků. Chcete-li provést sloučení (reverzní operaci fork), použijte jednu z následujících akcí:

- **Připojit řádky**. Sloučit dva nebo víc datových toků svisle (row-wise). 
- **Přidat sloupce**. Sloučit dva nebo víc datových toků vodorovně (column-wise).


>[!NOTE]
>Připojte sloupce selže, pokud dojde ke kolizi sloupce.


Po operaci sloučení odkazuje jeden nebo více toků dat. zdrojový datový tok. Příprava dat upozornění oznámení v pravém dolním rohu aplikace pod seznamem kroků.


Všechny operace na odkazovaný datový tok vyžaduje tok dat nadřazené aktualizovat ukázkový než odkazovaný datový tok. V tomto případě dialogové okno potvrzení nahradí oznámení odkaz toku dat v pravém dolním rohu. Toto dialogové potvrdí, že budete muset aktualizovat tok dat pro synchronizaci se změnami všechny toky dat závislostí.

### <a name="list-of-appendices"></a>Seznam příloh 
* [Podporované zdroje dat](data-prep-appendix2-supported-data-sources.md)  
* [Podporované transformace](data-prep-appendix3-supported-transforms.md)  
* [Podporované kontroly](data-prep-appendix4-supported-inspectors.md)  
* [Podporované cíle](data-prep-appendix5-supported-destinations.md)  
* [Ukázka výrazů filtru v Pythonu](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Ukázky transformace datového toku výrazů v Pythonu](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Ukázkové zdroje dat v Pythonu](data-prep-appendix8-sample-source-connections-python.md)  
* [Ukázka připojení cíle v Pythonu](data-prep-appendix9-sample-destination-connections-python.md)  
* [Ukázka sloupec transformace v jazyce Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
