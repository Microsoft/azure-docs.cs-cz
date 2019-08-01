---
title: Nastavte transformaci zdroje v funkci toku dat mapování Azure Data Factory
description: Přečtěte si, jak nastavit transformaci zdroje v toku dat mapování.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 974ece9cd035ae29ada38f34b7933d86f682194f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696231"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformace zdroje pro tok dat mapování 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Zdrojová transformace konfiguruje zdroj dat pro tok dat. Tok dat může zahrnovat víc než jednu zdrojovou transformaci. Při navrhování toků dat vždy začněte se zdrojovou transformací.

Každý tok dat vyžaduje aspoň jednu zdrojovou transformaci. Přidejte tolik zdrojů, kolik je potřeba k dokončení transformace dat. Tyto zdroje můžete spojit společně s transformací transformace nebo sjednocením.

> [!NOTE]
> Při ladění toku dat se data čtou ze zdroje pomocí nastavení vzorkování nebo zdrojů ladění. Chcete-li zapsat data do jímky, je nutné spustit tok dat z aktivity toku dat kanálu. 

![Možnosti transformace zdroje na kartě nastavení zdroje](media/data-flow/source.png "zdroj")

Přidružte transformaci zdroje toku dat k právě jedné datové sadě Data Factory. Datová sada definuje tvar a umístění dat, ke kterým chcete zapisovat, nebo z nich číst. Ve zdroji můžete použít zástupné znaky a seznamy souborů pro práci s více než jedním souborem v jednom okamžiku.

Když použijete možnost **vzorového zástupného znaku** , nastaví se ADF, aby prochází každou shodnou složku a soubor v jediné transformaci zdroje. Toto je velice efektivní způsob, jak zpracovat více souborů v rámci jednoho toku. Chcete-li sledovat název aktuálně zpracovávaného souboru, nastavte pole název sloupce na Uložit název souboru v možnostech zdroje.

> [!NOTE]
> Nastavte více vzorů pro porovnávání se zástupnými znaky pomocí znaménka + vedle stávajícího vzoru zástupného znaku a přidejte další pravidla zástupných znaků.

## <a name="data-flow-staging-areas"></a>Pracovní oblasti toku dat
Tok dat funguje s *přípravnými* datovými sadami, které jsou všechny v Azure. Tyto datové sady použijte pro přípravu při transformaci dat. 

Data Factory má přístup k téměř 80 nativním konektorům. Chcete-li zahrnout data z těchto jiných zdrojů do toku dat, použijte nástroj pro kopírování aktivit pro přípravu těchto dat v jednom z pracovních oblastí datové sady datových toků.

## <a name="options"></a>Možnosti

Vyberte možnosti schématu a vzorkování pro vaše data.

### <a name="schema-drift"></a>Posun schématu
[Posun schématu](concepts-data-flow-schema-drift.md) je schopnost ADF nativně zpracovávat flexibilní schémata v datových tocích, aniž by bylo nutné explicitně definovat změny sloupců.

* Pokud se zdrojové sloupce často mění, vyberte možnost **povoluje posun schématu** . Toto nastavení umožňuje, aby všechna vstupní pole zdroje prošla transformacemi do jímky.

* Zvolíte-li možnost **odvodit sloupce** s odrovnými sloupci, požádejte ADF, aby definovala datové typy pro každý zjištěný nový sloupec. Když je tato funkce vypnutá, vytvoří ADF řetězec.

### <a name="validate-schema"></a>Ověřit schéma

Pokud příchozí verze zdrojových dat neodpovídá definovanému schématu, tok dat se nepodaří spustit.

![Nastavení veřejného zdroje, které zobrazuje možnosti pro ověření schématu, povolení posunu schématu a vzorkování](media/data-flow/source1.png "veřejný zdroj 1")

### <a name="sample-the-data"></a>Ukázka dat
Povolte **vzorkování** , abyste omezili počet řádků ze zdroje. Toto nastavení použijte při testování nebo vzorkování dat ze zdroje pro účely ladění.

## <a name="define-schema"></a>Definovat schéma

