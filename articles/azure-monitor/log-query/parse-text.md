---
title: Analýza textových dat v protokolech Azure Monitoru | Dokumenty společnosti Microsoft
description: Popisuje různé možnosti pro analýzu dat protokolu v azure monitoru záznamů při požití dat a při jejich načtení v dotazu, porovnání relativní výhody pro každý.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: d7a37d51c411488231205fd036f9a287f5206ce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672442"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Analýza textových dat v protokolech Azure Monitoru
Některá data protokolu shromážděná službou Azure Monitor budou obsahovat více informací v jedné vlastnosti. Analýza těchto dat do více vlastností usnadňuje použití v dotazech. Běžným příkladem je [vlastní protokol,](../../log-analytics/log-analytics-data-sources-custom-logs.md) který shromažďuje celou položku protokolu s více hodnotami do jedné vlastnosti. Vytvořením samostatných vlastností pro různé hodnoty můžete vyhledávat a agregovat na každé z nich.

Tento článek popisuje různé možnosti pro analýzu dat protokolu v Azure Monitoru při požití dat a při jejich načtení v dotazu, porovnání relativní výhody pro každý.


## <a name="parsing-methods"></a>Metody analýzy
Data můžete analyzovat buď v době přijetí při shromažďování dat, nebo v době dotazu při analýze dat pomocí dotazu. Každá strategie má jedinečné výhody, jak je popsáno níže.

### <a name="parse-data-at-collection-time"></a>Analyzovat data v době sběru
Při analýzě dat v době shromažďování nakonfigurujete [vlastní pole,](../../log-analytics/log-analytics-custom-fields.md) která vytvářejí nové vlastnosti v tabulce. Dotazy nemusí obsahovat žádnou logiku analýzy a jednoduše použít tyto vlastnosti jako jakékoli jiné pole v tabulce.

Výhody této metody zahrnují následující:

- Snadnější dotazování na shromážděná data, protože do dotazu není nutné zahrnout příkazy analýzy.
- Lepší výkon dotazu, protože dotaz nemusí provádět analýzu.
 
Nevýhody této metody zahrnují následující:

- Musí být definována předem. Nelze zahrnout data, která již byla shromážděna.
- Pokud změníte logiku analýzy, bude se vztahovat pouze na nová data.
- Méně možností analýzy, než je k dispozici v dotazech.
- Zvyšuje latenci čas pro sběr dat.
- Chyby může být obtížné zpracovat.


### <a name="parse-data-at-query-time"></a>Analyzovat data v době dotazu
Při analýzě dat v době dotazu zahrnout logiku v dotazu analyzovat data do více polí. Samotná tabulka se nezmění.

Výhody této metody zahrnují následující:

- Platí pro všechna data, včetně dat, která již byla shromážděna.
- Změny v logice lze použít okamžitě na všechna data.
- Flexibilní možnosti analýzy včetně předdefinované logiky pro konkrétní datové struktury.
 
Nevýhody této metody zahrnují následující:

