---
title: Nastavení jímky transformace ve funkci mapování toku dat služby Azure Data Factory
description: Další informace o nastavení jímky transformace v mapování toku dat.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 4341cbb0e24330d535f5211c088f0068eab33af7
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596261"
---
# <a name="sink-transformation-for-a-data-flow"></a>Transformace toku dat jímky

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Po transformaci váš tok dat můžete jímky dat do cílové datové sadě. V transformaci jímky zvolte definici datové sady pro určení výstupní data. Může mít jak mnoho jímky transformací, jak váš tok dat vyžaduje.

K účtu pro odchylek schématu a změny v příchozích datech do složky bez definované schéma v výstupní datovou sadu jímky výstupní data. Můžete také berete v úvahu pro změny ve sloupcích v zdrojů tak, že vyberete **povolit schématu odchylek** ve zdroji. Potom automaticky mapovat všechna pole v jímce.

![Možnosti na kartě jímky, včetně možnosti automatického mapy](media/data-flow/sink1.png "jímky 1")

Jímka všechna příchozí pole, zapněte **automaticky mapy**. Chcete-li zvolit pole do jímky do cíle, nebo chcete-li změnit názvy polí v cílovém umístění, vypněte **automaticky mapy**. Otevřete **mapování** kartu pro výstupní pole mapování.

![Možnosti na kartě mapování](media/data-flow/sink2.png "jímky 2")

## <a name="output"></a>Výstup 
Pro úložiště objektů Blob v Azure nebo Data Lake Storage jímky typy výstupu Transformovaná data do složky. Spark generuje dělené výstupní soubory dat založené na schéma rozdělení oddílů, který používá transformace jímky. 

Můžete nastavit schéma rozdělení oddílů z **optimalizace** kartu. Pokud chcete datovou továrnu sloučit výstup do jednoho souboru, vyberte **jeden oddíl**.

![Možnosti na kartě Optimalizace](media/data-flow/opt001.png "jímky možnosti")

## <a name="field-mapping"></a>Mapování polí

Na **mapování** kartu transformace vaší jímky můžete namapovat příchozí sloupce na levé straně cíle na pravé straně. Když jímky datové toky pro soubory, Data Factory vždy psát nové soubory do složky. Při namapování na datové sady databáze můžete vygenerovat novou tabulku, která používá toto schéma tak, že nastavíte **Uložit zásadu** k **přepsat**. Nebo vkládání nových řádků v existující tabulce a poté mapování polí do existující schéma. 

![Na kartě mapování](media/data-flow/sink2.png "jímky")

V mapování tabulky je možné vícenásobný výběr propojení více sloupců, zrušit odkaz více sloupců nebo mapovat více řádků na stejný název sloupce.

Vždy mapovat příchozí sada polí na cíl, jak jsou a tak, aby přijímal plně flexibilní schéma definice, vyberte **povolit schématu odchylek**.

![Na kartě mapování zobrazí pole, které jsou mapované na sloupce v datové sadě](media/data-flow/multi1.png "více možností")

Chcete-li obnovit mapování sloupce, vyberte **přemapovat**.

![Karta jímka](media/data-flow/sink1.png "jímky jeden")

Vyberte **ověřením schématu** jímka selže, pokud se změní schéma.

Vyberte **vymazat složce** došlo ke zkrácení obsah složky jímky před zápisem cílové soubory v této cílové složky.

## <a name="file-name-options"></a>Možnosti názvu souboru

Nastavte pojmenovávání souborů: 

   * **Výchozí**: Spark umožňuje soubory název založeném na výchozím nastavení součástí.
   * **Vzor**: Zadejte vzor pro výstupní soubory. Například **půjček [n]** vytvoří loans1.csv loans2.csv a tak dále.
   * **Na oddíl**: Zadejte jeden název souboru na oddíl.
   * **Jako data ve sloupci**: Výstupní soubor nastavena na hodnotu sloupce.
   * **Výstup do jednoho souboru**: Pomocí této možnosti bude ADF kombinovat dělené výstupní soubory do jednoho souboru s názvem. Tato možnost dala použít, vaše datová sada musí se překládat na název složky. Navíc Upozorňujeme, že tuto operaci sloučení může selhat může být založen na velikost uzlu.

> [!NOTE]
> Pouze v případě, že máte spuštěnou aktivitu spuštění toku dat souboru počáteční operacích. Nejsou spuštění v režimu datového toku ladění.

## <a name="database-options"></a>Možnosti databáze

Vyberte nastavení databáze:

* **Metoda aktualizace**: Výchozí hodnota je umožňující vložení. Vymazat **povolit vložit** Pokud budete chtít zastavit, vložením nových řádků ze zdroje. Aktualizovat upsert, nebo odstranit řádky, nejprve přidáte transformaci alter řádků pro řádky značky pro tyto akce. 
* **Znovu vytvořte tabulku**: Vyřaďte nebo vytvoření cílové tabulky před toku dat dokončí.
* **Truncate table**: Předtím, než se dokončí toku dat, odeberte všechny řádky z cílové tabulce.
* **Velikost dávky**: Zadejte číslo do kbelíku zápisy do bloků. Tuto možnost použijte pro načítání velkých objemů dat. 
* **Zapnout pracovní režim**: Při načítání Azure Data Warehouse jako datové sady jímky pomocí PolyBase.

![Na kartě nastavení zobrazující možnosti SQL jímky](media/data-flow/alter-row2.png "možnosti SQL")

> [!NOTE]
> V toku dat může směrovat Data Factory k vytvoření nové definice tabulky v cílové databázi. Pokud chcete vytvořit definici tabulky, nastavte datové sady v jímky transformace, která má nový název tabulky. V sadě dat SQL, pod názvem tabulky vyberte **upravit** a zadejte nový název tabulky. Potom v transformaci jímky, zapněte **povolit schématu odchylek**. Nastavte **importovat schéma** k **žádný**.

![Nastavení datové sady SQL, znázorňující, kde můžete upravit název tabulky](media/data-flow/dataset2.png "SQL schématu")

> [!NOTE]
> Při aktualizaci nebo odstranění řádků v databázi pro jímku, je nutné nastavit klíčový sloupec. Toto nastavení umožňuje alter řádek transformace k určení jedinečném řádku v knihovna pro přesun dat (DML).

## <a name="next-steps"></a>Další postup

Teď, když jste vytvořili tok dat, přidejte [aktivitu toku dat do kanálu](concepts-data-flow-overview.md).
