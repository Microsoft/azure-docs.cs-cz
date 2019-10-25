---
title: Nastavte transformaci jímky v funkci toku dat mapování Azure Data Factory
description: Přečtěte si, jak nastavit transformaci jímky v toku dat mapování.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: da8dc332794cadc0eb6677390c566e67a6df6f3f
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882445"
---
# <a name="sink-transformation-for-a-data-flow"></a>Transformace jímky pro tok dat



Po transformaci toku dat můžete data zpracovat do cílové datové sady. V transformaci jímky vyberte definici datové sady pro cílová výstupní data. V případě, že datový tok vyžaduje, můžete mít tolik transformací jímky.

Aby se zohlednila změna schématu a změny v příchozích datech, zajímky výstupních dat do složky bez definovaného schématu ve výstupní datové sadě. V případě změn sloupců ve vašich zdrojích můžete také vybrat možnost u zdroje použít **posun schématu** . Pak automap všechna pole v jímky.

![Možnosti na kartě jímka, včetně možnosti automatické mapování](media/data-flow/sink1.png "jímka 1")

Chcete-li zpracovat všechna příchozí pole, zapněte **automatickou mapu**. Chcete-li vybrat pole, která mají být zajímka do cíle, nebo změnit názvy polí v cíli, vypněte **automatickou mapu**. Pak otevřete kartu **mapování** a namapujte výstupní pole.

![Možnosti na kartě mapování](media/data-flow/sink2.png "jímka 2")

## <a name="output"></a>Výstup 
Pro úložiště objektů BLOB v Azure nebo Data Lake Storage typy jímky výstup transformovaná data do složky. Spark generuje výstupní datové soubory rozdělené do oddílů na základě schématu dělení, které používá transformace jímky. 

Schéma dělení můžete nastavit na kartě **optimalizace** . Pokud chcete, Data Factory sloučit výstup do jednoho souboru, vyberte **jeden oddíl**.

![Možnosti na kartě optimalizace](media/data-flow/opt001.png "možnosti jímky")

## <a name="field-mapping"></a>Mapování polí
Na kartě **mapování** transformace jímky můžete namapovat příchozí sloupce vlevo na cílové umístění na pravé straně. Když datovou jímku zařadíte do souborů, Data Factory bude vždy zapisovat nové soubory do složky. Při mapování na datovou sadu databáze zvolíte možnosti operace databázové tabulky pro vložení, aktualizaci, Upsert nebo odstranění.

![Karta mapování](media/data-flow/sink2.png "Jímky")

V tabulce mapování můžete vícenásobný výběr propojit více sloupců, zrušit propojení více sloupců nebo mapovat více řádků na stejný název sloupce.

Chcete-li vždy mapovat příchozí sadu polí na cíl, protože jsou a plně akceptovat flexibilní definice schématu, vyberte možnost **Povolení posunu schématu**.

![Karta mapování zobrazující pole mapovaná na sloupce v datové sadě](media/data-flow/multi1.png "více možností")

Chcete-li obnovit mapování sloupců, vyberte možnost **znovu mapovat**.

![Karta jímka](media/data-flow/sink1.png "Jímka jedna")

Pokud se změní schéma, vyberte možnost **ověřit schéma** a selhání jímky.

Chcete-li zkrátit obsah složky jímky před zápisem cílových souborů do této cílové složky, vyberte možnost **zrušit zaškrtnutí složky** .

## <a name="fixed-mapping-vs-rule-based-mapping"></a>Pevné mapování vs. mapování na základě pravidel
Pokud automatické mapování vypnete, budete mít možnost Přidat mapování na základě sloupců (pevné mapování) nebo mapování na základě pravidel. Mapování na základě pravidel vám umožní zapisovat výrazy s porovnáváním vzorů, zatímco pevné mapování bude mapovat logické a fyzické názvy sloupců.

![Mapování na základě pravidel](media/data-flow/rules4.png "Mapování na základě pravidel")

Když zvolíte mapování na základě pravidel, budete mít k stránce ADF možnost vyhodnotit odpovídající výraz tak, aby odpovídala pravidlům příchozího vzoru, a definovat názvy odchozích polí. Můžete přidat libovolnou kombinaci polí i mapování na základě pravidel. Názvy polí se pak generují za běhu pomocí ADF na základě příchozích metadat ze zdroje. Můžete zobrazit názvy generovaných polí během ladění a pomocí podokna náhledu dat.

Podrobnosti o porovnávání vzorů jsou v [dokumentaci ke vzorci sloupců](concepts-data-flow-column-pattern.md).

Můžete také zadat vzory regulárních výrazů při použití shody založené na pravidlech rozbalením řádku a zadáním regulárního výrazu vedle položky "shoda názvů:".

