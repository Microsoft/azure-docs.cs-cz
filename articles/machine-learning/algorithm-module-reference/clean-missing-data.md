---
title: 'Vyčistit chybějící data: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Vyčistit chybějící data v Azure Machine Learning odebrat, nahradit nebo odvodit chybějící hodnoty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 14c3bc968da9d398fbc14eda74378047cf28277b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477693"
---
# <a name="clean-missing-data-module"></a>Modul Vyčistit chybějící data

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k odebrání, nahrazení nebo odvodit chybějící hodnoty. 

Datoví vědci často kontrolují data pro chybějící hodnoty a poté provádějí různé operace k opravě dat nebo vložení nových hodnot. Cílem těchto operací čištění je zabránit problémům způsobeným chybějícími daty, které mohou vzniknout při trénování modelu. 

Tento modul podporuje více typů operací pro "čištění" chybějící hodnoty, včetně:

+ Nahrazení chybějících hodnot zástupným symbolem, střední nebo jinou hodnotou
+ Úplné odebrání řádků a sloupců, u kterých chybí hodnoty
+ Odvození hodnot založených na statistických metodách


Použití tohoto modulu nezmění zdrojovou datovou sadu. Místo toho vytvoří novou datovou sadu ve vašem pracovním prostoru, který můžete použít v následujícím pracovním postupu. Můžete také uložit novou vyčištěnou datovou sadu pro opakované použití.

Tento modul také vyhotovuje definici transformace použité k čištění chybějících hodnot. Tuto transformaci můžete znovu použít v jiných datových sadách, které mají stejné schéma, pomocí modulu [Použít transformaci.](./apply-transformation.md)  

## <a name="how-to-use-clean-missing-data"></a>Použití funkce Vyčistit chybějící data

Tento modul umožňuje definovat operaci čištění. Můžete také uložit operaci čištění, takže ji můžete později použít na nová data. Postup vytvoření a uložení procesu čištění naleznete v následujících částech: 
 
