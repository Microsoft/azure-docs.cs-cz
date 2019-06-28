---
title: Funkce systému
description: Další informace o funkce systému SQL ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 11a6fdad187670bcb5af4c56198fd7343680690d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342533"
---
# <a name="system-functions"></a>Funkce systému

 Cosmos DB poskytuje mnoho předdefinovaných funkcí SQL. Kategorie předdefinované funkce jsou uvedeny níže.  
  
|Funkce|Popis|  
|--------------|-----------------|  
|[Matematické funkce](#mathematical-functions)|Matematické funkce provádí výpočet, obvykle podle vstupní hodnoty, které jsou k dispozici jako argumenty a vrátit číselnou hodnotu.|  
|[Kontrola funkce typu](#type-checking-functions)|Funkce pro kontrolu typů umožňují zkontrolujte typ výrazu v rámci dotazů SQL.|  
|[Funkce řetězců](#string-functions)|Řetězcové funkce provádění operací na vstupní hodnotu řetězce a vrátí řetězec, číslo nebo logickou hodnotu.|  
|[Funkce pole](#array-functions)|Funkce pole provádění operací na hodnotu vstupního pole a vrácené číselné, logickou hodnotu nebo hodnotu pole.|
|[Funkce Date a Time](#date-time-functions)|Funkce date a time umožňují získat aktuální datum a čas UTC ve dvou formách; číselné timestamp, jehož hodnota je epocha Unix v milisekundách, nebo jako řetězec, který odpovídá formátu ISO 8601.|
|[Prostorové funkce](#spatial-functions)|Prostorové funkce provádění operací na prostorový vstupní hodnotu a vrátí číslo nebo logickou hodnotu.|  

Níže je seznam funkcí v jednotlivých kategoriích:

| Skupina – funkce | Operace |
|---------|----------|
| Matematické funkce | ABS, HORNÍ MEZ, EXP, FLOOR, PROTOKOLU, LOG10, POWER, KRUHOVÉ, PŘIHLÁŠENÍ, SQRT, ČTVEREC, TRUNC –, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN TAN |
| Kontrola typu funkce | IS_ARRAY – IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT –, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funkce řetězců | CONCAT, OBSAHUJE, ENDSWITH, INDEX_OF, VLEVO, DÉLKA, DOLNÍ, LTRIM, NAHRAĎTE, REPLIKACI, OBRÁTIT, VPRAVO, RTRIM, STARTSWITH, PODŘETĚZEC, HORNÍ |
| Funkce pole | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH a ARRAY_SLICE |
| Funkce Date a Time | GETCURRENTDATETIME GETCURRENTTIMESTAMP,  |
| Prostorové funkce | ST_DISTANCE ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Pokud aktuálně používáte uživatelem definované funkce (UDF) pro kterou předdefinované funkce je teď k dispozici, bude odpovídající integrované funkce pro spuštění rychlejší a efektivnější.

Hlavní rozdíl mezi funkcí ANSI SQL a Cosmos DB je, že funkce Cosmos DB jsou navrženy pro práci i s daty bez schématu a smíšené schématu. Například, pokud vlastnost chybí nebo má jiné než číselné hodnoty, jako jsou `unknown`, položky se přeskočila místo vrácení chyby.

##  <a name="mathematical-functions"></a> Matematické funkce  

Matematické funkce provádí výpočet, založené na vstupní hodnoty, které jsou k dispozici jako argumenty a vrátit číselnou hodnotu.

Můžete spouštět dotazy jako v následujícím příkladu:

```sql
    SELECT VALUE ABS(-4)
```

Výsledkem je:

```json
    [4]
```

Tady je tabulka podporovaných předdefinovaných matematických funkcí.
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[HORNÍ MEZ](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[STUPŇŮ](#bk_degrees)|  
|[EXP](#bk_exp)|[DOLNÍ MEZ](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[NAPÁJENÍ](#bk_power)|  
|[RADIANS](#bk_radians)|[ZAOKROUHLIT](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[ČTVEREC](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC –](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Vrátí absolutní hodnotu (pozitivní) zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad ukazuje výsledky pomocí funkce ABS na tři různá čísla.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Tady je sada výsledků.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Vrací úhel v radiánech, jehož kosinus je zadaný číselný výraz. Zkratka Arkus kosinus.  
  
 **Syntaxe**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vrátí ACOS -1.  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 Tady je sada výsledků.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Vrací úhel v radiánech, jehož sinus je zadaný číselný výraz. Označuje se také Arkus sinus.  
  
 **Syntaxe**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vrátí ASIN -1.  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 Tady je sada výsledků.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Vrací úhel v radiánech, jehož tangens odpovídá zadané číselný výraz. Označuje se také Arkus tangens.  
  
 **Syntaxe**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vrátí ATAN zadanou hodnotu.  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 Tady je sada výsledků.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Vrátí hlavní hodnotu arkustangens y / x, vyjádřené v radiánech.  
  
 **Syntaxe**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vypočítá ATN2 pro zadaný rozbočovač x a y komponenty.  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Tady je sada výsledků.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> HORNÍ MEZ  
 Vrátí nejmenší hodnotu celé číslo větší než nebo rovna zadané číselný výraz.  
  
 **Syntaxe**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad ukazuje kladná číselná, záporná a nulové hodnoty funkcí mezní hodnoty.  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Tady je sada výsledků.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Vrátí trigonometrických kosinus úhlu určeného v radiánech v zadaným výrazem.  
  
 **Syntaxe**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vypočítá COS určeného úhlu.  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 Tady je sada výsledků.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Vrátí trigonometrických kotangens úhlu určeného v radiánech v zadané číselný výraz.  
  
 **Syntaxe**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vypočítá COT určeného úhlu.  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 Tady je sada výsledků.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> STUPŇŮ  
 Vrátí odpovídající úhel ve stupních, kterým je úhel zadaný v radiánech.  
  
 **Syntaxe**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vrátí počet stupňů v úhel PÍ/2 radiánů.  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Tady je sada výsledků.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Vrátí největší celé číslo menší nebo rovna zadané číselný výraz.  
  
 **Syntaxe**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad ukazuje kladná číselná, záporná a nulová s FLOOR – funkce.  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Tady je sada výsledků.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Vrátí hodnotu exponenciální zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Poznámky**  
  
  Konstanta **e** (2.718281...), je základ přirozeného logaritmu.  
  
  Exponent číslo je konstanta **e** umocněné na čísla. Například EXP(1.0) = e ^ 1.0 = 2.71828182845905 a EXP(10) = e ^ 10 = 22026.4657948067.  
  
  Exponent přirozený logaritmus čísla číslo se sama: EXP (protokol (n).) = n. A přirozený logaritmus čísla exponenciální je číslo sama: PROTOKOL (EXP (n).) = n.  
  
  **Příklady**  
  
  Následující příklad deklaruje proměnnou a vrátí hodnotu exponenciální hodnotu zadanou proměnnou (10).  
  
```  
SELECT EXP(10) AS exp  
```  
  
 Tady je sada výsledků.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 Následující příklad vrátí hodnotu exponenciální natural logarithm 20 a přirozený logaritmus exponenciální 20. Protože tyto funkce jsou inverzní funkce navzájem, vrácená hodnota s zaokrouhlení s plovoucí desetinnou čárkou matematické bod v obou případech je 20.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Tady je sada výsledků.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> PROTOKOL  
 Vrátí přirozený logaritmus zadaného číselný výraz.  
  
 **Syntaxe**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
- `base`  
  
   Volitelné číselný argument, který nastaví základ logaritmu.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Poznámky**  
  
  Ve výchozím nastavení LOG() vrátí přirozený logaritmus. Základ logaritmu na jinou hodnotu můžete změnit pomocí volitelného parametru základní.  
  
  Přirozený logaritmus se logaritmus o základu **e**, kde **e** rovná nenormální – konstanta přibližně 2.718281828.  
  
  Přirozený logaritmus čísla exponenciální je číslo sama: PROTOKOL (EXP (n).) = n. A exponent přirozený logaritmus čísla je číslo sama: EXP (protokol (n).) = n.  
  
  **Příklady**  
  
  Následující příklad deklaruje proměnnou a Vrátí logaritmus o hodnotu zadanou proměnnou (10).  
  
```  
SELECT LOG(10) AS log  
```  
  
 Tady je sada výsledků.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 Následující příklad vypočítá protokolu pro exponent číslo.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Tady je sada výsledků.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Vrátí logaritmus o základu base 10 zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Poznámky**  
  
  LOG10 a výkonu funkce jsou nepřímo sebou mají nějaký vztah. Například 10 ^ LOG10(n) = n.  
  
  **Příklady**  
  
  Následující příklad deklaruje proměnnou a vrátí LOG10 hodnotu zadanou proměnnou (100).  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 Tady je sada výsledků.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Vrátí konstantní hodnotu čísla PÍ.  
  
 **Syntaxe**  
  
```  
PI ()  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vrátí hodnotu čísla PÍ.  
  
```  
SELECT PI() AS pi 
```  
  
 Tady je sada výsledků.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> NAPÁJENÍ  
 Vrátí hodnotu zadaného výrazu na zadanou mocninu.  
  
 **Syntaxe**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
- `y`  
  
   Jsou možnosti, na který se má použít `numeric_expression`.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad ukazuje vyvolání číslo exponentem 3 (datové krychle čísla).  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Tady je sada výsledků.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Radians vrátí, pokud je zadán číselný výraz ve stupních.  
  
 **Syntaxe**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vezme jako vstupní údaje několik úhly a vrátí jejich odpovídající hodnoty radián.  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  Tady je sada výsledků.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ZAOKROUHLIT  
 Vrátí číselnou hodnotu, zaokrouhlený na nejbližší celočíselnou hodnotu.  
  
 **Syntaxe**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Poznámky**
  
  Operaci zaokrouhlení provádí následuje střední zaokrouhlení směrem od nuly. Pokud je vstup číselný výraz, který spadá do rozmezí dvou celých čísel přesně výsledkem bude nejbližší celočíselnou hodnotu od nuly.  
  
  |<numeric_expression>|Zaokrouhleno|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0,5|1|
  |6.5000|7||
  
  **Příklady**  
  
  V následujícím příkladu se zaokrouhlí na následující kladná a záporná čísla na nejbližší celé číslo.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Tady je sada výsledků.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> PŘIHLÁŠENÍ  
 Vrátí kladné (+ 1), nula (0) nebo záporné znaménko (-1), z určeného číselného výrazu.  
  
 **Syntaxe**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vrátí hodnoty znaménko čísel od -2 do 2.  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Tady je sada výsledků.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Vrátí trigonometrických sinus úhlu určeného v radiánech v zadaným výrazem.  
  
 **Syntaxe**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vypočítá SIN určeného úhlu.  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 Tady je sada výsledků.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Vrátí druhou odmocninu zadanou číselnou hodnotu.  
  
 **Syntaxe**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vrátí druhé odmocniny čísla 1-3.  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 Tady je sada výsledků.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> ČTVEREC  
 Vrátí druhou mocninu zadané číselnou hodnotu.  
  
 **Syntaxe**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vrátí druhých mocnin čísla 1-3.  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Tady je sada výsledků.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Vrátí tangens úhlu určeného v radiánech v zadaným výrazem.  
  
 **Syntaxe**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vypočítá tangens (PÍ) / 2.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 Tady je sada výsledků.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC –  
 Vrátí číselnou hodnotu, zkrácen na nejbližší celočíselnou hodnotu.  
  
 **Syntaxe**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   Je číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  V následujícím příkladu se zkrátí na následující kladná a záporná čísla na nejbližší celočíselnou hodnotu.  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Tady je sada výsledků.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>Kontrola funkce typu

Funkce kontroly typu umožňují zkontrolujte typ výrazu v rámci dotazu SQL. Kontrola typu funkce můžete použít k určení typů vlastností v rámci položky v reálném čase, když jsou proměnné nebo neznámý. Tady je tabulka funkcí podporovaných předdefinovaných kontroly typů:

Následující funkce podporují typ kontroly proti vstupní hodnoty a vrátí hodnotu typu Boolean.  
  
||||  
|-|-|-|  
|[IS_ARRAY –](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT –](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY –  
 Vrátí logickou hodnotu označující, zda je typ z určeného výrazu pole.  
  
 **Syntaxe**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Je libovolný platný výraz.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce is_array –.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Tady je sada výsledků.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Vrátí logickou hodnotu označující, pokud typ z určeného výrazu je logická hodnota.  
  
 **Syntaxe**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Je libovolný platný výraz.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Tady je sada výsledků.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Vrátí logickou hodnotu označující, pokud vlastnost byla přiřazena hodnota.  
  
 **Syntaxe**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Je libovolný platný výraz.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad zkontroluje přítomnost vlastnosti v rámci zadaného dokumentu JSON. První vrátí hodnotu true, protože "a" je k dispozici, ale druhá vrátí hodnotu false, protože chybí "b".  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Tady je sada výsledků.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Vrátí logickou hodnotu označující, zda je typ z určeného výrazu hodnotu null.  
  
 **Syntaxe**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Je libovolný platný výraz.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce IS_NULL.  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Tady je sada výsledků.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Vrátí logickou hodnotu označující, pokud typ z určeného výrazu je číslo.  
  
 **Syntaxe**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Je libovolný platný výraz.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Tady je sada výsledků.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT –  
 Vrátí logickou hodnotu označující, pokud je typ z určeného výrazu objektu JSON.  
  
 **Syntaxe**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Je libovolný platný výraz.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce is_object –.  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Tady je sada výsledků.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Vrátí logickou hodnotu označující, zda je typ z určeného výrazu jednoduchého typu (string, Boolean, číselné nebo null).  
  
 **Syntaxe**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Je libovolný platný výraz.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Tady je sada výsledků.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Vrátí logickou hodnotu označující, pokud je typ z určeného výrazu string.  
  
 **Syntaxe**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Je libovolný platný výraz.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce IS_STRING.  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Tady je sada výsledků.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>Řetězcové funkce

Následující skalární funkce provádění operací na vstupní hodnotu řetězce a vrátí řetězec, číslo nebo logickou hodnotu:
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[OBSAHUJE](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[DÉLKA](#bk_length)|  
|[NIŽŠÍ](#bk_lower)|[LTRIM](#bk_ltrim)|[NAHRADIT](#bk_replace)|  
|[REPLIKACE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[DÍLČÍ ŘETĚZEC](#bk_substring)|[ToString](#bk_tostring)|
|[UVOLNĚNÍ DOČASNÉ PAMĚTI](#bk_trim)|[HORNÍ](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 Vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcových hodnot.  
  
 **Syntaxe**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad vrací spojený řetězec ze zadaných hodnot.  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Tady je sada výsledků.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> OBSAHUJE  
 Vrátí hodnotu typu Boolean označující, zda řetězec prvního výrazu obsahuje druhý.  
  
 **Syntaxe**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad zkontroluje Pokud "abc" obsahuje "ab" a "d".  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Tady je sada výsledků.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce končí na druhý.  
  
 **Syntaxe**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad vrátí "abc" končí "b" a "bc".  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Tady je sada výsledků.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Vrátí počáteční pozici prvního výskytu druhý řetězec výrazu v rámci prvního zadaného řetězcového výrazu nebo -1, pokud není nalezen řetězec.  
  
 **Syntaxe**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad vrátí index různých podřetězců uvnitř "abc".  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Tady je sada výsledků.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> DOLEVA  
 Vrátí levou část řetězec zadaný počet znaků.  
  
 **Syntaxe**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
- `num_expr`  
  
   Je jakýkoli platný číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad vrátí levou část "abc" pro různé hodnoty pro délku.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Tady je sada výsledků.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> DÉLKA  
 Vrátí počet znaků ze zadaného řetězcového výrazu.  
  
 **Syntaxe**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad vrátí délku řetězce.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 Tady je sada výsledků.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> NIŽŠÍ  
 Vrátí řetězcový výraz po převedení dat velkým písmenem na malá písmena.  
  
 **Syntaxe**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad ukazuje způsob použití nižší v dotazu.  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 Tady je sada výsledků.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Vrátí řetězcový výraz po odebere úvodní mezery.  
  
 **Syntaxe**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak použít LTRIM uvnitř dotazu.  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Tady je sada výsledků.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> NAHRADIT  
 Nahradí všechny výskyty zadaná řetězcová hodnota s jinou hodnotou řetězce.  
  
 **Syntaxe**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak se používá v dotazu.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Tady je sada výsledků.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLIKACE  
 Opakuje hodnotu řetězce zadaného počtu opakování.  
  
 **Syntaxe**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
- `num_expr`  
  
   Je jakýkoli platný číselný výraz. Pokud num_expr je záporný nebo nekonečnou, výsledek není definován.

  > [!NOTE]
  > Maximální délka výsledku je 10 000 znaků tedy (length(str_expr) * num_expr) < = 10 000.
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak pomocí provádí REPLIKACI v dotazu.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 Tady je sada výsledků.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Vrátí hodnotu řetězce obráceném pořadí.  
  
 **Syntaxe**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak použít reverzní v dotazu.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 Tady je sada výsledků.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> DOPRAVA  
 Vrátí část reprezentující správný řetězec zadaný počet znaků.  
  
 **Syntaxe**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
- `num_expr`  
  
   Je jakýkoli platný číselný výraz.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad vrátí pravou část "abc" pro různé hodnoty pro délku.  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Tady je sada výsledků.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Vrátí řetězcový výraz po odebere koncové mezery.  
  
 **Syntaxe**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak použít RTRIM uvnitř dotazu.  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Tady je sada výsledků.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce začíná druhé.  
  
 **Syntaxe**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad zkontroluje, pokud se řetězec "abc" začíná písmenem "b" a "a".  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Tady je sada výsledků.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Vrátí výraz přeložit na pole. Pokud výraz nelze přeložit, vrátí nedefinovaný.  
  
 **Syntaxe**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argumenty**  
  
- `expr`  
  
   Je libovolný platný skalární výraz, který má být vyhodnocen jako výraz pole JSON. Všimněte si, že vnořené řetězcové hodnoty musí být napsané v uvozovkách platný. Podrobnosti o formátu JSON najdete v tématu [json.org](https://json.org/)
  
  **Návratové typy**  
  
  Vrátí výraz pole nebo není definován.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak se chová StringToArray do různých typů. 
  
 Následují příklady s platným vstupem.

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Tady je sada výsledků.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Následuje příklad neplatný vstup. 
   
 Jednoduchých uvozovek a být v rámci pole nejsou platný kód JSON.
I když jsou platné v rámci dotazu, se nebude analyzovat na platné pole. Buď musí být uvozena řetězců v rámci pole řetězce "[\\"\\"]" nebo okolních uvozovek musí být jeden "[" "]".

```
SELECT
    StringToArray("['5','6','7']")
```

Tady je sada výsledků.

```
[{}]
```

Následují příklady neplatný vstup.
   
 Výraz předaný bude možné analyzovat jako pole JSON. Následující není vyhodnocen na typ pole a tak vrátit nedefinovaný.
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Tady je sada výsledků.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 Vrátí výraz přeložit na logickou hodnotu. Pokud výraz nelze přeložit, vrátí nedefinovaný.  
  
 **Syntaxe**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argumenty**  
  
- `expr`  
  
   Je libovolný platný skalární výraz, který má být vyhodnocen jako logický výraz.  
  
  **Návratové typy**  
  
  Vrátí logického výrazu nebo není definován.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak se chová StringToBoolean do různých typů. 
 
 Následují příklady s platným vstupem.

Prázdný znak je povolen pouze před nebo za "true"/ "false".

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Tady je sada výsledků.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

Následují příklady s neplatný vstup.

 Logické hodnoty jsou malá a velká písmena a musí být zapsaný s všechna malá písmena, to znamená "true" a "false".

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Tady je sada výsledků.  
  
```  
[{}]
``` 

Výraz předaný bude možné analyzovat jako logický výraz; tyto vstupy není vyhodnocen na typu Boolean a vrátit tak nedefinovaný.

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Tady je sada výsledků.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Vrátí výraz převedeny na hodnotu null. Pokud výraz nelze přeložit, vrátí nedefinovaný.  
  
 **Syntaxe**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argumenty**  
  
- `expr`  
  
   Je libovolný platný skalární výraz, který má být vyhodnocen jako výraz hodnotu null.
  
  **Návratové typy**  
  
  Vrací výraz hodnotu null nebo undefined.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak se chová StringToNull do různých typů. 

Následují příklady s platným vstupem.

 Prázdný znak je povolen pouze před nebo po "null".

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Tady je sada výsledků.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

Následují příklady s neplatný vstup.

Hodnota null je velká a malá písmena a musí být zapsaný s všechna malá písmena, například "null".

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Tady je sada výsledků.  
  
```  
[{}]
```  

Výraz předaný bude možné analyzovat jako výrazu hodnotu null; Zadejte hodnotu null a vrátit tak nedefinované nevyhodnocují tyto vstupy.

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Tady je sada výsledků.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 Vrátí výraz přeložit na číslo. Pokud výraz nelze přeložit, vrátí nedefinovaný.  
  
 **Syntaxe**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argumenty**  
  
- `expr`  
  
   Je libovolný platný skalární výraz, který má být vyhodnocen jako výraz JSON číslo. Čísla ve formátu JSON musí být celé číslo nebo plovoucí desetinnou čárkou. Podrobnosti o formátu JSON najdete v tématu [json.org](https://json.org/)  
  
  **Návratové typy**  
  
  Vrátí číslo výraz nebo není definován.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak se chová StringToNumber do různých typů. 

Prázdný znak je povolen pouze před nebo po číslo.

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Tady je sada výsledků.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

Ve formátu JSON musí být platné číslo být celé číslo nebo plovoucí číslo bodu.

```  
SELECT   
    StringToNumber("0xF")
```  
  
 Tady je sada výsledků.  
  
```  
{{}}
```  

Výraz předaný bude možné analyzovat jako číslo výrazu; Zadejte číslo a vrátit tak nedefinované nevyhodnocují tyto vstupy. 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Tady je sada výsledků.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 Vrátí výraz přeložen na objekt. Pokud výraz nelze přeložit, vrátí nedefinovaný.  
  
 **Syntaxe**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argumenty**  
  
- `expr`  
  
   Je libovolný platný skalární výraz, který má být vyhodnocen jako výraz objektu JSON. Všimněte si, že vnořené řetězcové hodnoty musí být napsané v uvozovkách platný. Podrobnosti o formátu JSON najdete v tématu [json.org](https://json.org/)  
  
  **Návratové typy**  
  
  Vrátí objektový výraz nebo není definován.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak se chová StringToObject do různých typů. 
  
 Následují příklady s platným vstupem.

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Tady je sada výsledků.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Následují příklady s neplatný vstup.
I když jsou platné v rámci dotazu, se nebude analyzovat na platné objekty. Řetězce v rámci řetězce objektu musí buď být uvozeny řídicími znaky "{\\"\\":\\" str\\"}" nebo okolních uvozovek musí být jeden ' {"a": "str"} ".

Jednoduché uvozovky kolem názvy vlastností nejsou platný kód JSON.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Tady je sada výsledků.

```  
[{}]
```  

Názvy vlastností bez okolních uvozovek nejsou platný kód JSON.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Tady je sada výsledků.

```  
[{}]
``` 

Následují příklady s neplatný vstup.

 Výraz předaný bude možné analyzovat jako objekt JSON; tyto vstupy není vyhodnocen na typ objektu a tak vrátit nedefinovaný.

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Tady je sada výsledků.

```
[{}]
```

####  <a name="bk_substring"></a> DÍLČÍ ŘETĚZEC  
 Vrátí část řetězcového výrazu počínaje pozice s nulovým základem zadaný znak a pokračuje na určenou délku nebo na konci řetězce.  
  
 **Syntaxe**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
- `num_expr`  
  
   Je jakýkoli platný číselný výraz k označení počáteční a koncový znak.    
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad vrátí podřetězec "abc", spouštění na 1 a pro o délce 1 znak.  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Tady je sada výsledků.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Vrátí řetězcovou reprezentaci skalární výraz. 
  
 **Syntaxe**  
  
```  
ToString(<expr>)
```  
  
 **Argumenty**  
  
- `expr`  
  
   Je libovolný platný skalární výraz.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak se chová ToString do různých typů.   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 Tady je sada výsledků.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 Daný následující vstup:
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 Následující příklad ukazuje, jak lze ToString s jinými funkcemi řetězec jako CONCAT.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

Tady je sada výsledků.  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
Daný následující vstup.
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
Následující příklad ukazuje, jak lze ToString s jinými funkcemi řetězec jako nahradit.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
Tady je sada výsledků.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> UVOLNĚNÍ DOČASNÉ PAMĚTI  
 Vrátí řetězcový výraz po odstraní úvodní a koncové prázdné znaky.  
  
 **Syntaxe**  
  
```  
TRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak použít uvnitř dotazu uvolnění dočasné paměti.  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Tady je sada výsledků.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> HORNÍ  
 Vrátí řetězcový výraz po převedení data znaků na malá písmena na velká písmena.  
  
 **Syntaxe**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Je libovolný výraz platný řetězec.  
  
  **Návratové typy**  
  
  Vrátí řetězcový výraz.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak používat horní v dotazu  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 Tady je sada výsledků.  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>Funkce pole

Následující skalární funkce provádění operací na hodnotu vstupního pole a vrátit číselné, hodnota logická hodnota nebo pole:
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Vrátí pole, která je výsledkem zřetězení dvou nebo více hodnot pole.  
  
 **Syntaxe**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumenty**  
  
- `arr_expr`  
  
   Je libovolný výraz, platným polem.  
  
  **Návratové typy**  
  
  Vrátí výraz pole.  
  
  **Příklady**  
  
  Následující příklad jak ke zřetězení dvou polí.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Tady je sada výsledků.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Vrátí logickou hodnotu označující, zda pole obsahuje zadanou hodnotu. Částečné nebo úplné shody objektu můžete zkontrolovat pomocí logický výraz v příkazu. 

**Syntaxe**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumenty**  
  
- `arr_expr`  
  
   Je libovolný výraz, platným polem.  
  
- `expr`  
  
   Je libovolný platný výraz.  

- `bool_expr`  
  
   Je libovolný výraz boolean. Pokud je nastavena na "true'and, pokud je hodnota zadaného hledání objektu, příkaz vyhledá částečnou shodu (hledání objektu je podmnožinou některý z objektů). Pokud je nastavena na hodnotu 'false', tento příkaz zkontroluje úplná shoda všech objektů v poli. Výchozí hodnota, pokud není zadán je false. 
  
  **Návratové typy**  
  
  Vrátí logickou hodnotu.  
  
  **Příklady**  
  
  Následující příklad postup kontroly členství v poli pomocí ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Tady je sada výsledků.  
  
```  
[{"b1": true, "b2": false}]  
```  

Následující příklad návod k ověření pro částečnou shodu JSON v poli pomocí ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Tady je sada výsledků.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Vrátí počet prvků výrazu určeném poli.  
  
 **Syntaxe**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumenty**  
  
- `arr_expr`  
  
   Je libovolný výraz, platným polem.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu.  
  
  **Příklady**  
  
  Následující příklad jak získat délku pole pomocí ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Tady je sada výsledků.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Vrátí část reprezentující výraz pole.
  
 **Syntaxe**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumenty**  
  
- `arr_expr`  
  
   Je libovolný výraz, platným polem.  
  
- `num_expr`  
  
   Založený na nule číselný index, na kterém má být pole. Záporné hodnoty slouží k určení počátečního indexu relativnímu k poslední prvek pole tedy -1 odkazy na poslední prvek v poli.  

- `num_expr`  

   Maximální počet prvků v výsledným polem.    

  **Návratové typy**  
  
  Vrátí výraz pole.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak získat jiné řezy k poli pomocí ARRAY_SLICE.  
  
```  
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Tady je sada výsledků.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>Datum a čas – funkce

Následující skalární funkce umožňují získat aktuální datum a čas UTC ve dvou formách; číselné timestamp, jehož hodnota je epocha Unix v milisekundách, nebo jako řetězec, který odpovídá formátu ISO 8601. 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GETCURRENTDATETIME
 Vrátí aktuální datum UTC a času jako řetězec ve formátu ISO 8601.
  
 **Syntaxe**
  
```
GETCURRENTDATETIME ()
```
  
  **Návratové typy**
  
  Vrátí aktuální čas UTC datum a čas ISO 8601 řetězcovou hodnotu. 

  To je vyjádřená ve formátu RRRR-MM-DDThh:mm:ss.sssZ kde:
  
  |||
  |-|-|
  |RRRR|čtyřmístný rok|
  |MM|dvoumístným měsícem (01 = January, atd.)|
  |DD|dvěma číslicemi den v měsíci (01 do 31)|
  |T|signifier pro začátek prvků času|
  |hh|Hodina dvou číslic (00 do 23)|
  |mm|dvě číslice minut (00 do 59)|
  |ss|dvě číslice sekund (00 do 59)|
  |.sss|tři číslice desetinné zlomků sekund|
  |Z|Označení UTC (Coordinated Universal Time)||
  
  Další podrobnosti o formátu ISO 8601, naleznete v tématu [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **Poznámky**

  GETCURRENTDATETIME je Nedeterministický funkce. 
  
  Vrácený výsledek je čas UTC (Coordinated Universal Time).

  **Příklady**  
  
  Následující příklad ukazuje, jak získat aktuální datum čas UTC pomocí integrované funkce GetCurrentDateTime.
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 Tady je Ukázková sada výsledků.
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GETCURRENTTIMESTAMP
 Vrátí počet milisekund, které uplynuly od 00:00:00 čtvrtek, 1. ledna 1970. 
  
 **Syntaxe**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **Návratové typy**  
  
  Vrátí číselnou hodnotu, aktuální počet milisekund uplynulých od epochy Unix to znamená počet milisekund, které uplynuly od 00:00:00 čtvrtek, 1. ledna 1970.

  **Poznámky**

  GETCURRENTTIMESTAMP je Nedeterministický funkce.
  
  Vrácený výsledek je čas UTC (Coordinated Universal Time).

  **Příklady**  
  
  Následující příklad ukazuje, jak získat aktuální časové razítko pomocí integrované funkce GetCurrentTimestamp.
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 Tady je Ukázková sada výsledků.
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>Prostorové funkce

Cosmos DB podporuje následující předdefinované funkce Otevřít geoprostorové W3c (OGC) pro geoprostorové dotazování. Následující skalární funkce provádění operací na prostorový vstupní hodnotu a vrátí číslo nebo logickou hodnotu.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Vrací vzdálenost mezi dvěma GeoJSON bodu mnohoúhelníku či LineString výrazy.  
  
 **Syntaxe**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
- `spatial_expr`  
  
   Je libovolný platný výraz, objektu GeoJSON bodu mnohoúhelníku či LineString.  
  
  **Návratové typy**  
  
  Vrátí hodnotu číselného výrazu obsahujícího vzdálenost. Vyjadřuje se v metrech pro výchozí odkaz na systém.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak vrátí všechny rodiny dokumenty, které jsou v rámci 30 km pomocí integrované funkce ST_DISTANCE zadaného umístění. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Tady je sada výsledků.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Vrací výraz Boolean určující, zda je objekt GeoJSON (bodu, mnohoúhelník nebo LineString) zadané v prvním argumentu v rámci GeoJSON (bodu, mnohoúhelník nebo LineString) v druhý argument.  
  
 **Syntaxe**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
- `spatial_expr`  
  
   Je libovolný platný výraz, objektu GeoJSON bodu mnohoúhelníku či LineString.  
 
- `spatial_expr`  
  
   Je libovolný platný výraz, objektu GeoJSON bodu mnohoúhelníku či LineString.  
  
  **Návratové typy**  
  
  Vrátí logickou hodnotu.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak najít všechny řady dokumenty v rámci pomocí ST_WITHIN mnohoúhelníku.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Tady je sada výsledků.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Vrací výraz Boolean určující, zda objekt GeoJSON (bodu, mnohoúhelník nebo LineString) zadané v prvním argumentu protíná GeoJSON (bodu, mnohoúhelník nebo LineString) v druhý argument.  
  
 **Syntaxe**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
- `spatial_expr`  
  
   Je libovolný platný výraz, objektu GeoJSON bodu mnohoúhelníku či LineString.  
 
- `spatial_expr`  
  
   Je libovolný platný výraz, objektu GeoJSON bodu mnohoúhelníku či LineString.  
  
  **Návratové typy**  
  
  Vrátí logickou hodnotu.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak najít všechny oblasti, které průnik s danou mnohoúhelníku.  
  
```  
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Tady je sada výsledků.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Vrátí logickou hodnotu označující, zda je zadaný výraz GeoJSON bodu mnohoúhelníku či LineString platný.  
  
 **Syntaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumenty**
  
- `spatial_expr`  
  
   Je libovolný platný výraz GeoJSON bodu mnohoúhelníku či LineString.  
  
  **Návratové typy**  
  
  Vrátí logický výraz.  
  
  **Příklady**  
  
  Následující příklad ukazuje, jak zkontrolovat, zda je platný pomocí ST_VALID bod.  
  
  Například tento bod má hodnotu šířky, který není v platném rozsahu hodnot [-90, 90], takže dotaz vrátí hodnotu false.  
  
  Mnohoúhelníky vyžaduje specifikaci GeoJSON poslední souřadnic pár poskytuje by měl být stejný jako první, chcete-li vytvořit zavřeného tvaru. Body v rámci mnohoúhelníku musí zadat v pořadí proti směru hodinových ručiček. Mnohoúhelník zadat v pořadí po směru hodinových ručiček představuje inverzní oblasti v rámci něj.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Tady je sada výsledků.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Vrátí hodnotu JSON obsahující logická hodnota, pokud zadaný výraz GeoJSON bodu mnohoúhelníku či LineString je platný a pokud není platný, kromě důvod jako hodnotu řetězce.  
  
 **Syntaxe**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **Argumenty**  
  
- `spatial_expr`  
  
   Je libovolný platný výraz bodu nebo mnohoúhelník GeoJSON.  
  
  **Návratové typy**  
  
  Vrátí hodnotu JSON obsahující logická hodnota, pokud zadaný výraz bodu nebo mnohoúhelník GeoJSON je platný a pokud není platný, kromě důvod jako hodnotu řetězce.  
  
  **Příklady**  
  
  Následující příklad návod k ověření platnosti (s podrobnostmi) pomocí ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Tady je sada výsledků.  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Další postup

- [Úvod do služby Azure Cosmos DB](introduction.md)
- [UDFs](sql-query-udfs.md)
- [agregace](sql-query-aggregates.md)