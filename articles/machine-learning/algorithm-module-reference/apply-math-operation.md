---
title: Použití matematických operací
titleSuffix: Azure Machine Learning
description: Zjistěte, jak použít modul Použít matematické operace v Azure Machine Learning použít matematické operace na hodnoty sloupců v kanálu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2b4d6939aa1db4b8321c792898ed421c0d16cc99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456739"
---
# <a name="apply-math-operation"></a>Použití matematických operací

Tento článek popisuje modul Návrháře Azure Machine Learning (preview).

Pomocí operace Použít matematickou operaci můžete vytvořit výpočty, které se použijí na číselné sloupce ve vstupní datové sadě. 

Podporované matematické operace zahrnují běžné aritmetické funkce, jako je násobení a dělení, goniometrické funkce, různé funkce zaokrouhlení a speciální funkce používané v datové vědě, jako jsou funkce gama a chyb.  

Po definování operace a spuštění kanálu se hodnoty přidají do datové sady. V závislosti na konfiguraci modulu můžete:

+ Přidejte výsledky do datové sady. To je užitečné zejména při ověřování výsledku operace.
+ Nahraďte hodnoty sloupců novými vypočítanými hodnotami.
+ Vygenerujte nový sloupec pro výsledky a nezobrazujte původní data. 

Podívejte se na operaci, kterou potřebujete v těchto kategoriích:  

