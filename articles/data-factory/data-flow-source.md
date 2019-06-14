---
title: Nastavit zdroj transformace ve funkci mapování toku dat služby Azure Data Factory
description: Zjistěte, jak nastavit zdroj transformace v mapování se předávají Data.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 5d8a339049ebda02c2fe470c5d8dc2c743d547ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077238"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformace zdroje pro mapování toku dat 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformace zdrojového nakonfiguruje zdroje dat pro datový tok. Tok dat může obsahovat více než jeden zdroj transformace. Při návrhu datové toky, vždy začínají znakem transformace zdroje.

Každý tok dat vyžaduje alespoň jeden zdroj transformace. Podle potřeby k dokončení transformace dat, přidejte libovolný počet zdrojů. Můžete připojit k těmto zdrojům společně s připojení k transformaci nebo union transformace.

> [!NOTE]
> Při ladění váš tok dat je pro čtení data ze zdroje pomocí nastavení vzorkování nebo omezení ladění zdroje. Zapsat data do jímky, je nutné spustit váš tok dat z kanálu toku dat aktivit. 

![Možnosti transformace na kartě Nastavení zdroje zdroje](media/data-flow/source.png "zdroje")

Váš tok dat transformace zdroje přidružte přesně jednu datovou sadu služby Data Factory. Datová sada definuje tvar a umístění dat, které chcete zapisovat nebo číst z. Zástupné znaky a soubor seznamy můžete použít ve zdroji pro práci s více než jeden soubor současně.

## <a name="data-flow-staging-areas"></a>Data toku pracovní oblasti

Tok dat funguje s *pracovní* datových sad, které jsou v Azure. Pro přípravu, když jste transformace vašich dat použijte tyto datové sady. 

Data Factory má přístup k téměř 80 nativní konektory. Pokud chcete zahrnout data z těchto zdrojů ve svém toku dat, připravit data v jednom z pracovní oblasti datového toku datovou sadu pomocí nástroje aktivitu kopírování.

## <a name="options"></a>Možnosti

Výběr možností schématu a vzorkování pro vaše data.

### <a name="allow-schema-drift"></a>Povolit schématu odchylek
Vyberte **povolit schématu odchylek** Pokud zdrojové sloupce se změní často. Toto nastavení povolí všechna příchozí zdrojového pole tok prostřednictvím transformací pro jímku.

### <a name="validate-schema"></a>Ověření schématu

Pokud příchozí verze zdroje dat neodpovídá definované schéma, tok dat se nepodaří spustit.

![Nastavení veřejného zdroje, zobrazující možnosti pro ověřením schématu, povolit schématu odchylek a vzorkování](media/data-flow/source1.png "veřejný zdroj 1")

### <a name="sample-the-data"></a>Ukázková data
Povolit **vzorkování** omezit počet řádků ze zdroje. Toto nastavení použijte při testování nebo ukázková data ze zdroje pro účely ladění.

## <a name="define-schema"></a>Definovat schéma

Pokud zdrojové soubory nejsou silného typu (pro příklad, plochých souborů a nikoli soubory Parquet), definování typů dat pro každé pole tady v transformaci zdroje.  

![Transformace nastavení na kartě definovat schéma zdroje](media/data-flow/source2.png "zdroje 2")

Později můžete změnit názvy sloupců v vyberte transformaci. Ke změně datové typy, které použijte odvozené sloupce transformace. Pro silného typu zdroje můžete upravit datových typů v později vyberte transformace. 

![Datové typy v vyberte transformaci](media/data-flow/source003.png "datové typy")

### <a name="optimize-the-source-transformation"></a>Optimalizace transformace zdroje

Na **optimalizace** kartu pro transformaci zdroj, může se zobrazit **zdroj** oddílu typu. Tato možnost je dostupná jenom v případě zdroje je Azure SQL Database. Je to proto, že Data Factory se pokusí navázat připojení paralelní spouštění velkých dotazů na databázi SQL zdroje.

![Nastavení oddílu zdroje](media/data-flow/sourcepart3.png "dělení")

Nemáte k dělení dat v databázi SQL zdroje, ale jsou užitečné pro dotazy na velkých oddílů. Oddíl můžete založit na sloupec nebo dotazu.

### <a name="use-a-column-to-partition-data"></a>Použít sloupec k dělení dat

Ze zdrojové tabulky vyberte na sloupec, který oddíl. Nastavte také počet oddílů.

### <a name="use-a-query-to-partition-data"></a>Použijte dotaz k dělení dat

Můžete při vytváření oddílů připojení na základě dotazu. Stačí zadáte obsah predikát WHERE. Zadejte například roku > 1980.

## <a name="source-file-management"></a>Správa zdrojového souboru

Při volbě nastavení pro správu souborů ve zdroji. 

![Nová nastavení zdroje](media/data-flow/source2.png "nové nastavení")

* **Cesta se zástupným znakem**: Ze zdrojové složky zvolte řadu soubory, které odpovídají vzoru. Toto nastavení potlačí všechny soubory v definici datové sady.
* **Seznam souborů**: Toto je sada souborů. Vytvořte textový soubor, který obsahuje seznam souborů relativní cestu ke zpracování. Přejděte na tento textový soubor.
* **Sloupce pro uložení názvu souboru**: Název zdrojového souboru Store ve sloupci ve vašich datech. Zadejte nový název pro uložení řetězce názvu souboru.
* **Po dokončení**: Zvolte po data spouštění toků, odstranění souboru se zdrojovým nebo přesunutí zdrojového souboru se zdrojovým souborem neprovede žádnou akci. Jsou relativní cesty pro přesunutí.

### <a name="sql-datasets"></a>Datové sady SQL

Pokud je zdrojem v SQL Database nebo SQL Data Warehouse, máte další možnosti pro správu zdrojového souboru.

* **Dotaz:** Zadejte dotaz SQL pro zdroj. Toto nastavení potlačí všechny tabulky, kterou jste zvolili v datové sadě. Všimněte si, že **klauzule Order By** klauzule zde nejsou podporovány, ale můžete nastavit úplný příkaz SELECT FROM. Můžete také použít funkce uživatelem definovaná tabulka. **Vybrat * z udfGetData()** je UDF v SQL, která vrací tabulku. Tento dotaz vytvoří zdrojové tabulky, který používáte ve svém toku data.
* **Velikost dávky**: Zadejte velikost dávky k bloku dat velkých objemů dat do operace čtení.

> [!NOTE]
> Operace se soubory spustit pouze v případě, že spuštění toku dat z kanálu (ladění kanálu nebo spustit provádění), která používá aktivitu spuštění toku dat v rámci kanálu. Operace se soubory *nejsou* spuštění v režimu ladění se předávají Data.

### <a name="projection"></a>Projekce

Jako jsou schémata v datových sadách projekce ve zdroji definuje sloupce dat, typy a formáty ze zdrojových dat. 

![Nastavení na kartě projekce](media/data-flow/source3.png "projekce")

Pokud textový soubor nemá definované schéma, vyberte **rozpoznat datový typ** tak, aby se Data Factory ukázkový a odvodit datové typy. Vyberte **definovat výchozí formát** rozpozná výchozí data formátů. 

Datové typy sloupce v odvozených sloupců transformaci dále můžete upravit. Vyberte transformace použijte k úpravě názvů sloupců.

![Nastavení pro výchozí datových formátů](media/data-flow/source2.png "výchozí formáty")

## <a name="next-steps"></a>Další postup

Zahájení sestavování [odvozené sloupce transformace](data-flow-derived-column.md) a [vyberte transformace](data-flow-select.md).