Pokud zdrojové soubory nejsou silného typu (například ploché soubory namísto souborů Parquet), definujte datové typy pro každé pole ve zdrojové transformaci.  

![Nastavení transformace zdroje na kartě definice schématu](media/data-flow/source2.png "zdroj 2")

Později můžete změnit názvy sloupců v transformaci SELECT. Pro změnu datových typů použijte transformaci odvozeného sloupce. U zdrojů se silným typem můžete změnit datové typy v pozdější transformaci SELECT. 

![Datové typy v transformaci Select](media/data-flow/source003.png "datové typy")

### <a name="optimize-the-source-transformation"></a>Optimalizace zdrojové transformace

Na kartě **optimalizace** pro transformaci zdroje se může zobrazit typ **zdrojového** oddílu. Tato možnost je dostupná jenom v případě, že je váš zdroj Azure SQL Database. Důvodem je to, že Data Factory se snaží vytvořit propojení paralelně, aby se spouštěly velké dotazy proti vašemu zdroji SQL Database.

![Nastavení zdrojového oddílu](media/data-flow/sourcepart3.png "vytváření oddílů")

Nemusíte rozdělit data na zdroj SQL Database, ale oddíly jsou užitečné pro velké dotazy. Oddíl můžete založit na sloupci nebo dotazu.

### <a name="use-a-column-to-partition-data"></a>Použití sloupce k dělení dat

Ve zdrojové tabulce vyberte sloupec, na kterém chcete vytvořit oddíly. Nastavte také počet oddílů.

### <a name="use-a-query-to-partition-data"></a>Použití dotazu k dělení dat

Můžete se rozhodnout rozdělit připojení na základě dotazu. Stačí zadat obsah predikátu WHERE. Zadejte například rok > 1980.

## <a name="source-file-management"></a>Správa zdrojového souboru

Vyberte nastavení pro správu souborů ve zdroji. 

![Nové nastavení zdroje](media/data-flow/source2.png "Nové nastavení")

* **Cesta zástupného znaku**: Ze zdrojového kontejneru vyberte řadu souborů, které odpovídají vzoru. Toto nastavení přepíše libovolný soubor v definici datové sady.

Příklady zástupných znaků:

* ```*```Představuje libovolnou sadu znaků.
* ```**```Představuje rekurzivní vnořování adresářů.
* ```?```Nahradí jeden znak.
* ```[]```Odpovídá jednomu nebo více znakům v závorkách

* ```/data/sales/**/*.csv```Načte všechny soubory CSV v rámci/data/Sales.
* ```/data/sales/20??/**```Načte všechny soubory ve dvacátém století.
* ```/data/sales/2004/*/12/[XY]1?.csv```Načte všechny soubory CSV v 2004 v prosinci počínaje písmenem X nebo Y s předponou a číslem 2 číslice.

V datové sadě musí být zadaný kontejner. Cesta ke zástupným znakům proto musí taky obsahovat cestu ke složce z kořenové složky.

* **Kořenová cesta oddílu**: Pokud máte ve zdroji ```key=value``` souborů ve formátu dělené složky (tj. Year = 2019), můžete pomocí služby ADF požádat o přiřazení nejvyšší úrovně stromu složek oddílu k názvu sloupce v datovém proudu toku dat.

Nejdřív nastavte zástupný znak tak, aby zahrnoval všechny cesty, které jsou rozdělené do oddílů, a soubory listů, které chcete číst.

![Nastavení zdrojového souboru oddílu](media/data-flow/partfile2.png "Nastavení souboru oddílu")

Teď použijte nastavení kořenové cesty oddílu k informování ADF, co je nejvyšší úroveň struktury složek. Když teď zobrazíte obsah vašich dat, uvidíte, že tento ADF bude přidávat vyřešené oddíly, které se nacházejí v jednotlivých úrovních vaší složky.

![Kořenová cesta oddílu](media/data-flow/partfile1.png "Zobrazit kořenovou cestu oddílu")

