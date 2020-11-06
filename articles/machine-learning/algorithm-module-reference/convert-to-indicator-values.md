---
title: Převod na hodnoty indikátoru
titleSuffix: Azure Machine Learning
description: Použijte modul převést na hodnoty indikátoru v Návrháři Azure Machine Learning k převedení sloupců kategorií na řadu binárních sloupců ukazatelů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 81b3c113f46428327842c1555fdd1934e9ae8762
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420848"
---
# <a name="convert-to-indicator-values"></a>Převod na hodnoty indikátoru
Tento článek popisuje modul návrháře Azure Machine Learning.

Použijte modul **převést na hodnoty indikátoru** v Návrháři Azure Machine Learning k převedení sloupců, které obsahují hodnoty kategorií, do řady binárních sloupců ukazatelů.  

Tento modul také výstupuje definici transformace použité pro převod na hodnoty indikátoru. Tuto transformaci můžete znovu použít na jiné datové sady, které mají stejné schéma, pomocí modulu [použít transformaci](apply-transformation.md) .

## <a name="how-to-configure-convert-to-indicator-values"></a>Jak nakonfigurovat převod na hodnoty indikátoru

1.  Vyhledejte **hodnoty indikátoru převést na ukazatel** a přetáhněte ji do konceptu kanálu. Tento modul můžete najít v kategorii **transformace dat** .
    > [!NOTE]
    > K označení cílových sloupců jako kategorií můžete použít modul [Upravit metadata](edit-metadata.md) před příkazem **převést na Indiciator Values** .

1. Připojte modul **převést na hodnoty indikátoru** k datové sadě obsahující sloupce, které chcete převést. 

1. Vyberte **Upravit sloupec** a zvolte jeden nebo více kategorií sloupců.

1. Vyberte možnost **přepsat kategorií sloupce** , pokud chcete výstupem **pouze** nových logických sloupců. Tento parametr je standardně vypnutý.
    

    > [!TIP]
    >  Pokud zvolíte možnost přepsání, zdrojový sloupec se ve skutečnosti neodstraní ani nezmění. Místo toho jsou nové sloupce vygenerovány a prezentovány ve výstupní datové sadě a zdrojový sloupec zůstane v pracovním prostoru k dispozici. Pokud potřebujete zobrazit původní data, můžete použít modul [Přidat sloupce](add-columns.md) kdykoli a přidat zdrojový sloupec zpátky do.

1. Odešlete kanál.

## <a name="results"></a>Výsledky

Předpokládejme, že máte sloupec s výsledky, které určují, jestli má server vysokou, střední nebo nízkou pravděpodobnost selhání.  

| ID serveru | Skóre selhání |
| --------- | ------------- |
| 10301     | Nízká           |
| 10302     | Střední        |
| 10303     | Vysoké          |

Když použijete příkaz **převést na hodnoty indikátoru** , Návrhář převede jeden sloupec popisků na více sloupců, které obsahují logické hodnoty:  

| ID serveru | Skóre selhání – nízká | Skóre selhání – střední | Skóre selhání – vysoká |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Tady je postup, jak převod funguje:  

-   Ve sloupci **skóre selhání** , který popisuje riziko, existují pouze tři možné hodnoty (vysoká, střední a nízká) a žádné chybějící hodnoty. Vytvoří se tak přesně tři nové sloupce.  

-   Nové sloupce indikátoru jsou pojmenovány na základě záhlaví sloupců a hodnot zdrojového sloupce pomocí tohoto modelu: *\<source column>- \<data value>* .  

-   Měl by být 1 v přesně jednom sloupci indikátoru a 0 ve všech ostatních sloupcích indikátorů, protože každý server může mít pouze jedno hodnocení rizika.  

Teď můžete použít tři sloupce indikátoru jako funkce v modelu strojového učení.

Modul vrací dva výstupy:

- **Datová sada výsledků** : datová sada se sloupci převedených hodnot indikátoru. Sloupce, které nejsou vybrány pro čištění, jsou také "předávány".
- **Transformace hodnot ukazatelů** : transformace dat používaná pro převod na hodnoty indikátoru, které se dají uložit do svého pracovního prostoru a později použít na nová data.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Použití operace uložených hodnot ukazatelů na nová data

Pokud potřebujete opakovat operace hodnot ukazatelů často, můžete své kroky manipulace s daty Uložit jako *transformaci* a znovu ji použít se stejnou datovou sadou. To je užitečné v případě, že je nutné často znovu importovat a vyčistit data, která mají stejné schéma.

1. Přidejte do svého kanálu modul [použít transformaci](apply-transformation.md) .

1. Přidejte datovou sadu, kterou chcete vyčistit, a připojte datovou sadu ke vstupnímu portu na pravé straně.

1. Rozbalte **datovou skupinu pro transformaci dat** v levém podokně návrháře. Vyhledejte uloženou transformaci a přetáhněte ji do kanálu.

1. Připojte uloženou transformaci k levému vstupnímu portu [transformace Apply](apply-transformation.md).

   Když použijete uloženou transformaci, nemůžete vybrat sloupce, které se mají transformovat. Je to proto, že transformace byla definována a automaticky se aplikuje na datové typy zadané v původní operaci.

1. Odešlete kanál.
 
## <a name="technical-notes"></a>Technické poznámky  

Tato část obsahuje podrobné informace o implementaci, tipy a odpovědi na nejčastější dotazy.

### <a name="usage-tips"></a>Tipy k použití

-   Pouze sloupce označené jako kategorií lze převést na sloupce indikátorů. Pokud se zobrazí následující chyba, je pravděpodobný, že jeden ze sloupců, které jste vybrali, není kategorií:  

     Chyba 0056: sloupec s názvem  \<column name> není v povolené kategorii.  

     Ve výchozím nastavení je většina řetězcových sloupců zpracovávána jako řetězcové funkce, takže je musíte explicitně označit jako kategorií pomocí [Edit metadata](edit-metadata.md).  

-   Počet sloupců, které můžete převést na sloupce indikátorů, není nijak omezený. Vzhledem k tomu, že každý sloupec hodnot může vracet více sloupců indikátorů, můžete chtít v jednom okamžiku převést a zkontrolovat jen několik sloupců.  

-   Pokud sloupec obsahuje chybějící hodnoty, vytvoří se samostatný sloupec indikátoru pro chybějící kategorii s tímto názvem: *\<source column> – chybějící*  

-   Pokud sloupec, který převedete na hodnoty indikátoru, obsahuje čísla, musí být označený jako kategorií jako jakýkoliv jiný sloupec funkce. Až to uděláte, budou se čísla považovat za diskrétní hodnoty. Pokud máte například číselný sloupec s hodnotami MPG v rozsahu od 25 do 30, vytvoří se nový sloupec indikátoru pro každou diskrétní hodnotu:  

    | Značka       | Silniční MPG – 25 | Silniční MPG – 26 | Silniční MPG – 27 | Silniční MPG – 28 | Dálnice MPG-29 | Silniční MPG – 30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Automobily společnosti Contoso | 0               | 0               | 0               | 0               | 0               | 1               |

- Abyste se vyhnuli přidání příliš velkého počtu dimenzí do datové sady. Doporučujeme, abyste nejdřív zkontrolovali počet hodnot ve sloupci a přihrádku nebo kvantifikujeme data odpovídajícím způsobem.  


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
