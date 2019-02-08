---
title: Pomocí dekorace značky zvýrazněte text – vlastní vyhledávání Bingu
titlesuffix: Azure Cognitive Services
description: Ukazuje, jak povolit dekorace textu v odpovědi na vyhledávání.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: maheshb
ms.openlocfilehash: 17297b651d672cee746076d563b5c410390c0fa2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862798"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Pomocí značek dekorace zvýraznění textu

Bingu podporuje zvýraznění shod, které označí termíny dotazu (nebo jiné podmínky této Bingu najde relevantní) v zobrazení řetězce některé části odpovědi. Například na webové stránce společnosti `name`, `displayUrl`, a `snippet` pole označit termíny dotazu.

Ve výchozím nastavení neobsahuje Bingu zvýraznění značky v zobrazení řetězce. Zahrnout značek, zahrnout `textDecorations` parametr v žádosti o dotazu a nastavte ho na **true**. Bing označí termíny dotazu pomocí znaků E000 a E001 Unicode pro označení začátku a konci období. Například pokud termín dotazu je řízení ledě a buď termín existuje v poli, termín je ohraničen přístupů zvýraznění znaky jak je znázorněno v následujícím příkladu:  
  
![Zvýrazňování položek](./media/bing-hit-highlighting.PNG) 

Před zobrazením řetězce v uživatelském rozhraní, by znaky Unicode nahradit znaky, které jsou vhodné pro váš formát zobrazení. Například chcete-li zobrazit text ve formátu HTML, můžete chtít zvýraznit termín dotazu tak, že nahradíte E000 s < b\> a E001 s < /b\>. Pokud nechcete použít formátování, odeberte z řetězce značky. 

Bing poskytuje možnost používat znaky znakové sady Unicode nebo značky HTML jako značky. Pokud chcete zadat značky, které používat, patří `textFormat` parametr dotazu. Chcete-li označit obsah se znaky Unicode, nastavte `textFormat` Raw (výchozí) a označí obsah pomocí značky jazyka HTML, nastavte `textFormat` do formátu HTML. 
  
Pokud `textDecorations` je **true**, Bingu může zahrnovat následující značky v zobrazení řetězce odpovědi. Pokud neexistuje žádný ekvivalent HTML, buněk tabulky HTML je prázdný.

|Kódování Unicode|HTML|Popis
|-|-|-
|U + E000|\<b>|Označuje začátek výrazu dotazu (zvýrazňování shod)
|U+E001|\</b>|Označuje konec termín dotazu
|U+E002|\<i>|Označuje začátek kurzívou obsahu 
|U+E003|\</i>|Označuje konec obsahu kurzívou
|U+E004|\<br/>|Označuje konec řádku
|U+E005||Označuje začátek telefonní číslo
|U+E006||Označuje konec telefonní číslo
|U+E007||Označuje začátek adresy
|U+E008||Označuje konec adresu
|U+E009|\&nbsp;|Označí pevná mezera
|U + E00C|\<strong>|Označuje začátek tučného písma obsahu
|U+E00D|\</strong>|Označuje konec tučného písma obsahu
|U + E00E||Označuje konec obsahu, jehož pozadí by měla být nižší než jeho okolního pozadí
|U+E00F||Označuje konec obsahu, jehož pozadí by měla být nižší než jeho okolního pozadí
|U+E010||Označuje konec obsahu, jehož pozadí by měl být o něco tmavší než jeho okolního pozadí
|U + E011||Označuje konec obsahu, jehož pozadí by měl být o něco tmavší než jeho okolního pozadí
|U + E012|\<del>|Označuje konec obsahu, který by měl být přeškrtnutá.
|U+E013|\</del>|Označuje konec obsahu, který by měl být přeškrtnutá.
|U+E016|\<sub>|Označuje začátek dolního indexu obsah
|U+E017|\</sub>|Označuje konec obsahu dolního indexu
|U+E018|\<sup>|Označuje začátek horní index obsahu
|U+E019|\</sup>|Označuje konec obsahu horní index

Následující příklad ukazuje `Computation` odpověď, která obsahuje dolního indexu značky pro termín log(2) dotazu. `expression` Značky pouze tehdy, pokud obsahuje pole "je textdecoration – **true**.

![Výpočet značky](./media/bing-markers-computation.PNG) 

Pokud požadavek nezažádali dekorace, výraz by log10(2). 
  
