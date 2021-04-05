---
title: Oříznutí hodnot
titleSuffix: Azure Machine Learning
description: Naučte se používat modul Clip Values v Azure Machine Learning k detekci nevyužití a oříznutí nebo nahrazení jejich hodnot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 99fb41542dff28997438881abad71da11e927a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96014886"
---
# <a name="clip-values"></a>Oříznutí hodnot

Tento článek popisuje modul návrháře Azure Machine Learning.

Pomocí modulu Clip Values identifikujte a případně nahraďte hodnoty dat, které jsou nad nebo pod zadanou prahovou hodnotou středníku, konstantou nebo jinou náhradou.  

Propojíte modul s datovou sadou, která má čísla, která chcete oříznout, zvolit sloupce, se kterými chcete pracovat, a pak nastavit prahovou hodnotu nebo rozsah hodnot a metodu náhrady. Modul může vyvýstupovat buď pouze výsledky, nebo změněné hodnoty připojené k původní datové sadě.

## <a name="how-to-configure-clip-values"></a>Jak konfigurovat hodnoty klipů

Než začnete, určete sloupce, které chcete oříznout, a metodu, kterou chcete použít. Doporučujeme, abyste nejprve otestovali všechny metody oříznutí na malou podmnožinu dat.

Modul používá stejná kritéria a metodu nahrazení na **všechny** sloupce, které zahrnete do výběru. Proto nezapomeňte vyloučit sloupce, které nechcete měnit.

Pokud potřebujete pro některé sloupce použít ořezové metody nebo jiná kritéria, musíte pro každou sadu podobných sloupců použít novou instanci **hodnoty Clip** .

1.  Přidejte do svého kanálu modul **hodnoty klipu** a připojte ho k datové sadě, kterou chcete upravit. Tento modul můžete najít v části **transformace dat** v kategorii **škálování a zmenšení** . 
  
1.  V **seznamu sloupců** použijte selektor sloupců a vyberte sloupce, na které budou použity **hodnoty klipů** .  
  
1.  V rozevíracím seznamu pro **sadu mezních hodnot** vyberte jednu z následujících možností. Tyto možnosti určují, jak nastavíte horní a dolní hranice pro přijatelné hodnoty a hodnoty, které musí být oříznuty.  
  
    - **ClipPeaks**: při vystřihování hodnot podle špičky zadáte jenom horní hranici. Hodnoty větší než tato hodnota hranice jsou nahrazeny.
  
    -  **ClipSubpeaks**: při vystřihování hodnot podle podšpičky zadáte pouze spodní hranici. Hodnoty, které jsou menší než tato hodnota hranice, budou nahrazeny.  
  
    - **ClipPeaksAndSubpeaks**: když vystřihnout hodnoty podle špičky a podšpičky, můžete zadat horní i dolní hranici. Hodnoty, které jsou mimo tento rozsah, jsou nahrazeny. Hodnoty, které odpovídají hodnotám hranice, se nezmění.
  
1.  V závislosti na výběru v předchozím kroku můžete nastavit následující mezní hodnoty: 

    + **Dolní prahová hodnota**: zobrazí se jenom v případě, že vyberete **ClipSubPeaks** .
    + **Horní prahová hodnota**: zobrazí se jenom v případě, že vyberete **ClipPeaks** .
    + **Prahová hodnota**: zobrazí se pouze v případě, že zvolíte **ClipPeaksAndSubPeaks**

    Pro každý typ prahové hodnoty vyberte buď **konstanta** , nebo **percentil**.

1. Pokud vyberete možnost **konstanta**, zadejte do textového pole maximální nebo minimální hodnotu. Předpokládejme například, že víte, že hodnota 999 byla použita jako hodnota zástupného symbolu. U horní prahové hodnoty můžete zvolit **konstantu** a **pro horní prahovou hodnotu** zadejte 999.
  
