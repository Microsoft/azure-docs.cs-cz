---
title: Transformace zdroje v toku dat mapování – Azure Data Factory
description: Přečtěte si, jak nastavit transformaci zdroje v toku dat mapování.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 5889d96057d4b028e8716e407819d17938f58b3c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675950"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformace zdroje pro tok dat mapování 

Zdrojová transformace konfiguruje zdroj dat pro tok dat. Při navrhování toků dat bude váš první krok vždycky konfigurovat transformaci zdroje. Zdroj přidáte tak, že kliknete na pole **Přidat zdroj** v plátně toku dat.

Každý tok dat vyžaduje aspoň jednu zdrojovou transformaci, ale můžete přidat tolik zdrojů, kolik jich je potřeba k dokončení transformace dat. Tyto zdroje můžete spojit spolu s transformací, vyhledáváním nebo sjednocením.

Každá transformace zdroje je přidružená k právě jedné datové sadě Data Factory. Datová sada definuje tvar a umístění dat, ke kterým chcete zapisovat, nebo z nich číst. Pokud používáte datovou sadu založenou na souborech, můžete ve zdroji použít zástupné znaky a seznamy souborů pro práci s více než jedním souborem v jednom okamžiku.

## <a name="supported-connectors-in-mapping-data-flow"></a>Podporované konektory v toku dat mapování

Mapování toku dat sleduje přístup k extrakci, načítání, transformaci (ELT) a pracuje s *přípravnými* datovými sadami, které jsou všechny v Azure. V současné době je možné v transformaci zdroje použít následující datové sady:
    
* Azure Blob Storage (JSON, Avro, text, Parquet)
* Azure Data Lake Storage Gen1 (JSON, Avro, text, Parquet)
* Azure Data Lake Storage Gen2 (JSON, Avro, text, Parquet)
* Azure SQL Data Warehouse
* Azure SQL Database
* Azure CosmosDB

Azure Data Factory má přístup k více než 80 nativním konektorům. Pokud chcete do toku dat zahrnout data z jiných zdrojů, použijte aktivitu kopírování a načtěte tato data do jedné z podporovaných pracovních oblastí.

## <a name="source-settings"></a>Nastavení zdroje

Jakmile přidáte zdroj, nakonfigurujte ho přes kartu **Nastavení zdroje** . Tady můžete vybrat nebo vytvořit datovou sadu, na které se zdrojové body nacházejí. Můžete také vybrat možnosti schématu a vzorkování pro vaše data.

![Karta nastavení zdroje](media/data-flow/source1.png "Karta nastavení zdroje")

**Posun schématu:** [posun schématu](concepts-data-flow-schema-drift.md) je schopnost objektu pro vytváření dat nativně zpracovávat flexibilní schémata v datových tocích, aniž by bylo nutné explicitně definovat změny sloupců.

* Zaškrtněte políčko pro **Povolení posunu schématu** , pokud se zdrojové sloupce často mění. Toto nastavení umožňuje, aby všechna vstupní pole zdroje prošla transformacemi do jímky.

* Výběr možnosti **odvodit sloupce** s vydanými sloupci způsobí pokyn objektu pro vytváření dat ke zjištění a definování datových typů pro každý nalezený nový sloupec. Když je tato funkce vypnutá, všechny sloupce poklesu budou typu String.

**Ověřit schéma:** Pokud je vybraná možnost ověřit schéma, tok dat se nepodaří spustit, pokud příchozí zdrojová data neodpovídají definovanému schématu datové sady.

**Přeskočit počet řádků:** Pole počet řádků přeskočení určuje, kolik řádků se má na začátku datové sady ignorovat.

**Vzorkování:** Povolte vzorkování, abyste omezili počet řádků ze zdroje. Toto nastavení použijte při testování nebo vzorkování dat ze zdroje pro účely ladění.

**Víceřádkové řádky:** Vyberte víceřádkové řádky, pokud zdrojový textový soubor obsahuje řetězcové hodnoty, které jsou rozloženy do více řádků, tj. newlines uvnitř hodnoty.

