---
title: 'Vyčistit chybějící data: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu vyčištění chybějících dat v Azure Machine Learning odebrat, nahradit nebo odvodit chybějící hodnoty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 5b7943b2026d640ae7e5d119e165bd752ae2fe7f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898830"
---
# <a name="clean-missing-data-module"></a>Modul vyčištění chybějících dat

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete odebrat, nahradit nebo odvodit chybějící hodnoty. 

Odborníci přes data často kontrolují data pro chybějící hodnoty a pak provádějí různé operace, aby data opravili nebo vložili nové hodnoty. Cílem takových operací čištění je zabránit problémům způsobeným chybějícími daty, která mohou nastat při výuce modelu. 

Tento modul podporuje více typů operací pro "čištění" chybějících hodnot, včetně:

+ Nahrazení chybějících hodnot zástupným symbolem, středníku nebo jinou hodnotou
+ Zcela odebrání řádků a sloupců, které obsahují chybějící hodnoty
+ Odvození hodnot na základě statistických metod


Pomocí tohoto modulu se nezmění vaše zdrojová datová sada. Místo toho vytvoří v pracovním prostoru novou datovou sadu, kterou můžete použít v následujícím pracovním postupu. Můžete také uložit novou, vyčištěnou datovou sadu pro opakované použití.

Tento modul také výstupuje definici transformace použité k vyčištění chybějících hodnot. Tuto transformaci můžete znovu použít na jiné datové sady, které mají stejné schéma, pomocí modulu [použít transformaci](./apply-transformation.md) .  

## <a name="how-to-use-clean-missing-data"></a>Jak používat čištění chybějících dat

Tento modul vám umožní definovat operaci čištění. Operaci čištění můžete také uložit, abyste ji mohli později použít pro nová data. Postup vytvoření a uložení procesu čištění najdete v následujících částech: 
 