1. Pokud zvolíte **percentil**, omezíte hodnoty sloupce na rozsah percentilu. 

    Předpokládejme například, že chcete zachovat pouze hodnoty v rozsahu 10-80 percentilu a nahradit všechny ostatní. Zvolte **percentil** a potom zadejte hodnotu 10 pro **hodnotu percentilu pro nižší prahovou** hodnotu a jako hodnotu percentilu zadejte 80 pro možnost **horní prahová hodnota**. 

    Některé příklady použití rozsahů percentilu najdete v části o [percentilech](#examples-for-clipping-using-percentiles) .  
  
1.  Definujte náhradní hodnotu.

    Čísla, která přesně odpovídají hranicím, které jste zadali, se považují za v povoleném rozsahu hodnot a proto se nenahrazují. Všechna čísla, která spadají mimo zadaný rozsah, se nahradí nahrazenou hodnotou. 
  
    + **Náhradní hodnota pro špičky**: definuje hodnotu, která se nahradí pro všechny hodnoty sloupce, které jsou větší než zadaná prahová hodnota.  
    + **Náhradní hodnota pro podšpičky**: Určuje hodnotu, která má být použita jako náhrada pro všechny hodnoty sloupce, které jsou menší než zadaná prahová hodnota.  
    + Pokud použijete možnost **ClipPeaksAndSubpeaks** , můžete zadat samostatné nahrazující hodnoty pro horní a dolní oříznuté hodnoty.  

    Podporovány jsou následující nahrazující hodnoty:  
  
    -   **Prahová** hodnota: nahradí navýšené hodnoty zadanou prahovou hodnotou.  
  
    -   **Střední hodnota**: nahradí oříznuté hodnoty průměrem hodnot sloupců. Střední hodnota je vypočítána před tím, než jsou hodnoty oříznuty.  
  
    -   **Medián**: nahradí oříznuté hodnoty mediánem hodnot sloupců. Medián je vypočítán dříve, než jsou hodnoty oříznuty.   
  
    -   **Chybí**. Nahradí oříznuté hodnoty chybějící (prázdná) hodnota.  
  
1.  **Přidat sloupce indikátoru**: tuto možnost vyberte, pokud chcete vygenerovat nový sloupec, který vám ukáže, jestli se určená operace oříznutí pro data v tomto řádku používá nebo ne. Tato možnost je užitečná, když testujete novou sadu ořezových a substitučních hodnot.  
  
1. **Příznak přepsání**: Určete, jak chcete generovat nové hodnoty. Ve výchozím nastavení **vystřihnout hodnoty** vytvoří nový sloupec s hodnotami špičky oříznutými na požadovanou prahovou hodnotu. Nové hodnoty přepíšou původní sloupec.  
  
    Chcete-li zachovat původní sloupec a přidat nový sloupec s oříznutými hodnotami, zrušte výběr této možnosti.  
  
1.  Odešlete kanál.  
  
    Klikněte pravým tlačítkem myši na modul **hodnoty klipu** a vyberte **vizualizovat** nebo vyberte modul a přepněte na kartu **výstupy** na pravém panelu, klikněte na ikonu histogramu ve **výstupech portů**, zkontrolujte hodnoty a ujistěte se, že operace oříznutí splnila vaše očekávání.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Příklady pro oříznutí pomocí percentilů

Pro pochopení, jak funguje oříznutí podle percentilů, zvažte datovou sadu s 10 řádky, které mají jednu instanci každou z hodnot 1-10.  
  
- Použijete-li percentil jako horní prahovou hodnotu, hodnota pro 90. percentil, 90 procent všech hodnot v datové sadě musí být menší než tato hodnota.  
  
- Pokud jako spodní prahovou hodnotu používáte percentil, na hodnotě desátého percentilu musí být 10 procent všech hodnot v datové sadě menší než tato hodnota.  
  
1.  V případě **množiny prahových hodnot** vyberte možnost **ClipPeaksAndSubPeaks**.  
  
1.  Pro možnost **horní prahová hodnota** vyberte **percentil** a pro **číslo percentilu** zadejte 90.  
  
1.  V případě **hodnoty velká náhrada** vyberte možnost **chybějící hodnota**.  
  
1.  Pro možnost **nižší prahová hodnota** vyberte **percentil** a pro **číslo percentilu** zadejte 10.  
  
1.  V případě **nižší nahrazující hodnoty** vyberte možnost **chybějící hodnota**.  
  
1.  Zrušte výběr možnosti **přepsat příznak** a vyberte možnost **Přidat sloupec indikátoru**.  
  
Nyní vyzkoušejte stejný kanál s 60 jako prahovou hodnotu v horním percentilu a 30 jako dolní prahovou hodnotu percentilu a jako hodnotu náhrady použijte prahovou hodnotu. Následující tabulka porovnává tyto dvě výsledky:  
  
1.  Nahradit chybějícími; Horní prahová hodnota = 90; Nižší prahová hodnota = 20  
  
1.  Nahradit prahovou hodnotou; Horní percentil = 60; Dolní percentil = 40  
  
|Původní data|Nahradit chybějícími|Nahradit prahovou hodnotou|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> PRAVDA<br /><br /> 3, FALSE<br /><br /> 4, FALSE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, FALSE<br /><br /> 8, FALSE<br /><br /> 9, FALSE<br /><br /> TRUE|4, PRAVDA<br /><br /> 4, PRAVDA<br /><br /> 4, PRAVDA<br /><br /> 4, PRAVDA<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE| 
 
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
