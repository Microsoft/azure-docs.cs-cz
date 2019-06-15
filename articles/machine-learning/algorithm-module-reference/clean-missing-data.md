---
title: 'Vyčištění chybějících dat: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu vyčištění chybějících dat ve službě Azure Machine Learning k odebrání, nahrazení nebo odvodit chybějící hodnoty.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: de81204219a102734f1820258a3c32e59a64c685
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028782"
---
# <a name="clean-missing-data-module"></a>Modulu vyčištění chybějících dat

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k odebrání, nahrazení nebo odvodit chybějící hodnoty. 

Odborníci přes data často Kontrola dat pro chybějící hodnoty a pak provádět různé operace pro opravu data nebo vložit nové hodnoty. Cílem takových čisticí operací je zabránit potíže způsobené chybějící data, která se můžou objevit při cvičení modelu. 

Tento modul podporuje více typů operací "čištění" chybějící hodnoty, včetně:

+ Chybějící hodnoty nahraďte zástupný symbol, střední nebo jiná hodnota
+ Úplně odebrat řádky a sloupce, které chybí některé hodnoty
+ Odvození hodnoty na základě statistické metod


Pomocí tohoto modulu nezmění vaši zdrojovou datovou sadu. Místo toho vytvoří nová datová sada v pracovním prostoru, který můžete použít v následných pracovního postupu. Můžete také uložit nový, vyčištěnou datovou sadu pro opakované použití.

Tento modul také výstup definicí transformace používá k vyčištění chybějících hodnot. Můžete znovu použít tuto transformaci na jiné datové sady, která mají stejné schéma, pomocí [použít transformace](./apply-transformation.md) modulu.  

## <a name="how-to-use-clean-missing-data"></a>Jak používat vyčištění chybějících dat

Tento modul umožňuje definovat operace čištění. Můžete také uložit operace čištění tak, aby ho později můžete použít pro nová data. Zobrazíte popis toho, jak vytvořit a uložit čisticí proces prostřednictvím následujících odkazů: 
 
+ Chcete-li nahradit chybějící hodnoty
  
+ Chcete-li použít čisticí transformace na nová data
 
> [!IMPORTANT]
> Čisticí Metoda, který používáte pro zpracování chybějící hodnoty, může výrazně ovlivnit vaše výsledky. Doporučujeme nejprve experimenty s různými způsoby. Vezměte v úvahu obě zdůvodnění pro použití konkrétní metody a kvalitu výsledků.

### <a name="replace-missing-values"></a>Nahraďte chybějící hodnoty  

Pokaždé, když použijete [vyčištění chybějících dat](./clean-missing-data.md) modulu na sadu dat, stejné operace čištění platí pro všechny sloupce, které jste vybrali. Proto pokud chcete vyčistit různých sloupců pomocí různých metod, pomocí samostatných instancí modulu.

1.  Přidat [vyčištění chybějících dat](./clean-missing-data.md) modulu do svého experimentu a připojte se datová sada, která má chybějící hodnoty.  
  
2.  Pro **sloupce, které chcete vyčistit**, vyberte sloupce, které obsahující chybějící hodnoty, kterou chcete změnit. Můžete použít více sloupců, ale musí používat stejnou metodu nahrazení všech vybraných sloupců. Proto je obvykle potřeba vyčistit řetězcové sloupce a číselné sloupce samostatně.

    Chcete-li například vyhledávat chybějící hodnoty v číselné všechny sloupce:

    1. Otevřete selektor sloupců a vyberte **pravidla s**.
    2. Pro **začít s**vyberte **bez sloupců**.

        Můžete také začít s všechny sloupce a potom vyloučit sloupce. Na začátku pravidla nezobrazí, pokud nejprve klikněte **všechny sloupce**, ale můžete kliknout na **žádné sloupce** a potom klikněte na tlačítko **všechny sloupce** znovu ke spuštění se všemi sloupci a vyfiltrujte sloupce (vyloučení) na základě názvu, datový typ, nebo index sloupce.

    3. Pro **zahrnout**vyberte **typ sloupce** z rozevíracího seznamu a pak vyberte **číselné**, nebo konkrétnější číselného typu. 
  
    Čištění nebo nahrazení metodu, kterou zvolíte, musí být použitelná **všechny** sloupce ve výběru. Pokud jsou data na jakémkoli sloupci nekompatibilní se zadanou operací, modul vrátí chybu a zastaví experimentu.
  
3.  Pro **minimální chybí hodnota poměr**, určete minimální počet chybějící hodnoty požadované pro operaci, která se má provést.  
  
    Tuto možnost použijte v kombinaci s **maximální chybí hodnota poměr** definovat podmínky, za kterých se provádí operace čištění na datové sadě. Pokud existuje příliš mnoho nebo příliš málo řádků, které nebyly nalezeny hodnoty, nelze operaci provést. 
  
    Počet zadejte představuje **poměr** chybějících hodnot pro všechny hodnoty ve sloupci. Ve výchozím nastavení **minimální chybí hodnota poměr** je nastavena na 0. To znamená, že chybějící hodnoty vyčištěním i v případě, že existuje pouze jedna chybí hodnota. 

    > [!WARNING]
    > Tato podmínka musí být splněny každého sloupce v pořadí pro zadanou operaci použít. Například předpokládat, že jste vybrali tři sloupce a poté nastavte minimální poměr chybějící hodnoty od 2 (20 %), ale pouze jeden sloupec ve skutečnosti má chybějící hodnoty 20 %. V takovém případě by operace čištění platí pouze pro sloupce s více než 20 % chybějící hodnoty. Další sloupce, které by proto beze změny.
    > 
    > Pokud máte jakékoli pochybnosti o tom, zda byly změněny chybějících hodnot, vyberte možnost, **generovat chybí hodnota indikátoru sloupec**. Sloupec se připojí k datové sadě k označení, zda každý sloupec splněna zadaná kritéria pro minimální a maximální rozsahy.  
  