+ [Nahrazení chybějících hodnot](#replace-missing-values)
  
+ [Použití čisticí transformace na nová data](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> Čisticí metoda, kterou používáte pro zpracování chybějících hodnot, může významně ovlivnit vaše výsledky. Doporučujeme experimentovat s různými metodami. Vezměte v úvahu odůvodnění použití konkrétní metody a kvality výsledků.

### <a name="replace-missing-values"></a>Nahradit chybějící hodnoty  

Pokaždé, když použijete modul  [Vyčištění chybějících dat](./clean-missing-data.md) na sadu dat, použije se stejná operace čištění pro všechny sloupce, které vyberete. Proto pokud potřebujete vyčistit různé sloupce pomocí různých metod, použijte samostatné instance modulu.

1.  Přidejte modul [Vyčištění chybějících dat](./clean-missing-data.md) do kanálu a připojte datovou sadu, která obsahuje chybějící hodnoty.  
  
2.  Pro **sloupce, které mají být vyčištěny**vyberte sloupce obsahující chybějící hodnoty, které chcete změnit. Můžete zvolit více sloupců, ale je nutné použít stejnou metodu nahrazení ve všech vybraných sloupcích. Proto je obvykle nutné vyčistit řetězcové sloupce a číselné sloupce samostatně.

    Například pro kontrolu chybějících hodnot ve všech číselných sloupcích:

    1. Vyberte modul **Vyčištění chybějících dat** a klikněte na tlačítko **Upravit sloupec** v pravém panelu modulu.

    3. V rozevíracím seznamu **Zahrnout**vyberte **typy sloupců** a pak vyberte **Číselná**. 
  
    Jakákoli metoda čištění nebo náhrady, kterou zvolíte, musí být platná pro **všechny** sloupce ve výběru. Pokud jsou data v jakémkoli sloupci nekompatibilní se zadanou operací, modul vrátí chybu a zastaví kanál.
  
3.  Pro **poměr minimální chybějící hodnoty**zadejte minimální počet chybějících hodnot vyžadovaných pro provedení operace.  
  
    Tuto možnost použijete v kombinaci s **maximálním limitem chybějící hodnoty** , abyste mohli definovat podmínky, za kterých se operace čištění provádí na datové sadě. Pokud je příliš mnoho nebo příliš málo řádků, ve kterých chybí hodnoty, operaci nelze provést. 
  
    Číslo, které zadáte, představuje **poměr** chybějících hodnot ke všem hodnotám ve sloupci. Ve výchozím nastavení je vlastnost **minimální hodnota poměru chybějící hodnoty** nastavena na hodnotu 0. To znamená, že chybějící hodnoty se vyčistí i v případě, že existuje jenom jedna chybějící hodnota. 

    > [!WARNING]
    > Tato podmínka musí být splněna každým a každým sloupcem, aby bylo možné zadanou operaci použít. Předpokládejme například, že jste vybrali tři sloupce a pak nastavíte minimální poměr chybějících hodnot na .2 (20%), ale v jednom sloupci je ve skutečnosti 20% chybějících hodnot. V tomto případě se operace čištění použije jenom pro sloupec s více než 20% chybějícími hodnotami. Ostatní sloupce proto nebudou změněny.
    > 
    > Pokud máte nějaké pochybnosti o tom, jestli se chybějící hodnoty změnily, vyberte možnost, **vygenerujte sloupec indikátor chybějící hodnoty**. Sloupec je připojen k datové sadě, aby označoval, zda každý sloupec splnil zadaná kritéria pro minimální a maximální rozsahy.  
  
4. V poli **maximální velikost chybějící**hodnoty zadejte maximální počet chybějících hodnot, které mohou být k dispozici pro provedení operace.   
  
    Například může být vhodné provést nahrazení chybějící hodnoty pouze v případě, že 30% nebo méně řádků obsahuje chybějící hodnoty, ale hodnoty ponechte tak, jak jsou, pokud má více než 30% řádků chybějící hodnoty.  
  
    Číslo definujete jako poměr chybějících hodnot ke všem hodnotám ve sloupci. Ve výchozím nastavení je **maximální poměr chybějící hodnoty** nastavený na hodnotu 1. To znamená, že chybějící hodnoty se vyčistí i v případě, že hodnota ve sloupci není 100%.  
  
   
  
5. Pro **režim čištění**vyberte jednu z následujících možností pro nahrazení nebo odebrání chybějících hodnot:  
  
  
    + **Vlastní substituční hodnota**: pomocí této možnosti můžete zadat zástupnou hodnotu (například 0 nebo na), která se vztahuje na všechny chybějící hodnoty. Hodnota, kterou zadáte jako náhradu, musí být kompatibilní s datovým typem sloupce.
  
    + **Nahraďte průměrem**: vypočítá průměr sloupce a použije průměr jako hodnotu pro nahrazení každé chybějící hodnoty ve sloupci.  
  
        Platí pouze pro sloupce, které mají datový typ integer, Double nebo Boolean.  
  
    + **Nahradit hodnotou medián**: vypočítá medián sloupce a použije hodnotu mediánu jako náhradu pro všechny chybějící hodnoty ve sloupci.  
  
        Platí pouze pro sloupce, které mají datový typ Integer nebo Double. 
  
    + **Nahradit hodnotou Mode**: vypočítá režim sloupce a použije režim jako nahrazující hodnotu pro všechny chybějící hodnoty ve sloupci.  
  
        Platí pro sloupce, které mají datový typ integer, Double, Boolean nebo kategorií. 
  
    + **Odebrat celý řádek**: kompletně odebere všechny řádky v datové sadě, které mají jednu nebo více chybějících hodnot. To je užitečné v případě, že chybějící hodnota může být považována za náhodně chybět.  
  
    + **Odebrat celý sloupec**: zcela odebere všechny sloupce v datové sadě, které mají jednu nebo více chybějících hodnot.  
  
    
  
6. **Hodnota nahrazení** možnosti je k dispozici, pokud jste vybrali možnost a **vlastní substituční hodnota**. Zadejte novou hodnotu, která se použije jako nahrazující hodnota pro všechny chybějící hodnoty ve sloupci.  
  
    Všimněte si, že tuto možnost lze použít pouze ve sloupcích, které mají celočíselnou hodnotu, Double, logickou hodnotu nebo řetězec.
  
7. **Generovat sloupec indikátoru chybějící hodnoty**: tuto možnost vyberte, pokud chcete výstupem určit, zda hodnoty ve sloupci splňují kritéria pro čištění chybějících hodnot. Tato možnost je užitečná hlavně v případě, že nastavujete novou operaci čištění a chcete se ujistit, že funguje tak, jak je navržena.
  
8. Odešlete kanál.

### <a name="results"></a>Výsledky

Modul vrací dva výstupy:  

-   **Vyčištěná datová sada**: datová sada skládající se z vybraných sloupců s chybějícími hodnotami, které jsou zpracovány jako specifikované, spolu se sloupcem indikátoru, pokud jste vybrali tuto možnost.  

    Sloupce, které nejsou vybrány pro čištění, jsou také "předávány".  
  
-  **Čištění transformace**: transformace dat používaná k čištění, kterou je možné uložit do svého pracovního prostoru a později použít pro nová data.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Použít uloženou operaci čištění na nová data  

Pokud potřebujete znovu opakovat čisticí operace, doporučujeme, abyste uložili svůj recept na čištění dat jako *transformaci*, abyste ho mohli znovu použít se stejnou datovou sadou. Ukládání čisticí transformace je zvláště užitečné, pokud je nutné často znovu importovat a vyčistit data, která mají stejné schéma.  
      
1.  Přidejte do svého kanálu modul [použít transformaci](./apply-transformation.md) .  
  
2.  Přidejte datovou sadu, kterou chcete vyčistit, a připojte datovou sadu ke vstupnímu portu na pravé straně.  
  
3.  Rozbalte skupinu **Transformers** v levém podokně návrháře. Vyhledejte uloženou transformaci a přetáhněte ji do kanálu.  

4.  Připojte uloženou transformaci k levému vstupnímu portu [transformace Apply](./apply-transformation.md). 

    Když použijete uloženou transformaci, nemůžete vybrat sloupce, na které se transformace aplikuje. Důvodem je, že transformace již byla definována a automaticky se používá pro sloupce zadané v původní operaci.

    Předpokládejme však, že jste vytvořili transformaci u podmnožiny číselných sloupců. Tuto transformaci lze použít na datovou sadu smíšených typů sloupců bez vyvolání chyby, protože chybějící hodnoty jsou změněny pouze v rámci odpovídajícího číselného sloupce.

6.  Odešlete kanál.  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 