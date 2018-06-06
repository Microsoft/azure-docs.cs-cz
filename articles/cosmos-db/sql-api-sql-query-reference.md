---
title: 'Azure Cosmos DB: Referenční informace SQL syntaxe dotazu | Microsoft Docs'
description: Referenční dokumentace pro Azure Cosmos DB SQL dotazovací jazyk.
services: cosmos-db
author: LalithaMV
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2017
ms.author: laviswa
ms.openlocfilehash: 13337e7979a378382df5e62661b04bac8dffa689
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798827"
---
# <a name="azure-cosmos-db-sql-syntax-reference"></a>Azure Cosmos DB SQL reference syntaxe

Azure Cosmos DB podporuje dotazování dokumentů pomocí známých SQL (Structured Query Language) jako gramatika přes hierarchické dokumenty JSON bez nutnosti explicitního schématu nebo vytváření sekundárních indexů. Toto téma obsahuje referenční dokumentaci k nástroji pro dotaz jazyka SQL, který je kompatibilní s účty rozhraní SQL API.

Návod dotazovací jazyk SQL najdete v tématu [dotazy SQL pro Azure Cosmos DB](sql-api-sql-query.md).  
  
Také doporučujeme přejděte [Query Playground](http://www.documentdb.com/sql/demo) kde mohou zkuste Azure Cosmos DB a spouštění dotazů SQL na naší datové sadě.  
  
## <a name="select-query"></a>Vyberte možnost dotazu  
Načte z databáze dokumentů JSON. Podporuje vyhodnocení výrazu, projekce, filtrování a připojí.  V části syntaxe názvů jsou v tabulce konvencemi použitými pro popisující příkazů SELECT.  
  
**Syntaxe**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Poznámky**  
  
 Viz následující části Podrobnosti na každý klauzule:  
  
-   [Klauzule SELECT](#bk_select_query)  
  
-   [FROM – klauzule](#bk_from_clause)  
  
-   [Klauzule WHERE](#bk_where_clause)  
  
-   [Klauzuli ORDER by](#bk_orderby_clause)  
  
Klauzule v příkazu SELECT musejí být seřazeny, jak je uvedeno výše. Kterákoli z volitelné klauzule lze vynechat. Ale pokud volitelné klauzule používají, musí být ve správném pořadí.  
  
**Logické pořadí zpracování příkazu SELECT**  
  
Pořadí, ve kterém jsou zpracovány klauzule je:  

1.  [FROM – klauzule](#bk_from_clause)  
2.  [Klauzule WHERE](#bk_where_clause)  
3.  [Klauzuli ORDER by](#bk_orderby_clause)  
4.  [Klauzule SELECT](#bk_select_query)  

Všimněte si, že se jedná o liší od pořadí, ve kterém se zobrazí v syntaxi. Pořadí je tak, aby všechny nové symboly zavedených v klauzuli zpracovaná viditelné a zda lze použít v klauzulích zpracovat později. Pro instanci, jsou dostupné aliasy deklarované v klauzuli FROM tam, kde a klauzule FROM.  

**Prázdné znaky a komentáře**  

Všechny znaky prázdné znaky, které nejsou součástí řetězec v uvozovkách nebo identifikátor v uvozovkách nejsou součástí gramatika jazyka a jsou ignorovány během analýzy.  

Dotazovací jazyk podporuje komentáře styl T-SQL jako  

-   Příkaz jazyka SQL `-- comment text [newline]`  

Prázdné znaky a komentáře v není k dispozici žádné násobek gramatiku, musí se používají k oddělení tokeny. Pro instanci: `-1e5` je jeden číslo tokenu, chvíli`: – 1 e5` token znaménkem minus následuje identifikátor e5 a číslo 1.  

##  <a name="bk_select_query"></a> Klauzule SELECT  
Klauzule v příkazu SELECT musejí být seřazeny, jak je uvedeno výše. Kterákoli z volitelné klauzule lze vynechat. Ale pokud volitelné klauzule používají, musí být ve správném pořadí.  

**Syntaxe**  
```  
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumenty**  
  
 `<select_specification>`  
  
 Vlastnosti nebo hodnota, která má být vybrán pro sadu výsledků dotazu.  
  
 `'*'`  
  
Určuje, zda mají být načtena hodnota beze změn. Konkrétně Pokud zpracovaná hodnota objektu, budou všechny vlastnosti načteny.  
  
 `<object_property_list>`  
  
Určuje seznam vlastností, které mají být načteny. Každý vrácená hodnota bude objekt s byly zadány vlastnosti.  
  
`VALUE`  
  
Určuje, zda mají být načtena hodnota JSON místo kompletní objekt JSON. To, na rozdíl od `<property_list>` nezalamuje očekávaná hodnota v objektu.  
  
`<scalar_expression>`  
  
Výraz představující hodnotu počítaný. V tématu [skalární výrazy](#bk_scalar_expressions) podrobnosti.  
  
**Poznámky**  
  
`SELECT *` Syntaxe je platný, pokud klauzule FROM deklaruje právě jednu alias. `SELECT *` poskytuje identitu projekce, které mohou být užitečné, pokud není nutná žádná projekce. Vyberte * platí, pouze pokud je zadána klauzule FROM a zavedl pouze jeden vstupní zdroj.  
  
Všimněte si, že `SELECT <select_list>` a `SELECT *` jsou "syntaktické sugar" a může být případně vyjádřený pomocí jednoduchého příkazů SELECT, jak je uvedeno níže.  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     je ekvivalentní:  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     je ekvivalentní:  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Viz také**  
  
[Skalární výrazy](#bk_scalar_expressions)  
[Klauzule SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> FROM – klauzule  
Určuje zdroj nebo připojený k zdroje. V klauzuli FROM je volitelný. Pokud není zadaný, další klauzule bude proveden stále jako, pokud klauzule FROM zadaný jednotlivý dokument.  
  
**Syntaxe**  
  
```  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <collection_expression> [[AS] input_alias]  
        | input_alias IN <collection_expression>  
  
<collection_expression> ::=   
        ROOT   
     | collection_name  
     | input_alias  
     | <collection_expression> '.' property_name  
     | <collection_expression> '[' "property_name" | array_index ']'  
```  
  
**Argumenty**  
  
`<from_source>`  
  
Určuje zdroj dat s nebo bez alias. Pokud není zadán alias, bude odvodit z `<collection_expression>` pomocí následujících pravidel:  
  
-   Pokud ve výrazu název_kolekce, bude Název_kolekce použít jako alias.  
  
-   Pokud je výraz `<collection_expression>`, pak %{Property_Name/ pak %{Property_Name/ se použije jako alias. Pokud ve výrazu název_kolekce, bude Název_kolekce použít jako alias.  
  
STEJNĚ JAKO `input_alias`  
  
Určuje, že `input_alias` je sada hodnot vrácených základní výraz kolekce.  
 
`input_alias` V  
  
Určuje, že `input_alias` by měl představovat sadu hodnot získat iterování přes všechny elementy pole každé pole vrácené výrazem základní kolekce. Libovolná hodnota vrácený základní kolekce výraz, který není pole se ignoruje.  
  
`<collection_expression>`  
  
Určuje výraz kolekce, který má použít k načtení dokumenty.  
  
`ROOT`  
  
Určuje, že tento dokument se mají načíst výchozí kolekci aktuálně připojené.  
  
`collection_name`  
  
Určuje, že tento dokument mají být načtena ze zadané kolekci. Název kolekce musí odpovídat názvu aktuálně připojených do kolekce.  
  
`input_alias`  
  
Určuje, že tento dokument mají být načtena z jiného zdroje definované zadaný alias.  
  
`<collection_expression> '.' property_`  
  
Určuje v tomto dokumentu mají být načtena přímým přístupem `property_name` element pole vlastnosti nebo array_index pro všechny dokumenty načíst zadaný výraz kolekce.  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
Určuje v tomto dokumentu mají být načtena přímým přístupem `property_name` element pole vlastnosti nebo array_index pro všechny dokumenty načíst zadaný výraz kolekce.  
  
**Poznámky**  
  
Všechny aliasy zadáno nebo odvození v `<from_source>(`s) musí být jedinečný. Syntaxe `<collection_expression>.`%{Property_Name/ je stejný jako `<collection_expression>' ['"property_name"']'`. Druhé syntaxe však může být použita, pokud název vlastnosti obsahuje jiný identifikátor – znaky.  
  
**Chybí vlastnosti, chybějící pole prvky, není definovaná hodnoty zpracování**  
  
Pokud výraz kolekce přístup k vlastnosti nebo elementy pole a že hodnota neexistuje, bude se tato hodnota ignorována a není další zpracování.  
  
**Kolekce výraz kontextu rozsahu**  
  
Výraz kolekce může být celou kolekci nebo obor dokumentu:  
  
-   Výraz je celou kolekci, pokud je základní zdroj výrazu kolekce buď KOŘENOVÉ nebo `collection_name`. Takové výraz představuje sadu dokumenty načíst přímo z kolekce a není závislá na zpracování jiné kolekce výrazy.  
  
-   Výraz je dokument s rozsahem, pokud je základní zdroj výrazu kolekce `input_alias` zavedená dříve v dotazu. Takové výraz představuje sadu dokumenty získala při vyhodnocování výrazu kolekce v oboru jednotlivých dokumentů, které patří do sady přidružené ke kolekci alias.  Výsledná sada bude spojení získala při vyhodnocování výrazu kolekce pro jednotlivé dokumenty v sadě základní sady.  
  
**Spojení**  
  
V aktuální verzi podporuje Azure Cosmos DB vnitřní spojení. Připojení k další možnosti jsou chystaný.

Vnitřní spojení vést k dokončení smíšený produkt sad účastní spojení. Výsledek spojení N-způsob, jak je sada N element řazené kolekce členů, kde každá hodnota v řazené kolekci členů je spojena s alias nastavit účasti ve spojení a je přístupný pomocí odkazující na tento alias v jiných klauzulích.  
  
Připojení k vyhodnocení, závisí na oboru kontextu zúčastněných sad:  
  
-  Spojení mezi sadu kolekce A a celou kolekci nastavení B, výsledky v produktu mezi všechny elementy ve sady A a B.
  
-   Spojení mezi sada A a sada obor dokumentu B, výsledkem spojení všech sad získat vyhodnocením dokumentu obor sady B pro každý dokument z nastavit A.  
  
 V aktuální verzi maximálně jeden výraz celou kolekci podporuje procesor dotazů.  
  
**Příklady spojení:**  
  
Podívejme se na následující klauzule FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Umožní každý zdroj definovat `input_alias1, input_alias2, …, input_aliasN`. Tato klauzule FROM vrací sadu N-řazené kolekce členů (řazené kolekce členů s hodnotami N). Každá řazená kolekce členů má vyprodukované všechny aliasy kolekce iterování přes jejich příslušné sady hodnot.  
  
*Připojit ke Příklad 1, s 2 zdrojů:*  
  
- Umožní `<from_source1>` celou kolekci být a představují sadu {A, B, C}.  
  
- Umožní `<from_source2>` být obor dokumentu, odkazující na input_alias1 a představují sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} Pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- V klauzuli FROM `<from_source1> JOIN <from_source2>` bude mít za následek následující řazené kolekce členů:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*Příklad 2, s 3 zdroje připojení:*  
  
- Umožní `<from_source1>` celou kolekci být a představují sadu {A, B, C}.  
  
- Umožní `<from_source2>` být obor dokumentu odkazující na `input_alias1` a představují sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} Pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Umožní `<from_source3>` být obor dokumentu odkazující na `input_alias2` a představují sady:  
  
    {100, 200} pro `input_alias2 = 1,`  
  
    {300} Pro `input_alias2 = 3,`  
  
- V klauzuli FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` bude mít za následek následující řazené kolekce členů:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
> [!NOTE]
> Nedostatečná řazené kolekce členů pro jiné hodnoty `input_alias1`, `input_alias2`, pro kterou `<from_source3>` nevrátil žádné hodnoty.  
  
*Připojit ke příklad 3, s 3 zdrojů:*  
  
- Umožní < from_source1 > být celou kolekci a představují sadu {A, B, C}.  
  
- Umožní `<from_source1>` celou kolekci být a představují sadu {A, B, C}.  
  
- Umožní < from_source2 > být obor dokumentu odkazující input_alias1 a představují sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} Pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Umožní `<from_source3>` být omezená na `input_alias1` a představují sady:  
  
    {100, 200} pro `input_alias2 = A,`  
  
    {300} Pro `input_alias2 = C,`  
  
- V klauzuli FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` bude mít za následek následující řazené kolekce členů:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300) (C, 5, 300)  
  
> [!NOTE]
> To bylo způsobeno v smíšený produkt mezi `<from_source2>` a `<from_source3>` vzhledem k tomu, jak jsou omezená na stejné `<from_source1>`.  To bylo způsobeno 4 (2 x 2) řazených kolekcí členů má hodnotu, 0 řazené kolekce členů s hodnota B (1 × 0) a 2 (2 × 1) řazených kolekcí členů mají hodnotu C.  
  
**Viz také**  
  
 [Klauzule SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> Klauzule WHERE  
 Určuje podmínku vyhledávání pro dokumenty vrácené dotazem.  
  
 **Syntaxe**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumenty**  
  
-   `<filter_condition>`  
  
     Určuje podmínku, která má být splněny pro dokumenty, které má být vrácen.  
  
-   `<scalar_expression>`  
  
     Výraz představující hodnotu počítaný. Najdete v článku [skalární výrazy](#bk_scalar_expressions) podrobnosti.  
  
 **Poznámky**  
  
 Aby dokumentu, který má být vrácen výraz zadaný jako filtr musí podmínka vyhodnocena jako true. Pouze logickou hodnotu true budou splňovat podmínky, jakoukoli jinou hodnotu: nedefinované, null, hodnotu false, číslo, pole nebo objekt nebude splňují zadanou podmínku.  
  
##  <a name="bk_orderby_clause"></a> Klauzuli ORDER by  
 Určuje pořadí řazení výsledků vrácených dotazem.  
  
 **Syntaxe**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumenty**  
  
-   `<sort_specification>`  
  
     Určuje vlastnosti nebo výraz, podle kterého řazení sady výsledků dotazu. Sloupec pro řazení lze zadat jako název nebo sloupci alias.  
  
     Je možné zadat více řazení sloupců. Názvy sloupců musí být jedinečný. Definuje pořadí řazení sloupců v klauzuli ORDER by organizace ze seřazené sady. To znamená sadu výsledků dotazu je seřazen podle první vlastnost a potom tento seřazený seznam je seřazen podle druhý vlastností a tak dále.  
  
     Názvy sloupců, na který odkazuje klauzule ORDER BY musí odpovídat buď na sloupec v seznamu select nebo na sloupec definovaný v tabulce zadané v klauzuli FROM bez jakékoli nejednoznačnosti.  
  
-   `<sort_expression>`  
  
     Určuje vlastnosti jediné nebo výraz, podle kterého řazení sady výsledků dotazu.  
  
-   `<scalar_expression>`  
  
     Najdete v článku [skalární výrazy](#bk_scalar_expressions) podrobnosti.  
  
-   `ASC | DESC`  
  
     Určuje, že hodnoty ve vybraném sloupci by měly být seřazeny ve vzestupném nebo sestupném pořadí. ASC seřadí od nejnižší hodnoty po nejvyšší hodnotu. DESC seřadí od nejvyšší hodnoty nejnižší hodnotu. ASC je výchozí pořadí řazení. Hodnoty Null jsou považovány za nejnižší možné hodnoty.  
  
 **Poznámky**  
  
 I když gramatiky dotazů podporuje více pořadí podle vlastností, modulu runtime Azure Cosmos DB dotazu podporuje řazení pouze s jedinou vlastností a pouze s názvy vlastností, tj., není pro počítané vlastnosti. Řazení také vyžaduje, zásady indexování zahrnuje indexem rozsahu pro vlastnost a zadaného typu, s maximální přesnost. Naleznete v dokumentaci indexování zásady další podrobnosti.  
  
##  <a name="bk_scalar_expressions"></a> Skalární výrazy  
 Skalární výraz, který je kombinací symbolů a operátory, které lze vyhodnotit na získat jednu hodnotu. Jednoduché výrazy může být konstanty, odkazy na vlastnost, odkazuje na element pole, odkazy na alias nebo volání funkce. Jednoduché výrazy lze spojovat do složité výrazy pomocí operátorů.  
  
 Podrobnosti na hodnotách, které skalární výraz, který může mít najdete v tématu [konstanty](#bk_constants) části.  
  
 **Syntaxe**  
  
```  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Argumenty**  
  
-   `<constant>`  
  
     Představuje konstantní hodnotu. V tématu [konstanty](#bk_constants) podrobnosti.  
  
-   `input_alias`  
  
     Reprezentuje hodnotu definované `input_alias` počínaje `FROM` klauzule.  
    Tato hodnota představuje záruku není **nedefinované** –**nedefinované** hodnoty ve vstupu se přeskočí.  
  
-   `<scalar_expression>.property_name`  
  
     Reprezentuje hodnotu vlastnosti objektu. Pokud vlastnost neexistuje nebo je odkazováno vlastnost na hodnotu, která není objekt a potom vyhodnocen jako **nedefinované** hodnotu.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Reprezentuje hodnotu vlastnosti s názvem `property_name` nebo element pole s indexem `array_index` objekt nebo pole. Pokud neexistuje index vlastnost nebo pole nebo vlastnost nebo pole indexu je odkazováno na hodnotu, která není objekt nebo pole, pak je vyhodnocen nedefinovanou hodnotu.  
  
-   `unary_operator <scalar_expression>`  
  
     Představuje operátor, který se použije pro jednu hodnotu. V tématu [operátory](#bk_operators) podrobnosti.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Představuje operátor, který se použije pro dvě hodnoty. V tématu [operátory](#bk_operators) podrobnosti.  
  
-   `<scalar_function_expression>`  
  
     Reprezentuje hodnotu definované výsledku volání funkce.  
  
-   `udf_scalar_function`  
  
     Jméno uživatele definované skalární funkce.  
  
-   `builtin_scalar_function`  
  
     Název předdefinované skalární funkce.  
  
-   `<create_object_expression>`  
  
     Představuje hodnotu získat tak, že vytvoříte nový objekt v zadaných vlastností a jejich hodnoty.  
  
-   `<create_array_expression>`  
  
     Reprezentuje hodnotu získala při vytváření nové pole se zadanými hodnotami, jako elementy  
  
-   `parameter_name`  
  
     Reprezentuje hodnotu zadaný název parametru. Názvy parametrů musí mít jeden @ jako první znak.  
  
 **Poznámky**  
  
 Při volání předdefinované nebo uživatel definované skalární funkce musí být definovány všechny argumenty. Pokud některý z argumentů není definován, nebude volána funkce a výsledkem bude definován.  
  
 Při vytvoření objektu, bude přeskočen a nejsou zahrnuty v vytvořený objekt jakákoli vlastnost, která je přiřazena nedefinovanou hodnotu.  
  
 Při vytváření pole, libovolná hodnota elementu, který je přiřazen **nedefinované** hodnota bude přeskočen a nejsou zahrnuty v vytvořený objekt. To způsobí, že další definované elementu, který chcete provést jeho tak, že vytvořený pole nebude přeskočena indexy.  
  
##  <a name="bk_operators"></a> Operátory  
 Tato část popisuje podporované operátory. Každý operátor lze přiřadit k přesně jednu kategorii.  
  
 V tématu **operátor kategorie** tabulce podrobnosti ohledně zpracování **nedefinované** hodnoty, požadavky na typ vstupní hodnoty a zpracování hodnot s není odpovídající typy.  
  
 **Operátor kategorie:**  
  
|**Kategorie**|**Podrobnosti**|  
|-|-|  
|**Aritmetické operace**|Operátor očekává input(s) být čísel. Výstup je také číslo. Pokud je některý ze vstupních údajů **nedefinované** nebo typ než číslo potom výsledek **nedefinované**.|  
|**Bitový**|Operátor očekává input(s) být 32bitové číslo se znaménkem čísel. Výstup je také 32bitové číslo se znaménkem číslo.<br /><br /> Libovolná hodnota není celé číslo se zaokrouhlí. Kladné celé číslo se zaokrouhlí směrem dolů, záporné hodnoty zaokrouhlený nahoru.<br /><br /> Provedením poslední 32 bity jeho dva pro zápis doplňku budou převedeny jakoukoli hodnotu, která je mimo rozsah 32bitové celé číslo.<br /><br /> Pokud je některý ze vstupních údajů **nedefinované** nebo jiného typu než čísla, potom je **nedefinované**.<br /><br /> **Poznámka:** výše uvedené chování je kompatibilní s chováním bitový operátor jazyka JavaScript.|  
|**Logické**|Operátor očekává input(s) být Boolean(s). Výstup je také logickou hodnotu.<br />Pokud je některý ze vstupních údajů **nedefinované** nebo jiného typu než logickou hodnotu, bude výsledkem **nedefinované**.|  
|**Porovnání**|Operátor očekává input(s) stejného typu a nesmí být definován. Výstup je logická hodnota.<br /><br /> Pokud je některý ze vstupních údajů **nedefinované** nebo mají různé typy vstupních hodnot, potom je **nedefinované**.<br /><br /> V tématu **řazení hodnot pro porovnání** tabulky pro hodnotu řazení podrobnosti.|  
|**Řetězec**|Operátor očekává input(s) jako řetězec nebo řetězce. Výstup je také řetězec.<br />Pokud je některý ze vstupních údajů **nedefinované** nebo jiného typu než řetězec potom je **nedefinované**.|  
  
 **Unární operátory:**  
  
|**Název**|**Operátor**|**Podrobnosti**|  
|-|-|-|  
|**Aritmetické operace**|+<br /><br /> -|Vrátí číselnou hodnotu.<br /><br /> Bitovou negaci. Vrátí Negované číselnou hodnotu.|  
|**Bitový**|~|Ty, které se doplňku. Vrátí zbytek číselnou hodnotu.|  
|**Logické**|**NENÍ**|Negace. Vrátí Negované logická hodnota.|  
  
 **Binární operátory:**  
  
|**Název**|**Operátor**|**Podrobnosti**|  
|-|-|-|  
|**Aritmetické operace**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Přidání.<br /><br /> Odčítání.<br /><br /> Násobení.<br /><br /> Dělení.<br /><br /> Modulační.|  
|**Bitový**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Bitový operátor OR.<br /><br /> Bitové operace AND.<br /><br /> Bitové operace XOR.<br /><br /> Operátor posunu vlevo.<br /><br /> Posunutí doprava.<br /><br /> Posunutí doprava výplně nula.|  
|**Logické**|**A**<br /><br /> **OR**|Logické spojení. Vrátí **true** Pokud jsou oba argumenty **true**, vrátí **false** jinak.<br /><br /> Logické spojení. Vrátí **true** Pokud jsou oba argumenty **true**, vrátí **false** jinak.|  
|**Porovnání**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Rovná se. Vrátí **true** Pokud argumenty jsou stejné, vrátí **false** jinak.<br /><br /> Není rovno. Vrátí **true** Pokud argumenty nejsou stejné, vrátí **false** jinak.<br /><br /> Větší než. Vrátí **true** Pokud je větší než druhý argument, vrátí **false** jinak.<br /><br /> Větší než nebo rovna hodnotě. Vrátí **true** Pokud první argument je větší než nebo rovno druhý, vrátí **false** jinak.<br /><br /> Menší než. Vrátí **true** Pokud první argument je menší než druhý jeden návratový **false** jinak.<br /><br /> Menší než nebo rovno. Vrátí **true** Pokud první argument je menší než nebo rovna druhý, vrátí **false** jinak.<br /><br /> Sloučení. Vrátí druhý argument, pokud je první argument **nedefinované** hodnotu.|  
|**Řetězec**|**&#124;&#124;**|Zřetězení. Vrátí zřetězení oba argumenty.|  
  
 **Ternární operátory:**  
  
|Ternární operátor|?|Vrátí druhý argument, pokud se vyhodnotí jako první argument **true**; jinak vrátí třetí argument.|  
|-|-|-|  
  
 **Řazení hodnot pro porovnání**  
  
|**Typ**|**Pořadí hodnoty**|  
|-|-|  
|**Nedefinovaná**|Není porovnatelný.|  
|**Hodnotu Null**|Jedna hodnota: **hodnotu null.**|  
|**Číslo**|Fyzická reálné číslo.<br /><br /> Zápornou hodnotu Infinity je menší než ostatní číselnou hodnotu.<br /><br /> Je větší než jakoukoli jinou hodnotu, číslo kladné nekonečnou hodnotu. **NaN** hodnota není porovnatelný. Porovnávání s **NaN** bude mít za následek **nedefinované** hodnotu.|  
|**Řetězec**|Lexicographical pořadí.|  
|**Pole**|Žádné řazení, ale přiměřenou.|  
|**Objekt**|Žádné řazení, ale přiměřenou.|  
  
 **Poznámky**  
  
 V Azure DB Cosmos typy hodnot, často není známý, dokud se ve skutečnosti načítají z databáze. Za účelem podpory efektivní provádění dotazů, většina operátory má požadavky na typ strict. Operátory samy o sobě taky neprovádět implicitní převody.  
  
 To znamená, že jako dotaz: vybrat * z ROOT r kde r.Age = 21 vrátí jenom dokumenty s vlastností stáří rovná počtu 21. Dokumenty s vlastností stáří rovna řetězec "21" nebo "0021" řetězec nebude odpovídat jako výraz "21" = 21 vyhodnotí na nedefinovaný. To umožňuje lepší využití indexy, protože je vyhledávání určitou hodnotu (tj. počet 21) je rychlejší než vyhledávání nekonečný počet potenciální odpovídá (tj. počet 21 nebo řetězce "21", "021", "21.0"...). To se liší od způsobu JavaScript vyhodnotí operátory na hodnoty různých typů.  
  
 **Porovnání rovnosti pole a objekty a**  
  
 Porovnání hodnot pole nebo objekt pomocí operátorů rozsah (>, > =, <, < =) bude mít za následek není definována jako není pořadí na objekt nebo pole hodnot. Ale pomocí operátory rovnosti/nerovnosti (=,! =, <>) je podporována a hodnoty budou porovnány strukturálně.  
  
 Pole jsou stejné, pokud obě pole mají stejný počet elementů a prvky v odpovídajícím pozic jsou také stejné. Pokud není definovaná porovnání výsledků elementy v žádném páru, výsledku porovnání pole není definováno.  
  
 Objekty jsou stejné, pokud mají oba objekty stejné vlastnosti definované a jsou také stejné hodnoty odpovídající vlastnosti. Pokud není definovaná porovnání výsledků hodnoty vlastností v žádném páru, výsledku porovnání objekt není definován.  
  
##  <a name="bk_constants"></a> Konstanty  
 Konstanta, také známé jako literál nebo skalární hodnota, je symbol, který představuje konkrétní datové hodnoty. Formát konstanta závisí na datový typ hodnoty, který představuje.  
  
 **Podporované typy skalární data:**  
  
|**Typ**|**Pořadí hodnoty**|  
|-|-|  
|**Nedefinovaná**|Jedna hodnota: **Nedefinovaná**|  
|**Hodnotu Null**|Jedna hodnota: **hodnotu null.**|  
|**Logická hodnota**|Hodnoty: **false**, **true**.|  
|**Číslo**|Dvojitá přesnost s plovoucí desetinnou čárkou číslo, IEEE 754 standard.|  
|**Řetězec**|Pořadí nula nebo více znaků Unicode. Řetězce musí být uzavřena do jednoduchých nebo dvojitých uvozovek.|  
|**Pole**|Pořadí počtu nula či více elementů. Každý element může být hodnota všechny skalární datového typu, s výjimkou Undefined.|  
|**Objekt**|Neseřazený sadu nula nebo více dvojic název hodnota. Název je řetězec znaků Unicode, hodnota může být jakékoli skalární datového typu, s výjimkou **Undefined**.|  
  
 **Syntaxe**  
  
```  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **Argumenty**  
  
1.  `<undefined_constant>; undefined`  
  
     Představuje není definovaná hodnota typu Undefined.  
  
2.  `<null_constant>; null`  
  
     Představuje **null** hodnotu typu **Null**.  
  
3.  `<boolean_constant>`  
  
     Představuje konstanta typu Boolean.  
  
4.  `false`  
  
     Představuje **false** hodnota typu Boolean.  
  
5.  `true`  
  
     Představuje **true** hodnota typu Boolean.  
  
6.  `<number_constant>`  
  
     Představuje konstantu.  
  
7.  `decimal_literal`  
  
     Decimal literály jsou reprezentované pomocí zápisu s tečkou nebo exponenciální notace čísla.  
  
8.  `hexadecimal_literal`  
  
     Šestnáctkové literály jsou reprezentované pomocí předpony '0 x, za nímž následuje jeden nebo více šestnáctkové číslice čísla.  
  
9. `<string_constant>`  
  
     Představuje konstantu typu řetězec.  
  
10. `string _literal`  
  
     Textové literály jsou reprezentována posloupnost nula nebo více znaků Unicode nebo řídicí sekvence řetězců v kódu Unicode. Textové literály jsou uzavřená do jednoduchých uvozovek (apostrof: ') nebo dvojité uvozovky (uvozovky: ").  
  
 Jsou povoleny následující řídicí sekvence:  
  
|**Řídicí sekvence**|**Popis**|**Znak Unicode**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|dvojité uvozovky (")|U+0022|  
|\\\|obrácené lomítko (\\)|U+005C|  
|\\/|lomítko (/)|U+002F|  
|\b|BACKSPACE|U+0008|  
|\f|řídicí znak|U + 000C|  
|\n|Přechod na nový řádek|U + 000A|  
|\r|Návrat na začátek|U + 000D|  
|\t|tabulátor|U+0009|  
|\uXXXX|Znak Unicode definované 4 hexadecimální číslice.|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Pravidla výkonu dotazu  
 Aby dotaz nelze provést efektivně pro velké kolekce měla by používat filtry, které je možné dodávat prostřednictvím jednoho nebo více indexů.  
  
 Následující filtry se bude zvažovat indexu vyhledávání:  
  
-   Operátor rovnosti (=) pomocí výrazu cesty dokumentu a konstanta.  
  
-   Rozsah operátory (<, \<=, >, > =) s výraz cesty dokumentu a číselné konstanty.  
  
-   Výraz cesty dokumentu zastupuje všechny výraz, který identifikuje konstantní cestu v dokumentech z kolekce odkazovaná databáze.  
  
 **Výraz cesty dokumentu**  
  
 Výrazy cesty dokumentu jsou výrazy, cesta vlastnost nebo pole posuzovatelů indexer přes dokumentu pocházejících z databáze kolekce dokumentů. Tuto cestu můžete použít pro určení umístění hodnot odkazuje ve filtru přímo v rámci dokumentů v kolekci databáze.  
  
 Pro výraz považovat za výraz cesty dokumentu by měl:  
  
1.  Kořenové kolekce odkazujte přímo.  
  
2.  Odkaz na vlastnost nebo konstanta indexer pole některé výrazu cesta dokumentu  
  
3.  Referenční alias, který představuje výraz cesty některé dokumentu.  
  
     **Syntaxe konvence**  
  
     Následující tabulka popisuje konvence používají k popisu syntaxi následující odkaz SQL.  
  
    |**Konvence**|**Použít pro**|  
    |-|-|    
    |VELKÁ PÍSMENA|Klíčová slova velká a malá písmena.|  
    |malá písmena|Klíčová slova malá a velká písmena.|  
    |\<nonterminal >|Nonterminal, definované samostatně.|  
    |\<nonterminal >:: =|Syntaxe definice nonterminal.|  
    |other_terminal|Terminálové (token), podrobně popsané v slova.|  
    |Identifikátor|Identifikátor. Umožňuje následující pouze znaky: a – z A-Z 0-9 _First znak nemůže být číslice.|  
    |"řetězec"|Řetězec v uvozovkách. Umožňuje libovolný platný řetězec. Viz popis string_literal.|  
    |'symbol.|Literál symbol, který je součástí syntaxe.|  
    |&#124;(svislé čáry)|Alternativy pro položky syntaxe. Můžete vytvořit pouze jeden položky.|  
    |[] /(brackets)|Závorky uzavřete jeden nebo více nepovinných položek.|  
    |[,.. .n]|Označuje, že předchozí položce může být opakovaný n stanovený počet. Výskytů jsou oddělené čárkami.|  
    |[ ...n ]|Označuje, že předchozí položce může být opakovaný n stanovený počet. Výskytů se oddělují mezerami.|  
  
##  <a name="bk_built_in_functions"></a> Integrované funkce  
 Azure Cosmos DB poskytuje mnoho předdefinovaných funkcí SQL. Kategorie integrované funkce jsou uvedeny níže.  
  
|Funkce|Popis|  
|--------------|-----------------|  
|[Matematické funkce](#bk_mathematical_functions)|Matematické funkce provedení výpočtů, obvykle podle vstupní hodnoty, které jsou k dispozici jako argumenty a vrátí číselnou hodnotu.|  
|[Typ kontroly funkce](#bk_type_checking_functions)|Kontrola, zda funkce typů umožňují zkontrolujte typ výrazu v rámci dotazů SQL.|  
|[Funkce řetězců](#bk_string_functions)|Funkce řetězce provést operaci s vstupní hodnotu řetězce a vrátí řetězec, číselnou nebo logická hodnota.|  
|[Funkce pole](#bk_array_functions)|Funkce pole provést operaci na hodnotu vstupní pole a vrátí číselnou, logická hodnota nebo pole hodnota.|  
|[Prostorové funkce](#bk_spatial_functions)|Prostorové funkce provést operaci s vstupní hodnotu prostorový objekt a vrátit hodnotu číselná nebo logická hodnota.|  
  
###  <a name="bk_mathematical_functions"></a> Matematické funkce  
 Následující funkce provedení výpočtů, obvykle podle vstupní hodnoty, které jsou k dispozici jako argumenty a vrátí číselnou hodnotu.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[MEZNÍ HODNOTY](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[STUPŇŮ](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[NAPÁJENÍ](#bk_power)|  
|[RADIÁNECH](#bk_radians)|[ZAOKROUHLIT](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[HRANATÉ](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Vrátí absolutní hodnotu (kladné) zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje výsledky pomocí funkce ABS na tři odlišné počty.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Vrací úhel, v radiánech, jehož kosinus je zadaný číselný výraz. Zkratka Arkus.  
  
 **Syntaxe**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí ACOS-1.  
  
```  
SELECT ACOS(-1)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Vrací úhel, v radiánech, jehož sinus je zadaný číselný výraz. To je také označován Arkus sinus.  
  
 **Syntaxe**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí ASIN-1.  
  
```  
SELECT ASIN(-1)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Vrací úhel, v radiánech, jehož tangens je zadaný číselný výraz. To je také označován Arkus.  
  
 **Syntaxe**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí ATAN zadané hodnoty.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Vrací hlavní hodnotu tangens oblouk y / x, vyjádřené v radiánech.  
  
 **Syntaxe**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vypočítá ATN2 pro zadaný x a y součásti.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> MEZNÍ HODNOTY  
 Vrátí nejmenší hodnotu, celé číslo větší než nebo rovna hodnotě zadané číselný výraz.  
  
 **Syntaxe**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje kladné číselné, záporné a nulové hodnoty pomocí funkce mezní hodnoty.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Vrací trigonometrické kosinus určeného úhlu v radiánech v zadaným výrazem.  
  
 **Syntaxe**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vypočítá COS určeného úhlu.  
  
```  
SELECT COS(14.78)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Vrací trigonometrické kotangens zadaný úhel v radiánech v zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vypočítá COP z určeného úhlu.  
  
```  
SELECT COT(124.1332)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> STUPŇŮ  
 Vrací odpovídající úhel ve stupních pro úhlu uvedeného v radiánech.  
  
 **Syntaxe**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí počet stupňů v úhel radiánech PÍ/2.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Vrátí největší celé číslo menší než nebo rovna zadané číselný výraz.  
  
 **Syntaxe**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje kladné číselné, záporné a nulové hodnoty pomocí funkce FLOOR.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Vrátí hodnotu exponenciálního zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Poznámky**  
  
 Konstanta **e** (2.718281...), je základ přirozeného logaritmu.  
  
 Exponent číslo je konstanta **e** umocněné číslo. Například EXP(1.0) = e ^ 1.0 = 2.71828182845905 a EXP(10) = e ^ 10 = 22026.4657948067.  
  
 Exponent přirozený logaritmus čísla se číslo samotné: EXP (protokol (ne)) = n. A přirozený logaritmus čísla exponenciální je číslo samotné: protokolu (EXP (ne)) = n.  
  
 **Příklady**  
  
 Následující příklad deklaruje proměnné a vrátí hodnotu exponenciálního zadanou proměnnou (10).  
  
```  
SELECT EXP(10)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 Následující příklad vrátí hodnotu exponenciálního natural logarithm 20 a přirozený logaritmus exponenciální 20. Protože tyto funkce jsou inverzní funkce vzájemně, návratovou hodnotu s zaokrouhlení pro plovoucí matematické bodu v obou případech je 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> PROTOKOLU  
 Vrátí přirozený logaritmus zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
-   `base`  
  
     Nepovinný argument číselné, který nastaví základ pro logaritmus  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Poznámky**  
  
 Ve výchozím nastavení LOG() vrátí přirozený logaritmus. Základ logaritmu. můžete změnit na jinou hodnotu pomocí základní volitelný parametr.  
  
 Přirozený logaritmus je logaritmus s jejím základem **e**, kde **e** je přibližně rovna 2.718281828 nenormální konstanta.  
  
 Přirozený logaritmus čísla exponenciální je číslo samotné: protokolu (EXP (ne)) = n. A exponent přirozený logaritmus čísla je číslo samotné: EXP (protokol (ne)) = n.  
  
 **Příklady**  
  
 Následující příklad deklaruje proměnné a Vrátí logaritmus hodnotu zadanou proměnnou (10).  
  
```  
SELECT LOG(10)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 Následující příklad vypočítá protokol pro exponent číslo.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Vrátí logaritmus o základu 10 zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Poznámky**  
  
 Funkce LOG10 a POWER souvisejí nepřímo jednu na druhou. Například 10 ^ LOG10(n) = n.  
  
 **Příklady**  
  
 Následující příklad deklaruje proměnné a vrátí hodnotu LOG10 zadanou proměnnou (100).  
  
```  
SELECT LOG10(100)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Vrátí konstantní hodnotu čísla PÍ.  
  
 **Syntaxe**  
  
```  
PI ()  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí hodnotu čísla PÍ.  
  
```  
SELECT PI()  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> NAPÁJENÍ  
 Vrátí hodnotu zadaného výrazu na zadanou mocninu.  
  
 **Syntaxe**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
-   `y`  
  
     Je napájení, do kterého se mají zvýšit `numeric_expression`.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, vyvolání číslo exponentem 3 (datové krychle číslo).  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIÁNECH  
 Vrátí radiánech při zadání číselného výrazu, ve stupních, se.  
  
 **Syntaxe**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vezme jako vstupní údaje několik úhly a vrátí jejich příslušné hodnoty radián.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 Zde je sada výsledků.  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ZAOKROUHLIT  
 Vrátí číselnou hodnotu, zaokrouhlí na nejbližší celé číslo.  
  
 **Syntaxe**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 V následujícím příkladu se zaokrouhlí na následující kladné a záporné čísla na nejbližší celé číslo.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> PŘIHLÁŠENÍ  
 Vrátí kladnou (+ 1), nula (0) nebo záporné znaménko (-1) zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vrací hodnoty znaménko čísla z -2 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Vrací trigonometrické sinus určeného úhlu v radiánech v zadaným výrazem.  
  
 **Syntaxe**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vypočítá SIN z určeného úhlu.  
  
```  
SELECT SIN(45.175643)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Vrátí druhou odmocninu čísla zadaná číselná hodnota.  
  
 **Syntaxe**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí druhé odmocniny čísel 1 – 3.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> HRANATÉ  
 Vrátí druhou mocninu Zadaná číselná hodnota.  
  
 **Syntaxe**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí čtverce čísel 1 – 3.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Vrací tangens určeného úhlu v radiánech v zadaným výrazem.  
  
 **Syntaxe**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vypočítá tangens PI () / 2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 Vrátí číselnou hodnotu, zkrácen na nejbližší celé číslo.  
  
 **Syntaxe**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad zkrátí následující kladné a záporné čísla na nejbližší celé číslo.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> Typ kontroly funkce  
 Následující funkce podporují kontroly proti vstupní hodnoty typu a každý vrátit logickou hodnotu.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Vrátí logickou hodnotu udávající, pokud je typ zadaný výraz pole.  
  
 **Syntaxe**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je jakýkoli platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad ověří objekty JSON Boolean, číslo, řetězec hodnotu null, objekt, pole a nedefinované typy pomocí funkce IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Vrátí logickou hodnotu udávající, pokud typ zadaný výraz je logická hodnota.  
  
 **Syntaxe**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je jakýkoli platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad ověří objekty JSON Boolean, číslo, řetězec hodnotu null, objekt, pole a nedefinované typy pomocí funkce IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Vrátí logickou hodnotu udávající, pokud byla vlastnost přiřazenou hodnotu.  
  
 **Syntaxe**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je jakýkoli platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zjišťuje přítomnost vlastnosti v rámci zadaný dokument JSON. První vrací hodnotu true, protože "a" je k dispozici, ale druhý vrací hodnotu false, protože chybí "b".  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 Zde je sada výsledků.  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Vrátí logickou hodnotu udávající, pokud není typ zadaného výrazu hodnotu null.  
  
 **Syntaxe**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je jakýkoli platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad ověří objekty JSON Boolean, číslo, řetězec hodnotu null, objekt, pole a nedefinované typy pomocí funkce IS_NULL.  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Vrátí logickou hodnotu udávající, pokud typ zadaný výraz je číslo.  
  
 **Syntaxe**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je jakýkoli platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad ověří objekty JSON Boolean, číslo, řetězec hodnotu null, objekt, pole a nedefinované typy pomocí funkce IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Vrátí logickou hodnotu udávající, pokud typ zadaný výraz je objekt JSON.  
  
 **Syntaxe**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je jakýkoli platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad ověří objekty JSON Boolean, číslo, řetězec hodnotu null, objekt, pole a nedefinované typy pomocí funkce IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Vrátí logickou hodnotu udávající, pokud není typ zadaného výrazu jednoduchého typu (řetězec, logická hodnota, číselná nebo má hodnotu null).  
  
 **Syntaxe**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je jakýkoli platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad ověří objekty JSON Boolean, číslo, řetězec hodnotu null, objekt, pole a nedefinované typy pomocí funkce IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Vrátí logickou hodnotu udávající, pokud typ zadaný výraz obsahuje řetězec.  
  
 **Syntaxe**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je jakýkoli platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad ověří objekty JSON Boolean, číslo, řetězec hodnotu null, objekt, pole a nedefinované typy pomocí funkce IS_STRING.  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a> Řetězcové funkce  
 Následující skalární funkce provést operaci s vstupní hodnotu řetězce a vrátí řetězec, číselnou nebo logická hodnota.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[OBSAHUJE](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[DÉLKA](#bk_length)|  
|[NIŽŠÍ](#bk_lower)|[LTRIM](#bk_ltrim)|[NAHRADIT](#bk_replace)|  
|[REPLIKACE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[DÍLČÍ ŘETĚZEC](#bk_substring)|  
|[HORNÍ](#bk_upper)|||  
  
####  <a name="bk_concat"></a> CONCAT  
 Vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcové hodnoty.  
  
 **Syntaxe**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad vrací spojený řetězec ze zadaných hodnot.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> OBSAHUJE  
 Vrátí logická hodnota, která určuje zda první řetězec výraz obsahuje druhý.  
  
 **Syntaxe**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje Pokud "abc" "ab" a "d" obsahuje.  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Vrátí logická hodnota, která určuje zda první řetězec výraz končí druhý.  
  
 **Syntaxe**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí "abc" končí textem "b" a "bc".  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Vrátí počáteční pozici prvního výskytu druhý řetězec výrazu v rámci první zadaného řetězcového výrazu nebo -1, pokud není nalezen řetězec.  
  
 **Syntaxe**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí index různé dílčích řetězců uvnitř "abc".  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> VLEVO  
 Vrátí levé části řetězec s zadaný počet znaků.  
  
 **Syntaxe**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
-   `num_expr`  
  
     Je libovolný platný číselný výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad vrátí levé části "abc" pro různé hodnoty pro délku.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a> DÉLKA  
 Vrátí počet znaků ze zadaného řetězcového výrazu.  
  
 **Syntaxe**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad vrátí délku řetězce.  
  
```  
SELECT LENGTH("abc")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> NIŽŠÍ  
 Vrací výraz řetězce po převodu dat velké písmeno na malá písmena.  
  
 **Syntaxe**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak používat nižší v dotazu.  
  
```  
SELECT LOWER("Abc")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Vrací výraz řetězce po ho odebere úvodní mezery.  
  
 **Syntaxe**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak používat LTRIM uvnitř dotazu.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> NAHRADIT  
 Nahradí všechny výskyty zadaná řetězcová hodnota s jinou hodnotou řetězce.  
  
 **Syntaxe**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak se používá v dotazu.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLIKACE  
 Opakuje hodnotu řetězce zadaného počtu opakování.  
  
 **Syntaxe**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
-   `num_expr`  
  
     Je libovolný platný číselný výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak používat REPLIKACE v dotazu.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Vrátí obráceném pořadí řetězcovou hodnotu.  
  
 **Syntaxe**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak používat zpětného v dotazu.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> VPRAVO  
 Vrátí pravou část řetězec s zadaný počet znaků.  
  
 **Syntaxe**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
-   `num_expr`  
  
     Je libovolný platný číselný výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad vrátí pravou část "abc" pro různé hodnoty pro délku.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Vrací výraz řetězce po odebere koncové mezery.  
  
 **Syntaxe**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak používat RTRIM uvnitř dotazu.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Vrátí logická hodnota, která určuje zda první řetězec výraz začíná druhý.  
  
 **Syntaxe**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje, zda řetězec "abc" začíná "b" a "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> DÍLČÍ ŘETĚZEC  
 Vrátí část řetězcového výrazu od pozice s nulovým základem zadaný znak a pokračuje na určenou délku nebo na konci řetězce.  
  
 **Syntaxe**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
-   `num_expr`  
  
     Je libovolný platný číselný výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad vrátí dílčí řetězec "abc" spouštění v 1 a o délce 1 znak.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "b"}]  
```  
  
####  <a name="bk_upper"></a> HORNÍ  
 Vrací výraz řetězce po převodu dat malé písmeno na velká písmena.  
  
 **Syntaxe**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je jakýkoli platný řetězec výraz.  
  
 **Návratové typy**  
  
 Vrací výraz řetězce.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak používat horní v dotazu  
  
```  
SELECT UPPER("Abc")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Funkce pole  
 Provedení operace hodnota vstupní pole a vrátí číselnou, logická hodnota nebo pole hodnota následující skalární funkce  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Vrátí pole, které je výsledkem zřetězení dvě nebo více hodnot pole.  
  
 **Syntaxe**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Je jakýkoli platný pole výraz.  
  
 **Návratové typy**  
  
 Vrací výraz pole.  
  
 **Příklady**  
  
 Následující příklad postup řetězení dvěma poli.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Vrátí logickou hodnotu udávající, zda pole obsahuje zadanou hodnotu. Můžete zadat, pokud je shoda celé nebo jeho část. 

 **Syntaxe**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Je jakýkoli platný pole výraz.  
  
-   `expr`  
  
     Je jakýkoli platný výraz.  

-   `bool_expr`  
  
     Je libovolný logický výraz.       
  
 **Návratové typy**  
  
 Vrátí logickou hodnotu.  
  
 **Příklady**  
  
 Následující příklad postup kontroly členství v poli pomocí ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": true, "$2": false}]  
```  

 Následující příklad postup kontroly částečnou shodu JSON v poli pomocí ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 Zde je sada výsledků.  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Vrátí počet prvků výrazu zadané pole.  
  
 **Syntaxe**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Je jakýkoli platný pole výraz.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz.  
  
 **Příklady**  
  
 Následující příklad jak získat délka pole pomocí ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 Zde je sada výsledků.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Vrátí část výraz pole.
  
 **Syntaxe**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Je jakýkoli platný pole výraz.  
  
-   `num_expr`  
  
     Je libovolný platný číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí logickou hodnotu.  
  
 **Příklady**  
  
 Následující příklad jak získat součástí pomocí ARRAY_SLICE pole.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 Zde je sada výsledků.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a> Prostorové funkce  
 Následující skalární funkce provádění operací na prostorový objekt vstupní hodnotu a vrátí číslo nebo logická hodnota.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Vrací vzdálenost mezi dvěma GeoJSON bodu, mnohoúhelníku nebo LineString výrazy.  
  
 **Syntaxe**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Je jakýkoli platný objekt výraz GeoJSON bodu, mnohoúhelníku nebo LineString.  
  
 **Návratové typy**  
  
 Vrátí číselný výraz obsahující vzdálenost. Vyjadřuje se v měřidla pro výchozí odkaz na systém.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak vrátit všechny rodiny dokumenty, které jsou v rámci 30 km v zadaném umístění pomocí předdefinované funkci ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Zde je sada výsledků.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Vrátí hodnotu určující, zda je v prvním argumentu zadaného objektu GeoJSON (bod, mnohoúhelníku nebo LineString) v rámci GeoJSON (bod, mnohoúhelníku nebo LineString) v druhý argument logický výraz.  
  
 **Syntaxe**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Je jakýkoli platný objekt výraz GeoJSON bodu, mnohoúhelníku nebo LineString.  
 
-   `spatial_expr`  
  
     Je jakýkoli platný objekt výraz GeoJSON bodu, mnohoúhelníku nebo LineString.  
  
 **Návratové typy**  
  
 Vrátí logickou hodnotu.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak najít všechny rodiny dokumenty pomocí ST_WITHIN mnohoúhelníku.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Zde je sada výsledků.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Vrátí hodnotu označující, zda zadaného v prvním argumentu objektu GeoJSON (bod, mnohoúhelníku nebo LineString) protíná GeoJSON (bod, mnohoúhelníku nebo LineString) v druhý argument logický výraz.  
  
 **Syntaxe**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Je jakýkoli platný objekt výraz GeoJSON bodu, mnohoúhelníku nebo LineString.  
 
-   `spatial_expr`  
  
     Je jakýkoli platný objekt výraz GeoJSON bodu, mnohoúhelníku nebo LineString.  
  
 **Návratové typy**  
  
 Vrátí logickou hodnotu.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak najít všechny oblasti, která protíná s danou mnohoúhelníku.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Zde je sada výsledků.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Vrátí logickou hodnotu udávající, zda je zadaný výraz GeoJSON bodu, mnohoúhelníku nebo LineString platný.  
  
 **Syntaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Je platný výraz GeoJSON bodu, mnohoúhelníku nebo LineString.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak zkontrolovat, jestli je platný, pomocí ST_VALID bod.  
  
 Například tohoto bodu má hodnotu zeměpisnou šířku, která není v platném rozsahu hodnot [-90, 90], takže dotaz vrátí hodnotu false.  
  
 Pro mnohoúhelníky specifikace GeoJSON vyžaduje, aby poslední souřadnic pár zadat stejný jako první, chcete-li vytvořit uzavřený obrazec. Je třeba zadat body v rámci mnohoúhelníku v pořadí proti směru hodinových ručiček. Mnohoúhelníku zadaný v po směru hodinových ručiček pořadí představuje inverzní oblasti v něm.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 Zde je sada výsledků.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Vrátí hodnotu hodnotu JSON obsahující logickou hodnotu, pokud zadaný výraz GeoJSON bodu, mnohoúhelníku nebo LineString je platný a pokud neplatný, dále z důvodu jako hodnotu řetězce.  
  
 **Syntaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Je jakýkoli platný výraz bodu nebo mnohoúhelníku GeoJSON.  
  
 **Návratové typy**  
  
 Vrátí hodnotu hodnotu JSON obsahující logickou hodnotu, pokud zadaný výraz bodu nebo mnohoúhelníku GeoJSON je platný a pokud je neplatný, dále z důvodu jako hodnotu řetězce.  
  
 **Příklady**  
  
 Následující příklad postup kontroly platnosti (s podrobnostmi) pomocí ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 Zde je sada výsledků.  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>Další postup  
 [Syntaxe jazyka SQL a dotaz SQL pro Azure Cosmos DB](sql-api-sql-query.md)   
 [Dokumentace k Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
  
  
