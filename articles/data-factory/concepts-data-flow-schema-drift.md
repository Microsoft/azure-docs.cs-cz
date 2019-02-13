---
title: Azure Data Factory mapování odchylek schématu toku dat
description: Vytváření odolných toky dat ve službě Azure Data Factory pomocí schématu odchylek
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 5799386b3ed725c610569caae8dfc72796c654b9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213302"
---
# <a name="azure-data-factory-mapping-data-flow-schema-drift"></a>Azure Data Factory mapování odchylek schématu toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Koncept odchylek schématu je případ, kde vaše zdroje často mění metadat. Pole, sloupce, typy, atd. lze přidat, odebrat nebo změnit v reálném čase. Bez zpracování pro schéma odchylek toku dat stane citlivé na změny změny nadřazeného datového zdroje. Při změně příchozí sloupců a polí, typické vzorech ETL se nezdaří, protože mají tendenci svázány s těmito názvy zdroje.

Pokud chcete chránit proti schématu odchylek, je důležité mít zařízení v nástroji toku dat, aby jako datových inženýrů na:

* Definovat zdroje, které mají proměnlivé pole názvy, typy dat, hodnoty a velikosti
* Definování parametrů transformace, které budou fungovat s modely dat namísto pevně zakódované pole a hodnoty
* Definování výrazů, které porozumění vzorcům tak, aby odpovídaly příchozí pole namísto použití pole s názvem

V Azure Data Factory toku dat zobrazují se zařízení prostřednictvím tohoto pracovního postupu:

* Zvolte možnost "Povolit odchylek schématu" ve vaší zdrojové transformace

<img src="media/data-flow/schemadrift001.png" width="400">

* Když vyberete tuto možnost, všechny příchozí pole se načtou ze zdroje na každého spuštění toku dat a bude celý tok má předat do jímky.

* Nezapomeňte použít "Auto-Map" mapovat všechna nová pole v transformaci jímky tak, aby všechna nová pole získat výběru nahoru a dostali se do cíl:

<img src="media/data-flow/automap.png" width="400">

* Všechno, co bude fungovat při zavedení nových polí v tomto scénáři s jednoduchou zdroj -> jímky (neboli zkopírujte) mapování.

* Přidat transformace pracovního postupu, který zpracovává odchylek schématu, vám pomůže porovnávání vzorů odpovídají sloupcům podle názvu, typ a hodnotu.

* Pokud chcete k vytvoření transformace, která analyzuje "Odchylek schéma", klikněte na "Přidat sloupec vzor" v odvozené sloupce nebo agregace transformace.

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Potřebujete provést rozhodnutí o architektuře ve svém toku dat tak, aby přijímal odchylek schématu v rámci toku. Když toto provedete, můžete chránit proti změny schématu ze zdroje. Časná vazba sloupce a typy však budou ztraceny v celém datový tok. Azure Data Factory považuje za schématu odchylek toky toky pozdní vazbu, takže při sestavování transformace názvy sloupců, nebudou k dispozici v zobrazení schématu během celého toku.

<img src="media/data-flow/taxidrift1.png" width="400">

V ukázce ukázka taxislužby toku dat je ukázka odchylek schématu v dolní části toku dat se zdrojem TripFare. V agregované transformaci Všimněte si, že používáme návrhu "sloupce vzor" pro agregaci pole. Namísto názvů konkrétní sloupce, nebo pokud chcete najít sloupce podle pozice, předpokládáme, že data můžete změnit a nemusí ve stejném pořadí mezi spuštění.

V tomto příkladu schématu odchylek toku dat objekt pro vytváření dat Azure zpracování jsme vytvořili a agregace, který kontroluje pro sloupce typu double, vědomím, že data doména obsahuje ceny pro každou cestu. Výpočtu agregační matematické jsme pak můžete provádět přes všechny dvojitá pole ve zdroji, bez ohledu na to, kde jsou sloupci a bez ohledu na to, názvy sloupce.

Syntaxe toku dat objekt pro vytváření dat Azure používá k reprezentaci jednotlivých odpovídající sloupec z odpovídající vzor $$. Můžete také hledat shody na použití funkcí regulárních výrazů a složité řetězec hledání názvy sloupců. V takovém případě budeme vytvářet nový název pole agregované podle jednotlivých shod 'double' typu sloupce a přidat text ```_total``` všechny odpovídající názvy: 

```concat($$, '_total')```

Pak jsme bude zaokrouhlení a součet hodnot pro každou z těchto odpovídající sloupce:

```round(sum ($$))```

To všechno s ukázkou toku dat objekt pro vytváření dat Azure "Taxislužby ukázku" můžete otestovat. Přepněte na relaci ladění pomocí ladění přepnout v horní části návrhové plochy toku dat, takže můžete interaktivně zobrazit výsledky:

<img src="media/data-flow/taxidrift2.png" width="800">

