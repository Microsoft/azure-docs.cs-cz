---
title: Použití matematických operací
titleSuffix: Azure Machine Learning
description: Naučte se používat modul použití matematické operace v Azure Machine Learning k použití matematické operace na hodnoty sloupce v kanálu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 04ce45f428604275696d83938708bcee0c6c023f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536762"
---
# <a name="apply-math-operation"></a>Použití matematických operací

Tento článek popisuje modul návrháře Azure Machine Learning.

Použijte operaci použít matematický postup k vytvoření výpočtů, které jsou použity na číselné sloupce vstupní datové sady. 

Matematické operace zahrnují aritmetické funkce, trigonometrické funkce, funkce zaokrouhlování a speciální funkce používané v datové vědy, jako je třeba hodnota gamma a funkce Error.  

Po definování operace a spuštění kanálu jsou hodnoty přidány do datové sady. V závislosti na tom, jak nakonfigurujete modul, můžete:

+ Přidejte výsledky do datové sady (užitečné při ověřování výsledku operace).
+ Nahraďte hodnoty sloupců novými, vypočítanými hodnotami.
+ Vygeneruje nový sloupec pro výsledky a nezobrazuje původní data. 

Hledání potřebných operací v těchto kategoriích:  

- [Basic](#basic-math-operations)  
  
     Funkce v kategorii **Basic** lze použít k manipulaci s jednou hodnotou nebo sloupcem hodnot. Například můžete získat absolutní hodnotu všech čísel ve sloupci nebo vypočítat druhou odmocninu každé hodnoty ve sloupci.  
  
-   [Porovnání](#comparison-operations)  
  
      Funkce v kategorii **porovnání** jsou použity pro porovnání: můžete provést párové porovnání hodnot ve dvou sloupcích nebo můžete porovnat jednotlivé hodnoty ve sloupci se zadanou konstantou. Můžete například porovnat sloupce a určit, zda byly hodnoty stejné ve dvou datových sadách. Nebo můžete použít konstantu, jako je například maximální povolená hodnota, abyste našli odlehlé hodnoty v číselném sloupci.  
  
-   [Operace](#arithmetic-operations)  
  
     Kategorie **operací** zahrnuje základní matematické funkce: sčítání, odčítání, násobení a dělení. Můžete pracovat s buď sloupci, nebo konstantami. Například můžete přidat hodnotu do sloupce A do hodnoty ve sloupci B. Případně můžete z každé hodnoty ve sloupci A odečíst konstantu, jako je například dříve vypočtená střední hodnota.  
  
-   [Zaokrouhlení](#rounding-operations)  
  
     Kategorie **Zaokrouhlení** zahrnuje řadu funkcí pro provádění operací, jako je zaokrouhlení, strop, patra a zkrácení na různé úrovně přesnosti. Můžete určit úroveň přesnosti pro Desítková i celá čísla.  
  
-   [Speciální](#special-math-functions)  
  
     **Speciální** kategorie obsahuje matematické funkce, které jsou obzvláště používány v oblasti datové vědy, jako jsou eliptické integrály a funkce pro chybu Gaussovské.  
  
-   [Trigonometrické](#trigonometric-functions)  
  
     **Trigonometrické** kategorie zahrnuje všechny standardní trigonometrické funkce. Například můžete převést radiány na stupně nebo výpočetní funkce, jako je například Tečna v radiánech nebo stupních.
     Tyto funkce jsou Unární, což znamená, že přebírají jeden sloupec hodnot jako vstup, aplikují trigonometrické funkci a vrátí sloupec hodnot jako výsledek. Ujistěte se, že vstupní sloupec je odpovídající typ a obsahuje správný typ hodnot pro zadanou operaci.   

## <a name="how-to-configure-apply-math-operation"></a>Postup konfigurace použití operace Math  

Modul **použití matematické operace** vyžaduje datovou sadu, která obsahuje alespoň jeden sloupec obsahující pouze čísla. Čísla mohou být diskrétní nebo souvislá, ale musí být číselného datového typu, nikoli řetězce.

Stejnou operaci můžete použít na několik číselných sloupců, ale všechny sloupce musí být ve stejné datové sadě. 

Každá instance tohoto modulu může v jednom okamžiku provádět pouze jeden typ operace. Chcete-li provádět složité matematické operace, může být nutné zřetězit několik instancí modulu **použití matematické operace** .  
  
1.  Přidejte do svého kanálu modul **použití matematické operace** .

1. Připojte datovou sadu, která obsahuje alespoň jeden numerický sloupec.  

1.  Vyberte jeden nebo více zdrojových sloupců, u kterých chcete provést výpočet.   
  
    - Libovolný sloupec, který zvolíte, musí být číselný datový typ. 
    - Rozsah dat musí být platný pro vybranou matematickou operaci. V opačném případě může dojít k chybě nebo NaN (nejedná se o číslo). Například ln (-1,0) je neplatná operace a výsledkem je hodnota `NaN` .
  
1.  Vyberte **kategorie** a vyberte **typ** matematické operace, která se má provést.
    
1. Vyberte konkrétní operaci ze seznamu v této kategorii.
  
1.  Nastavte další parametry požadované jednotlivými typy operace.  
  
1.  Použijte možnost **výstupní režim** k určení, jak chcete, aby se vygenerovala Matematická operace: 

    - **Připojit**. Do výstupní datové sady jsou zahrnuty všechny sloupce používané jako vstupy a navíc je připojen jeden další sloupec, který obsahuje výsledky matematické operace.
    - Na **místě**. Hodnoty ve sloupcích použitých jako vstupy jsou nahrazeny novými počítanými hodnotami. 
    - **ResultOnly**. Vrátí se jeden sloupec obsahující výsledky matematické operace.
  
1.  Odešlete kanál.  
  
## <a name="results"></a>Výsledky

Vygenerujete-li výsledky pomocí možností **připojit** nebo **ResultOnly** , budou záhlaví sloupců vrácené datové sady označovat operaci a sloupce, které byly použity. Například pokud porovnáte dva sloupce pomocí operátoru **Equals** , výsledky budou vypadat takto:  
  
-   **Equals (Col2_Col1)**, což značí, že jste otestovali col2 proti sloupci Sloupec1.  
-   **Rovná se (Col2_ $10)**, což značí, že jste porovnali sloupec 2 s konstantou 10.  

I když použijete možnost **v místě** , zdrojová data se neodstraní ani nezmění. sloupec v původní datové sadě je stále k dispozici v návrháři. Chcete-li zobrazit původní data, můžete připojit modul [Přidat sloupce](add-columns.md) a spojit ho s výstupem **použití matematické operace**.  
    
## <a name="basic-math-operations"></a>Základní matematické operace 

Funkce v **základní** kategorii obvykle přebírají jednu hodnotu ze sloupce, provádějí předdefinovanou operaci a vracejí jednu hodnotu. U některých funkcí můžete zadat konstantu nebo sadu sloupců jako druhý argument.  
  
 Azure Machine Learning podporuje následující funkce v kategorii **základní** :  

### <a name="abs"></a>Abs

Vrátí absolutní hodnotu vybraných sloupců.  
  
### <a name="atan2"></a>Atan2

Vrátí hyperbolický tangens o čtyřnásobné čtvrti.  

Vyberte sloupce, které obsahují souřadnice bodu. Pro druhý argument, který odpovídá souřadnici x, můžete také zadat konstantu.  

Odpovídá funkci ARCTG2 v programu MATLAB.  

### <a name="conj"></a>Conj

Vrátí sdruženou hodnotu ve vybraném sloupci.  

### <a name="cuberoot"></a>CubeRoot

Vypočítá kořenovou datovou krychli pro hodnoty ve vybraném sloupci.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Vypočítá hodnotu Double faktoriál pro hodnoty ve vybraném sloupci. Dvojitý faktoriál je rozšíření normálního faktoriál funkce a je označeno jako `x!!` .  

### <a name="eps"></a>EPS

Vrátí velikost mezery mezi aktuální hodnotou a nejbližším číslem s dvojitou přesností. Odpovídá funkci EPS v programu MATLAB.  
  
### <a name="exp"></a>Exp

Vrátí hodnotu e umocněnou na mocninu hodnoty ve vybraném sloupci. Tato funkce je stejná jako funkce Excelu EXP.  

### <a name="exp2"></a>Exp2 –

Vrátí exponenciální hodnotu argumentu (Base-2) argumentů a řešení pro y = x * 2<sup>t</sup> , kde t je sloupec hodnot obsahující exponenty.  

V části  **sada sloupců**vyberte sloupec, který obsahuje hodnoty exponentu t.

Pro **exp2 –** můžete zadat druhý argument x, který může být buď konstantou, nebo jiným sloupcem hodnot. V poli **typ druhého argumentu**určete, zda budete zadat násobitel x jako konstantu nebo hodnotu ve sloupci.  

Pokud například vyberete sloupec s hodnotami {0,1,2,3,4,5} pro násobitel i exponent, funkce vrátí hodnotu {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Vrátí záporné exponent pro hodnoty ve vybraném sloupci.  

### <a name="factorial"></a>Faktoriál
Vrátí faktoriál hodnot ve vybraném sloupci.  

### <a name="hypotenuse"></a>Přepony
Vypočítá přepony pro trojúhelník, ve kterém je délka jedné strany zadána jako sloupec hodnot, a délka druhé strany je zadána buď jako konstanta, nebo jako dva sloupce.  

### <a name="ln"></a>Logaritmus

Vrátí přirozený logaritmus hodnot ve vybraném sloupci.  

### <a name="lnplus1"></a>LnPlus1

Vrátí přirozený logaritmus a jeden pro hodnoty ve vybraném sloupci.  

### <a name="log"></a>Protokol

Vrátí protokol hodnot ve vybraném sloupci, které jsou zadané základem.  

Základní (druhý argument) můžete určit buď jako konstantu, nebo výběrem jiného sloupce hodnot.  

### <a name="log10"></a>Log10

Vrátí základní hodnoty 10-logaritmu pro vybraný sloupec.  

### <a name="log2"></a>Log2 –

Vrátí hodnoty základu 2-logaritmu pro vybraný sloupec.  

### <a name="nthroot"></a>NthRoot
Vrátí n-tý kořen hodnoty s použitím n, který zadáte.  

Vyberte sloupce, pro které chcete vypočítat kořen, pomocí možnosti **sadou sloupců** .  

V **druhém typu argumentu**vyberte jiný sloupec, který obsahuje kořen, nebo zadejte konstantu, která má být použita jako kořenová.  

Pokud je druhým argumentem sloupec, všechny hodnoty ve sloupci se použijí jako hodnota n pro odpovídající řádek. Pokud je druhý argument konstanta, zadejte hodnotu pro n do textového pole **druhý argument** .
### <a name="pow"></a>Log

Vypočítá X umocněnou na mocninu Y pro každou hodnotu ve vybraném sloupci.  

Nejprve vyberte sloupce, které obsahují **základ**, který by měl být typu float, a to pomocí možnosti **sadou sloupců** .  

V **druhém typu argumentu**vyberte sloupec, který obsahuje exponent, nebo zadejte konstantu, která má být použita jako exponent.  

Pokud je druhým argumentem sloupec, všechny hodnoty ve sloupci se použijí jako exponent pro odpovídající řádek. Je-li druhý argument konstanta, zadejte hodnotu exponent do textového pole **druhý argument** .  

### <a name="sqrt"></a>Sqrt

Vrátí druhou odmocninu hodnot ve vybraném sloupci.  

### <a name="sqrtpi"></a>SqrtPi

Pro každou hodnotu ve vybraném sloupci vynásobí hodnotu hodnotou PI a potom vrátí druhou odmocninu výsledku.  

### <a name="square"></a>Square

Odmocninuje hodnoty ve vybraném sloupci.  

## <a name="comparison-operations"></a>Operace porovnání  

Použijte funkce porovnávání v Návrháři Azure Machine Learning, kdykoli potřebujete testovat dvě sady hodnot mezi sebou. Například v kanálu může být nutné provést tyto operace porovnání:  

- Vyhodnotit sloupec s rizikovým skóre modelu s prahovou hodnotou.
- Určí, zda jsou dvě sady výsledků stejné. Pro každý řádek, který je jiný, přidejte příznak FALSE, který lze použít pro další zpracování nebo filtrování.  

### <a name="equalto"></a>EqualTo

Vrátí hodnotu true, pokud jsou hodnoty stejné.  

### <a name="greaterthan"></a>GreaterThan

Vrátí hodnotu true, pokud jsou hodnoty v **sadě sloupců** větší než zadaná konstanta nebo větší než odpovídající hodnoty ve sloupci porovnání.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Vrátí hodnotu true, pokud jsou hodnoty v **sadě sloupců** větší než nebo rovny zadané konstantě nebo větší nebo rovny odpovídajícím hodnotám ve sloupci porovnání.  

### <a name="lessthan"></a>LessThan

Vrátí hodnotu true, pokud jsou hodnoty v **sadě sloupců** menší než zadaná konstanta nebo menší než odpovídající hodnoty ve sloupci porovnání.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Vrátí hodnotu true, pokud jsou hodnoty v **sadě sloupců** menší nebo rovny zadané konstantě nebo menší nebo rovny odpovídajícím hodnotám ve sloupci porovnání.  

### <a name="notequalto"></a>NotEqualTo

Vrátí hodnotu true, pokud hodnoty v **sadě sloupců** nejsou rovny sloupci konstanta nebo porovnání, a vrátí hodnotu false, pokud jsou stejné.  

### <a name="pairmax"></a>PairMax

Vrátí hodnotu, která je větší, hodnota v **sadě sloupců** nebo hodnota ve sloupci konstanta nebo porovnání.  

### <a name="pairmin"></a>PairMin

Vrátí hodnotu, která je menší, hodnota v **sadě sloupců** nebo hodnota ve sloupci konstanta nebo porovnání.  
  
##  <a name="arithmetic-operations"></a>Aritmetické operace   

Zahrnuje základní aritmetické operace: sčítání a odčítání, dělení a násobení.  Vzhledem k tomu, že většina operací je binární, vyžaduje dvě čísla, nejprve zvolíte operaci a pak zvolíte sloupec nebo čísla, které chcete použít v prvním a druhém argumentu.

Pořadí, ve kterém zvolíte sloupce pro dělení a odčítání, se může zdát neintuitivní; aby bylo ale snazší pochopit výsledky, nadpis sloupce poskytuje název operace a pořadí, ve kterém byly sloupce použity.

Operace|Num1|Num2|Sloupec výsledku|Výsledná hodnota|
----|----|----|----|----
|Sčítání|1|5|Přidat (Num2_Num1)| 4|
|Násobení|1|5|Vícenásobný (Num2_Num1)|5|
|Odčítání|1|5|Odečíst (Num2_Num1)|4|
|Odčítání|0|1|Odečíst (Num2_Num1)|0|
|Dělení|1|5|Dělit (Num2_Num1)|5|
|Dělení|0|1|Dělit (Num2_Num1)|Nekonečno|

### <a name="add"></a>Přidat

Zadejte zdrojové sloupce pomocí **sady sloupců**a potom do těchto hodnot přidejte číslo zadané v **druhém argumentu**.  

Chcete-li přidat hodnoty ve dvou sloupcích, zvolte sloupec nebo sloupce pomocí **sady sloupců**a pak vyberte druhý sloupec pomocí **druhého argumentu**.  

### <a name="divide"></a>Dělení

Vydělí hodnoty ve **sloupci** pomocí konstanty nebo hodnoty sloupce definované v **druhém argumentu**.  Jinými slovy, můžete nejprve vybrat dělitele a potom dividendy. Výstupní hodnota je podíl.

### <a name="multiply"></a>Násobení

Vynásobí hodnoty v **sadě sloupců** zadanými hodnotami konstanty nebo sloupce.  

### <a name="subtract"></a>Odčítání

Určete sloupec hodnot, na kterých se má ( *minuend*) pracovat, výběrem jiného sloupce pomocí možnosti **sada sloupců** . Pak zadejte číslo, které se má odečíst ( *subtrahend*), a to pomocí rozevíracího seznamu **druhý argument** . Můžete zvolit buď konstantu, nebo sloupec hodnot.

##  <a name="rounding-operations"></a>Operace zaokrouhlení 

Návrhář Azure Machine Learning podporuje nejrůznější operace zaokrouhlení. Pro mnoho operací je nutné zadat množství přesnosti, které se má použít při zaokrouhlování. Můžete použít buď statickou úroveň přesnosti, zadanou jako konstantu, nebo můžete použít hodnotu dynamické přesnosti získanou ze sloupce hodnot.  

- Použijete-li konstantu, nastavte **typ přesnosti** na **konstantní** a pak zadejte počet číslic jako celé číslo v textovém poli **přesnost konstanty** . Pokud zadáte jiné než celé číslo, modul nevyvolává chybu, ale výsledky mohou být neočekávané.  

- Chcete-li pro každý řádek datové sady použít jinou hodnotu přesnosti, nastavte **typ přesnosti** na **sadou sloupců**a pak vyberte sloupec, který obsahuje odpovídající hodnoty přesnosti.  

### <a name="ceiling"></a>Ceiling

Vrátí strop pro hodnoty v **sadě sloupců**.  

### <a name="ceilingpower2"></a>CeilingPower2

Vrátí čtvercovou mez pro hodnoty v **sadě sloupců**.  

### <a name="floor"></a>Floor

Vrátí podlahovou hodnotu pro hodnoty ve **sloupci nastavené**na zadanou přesnost.  

### <a name="mod"></a>Mod

Vrátí zlomkovou část hodnot v **sadě sloupců**na zadanou přesnost.  

### <a name="quotient"></a>Podíl

Vrátí zlomkovou část hodnot v **sadě sloupců**na zadanou přesnost.  

### <a name="remainder"></a>Zbytek

Vrátí zbytek hodnot v **sadě sloupců**.  

### <a name="rounddigits"></a>RoundDigits

Vrátí hodnoty v **sadě sloupců**zaokrouhlené pravidlem 4/5 na zadaný počet číslic.  

### <a name="rounddown"></a>RoundDown

Vrátí hodnoty v **sadě sloupců**zaokrouhlené dolů na zadaný počet číslic.  

### <a name="roundup"></a>RoundUp

Vrátí hodnoty v **sadě sloupců**zaokrouhlené až na zadaný počet číslic.  

### <a name="toeven"></a>ToEven

Vrátí hodnoty v **sadě sloupců**zaokrouhlené na nejbližší celé číslo, sudé číslo.  

### <a name="toodd"></a>ToOdd

Vrátí hodnoty v **sadě sloupců**zaokrouhlené na nejbližší celé, liché číslo.  

### <a name="truncate"></a>Truncate

Zkrátí hodnoty v **sadě sloupců** odebráním všech číslic, které nejsou povoleny zadanou přesností.  
  
## <a name="special-math-functions"></a>Speciální matematické funkce

Tato kategorie zahrnuje specializované matematické funkce často používané v oblasti datových věd. Není-li uvedeno jinak, funkce je unární a vrátí zadaný výpočet pro každou hodnotu ve vybraném sloupci nebo sloupci.  

### <a name="beta"></a>Beta

Vrátí hodnotu funkce beta Eulerova.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Vrací hodnotu neúplného eliptického integrálu.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Vrátí hodnotu úplného typu eliptického integrálu (K).  

### <a name="erf"></a>ERF

Vrátí hodnotu funkce Error.  

Funkce Error (označovaná také jako funkce Error Gauss) je zvláštní funkcí tvaru sigmoid, který se používá v pravděpodobnosti k popisu difúze.  

### <a name="erfc"></a>ERFC –

Vrátí hodnotu doplňkové chybové funkce.  

`Erfc` je definován jako 1 – ERF (x).  

### <a name="erfscaled"></a>ErfScaled

Vrátí hodnotu funkce s možností horizontálního navýšení kapacity.  

Pomocí škálované verze funkce Error lze zabránit aritmetickému podtečení.  

### <a name="erfinverse"></a>ErfInverse

Vrátí hodnotu inverzní `erf` funkce.  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

Vrátí hodnotu exponenciálního integrálu EI.  

### <a name="gamma"></a>Hodnot

Vrátí hodnotu funkce gamma.  

### <a name="gammaln"></a>GammaLn

Vrátí přirozený logaritmus funkce gamma.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Vrací hodnotu regulární neúplné funkce gamma.  

Tato funkce přijímá druhý argument, který lze zadat buď jako konstantu, nebo jako sloupec hodnot.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Vrátí hodnotu inverzní nekompletní funkce gamma.  

Tato funkce přijímá druhý argument, který lze zadat buď jako konstantu, nebo jako sloupec hodnot.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Vrací hodnotu regulární neúplné funkce gamma.  

Tato funkce přijímá druhý argument, který lze zadat buď jako konstantu, nebo jako sloupec hodnot.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Vrací hodnotu inverzní generalizované neúplné funkce gamma.

Tato funkce přijímá druhý argument, který lze zadat buď jako konstantu, nebo jako sloupec hodnot.  

### <a name="polygamma"></a>Hodnota Gamma

Vrátí hodnotu funkce gamma.  

Tato funkce přijímá druhý argument, který lze zadat buď jako konstantu, nebo jako sloupec hodnot.

##  <a name="trigonometric-functions"></a>Trigonometrické funkce 

Tato kategorie iIncludes většinu důležitých trigonometrických a inverzních trigonometrických funkcí. Všechny trigonometrické funkce jsou unární a nevyžadují žádné další argumenty.  

### <a name="acos"></a>Acos

Vypočítá Arkus kosinus pro hodnoty sloupce.  

### <a name="acosdegree"></a>AcosDegree

Vypočítá Arkus kosinus hodnot sloupce ve stupních.  

### <a name="acosh"></a>Acosh –

Vypočítá Hyperbolický arkuskosinus hodnot sloupců.  

### <a name="acot"></a>Acot

Vypočítá Arkus kotangens hodnot sloupců.  

### <a name="acotdegrees"></a>AcotDegrees

Vypočítá Arkus kotangens hodnot sloupce ve stupních.  

### <a name="acoth"></a>Acoth

Vypočítá hyperbolický Arkus kotangens hodnot sloupců.  

### <a name="acsc"></a>Acsc

Vypočítá arccosecant hodnot sloupců.  

### <a name="acscdegrees"></a>AcscDegrees

Vypočítá arccosecant hodnot sloupce ve stupních.  
### <a name="asec"></a>Asec

Vypočítá arcsecant hodnot sloupců.  

### <a name="asecdegrees"></a>AsecDegrees

Vypočítá arcsecant hodnot sloupce ve stupních.  

### <a name="asech"></a>Asech

Vypočítá hyperbolický arcsecant hodnot sloupců.  

### <a name="asin"></a>Asin

Vypočítá Arkus sinus hodnot sloupců.  

### <a name="asindegrees"></a>AsinDegrees

Vypočítá Arkus sinus hodnot sloupce ve stupních.  

### <a name="asinh"></a>Asinh –

Vypočítá hyperbolický arkussinus pro hodnoty sloupce.  

### <a name="atan"></a>Atan

Vypočítá arkustangens hodnot sloupců.  

### <a name="atandegrees"></a>AtanDegrees

Vypočítá arkustangens hodnot sloupce ve stupních.  

### <a name="atanh"></a>Atanh –

Vypočítá hyperbolický arkustangens hodnot sloupců.  

### <a name="cos"></a>Cos

Vypočítá kosinus hodnot sloupců.  

### <a name="cosdegrees"></a>CosDegrees

Vypočítá kosinus pro hodnoty sloupce ve stupních.  

### <a name="cosh"></a>Cosh

Vypočítá hyperbolický kosinus hodnot sloupců.  

### <a name="cot"></a>Cot

Vypočítá kotangens hodnot sloupců.  

### <a name="cotdegrees"></a>CotDegrees

Vypočítá kotangens hodnot sloupců ve stupních.  

### <a name="coth"></a>Coth
Vypočítá hyperbolický kotangens pro hodnoty sloupce.  

### <a name="csc"></a>Soubor

Vypočítá kosekans pro hodnoty sloupce.  

### <a name="cscdegrees"></a>CscDegrees

Vypočítá kosekans pro hodnoty sloupce ve stupních.  

### <a name="csch"></a>Csch

Vypočítá hyperbolický kosekans pro hodnoty sloupce.  

### <a name="degreestoradians"></a>DegreesToRadians

Převede stupně na radiány.  

### <a name="sec"></a>Přihrádka

Vypočítá sekans hodnot sloupců.  

### <a name="asecdegrees"></a>aSecDegrees

Vypočítá sekans pro hodnoty sloupce ve stupních.  

### <a name="asech"></a>aSech

Vypočítá hyperbolický sekans hodnot sloupců.  

### <a name="sign"></a>Znaménko

Vrátí znaménko hodnot sloupce.  

### <a name="sin"></a>Sin

Vypočítá sinus hodnot sloupce.  

### <a name="sinc"></a>Sinc

Vypočítá hodnotu hodnoty sloupce ve sinus-kosinus hodnot.  

### <a name="sindegrees"></a>SinDegrees

Vypočítá sinus hodnot sloupce ve stupních.  

### <a name="sinh"></a>Sinh

Vypočítá hyperbolický sinus hodnot sloupců.  

### <a name="tan"></a>Tan

Vypočítá tangens hodnot sloupců.  

### <a name="tandegrees"></a>TanDegrees

Vypočítá tangens argumentu ve stupních.  

### <a name="tanh"></a>Tanh

Vypočítá hyperbolický tangens hodnot sloupců.  
  
## <a name="technical-notes"></a>Technické poznámky

Buďte opatrní, když vyberete více než jeden sloupec jako druhý operátor. Výsledky jsou snadno srozumitelné, pokud je operace jednoduchá, například přidání konstanty do všech sloupců. 

Předpokládejme, že vaše datová sada obsahuje více sloupců, a vy přidáte datovou sadu do sebe samé. Ve výsledcích jsou jednotlivé sloupce přidány do sebe samé takto:  
  
|Num1|Num2|Num3|Přidat (Num1_Num1)|Přidat (Num2_Num2)|Přidat (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Pokud potřebujete provádět složitější výpočty, můžete zřetězit více instancí **operace použití matematických operací**. Například můžete přidat dva sloupce pomocí jedné instance **použití matematické operace**a potom použít jinou instanci **operace Apply** (použít) k rozdělení součtu konstantou pro získání střední hodnoty.  
  
Případně můžete použít jeden z následujících modulů k provedení všech výpočtů najednou, pomocí jazyka SQL, R nebo skriptu Python:
 
+ [Spouštění skriptů R](execute-r-script.md)
+ [Spouštění skriptů Pythonu](execute-python-script.md)
+ [Použití transformace SQL](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
