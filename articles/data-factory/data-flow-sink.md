---
title: Azure Data Factory mapování transformace jímky toku dat
description: Azure Data Factory mapování transformace jímky toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a56f391aa76bd1216fd51d516adb836a2093bcba
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371135"
---
# <a name="mapping-data-flow-sink-transformation"></a>Mapování datového toku jímky transformace

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Jímka možnosti](media/data-flow/windows1.png "jímky 1")

Po dokončení vaší transformace toku dat můžete jímky Transformovaná data do cílové datové sadě. V transformaci jímky můžete definici datové sady, kterou chcete použít pro určení výstupní data. Můžete mít libovolný počet jímky transformace jako váš tok dat vyžaduje.

Běžnou praxí pro změnu příchozích dat a aby se zohlednily odchylek schématu je do složky bez definované schéma v výstupní datovou sadu jímky výstupní data. Můžete také započítat i všechny změny ve sloupcích ve zdrojích tak, že vyberete "Povolit schématu odchylek" ve zdroji i automaticky mapovat všechna pole v jímce.

Můžete přepsat, připojte nebo selhání toku dat při vnořování do datové sady.

Můžete také "automaticky mapovat" do jímky všechna příchozí pole. Pokud chcete zvolit pole, které chcete do jímky do cíle, nebo pokud chcete změnit názvy polí v cílovém umístění, zvolte "Off" pro "automaticky mapovat" a potom klikněte na kartu mapování a mapování polí výstup:

![Jímka možnosti](media/data-flow/sink2.png "jímky 2")

## <a name="output-to-one-file"></a>Výstup do jednoho souboru
Pro Data Lake nebo Azure Storage Blob jímky typy bude výstup Transformovaná data do složky. Spark se vygenerují dělené výstupní soubory data podle schématu dělení se používají v transformace jímky. Kliknutím na kartu "Optimalizace" můžete nastavit schéma rozdělení oddílů. Pokud byste o ni ADF sloučit výstup do jednoho souboru, klikněte na přepínací tlačítko "Jeden oddíl".

![Jímka možnosti](media/data-flow/opt001.png "jímky možnosti")

## <a name="field-mapping"></a>Mapování polí

Na kartě mapování vaší jímky transformace můžete mapování sloupců příchozí (levá strana) na cílovém umístění (pravá strana). Když jímky toků dat k souborům, ADF vždy psát nové soubory do složky. Při připojování k datové sadě databáze, můžete buď vygenerovat novou tabulku s toto schéma (nastavená na "přepsat" uložení zásad) nebo nové řádky vložit do existující tabulky a mapování polí do existující schéma.

Vícenásobný výběr v tabulce mapování můžete propojit více sloupců s jedním kliknutím, zrušit odkaz více sloupců nebo mapovat více řádků na stejný název sloupce.

Když chcete vždy přijmout příchozí sada polí a jejich namapování na cíl jako-nastavena, nastavení "Povolit schématu odchylek".

![Mapování polí](media/data-flow/multi1.png "více možností")

Pokud chcete obnovit mapování sloupců, stiskněte tlačítko "Přemapování" Chcete-li obnovit mapování.

![Jímka možnosti](media/data-flow/sink1.png "jímky jeden")

![Jímka možnosti](media/data-flow/sink2.png "jímky")

* Povolit odchylek schématu a ověření schématu možnosti jsou teď dostupné v jímky. To vám umožní dát pokyn ADF plně přijmout flexibilní schéma definice (schéma odchylek) nebo nezdaří jímka v případě změny schématu (schéma ověření).

* Vymažte složce. ADF bude obsah složky jímky zkrátit před zápisem cílové soubory v této cílové složky.

## <a name="file-name-options"></a>Možnosti názvu souboru

   * Výchozí: Spark umožňuje soubory název založeném na výchozím nastavení součástí
   * Vzor: Zadejte vzor pro výstupní soubory. Například "půjček [n]" vytvoří loans1.csv, loans2.csv,...
   * Na oddíl: Zadejte název souboru na oddíl
   * Jako data ve sloupci: Nastavte na hodnotu sloupce výstupního souboru

> [!NOTE]
> Operace se soubory se spustí, jenom Pokud spouštíte aktivitu spuštění toku dat, není v režimu tok dat ladění

## <a name="database-options"></a>Možnosti databáze

* Povolte insert, update, delete, upsertuje. Výchozí hodnota je umožňující vložení. Pokud chcete aktualizovat, upsert nebo odstranit řádky, musí nejprve přidat transformaci alter řádek do řádků značky pro tyto konkrétní akce. Vypnutí "Povolit Vložit" zastaví ADF vložením nových řádků ze zdroje.
* Truncate table (odebere všechny řádky z cílové tabulce před dokončením toku dat)
* Vytvořte tabulku (provádí rozevírací/vytvoření cílové tabulky před dokončením toku dat)
* Velikost dávky pro načítání velkých objemů dat Zadejte číslo do kbelíku zápisy do bloků dat
* Zapnout pracovní režim: To bude dát pokyn ADF pomocí Polybase při načítání Azure Data Warehouse jako datové sady jímky

> [!NOTE]
> V toku dat můžete požádat o ADF můžete vytvořit novou definici tabulky v cílové databázi nastavením datovou sadu jímky transformace, která má nový název tabulky. V sadě dat SQL klikněte na tlačítko "Upravit" pod název tabulky a zadejte nový název tabulky. Potom v transformaci jímky zapněte "Povolit schématu odchylek". Seth "Importovat schéma" nastavení na hodnotu None.

![Transformace schématu zdroje](media/data-flow/dataset2.png "SQL schématu")

![SQL jímky možnosti](media/data-flow/alter-row2.png "možnosti SQL")

> [!NOTE]
> Při aktualizaci nebo odstraňování řádků v databázi pro jímku, je nutné nastavit klíčový sloupec. Tímto způsobem Alter řádek je schopna určit jedinečném řádku v DML.

## <a name="next-steps"></a>Další postup

Teď, když jste vytvořili tok dat, přidejte [aktivitu spuštění toku dat do kanálu](concepts-data-flow-overview.md).
