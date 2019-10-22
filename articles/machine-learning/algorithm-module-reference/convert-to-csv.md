---
title: 'Převést na sdílený svazek clusteru: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul převést do sdíleného svazku clusteru ve službě Azure Machine Learning k převodu datové sady do formátu CSV, který se dá stáhnout, exportovat nebo sdílet s moduly R nebo Python Script.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 37c1653b31049dc0142cf8c0c6f6d405338b1d1f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693202"
---
# <a name="convert-to-csv-module"></a>Převést na modul CSV

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Pomocí tohoto modulu můžete převést datovou sadu na formát CSV, který se dá stáhnout, exportovat nebo sdílet s moduly R nebo Python Script.

### <a name="more-about-the-csv-format"></a>Další informace o formátu CSV 

Formát CSV, který představuje "hodnoty oddělené čárkami", je formát souboru, který používá mnoho externích nástrojů strojového učení. CSV je běžný formát pro výměnu při práci s open source jazyky, jako je R nebo Python.

I v případě, že většinu práce v Azure Machine Learning používáte, v některých případech může být užitečné převést datovou sadu na CSV a použít ji v externích nástrojích. Například:

+ Stáhněte soubor CSV a otevřete ho v Excelu nebo ho importujte do relační databáze.  
+ Uložte soubor CSV do cloudového úložiště a připojte se k němu z Power BI, abyste mohli vytvářet vizualizace.  
+ Pomocí formátu CSV Připravte data pro použití v R a Pythonu. Stačí kliknout pravým tlačítkem na výstup modulu, který vygeneruje kód potřebný pro přístup k datům přímo z Pythonu nebo Jupyter poznámkového bloku. 

Když datovou sadu převedete do sdíleného svazku clusteru, soubor se uloží do vašeho pracovního prostoru Azure ML. Pomocí nástroje Azure Storage můžete soubor otevřít a použít přímo, nebo můžete kliknout pravým tlačítkem na výstup modulu a stáhnout soubor CSV do počítače nebo ho použít v kódu R nebo Pythonu.  

## <a name="how-to-configure-convert-to-csv"></a>Jak nakonfigurovat převod na CSV

1.  Přidejte do svého kanálu modul [převést do sdíleného svazku clusteru](./convert-to-csv.md) . Tento modul můžete najít ve skupině **převodů formátu dat** v rozhraní. 

2. Připojte ho ke všem modulům, které výstupují datovou sadu.   
  
3.  Spuštění kanálu

### <a name="results"></a>Výsledky
  

Dvakrát klikněte na výstup [převést na sdílený svazek clusteru](./convert-to-csv.md)a vyberte jednu z těchto možností.  

 + **Výsledná sada dat – > stáhnout**: ihned otevře kopii dat ve formátu CSV, kterou můžete uložit do místní složky. Pokud složku nezadáte, použije se výchozí název souboru a soubor CSV se uloží do místní knihovny pro **stahování** .


 + **Výsledná sada dat – > Uložit jako datovou sadu**: uloží soubor CSV zpátky do pracovního prostoru Azure ml jako samostatnou datovou sadu.

 + **Generování kódu pro přístup k datům**: Azure ml generuje dvě sady kódu pro přístup k datům, a to buď pomocí Pythonu, nebo pomocí R. Chcete-li získat přístup k datům, zkopírujte do aplikace fragment kódu. (*Generování kódu pro přístup k datům bude brzy.* )

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 