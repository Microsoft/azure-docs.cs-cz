---
title: 'Převést na CSV: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Převést na CSV v Azure Machine Learning převést datovou sadu do formátu CSV, který lze stáhnout, exportovat nebo sdílet s moduly skriptů R nebo Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477676"
---
# <a name="convert-to-csv-module"></a>Převést na modul CSV

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k převodu datové sady do formátu CSV, který lze stáhnout, exportovat nebo sdílet s moduly skriptů R nebo Pythonu.

### <a name="more-about-the-csv-format"></a>Více o formátu CSV 

Formát CSV, který je zkratkou pro "hodnoty oddělené čárkami", je formát souboru používaný mnoha externími strojovými nástroji. CSV je běžný výměnný formát při práci s otevřenými zdrojovými jazyky, jako je R nebo Python.

I v případě, že většinu práce v Azure Machine Learning uděláte, jsou chvíle, kdy vám může být užitečné převést datovou sadu na CSV pro použití v externích nástrojích. Například:

+ Stáhněte soubor CSV a otevřete jej v aplikaci Excel nebo importujte do relační databáze.  
+ Uložte soubor CSV do cloudového úložiště a připojte se k němu z Power BI a vytvořte vizualizace.  
+ Pomocí formátu CSV připravte data pro použití v R a Pythonu. 

Při převodu datové sady na CSV se csv uloží do pracovního prostoru Azure ML. Nástroj úložiště Azure můžete použít k otevření a použití souboru přímo. K souboru CSV v návrháři můžete také přistupovat výběrem modulu **Převést na CSV** a potom vybrat ikonu histogramu na kartě **Výstupy** v pravém panelu a zobrazit výstup. Soubor CSV můžete stáhnout ze složky Výsledky do místního adresáře.  

## <a name="how-to-configure-convert-to-csv"></a>Jak nakonfigurovat převést na CSV


1.  Přidejte modul Převést na CSV do kanálu. Tento modul najdete ve skupině **Transformace dat** v návrháři. 

2. Připojte jej k libovolnému modulu, který vyvádí datovou sadu.   
  
3.  Odešlete potrubí.

### <a name="results"></a>Výsledky
  

Vpravém panelu **převést na csv**vyberte kartu **Výstupy** a vyberte na jedné z těchto ikon pod **výstupy portů**.  

+ **Registrace datové sady**: Vyberte ikonu a uložte soubor CSV zpět do pracovního prostoru Azure ML jako samostatnou datovou sadu. Datovou sadu jako modul najdete ve stromu modulů v části **Moje datové sady.**

 + **Zobrazit výstup**: Vyberte ikonu oka a podle pokynů procházejte **složku Results_dataset** a stáhněte soubor data.csv.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 