- Vyžaduje složitější dotazy. To lze zmírnit pomocí [funkcí pro simulaci tabulky](#use-function-to-simulate-a-table).
- Musí replikovat logiku analýzy ve více dotazech. Můžete sdílet některé logiky prostřednictvím funkcí.
- Můžete vytvořit režii při spuštění komplexní logiky proti velmi velké sady záznamů (miliardy záznamů).

## <a name="parse-data-as-its-collected"></a>Analyzovat data tak, jak jsou shromažďována
Podrobnosti o shromažďovacích datech v tématu [Vytváření vlastních polí v Azure Monitoru](../platform/custom-fields.md) najdete podrobnosti o shromažďovacích datech. Tím se vytvoří vlastní vlastnosti v tabulce, které lze použít dotazy stejně jako všechny ostatní vlastnosti.

## <a name="parse-data-in-query-using-patterns"></a>Analýza dat v dotazu pomocí vzorků
Pokud data, která chcete analyzovat, lze identifikovat vzorem opakovaným v rámci záznamů, můžete použít různé operátory v [dotazovacím jazyce Kusto](/azure/kusto/query/) k extrahování konkrétní části dat do jedné nebo více nových vlastností.

### <a name="simple-text-patterns"></a>Jednoduché textové vzorky

Pomocí operátoru [analýzy](/azure/kusto/query/parseoperator) v dotazu vytvořte jednu nebo více vlastních vlastností, které lze extrahovat z řetězcového výrazu. Zadáte vzorek, který má být identifikován, a názvy vlastností, které chcete vytvořit. To je užitečné zejména pro data s řetězci klíč-hodnota s formulářem podobným _key=value_.

Zvažte vlastní protokol s daty v následujícím formátu.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Následující dotaz by analyzovat tato data do jednotlivých vlastností. Řádek s _projektem_ je přidán pouze vrátit vypočtené vlastnosti a nikoli _RawData_, což je jedna vlastnost, která drží celou položku z vlastního protokolu.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Následuje další příklad, který vyloží uživatelské jméno hlavního názvu uživatele v tabulce _AzureActivity._

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Regulární výrazy
Pokud lze data identifikovat pomocí regulárního výrazu, můžete k extrahování jednotlivých hodnot [použít funkce, které používají regulární výrazy.](/azure/kusto/query/re2) Následující příklad používá [extrakt](/azure/kusto/query/extractfunction) k prolomení pole _UPN_ ze záznamů _AzureActivity_ a pak vrátit odlišné uživatele.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Chcete-li povolit efektivní analýzu ve velkém měřítku, Azure Monitor používá re2 verze regulární výrazy, která je podobná, ale není shodná s některými jinými variantami regulárních výrazů. Podrobnosti naleznete v [syntaxi výrazu re2.](https://aka.ms/kql_re2syntax)


## <a name="parse-delimited-data-in-a-query"></a>Analýza oddělených dat v dotazu
Oddělená data oddělují pole společným znakem, například čárkou v souboru CSV. Pomocí funkce [rozdělení](/azure/kusto/query/splitfunction) můžete analyzovat oddělená data pomocí oddělovače, který zadáte. Pomocí operátoru [extend](/azure/kusto/query/extendoperator) můžete vrátit všechna pole v datech nebo určit jednotlivá pole, která mají být zahrnuta do výstupu.

> [!NOTE]
> Vzhledem k tomu, že funkce split vrací dynamický objekt, může být nutné explicitně přetypovat na datové typy, jako je řetězec, který se má použít v operátorech a filtrech.

Zvažte vlastní protokol s daty v následujícím formátu CSV.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Následující dotaz by analyzovat tato data a sumarizovat dvě vypočtené vlastnosti. První řádek rozdělí vlastnost _RawData_ na pole řetězců. Každý z následujících řádků dává název jednotlivým vlastnostem a přidá je do výstupu pomocí funkcí, které je převedou na příslušný datový typ.

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

## <a name="parse-predefined-structures-in-a-query"></a>Analýza předdefinovaných struktur v dotazu
Pokud jsou data formátována ve známé struktuře, můžete použít jednu z funkcí v [dotazovacím jazyce Kusto](/azure/kusto/query/) pro analýzu předdefinovaných struktur:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [Adresa URL](/azure/kusto/query/parseurlfunction)
- [Dotaz url](/azure/kusto/query/parseurlqueryfunction)
- [Cesta k souboru](/azure/kusto/query/parsepathfunction)
- [Uživatelský agent](/azure/kusto/query/parse-useragentfunction)
- [Řetězec verze](/azure/kusto/query/parse-versionfunction)

Následující příklad dotazu analyzuje _vlastnosti_ pole _Tabulka AzureActivity,_ která je strukturována v JSON. Uloží výsledky na dynamickou vlastnost s názvem _parsedProp_, která zahrnuje jednotlivé pojmenované hodnoty v JSON. Tyto hodnoty se používají k filtrování a shrnutí výsledků dotazu.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Tyto funkce analýzy mohou být náročné na procesor, takže by měly být použity pouze v případě, že dotaz používá více vlastností z formátovaných dat. V opačném případě bude jednoduché zpracování porovnávání vzorů rychlejší.

Následující příklad ukazuje rozdělení typu TGT Preauth řadiče domény. Typ existuje pouze v poli EventData, což je řetězec XML, ale žádná další data z tohoto pole nejsou potřeba. V tomto případě [se analýza](/azure/kusto/query/parseoperator) používá k výběru požadovaných dat.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Použití funkce k simulaci tabulky
Můžete mít více dotazů, které provádějí stejnou analýzu určité tabulky. V takovém případě [vytvořte funkci,](functions.md) která vrátí analyzovaná data namísto replikace logiky analýzy v každém dotazu. Potom můžete použít alias funkce místo původní tabulky v jiných dotazech.

Vezměme si čárka-oddělený vlastní log příklad výše. Chcete-li analyzovat data ve více dotazech, vytvořte funkci pomocí následujícího dotazu a uložte ji s aliasem _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Nyní můžete použít alias _MyCustomCSVLog_ místo názvu skutečné tabulky v dotazech, jako je následující.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolu](log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení.
