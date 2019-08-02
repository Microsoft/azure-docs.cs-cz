---
title: Azure Data Factory – posun schématu toku dat
description: Vytváření odolných toků dat v Azure Data Factory s využitím posunu schématu
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: b5777300f5033569caf3868218e747df3ff83a76
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640226"
---
# <a name="mapping-data-flow-schema-drift"></a>Mapování posunu schématu toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Koncept posunu schématu je případ, kdy zdroje často mění metadata. Pole, sloupce, typy atd. lze v průběhu práce přidat, odebrat nebo změnit. Bez manipulace se započetím schématu se váš tok dat bude zranitelný vůči změnám v nadřazených zdrojích dat. Když se změní příchozí sloupce a pole, typické vzory ETL se nezdaří, protože by mohly být vázané na tyto názvy zdrojů.

Aby bylo možné chránit proti posunu schématu, je důležité mít zařízení v nástroji pro tok dat, který vám jako datovou inženýry umožní:

* Definování zdrojů, které mají proměnlivé názvy polí, datové typy, hodnoty a velikosti
* Definování parametrů transformace, které mohou pracovat se vzorci dat místo pevně zakódovaných polí a hodnot
* Definujte výrazy, které porozumět vzorům, aby se shodovaly se vstupními poli namísto použití pojmenovaných polí

## <a name="how-to-implement-schema-drift-in-adf-mapping-data-flows"></a>Postup implementace posunu schématu v Tokůch dat mapování ADF
ADF nativně podporuje flexibilní schémata, která se mění z provádění na spouštění, takže můžete vytvořit obecnou logiku transformace dat bez nutnosti překompilovat toky dat.

* Volba "povolení posunu schématu" ve zdrojové transformaci

<img src="media/data-flow/schemadrift001.png" width="400">

* Pokud jste vybrali tuto možnost, všechna příchozí pole budou načtena ze zdroje při každém spuštění toku dat a budou předána celým tokem do jímky.

* Všechny nově zjištěné sloupce (sloupce ve sloupci) budou ve výchozím nastavení doručeny jako datový typ String. Ve zdrojové transformaci vyberte možnost "odvoditelné typy sloupců", pokud chcete, aby ADF automaticky odvodit datové typy ze zdroje.

* Nezapomeňte použít "automatické mapování" pro mapování všech nových polí v transformaci jímky, aby se všechna nová pole vybrala a vyložila ve vašem cíli a v jímky také nastavila možnost "povolení posunu schématu".

<img src="media/data-flow/automap.png" width="400">

* Vše bude fungovat, když jsou v tomto scénáři zavedena nová pole s jednoduchým mapováním > jímka na zdrojovém kódu.

* Chcete-li do tohoto pracovního postupu přidat transformace, které zpracovávají posun schématu, můžete použít porovnávání vzorů pro porovnávání sloupců podle názvu, typu a hodnoty.

* Pokud chcete vytvořit transformaci, která pochopení "posunu schématu", klikněte na tlačítko "přidat vzor sloupce" v odvozeném sloupci nebo v agregační transformaci.

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> V toku dat musíte učinit rozhodnutí o architektuře, abyste mohli přijímat v celém toku posun schématu. Když to uděláte, můžete se chránit proti změnám schématu ze zdrojů. Ztratíte ale počáteční vazbu sloupců a typů v rámci toku dat. Azure Data Factory považuje v průběhu toku k dispozici špičky schématu jako proudy s pozdní vazbou, takže při sestavování transformací nebudou mít názvy sloupců k dispozici v zobrazeních schématu v průběhu toku.

<img src="media/data-flow/taxidrift1.png" width="400">

V toku ukázkových dat ukázky taxislužby se v dolním toku dat se zdrojem TripFare posune vzorové schéma. V agregační transformaci si všimněte, že pro pole agregace používáme návrh "vzor sloupců". Místo pro pojmenování určitých sloupců nebo hledání sloupců podle pozice předpokládáme, že se data můžou změnit a nemusí se zobrazovat ve stejném pořadí mezi běhy.

V tomto příkladu Azure Data Factory zpracování posunu schématu toku dat jsme sestavili a agregaci, která vyhledává sloupce typu "Double" s vědomím, že datová doména obsahuje ceny za každou cestu. Pak můžeme provádět agregační matematický výpočet ve všech dvojitých polích ve zdroji bez ohledu na to, kde sloupec je, a bez ohledu na pojmenování sloupce.

Syntaxe toku dat Azure Data Factory používá $ $ k vyjádření každého odpovídajícího sloupce z odpovídajícího vzoru. Můžete se také shodovat s názvy sloupců pomocí funkcí vyhledávání složitých řetězců a regulárních výrazů. V tomto případě vytvoříme nový agregovaný název pole založený na každé shodě typu "Double" (sloupec) a připojíte text ```_total``` ke každému z těchto odpovídajících názvů: 

```concat($$, '_total')```

Potom budeme zaokrouhlovat a sčítat hodnoty pro každý z těchto odpovídajících sloupců:

```round(sum ($$))```

Tuto funkci funkce posunu schématu můžete sledovat v práci s ukázkou toku dat Azure Data Factory ukázka "taxislužby demo". Přepněte se na relaci ladění pomocí přepínače ladění v horní části návrhové plochy toku dat, abyste viděli výsledky interaktivně:

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>Přístup k novým sloupcům pro příjem dat
Když generujete nové sloupce se vzorci sloupců, budete mít k těmto novým sloupcům později přístup v rámci transformace toku dat pomocí těchto metod:

* Pomocí příkazu "byPosition" Identifikujte nové sloupce podle čísla pozice.
* Pomocí příkazu "byName" Identifikujte nové sloupce podle jejich názvu.
* V vzorcích sloupců použijte "název", "Stream", "Position" nebo "Type" nebo libovolnou kombinaci těchto hodnot, aby odpovídaly novým sloupcům.

## <a name="rule-based-mapping"></a>Mapování na základě pravidel
Transformace Select a jímka podporuje vzorové porovnávání prostřednictvím mapování založeného na pravidlech. To vám umožní vytvořit pravidla, která mapují sloupce po sloupcích na aliasy sloupců a zajímky těchto sloupců do cíle.

## <a name="next-steps"></a>Další postup
V [jazyce výrazu toku dat](data-flow-expression-functions.md) najdete další možnosti pro vzorce sloupců a posun schématu, včetně "byName" a "byPosition".
