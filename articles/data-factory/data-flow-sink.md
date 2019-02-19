---
title: Azure Data Factory mapování transformace jímky toku dat
description: Azure Data Factory mapování transformace jímky toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 381dc2f9f6d3a074af00ba047472719c086f5811
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408404"
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

### <a name="output-settings"></a>Nastavení výstupu

Přepsání vymazat data z tabulky, pokud existuje, znovu ji vytvořte a nahrajte data. Připojte se nové řádky vložit. Pokud tabulky z tabulky název datové sady v cíli rozpoznáno ADW neexistuje vůbec, toku dat zobrazí vytvořit tabulku a načíst data.

Pokud zrušíte zaškrtnutí políčka "Automatické mapy", můžete ho namapovat pole cílové tabulky ručně.

![Jímka rozpoznáno ADW možnosti](media/data-flow/adw2.png "rozpoznáno adw jímky")

#### <a name="field-mapping"></a>Mapování polí

Na kartě mapování vaší jímky transformace můžete mapování sloupců příchozí (levá strana) na cílovém umístění (pravá strana). Když jímky toků dat k souborům, ADF vždy psát nové soubory do složky. Při připojování k datové sadě databáze, můžete buď vygenerovat novou tabulku s toto schéma (nastavená na "přepsat" uložení zásad) nebo nové řádky vložit do existující tabulky a mapování polí do existující schéma.

Vícenásobný výběr v tabulce mapování můžete propojit více sloupců s jedním kliknutím, zrušit odkaz více sloupců nebo mapovat více řádků na stejný název sloupce.

![Mapování polí](media/data-flow/multi1.png "více možností")

Pokud chcete obnovit mapování sloupců, stiskněte tlačítko "Přemapování" Chcete-li obnovit mapování.

![Připojení](media/data-flow/maxcon.png "připojení")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>Aktualizace do jímky transformaci pro verzi GA ADF V2

![Jímka možnosti](media/data-flow/sink1.png "jímky jeden")

![Jímka možnosti](media/data-flow/sink2.png "jímky")

* Povolit odchylek schématu a ověření schématu možnosti jsou teď dostupné v jímky. To vám umožní dát pokyn ADF plně přijmout flexibilní schéma definice (schéma odchylek) nebo nezdaří jímka v případě změny schématu (schéma ověření).

* Vymažte složce. ADF bude obsah složky jímky zkrátit před zápisem cílové soubory v této cílové složky.

* Možnosti názvu souboru

   * Výchozí: Spark umožňuje soubory název založeném na výchozím nastavení součástí
   * Vzor: Zadejte název výstupních souborů
   * Na oddíl: Zadejte název souboru na oddíl
   * Jako data ve sloupci: Nastavte na hodnotu sloupce výstupního souboru

> [!NOTE]
> Operace se soubory se spustí, jenom Pokud spouštíte aktivitu spuštění toku dat, není v režimu tok dat ladění

Typ jímky SQL můžete nastavit:

* Truncate table
* Vytvořte tabulku (provádí přetažení a vytváření)
* Velikost dávky pro načítání velkých objemů dat Zadejte číslo do kbelíku zápisy do bloků.

![Mapování polí](media/data-flow/sql001.png "možnosti SQL")

## <a name="next-steps"></a>Další postup

Teď, když jste vytvořili tok dat, přidejte [aktivitu spuštění toku dat do kanálu](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview).
