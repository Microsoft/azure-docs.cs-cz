---
title: Azure Data Factory mapování toku dat, vyberte transformaci
description: Azure Data Factory mapování toku dat, vyberte transformaci
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 289f98fdc2f39449cdeede9ee46fb39847ae2cb5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029267"
---
# <a name="mapping-data-flow-select-transformation"></a>Mapování toku dat pro výběr transformace


Tuto transformaci použijte pro selektivitu sloupce (zmenšení počtu sloupců), sloupce aliasů a názvy datových proudů a změnu pořadí sloupců.

## <a name="how-to-use-select-transformation"></a>Jak použít transformaci Select
Možnost vybrat Transformer umožňuje aliasovat celý datový proud nebo sloupce v tomto datovém proudu, přiřazovat jiné názvy (aliasy) a následně na tyto nové názvy odkazovat později v toku dat. Tato transformace je užitečná pro scénáře automatického spojení. Způsob implementace automatického spojení v toku dat ADF je použít datový proud, vytvořit větvení s "novou větví" a hned následně přidat transformaci "SELECT". Tento datový proud teď bude mít nový název, který můžete použít pro připojení zpátky k původnímu streamu, a vytvořit tak nové připojení:

![Samoobslužné](media/data-flow/selfjoin.png "spojení")

Ve výše uvedeném diagramu je transformace SELECT v horní části. Tím se aliasuje původní datový proud na "OrigSourceBatting". Ve zvýrazněné transformaci spojení pod ním vidíte, že používáme tento datový proud aliasu jako připojení na pravé straně, což nám umožňuje odkazovat na stejný klíč v levém & pravé straně vnitřního spojení.

Možnost vybrat se dá použít taky jako způsob, jak vybrat sloupce z toku dat. Například pokud máte v jímky definované 6 sloupců, ale chcete pouze vybrat konkrétní 3 pro transformaci a následně tok do jímky, můžete vybrat pouze ty 3 pomocí transformace SELECT.

![Vybrat transformaci]–(media/data-flow/newselect1.png "Vybrat alias")

## <a name="options"></a>Možnosti
* Výchozím nastavením pro možnost vybrat je zahrnout všechny příchozí sloupce a zachovat tyto původní názvy. Datový proud můžete aliasovat tak, že nastavíte název transformace SELECT.
* Pokud chcete jednotlivé sloupce aliasovat, zrušte výběr možnosti Vybrat vše a v dolní části použijte mapování sloupce.
* Vyberte Přeskočit duplicity, aby se vyloučily duplicitní sloupce ze vstupních nebo výstupních metadat.

![Přeskočit duplicity](media/data-flow/select-skip-dup.png "Přeskočit duplicity")

* Pokud se rozhodnete přeskočit duplicity, výsledky se zobrazí na kartě zkontrolovat. ADF zachová první výskyt sloupce a uvidíte, že se z vašeho toku odebraly všechny další výskyty stejného sloupce.

> [!NOTE]
> Pravidla mapování vymažete stisknutím tlačítka **obnovit** .

## <a name="mapping"></a>Připojení
Ve výchozím nastavení bude transformace výběru automaticky namapována na všechny sloupce, které budou předávat všechny příchozí sloupce se stejným názvem ve výstupu. Název výstupního datového proudu, který je nastaven v nastavení výběr, bude definovat nový název aliasu pro datový proud. Pokud zachováte sadu výběrů pro automatické mapování, pak můžete celý datový proud vytvořit alias se všemi sloupci.

![Vybrat](media/data-flow/rule2.png "mapování na základě pravidel pro") transformaci

Pokud chcete, aby byly sloupce aliasy, odebrány, přejmenovány nebo přeobjednány, musíte nejprve přepnout na automatické mapování. Ve výchozím nastavení se zobrazí výchozí pravidlo, které se označuje jako "všechny vstupní sloupce". Toto pravidlo můžete ponechat v takovém případě, pokud máte v úmyslu vždy, aby se všechny příchozí sloupce namapovaly na stejný název na svém výstupu.

Pokud ale chcete přidat vlastní pravidla, klikněte na Přidat mapování. Mapování polí vám poskytne seznam příchozích a odchozích názvů sloupců k mapování a aliasu. Pokud chcete vytvořit pravidla porovnávání vzorů, vyberte mapování na základě pravidel.

## <a name="rule-based-mapping"></a>Mapování na základě pravidel
Když zvolíte mapování na základě pravidel, budete mít k stránce ADF možnost vyhodnotit odpovídající výraz tak, aby odpovídala pravidlům příchozího vzoru, a definovat názvy odchozích polí. Můžete přidat libovolnou kombinaci polí i mapování na základě pravidel. Názvy polí se pak generují za běhu pomocí ADF na základě příchozích metadat ze zdroje. Můžete zobrazit názvy generovaných polí během ladění a pomocí podokna náhledu dat.

Další podrobnosti o porovnávání vzorů jsou k dispozici v [dokumentaci ke vzorci sloupců](concepts-data-flow-column-pattern.md).

## <a name="next-steps"></a>Další kroky
* Po použití příkazu vybrat k přejmenování, změně pořadí a aliasu použijte [transformaci jímky](data-flow-sink.md) k vytvoření plochy dat do úložiště dat.