* **Seznam souborů**: Toto je sada souborů. Vytvořte textový soubor, který obsahuje seznam relativních souborů cest ke zpracování. Najeďte na tento textový soubor.
* **Sloupec pro uložení názvu souboru**: Uložte název zdrojového souboru do sloupce v datech. Sem zadejte nový název pro uložení řetězce názvu souboru.
* **Po dokončení**: Po spuštění toku dat vyberte, že nechcete nic dělat se zdrojovým souborem, odstraňte zdrojový soubor nebo přesuňte zdrojový soubor. Cesty pro přesun jsou relativní.

Chcete-li přesunout zdrojové soubory do následujícího následného zpracování, vyberte nejprve možnost přesunout pro operaci soubor. Potom nastavte adresář "z". Pokud pro cestu nepoužíváte žádné zástupné znaky, pak bude mít nastavení "od" stejnou složku jako vaše zdrojová složka.

Pokud máte zdrojovou cestu se zástupným znakem, vaše syntaxe bude vypadat následovně:

```/data/sales/20??/**/*.csv```

Můžete zadat "od" jako

```/data/sales```

A "to" jako

```/backup/priorSales```

V tomto případě se všechny soubory, které byly nasource v/data/Sales, přesunuly do/backup/priorSales.

### <a name="sql-datasets"></a>Datové sady SQL

Pokud je váš zdroj v SQL Database nebo SQL Data Warehouse, máte k dispozici další možnosti správy zdrojového souboru.

* **Dotaz:** Zadejte dotaz SQL pro zdroj. Toto nastavení potlačí všechny tabulky, které jste vybrali v datové sadě. Všimněte si, že klauzule **ORDER by** nejsou tady podporované, ale můžete nastavit úplný příkaz SELECT FROM. Můžete také použít uživatelsky definované funkce tabulky. **SELECT * FROM udfGetData ()** je UDF v SQL, který vrací tabulku. Tento dotaz vytvoří zdrojovou tabulku, kterou můžete použít v toku dat.
* **Velikost dávky**: Zadejte velikost dávky pro velké objemy dat v čtení.
* **Úroveň izolace**: Ve výchozím nastavení pro zdroje SQL v datových tocích mapování ADF je čtení nepotvrzené. Úroveň izolace můžete změnit tady na jednu z těchto hodnot:
* Čtení potvrzeno
* Čtení nepotvrzených
* Opakované čtení
* Serializovatelný
* Žádné (ignorovat úroveň izolace)

![Úroveň izolace](media/data-flow/isolationlevel.png "Úroveň izolace")

> [!NOTE]
> Operace se soubory běží jenom při spuštění toku dat ze spuštění kanálu (ladění kanálu nebo spuštění spuštění), které používá aktivitu spustit tok dat v kanálu. Operace *se* soubory neběží v režimu ladění toku dat.

### <a name="projection"></a>Projekce

Stejně jako schémata v datových sadách definuje projekce ve zdroji datové sloupce, typy a formáty ze zdrojových dat. 

![Nastavení na kartě projekce](media/data-flow/source3.png "Projekce")

Pokud textový soubor nemá žádné definované schéma, vyberte možnost **detekovat datový typ** , aby data Factory vzorkovat a odvodit datové typy. Pro automatické rozpoznání výchozích formátů dat vyberte možnost **definovat výchozí formát** . 

Datové typy sloupců můžete upravit v pozdější transformaci odvozeného sloupce. Použijte transformaci SELECT k úpravě názvů sloupců.

![Nastavení pro výchozí formáty dat](media/data-flow/source2.png "Výchozí formáty")

### <a name="add-dynamic-content"></a>Přidat dynamický obsah
Po kliknutí na pole na panelu nastavení se zobrazí hypertextový odkaz na Přidat dynamický obsah. Když vyberete spuštění Tvůrce výrazů, nastavíte hodnoty dynamicky pomocí výrazů, statických literálových hodnot nebo parametrů.

![Parametry](media/data-flow/params6.png "Parametry")

## <a name="next-steps"></a>Další postup

Začněte sestavovat [transformaci odvozeného sloupce](data-flow-derived-column.md) a [transformaci Select](data-flow-select.md).
