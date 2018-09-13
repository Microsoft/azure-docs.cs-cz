---
title: Podporované kontroly, které jsou k dispozici s přípravou dat aplikace Azure Machine Learning | Dokumentace Microsoftu
description: Tento dokument obsahuje úplný seznam kontroly, které jsou k dispozici pro přípravu dat Azure Machine Learning
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
ms.openlocfilehash: ef5f6f3dc7ae0c555b2afe000b54c443313800f1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643396"
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Podporované kontroly pro náhled přípravu dat Azure Machine Learning
Tento dokument popisuje nastavení kontroly, které jsou k dispozici v této verzi preview.

## <a name="the-halo-effect"></a>Haló efekt 
Některé kontroly podporují Haló efekt. Tento efekt využívá dvě různé barvy okamžitě ještě neprojevila změna vizuálně z transformace. Šedá představuje hodnotu před transformací, která se nejnovější a modrá zobrazí aktuální hodnotu. Tento efekt lze povolit a zakázat v možnosti.

## <a name="graphical-filtering"></a>Grafické filtrování 
Některé z inspektor podporují filtrování dat pomocí inspektoru jako editor. Použití inspektoru jako editor vyžaduje výběr grafické prvky a potom pomocí panelu nástrojů v pravé horní části okna Inspektor filtrování snížení nebo navýšení kapacity vybrané hodnoty. 

## <a name="column-statistics"></a>Statistiky sloupce
Tato kontrola pro číselné sloupce, poskytuje celou řadu různých statistiky o sloupci. Statistika zahrnují následující hodnoty: 
- Minimální
- Nižší QUARTIL
- Medián
- Horní QUARTIL
- Maximum
- Průměr
- Směrodatná odchylka


### <a name="options"></a>Možnosti 
- Žádný

## <a name="histogram"></a>Histogram 
Vypočítá a zobrazí histogram jeden číselný sloupec. Výchozí počet intervalů se vypočítá Scottova pravidlo. Pravidlo však lze přepsat pomocí možnosti.

Tato kontrola podporuje Haló efekt.


### <a name="options"></a>Možnosti
- Minimální počet kbelíků (platí, i když výchozí kontejnery je zaškrtnuté)
- Výchozí počet kbelíků (Scottova pravidlo) 
- Zobrazit Haló
- Překrytí vykreslení hustotou jádra (Gaussovy jádra) 
- Použít logaritmické měřítko


### <a name="actions"></a>Akce
Tato kontrola podporuje filtrování prostřednictvím kontejnerů, které mohou zahrnovat jednoduchého nebo vícenásobného výběru intervalů. Použijte filtry, jak je uvedeno výše.

## <a name="value-counts"></a>Četnost hodnot
Tato kontrola představuje tabulku četnost hodnot pro sloupec, který aktuálně není vybrán. Výchozí zobrazení je pro začátek šesti hodnotami. Limit můžete změnit ale do libovolného počtu. Můžete také nastavit displej tak, aby počítat od konce místo horní části. Tato kontrola podporuje Haló efekt.

### <a name="options"></a>Možnosti 
- Počet horní hodnoty
- Sestupně
- Obsahovat hodnoty null/chyba
- Zobrazit Haló
- Použít logaritmické měřítko


### <a name="actions"></a>Akce 
Tato kontrola podporuje filtrování prostřednictvím pruhy, které mohou zahrnovat pruhy jednoduchého nebo vícenásobného výběru. Použijte filtry, jak je uvedeno výše.

## <a name="box-plot"></a>Krabicový graf 
Vykreslení krabicového grafu pole číselné sloupce.

### <a name="options"></a>Možnosti 
- Seskupit podle sloupce

## <a name="scatter-plot"></a>Bodové vykreslení
Korelačního diagramu pro dvě číselné sloupce. Data jsou předvýpočtem zredukovaných z důvodů výkonu. Velikost vzorku lze přepsat v dialogovém okně Možnosti.

### <a name="options"></a>Možnosti  
- Sloupec osy x
- Sloupec osy y
- Velikost vzorku
- Seskupit podle sloupce


## <a name="time-series"></a>Časové řady
Spojnicový graf povědomí o času na ose x.

### <a name="options"></a>Možnosti
- Sloupec kalendářních dat
- Číselný sloupec
- Velikost vzorku


### <a name="actions"></a>Akce
Tato kontrola podporuje filtrování prostřednictvím metodu pro výběr kliknutím a přetažením vyberte oblast v grafu. Po dokončení výběru použijte filtry, jak je uvedeno výše.


## <a name="map"></a>Mapa 
Mapa s body, které jsou zobrazeny, za předpokladu, že nebyly zadány zeměpisné šířky a délky. Nejprve je nutné vybrat zeměpisné šířky.

### <a name="options"></a>Možnosti
- Zeměpisná šířka sloupce
- Zeměpisná délka sloupce
- Clustering pro
- Seskupit podle sloupce


### <a name="actions"></a>Akce
Tato kontrola podporuje filtrování přes výběr bodů na mapě. Stisknutím klávesy **Ctrl** klíče a potom klikněte na tlačítko a přetáhněte myší na formuláři čtverec kolem body. Pak použijte filtry, jak je uvedeno výše.

Můžete rychle velikost mapu, aby zobrazovala pouze možné body stisknutím kombinace kláves **E** na levé straně mapy.


## <a name="pattern-frequency"></a>Vzor četnosti 

Tato kontrola se zobrazí seznam vzorů ve vybraném sloupci řetězec. Tyto vzory se dají se vyjadřují pomocí regulárních výrazů, jako je syntaxe. Najetí myší na vzorek ukazuje příklady hodnoty podle tohoto vzoru. Spolu s vzory je zobrazen také přibližné coverages jako procenta.

![Obrázek inspektoru vzor](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>Možnosti
- Počet horní hodnoty
- Sestupně
- Zobrazit Haló

### <a name="actions"></a>Akce
Tato kontrola podporuje filtrování podle zobrazeného vzorů. Stisknutím klávesy **Ctrl** klíče a pak vyberte plného pruhy v inspektoru vzor. Pak použijte filtry, jak je uvedeno výše. V důsledku acion uživatel se přidá na krok Rozšířený filtr. Můžete zobrazit a upravit generovaného kódu Python vyvoláním možnosti úprav kroku Rozšířený filtr.
