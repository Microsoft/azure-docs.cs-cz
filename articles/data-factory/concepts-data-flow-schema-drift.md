---
title: Posun schématu v toku dat mapování
description: Vytváření odolných toků dat ve službě Azure Data Factory pomocí funkce Posun schémat
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 6e361d23860ce8f40abba5c246242cf345bb974c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606102"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Posun schématu v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Posun schématu je případ, kdy zdroje často mění metadata. Pole, sloupce a typy lze přidávat, odebírat nebo měnit průběžně. Bez zpracování posunu schématu se tok dat stává zranitelným vůči změnám zdrojových dat pro upstream. Typické vzory ETL se nezdaří při změně příchozích sloupců a polí, protože mají tendenci být vázány na tyto názvy zdrojů.

Chcete-li se chránit před posunem schématu, je důležité mít zařízení v nástroji pro tok dat, který vám jako datovému inženýrovi umožní:

* Definování zdrojů, které mají měnitelné názvy polí, datové typy, hodnoty a velikosti
* Definujte parametry transformace, které mohou pracovat se vzorky dat namísto pevně zakódovaných polí a hodnot
* Definujte výrazy, které chápou vzorky tak, aby odpovídaly příchozím polím, namísto použití pojmenovaných polí

Azure Data Factory nativně podporuje flexibilní schémata, která se mění z provádění na spuštění, takže můžete vytvářet obecnou logiku transformace dat bez nutnosti překompilovat toky dat.

Musíte provést architektonické rozhodnutí v toku dat přijmout posun schématu v celém toku. Když toto provést, můžete chránit před změnami schématu ze zdrojů. Vprůběhu toku dat však ztratíte časnou vazbu sloupců a typů. Azure Data Factory považuje drift schématu jako toky pozdní vazby, takže při vytváření transformací, unášené názvy sloupců nebude k dispozici v zobrazení schématu v celém toku.

Toto video poskytuje úvod do některých složitých řešení, které můžete snadno vytvářet v adf s funkcí posunu schématu toku dat. V tomto příkladu vytvoříme opakovaně použitelné vzory založené na flexibilních schématech databáze:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Posun schématu ve zdroji

Sloupce přicházející do toku dat z definice zdroje jsou definovány jako "posunuté", pokud nejsou přítomny ve zdrojové projekci. Zdrojovou projekci můžete zobrazit z karty projekce ve zdrojové transformaci. Když vyberete datovou sadu pro zdroj, ADF automaticky převezme schéma z datové sady a vytvoří projekt z této definice schématu datové sady.

Ve zdrojové transformaci je posun schématu definován jako čtení sloupců, které nejsou definovány schéma datové sady. Chcete-li povolit posun schématu, zaškrtněte **políčko Povolit posun schématu** v transformaci zdroje.

![Zdroj driftu schématu](media/data-flow/schemadrift001.png "Zdroj driftu schématu")

Pokud je povoleno posun schématu, všechna příchozí pole jsou čtena ze zdroje během provádění a předány přes celý tok do jímky. Ve výchozím nastavení všechny nově zjištěné sloupce, označované jako *posunuté sloupce*, dorazí jako datový typ řetězce. Pokud chcete, aby tok dat automaticky odvodil datové typy posunutých sloupců, zaškrtněte **políčko Infer unášené typy sloupců** ve zdrojovém nastavení.

## <a name="schema-drift-in-sink"></a>Posun schématu v umyvadle

V transformaci jímky je posun schématu, když zapíšete další sloupce nad co je definováno ve schématu dat jímky. Chcete-li povolit posun schématu, **zkontrolujte povolit posun schématu** v transformaci jímky.

![Posunový dřez schématu](media/data-flow/schemadrift002.png "Posunový dřez schématu")

Pokud je povolen posun schématu, zkontrolujte, zda je zapnutý jezdec **Automatické mapování** na kartě Mapování. S tímto posuvníkem na všechny příchozí sloupce jsou zapsány do cíle. V opačném případě je nutné použít mapování založené na pravidlech k zápisu posunutých sloupců.

![Automatické mapování jímky](media/data-flow/automap.png "Automatické mapování jímky")

## <a name="transforming-drifted-columns"></a>Transformace posunutých sloupců

Pokud tok dat posunul sloupce, můžete k nim přistupovat v transformacích pomocí následujících metod:

* Výrazy `byPosition` `byName` a slouží k explicitnímu odkazu na sloupec podle názvu nebo čísla pozice.
* Přidání vzorku sloupce v odvozeném sloupci nebo agregační transformaci tak, aby odpovídala libovolné kombinaci názvu, datového proudu, pozice nebo typu
* Přidání mapování založeného na pravidlech v transformaci Select nebo Sink tak, aby odpovídalo posunutým sloupcům s aliasy sloupců pomocí vzoru

Další informace o implementaci vzorů sloupců naleznete [v tématu Vzory sloupců v toku dat mapování](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Rychlá akce v mapových unášených sloupech

Chcete-li explicitně odkazovat na posunuté sloupce, můžete rychle generovat mapování pro tyto sloupce prostřednictvím rychlé akce náhledu dat. Po zapnutí [režimu ladění](concepts-data-flow-debug-mode.md) přejděte na kartu Náhled dat a kliknutím na **Aktualizovat** najděte náhled dat. Pokud data factory zjistí, že existují posunuté sloupce, můžete kliknout na **Mapa posunuté** a generovat odvozený sloupec, který umožňuje odkazovat na všechny posunuté sloupce v zobrazení schématu po proudu.

![Mapa unášena](media/data-flow/mapdrifted1.png "Mapa unášena")

V transformaci generované odvozené sloupce každý posunuté sloupec je mapován na jeho zjištěný název a datový typ. Ve výše uvedeném náhledu dat je sloupec "movieId" rozpoznán jako celé číslo. Po **kliknutí na posun mapy** je v odvozeném sloupci `toInteger(byName('movieId'))` definováno i i i v zobrazení schématu v následných transformacích.

![Mapa unášena](media/data-flow/mapdrifted2.png "Mapa unášena")

## <a name="next-steps"></a>Další kroky
V [jazyce exprese toku dat](data-flow-expression-functions.md)najdete další zařízení pro vzory sloupců a posun schématu včetně "byName" a "byPosition".
