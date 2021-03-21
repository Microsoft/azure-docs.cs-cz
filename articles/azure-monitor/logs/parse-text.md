---
title: Analyzovat textová data v protokolech Azure Monitor | Microsoft Docs
description: Popisuje různé možnosti analýzy dat protokolu v Azure Monitor záznamy, když se data ingestují a když se načtou v dotazu, a porovnává si relativní výhody pro každou z nich.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: 2e2babd39dc9dadf06c9a3ca3292e021e0c1deaa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037179"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Analyzovat textová data v protokolech Azure Monitor
Některá data protokolu shromážděná pomocí Azure Monitor budou obsahovat více informací v jedné vlastnosti. Analýza těchto dat na více vlastností usnadňuje jejich použití v dotazech. Běžným příkladem je [vlastní protokol](../agents/data-sources-custom-logs.md) , který shromažďuje celou položku protokolu s více hodnotami do jedné vlastnosti. Vytvořením samostatných vlastností pro různé hodnoty můžete vyhledávat a agregovat na každé z nich.

Tento článek popisuje různé možnosti analýzy dat protokolu v Azure Monitor, když se data ingestují a když se načtou v dotazu, a porovnává si relativní výhody pro každou z nich.


## <a name="parsing-methods"></a>Metody analýzy
Data můžete analyzovat buď při příjmu dat, když jsou data shromažďována nebo v době dotazu při analýze dat pomocí dotazu. Každá strategie má jedinečné výhody, jak je popsáno níže.

### <a name="parse-data-at-collection-time"></a>Analyzovat data v době shromažďování
Při analýze dat v době shromažďování můžete nakonfigurovat [vlastní pole](../logs/custom-fields.md) , která vytvoří nové vlastnosti v tabulce. Dotazy nemusejí zahrnovat žádnou logiku analýzy a jednoduše tyto vlastnosti použít jako jakékoli jiné pole v tabulce.

Mezi výhody této metody patří následující:

- Snazší dotazování shromážděných dat, protože v dotazu nemusíte vkládat příkazy analýzy.
- Lepší výkon dotazů, protože dotaz nemusí provádět analýzu.
 
Nevýhodou této metody jsou následující:

- Musí být definováno předem. Data, která jsou již shromážděna, nelze zahrnout.
- Změníte-li logiku analýzy, bude použita pouze pro nová data.
- Menší možnosti analýzy než dostupné v dotazech.
- Zvyšuje čas latence při sběru dat.
- Chyby může být obtížné zvládnout.


### <a name="parse-data-at-query-time"></a>Analyzovat data v době dotazu
Když analyzujete data v době dotazu, zahrnete do dotazu logiku, která analyzuje data do více polí. Samotná skutečná tabulka není upravena.

Mezi výhody této metody patří následující:

- Platí pro všechna data, včetně dat, která jsou už shromážděná.
- Změny v logice lze použít okamžitě pro všechna data.
- Flexibilní možnosti analýzy, včetně předdefinované logiky pro konkrétní datové struktury.
 
Nevýhodou této metody jsou následující:

