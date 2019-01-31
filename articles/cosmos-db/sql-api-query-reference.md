---
title: Syntaxe jazyka SQL ve službě Azure Cosmos DB
description: Tento článek popisuje syntaxi jazyka dotazů SQL v Azure Cosmos DB, různé operátory a klíčová slova k dispozici v tomto jazyce.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: reference
ms.date: 12/07/2018
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: bfc3ed5553802c8a87776dc1a5372bc27ac8d13d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475182"
---
# <a name="sql-language-reference-for-azure-cosmos-db"></a>Referenční příručka jazyka SQL pro službu Azure Cosmos DB 

Azure Cosmos DB podporuje dotazování dokumentů pomocí známých SQL (Structured Query Language), jako je gramatika přes hierarchické dokumenty JSON bez nutnosti explicitního schématu nebo vytváření sekundárních indexů. Tento článek poskytuje dokumentaci pro syntaxi jazyka dotazů SQL, který je kompatibilní s účty SQL API. Postup ukázky dotazů SQL, najdete v části [dotazy SQL ve službě Cosmos DB](how-to-sql-query.md).  
  
Přejděte [Query Playground](https://www.documentdb.com/sql/demo) kde zkuste Cosmos DB a spouštět dotazy SQL proti naší datové sadě.  
  
## <a name="select-query"></a>Zpracování dotazu SELECT  
Každý dotaz se skládá z klauzule SELECT a volitelné a klauzulí WHERE za standardy ANSI SQL. Pro každý dotaz, obvykle je vypočten zdroji v klauzuli FROM. Filtr v klauzuli WHERE je pak použije ve zdroji se načíst podmnožinu dokumentů JSON. Nakonec se používá klauzuli SELECT do projektu požadované hodnoty JSON v seznamu select. Konvence pro příkazy SELECT popisující jsou uvedených v části konvence syntaxe. Příklady najdete v tématu [Příklady zpracování dotazu SELECT](how-to-sql-query.md#SelectClause)
  
**Syntaxe**  
  
```sql
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Poznámky**  
  
 Viz následující oddíly podrobnosti na každou klauzuli:  
  
-   [Klauzule SELECT](#bk_select_query)    
-   [FROM – klauzule](#bk_from_clause)    
-   [Klauzule WHERE](#bk_where_clause)    
-   [ORDER BY – klauzule](#bk_orderby_clause)  
  
Klauzule v příkazu SELECT musejí být seřazeny, jak je znázorněno výše. Některý volitelný klauzule lze vynechat. Ale v případě volitelná klauzule používají, musí být ve správném pořadí.  
  
### <a name="logical-processing-order-of-the-select-statement"></a>Logické pořadí zpracování příkazu SELECT  
  
Pořadí, ve kterém jsou zpracovány klauzulí je:  

1.  [FROM – klauzule](#bk_from_clause)  
2.  [Klauzule WHERE](#bk_where_clause)  
3.  [ORDER BY – klauzule](#bk_orderby_clause)  
4.  [Klauzule SELECT](#bk_select_query)  

Všimněte si, že se liší od pořadí, v jakém jsou uvedeny v syntaxi. Řazení je tak, aby všechny nové symboly zavedených v klauzuli zpracovaných vidí a můžou používat v klauzulích zpracovány později. Pro instanci, jsou dostupné aliasy, které jsou deklarované v klauzuli FROM tam, kde a klauzule FROM.  

### <a name="whitespace-characters-and-comments"></a>Prázdné znaky a komentáře  

Všechny prázdné znaky, které nejsou součástí řetězec v uvozovkách nebo identifikátor v uvozovkách nejsou součástí jazyk gramatiky a ignorují během analýzy.  

Dotazovací jazyk podporuje komentáře stylu jazyka T-SQL jako  

-   Příkaz jazyka SQL `-- comment text [newline]`  

Zatímco prázdné znaky a komentáře nemají žádné význam v gramatice, musíte použít k oddělení tokeny. Například: `-1e5` je jednotné číslo token, nějakou`: – 1 e5` minus token následuje číslo 1 a identifikátor e5.  

##  <a name="bk_select_query"></a> Klauzule SELECT  
Klauzule v příkazu SELECT musejí být seřazeny, jak je znázorněno výše. Některý volitelný klauzule lze vynechat. Ale v případě volitelná klauzule používají, musí být ve správném pořadí. Příklady najdete v tématu [Příklady zpracování dotazu SELECT](how-to-sql-query.md#SelectClause).

**Syntaxe**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumenty**  
  
- `<select_specification>`  

  Vlastnosti nebo hodnota, která má být vybrána sada výsledků.  
  
- `'*'`  

  Určuje, že mají být načtena hodnota beze změn. Konkrétně, pokud je zpracovaných hodnota objekt, budou načítat všechny vlastnosti.  
  
- `<object_property_list>`  
  
  Určuje seznam vlastností, které se mají načíst. Každá vrácená hodnota bude objekt s vlastnostmi zadán.  
  
- `VALUE`  

  Určuje, že hodnota JSON má být načtena namísto kompletního objektu JSON. To, na rozdíl od `<property_list>` nezalamuje očekávaná hodnota do objektu.  
  
- `<scalar_expression>`  

  Výraz představující hodnotu, která chcete vypočítat. Zobrazit [skalární výrazy](#bk_scalar_expressions) podrobné informace.  
  
**Poznámky**  
  
`SELECT *` Syntaxe je platná pouze pokud klauzule FROM deklaroval přesně jeden alias. `SELECT *` poskytuje identitu projekce, které mohou být užitečné v případě potřeby žádná projekce. Vyberte * je platná pouze pokud je zadaná klauzule FROM a zavedl pouze jednoho vstupního zdroje.  
  
Obě `SELECT <select_list>` a `SELECT *` jsou "syntaktické sugar" a může být také vyjádřena pomocí jednoduchých příkazů SELECT, jak je znázorněno níže.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   je ekvivalentní:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   je ekvivalentní:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Viz také**  
  
[Skalární výrazy](#bk_scalar_expressions)  
[Klauzule SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> FROM – klauzule  
Určuje zdroj nebo zdroje připojené k doméně. V klauzuli FROM je nepovinný, pokud je zdroj filtrovaná nebo předpokládané později v dotazu. Účelem tuto klauzuli je určení zdroje dat, na kterém musí pracovat dotazu. Běžně celého kontejneru se zdroji, ale jeden místo toho zadejte podmnožinu kontejneru. Pokud tato klauzule není zadán, bude spuštěn další klauzule stále jakoby z klauzule zadaný jednotlivý dokument. Příklady najdete v tématu [z příkladů – klauzule](how-to-sql-query.md#FromClause)
  
**Syntaxe**  
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
**Argumenty**  
  
- `<from_source>`  
  
  Určuje zdroj dat, s nebo bez něj alias. Pokud není zadaný alias, bude odvodit z `<container_expression>` pomocí následujících pravidel:  
  
  -  Pokud má výraz hodnotu container_name, bude container_name použít jako alias.  
  
  -  Pokud má výraz hodnotu `<container_expression>`, pak %{Property_Name/ pak %{Property_Name/ se použije jako alias. Pokud má výraz hodnotu container_name, bude container_name použít jako alias.  
  
- STEJNĚ JAKO `input_alias`  
  
  Určuje, že `input_alias` je sada hodnot vrácených základní výraz kontejneru.  
 
- `input_alias` INDIE  
  
  Určuje, že `input_alias` by měly představovat sadu hodnot získala iterace přes všechny prvky pole každé pole vrácené výrazem základní kontejneru. Libovolnou hodnotu vrácenou základní výraz kontejneru, který není pole se ignoruje.  
  
- `<container_expression>`  
  
  Určuje výraz kontejneru se použije k načtení dokumentů.  
  
- `ROOT`  
  
  Určuje, že tento dokument by mělo být získáno z výchozí hodnoty, aktuálně připojeného kontejneru.  
  
- `container_name`  
  
  Určuje, že tento dokument by měl být načten ze zadaného kontejneru. Název kontejneru musí odpovídat názvu kontejneru aktuálně připojeni.  
  
- `input_alias`  
  
  Určuje, že tento dokument by měl být načten z jiného zdroje určené zadaný alias.  
  
- `<container_expression> '.' property_`  
  
  Určuje tento dokument by mělo být získáno díky přístupu `property_name` vlastnost nebo array_index prvek pole pro všechny dokumenty načte zadaný výraz kontejneru.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Určuje tento dokument by mělo být získáno díky přístupu `property_name` vlastnost nebo array_index prvek pole pro všechny dokumenty načte zadaný výraz kontejneru.  
  
**Poznámky**  
  
K dispozici všechny aliasy nebo odvozen `<from_source>(`s) musí být jedinečný. Syntaxe `<container_expression>.`%{Property_Name/ je stejný jako `<container_expression>' ['"property_name"']'`. Druhá možnost syntaxi však můžete použít, pokud název vlastnosti obsahuje znak identifikátoru.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>zpracování chybí vlastnosti chybějící prvky pole a nedefinované hodnoty
  
Pokud výraz kontejneru, který přistupuje k vlastnosti nebo prvky pole a že hodnota neexistuje, tuto hodnotu budou ignorovány a další nebyl zpracován.  
  
### <a name="container-expression-context-scoping"></a>Zkoumání kontextu výrazu kontejneru  
  
Kontejner výrazu může být kontejner rozsahem nebo rozsahem dokumentu:  
  
-   Výraz je kontejner s rozsahem, pokud podkladový zdroj výraz kontejneru je buď ROOT nebo `container_name`. Takový výraz představuje sadu dokumentů načíst přímo z kontejneru a není závislá na zpracování jiných výrazech kontejneru.  
  
-   Výraz je dokument s rozsahem, pokud je podkladový zdroj výrazu kontejneru `input_alias` zavedené dříve v dotazu. Takový výraz představuje sadu dokumentů získanou vyhodnocením výrazu kontejneru v rámci jednotlivých dokumentů, které patří do sady spojenými s daným kontejnerem alias.  Výsledná sada bude sjednocení sad získanou vyhodnocením výrazu kontejner pro všechny dokumenty v základní sadě.  
  
### <a name="joins"></a>Spojení 
  
V aktuální verzi Cosmos DB podporuje vnitřní spojení. Zobrazování poruch se připojení k další možnosti. 

Vnitřní spojení za následek úplnou smíšený produkt sad účastní spojení. Výsledek spojení N-způsob, jak je sada N prvek řazené kolekce členů, kde každá hodnota řazené kolekce členů je přidružen alias nastavit účast ve spojení a je přístupný pomocí odkazu na tento alias v jiných klauzule. Příklady najdete v tématu [příklady – klíčové slovo JOIN](how-to-sql-query.md#Joins)
  
Vyhodnocení spojení závisí na kontext rozsah zúčastněných sad:  
  
-  Spojení mezi container-set A a rozsahem kontejneru nastavit B, výsledkem smíšený produkt všechny prvky v sadách A a B.
  
-   Spojení mezi sada A a rozsahem dokumentu sada B, výsledkem sjednocení všech sad získanou vyhodnocením rozsahem dokumentu nastavte B pro každý dokument z A.  
  
 V aktuální verzi maximálně jeden výraz s rozsahem kontejner podporuje procesor dotazů.  
  
### <a name="examples-of-joins"></a>Příklady spojení  
  
Podívejme se na následující klauzule FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Umožnit každému zdroji definovat `input_alias1, input_alias2, …, input_aliasN`. Tato klauzule FROM vrací sadu N-řazených kolekcí členů (řazené kolekce členů s N hodnot). Všechny řazené kolekce členů obsahuje hodnoty vytvořené ve všechny aliasy kontejneru iterování celého jejich příslušných sad.  
  
**Příklad 1** -2 zdroje  
  
- Umožní `<from_source1>` být kontejner rozsahem a reprezentaci sady {A, B, C}.  
  
- Umožní `<from_source2>` být dokument s rozsahem odkazující na input_alias1 a představují sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Klauzule FROM `<from_source1> JOIN <from_source2>` bude mít za následek následující řazených kolekcí členů:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Příklad 2** -3 zdrojů  
  
- Umožní `<from_source1>` být kontejner rozsahem a reprezentaci sady {A, B, C}.  
  
- Umožní `<from_source2>` být odkazující na dokument s rozsahem `input_alias1` a představují sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Umožní `<from_source3>` být odkazující na dokument s rozsahem `input_alias2` a představují sady:  
  
    {100, 200} pro `input_alias2 = 1,`  
  
    {300} pro `input_alias2 = 3,`  
  
- Klauzule FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` bude mít za následek následující řazených kolekcí členů:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Chybějící řazených kolekcí členů pro ostatní hodnoty `input_alias1`, `input_alias2`, pro kterou `<from_source3>` nevrátil žádné hodnoty.  
  
**Příklad 3** -3 zdrojů  
  
- Umožní < from_source1 > se s rozsahem kontejneru a reprezentaci sady {A, B, C}.  
  
- Umožní `<from_source1>` být kontejner rozsahem a reprezentaci sady {A, B, C}.  
  
- Umožní < from_source2 > se s rozsahem dokumentu odkazující input_alias1 a představují sady:  
  
    {1, 2} pro `input_alias1 = A,`  
  
    {3} pro `input_alias1 = B,`  
  
    {4, 5} pro `input_alias1 = C,`  
  
- Umožní `<from_source3>` omezené na `input_alias1` a představují sady:  
  
    {100, 200} pro `input_alias2 = A,`  
  
    {300} pro `input_alias2 = C,`  
  
- Klauzule FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` bude mít za následek následující řazených kolekcí členů:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), C, 4, 300, (C, 5, 300)  
  
  > [!NOTE]
  > Výsledkem byl smíšený produkt mezi `<from_source2>` a `<from_source3>` protože obě jsou omezená na stejný `<from_source1>`.  Výsledkem byl 4 (2 x 2) řazené kolekce členů s hodnotou A 0 řazené kolekce členů s hodnotou B (1 x 0) a 2 (2 × 1) řazené kolekce členů s hodnotou C.  
  
**Viz také**  
  
 [Klauzule SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> Klauzule WHERE  
 Určuje podmínku vyhledávání pro dokumenty vrácené dotazem. Příklady najdete v tématu [ukázky použití klauzule WHERE](how-to-sql-query.md#WhereClause)
  
 **Syntaxe**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumenty**  
  
-   `<filter_condition>`  
  
     Určuje podmínku, která má být splněny pro dokumenty, které se mají vrátit.  
  
-   `<scalar_expression>`  
  
     Výraz představující hodnotu, která chcete vypočítat. Zobrazit [skalární výrazy](#bk_scalar_expressions) podrobné informace.  
  
 **Poznámky**  
  
 Aby dokumentu, který má být vrácen výraz zadaný jako filtr musí podmínka vyhodnocena na hodnotu true. Pouze pro logickou hodnotu true budou splňují zadanou podmínku, jakákoli jiná hodnota: undefined, null, hodnotu false, číslo, pole nebo objekt nebude splňují zadanou podmínku.  
  
##  <a name="bk_orderby_clause"></a> ORDER BY – klauzule  
 Určuje pořadí řazení pro výsledky vrácené dotazem. Příklady najdete v tématu [příklady klauzule ORDER BY](how-to-sql-query.md#OrderByClause)
  
 **Syntaxe**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumenty**  
  
-   `<sort_specification>`  
  
     Určuje vlastnost nebo výraz, podle kterého chcete řazení sady výsledků dotazu. Sloupec pro řazení lze zadat jako název nebo sloupci alias.  
  
     Je možné zadat více sloupců seřadit. Názvy sloupců musí být jedinečné. Definuje pořadí řazení sloupců v klauzuli ORDER by organizace sady seřazených výsledků. To znamená sada výsledků je seřazený podle první vlastnost a potom tuto seřazený seznam je seřazen podle druhý vlastnosti a tak dále.  
  
     Názvy sloupců odkazovat v klauzuli ORDER by musí odpovídat na sloupec v seznamu select nebo sloupce definované v tabulce zadaný v klauzuli FROM bez jakékoli nejednoznačnosti.  
  
-   `<sort_expression>`  
  
     Určuje jednu vlastnost nebo výraz, podle kterého chcete řazení sady výsledků dotazu.  
  
-   `<scalar_expression>`  
  
     Zobrazit [skalární výrazy](#bk_scalar_expressions) podrobné informace.  
  
-   `ASC | DESC`  
  
     Určuje, zda mají být řazeny hodnot v zadaném sloupci ve vzestupném nebo sestupném pořadí. ASC řadí od nejnižší hodnoty po nejvyšší hodnotu. DESC seřadí od nejvyšší hodnotu na nejnižší hodnotu. ASC se použije výchozí pořadí řazení. Hodnoty Null jsou považovány za nejnižší možné hodnoty.  
  
 **Poznámky**  
  
 I když gramatiky dotazů podporuje více pořadí podle vlastností, modul runtime dotazu Cosmos DB podporuje řazení pouze proti jedné vlastnosti a pouze názvy vlastností (ne před vypočítané vlastnosti). Řazení také vyžaduje, zásady indexování obsahuje index rozsahu pro vlastnost a zadaného typu s nejvyšší přesností. Odkazovat na indexování dokumentace k zásadám pro další podrobnosti.  
  
##  <a name="bk_scalar_expressions"></a> Skalární výrazy  
 Skalární výraz, který je kombinací symbolů a operátorů, které lze vyhodnotit na získání jedné hodnoty. Jednoduché výrazy mohou být konstanty, odkazy na vlastnosti, odkazy na prvky pole, odkazy na alias nebo volání funkce. Jednoduché výrazy je možné kombinovat do složité výrazy pomocí operátorů. Příklady najdete v tématu [příklady skalární výrazy](how-to-sql-query.md#scalar-expressions)
  
 Podrobnosti na hodnoty, které mohou mít skalární výraz, který najdete v tématu [konstanty](#bk_constants) oddílu.  
  
 **Syntaxe**  
  
```sql  
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
  
     Reprezentuje hodnotu konstanty. Zobrazit [konstanty](#bk_constants) podrobné informace.  
  
-   `input_alias`  
  
     Představuje hodnoty definované `input_alias` zavedený `FROM` klauzuli.  
    Tato hodnota je zaručeno, že nebudou **nedefinované** –**nedefinované** hodnoty ve vstupu se přeskočí.  
  
-   `<scalar_expression>.property_name`  
  
     Reprezentuje hodnotu vlastnosti objektu. Vlastnost neexistuje nebo vlastnost se odkazuje na hodnotu, která není objekt a potom je výraz vyhodnocen **nedefinované** hodnotu.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Reprezentuje hodnotu vlastnosti s názvem `property_name` nebo element pole s indexem `array_index` objekt nebo pole. Pokud vlastnost nebo pole index neexistuje nebo index vlastnost nebo pole se odkazuje na hodnotu, která není objekt nebo pole a pak je výraz vyhodnocen nedefinovaná hodnota.  
  
-   `unary_operator <scalar_expression>`  
  
     Představuje operátor, který se použije na jednu hodnotu. Zobrazit [operátory](#bk_operators) podrobné informace.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Představuje operátor, který se použije na dvě hodnoty. Zobrazit [operátory](#bk_operators) podrobné informace.  
  
-   `<scalar_function_expression>`  
  
     Představuje hodnou definovanou ve výsledku volání funkce.  
  
-   `udf_scalar_function`  
  
     Název definovaný uživatelem skalární funkci.  
  
-   `builtin_scalar_function`  
  
     Název integrovanou skalární funkci.  
  
-   `<create_object_expression>`  
  
     Představuje hodnotu získat tak, že vytvoříte nový objekt v zadaných vlastností a jejich hodnoty.  
  
-   `<create_array_expression>`  
  
     Reprezentuje hodnotu získala při vytváření nové pole pomocí zadaných hodnot jako prvky  
  
-   `parameter_name`  
  
     Reprezentuje hodnotu zadaný název parametru. Názvy parametrů musí mít jeden \@ jako první znak.  
  
 **Poznámky**  
  
 Při volání skalární funkce vestavěný nebo uživatelem definované všechny argumenty musí být definovaný. Pokud některý z argumentů není definován, nebude volána funkce a bude výsledek nedefinovaný.  
  
 Při vytvoření objektu, bude přeskočen a není součástí vytvořený objekt jakákoli vlastnost, která je přiřazena hodnota undefined.  
  
 Při vytváření pole, libovolný element, který je přiřazen **nedefinované** hodnota bude přeskočen a není součástí vytvořený objekt. To způsobí, že další definovaný prvek k provedení jeho tak, že vytvořené pole nebude přeskočena indexy.  
  
##  <a name="bk_operators"></a> Operátory  
 Tato část popisuje podporované operátory. Každý operátor je možné přiřadit přesně jednu kategorii.  
  
 Naleznete v tématu **operátor kategorie** níže uvedené tabulky Podrobnosti týkající se zpracování **nedefinované** hodnoty, požadavky na typ pro vstupní hodnoty a zpracování hodnot se neshodují s typy.  
  
 **Operátor kategorie:**  
  
|**Kategorie**|**Podrobnosti**|  
|-|-|  
|**Aritmetické operace**|Operátor očekává, že nahráním být číslo nebo čísla. Výstup je také číslo. Pokud kterýkoli ze vstupních **nedefinované** nebo je typ jiný než číslo potom výsledek **nedefinované**.|  
|**bitové operace**|Operátor očekává, že nahráním jako 32bitové celé číslo se znaménkem číslo nebo čísla. Výstup je také 32bitové celé číslo se znaménkem číslo.<br /><br /> Libovolná hodnota celé číslo se zaokrouhlí. Kladná hodnota se zaokrouhlí směrem dolů, záporné hodnoty zaokrouhluje nahoru.<br /><br /> Libovolnou hodnotu, která je mimo rozsah 32bitové celé číslo se převedou provedením poslední 32 bity jeho dvojkový doplněk zápisu.<br /><br /> Pokud kterýkoli ze vstupních **nedefinované** nebo jiného typu než číslo, bude výsledkem **nedefinované**.<br /><br /> **Poznámka:** Výše uvedené chování je kompatibilní s chováním bitový operátor jazyka JavaScript.|  
|**Logické**|Operátor očekává, že nahráním bude Boolean(s). Výstup je také logickou hodnotu.<br />Pokud kterýkoli ze vstupních **nedefinované** nebo jiného typu než logickou hodnotu, bude výsledkem **nedefinované**.|  
|**Porovnání**|Operátor očekává, že nahráním být stejného typu a nesmí být definován. Výstup je logická hodnota.<br /><br /> Pokud kterýkoli ze vstupních **nedefinované** nebo vstupy mají rozdílné typy a výsledkem je **nedefinované**.<br /><br /> Zobrazit **řazení hodnot pro porovnání** tabulky pro hodnotu řazení podrobnosti.|  
|**řetězec**|Operátor očekává, že nahráním být řetězce. Výstup je také řetězec.<br />Pokud kterýkoli ze vstupních **nedefinované** nebo jiného typu než řetězce a výsledkem je **nedefinované**.|  
  
 **Jednočlenné operátory:**  
  
|**Název**|**– Operátor**|**Podrobnosti**|  
|-|-|-|  
|**Aritmetické operace**|+<br /><br /> -|Vrátí číselnou hodnotu.<br /><br /> Bitová negace. Vrátí negovat číselnou hodnotu.|  
|**bitové operace**|~|Doplněk těch, které jsou. Vrátí zbytek číselnou hodnotu.|  
|**Logické**|**NOT**|Negace. Vrátí negovat logickou hodnotu.|  
  
 **Binární operátory:**  
  
|**Název**|**– Operátor**|**Podrobnosti**|  
|-|-|-|  
|**Aritmetické operace**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Přidání.<br /><br /> Odčítání.<br /><br /> Násobení.<br /><br /> Dělení.<br /><br /> Modulace.|  
|**bitové operace**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Bitový operátor OR.<br /><br /> Bitový AND.<br /><br /> Bitový operátor XOR.<br /><br /> Operátor posunu vlevo.<br /><br /> Posunutí doprava.<br /><br /> Výplň nulové posunutí doprava.|  
|**Logické**|**A**<br /><br /> **OR**|Logické spojení. Vrátí **true** Pokud jsou oba argumenty **true**, vrátí **false** jinak.<br /><br /> Logické spojení. Vrátí **true** Pokud jsou oba argumenty **true**, vrátí **false** jinak.|  
|**Porovnání**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Rovná se. Vrátí **true** Pokud argumenty jsou stejné, vrátí **false** jinak.<br /><br /> Není rovno. Vrátí **true** Pokud argumenty nejsou stejné, vrátí **false** jinak.<br /><br /> Větší než. Vrátí **true** Pokud je první argument větší než druhá, vrátí **false** jinak.<br /><br /> Větší než nebo rovna hodnotě. Vrátí **true** Pokud první argument je větší než nebo rovna hodnotě pro druhou kolekci, vrátí **false** jinak.<br /><br /> Menší než. Vrátí **true** Pokud je první argument menší než druhý jeden návratový **false** jinak.<br /><br /> Menší než nebo rovno. Vrátí **true** Pokud první argument menší než ten druhý, vrátí **false** jinak.<br /><br /> Sloučení. Vrátí druhý argument, pokud je první argument **nedefinované** hodnotu.|  
|**řetězec**|**&#124;&#124;**|Zřetězení. Vrátí zřetězení těchto oba argumenty.|  
  
 **Ternární operátory:**  
  
|Ternární operátor|?|Vrátí druhý argument, pokud je vyhodnocen jako první argument **true**; v opačném případě vrátí třetí argument.|  
|-|-|-|  
  
 **Řazení porovnávaných hodnot**  
  
|**Typ**|**Hodnoty pořadí**|  
|-|-|  
|**Nedefinovaný**|Není porovnatelný.|  
|**Hodnotu Null**|Jedna hodnota: **null**|  
|**Číslo**|Přirozené reálné číslo.<br /><br /> Záporná hodnota nekonečno je menší než jakoukoli jinou číselnou hodnotu.<br /><br /> Kladná hodnota nekonečno je větší než jakoukoli jinou číselnou hodnotu. **NaN** hodnota není porovnatelný. Výsledkem porovnání s **NaN** způsobí **nedefinované** hodnotu.|  
|**řetězec**|Lexicographical pořadí.|  
|**Pole**|Žádné řazení, ale spravedlivé.|  
|**objekt**|Žádné řazení, ale spravedlivé.|  
  
 **Poznámky**  
  
 Ve službě Cosmos DB typy hodnot, často nejsou známé, dokud se načítají z databáze. Aby bylo možné podporovat efektivní provádění dotazů, většina operátory mají požadavky na striktní typy. Operátory samy také neprovádějte implicitní převody.  
  
 To znamená, že dotaz podobný tomuto: Vybrat * z kořenového r WHERE r.Age = 21 vrátí jenom dokumenty s vlastností stáří rovná počtu 21. Dokumenty s vlastností rovno řetězci "21" nebo "0021" řetězec věk nebudou odpovídat jako výraz "21" = 21 vyhodnotí na nedefinovaný. To umožňuje lepší využití indexy, protože je rychlejší než hledání nekonečný počet vyhledávání konkrétní hodnotu (například číslo 21) potenciál odpovídá (číslo 21 nebo řetězce "21", "021", "21.0"...). Tím se liší od způsob, jak JavaScript vyhodnocuje operátory na hodnoty různých typů.  
  
 **Pole a objekty rovnosti a porovnání**  
  
 Porovnání hodnot pole nebo objekt pomocí operátorů rozsah (>, > =, <, < =) způsobí nedefinované, protože není definované na objekt nebo pole hodnot není pořadí. Nicméně použití operátory rovnosti nebo nerovnosti (=,! =, <>) je podporován a hodnoty budou strukturálně porovnávat.  
  
 Pokud obě pole mají stejný počet prvků a elementů na odpovídající pozice jsou také stejné pole jsou si rovny. Pokud nějaká dvojice prvků výsledkem porovnání nedefinovaný výsledek porovnání pole není definován.  
  
 Pokud oba objekty stejné vlastnosti definované, a pokud jsou hodnoty odpovídajících vlastností také stejné objekty rovnají. Pokud porovnání nějaká dvojice vlastnost hodnot za následek nedefinované výsledek porovnání objekt není definován.  
  
##  <a name="bk_constants"></a> Konstanty  
 Konstanta, označované také jako literál nebo skalární hodnota, je symbol, který reprezentuje hodnotu konkrétního data. Formát konstantě závisí na datový typ, který představuje.  
  
 **Podporované skalárních datových typů:**  
  
|**Typ**|**Hodnoty pořadí**|  
|-|-|  
|**Nedefinovaný**|Jedna hodnota: **nedefinované**|  
|**Hodnotu Null**|Jedna hodnota: **null**|  
|**Datový typ Boolean**|Hodnoty: **false**, **true**.|  
|**Číslo**|Číslo dvojité přesnosti s plovoucí desetinnou čárkou, standardní IEEE 754.|  
|**řetězec**|Posloupnost nula nebo více znaků Unicode. Řetězce musí být uzavřen v jednoduchých nebo dvojitých uvozovkách.|  
|**Pole**|Posloupnost nula nebo více prvků. Každý prvek může být hodnota libovolného skalární datového typu, s výjimkou typu Undefined.|  
|**objekt**|Neuspořádanou sadu párů název/hodnota nula nebo více. Název je řetězec znaků Unicode, hodnota může být libovolného skalární datového typu, s výjimkou **Nedefinováno**.|  
  
 **Syntaxe**  
  
```sql  
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
  
* `<undefined_constant>; undefined`  
  
  Představuje nedefinované hodnoty typu Nedefinováno.  
  
* `<null_constant>; null`  
  
  Představuje **null** hodnotu typu **Null**.  
  
* `<boolean_constant>`  
  
  Představuje konstantu typu Boolean.  
  
* `false`  
  
  Představuje **false** hodnotu typu Boolean.  
  
* `true`  
  
  Představuje **true** hodnotu typu Boolean.  
  
* `<number_constant>`  
  
  Představuje konstantu.  
  
* `decimal_literal`  
  
  Desítkové literály jsou čísla vyjadřuje desítkovém zápisu nebo vědecký zápis.  
  
* `hexadecimal_literal`  
  
  Šestnáctkové literály jsou čísla, které jsou reprezentovány pomocí předpony "0 x" a jedné nebo několika šestnáctkovými číslicemi.  
  
* `<string_constant>`  
  
  Představuje konstantu typu String.  
  
* `string _literal`  
  
  Řetězcové literály jsou reprezentovány posloupnost nula nebo více znaků Unicode nebo řídicí sekvence řetězců v kódu Unicode. Řetězcové literály jsou uzavřeny v jednoduchých uvozovkách (apostrof: ") nebo dvojité uvozovky (uvozovky:").  
  
 Následující řídicí sekvence jsou povoleny:  
  
|**Řídicí sekvence**|**Popis**|**Znak Unicode**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|dvojité uvozovky (")|U+0022|  
|\\\|zpětné lomítko (\\)|U+005C|  
|\\/|lomítko (/)|U+002F|  
|\b|BACKSPACE|U+0008|  
|\f|Posun strany|U + 000C|  
|\n|Přechod na nový řádek|U + 000A|  
|\r|Návrat na začátek|U + 000D|  
|\t|tabulátor|U+0009|  
|\uXXXX|Znak Unicode definované 4 šestnáctkovými číslicemi.|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Pokyny k výkonu dotazů  
 Aby dotazu pro kontejner velká efektivně provádět měla by používat filtry, které se dají obsluhovat pomocí jednoho nebo více indexů.  
  
 Následující filtry se budou považovat za pro index vyhledávání:  
  
-   Operátor rovnosti (=) pomocí výrazu cesty dokumentu a konstanty.  
  
-   Použití operátorů rozsah (<, \<=, >, > =) pomocí výrazu cesty dokumentu a číselné konstanty.  
  
-   Výraz cesty dokumentu jsou zahrnovaného libovolný výraz, který identifikuje konstantní cestu v dokumentech z kontejneru odkazovaná databáze.  
  
 **Výraz cesty dokumentu**  
  
 Výrazy jsou výrazy cesta k dokumentu, který cestu vlastnosti nebo pole assessors indexer přes dokumentu z databáze kontejnerové dokumenty. Tato cesta je možné pro určení umístění hodnoty odkazované ve filtru přímo v rámci dokumenty v databázi kontejneru.  
  
 Pro výraz považuje za výraz cesty dokumentu by měl:  
  
1.  Kořenový kontejner odkazovat přímo.  
  
2.  Odkaz na vlastnost nebo konstantu indexer pole některé výrazu cesta dokumentu  
  
3.  Odkazovat na alias, který představuje výraz cesty některé dokumentu.  
  
     **Konvencí syntaxe**  
  
     Následující tabulka popisuje konvence pro popis syntaxe v následujícím odkazu SQL.  
  
    |**Konvence**|**Používá pro**|  
    |-|-|    
    |VELKÁ PÍSMENA|Klíčová slova velká a malá písmena.|  
    |Malá|Malá a velká písmena slov.|  
    |\<neterminálu >|Neterminálu, definovat samostatně.|  
    |\<neterminálu >:: =|Syntaxe definice neterminálu.|  
    |other_terminal|Ve slovech podrobně terminálu (token).|  
    |identifikátor|Identifikátor. Umožňuje následující znaky: a – z A-Z, 0-9 _First znak nemůže být číslice.|  
    |"string"|Řetězec v uvozovkách. Povoluje libovolný platný řetězec. Zobrazit popis řetězcový_literál.|  
    |'symbol'|Literál symbol, který je součástí syntaxe.|  
    |&#124;(svislá čára)|Alternativy syntaxe položky. Lze použít pouze jednu ze zadané položky.|  
    |/(brackets) [] č.|Hranaté závorky uzavřete jeden nebo více volitelných položek.|  
    |[,.. .n]|Označuje, že předchozí položka může být n počet opakovaných pokusů o. Opakování jsou odděleny čárkami.|  
    |[ ...n ]|Označuje, že předchozí položka může být n počet opakovaných pokusů o. Opakování jsou oddělené mezerami.|  
  
##  <a name="bk_built_in_functions"></a> Předdefinované funkce  
 Cosmos DB poskytuje mnoho předdefinovaných funkcí SQL. Kategorie předdefinované funkce jsou uvedeny níže.  
  
|Funkce|Popis|  
|--------------|-----------------|  
|[Matematické funkce](#bk_mathematical_functions)|Matematické funkce provádí výpočet, obvykle podle vstupní hodnoty, které jsou k dispozici jako argumenty a vrátit číselnou hodnotu.|  
|[Kontrola funkce typu](#bk_type_checking_functions)|Funkce pro kontrolu typů umožňují zkontrolujte typ výrazu v rámci dotazů SQL.|  
|[Funkce řetězců](#bk_string_functions)|Řetězcové funkce provádění operací na vstupní hodnotu řetězce a vrátí řetězec, číslo nebo logickou hodnotu.|  
|[Funkce pole](#bk_array_functions)|Funkce pole provádění operací na hodnotu vstupního pole a vrácené číselné, logickou hodnotu nebo hodnotu pole.|  
|[Prostorové funkce](#bk_spatial_functions)|Prostorové funkce provádění operací na prostorový vstupní hodnotu a vrátí číslo nebo logickou hodnotu.|  
  
###  <a name="bk_mathematical_functions"></a> Matematické funkce  
 Následující funkce provádí výpočet, obvykle podle vstupní hodnoty, které jsou k dispozici jako argumenty a vrátit číselnou hodnotu.  
  
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
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad ukazuje výsledky pomocí funkce ABS na tři různá čísla.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Vrací úhel v radiánech, jehož kosinus je zadaný číselný výraz. Zkratka Arkus kosinus.  
  
 **Syntaxe**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vrátí ACOS -1.  
  
```  
SELECT ACOS(-1)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Vrací úhel v radiánech, jehož sinus je zadaný číselný výraz. Označuje se také Arkus sinus.  
  
 **Syntaxe**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vrátí ASIN -1.  
  
```  
SELECT ASIN(-1)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Vrací úhel v radiánech, jehož tangens odpovídá zadané číselný výraz. Označuje se také Arkus tangens.  
  
 **Syntaxe**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vrátí ATAN zadanou hodnotu.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Vrátí hlavní hodnotu arkustangens y / x, vyjádřené v radiánech.  
  
 **Syntaxe**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vypočítá ATN2 pro zadaný rozbočovač x a y komponenty.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> HORNÍ MEZ  
 Vrátí nejmenší hodnotu celé číslo větší než nebo rovna zadané číselný výraz.  
  
 **Syntaxe**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad ukazuje kladná číselná, záporná a nulové hodnoty funkcí mezní hodnoty.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Vrátí trigonometrických kosinus úhlu určeného v radiánech v zadaným výrazem.  
  
 **Syntaxe**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vypočítá COS určeného úhlu.  
  
```  
SELECT COS(14.78)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Vrátí trigonometrických kotangens úhlu určeného v radiánech v zadané číselný výraz.  
  
 **Syntaxe**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vypočítá COT určeného úhlu.  
  
```  
SELECT COT(124.1332)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> STUPŇŮ  
 Vrátí odpovídající úhel ve stupních, kterým je úhel zadaný v radiánech.  
  
 **Syntaxe**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vrátí počet stupňů v úhel PÍ/2 radiánů.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Vrátí největší celé číslo menší nebo rovna zadané číselný výraz.  
  
 **Syntaxe**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad ukazuje kladná číselná, záporná a nulová s FLOOR – funkce.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Vrátí hodnotu exponenciální zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
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
SELECT EXP(10)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 Následující příklad vrátí hodnotu exponenciální natural logarithm 20 a přirozený logaritmus exponenciální 20. Protože tyto funkce jsou inverzní funkce navzájem, vrácená hodnota s zaokrouhlení s plovoucí desetinnou čárkou matematické bod v obou případech je 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> PROTOKOL  
 Vrátí přirozený logaritmus zadaného číselný výraz.  
  
 **Syntaxe**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
-   `base`  
  
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
SELECT LOG(10)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 Následující příklad vypočítá protokolu pro exponent číslo.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Vrátí logaritmus o základu base 10 zadaný číselný výraz.  
  
 **Syntaxe**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Poznámky**  
  
 LOG10 a výkonu funkce jsou nepřímo sebou mají nějaký vztah. Například 10 ^ LOG10(n) = n.  
  
 **Příklady**  
  
 Následující příklad deklaruje proměnnou a vrátí LOG10 hodnotu zadanou proměnnou (100).  
  
```  
SELECT LOG10(100)  
```  
  
 Tady je sada výsledků.  
  
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
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vrátí hodnotu čísla PÍ.  
  
```  
SELECT PI()  
```  
  
 Tady je sada výsledků.  
  
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
  
     Jsou možnosti, na který se má použít `numeric_expression`.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad ukazuje vyvolání číslo exponentem 3 (datové krychle čísla).  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Radians vrátí, pokud je zadán číselný výraz ve stupních.  
  
 **Syntaxe**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vezme jako vstupní údaje několik úhly a vrátí jejich odpovídající hodnoty radián.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 Tady je sada výsledků.  
  
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
 Vrátí číselnou hodnotu, zaokrouhlený na nejbližší celočíselnou hodnotu.  
  
 **Syntaxe**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 V následujícím příkladu se zaokrouhlí na následující kladná a záporná čísla na nejbližší celé číslo.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> PŘIHLÁŠENÍ  
 Vrátí kladné (+ 1), nula (0) nebo záporné znaménko (-1), z určeného číselného výrazu.  
  
 **Syntaxe**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vrátí hodnoty znaménko čísel od -2 do 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Vrátí trigonometrických sinus úhlu určeného v radiánech v zadaným výrazem.  
  
 **Syntaxe**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vypočítá SIN určeného úhlu.  
  
```  
SELECT SIN(45.175643)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Vrátí druhou odmocninu zadanou číselnou hodnotu.  
  
 **Syntaxe**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vrátí druhé odmocniny čísla 1-3.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> ČTVEREC  
 Vrátí druhou mocninu zadané číselnou hodnotu.  
  
 **Syntaxe**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vrátí druhých mocnin čísla 1-3.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Vrátí tangens úhlu určeného v radiánech v zadaným výrazem.  
  
 **Syntaxe**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vypočítá tangens (PÍ) / 2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC –  
 Vrátí číselnou hodnotu, zkrácen na nejbližší celočíselnou hodnotu.  
  
 **Syntaxe**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumenty**  
  
-   `numeric_expression`  
  
     Je číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 V následujícím příkladu se zkrátí na následující kladná a záporná čísla na nejbližší celočíselnou hodnotu.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 Tady je sada výsledků.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> Kontrola funkce typu  
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
  
-   `expression`  
  
     Je libovolný platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce is_array –.  
  
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
  
 Tady je sada výsledků.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Vrátí logickou hodnotu označující, pokud typ z určeného výrazu je logická hodnota.  
  
 **Syntaxe**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je libovolný platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce IS_BOOL.  
  
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
  
 Tady je sada výsledků.  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Vrátí logickou hodnotu označující, pokud vlastnost byla přiřazena hodnota.  
  
 **Syntaxe**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je libovolný platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje přítomnost vlastnosti v rámci zadaného dokumentu JSON. První vrátí hodnotu true, protože "a" je k dispozici, ale druhá vrátí hodnotu false, protože chybí "b".  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 Tady je sada výsledků.  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Vrátí logickou hodnotu označující, zda je typ z určeného výrazu hodnotu null.  
  
 **Syntaxe**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je libovolný platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce IS_NULL.  
  
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
  
 Tady je sada výsledků.  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Vrátí logickou hodnotu označující, pokud typ z určeného výrazu je číslo.  
  
 **Syntaxe**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je libovolný platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce IS_NULL.  
  
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
  
 Tady je sada výsledků.  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT –  
 Vrátí logickou hodnotu označující, pokud je typ z určeného výrazu objektu JSON.  
  
 **Syntaxe**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je libovolný platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce is_object –.  
  
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
  
 Tady je sada výsledků.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Vrátí logickou hodnotu označující, zda je typ z určeného výrazu jednoduchého typu (string, Boolean, číselné nebo null).  
  
 **Syntaxe**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je libovolný platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce IS_PRIMITIVE.  
  
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
  
 Tady je sada výsledků.  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Vrátí logickou hodnotu označující, pokud je typ z určeného výrazu string.  
  
 **Syntaxe**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumenty**  
  
-   `expression`  
  
     Je libovolný platný výraz.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje objekty JSON logická, číslo, řetězec, null, objekt, pole a nedefinované typů pomocí funkce IS_STRING.  
  
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
  
 Tady je sada výsledků.  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a> Řetězcové funkce  
 Následující skalární funkce provádění operací na vstupní hodnotu řetězce a vrátí řetězec, číslo nebo logickou hodnotu.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[OBSAHUJE](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[DÉLKA](#bk_length)|  
|[NIŽŠÍ](#bk_lower)|[LTRIM](#bk_ltrim)|[NAHRADIT](#bk_replace)|  
|[REPLIKACE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[DÍLČÍ ŘETĚZEC](#bk_substring)|  
|[ToString](#bk_tostring)|[UVOLNĚNÍ DOČASNÉ PAMĚTI](#bk_trim)|[HORNÍ](#bk_upper)||| 
  
####  <a name="bk_concat"></a> CONCAT  
 Vrátí řetězec, který je výsledkem zřetězení dvou nebo více řetězcových hodnot.  
  
 **Syntaxe**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad vrací spojený řetězec ze zadaných hodnot.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> OBSAHUJE  
 Vrátí hodnotu typu Boolean označující, zda řetězec prvního výrazu obsahuje druhý.  
  
 **Syntaxe**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje Pokud "abc" obsahuje "ab" a "d".  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce končí na druhý.  
  
 **Syntaxe**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí "abc" končí "b" a "bc".  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Vrátí počáteční pozici prvního výskytu druhý řetězec výrazu v rámci prvního zadaného řetězcového výrazu nebo -1, pokud není nalezen řetězec.  
  
 **Syntaxe**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad vrátí index různých podřetězců uvnitř "abc".  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> DOLEVA  
 Vrátí levou část řetězec zadaný počet znaků.  
  
 **Syntaxe**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
-   `num_expr`  
  
     Je jakýkoli platný číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí levou část "abc" pro různé hodnoty pro délku.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 Tady je sada výsledků.  
  
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
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí délku řetězce.  
  
```  
SELECT LENGTH("abc")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> NIŽŠÍ  
 Vrátí řetězcový výraz po převedení dat velkým písmenem na malá písmena.  
  
 **Syntaxe**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje způsob použití nižší v dotazu.  
  
```  
SELECT LOWER("Abc")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Vrátí řetězcový výraz po odebere úvodní mezery.  
  
 **Syntaxe**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak použít LTRIM uvnitř dotazu.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 Tady je sada výsledků.  
  
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
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak se používá v dotazu.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 Tady je sada výsledků.  
  
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
  
     Je libovolný výraz platný řetězec.  
  
-   `num_expr`  
  
     Je jakýkoli platný číselný výraz. Pokud num_expr je záporný nebo nekonečnou, výsledek není definován.

  > [!NOTE]
  > Maximální délka výsledku je 10 000 znaků tedy (length(str_expr) * num_expr) < = 10 000.
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak pomocí provádí REPLIKACI v dotazu.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Vrátí hodnotu řetězce obráceném pořadí.  
  
 **Syntaxe**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak použít reverzní v dotazu.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> DOPRAVA  
 Vrátí část reprezentující správný řetězec zadaný počet znaků.  
  
 **Syntaxe**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
-   `num_expr`  
  
     Je jakýkoli platný číselný výraz.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí pravou část "abc" pro různé hodnoty pro délku.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Vrátí řetězcový výraz po odebere koncové mezery.  
  
 **Syntaxe**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak použít RTRIM uvnitř dotazu.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Vrátí hodnotu typu Boolean označující, zda se první výraz řetězce začíná druhé.  
  
 **Syntaxe**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad zkontroluje, pokud se řetězec "abc" začíná písmenem "b" a "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> DÍLČÍ ŘETĚZEC  
 Vrátí část řetězcového výrazu počínaje pozice s nulovým základem zadaný znak a pokračuje na určenou délku nebo na konci řetězce.  
  
 **Syntaxe**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
-   `num_expr`  
  
     Je jakýkoli platný číselný výraz k označení počáteční a koncový znak.    
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad vrátí podřetězec "abc", spouštění na 1 a pro o délce 1 znak.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Vrátí řetězcovou reprezentaci skalární výraz. 
  
 **Syntaxe**  
  
```  
ToString(<expr>)
```  
  
 **Argumenty**  
  
-   `expr`  
  
     Je libovolný platný skalární výraz.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak se chová ToString do různých typů.   
  
```  
SELECT ToString(1.0000), ToString("Hello World"), ToString(NaN), ToString(Infinity),
ToString(IS_STRING(ToString(undefined))), IS_STRING(ToString(0.1234), ToString(false), ToString(undefined))
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": "1", "$2": "Hello World", "$3": "NaN", "$4": "Infinity", "$5": "false", "$6": true, "$7": "false"}]  
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
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak použít uvnitř dotazu uvolnění dočasné paměti.  
  
```  
SELECT TRIM("   abc"), TRIM("   abc   "), TRIM("abc   "), TRIM("abc")   
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc", "$4": "abc"}]  
``` 
####  <a name="bk_upper"></a> HORNÍ  
 Vrátí řetězcový výraz po převedení data znaků na malá písmena na velká písmena.  
  
 **Syntaxe**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumenty**  
  
-   `str_expr`  
  
     Je libovolný výraz platný řetězec.  
  
 **Návratové typy**  
  
 Vrátí řetězcový výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak používat horní v dotazu  
  
```  
SELECT UPPER("Abc")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Funkce pole  
 Následující skalární funkce provádění operací na hodnotu vstupního pole a vrátit číselné, hodnota logická hodnota nebo pole  
  
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
  
-   `arr_expr`  
  
     Je libovolný výraz, platným polem.  
  
 **Návratové typy**  
  
 Vrátí výraz pole.  
  
 **Příklady**  
  
 Následující příklad jak ke zřetězení dvou polí.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Vrátí logickou hodnotu označující, zda pole obsahuje zadanou hodnotu. Částečné nebo úplné shody objektu můžete zkontrolovat pomocí logický výraz v příkazu. 

 **Syntaxe**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Je libovolný výraz, platným polem.  
  
-   `expr`  
  
     Je libovolný platný výraz.  

-   `bool_expr`  
  
     Je libovolný výraz boolean. Pokud je nastavena na "true'and, pokud je hodnota zadaného hledání objektu, příkaz vyhledá částečnou shodu (hledání objektu je podmnožinou některý z objektů). Pokud je nastavena na hodnotu 'false', tento příkaz zkontroluje úplná shoda všech objektů v poli. Výchozí hodnota, pokud není zadán je false. 
  
 **Návratové typy**  
  
 Vrátí logickou hodnotu.  
  
 **Příklady**  
  
 Následující příklad postup kontroly členství v poli pomocí ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": true, "$2": false}]  
```  

 Následující příklad návod k ověření pro částečnou shodu JSON v poli pomocí ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 Tady je sada výsledků.  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Vrátí počet prvků výrazu určeném poli.  
  
 **Syntaxe**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Je libovolný výraz, platným polem.  
  
 **Návratové typy**  
  
 Vrátí hodnotu číselného výrazu.  
  
 **Příklady**  
  
 Následující příklad jak získat délku pole pomocí ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 Tady je sada výsledků.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Vrátí část reprezentující výraz pole.
  
 **Syntaxe**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumenty**  
  
-   `arr_expr`  
  
     Je libovolný výraz, platným polem.  
  
-   `num_expr`  
  
     Založený na nule číselný index, na kterém má být pole. Záporné hodnoty slouží k určení počátečního indexu relativnímu k poslední prvek pole tedy -1 odkazy na poslední prvek v poli.  

-   `num_expr`  

     Maximální počet prvků v výsledným polem.    

 **Návratové typy**  
  
 Vrátí výraz pole.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak získat jiné řezy k poli pomocí ARRAY_SLICE.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100)      
  
```  
  
 Tady je sada výsledků.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"],
           "$3": ["strawberries"],  
           "$4": ["strawberries", "bananas"], 
           "$5": [],
           "$6": ["strawberries", "bananas"],
           "$7": [] 
}]  
```  
 
###  <a name="bk_spatial_functions"></a> Prostorové funkce  
 Následující skalární funkce provádění operací na prostorový vstupní hodnotu a vrátí číslo nebo logickou hodnotu.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Vrací vzdálenost mezi dvěma GeoJSON bodu mnohoúhelníku či LineString výrazy.  
  
 **Syntaxe**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
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
  
-   `spatial_expr`  
  
     Je libovolný platný výraz, objektu GeoJSON bodu mnohoúhelníku či LineString.  
 
-   `spatial_expr`  
  
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
  
-   `spatial_expr`  
  
     Je libovolný platný výraz, objektu GeoJSON bodu mnohoúhelníku či LineString.  
 
-   `spatial_expr`  
  
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
  
-   `spatial_expr`  
  
     Je libovolný platný výraz GeoJSON bodu mnohoúhelníku či LineString.  
  
 **Návratové typy**  
  
 Vrátí logický výraz.  
  
 **Příklady**  
  
 Následující příklad ukazuje, jak zkontrolovat, zda je platný pomocí ST_VALID bod.  
  
 Například tento bod má hodnotu šířky, který není v platném rozsahu hodnot [-90, 90], takže dotaz vrátí hodnotu false.  
  
 Mnohoúhelníky vyžaduje specifikaci GeoJSON poslední souřadnic pár poskytuje by měl být stejný jako první, chcete-li vytvořit zavřeného tvaru. Body v rámci mnohoúhelníku musí zadat v pořadí proti směru hodinových ručiček. Mnohoúhelník zadat v pořadí po směru hodinových ručiček představuje inverzní oblasti v rámci něj.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 Tady je sada výsledků.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Vrátí hodnotu JSON obsahující logická hodnota, pokud zadaný výraz GeoJSON bodu mnohoúhelníku či LineString je platný a pokud není platný, kromě důvod jako hodnotu řetězce.  
  
 **Syntaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumenty**  
  
-   `spatial_expr`  
  
     Je libovolný platný výraz bodu nebo mnohoúhelník GeoJSON.  
  
 **Návratové typy**  
  
 Vrátí hodnotu JSON obsahující logická hodnota, pokud zadaný výraz bodu nebo mnohoúhelník GeoJSON je platný a pokud není platný, kromě důvod jako hodnotu řetězce.  
  
 **Příklady**  
  
 Následující příklad návod k ověření platnosti (s podrobnostmi) pomocí ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 Tady je sada výsledků.  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>Další postup  

- [Syntaxe SQL a dotaz SQL pro službu Cosmos DB](how-to-sql-query.md)

- [Dokumentace ke službě cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
