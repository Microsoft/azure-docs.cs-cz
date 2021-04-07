---
title: Využijte modul & hloubkového vzdělávání pro vlaky na světě.
titleSuffix: Azure Machine Learning
description: Naučte se používat v nástroji Azure Machine Learning Designer modul pro vlakovou škálu & hluboko, který vám umožní vytvořit doporučení modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: d7dd7105ddb0d6503faefb996b84c0e53a62ce49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655372"
---
# <a name="train-wide--deep-recommender"></a>Pro vlak v široké & Doporučené
Tento článek popisuje, jak v nástroji Azure Machine Learning Designer využít integrovaný modul pro **vlakovou škálu &** , který vám umožní naučit model doporučení. Tento modul je založený na širokém & hlubokou výuku, který navrhuje Google.

Vysoce **doporučený modul &** špičkového modulu pro vlaky přečte datovou sadu pro hodnocení uživatelem a položky a volitelně i některé funkce uživatelů a položek. Vrátí vyškolenou & hlubokou doporučenou.  Potom můžete pomocí proučeného modelu vygenerovat hodnocení předpovědi nebo doporučení pomocí modulu hodnocení [širokého a hloubkového](score-wide-and-deep-recommender.md) nástroje.  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>Další informace o modelech doporučení a širokém &ovém doporučování  

Hlavním cílem systému doporučení je doporučit *uživatelům* systému jednu nebo více *položek* . Příkladem položky může být film, restaurace, kniha nebo písně. Uživatel může být osoba, skupina osob nebo jiná entita s preferencemi položek.  

Existují dva hlavní přístupy ke doporučeným systémům. 

+ Prvním je přístup **založený na obsahu** , který využívá funkce pro uživatele a položky. Uživatelé mohou být popsáni pomocí vlastností, jako jsou věk a pohlaví, a položky mohou být popsány pomocí vlastností, jako je autor a výrobce. Typické příklady systémů doporučení založených na obsahu najdete na sociálních sítích Matchmaking. 
+ Druhý přístup je **filtrování spolupráce**, které používá pouze identifikátory uživatelů a položek a získává implicitní informace o těchto entitách z matice (zhuštěného) matice hodnocení, které jsou dány uživateli k položkám. Můžeme se dozvědět o uživateli z položek, které ohodnotili, a od jiných uživatelů, kteří mají stejné položky.  

Širší &, který doporučuje, kombinuje tyto přístupy pomocí filtrování spolupráce s přístupem založeným na obsahu. Proto se považuje za **hybridního doporučit**. 

Jak to funguje: když je uživatel relativně přihlášený k systému, předpovědi se vylepšili tím, že použije informace o funkcích pro uživatele, čímž se vyřeší dobře známý problém "studeného startu". Po shromáždění dostatečného počtu hodnocení od konkrétního uživatele ale můžete na základě jejich konkrétního hodnocení vytvořit plně přizpůsobené předpovědi, a ne jenom na jejich funkce. Proto existuje hladký přechod z doporučení na základě obsahu na doporučení založená na filtrování spolupráce. I když nejsou k dispozici funkce pro uživatele nebo položku, bude v režimu filtrování spolupráce stále fungovat v celém &.  