- [Basic](#basic-math-operations)  
  
     Funkce v **základní** kategorii lze použít k manipulaci s jedinou hodnotou nebo sloupcem hodnot. Můžete například získat absolutní hodnotu všech čísel ve sloupci nebo vypočítat druhou odmocninu každé hodnoty ve sloupci.  
  
-   [Porovnání](#comparison-operations)  
  
      Funkce v kategorii **Porovnat** se používají pro porovnání: můžete provést porovnání hodnot ve dvou sloupcích nebo můžete porovnat každou hodnotu ve sloupci se zadanou konstantou. Můžete například porovnat sloupce a určit, zda byly hodnoty ve dvou datových sadách stejné. Nebo můžete použít konstantu, například maximální povolenou hodnotu, k vyhledání odlehlých hodnot v číselném sloupci.  
  
-   [Provoz](#arithmetic-operations)  
  
     Kategorie **Operace** zahrnuje základní matematické funkce: sčítání, odčítání, násobení a dělení. Můžete pracovat se sloupci nebo konstantami. Můžete například přidat hodnotu ve sloupci A k hodnotě ve sloupci B. Nebo můžete odečíst konstantu, například dříve vypočtenou střední hodnotu, od každé hodnoty ve sloupci A.  
  
-   [Zaokrouhlení](#rounding-operations)  
  
     Kategorie **Zaokrouhlení** zahrnuje celou řadu funkcí pro provádění operací, jako je zaoblení, strop, podlaha a zkrácení na různé úrovně přesnosti. Můžete určit úroveň přesnosti pro desetinná i celá čísla.  
  
-   [Zvláštní](#special-math-functions)  
  
     **Kategorie Special** zahrnuje matematické funkce, které se používají zejména v datové vědě, jako jsou eliptické integrály a Gaussova chybová funkce.  
  
-   [Trigonometrické](#trigonometric-functions)  
  
     Kategorie **goniometriky** zahrnuje všechny standardní goniometrické funkce. Můžete například převést radiány na stupně nebo vypočítat funkce, jako je tečna, buď radiány, nebo stupňů.
     Tyto funkce jsou unární, což znamená, že berou jeden sloupec hodnot jako vstup, použít goniometrické funkce a vrátit sloupec hodnot jako výsledek.  Proto je třeba se ujistit, že vstupní sloupec je vhodný typ a obsahuje správný druh hodnot pro zadanou operaci.   

## <a name="how-to-configure-apply-math-operation"></a>Jak nakonfigurovat použít matematickou operaci  

Modul **Použít matematickou operaci** vyžaduje datovou sadu, která obsahuje alespoň jeden sloupec obsahující pouze čísla. Čísla mohou být diskrétní nebo souvislá, ale musí být číselného datového typu, nikoli řetězce.

Stejnou operaci můžete použít na více číselných sloupců, ale všechny sloupce musí být ve stejné datové sadě. 

Každá instance tohoto modulu může provádět pouze jeden typ operace najednou. Chcete-li provádět složité matematické operace, může být nutné zřetězit několik instancí modulu **Použít operaci matematiky.**  
  
1.  Přidejte do kanálu modul **Použít operaci matematiky.**

1. Připojte datovou sadu, která obsahuje alespoň jeden číselný sloupec.  

1.  Vyberte jeden nebo více zdrojových sloupců, na kterých chcete provést výpočet.   
  
    - Libovolný sloupec, který zvolíte, musí být číselný datový typ. 
    - Rozsah dat musí být platný pro vybranou matematickou operaci. V opačném případě může dojít k chybě nebo výsledku NaN (nikoli čísla). Například Ln(-1.0) je neplatná operace a `NaN`má za následek hodnotu .
  
1.  Klepnutím na **tlačítko Kategorie** vyberte **typ** matematické operace, kterou chcete provést.
    
1. Vyberte konkrétní operaci ze seznamu v dané kategorii.
  
1.  Nastavte další parametry vyžadované jednotlivými typy operací.  
  
1.  Pomocí možnosti **Výstupní režim** můžete určit, jak chcete vygenerovat operaci matematiky: 

    - **Připojit**. Všechny sloupce použité jako vstupy jsou zahrnuty ve výstupní datové sadě plus jeden další sloupec je připojen, který obsahuje výsledky matematické operace.
    - **Na místě**. Hodnoty ve sloupcích použitých jako vstupy jsou nahrazeny novými vypočtenými hodnotami. 
    - **ResultOnly**. Je vrácen jeden sloupec obsahující výsledky matematické operace.
  
1.  Odešlete potrubí.  
  
## <a name="results"></a>Výsledky

Pokud generujete výsledky pomocí možností **Připojit** nebo **ResultOnly,** záhlaví sloupců vrácené datové sady označují operaci a sloupce, které byly použity. Pokud například porovnáte dva sloupce pomocí operátoru **Rovná** se, výsledky budou vypadat takto:  
  
-   **Equals(Col2_Col1)**, což znamená, že jste testovali Col2 proti Col1.  
-   **Rovná se (Col2_$10)**, což znamená, že jste porovnali sloupec 2 s konstantou 10.  

I když použijete možnost **Inplace,** zdrojová data se neodstraní ani nezmění; sloupec v původní datové sadě je stále k dispozici v návrháři. Chcete-li zobrazit původní data, můžete připojit modul [Přidat sloupce](add-columns.md) a připojit jej k výstupu operace **Použít matematickou operaci**.  
    
## <a name="basic-math-operations"></a>Základní matematické operace 

Funkce v **základní** kategorii obvykle trvat jednu hodnotu ze sloupce, provést předdefinovanou operaci a vrátit jednu hodnotu. U některých funkcí můžete jako druhý argument zadat konstantu nebo sadu sloupců.  
  
 Azure Machine Learning podporuje následující funkce v **základní** kategorii:  

### <a name="abs"></a>Abs

Vrátí absolutní hodnotu vybraných sloupců.  
  
### <a name="atan2"></a>Atan2

Vrátí inverzní tečnu se čtyřmi kvadranty.  

Vyberte sloupce, které obsahují souřadnice bodu. Pro druhý argument, který odpovídá souřadnici x, můžete také zadat konstantu.  

Odpovídá funkci ATAN2 v Matlabu.  

### <a name="conj"></a>Conj

Vrátí konjugát pro hodnoty ve vybraném sloupci.  

### <a name="cuberoot"></a>Kořen ová cube

Vypočítá kořen krychle pro hodnoty ve vybraném sloupci.  

### <a name="doublefactorial"></a>Dvojitý faktor  
 Vypočítá dvojitý faktoriál pro hodnoty ve vybraném sloupci. Dvojitý faktoriál je rozšíření masívní faktoriální funkce a je označen jako x!!.  

### <a name="eps"></a>Eps

Vrátí velikost mezery mezi aktuální hodnotou a dalším nejvyšším číslem s dvojitou přesností. Odpovídá funkci EPS v Matlabu.  
  
### <a name="exp"></a>Exp

Vrátí hodnotu e na hodnotu ve vybraném sloupci. Je to stejné jako funkce Excel EXP.  

### <a name="exp2"></a>Exp2

Vrátí hodnotu argumentů base-2, která řeší pro y = x * 2<sup>t,</sup> kde t je sloupec hodnot obsahujících exponenty.  

V **sadě sloupců**vyberte sloupec, který obsahuje exponentní hodnoty t.

Pro **Exp2** můžete zadat druhý argument x, který může být buď konstanta nebo jiný sloupec hodnot. V **second type argument**, označte, zda zadáte násobič x jako konstantu nebo hodnotu ve sloupci.  

Pokud například vyberete sloupec s {0,1,2,3,4,5} hodnotami pro násobič i exponent, vrátí funkce {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Vrátí záporný exponent pro hodnoty ve vybraném sloupci.  

### <a name="factorial"></a>Faktoriál
Vrátí faktoriál pro hodnoty ve vybraném sloupci.  

### <a name="hypotenuse"></a>Přepona
Vypočítá přeponu pro trojúhelník, ve kterém je délka jedné strany určena jako sloupec hodnot a délka druhé strany je určena buď jako konstanta, nebo jako dva sloupce.  

### <a name="ln"></a>Logaritmus

Vrátí přirozený logaritmus pro hodnoty ve vybraném sloupci.  

### <a name="lnplus1"></a>Informace o tom, že

Vrátí přirozený logaritmus plus jeden pro hodnoty ve vybraném sloupci.  

### <a name="log"></a>Protokol

Vrátí protokol hodnot ve vybraném sloupci za daný základ.  

Základ (druhý argument) můžete určit buď jako konstantu, nebo výběrem jiného sloupce hodnot.  

### <a name="log10"></a>Log10

Vrátí základní 10 hodnot logaritmu pro vybraný sloupec.  

### <a name="log2"></a>Protokol2

Vrátí hodnoty logaritmu báze 2 pro vybraný sloupec.  

### <a name="nthroot"></a>NthRoot
Vrátí n-tý kořen hodnoty pomocí n, který zadáte.  

Pomocí možnosti **ColumnSet** vyberte sloupce, pro které chcete kořenový adresář vypočítat.  

V **poli Druhý typ argumentu**vyberte jiný sloupec, který obsahuje kořen, nebo určete konstantu, která se má použít jako kořen.  

Pokud druhý argument je sloupec, každá hodnota ve sloupci se používá jako hodnota n pro odpovídající řádek. Pokud je druhý argument konstanta, zadejte hodnotu pro n do textového pole **Druhý argument.**
### <a name="pow"></a>Pow

Vypočítá X umocněnou na hodnotu Y pro každou z hodnot ve vybraném sloupci.  

Nejprve vyberte sloupce, které obsahují **základnu**, která by měla být float, pomocí **ColumnSet** možnost.  

V **poli Druhý typ argumentu**vyberte sloupec, který obsahuje exponent, nebo zadejte konstantu, která se má použít jako exponent.  

Pokud druhý argument je sloupec, každá hodnota ve sloupci se používá jako exponent pro odpovídající řádek. Pokud je druhý argument konstanta, zadejte hodnotu exponentu do textového pole **Druhý argument.**  

### <a name="sqrt"></a>Sqrt

Vrátí druhou odmocninu hodnot ve vybraném sloupci.  

### <a name="sqrtpi"></a>SqrtPi

Pro každou hodnotu ve vybraném sloupci vynásobí hodnotu pí a poté vrátí druhou odmocninu výsledku.  

### <a name="square"></a>Square

Urovná hodnoty ve vybraném sloupci.  

## <a name="comparison-operations"></a>Srovnávací operace  

Pomocí funkcí porovnání v návrháři Azure Machine Learning kdykoli, že budete potřebovat otestovat dvě sady hodnot proti sobě. Například v kanálu může být nutné provést tyto operace porovnání:  

- Vyhodnoťte model skóre sloupce pravděpodobnosti oproti prahové hodnotě.
- Zjistěte, zda jsou dvě sady výsledků stejné. Pro každý jiný řádek přidejte příznak FALSE, který lze použít pro další zpracování nebo filtrování.  

### <a name="equalto"></a>Rovno

Vrátí hodnotu True, pokud jsou hodnoty stejné.  

### <a name="greaterthan"></a>GreaterThan

Vrátí hodnotu True, pokud jsou hodnoty v **sadě sloupců** větší než zadaná konstanta nebo větší než odpovídající hodnoty ve sloupci porovnání.  

### <a name="greaterthanorequalto"></a>GreaterThanorEqualto

Vrátí hodnotu True, pokud jsou hodnoty v **sadě sloupců** větší nebo rovny zadané konstantě nebo větší nebo rovny odpovídajícím hodnotám ve sloupci porovnání.  

### <a name="lessthan"></a>LessThan

Vrátí hodnotu True, pokud jsou hodnoty v **sadě sloupců** menší než zadaná konstanta nebo menší než odpovídající hodnoty ve sloupci porovnání.  
  
### <a name="lessthanorequalto"></a>LessthanorEqualto

Vrátí hodnotu True, pokud jsou hodnoty v **sadě sloupců** menší nebo rovny zadané konstantě nebo menší nebo rovny odpovídajícím hodnotám ve sloupci porovnání.  

### <a name="notequalto"></a>NotequalTo

Vrátí hodnotu True, pokud se hodnoty v **sadě sloupců** nerovnají konstantě nebo sloupci porovnání, a vrátí hodnotu False, pokud jsou stejné.  

### <a name="pairmax"></a>PairMax

Vrátí hodnotu, která je větší – hodnotu v **sadě sloupců** nebo hodnotu ve sloupci konstanty nebo porovnání.  

### <a name="pairmin"></a>PárMin

Vrátí hodnotu, která je menší – hodnotu v **sadě sloupců** nebo hodnotu ve sloupci konstanty nebo porovnání.  
  
##  <a name="arithmetic-operations"></a>Aritmetické operace   

Zahrnuje základní aritmetické operace: sčítání a odčítání, dělení a násobení.  Vzhledem k tomu, že většina operací jsou binární, vyžadující dvě čísla, nejprve zvolte operaci a pak zvolte sloupec nebo čísla, která chcete použít v prvním a druhém argumentu.

Pořadí, ve kterém zvolíte sloupce pro dělení a odčítání, se může zdát neintuitivní; aby však bylo snazší pochopit výsledky, nadpis sloupce obsahuje název operace a pořadí, ve kterém byly sloupce použity.

Operace|Číslo 1|Číslo 2|Sloupec Výsledek|Výsledná hodnota|
----|----|----|----|----
|Sčítání|1|5|Přidat (Num2_Num1)| 4|
|Násobení|1|5|Více (Num2_Num1)|5|
|Odčítání|1|5|Odečíst (Num2_Num1)|4|
|Odčítání|0|1|Odečíst (Num2_Num1)|0|
|Dělení|1|5|Rozdělit (Num2_Num1)|5|
|Dělení|0|1|Rozdělit (Num2_Num1)|Nekonečno|

### <a name="add"></a>Přidat

Zadejte zdrojové sloupce pomocí **sady sloupců**a pak k těmto hodnotám přidejte číslo určené v **argumentu Druhý**.  

Chcete-li přidat hodnoty ve dvou sloupcích, zvolte sloupec nebo sloupce pomocí **sady sloupců**a pak zvolte druhý sloupec pomocí **druhého argumentu**.  

### <a name="divide"></a>Dělení

Vydělí hodnoty ve **sloupci nastavené** konstantou nebo hodnotami sloupců definovanými v **argumentu Druhý**.  Jinými slovy, nejprve si vyberete dělitela a pak dividendu. Výstupní hodnota je podíl.

### <a name="multiply"></a>Násobení

Vynásobí hodnoty ve **sloupci nastavené** zadanou hodnotou konstanty nebo sloupců.  

### <a name="subtract"></a>Odčítání

Zadejte sloupec hodnot, na kterých se má pracovat *(minuend),* výběrem jiného sloupce pomocí možnosti **Sada sloupců.** Potom zadejte číslo odečíst *(subtrahend*) pomocí druhý **argument** rozevírací seznam. Můžete zvolit konstantu nebo sloupec hodnot.

##  <a name="rounding-operations"></a>Zaokrouhlení operace 

Azure Machine Learning návrhář podporuje celou řadu operací zaokrouhlení. Pro mnoho operací je nutné zadat míru přesnosti, která se má použít při zaokrouhlení. Můžete použít buď statickou úroveň přesnosti, určenou jako konstanta, nebo můžete použít hodnotu dynamické přesnosti získanou ze sloupce hodnot.  

- Pokud používáte konstantu, nastavte **typ přesnosti** na **konstantní** a zadejte počet číslic jako celé číslo do textového pole **Konstantní přesnost.** Pokud zadáte necelé číslo, modul nevyvolá chybu, ale výsledky mohou být neočekávané.  

- Chcete-li pro každý řádek datové sady použít jinou hodnotu přesnosti, nastavte **hodnotu typu Přesnost** na **columnset**a pak zvolte sloupec, který obsahuje příslušné hodnoty přesnosti.  

### <a name="ceiling"></a>Ceiling

Vrátí strop pro hodnoty v **sadě sloupců**.  

### <a name="ceilingpower2"></a>Stropní síla2

Vrátí čtvercový strop pro hodnoty v **sadě sloupců**.  

### <a name="floor"></a>Floor

Vrátí podlahu pro hodnoty v **sadě sloupců**na zadanou přesnost.  

### <a name="mod"></a>Mod

Vrátí zlomkovou část hodnot v **sadě sloupců**na zadanou přesnost.  

### <a name="quotient"></a>Podíl

Vrátí zlomkovou část hodnot v **sadě sloupců**na zadanou přesnost.  

### <a name="remainder"></a>Zbytek

Vrátí zbytek pro hodnoty v **sadě sloupců**.  

### <a name="rounddigits"></a>RoundDigits

Vrátí hodnoty v **sadě sloupců**zaokrouhlené pravidlem 4/5 na zadaný počet číslic.  

### <a name="rounddown"></a>RoundDown

Vrátí hodnoty v **sadě sloupců**zaokrouhlené dolů na zadaný počet číslic.  

### <a name="roundup"></a>RoundUp

Vrátí hodnoty v **sadě sloupců**zaokrouhlené nahoru na zadaný počet číslic.  

### <a name="toeven"></a>Toeven (Až po sudý)

Vrátí hodnoty v **sadě sloupců**zaokrouhlené na nejbližší celé sudé číslo.  

### <a name="toodd"></a>Toodd (Toodd)

Vrátí hodnoty v **sadě sloupců**zaokrouhlené na nejbližší celé, liché číslo.  

### <a name="truncate"></a>Truncate

Zkrátí hodnoty ve **sloupci nastavené** odebráním všech číslic, které nejsou povoleny zadanou přesností.  
  
## <a name="special-math-functions"></a>Speciální matematické funkce

Tato kategorie zahrnuje specializované matematické funkce, které se často používají v datové vědě. Není-li uvedeno jinak, funkce je unární a vrátí zadaný výpočet pro každou hodnotu ve vybraném sloupci nebo sloupcích.  

### <a name="beta"></a>Beta

Vrátí hodnotu Eulerovy beta funkce.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Vrátí hodnotu neúplného eliptického integrálu.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Vrátí hodnotu celého eliptický integrál (K).  

### <a name="erf"></a>Erf (Erf)

Vrátí hodnotu chybové funkce.  

Chybová funkce (také nazývaná Gaussova chybová funkce) je speciální funkce sigmoidního tvaru, která se používá pravděpodobně k popisu difúze.  

### <a name="erfc"></a>Erfc (Fr.)

Vrátí hodnotu funkce doplňkové chyby.  

Erfc je definována jako 1 – erf(x).  

### <a name="erfscaled"></a>ErfScaled

Vrátí hodnotu chybové funkce s měřítkem.  

Škálované verze funkce chyby lze zabránit aritmetické podtečení.  

### <a name="erfinverse"></a>ErfInverse

Vrátí hodnotu inverzní funkce erf.  

### <a name="exponentialintegralein"></a>ExponenciálníIntegralEin

Vrátí hodnotu exponenciálního integrálu Ei.  

### <a name="gamma"></a>Gama

Vrátí hodnotu funkce gama.  

### <a name="gammaln"></a>GammaLn

Vrátí přirozený logaritmus funkce gama.  

### <a name="gammaregularizedp"></a>GamaRegularizedP

Vrátí hodnotu regularizované neúplné funkce gama.  

Tato funkce má druhý argument, který může být poskytnuta buď jako konstanta nebo sloupec hodnot.  

### <a name="gammaregularizedpinverse"></a>GamaRegularizedPInverse

Vrátí hodnotu inverzní regularizované neúplné funkce gama.  

Tato funkce má druhý argument, který může být poskytnuta buď jako konstanta nebo sloupec hodnot.  

### <a name="gammaregularizedq"></a>GamaRegularizedQ  

Vrátí hodnotu regularizované neúplné funkce gama.  

Tato funkce má druhý argument, který může být poskytnuta buď jako konstanta nebo sloupec hodnot.  

### <a name="gammaregularizedqinverse"></a>GamaRegularizedQInverse

Vrátí hodnotu inverzní generalizované regularizované neúplné funkce gama.

Tato funkce má druhý argument, který může být poskytnuta buď jako konstanta nebo sloupec hodnot.  

### <a name="polygamma"></a>PolyGamma

Vrátí hodnotu funkce polygammu.  

Tato funkce má druhý argument, který může být poskytnuta buď jako konstanta nebo sloupec hodnot.

##  <a name="trigonometric-functions"></a>Goniometrické funkce 

Tato kategorie iZahrnuje většinu důležitých goniometrických a inverzních goniometrických funkcí. Všechny goniometrické funkce jsou unární a nevyžadují žádné další argumenty.  

### <a name="acos"></a>Acos

Vypočítá arckosinus pro hodnoty sloupců.  

### <a name="acosdegree"></a>AcosDegree

Vypočítá arckosinus hodnot sloupců ve stupních.  

### <a name="acosh"></a>Acosh

Vypočítá hyperbolický arckosinu hodnot sloupců.  

### <a name="acot"></a>Acot

Vypočítá arccotangent hodnot sloupců.  

### <a name="acotdegrees"></a>AcotDegrees

Vypočítá arccotangent hodnot sloupců ve stupních.  

### <a name="acoth"></a>Acoth (Acoth)

Vypočítá hyperbolický arccotangent hodnot sloupců.  

### <a name="acsc"></a>Acsc

Vypočítá arccosecant hodnot sloupců.  

### <a name="acscdegrees"></a>AcscDegrees

Vypočítá arccosecant hodnot sloupců ve stupních.  
### <a name="asec"></a>Asec

Vypočítá arcsecant hodnot sloupců.  

### <a name="asecdegrees"></a>AsecDegrees

Vypočítá arcsecant hodnot sloupců ve stupních.  

### <a name="asech"></a>Asech (Asech)

Vypočítá hyperbolický arcsecant hodnot sloupců.  

### <a name="asin"></a>Asin

Vypočítá arcinsin hodnot sloupců.  

### <a name="asindegrees"></a>AsinDegrees

Vypočítá arcinsin hodnot sloupců ve stupních.  

### <a name="asinh"></a>Asinh (Asinh)

Vypočítá hyperbolický arcinin pro hodnoty sloupců.  

### <a name="atan"></a>Atan

Vypočítá arctangent hodnot sloupců.  

### <a name="atandegrees"></a>AtanDegrees

Vypočítá arctangent hodnot sloupců ve stupních.  

### <a name="atanh"></a>Atanh (Atanh)

Vypočítá hyperbolický arctangent hodnot sloupců.  

### <a name="cos"></a>Cos

Vypočítá kosinus hodnot sloupců.  

### <a name="cosdegrees"></a>CosDegrees

Vypočítá kosinus pro hodnoty sloupců ve stupních.  

### <a name="cosh"></a>Cosh

Vypočítá hyperbolický kosinus pro hodnoty sloupců.  

### <a name="cot"></a>Cot

Vypočítá kotangent pro hodnoty sloupců.  

### <a name="cotdegrees"></a>Dětská uchy

Vypočítá kotangent pro hodnoty sloupců ve stupních.  

### <a name="coth"></a>Coth
Vypočítá hyperbolický kotangenpro hodnoty sloupců.  

### <a name="csc"></a>Csc

Vypočítá kosekanpro hodnoty sloupců.  

### <a name="cscdegrees"></a>CscDegrees

Vypočítá kosekant pro hodnoty sloupců ve stupních.  

### <a name="csch"></a>Čsch

Vypočítá hyperbolický kosekanpro hodnoty sloupců.  

### <a name="degreestoradians"></a>DegreesToRadians

Převede stupně na radiány.  

### <a name="sec"></a>Sec

Vypočítá sekans hodnot sloupců.  

### <a name="asecdegrees"></a>aSecDegrees

Vypočítá sekan pro hodnoty sloupců ve stupních.  

### <a name="asech"></a>aSech

Vypočítá hyperbolický sekans hodnot sloupců.  

### <a name="sign"></a>Znaménko

Vrátí znaménko hodnot sloupců.  

### <a name="sin"></a>Sin

Vypočítá sinus hodnot sloupců.  

### <a name="sinc"></a>Sink

Vypočítá hodnotu sinusového kosinusu hodnot sloupců.  

### <a name="sindegrees"></a>SinDegrees

Vypočítá sinus pro hodnoty sloupců ve stupních.  

### <a name="sinh"></a>Sinh

Vypočítá hyperbolický sinus hodnot sloupců.  

### <a name="tan"></a>Tan

Vypočítá tečnu hodnot sloupců.  

### <a name="tandegrees"></a>TanDegrees

Vypočítá tečnu pro argument ve stupních.  

### <a name="tanh"></a>Tanh

Vypočítá hyperbolickou tečnu hodnot sloupců.  
  
## <a name="technical-notes"></a>Technické poznámky

Buďte opatrní, když vyberete více než jeden sloupec jako druhý operátor. Výsledky jsou snadno pochopitelné, pokud je operace jednoduchá, například přidání konstanty do všech sloupců. 

Předpokládejme, že vaše datová sada obsahuje více sloupců a přidáte ji do sebe. Ve výsledcích je každý sloupec přidán sám k sobě, a to následovně:  
  
|Číslo 1|Číslo 2|Číslo 3|Přidat (Num1_Num1)|Přidat (Num2_Num2)|Přidat (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Pokud potřebujete provádět složitější výpočty, můžete zřetězit více instancí **použít operaci matematiky**. Můžete například přidat dva sloupce pomocí jedné instance **operace Použít matematický a**potom použít jinou instanci operace Použít **matematický** k rozdělení součtu konstantou pro získání střední.  
  
Případně použijte jeden z následujících modulů k provedení všech výpočtů najednou pomocí skriptu SQL, R nebo Pythonu:
 
+ [Spouštění skriptů R](execute-r-script.md)
+ [Spouštění skriptů Pythonu](execute-python-script.md)
+ [Použití transformace SQL](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