Pokud chcete ověřit, že je váš zdroj správně nakonfigurovaný, zapněte režim ladění a načtěte data ve verzi Preview. Další informace naleznete v tématu [režim ladění](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Když je režim ladění zapnutý, při konfiguraci limitu řádků v nastavení ladění dojde k přepsání nastavení vzorkování ve zdroji během náhledu dat.

## <a name="file-based-source-options"></a>Souborové možnosti zdroje

Pokud používáte datovou sadu založenou na souborech, jako je například Azure Blob Storage nebo Azure Data Lake Storage, karta **Možnosti zdroje** vám umožní spravovat způsob čtení souborů ve zdroji.

![Možnosti zdroje](media/data-flow/sourceOPtions1.png "Možnosti zdroje")

**Cesta zástupného znaku:** Pomocí vzoru se zástupnými znaky nastavíte ADF, aby prochází každou shodnou složku a soubor v jediné zdrojové transformaci. Toto je efektivní způsob, jak zpracovat více souborů v rámci jednoho toku. Přidejte více vzorů pro porovnávání se zástupnými znaky s symbolem +, který se zobrazí při najetí myší na stávající zástupný vzor.

Ze zdrojového kontejneru vyberte řadu souborů, které odpovídají vzoru. V datové sadě lze zadat pouze kontejner. Cesta ke zástupným znakům proto musí taky obsahovat cestu ke složce z kořenové složky.

Příklady zástupných znaků:

* ```*``` představuje libovolnou sadu znaků.
* ```**``` představuje rekurzivní vnořování adresářů.
* ```?``` nahrazuje jeden znak.
* ```[]``` odpovídá jednomu nebo více znakům v závorkách.

* ```/data/sales/**/*.csv``` získá všechny soubory CSV pod/data/Sales
* ```/data/sales/20??/**``` získá všechny soubory ve dvacátém století.
* ```/data/sales/2004/*/12/[XY]1?.csv``` získá všechny soubory CSV v 2004 v prosinci začínající znakem X nebo Y a číslem se dvěma číslicemi.

**Kořenová cesta oddílu:** Pokud jste ve zdroji souborů nastavili dělené složky s formátem ```key=value``` (například Year = 2019), můžete přiřadit nejvyšší úroveň stromu složek oddílu k názvu sloupce v datovém proudu toku dat.

Nejdřív nastavte zástupný znak tak, aby zahrnoval všechny cesty, které jsou rozdělené do oddílů, a soubory listů, které chcete číst.

![Nastavení zdrojového souboru oddílu](media/data-flow/partfile2.png "Nastavení souboru oddílu")

Nastavení kořenové cesty oddílu použijte k definování toho, co je nejvyšší úroveň struktury složek. Když zobrazíte obsah vašich dat prostřednictvím náhledu dat, uvidíte, že tento ADF bude přidávat vyřešené oddíly, které se nacházejí v jednotlivých úrovních vaší složky.

![Kořenová cesta oddílu](media/data-flow/partfile1.png "Zobrazit kořenovou cestu oddílu")

**Seznam souborů:** Toto je sada souborů. Vytvořte textový soubor, který obsahuje seznam relativních souborů cest ke zpracování. Najeďte na tento textový soubor.

**Sloupec pro uložení názvu souboru:** Uložte název zdrojového souboru do sloupce v datech. Sem zadejte nový název sloupce pro uložení řetězce názvu souboru.

**Po dokončení:** Po spuštění toku dat vyberte, že nechcete nic dělat se zdrojovým souborem, odstraňte zdrojový soubor nebo přesuňte zdrojový soubor. Cesty pro přesun jsou relativní.

Chcete-li přesunout zdrojové soubory do následujícího následného zpracování, vyberte nejprve možnost přesunout pro operaci soubor. Potom nastavte adresář "z". Pokud pro cestu nepoužíváte žádné zástupné znaky, pak bude mít nastavení "od" stejnou složku jako vaše zdrojová složka.

Pokud máte zdrojovou cestu se zástupným znakem, vaše syntaxe bude vypadat následovně:

```/data/sales/20??/**/*.csv```

Můžete zadat "od" jako

```/data/sales```

A "to" jako

```/backup/priorSales```

V tomto případě se všechny soubory, které se nacházely v/data/Sales, přesunuly do/backup/priorSales.

> [!NOTE]
> Operace se soubory běží jenom při spuštění toku dat ze spuštění kanálu (ladění kanálu nebo spuštění spuštění), které používá aktivitu spustit tok dat v kanálu. Operace *se* soubory neběží v režimu ladění toku dat.

**Filtrovat podle poslední změny:** Můžete filtrovat, které soubory se mají zpracovat, zadáním rozsahu data při jejich poslední úpravě. Všechna data jsou v čase UTC. 

### <a name="add-dynamic-content"></a>Přidat dynamický obsah

Všechna nastavení zdroje lze zadat jako výrazy pomocí [jazyka výrazů transformace toku dat mapování](data-flow-expression-functions.md). Chcete-li přidat dynamický obsah, klikněte nebo umístěte ukazatel myši uvnitř polí na panelu nastavení. Klikněte na hypertextový odkaz **Přidat dynamický obsah**. Tím se spustí Tvůrce výrazů, kde můžete dynamicky nastavit hodnoty pomocí výrazů, hodnot statických literálů nebo parametrů.

![Parametry](media/data-flow/params6.png "Parametry")

## <a name="sql-source-options"></a>Možnosti zdroje SQL

Pokud je váš zdroj v SQL Database nebo SQL Data Warehouse, na kartě **Možnosti zdroje** jsou k dispozici další nastavení specifická pro SQL. 

**Vstup:** Vyberte, zda chcete nasměrovat zdroj v tabulce (ekvivalent ```Select * from <table-name>```), nebo zadejte vlastní dotaz SQL.

**Dotaz**: Pokud ve vstupním poli vyberete možnost dotaz, zadejte pro zdroj dotaz SQL. Toto nastavení potlačí všechny tabulky, které jste vybrali v datové sadě. Klauzule **ORDER by** nejsou tady podporované, ale můžete nastavit úplný příkaz SELECT FROM. Můžete také použít uživatelsky definované funkce tabulky. **SELECT * FROM udfGetData ()** je UDF v SQL, který vrací tabulku. Tento dotaz vytvoří zdrojovou tabulku, kterou můžete použít v toku dat. Použití dotazů je také skvělým způsobem, jak omezit řádky pro testování nebo pro vyhledávání. Příklad: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Velikost dávky**: zadejte velikost dávky pro velké objemy dat v čtení.

**Úroveň izolace**: ve výchozím nastavení pro zdroje SQL v toku dat mapování je čtení nepotvrzené. Úroveň izolace můžete změnit tady na jednu z těchto hodnot:
* Čtení potvrzeno
* Čtení nepotvrzených
* Opakované čtení
* Serializovatelný
* Žádné (ignorovat úroveň izolace)

![Úroveň izolace](media/data-flow/isolationlevel.png "Úroveň izolace")

## <a name="projection"></a>Projekce

Stejně jako schémata v datových sadách definuje projekce ve zdroji datové sloupce, typy a formáty ze zdrojových dat. U většiny typů datových sad, jako je SQL a Parquet, je projekce ve zdroji pevná, aby odrážela schéma definované v datové sadě. Pokud zdrojové soubory nejsou silného typu (například ploché soubory CSV namísto souborů Parquet), můžete definovat datové typy pro každé pole ve zdrojové transformaci.

![Nastavení na kartě projekce](media/data-flow/source3.png "Projekce")

Pokud textový soubor nemá žádné definované schéma, vyberte možnost **detekovat datový typ** , aby data Factory vzorkovat a odvodit datové typy. Pro automatické rozpoznání výchozích formátů dat vyberte možnost **definovat výchozí formát** . 

Můžete upravit typy dat sloupce v transformaci odvozeného sloupce z vedlejšího datového proudu. Použijte transformaci SELECT k úpravě názvů sloupců.

### <a name="import-schema"></a>Importovat schéma

Datové sady, jako jsou Avro a CosmosDB, které podporují komplexní datové struktury, nevyžadují, aby v datové sadě existovaly definice schématu. Proto budete moci kliknout na tlačítko "importovat schéma" na kartu projekce pro tyto typy zdrojů.

## <a name="cosmosdb-specific-settings"></a>Konkrétní nastavení CosmosDB

Při použití CosmosDB jako typu zdroje existuje několik možností, které je třeba vzít v úvahu:

* Zahrnout systémové sloupce: Pokud zaškrtnete toto, ```id```, ```_ts```a další systémové sloupce budou zahrnuty do metadat toku dat z CosmosDB. Při aktualizaci kolekcí je důležité zahrnout to, abyste mohli existující ID řádku vzít.
* Velikost stránky: počet dokumentů na stránku výsledku dotazu. Výchozí hodnota je-1, která dynamickou stránku služby používá až 1000.
* Propustnost: nastavte volitelnou hodnotu pro počet ru, které chcete použít pro kolekci CosmosDB pro každé spuštění tohoto toku dat během operace čtení. Minimum je 400.
* Preferované oblasti: můžete zvolit preferované oblasti čtení pro tento proces.

## <a name="optimize-the-source-transformation"></a>Optimalizace zdrojové transformace

Na kartě **optimalizace** pro transformaci zdroje se může zobrazit typ **zdrojového** oddílu. Tato možnost je dostupná jenom v případě, že je váš zdroj Azure SQL Database. Důvodem je to, že Data Factory se snaží vytvořit propojení paralelně, aby se spouštěly velké dotazy proti vašemu zdroji SQL Database.

![Nastavení zdrojového oddílu](media/data-flow/sourcepart3.png "Dělení")

Nemusíte rozdělit data na zdroj SQL Database, ale oddíly jsou užitečné pro velké dotazy. Oddíl můžete založit na sloupci nebo dotazu.

### <a name="use-a-column-to-partition-data"></a>Použití sloupce k dělení dat

Ve zdrojové tabulce vyberte sloupec, na kterém chcete vytvořit oddíly. Nastavte také počet oddílů.

### <a name="use-a-query-to-partition-data"></a>Použití dotazu k dělení dat

Můžete se rozhodnout rozdělit připojení na základě dotazu. Zadejte obsah predikátu WHERE. Zadejte například rok > 1980.

Další informace o optimalizaci v rámci mapování toku dat najdete na [kartě optimalizace](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Další kroky

Začněte sestavovat [transformaci odvozeného sloupce](data-flow-derived-column.md) a [transformaci Select](data-flow-select.md).
