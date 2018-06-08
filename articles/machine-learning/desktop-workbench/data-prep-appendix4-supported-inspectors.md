---
title: Podporované kontroly, které jsou k dispozici v Azure Machine Learning Data přípravy | Microsoft Docs
description: Tento dokument obsahuje úplný seznam kontroly, které jsou k dispozici pro přípravu Azure Machine Learning dat.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: ca9b46335105e62b023e7db16e0ead4061fbfd00
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830710"
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Podporované inspektoři Azure Machine Learning datový přípravy ve verzi Preview
Tento dokument popisuje sadu kontroly, které jsou k dispozici v této verzi preview.

## <a name="the-halo-effect"></a>Haló efekt 
Některé inspektoři podporovat bylo účinek. Tento efekt pomocí dvou různých barev okamžitě zobrazit změnu vizuálně z transformace. Šedá představuje hodnotu před nejnovější transformace a modrý zobrazuje aktuální hodnotu. Tento efekt lze povolit a zakázat v možnostech.

## <a name="graphical-filtering"></a>Grafické filtrování 
Některé inspektoři podporují filtrování dat pomocí kontrolor jako editor. Jako editor pomocí kontrolor zahrnuje výběr grafické prvky a potom pomocí panelu nástrojů v pravé horní části okna inspector filtrovat příchozí nebo odchozí vybraných hodnot. 

## <a name="column-statistics"></a>Statistiky sloupce
Tato kontrola pro číselné sloupce, poskytuje celou řadu různých statistiky o sloupci. Statistické údaje následující měření: 
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
Vypočítá a zobrazí histogram jednoho číselné sloupce. Výchozí počet kbelíků se počítá pomocí Scott na pravidla. Pravidlo však lze přepsat pomocí možnosti.

Tato kontrola podporuje bylo účinek.


### <a name="options"></a>Možnosti
- Minimální počet kbelíků (platí to i když výchozí bucketing je zaškrtnuta možnost)
- Výchozí počet kbelíků (Scott na pravidlo) 
- Zobrazit bylo
- Překrytí výkresu hustotou jádra (Gaussovské jádra) 
- Použít logaritmickou stupnici


### <a name="actions"></a>Akce
Tato kontrola podporuje filtrování prostřednictvím sad, které mohou zahrnovat jeden nebo více vyberte intervalů. Použijte filtry, jak se popisuje výš.

## <a name="value-counts"></a>Počty hodnota
Tato kontrola uvede tabulku frekvence hodnot pro sloupec, který je aktuálně vybraný. Výchozí zobrazení je nejvyšší šesti hodnotami. Limit můžete změnit na jakékoli číslo, ale. Můžete také nastavit zobrazení počítat od nejnižší místo horní. Tato kontrola podporuje bylo účinek.

### <a name="options"></a>Možnosti 
- Počet nejvyšší hodnoty
- Sestupně
- Obsahovat hodnoty null nebo chyby
- Zobrazit bylo
- Použít logaritmickou stupnici


### <a name="actions"></a>Akce 
Tato kontrola podporuje filtrování prostřednictvím řádky, které mohou zahrnovat jeden nebo více vyberte řádky. Použijte filtry, jak se popisuje výš.

## <a name="box-plot"></a>Krabicový graf 
Vykreslení krabicového grafu pole číselné sloupce.

### <a name="options"></a>Možnosti 
- Seskupit podle sloupce

## <a name="scatter-plot"></a>Bodový graf
Bodové vykreslení pro dvě číselné sloupce. Data jsou nižší vzorkovat z důvodů výkonu. Velikost vzorku může být přepsána nastaveními v možnosti.

### <a name="options"></a>Možnosti  
- Sloupec osy x
- Sloupec osy y
- Velikost vzorku
- Seskupit podle sloupce


## <a name="time-series"></a>Časové řady
Spojnicový graf povědomí o času na ose x.

### <a name="options"></a>Možnosti
- Sloupce s datem
- Číselný sloupec
- Velikost vzorku


### <a name="actions"></a>Akce
Tato kontrola podporuje filtrování vyberte metodou kliknutím a přetažením vybrat rozsah, v grafu. Po dokončení výběru použijte filtry, jak se popisuje výš.


## <a name="map"></a>Mapa 
Mapa s body, které jsou zobrazeny, za předpokladu, že bylo zadáno zeměpisné šířky a délky. Zeměpisná šířka musí být vybrán první.

### <a name="options"></a>Možnosti
- Zeměpisná šířka sloupce
- Zeměpisná délka sloupce
- Clustering pro
- Seskupit podle sloupce


### <a name="actions"></a>Akce
Tato kontrola podporuje filtrování přes výběr bodu na mapě. Stiskněte **Ctrl** klíče a pak klikněte na tlačítko a přetáhněte ji pomocí myši na formuláři čtverce kolem body. Potom použijte filtry, jak se popisuje výš.

Můžete rychle velikost mapu, aby zobrazila pouze možné body stisknutím **E** na levé straně mapy.


## <a name="pattern-frequency"></a>Vzor frekvence 

Tato kontrola zobrazuje seznam vzorů ve vybraném sloupci řetězec. Vzory jsou reprezentované pomocí regulárního výrazu jako syntaxe. Ukazatele na vzoru jsou uvedeny příklady hodnot reprezentována tohoto vzoru. Společně s vzory je také zobrazit přibližnou coverages jako procenta.

![Obrázek inspector vzor](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>Možnosti
- Počet nejvyšší hodnoty
- Sestupně
- Zobrazit bylo

### <a name="actions"></a>Akce
Tato kontrola podporuje filtrování podle zobrazených vzory. Stiskněte **Ctrl** klíče a potom vyberte vyplněné řádky v vzor inspector. Potom použijte filtry, jak se popisuje výš. V důsledku acion uživatele se přidá na krok rozšířeného filtru. Můžete zobrazit a upravit generovaný kód Python vyvoláním možnost Upravit krok Advanced filtru.