- Vyžaduje složitější dotazy. To lze zmírnit použitím [funkcí pro simulaci tabulky](#use-function-to-simulate-a-table).
- Je nutné replikovat logiku analýzy ve více dotazech. Může sdílet některé logiky prostřednictvím funkcí.
- Může vytvářet režijní náklady při spouštění komplexní logiky proti velmi rozsáhlým sadám záznamů (miliardy záznamů).

## <a name="parse-data-as-its-collected"></a>Analyzovat data při jejich shromažďování
Podrobnosti o analýze dat při jejich shromažďování najdete v tématu [Vytvoření vlastních polí v Azure monitor](../logs/custom-fields.md) . Tím se vytvoří vlastní vlastnosti v tabulce, které mohou být použity dotazy stejně jako jakákoli jiná vlastnost.

## <a name="parse-data-in-query-using-patterns"></a>Analyzovat data v dotazu pomocí vzorů
Pokud se data, která chcete analyzovat, dají identifikovat pomocí vzoru opakovaného v rámci záznamů, můžete použít různé operátory v [jazyce dotazů Kusto](/azure/kusto/query/) k extrakci konkrétních dat do jedné nebo více nových vlastností.

### <a name="simple-text-patterns"></a>Vzory jednoduchých textů

Pomocí operátoru [Parse](/azure/kusto/query/parseoperator) v dotazu vytvořte jednu nebo více vlastních vlastností, které lze extrahovat z řetězcového výrazu. Určíte vzor, který se má identifikovat, a názvy vlastností, které se mají vytvořit. To je zvlášť užitečné pro data s řetězci klíč-hodnota s formulářem podobným _klíčovým = hodnota_.

Zvažte vlastní protokol s daty v následujícím formátu.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Následující dotaz by tato data analyzoval na jednotlivé vlastnosti. Řádek s _projektem_ je přidán, aby vracel pouze počítané vlastnosti, nikoli _rawData_, což je jediná vlastnost držící celou položku z vlastního protokolu.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Následuje další příklad, který rozdělí uživatelské jméno hlavního názvu uživatele (UPN) do tabulky _AzureActivity_ .

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Regulární výrazy
Pokud se vaše data dají identifikovat pomocí regulárního výrazu, můžete použít [funkce, které používají regulární výrazy](/azure/kusto/query/re2) k extrakci jednotlivých hodnot. Následující příklad používá [extrakci](/azure/kusto/query/extractfunction) k rozdělení pole _hlavního názvu uživatele (UPN)_ ze záznamů _AzureActivity_ a vrácení jedinečných uživatelů.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Aby bylo možné efektivně analyzovat ve velkém měřítku, Azure Monitor používá re2 verzi regulárních výrazů, která je podobná, ale není shodná s některými jinými variantami regulárních výrazů. Podrobnosti najdete v [syntaxi výrazu re2](https://aka.ms/kql_re2syntax) .


## <a name="parse-delimited-data-in-a-query"></a>Analyzovat data oddělená v dotazu
Oddělená data oddělují pole se společným znakem, jako je čárka v souboru CSV. Pomocí funkce [Split](/azure/kusto/query/splitfunction) můžete analyzovat data oddělená pomocí oddělovače, který zadáte. Pomocí operátoru [Extended](/azure/kusto/query/extendoperator) můžete vrátit všechna pole v datech nebo zadat jednotlivá pole, která chcete zahrnout do výstupu.

> [!NOTE]
> Vzhledem k tomu, že funkce Split vrátí dynamický objekt, mohou být výsledky explicitně přetypování na datové typy, jako je například řetězec, který se má použít v operátorech a filtrech.

Vezměte v úvahu vlastní protokol s daty v následujícím formátu CSV.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Následující dotaz by analyzoval tato data a sumarizuje je dvěma ze počítaných vlastností. První řádek rozdělí vlastnost  _rawData_ do pole řetězce. Každý z dalších řádků obsahuje název jednotlivých vlastností a přidává je do výstupu pomocí funkcí pro jejich převod na příslušný datový typ.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Analyzovat předdefinované struktury v dotazu
Pokud jsou data ve známé struktuře naformátovaná, možná budete moct použít jednu z funkcí v [dotazovacím jazyce Kusto](/azure/kusto/query/) k analýze předdefinovaných struktur:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [Adresa URL](/azure/kusto/query/parseurlfunction)
- [Dotaz na adresu URL](/azure/kusto/query/parseurlqueryfunction)
- [Cesta k souboru](/azure/kusto/query/parsepathfunction)
- [Uživatelský agent](/azure/kusto/query/parse-useragentfunction)
- [Řetězec verze](/azure/kusto/query/parse-versionfunction)

Následující příklad dotazu analyzuje pole _vlastností_ tabulky _AzureActivity_ , která je strukturována ve formátu JSON. Výsledky uloží do dynamické vlastnosti s názvem _parsedProp_, která zahrnuje jednotlivé pojmenované hodnoty ve formátu JSON. Tyto hodnoty slouží k filtrování a sumarizaci výsledků dotazu.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Tyto funkce analýzy mohou být náročné na procesor, takže by měly být použity pouze v případě, že dotaz používá více vlastností ze formátovaných dat. V opačném případě bude jednodušší zpracování jednoduchých vzorů rychlejší.

Následující příklad ukazuje rozpis typu předběžného ověření TGT řadiče domény. Typ existuje pouze v poli EventData, což je řetězec XML, ale žádná jiná data z tohoto pole nejsou potřeba. V tomto případě se k výběru požadovaných částí dat používá [Analýza](/azure/kusto/query/parseoperator) .

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Použití funkce k simulaci tabulky
Je možné, že máte více dotazů, které provádějí stejnou analýzu konkrétní tabulky. V takovém případě [vytvořte funkci](../logs/functions.md) , která vrátí Analyzovaná data namísto replikace logiky analýzy v každém dotazu. Pak můžete použít alias funkce místo původní tabulky v jiných dotazech.

Vezměte v úvahu příklad vlastního protokolu odděleného čárkami. Chcete-li použít Analyzovaná data ve více dotazech, vytvořte funkci pomocí následujícího dotazu a uložte ji s aliasem _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Nyní můžete použít alias _MyCustomCSVLog_ místo skutečného názvu tabulky v dotazech, jako jsou následující.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolů](./log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení.