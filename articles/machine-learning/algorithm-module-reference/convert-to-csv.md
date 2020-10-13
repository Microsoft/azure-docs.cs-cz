---
title: 'Převést na sdílený svazek clusteru: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul převést do sdíleného svazku clusteru v Azure Machine Learning k převodu datové sady do formátu CSV, který se dá stáhnout, exportovat nebo sdílet s moduly R nebo Python Script.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 3b7c781717952765941acd4ea4aa47593d4b8a0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898784"
---
# <a name="convert-to-csv-module"></a>Převést na modul CSV

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete převést datovou sadu na formát CSV, který se dá stáhnout, exportovat nebo sdílet s moduly R nebo Python Script.

### <a name="more-about-the-csv-format"></a>Další informace o formátu CSV 

Formát CSV, který představuje "hodnoty oddělené čárkami", je formát souboru, který používá mnoho externích nástrojů strojového učení. CSV je běžný formát pro výměnu při práci s open source jazyky, jako je R nebo Python.

I v případě, že většinu práce v Azure Machine Learning používáte, v některých případech může být užitečné převést datovou sadu na CSV a použít ji v externích nástrojích. Například:

+ Stáhněte soubor CSV a otevřete ho v Excelu nebo ho importujte do relační databáze.  
+ Uložte soubor CSV do cloudového úložiště a připojte se k němu z Power BI, abyste mohli vytvářet vizualizace.  
+ Pomocí formátu CSV Připravte data pro použití v R a Pythonu. 

Když datovou sadu převedete na CSV, uloží se sdílený svazek clusteru do pracovního prostoru Azure ML. Pomocí nástroje Azure Storage můžete soubor otevřít a použít přímo. Ke sdílenému svazku clusteru se dostanete také tak, že vyberete modul **převést na sdílený svazek clusteru** a potom vyberete ikonu histogramu na kartě **výstupy** na pravém panelu, abyste zobrazili výstup. Sdílený svazek clusteru můžete stáhnout ze složky výsledků do místního adresáře.  

## <a name="how-to-configure-convert-to-csv"></a>Jak nakonfigurovat převod na CSV


1.  Přidejte do svého kanálu modul převést do sdíleného svazku clusteru. Tento modul můžete najít ve skupině **transformace dat** v návrháři. 

2. Připojte ho ke všem modulům, které výstupují datovou sadu.   
  
3.  Odešlete kanál.

### <a name="results"></a>Výsledky
  

Vyberte kartu **výstupy** v pravém panelu **převést na sdílený svazek clusteru**a v části **výstupy portů**vyberte jednu z těchto ikon.  

+ **Registrovat datovou sadu**: vyberte ikonu a uložte soubor CSV zpátky do pracovního prostoru Azure ml jako samostatnou datovou sadu. Datovou sadu můžete najít jako modul ve stromu modulu pod částí **Moje datové sady** .

 + **Zobrazit výstup**: vyberte ikonu oka a postupujte podle pokynů pro procházení složky **Results_dataset** a stáhněte data.csv soubor.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 