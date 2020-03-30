---
title: Oříznutí hodnot
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Hodnoty klipů v Azure Machine Learning zjistit odlehlé hodnoty a klip nebo nahradit jejich hodnoty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 6466cea9fe04bb308a670cb03fd3de5314758142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456603"
---
# <a name="clip-values"></a>Oříznutí hodnot

Tento článek popisuje modul Návrháře Azure Machine Learning (preview).

Modul Hodnoty oříznutí slouží k identifikaci a volitelně nahrazení datových hodnot, které jsou nad nebo pod zadanou prahovou hodnotou, střední hodnotou, konstantou nebo jinou náhradní hodnotou.  

Připojíte modul k datové sadě, která obsahuje čísla, která chcete oříznout, vyberte sloupce, se kterými chcete pracovat, a pak nastavte prahovou hodnotu nebo rozsah hodnot a metodu nahrazení. Modul může výstup buď pouze výsledky, nebo změněné hodnoty připojené k původní datové sady.

## <a name="how-to-configure-clip-values"></a>Konfigurace hodnot klipu

Než začnete, určete sloupce, které chcete oříznout, a metodu, kterou chcete použít. Doporučujeme nejprve otestovat libovolnou metodu oříznutí na malé podmnožině dat.

Modul použije stejná kritéria a metodu nahrazení pro **všechny** sloupce, které zahrnete do výběru. Proto nezapomeňte vyloučit sloupce, které nechcete změnit.

Pokud potřebujete použít metody oříznutí nebo různá kritéria pro některé sloupce, musíte pro každou sadu podobných sloupců použít novou instanci **Hodnot klipu.**

1.  Přidejte modul **Hodnoty klipu** do kanálu a připojte ho k datové sadě, kterou chcete upravit. Tento modul najdete v části **Transformace dat**v kategorii Měřítko **a Snížit.** 
  
1.  V **seznamu sloupců**vyberte pomocí voliče sloupců sloupce, na které budou **hodnoty klipu** použity.  
  
1.  V **části Sada prahových hodnot**zvolte z rozevíracího seznamu jednu z následujících možností. Tyto možnosti určují, jak nastavit horní a dolní hranice pro přijatelné hodnoty vs hodnoty, které musí být oříznuty.  
  
    - **ClipPeaks**: Když oříznete hodnoty podle špiček, určíte pouze horní hranici. Hodnoty větší než tato hodnota hranice jsou nahrazeny.
  
    -  **ClipSubpeaks**: Když oříznete hodnoty podvrcholy, určíte pouze dolní hranici. Hodnoty, které jsou menší než tato hodnota hranice jsou nahrazeny.  
  
    - **ClipPeaksAndSubpeaks**: Když oříznete hodnoty podle špiček a podvrcholů, můžete určit horní i dolní hranice. Hodnoty, které jsou mimo tento rozsah jsou nahrazeny. Hodnoty, které odpovídají hodnotám hranic, se nezmění.
  
1.  V závislosti na výběru v předchozím kroku můžete nastavit následující prahové hodnoty: 

    + **Dolní prahová hodnota**: Zobrazí se pouze v případě, že zvolíte **ClipSubPeaks**
    + **Horní prahová hodnota**: Zobrazí se pouze v případě, že zvolíte **ClipPeaks**
    + **Práh**: Zobrazí se pouze v případě, že zvolíte **ClipPeaksAndSubPeaks**

    Pro každý typ prahové hodnoty zvolte **konstantu** nebo **percentil**.

1. Pokud vyberete **Konstantní**, zadejte do textového pole maximální nebo minimální hodnotu. Předpokládejme například, že víte, že hodnota 999 byla použita jako zástupná hodnota. Můžete zvolit **Konstanta** pro horní prahovou hodnotu a zadejte 999 v **Konstanta hodnota pro horní prahovou hodnotu**.
  
