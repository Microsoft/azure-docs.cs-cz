---
title: Azure Data Factory mapování transformace toku zdroje dat.
description: Azure Data Factory mapování transformace toku zdroje dat.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 38a01b4f81b76ba90a5fda4909d0e65e6307057e
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408710"
---
# <a name="mapping-data-flow-source-transformation"></a>Mapování datového toku zdroj transformace

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformace zdrojového nakonfiguruje zdroje dat, který chcete použít k vložení dat do datového toku. V jedné toku dat může mít více než jedna transformace zdroje. Vždy začínají návrh vašich toků dat se zdrojem.

> [!NOTE]
> Každý tok dat vyžaduje alespoň jeden zdroj transformace. Přidejte tolik další zdroje, potřebujete k dokončení transformace data. Můžete připojit k těmto zdrojům společně s spojení nebo Union transformace.

![Možnosti transformace zdroje](media/data-flow/source.png "zdroje")

Každá transformace toku dat zdroje musí být přidružená přesně jednu datovou sadu datové továrny, který definuje tvar a umístění dat k zápisu nebo čtení z. Můžete použít zástupné znaky a soubor seznamů ve zdroji pro práci s více než jeden soubor současně.

## <a name="data-flow-staging-areas"></a>Tok dat pracovní oblasti

Tok dat funguje s "staging" datových sad, které jsou v Azure. Tyto datové sady datového toku se používají pro pracovní postup transformace dat data. Data Factory má přístup k téměř 80 různé nativní konektory. Zahrnout data z těchto zdrojů do vašeho toku dat, nejprve připravte tato data do jednoho z těchto oblastí pracovní datové sady se předávají Data pomocí aktivity kopírování.

## <a name="options"></a>Možnosti

### <a name="allow-schema-drift"></a>Povolit schématu odchylek
Pokud zdrojové sloupce se změní často, vyberte Povolit odchylek schématu. Toto nastavení povolí všechna příchozí pole ze zdroje do toku prostřednictvím transformací pro jímku.

### <a name="validate-schema"></a>Ověření schématu

![Veřejný zdroj](media/data-flow/source1.png "veřejný zdroj 1")

Pokud příchozí verze zdroje dat se neshoduje s definované schéma, se nezdaří spuštění toku dat.

### <a name="sampling"></a>Vzorkování
Pomocí vzorkování můžete omezit počet řádků ze zdroje.  To je užitečné, když potřebujete jen ukázkou svá zdrojová data pro účely ladění a testování.

## <a name="define-schema"></a>Definovat schéma

![Zdroj transformace](media/data-flow/source2.png "zdroje 2")

Pro typy zdrojových souborů, které nejsou silného typu (to znamená plochých souborů na rozdíl od soubory Parquet) byste měli definovat typy dat pro jednotlivá pole tady v transformaci zdroje. Následně můžete změnit názvy sloupců v vyberte transformaci a datové typy v transformaci odvozené sloupce. 

![Zdroj transformace](media/data-flow/source003.png "datové typy")

Pro silného typu zdroje, můžete upravit 

### <a name="optimize"></a>Optimalizace

![Zdrojové oddíly](media/data-flow/sourcepart.png "dělení")

Na kartě optimalizovat pro transformaci zdroje se zobrazí další dělení typ nazývá "Zdroj". To bude pouze světla – až po dokončení výběru databáze Azure SQL jako zdroj. Je to proto ADF bude chtít paralelizovat připojení ke spuštění velkého dotazy na zdroje služby Azure SQL DB.

Dělení dat v databázi SQL zdroje je volitelný, ale je vhodné pro velké dotazy. Máte dvě možnosti:

### <a name="column"></a>Sloupec

Vyberte sloupec do oddílu na ze zdrojové tabulky. Musíte také nastavit maximální počet připojení.

### <a name="query-condition"></a>Podmínka dotazu

Volitelně můžete při vytváření oddílů připojení na základě dotazu. Pro tuto možnost stačí vložte obsah predikát WHERE. I.e. year > 1980

## <a name="source-file-management"></a>Správa zdrojového souboru
![Nová nastavení zdroje](media/data-flow/source2.png "nové nastavení")

* Zástupný cesta k výběru řadu soubory ze zdrojové složky, které odpovídají vzoru. Tím se přepíše všech souborů, které jste nastavili ve vaší definici datové sady.
* Seznam souborů. Stejné jako sady souborů. Přejděte do textového souboru, který vytvoříte seznam soubory relativní cestu ke zpracování.
* Sloupce pro uložení názvu souboru uloží název souboru ze zdroje ve sloupci ve vašich datech. Zadejte nový název pro uložení řetězce názvu souboru.
* Po dokončení (můžete zvolit Neprovádět žádnou akci se zdrojovým souborem po spuštění toku dat, zdrojový soubor odstranit nebo přesunout zdrojové soubory. Cesty pro přesunutí jsou relativní cesty.

### <a name="sql-datasets"></a>Datové sady SQL

Pokud používáte Azure SQL Database nebo Azure SQL data Warehouse jako zdroj, budou mít další možnosti.

* Dotaz: Zadejte dotaz SQL pro zdroj. Nastavení dotazu se přepíšou všechny tabulky, kterou jste zvolili v datové sadě. Všimněte si, že se tady nepodporují klauzule Order By.

* Velikost dávky: Zadejte velikost dávky k bloku dat velkých objemů dat do služby batch velikost čtení.

> [!NOTE]
> Operace nastavení soubor se spustí, jenom Pokud tok dat je spuštěn ze spuštění kanálu (ladění kanálu nebo spustit provádění) pomocí aktivity spustit tok dat v rámci kanálu. Operace se soubory se neprovedou v toku dat režimu ladění.

### <a name="projection"></a>Projekce

![Projekce](media/data-flow/source3.png "projekce")

Podobně jako na schémata v datových sadách, projekce ve zdroji definuje sloupce dat, datové typy a formáty dat ze zdrojových dat. Pokud máte textový soubor s definované schéma, klikněte na tlačítko "Rozpoznat datový typ" požádat ADF pokusí ukázkový a odvodit datové typy. Můžete nastavit výchozí data formátů pro automatické rozpoznání pomocí tlačítka "Definovat výchozí formát". Můžete upravit datové typy sloupce v následné transformace odvozené sloupce. Názvy sloupců může být upraveno pomocí vyberte transformace.

![Výchozí formáty](media/data-flow/source2.png "výchozí formáty")

## <a name="next-steps"></a>Další kroky

Začít sestavovat vaši jak transformovat data pomocí [odvozené sloupce](data-flow-derived-column.md) a [vyberte](data-flow-select.md).
