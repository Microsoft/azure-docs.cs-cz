---
title: 'Převeďte na sdílený svazek clusteru: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak použít převést do modulu sdíleného svazku clusteru ve službě Azure Machine Learning a datové sady převést do formátu CSV, který můžete stáhnout, exportu nebo sdílení s moduly skriptu R nebo Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8b8b6758cc2df7a092ce36e9507f84ac534d0e3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028722"
---
# <a name="convert-to-csv-module"></a>Převést do modulu sdíleného svazku clusteru

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k převedení datovou sadu do formátu CSV, který můžete stáhnout, exportu nebo sdílení s moduly skriptu R nebo Python.

### <a name="more-about-the-csv-format"></a>Další informace o formátu CSV 

Formát CSV, který zastupuje "textový soubor s oddělovači", je formát souboru používaný mnoha externích nástrojů strojového učení. Sdílený svazek clusteru je běžné formát pro výměnu při práci s open source jazyků, jako je R nebo Python.

I v případě, že budete provádět většinu práce ve službě Azure Machine Learning, jsou časy, kdy možná pro vás bude užitečné převést vaše datové sady do souboru CSV pro použití v externích nástrojů. Příklad:

+ Stáhněte si soubor CSV a otevřít ho pomocí aplikace Excel nebo ho naimportovat do relační databáze.  
+ Uložte soubor CSV do cloudového úložiště a k němu připojit z Power BI a vytvářet vizualizace.  
+ Použijte formát CSV k přípravě dat pro použití v R a Python. Klepněte pravým tlačítkem výstup modulu pro generování kódu potřebné pro přístup k datům přímo z Pythonu nebo Poznámkový blok Jupyter. 

Při převodu datovou sadu do sdíleného svazku clusteru, je soubor uložen ve vašem pracovním prostoru Azure ML. Nástroj služby Azure storage můžete použít k otevření a použití souboru přímo, nebo klikněte pravým tlačítkem na výstup modulu a stažení souboru CSV do počítače nebo použít v kódem R nebo Python.  

## <a name="how-to-configure-convert-to-csv"></a>Jak nakonfigurovat převést na sdílený svazek clusteru

1.  Přidat [převést na sdílený svazek clusteru](./convert-to-csv.md) modulu do experimentu. V tomto modulu můžete najít **převody formátů dat** skupiny v rozhraní. 

2. Připojte ho k libovolného modulu, jejichž výstupem jsou datové sady.   
  
3.  Spusťte experiment.

### <a name="results"></a>Výsledky
  

Dvakrát klikněte na panel výstupu [převést na sdílený svazek clusteru](./convert-to-csv.md)a vyberte jednu z těchto možností.  

 + **Datová sada výsledků -> stáhnout**: Okamžitě se otevře kopii dat ve formátu CSV, který můžete uložit do místní složky. Pokud nezadáte složku, je použit výchozí název souboru a soubor CSV je uložen v místní **stáhne** knihovny.


 + **Datová sada výsledků -> Uložit jako datovou sadu**: Uloží soubor CSV zpět do pracovního prostoru Azure ML jako samostatné datové sady.

 + **Generovat kód pro přístup k datům**: Služba Azure ML generuje dvě sady kódu, abyste pro přístup k datům s použitím jazyka Python nebo s použitím R. Přístup k datům, zkopírujte fragment kódu do vaší aplikace. (*Generovat kód přístupu k datům se chystá.* )

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 