Další podrobnosti o celém & a jeho základním pravděpodobnostní algoritmu najdete v příslušném výzkumném dokumentu: [široké & hloubkové učení pro doporučované systémy](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-train-wide--deep-recommender"></a>Jak nakonfigurovat výuku širokého & vhodného pro vlaky  

+ [Příprava školicích dat](#prepare-data)
+ [Trénování modelu](#train-the-model)

### <a name="prepare-data"></a>Příprava dat

Předtím, než se pokusíte použít modul, je nezbytné, aby vaše data byla ve formátu očekávaném modelem doporučení. K dispozici je například školicí sada dat, která je nutná pro **hodnocení uživatelem-položky** , ale v samostatných datových sadách můžete také zahrnout uživatelské funkce a funkce položek (pokud jsou k dispozici).

#### <a name="required-dataset-of-user-item-ratings"></a>Požadovaná datová sada – hodnocení položek uživatelů

Vstupní data použitá pro školení musí obsahovat správný typ dat ve správném formátu: 

+ První sloupec musí obsahovat identifikátory uživatele.
+ Druhý sloupec musí obsahovat identifikátory položek.
+ Třetí sloupec obsahuje hodnocení páru uživatel-položka. Hodnoty hodnocení musí být číselného typu. 

Například typická sada hodnocení uživatel-položka může vypadat takto:

|UserId|MovieId|Rating|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>Datová sada uživatelských funkcí (volitelné)

Datová sada **uživatelských funkcí** musí obsahovat identifikátory pro uživatele a používat stejné identifikátory, jaké byly zadány v prvním sloupci datové sady hodnocení uživatelé-položky. Zbývající sloupce mohou obsahovat libovolný počet funkcí, které popisují uživatele.  

Například typická sada uživatelských funkcí může vypadat takto: 

|UserId|Věk|Pohlaví|Týkají|Umístění|
|------------|--------------|-----------------------|---------------|------------|
|1|25|male (muž)| Drama    |Evropa|
|223|40|female (žena)|Země|Asie|

#### <a name="item-features-dataset-optional"></a>Datová sada funkcí položky (volitelné)

Datová sada funkcí položky musí v prvním sloupci obsahovat identifikátory položek. Zbývající sloupce mohou obsahovat libovolný počet popisných funkcí pro položky.  

Například typická sada funkcí položek může vypadat takto:  

|MovieId|Nadpis|Původní jazyk|Žánry|Year (Rok)|
|-------------|-------------|-------------------|-----------|---------------|
|68646|Godfather|Angličtina|Drama|1972|
|31381|Pryč s vítr|Angličtina|Historie|1939|

### <a name="train-the-model"></a>Trénování modelu

1.  Přidejte do experimentu modul pro zajištění **širokého a hloubkového** nástroje pro vlak a připojte ho k datové sadě školení.  
  
2. Pokud máte samostatnou datovou sadu uživatelských funkcí nebo funkcí položek, připojte je ke vlakovému modulu pro zajištění **širokého a hloubkového** nástroje.  
  
    - **Datová sada uživatelských funkcí**: Připojte datovou sadu, která popisuje uživatele pro druhý vstup.
    - **Datová sada funkcí položky**: Připojte datovou sadu, která popisuje položky třetího vstupu.  
    
3.  **Epochs**: Určete, kolikrát by měl algoritmus zpracovat veškerá data školení. 

    Čím vyšší je toto číslo, tím lépe vyhovuje školení. školení ale ještě víc času a může způsobit přeložení.

4. **Velikost dávky**: zadejte počet příkladů cvičení využívaných v jednom kroku školení. 

     Tento parametr může mít vliv na rychlost školení. Vyšší velikost dávky vede k epocha méně času, ale může prodloužit dobu konvergence. A pokud je dávka příliš velká, aby odpovídala GPU nebo procesoru, může se vyvolaná Chyba paměti.

5.  Nástroj pro **optimalizaci na nejrůznějších součástech**: vyberte jeden Optimalizátor, aby se přechody na rozsáhlou část modelu projevily.

6.  **Rychlost učení pro celý** Nástroj pro optimalizaci: zadejte číslo mezi 0,0 a 2,0, které definuje výukový kurz pro optimalizaci na nejrůznějších částech.

    Tento parametr představuje velikost kroku v každém výukovém kroku při přesunu směrem k funkci minimální ztráty. Příliš velká míra učení může způsobit, že se bude přecházet na minima, zatímco příliš malá rychlost učení může způsobit problémy s konvergencí.

7.  **Dimenze překřížených funkcí**: zadejte dimenzi zadáním požadovaných ID uživatelů a funkcí ID položek. 

    Ve výchozím nastavení provádí v rámci široké škály & ve výchozím nastavení transformaci mezi produkty přes ID uživatele a funkce ID položek. Výsledkem překročení výsledku bude hodnota hash podle tohoto čísla, aby se zajistila dimenze.

8.  **Optimalizátor hlubokých částí**: vyberte jeden Optimalizátor, abyste mohli přechody použít na hlubokou část modelu.

9.  **Rychlost učení hloubkového optimalizace**: zadejte číslo mezi 0,0 a 2,0, které definuje výukovou rychlost Optimalizátoru hlubokých částí.

10.  **Dimenze vložení uživatele**: Zadejte celé číslo pro určení dimenze vkládání ID uživatele.

     V rámci široké škály &, který doporučuje, se vytváří vložení ID sdíleného uživatele a vkládání ID položek pro rozsáhlou a hlubokou část.

11.  **Dimenze vložení položky**: Zadejte celé číslo pro určení dimenze vkládání ID položky.

12.  **Dimenze vkládání funkcí kategorií**: Zadejte celé číslo a určete rozměry vkládání funkcí kategorií.

     V hluboké součásti širokého nástroje pro hloubkové &, který doporučuje, se vkládání vektoru učí pro každou funkci kategorií. A tyto vektory pro vložení sdílejí stejnou dimenzi.

13.  **Skryté jednotky**: zadejte počet skrytých uzlů hluboké součásti. Počet uzlů v každé vrstvě je oddělen čárkami. Například podle typu "1 000 500 100" zadáte hloubkovou komponentu má tři vrstvy s první vrstvou na poslední v uvedeném pořadí má 1000 uzlů, 500 uzly a 100 uzly.

14.  **Aktivační funkce**: vyberte jednu funkci aktivace použitou pro každou vrstvu, výchozí hodnota je ReLU.

15.  **Dropout**: zadejte číslo mezi 0,0 a 1,0 k určení pravděpodobnosti, během školení budou výstupy vyhozeny v jednotlivých vrstvách.

     Dropout je metoda, která zabraňuje přebudování sítí neuronové. Jedním z běžných rozhodnutí pro tuto hodnotu je začít s 0,5, což se jeví jako optimální pro nejrůznější sítě a úlohy.

16.  **Normalizace dávek**: tuto možnost vyberte, pokud chcete použít normalizaci dávky za každou skrytou vrstvu v hluboké součásti.

     Normalizace dávek je technika k boji při výuce interních potíží s covariate SHIFT během školení sítí. Obecně vám může pomoci zlepšit rychlost, výkon a stabilitu sítí. 

17.  Spuštění kanálu

## <a name="results"></a>Výsledky

Po dokončení běhu kanálu můžete model použít k vyhodnocování, připojit se k širokému a podrobnému [doporučenému](train-wide-and-deep-recommender.md) příchodu do [široké škály](score-wide-and-deep-recommender.md), abyste mohli předpovědět hodnoty pro nové vstupní příklady.

##  <a name="technical-notes"></a>Technické poznámky

Široké & hlubokou škálované lineární modely a rozsáhlé neuronové sítě pro kombinování síly zapamatování a generalizace. V rámci celé součásti je možné použít sadu nezpracovaných funkcí a transformací funkcí, které nepamatují interakce funkcí. A díky méně technickým technikám je hluboká součást generalizovaná na nepřesné kombinace funkcí prostřednictvím nekvalitních vkládání funkcí. 

V implementaci širokého doporučení pro rozsáhlou & modul používá výchozí strukturu modelu. V rámci stejné součásti se přebírají uživatelská vložení, vkládání položek a transformace křížového produktu ID uživatelů a ID položek jako vstup. Pro hlubokou část modelu se vkládání vektoru učí u jednotlivých funkcí kategorií. Spolu s dalšími numerickými vektory funkcí se tyto vektory pak dodávají do neuronové sítě s přímým kanálem. Rozsáhlá část a hluboká část jsou kombinovány sečtením konečných výstupů protokolu lichá jako předpovědi, která nakonec přechází na jednu běžnou funkci ztráty pro společné školení.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) Azure Machine Learning. 