4. Pro **maximální chybí hodnota poměr**, zadejte maximální počet chybějících hodnot, které může být k dispozici pro operace, která se má provést.   
  
    Například můžete chtít provést nahrazení chybějící hodnoty pouze v případě, že je 30 % nebo méně řádků obsahovat chybějící hodnoty, ale ponechte hodnoty jako-se, pokud máte více než 30 % řádky chybějící hodnoty.  
  
    Definujte číslo jako poměr chybějící hodnoty pro všechny hodnoty ve sloupci. Ve výchozím nastavení **maximální chybí hodnota poměr** je nastavená na 1. To znamená, že chybějící hodnoty vyčištěním i v případě, že chybí 100 % z hodnot ve sloupci.  
  
   
  
5. Pro **čištění režimu**, vyberte jednu z následujících možností pro nahrazení nebo odstranění chybějící hodnoty:  
  
  
    + **Hodnota vlastní nahrazení**: Pomocí této možnosti můžete zadat hodnotu zástupného symbolu (jako je například 0 nebo není k dispozici), která se vztahuje na všechny chybějící hodnoty. Hodnota zadaná jako náhrada musí být kompatibilní s datovým typem sloupce.
  
    + **Nahraďte střední**: Vypočítá průměr sloupce a používá průměr jako nahrazující hodnotou pro každou chybí hodnota ve sloupci.  
  
        Platí pouze pro sloupce, které obsahují Integer, Double nebo logické datové typy.  
  
    + **Nahraďte Medián**: Vypočítá Medián sloupce a používá střední hodnotu jako náhrada. pro všechny chybějící hodnota ve sloupci.  
  
        Platí pouze pro sloupce, které mají datové typy celého čísla nebo Double. 
  
    + **Nahraďte režimu**: Vypočte režimu sloupce a používá režim jako nahrazující hodnotou pro každou chybí hodnota ve sloupci.  
  
        Platí pro sloupce, které mají datové typy Integer, Double, logická hodnota nebo zařazené do kategorií. 
  
    + **Odstranit celý řádek**: Úplně odebere všechny řádky v datové sadě, která má jeden nebo více chybějící hodnoty. To je užitečné, pokud chybí hodnota lze považovat za náhodně chybí.  
  
    + **Odeberte celý sloupec**: Úplně odebere všechny sloupce v datové sadě, která má jeden nebo více chybějící hodnoty.  
  
    
  
6. Možnost **nahrazující hodnotou** je k dispozici, pokud jste vybrali možnost **vlastní hodnota nahrazení**. Zadejte novou hodnotu pro použití jako nahrazující hodnotou pro všechny chybějící hodnoty ve sloupci.  
  
    Všimněte si, že tuto možnost můžete použít pouze ve sloupcích, které mají datové typy Integer, Double, logická hodnota nebo datum. Pro sloupce s datem, nahrazující hodnotou lze také zadat jako počet taktů 100 nanosekund od 1/1/0001 12:00 dop.  
  
7. **Generovat chybí hodnota indikátoru sloupec**: Tuto možnost vyberte, pokud chcete výstup některé označení, zda hodnoty ve sloupci splněna kritéria pro chybějící hodnota čištění. Tato možnost je obzvláště užitečná při nastavování nové operace čištění a ujistěte se, že funguje tak, jak navrženo.
  
8. Spusťte experiment.

### <a name="results"></a>Výsledky

V modulu vrátí dva výstupy:  

-   **Vyčištěnou datovou sadu**: Datová sada sestává z vybraných sloupců s chybějící hodnoty zpracovávány jako zadaný společně s sloupec indikátoru, pokud vyberete tuto možnost.  

    Není vybrána pro čištění sloupce jsou také "předává".  
  
-  **Čištění transformace**: Požadovanou transformaci dat používá pro čištění, který můžete uložit ve vašem pracovním prostoru a použít pro nová data později.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Použít uložený operace čištění pro nová data  

Pokud potřebujete často opakujte čištění, doporučujeme uložit vaši předpisu pro čištění dat jako *transformace*, chcete-li znovu použít stejné datové sadě. Ukládá se čištění transformace je zvlášť užitečné, pokud musí často znovu naimportovat a následně vyčistit data, která má stejné schéma.  
      
1.  Přidat [použít transformace](./apply-transformation.md) modulu do experimentu.  
  
2.  Přidat datovou sadu, kterou chcete vyčistit a připojení k pravé vstupním portem datové sady.  
  
3.  Rozbalte **transformuje** skupinu v podokně na levé straně rozhraní. Vyhledání uloženého transformaci a přetáhněte ji do experimentu.  
  
4.  Připojte se k levým vstupním portem z uložené transformace [použít transformace](./apply-transformation.md). 

    Pokud použijete uložené transformace, nelze vybrat sloupce, na které se použijí transformace. Důvodem je skutečnost, že transformace již byla definována a automaticky se vztahuje na sloupce zadané v původní operaci.

    Však Předpokládejme, že jste vytvořili transformaci na podmnožinu číselné sloupce. Tato transformace můžete použít na datovou sadu typů smíšené sloupce bez vyvolání k chybě, protože chybějící hodnoty se změní pouze v odpovídající číselné sloupce.

6.  Spusťte experiment.  

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 