+ [Nahrazení chybějících hodnot](#replace-missing-values)
  
+ [Použití transformace čištění na nová data](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> Metoda čištění, kterou používáte pro zpracování chybějícíhodnoty může výrazně ovlivnit výsledky. Doporučujeme experimentovat s různými metodami. Zvažte jak odůvodnění použití určité metody, tak kvalitu výsledků.

### <a name="replace-missing-values"></a>Nahradit chybějící hodnoty  

Pokaždé, když použijete modul [Vyčistit chybějící data](./clean-missing-data.md) na sadu dat, bude stejná operace čištění použita na všechny vybrané sloupce. Proto pokud potřebujete vyčistit různé sloupce pomocí různých metod, použijte samostatné instance modulu.

1.  Přidejte modul [Vyčistit chybějící data](./clean-missing-data.md) do kanálu a připojte datovou sadu, která obsahuje chybějící hodnoty.  
  
2.  **Chcete-li sloupce vyčistit**, zvolte sloupce, které obsahují chybějící hodnoty, které chcete změnit. Můžete zvolit více sloupců, ale ve všech vybraných sloupcích musíte použít stejnou metodu nahrazení. Proto obvykle je třeba vyčistit sloupce řetězce a číselné sloupce samostatně.

    Chcete-li například zkontrolovat chybějící hodnoty ve všech číselných sloupcích:

    1. Vyberte modul **Vyčistit chybějící data** a klikněte na sloupec **Upravit** v pravém panelu modulu.

    3. V **části Zahrnout**vyberte v rozevíracím seznamu typy **sloupců** a pak vyberte **Číselné**. 
  
    Všechny metody čištění nebo výměny, které zvolíte, musí být použitelné pro **všechny** sloupce ve výběru. Pokud data v libovolném sloupci není kompatibilní se zadanou operací, modul vrátí chybu a zastaví kanál.
  
3.  Pro **minimální poměr chybějících hodnot**zadejte minimální počet chybějících hodnot požadovaných pro operaci, která má být provedena.  
  
    Tuto možnost použijete v kombinaci s **maximálním poměrem chybějících hodnot** k definování podmínek, za kterých se provádí operace čištění datové sady. Pokud existuje příliš mnoho nebo příliš málo řádků, které chybí hodnoty, operaci nelze provést. 
  
    Zadávané číslo představuje **poměr** chybějících hodnot ke všem hodnotám ve sloupci. Ve výchozím nastavení je vlastnost **Minimální poměr chybějícíhodnoty** nastavena na hodnotu 0. To znamená, že chybějící hodnoty jsou vyčištěny i v případě, že existuje pouze jedna chybějící hodnota. 

    > [!WARNING]
    > Tato podmínka musí být splněna každým sloupcem, aby se zadaná operace použila. Předpokládejme například, že jste vybrali tři sloupce a potom nastavte minimální poměr chybějících hodnot na 0,2 (20 %), ale pouze jeden sloupec má ve skutečnosti 20 % chybějících hodnot. V takovém případě by se operace vyčištění vztahovala pouze na sloupec s více než 20 % chybějícími hodnotami. Proto ostatní sloupce by beze změny.
    > 
    > Máte-li pochybnosti o tom, zda byly chybějící hodnoty změněny, vyberte možnost **Generovat sloupec indikátoru chybějící hodnoty**. Ke datové sadě je připojen sloupec označující, zda každý sloupec splňuje zadaná kritéria pro minimální a maximální rozsahy.  
  
4. V **poli Maximální poměr chybějících hodnot**zadejte maximální počet chybějících hodnot, které mohou být přítomny pro operaci, která má být provedena.   
  
    Například můžete chtít provést chybějící nahrazení hodnoty pouze v případě, že 30 % nebo méně řádků obsahuje chybějící hodnoty, ale ponechat hodnoty jako -je, pokud více než 30 % řádků mají chybějící hodnoty.  
  
    Číslo definujete jako poměr chybějících hodnot ke všem hodnotám ve sloupci. Ve výchozím nastavení je **maximální poměr chybějících hodnot** nastaven na 1. To znamená, že chybějící hodnoty jsou vyčištěny i v případě, že chybí 100 % hodnot ve sloupci.  
  
   
  
5. V **části Režim čištění**vyberte jednu z následujících možností pro nahrazení nebo odebrání chybějících hodnot:  
  
  
    + **Vlastní substituční hodnota**: Tuto možnost použijte k určení zástupné hodnoty (například 0 nebo NA), která se vztahuje na všechny chybějící hodnoty. Hodnota, kterou zadáte jako náhradu, musí být kompatibilní s datovým typem sloupce.
  
    + **Nahradit průměrem**: Vypočítá střední hodnotu sloupce a použije střední hodnotu jako reprodukční hodnotu pro každou chybějící hodnotu ve sloupci.  
  
        Platí pouze pro sloupce, které mají datové typy Integer, Double nebo Boolean.  
  
    + **Nahradit mediánem**: Vypočítá střední hodnotu sloupce a použije střední hodnotu jako náhradu za chybějící hodnotu ve sloupci.  
  
        Platí pouze pro sloupce, které mají datové typy Integer nebo Double. 
  
    + **Nahradit režimem**: Vypočítá režim sloupce a použije režim jako náhradní hodnotu pro každou chybějící hodnotu ve sloupci.  
  
        Platí pro sloupce, které mají datové typy Integer, Double, Boolean nebo Categorical. 
  
    + **Odebrat celý řádek**: Zcela odebere libovolný řádek v datové sadě, který má jednu nebo více chybějících hodnot. To je užitečné, pokud chybějící hodnota může být považována za náhodně chybějící.  
  
    + **Odebrat celý sloupec**: Zcela odstraní všechny sloupce v datové sadě, která má jednu nebo více chybějících hodnot.  
  
    
  
6. Možnost **Náhradní hodnota** je k dispozici, pokud jste vybrali možnost **Vlastní substituční hodnota**. Zadejte novou hodnotu, která se má použít jako náhradní hodnota pro všechny chybějící hodnoty ve sloupci.  
  
    Všimněte si, že tuto možnost můžete použít pouze ve sloupcích, které mají celé číslo, dvojité, logické nebo řetězci.
  
7. **Generovat chybějící sloupec indikátoru hodnoty**: Tuto možnost vyberte, pokud chcete vytvořit nějaký údaj o tom, zda hodnoty ve sloupci splňují kritéria pro chybějící čištění hodnot. Tato možnost je užitečná zejména při nastavování nové operace čištění a chcete se ujistit, že funguje tak, jak byla navržena.
  
8. Odešlete potrubí.

### <a name="results"></a>Výsledky

Modul vrací dva výstupy:  

-   **Vyčištěná datová sada**: Datová sada tvořená vybranými sloupci s chybějícími hodnotami zpracovanými podle zadaných hodnot spolu se sloupcem indikátoru, pokud jste tuto možnost vybrali.  

    Sloupce, které nejsou vybrány pro čištění, jsou také "prošly".  
  
-  **Transformace čištění**: Transformace dat použitá pro čištění, která může být uložena v pracovním prostoru a později použita na nová data.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Použití uložené operace čištění u nových dat  

Pokud potřebujete často opakovat operace čištění, doporučujeme uložit recept na čištění dat jako *transformaci*, znovu použít se stejnou datovou sadou. Uložení transformace čištění je zvláště užitečné, pokud je nutné často znovu importovat a potom vyčistit data, která má stejné schéma.  
      
1.  Přidejte do kanálu modul [Použít transformaci.](./apply-transformation.md)  
  
2.  Přidejte datovou sadu, kterou chcete vyčistit, a připojte ji k pravému vstupnímu portu.  
  
3.  Rozbalte skupinu **Transformace** v levém podokně návrháře. Vyhledejte uloženou transformaci a přetáhněte ji do kanálu.  

4.  Připojte uloženou transformaci k levému vstupnímu portu [Použít transformaci](./apply-transformation.md). 

    Při použití uložené transformace nelze vybrat sloupce, na které se transformace aplikuje. Je to proto, že transformace již byla definována a automaticky se použije na sloupce zadané v původní operaci.

    Předpokládejme však, že jste vytvořili transformaci v podmnožině číselných sloupců. Tuto transformaci můžete použít na datovou sadu smíšených typů sloupců bez vyvolání chyby, protože chybějící hodnoty jsou změněny pouze v odpovídajících číselných sloupcích.

6.  Odešlete potrubí.  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 