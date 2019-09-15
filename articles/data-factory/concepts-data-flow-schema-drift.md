---
title: Posun schématu v mapování toku dat | Azure Data Factory
description: Vytváření odolných toků dat v Azure Data Factory s využitím posunu schématu
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 68c0da5a7fe2b02c6115a8c1bbc24feb95e12adb
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003738"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Posun schématu v mapování toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Posun schématu je případ, kdy zdroje často mění metadata. Pole, sloupce a typy lze průběžně přidávat, odebírat nebo měnit. Bez manipulace se započetím schématu se váš tok dat bude zranitelný proti změnám nadřazeného zdroje dat. Typické vzory ETL selžou, když se změní příchozí sloupce a pole, protože by měly být vázány na tyto názvy zdrojů.

Pro zajištění ochrany proti posunu schématu je důležité mít zařízení v nástroji pro tok dat, který vám jako datovou inženýru umožní:

* Definování zdrojů, které mají proměnlivé názvy polí, datové typy, hodnoty a velikosti
* Definování parametrů transformace, které mohou pracovat se vzorci dat místo pevně zakódovaných polí a hodnot
* Definujte výrazy, které porozumět vzorům, aby se shodovaly se vstupními poli namísto použití pojmenovaných polí

Azure Data Factory nativně podporuje flexibilní schémata, která se mění z provádění na spouštění, aby bylo možné vytvořit obecnou logiku transformace dat bez nutnosti překompilovat toky dat.

V toku dat musíte učinit rozhodnutí o architektuře, abyste mohli přijímat v celém toku posun schématu. Když to uděláte, můžete se chránit proti změnám schématu ze zdrojů. Ztratíte ale počáteční vazbu sloupců a typů v rámci toku dat. Azure Data Factory zachází s Flowy ze schémat jako s pozdní vazbou, takže při sestavování transformací nebudete mít k dispozici v zobrazeních schématu v průběhu toku sloupce s nezpracovanými názvy.

## <a name="schema-drift-in-source"></a>Odtenatových schématu ve zdroji

Ve zdrojové transformaci je na posunu schématu definovaná jako sloupce pro čtení, které nedefinují vaše schéma datové sady. Pokud chcete povolit posun schématu, zaškrtněte možnost **Povolit posun schématu** ve zdrojové transformaci.

![Zdroj posunu schématu](media/data-flow/schemadrift001.png "Zdroj posunu schématu")

Když je zapnutý posun schématu, všechna příchozí pole se během provádění načtou ze zdroje a předají se celému toku do jímky. Ve výchozím nastavení se všechny nově zjištěné sloupce, označované jako *sloupce*s datovým typem, dorazí jako datový typ String. Pokud chcete, aby tok dat automaticky odvodit datové typy sloupců se sloupci, zrušte ve svém nastavení zdroje možnost **odvodit typy** vydaných sloupců.

## <a name="schema-drift-in-sink"></a>Posunování schématu v jímky

Při transformaci jímky je posun schématu při psaní dalších sloupců nad tím, co je definováno ve schématu dat jímky. Pokud chcete povolit posun schématu, zaškrtněte možnost **Povolit posun schématu** při transformaci jímky.

![Nedigitalizační jímka schématu](media/data-flow/schemadrift002.png "Nedigitalizační jímka schématu")

Pokud je zapnutý posun schématu, ujistěte se, že je zapnutý posuvník **automatického mapování** na kartě mapování. Pomocí tohoto posuvníku na jsou všechny příchozí sloupce zapsány do vašeho cíle. V opačném případě je nutné použít mapování na základě pravidel k zápisu vydaných sloupců.

![Automatické mapování jímky](media/data-flow/automap.png "Automatické mapování jímky")

## <a name="transforming-drifted-columns"></a>Transformace vydaných sloupců

Když datový tok obsahuje sloupce, můžete k nim přistupovat v transformacích pomocí následujících metod:

* Použijte výrazy `byName` a k explicitnímu odkazu na sloupec podle názvu nebo čísla pozice. `byPosition`
* Přidejte do odvozeného sloupce vzor sloupce nebo agregovanou transformaci podle libovolné kombinace názvu, datového proudu, pozice nebo typu.
* Přidání mapování založeného na pravidlech v transformaci SELECT nebo Sink tak, aby se shodovaly se sloupci s aliasy přes vzor

Další informace o implementaci vzorů sloupců najdete v tématu [vzory sloupců v části mapování toku dat](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Rychlá akce mapování vydaných sloupců

Chcete-li explicitně odkazovat na sloupce, můžete pro tyto sloupce rychle vygenerovat mapování pomocí rychlé akce Náhled dat. Jakmile je [režim ladění](concepts-data-flow-debug-mode.md) zapnutý, přejděte na kartu náhled dat a kliknutím na **aktualizovat** načtěte data Preview. Pokud objekt pro vytváření dat zjistí, že sloupce existují, můžete kliknout na tlačítko **Mapa se posunem** a vygenerovat odvozený sloupec, který vám umožní odkazovat na všechny sloupce v zobrazení schématu pro podřízené.

![Mapa s posunem](media/data-flow/mapdrifted1.png "Mapa s posunem")

Ve vygenerované transformaci odvozeného sloupce je každý sloupec s rovným sloupcem namapován na jeho zjištěné názvy a datový typ. Ve výše uvedeném náhledu dat je sloupec ' movieId ' zjištěn jako celé číslo. Po kliknutí na **mapu** se movieId je v odvozeném sloupci definována jako `toInteger(byName('movieId'))` a obsažená v zobrazeních schématu v části s transformacemi na navazujících typech.

![Mapa s posunem](media/data-flow/mapdrifted2.png "Mapa s posunem")

## <a name="next-steps"></a>Další kroky
V [jazyce výrazu toku dat](data-flow-expression-functions.md)najdete další informace o vzorcích sloupců a posunu schématu včetně možností "byName" a "byPosition".