1. Pokud zvolíte **percentil**, omezíte hodnoty sloupců na rozsah percentilu. 

    Předpokládejme například, že chcete zachovat pouze hodnoty v rozsahu 10-80 percentilů a nahradit všechny ostatní. Zvolíte **percentil**a pak zadejte hodnotu 10 **pro hodnotu percentilu pro nižší prahovou hodnotu**a zadejte hodnotu 80 pro **hodnotu percentilu pro horní prahovou hodnotu**. 

    Některé příklady použití rozsahů percentilů naleznete v části [o percentilech.](#examples-for-clipping-using-percentiles)  
  
1.  Definujte náhradní hodnotu.

    Čísla, která přesně odpovídají zadaným hranicím, jsou považována za uvnitř povoleného rozsahu hodnot, a proto nejsou nahrazena. Všechna čísla, která spadají mimo zadaný rozsah, jsou nahrazena náhradní hodnotou. 
  
    + **Nahradit hodnotu pro špičky**: Definuje hodnotu, která nahradí všechny hodnoty sloupců, které jsou větší než zadaná prahová hodnota.  
    + **Nahradit hodnotu pro podšpičky**: Definuje hodnotu, která má být používána jako náhrada všech hodnot sloupců, které jsou menší než zadaná prahová hodnota.  
    + Pokud použijete volbu **ClipPeaksAndSubpeaks,** můžete určit samostatné náhradní hodnoty pro horní a dolní oříšknuté hodnoty.  

    Podporovány jsou následující náhradní hodnoty:  
  
    -   **Práh**: Nahradí oříznuté hodnoty zadanou prahovou hodnotou.  
  
    -   **Střední**hodnota : Nahradí oříznuté hodnoty střední hodnotou sloupců. Střední hodnota je vypočítána před oříznutím hodnot.  
  
    -   **Medián**: Nahradí oříznuté hodnoty mediánem hodnot sloupců. Medián se vypočítá před oříznutím hodnot.   
  
    -   **Chybí**. Nahradí oříznuté hodnoty chybějící (prázdnou) hodnotou.  
  
1.  **Přidat sloupce indikátorů**: Tuto možnost vyberte, pokud chcete vygenerovat nový sloupec, který informuje o tom, zda je zadaná ořezová operace použitá na data v tomto řádku. Tato možnost je užitečná při testování nové sady hodnot oříznutí a nahrazení.  
  
1. **Přepsat příznak**: Označte, jak chcete generovat nové hodnoty. Ve výchozím nastavení **vytvoří hodnoty oříznutí** nový sloupec s hodnotami ve špičce oříznutými na požadovanou prahovou hodnotu. Nové hodnoty přepíší původní sloupec.  
  
    Chcete-li zachovat původní sloupec a přidat nový sloupec s oříznutými hodnotami, odznačte tuto volbu.  
  
1.  Odešlete potrubí.  
  
    Klikněte pravým tlačítkem myši na modul **Hodnoty klipu** a vyberte **možnost Vizualizovat** nebo vyberte modul a přepněte na kartu **Výstupy** v pravém panelu, klikněte na ikonu histogramu ve **výstupech portu**, zkontrolujte hodnoty a ujistěte se, že ořezová operace splnila vaše očekávání.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Příklady oříznutí pomocí percentilů

Chcete-li pochopit, jak funguje oříznutí percentily, zvažte datovou sadu s 10 řádky, které mají jednu instanci každou z hodnot 1-10.  
  
- Pokud používáte percentil jako horní prahovou hodnotu, při hodnotě 90 percentilu musí být 90 procent všech hodnot v datové sadě menší než tato hodnota.  
  
- Pokud používáte percentil jako nižší prahovou hodnotu, při hodnotě desátého percentilu musí být 10 procent všech hodnot v datové sadě menší než tato hodnota.  
  
1.  V **poli Sada prahových hodnot**zvolte **ClipPeaksAndSubPeaks**.  
  
1.  V **poli Horní prahová hodnota**zvolte **Percentil**a **pro číslo percentilu**zadejte 90.  
  
1.  V **popřípadě Horní náhradní hodnota**zvolte Chybějící **hodnota**.  
  
1.  V **poli Dolní prahová hodnota**zvolte **Percentil**a **pro číslo percentilu**zadejte 10.  
  
1.  V **popřípadě Nižší náhradní hodnota**zvolte Chybějící **hodnota**.  
  
1.  Odznačte **příznak Přepsání**a vyberte volbu **Přidat sloupec indikátoru**.  
  
Nyní zkuste stejný kanál pomocí 60 jako horní prahovou hodnotu percentilu a 30 jako dolní prahovou hodnotu percentilu a použijte prahovou hodnotu jako náhradní hodnotu. Následující tabulka porovnává tyto dva výsledky:  
  
1.  Nahradit chybějící; Horní práh = 90; Dolní prahová hodnota = 20  
  
1.  Nahradit prahem; Horní percentil = 60; Dolní percentil = 40  
  
|Původní data|Nahradit chybějícím|Nahradit prahem|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSE<br /><br /> 4, NEPRAVDIVÉ<br /><br /> 5, FALSE<br /><br /> 6, NEPRAVDIVÉ<br /><br /> 7, NEPRAVDIVÉ<br /><br /> 8, NEPRAVDIVÉ<br /><br /> 9, NEPRAVDIVÉ<br /><br /> TRUE|4, PRAVDA<br /><br /> 4, PRAVDA<br /><br /> 4, PRAVDA<br /><br /> 4, PRAVDA<br /><br /> 5, FALSE<br /><br /> 6, NEPRAVDIVÉ<br /><br /> 7, PRAVDA<br /><br /> 7, PRAVDA<br /><br /> 7, PRAVDA<br /><br /> 7, PRAVDA| 
 
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