![Mapování regulárního výrazu](media/data-flow/scdt1g4.png "Mapování regulárního výrazu")

Velmi základní běžný příklad pro mapování na základě pravidel a pevné mapování je případ, kdy chcete mapovat všechna příchozí pole na stejný název v cíli. V případě pevných mapování byste měli v tabulce uvést každý jednotlivý sloupec. Pro mapování na základě pravidel byste měli mít jedno pravidlo, které mapuje všechna pole pomocí ```true()``` na stejný název pole, který je reprezentován ```$$```.

### <a name="sink-association-with-dataset"></a>Přidružení jímky s datovou sadou

Datová sada, kterou vyberete pro jímku, může nebo nemusí mít schéma definované v definici datové sady. Pokud nemá definované schéma, je nutné zakázat posun schématu. Pokud jste definovali pevné mapování, mapování logických na fyzického názvu bude uchováno v transformaci jímky. Pokud změníte definici schématu pro datovou sadu, pak budete pravděpodobně přerušit mapování jímky. Pokud se tomu chcete vyhnout, použijte mapování na základě pravidel. Mapování na základě pravidel jsou zobecněna, což znamená, že změny schématu ve vaší datové sadě nebudou přerušit mapování.

## <a name="file-name-options"></a>Možnosti názvu souboru

Nastavit pojmenování souborů: 

   * **Výchozí**: umožňuje Sparku pojmenovat soubory založené na výchozím nastavení části.
   * **Vzor**: zadejte vzor pro výstupní soubory. Například **půjčky [n]** vytvoří loans1. csv, loans2. csv a tak dále.
   * **Na oddíl**: zadejte jeden název souboru na oddíl.
   * **Jako data ve sloupci**: Nastavte výstupní soubor na hodnotu sloupce.
   * **Výstup do jednoho souboru**: pomocí této možnosti bude ADF spojovat výstupní soubory rozdělené do jednoho pojmenovaného souboru. Chcete-li použít tuto možnost, musí být datová sada přeložena na název složky. Všimněte si také, že tato operace sloučení může být na základě velikosti uzlu neúspěšná.

> [!NOTE]
> Operace se soubory zahájí pouze v případě, že jste spustili aktivitu spustit tok dat. Nespustí se v režimu ladění toku dat.

## <a name="database-options"></a>Možnosti databáze

Vyberte nastavení databáze:

![Karta nastavení zobrazující možnosti jímky SQL](media/data-flow/alter-row2.png "Možnosti SQL")

* **Metoda aktualizace**: ve výchozím nastavení se povoluje vkládání. Pokud chcete zastavit vkládání nových řádků ze zdroje, zrušte zaškrtnutí políčka **povolí vložení** . Chcete-li aktualizovat, Upsert nebo odstranit řádky, přidejte nejprve transformaci ALTER-Row k označení řádků pro tyto akce. 
* **Znovu vytvořit tabulku**: před dokončením toku dat vyřaďte nebo vytvořte cílovou tabulku.
* **Zkrátit tabulku**: před dokončením toku dat odeberte všechny řádky z cílové tabulky.
* **Velikost dávky**: zadejte číslo pro zablokování zápisů do bloků dat. Tato možnost slouží k načítání velkých objemů dat. 
* **Povolit přípravu**: při načítání datového skladu Azure jako datové sady jímky použijte základnu.
* **Skripty před a po SQL**: zadejte víceřádkové skripty SQL, které se spustí před (před zpracováním) a po (po zpracování) se zapisují do databáze jímky.

![skripty pro zpracování před a po SQL](media/data-flow/prepost1.png "Skripty pro zpracování SQL")

> [!NOTE]
> V toku dat můžete Data Factory přímo vytvořit novou definici tabulky v cílové databázi. Chcete-li vytvořit definici tabulky, nastavte datovou sadu v transformaci jímky, která má název nové tabulky. V datové sadě SQL pod názvem tabulky vyberte **Upravit** a zadejte nový název tabulky. Potom v transformaci jímky zapněte možnost **Povolit posun schématu**. Nastavte **schéma pro import** na **none**.

![Nastavení datové sady SQL, kde se zobrazuje, kde upravit název tabulky](media/data-flow/dataset2.png "Schéma SQL")

> [!NOTE]
> Když aktualizujete nebo odstraníte řádky v jímky databáze, musíte nastavit klíčový sloupec. Toto nastavení umožňuje transformaci ALTER-Row určit jedinečný řádek v knihovně pro přesun dat (DML).

## <a name="next-steps"></a>Další kroky
Teď, když jste vytvořili tok dat, přidejte [do svého kanálu aktivitu toku dat](concepts-data-flow-overview.md).
