---
title: Převod na hodnoty indikátoru
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Převést na hodnoty indikátorů v Azure Machine Learning převést sloupce, které obsahují kategorické hodnoty do řady sloupců binární indikátor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: f1b194f2c65f95ad4daff0353d05ca589db9ce51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477659"
---
# <a name="convert-to-indicator-values"></a>Převod na hodnoty indikátoru
Tento článek popisuje modul návrháře Azure Machine Learning.

Pomocí modulu **Převést na hodnoty indikátorů** v návrháři Azure Machine Learning můžete převést sloupce, které obsahují kategorické hodnoty, do řady sloupců binárních indikátorů.  

Tento modul také vyvede definici transformace použité k převodu na hodnoty indikátorů. Tuto transformaci můžete znovu použít v jiných datových sadách, které mají stejné schéma, pomocí modulu [Použít transformaci.](apply-transformation.md)

## <a name="how-to-configure-convert-to-indicator-values"></a>Konfigurace převodu na hodnoty indikátorů

1.  Najděte **možnost Převést na hodnoty indikátorů** a přetáhněte ji do pracovní verze kanálu. Tento modul naleznete v kategorii **Transformace dat.**
    > [!NOTE]
    > Modul Upravit [metadata](edit-metadata.md) můžete použít před modulem **Převést na hodnoty indiciatoru** k označení cílového sloupce jako kategorického.

1. Připojte modul **Převést na hodnoty indikátorů** k datové sadě obsahující sloupce, které chcete převést. 

1. Vyberte **Upravit sloupec,** chcete-li vybrat jeden nebo více kategorických sloupců.

1. Pokud chcete vypsat **pouze** nové logické sloupce, vyberte možnost **Přepsat kategorie sloupců.** Tento parametr je standardně vypnutý.
    

    > [!TIP]
    >  Pokud zvolíte možnost přepsání, zdrojový sloupec se ve skutečnosti neodstraní ani nezmění. Místo toho jsou nové sloupce generovány a prezentovány ve výstupní datové sadě a zdrojový sloupec zůstane k dispozici v pracovním prostoru. Pokud potřebujete zobrazit původní data, můžete kdykoli použít modul [Přidat sloupce](add-columns.md) a přidat zdrojový sloupec zpět.

1. Odešlete potrubí.

## <a name="results"></a>Výsledky

Předpokládejme, že máte sloupec s skóre, které označují, zda má server vysokou, střední nebo nízkou pravděpodobnost selhání.  

| ID serveru | Skóre selhání |
| --------- | ------------- |
| 10301     | Nízká           |
| 10302     | Střednědobé používání        |
| 10303     | Vysoká          |

Když použijete **převést na hodnoty indikátorů**, návrhář převede jeden sloupec popisků na více sloupců obsahujících logické hodnoty:  

| ID serveru | Skóre selhání - nízká | Skóre selhání - střední | Skóre selhání - vysoké |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Konverze funguje takto:  

-   Ve sloupci **Skóre selhání,** který popisuje riziko, existují pouze tři možné hodnoty (Vysoká, Střední a Nízká) a žádné chybějící hodnoty. Takže jsou vytvořeny přesně tři nové sloupce.  

-   Nové sloupce indikátorů jsou pojmenovány na základě záhlaví sloupců a * \< \< *hodnot zdrojového sloupce pomocí tohoto vzoru: zdrojový sloupec> hodnota dat>.  

-   Měl by existovat sloupec 1 přesně v jednom sloupci indikátoru a 0 ve všech ostatních sloupcích indikátorů, protože každý server může mít pouze jedno hodnocení rizika.  

Nyní můžete použít tři sloupce indikátoru jako funkce v modelu strojového učení.

Modul vrací dva výstupy:

- **Datová sada výsledků**: Datová sada s převedenými sloupci hodnot indikátorů. Sloupce, které nejsou vybrány pro čištění, jsou také "prošly".
- **Transformace hodnot indikátoru**: Transformace dat použitá pro převod na hodnoty indikátorů, kterou lze uložit do pracovního prostoru a později použít na nová data.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Použití operace uložených hodnot indikátoru u nových dat

Pokud potřebujete často opakovat operace s hodnotami indikátorů, můžete uložit kroky manipulace s daty jako *transformaci* a znovu je použít se stejnou datovou sadou. To je užitečné, pokud je nutné často reimport a potom vyčistit data, která mají stejné schéma.

1. Přidejte do kanálu modul [Použít transformaci.](apply-transformation.md)

1. Přidejte datovou sadu, kterou chcete vyčistit, a připojte ji k pravému vstupnímu portu.

1. Rozbalte skupinu **Transformace dat** v levém podokně návrháře. Vyhledejte uloženou transformaci a přetáhněte ji do kanálu.

1. Připojte uloženou transformaci k levému vstupnímu portu [Použít transformaci](apply-transformation.md).

   Při použití uložené transformace nelze vybrat sloupce, které chcete transformovat. Důvodem je, že transformace byla definována a platí automaticky na datové typy zadané v původní operaci.

1. Odešlete potrubí.
 
## <a name="technical-notes"></a>Technické poznámky  

Tato část obsahuje podrobnosti implementace, tipy a odpovědi na často kladené otázky.

### <a name="usage-tips"></a>Tipy pro použití

-   Na sloupce indikátorů lze převést pouze sloupce, které jsou označeny jako kategorické. Pokud se zobrazí následující chyba, je pravděpodobné, že jeden z vybraných sloupců není kategorický:  

     Chyba 0056: Sloupec \<s názvem názvu sloupce> není v povolené kategorii.  

     Ve výchozím nastavení je většina sloupců řetězce zpracována jako funkce řetězce, takže je nutné je explicitně označit jako kategorické pomocí [upravit metadata](edit-metadata.md).  

-   Počet sloupců, které lze převést na sloupce indikátorů, není nijak omezen. Protože však každý sloupec hodnot může přinést více sloupců indikátoru, můžete je převést a zkontrolovat vždy jen několik sloupců.  

-   Pokud sloupec obsahuje chybějící hodnoty, vytvoří se pro chybějící kategorii samostatný sloupec indikátoru s tímto názvem: * \<zdrojový sloupec>- Chybějící*  

-   Pokud sloupec, který převedete na hodnoty indikátorů, obsahuje čísla, musí být označeny jako kategorické jako jakýkoli jiný sloupec prvku. Poté, co jste tak učinili, jsou čísla považována za diskrétní hodnoty. Pokud například máte číselný sloupec s hodnotami MPG v rozsahu od 25 do 30, bude pro každou samostatnou hodnotu vytvořen nový sloupec indikátoru:  

    | Značka       | Dálnice mpg -25 | Dálnice mpg -26 | Dálnice mpg -27 | Dálnice mpg -28 | Dálnice mpg -29 | Dálnice mpg -30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso Auta | 0               | 0               | 0               | 0               | 0               | 1               |

- Chcete-li se vyhnout přidání příliš mnoho dimenzí do datové sady. Doporučujeme nejprve zkontrolovat počet hodnot ve sloupci a správně přiřadit nebo kvantizovat data.  


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
