---
title: Parsovat text data v protokolech Azure Monitor | Dokumentace Microsoftu
description: Popisuje různé možnosti pro analýzu dat protokolu v záznamech Azure Monitor, když data ingestují a když je načten v dotazu, porovnání relativní výhody pro každý.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: bwren
ms.openlocfilehash: b6a2ca70faa36b94ace8158f33e58b5e6688ece3
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002184"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Parsovat text data v protokolech Azure Monitor
Některé protokolu data shromážděná službou Azure Monitor bude obsahovat více kusů informace v jedné vlastnosti. Analýza těchto dat do více vlastností usnadňují použít v dotazech. Běžným příkladem jsou [vlastního protokolu](../../log-analytics/log-analytics-data-sources-custom-logs.md) položku celý protokol s více hodnotami, které shromažďuje do vlastnosti jediné. Vytvoření samostatné vlastnosti pro různé hodnoty, můžete hledat a agregace v každém.

Tento článek popisuje různé možnosti pro analýzu dat protokolu ve službě Azure Monitor, když data ingestují a když je načten v dotazu, porovnání relativní výhody pro každý.


## <a name="parsing-methods"></a>Analýza kódu metody
Můžete analyzovat data v době příjmu, kdy data jsou shromažďována nebo na dotaz při analýze dat pomocí dotazu. Každé strategie zahrnuje jedinečné výhody, jak je popsáno níže.

### <a name="parse-data-at-collection-time"></a>Analýza dat v době shromažďování
Při analýze dat v době shromažďování nakonfigurujete [vlastní pole](../../log-analytics/log-analytics-custom-fields.md) , které v tabulce vytvořte nové vlastnosti. Dotazy nemusí obsahovat všechny analýzy logiky a tyto vlastnosti jednoduše používat jako jakékoli jiné pole v tabulce.

Výhody této metody patří:

- Usnadňuje dotazu analyzovat shromážděná data, protože není nutné zahrnovat příkazy v dotazu.
- Lepší výkon dotazů, protože dotaz není nutné provádět analýzy.
 
Nevýhody této metody patří:

- Musí být předem definovaný. Nesmí obsahovat data, která již byla shromážděna.
- Pokud změníte analýzy logiku, bude vztahovat jenom na nová data.
- Možnosti analýzy na méně než je k dispozici v dotazech.
- Latence dobu shromažďování dat se zvyšuje.
- Chyby může být obtížné zpracovat.


### <a name="parse-data-at-query-time"></a>Analýza dat v době zpracování dotazu
Při analýze dat v době zpracování dotazu zahrnete logiku dotazu k analýze dat do několika polí. Samotné tabulky se nezmění.

Výhody této metody patří:

- Platí pro všechna data, včetně dat, která již byla shromážděna.
- Změny v logiky můžete okamžitě použít pro všechna data.
- Flexibilní možnosti včetně předdefinovaných logiku pro konkrétní datové struktury analýzy.
 
Nevýhody této metody patří:

- Vyžaduje složitější dotazy. To lze zmírnit použitím [funkce pro simulaci tabulku](#Use-function-to-simulate-a-table).
- Musí replikovat analýzy logiky ve více dotazů. Můžete sdílet nějaké logiky prostřednictvím funkce.
- Můžete vytvořit režie při spouštění velmi rozsáhlých záznam komplexní logiku nastaví (miliard záznamů).

## <a name="parse-data-as-its-collected"></a>Jak se shromažďují data analyzovat
Zobrazit [vytvářet vlastní pole ve službě Azure Monitor](../platform/custom-fields.md) podrobné informace o analýze dat, jako jsou shromažďovány. Tím se vytvoří vlastní vlastnosti v tabulce, která mohou být využívána dotazy stejně jako jakoukoli jinou vlastnosti.

## <a name="parse-data-in-query-using-patterns"></a>Analyzovat data v dotazu pomocí vzorů
Pokud chcete analyzovat data lze identifikovat podle vzoru opakuje napříč záznamy, můžete použít různé operátory ve [Průzkumník dat dotazovací jazyk](/azure/kusto/query/) extrahovat konkrétní data do jednoho nebo více nových vlastností.

### <a name="simple-text-patterns"></a>Jednoduchý text vzorce

Použití [analyzovat](/azure/kusto/query/parseoperator) operátor v dotazu, které se má vytvořit jeden nebo více vlastních vlastností, které může být extrahována z řetězcového výrazu. Určete vzor musí identifikovat a názvy vlastnosti, které chcete vytvořit. To je zvláště užitečná pro data s řetězci klíč hodnota se formulář podobný tomu _klíč = hodnota_.

Vezměte v úvahu vlastní protokol s daty v následujícím formátu.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Následující dotaz by analyzovat tato data do jednotlivých vlastností. Řádek s _projektu_ přidá pouze vrátit vypočítané vlastnosti, ne _RawData_, což je jedinou vlastnost obsahující jednotlivé položky z vlastního protokolu.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Tady je další příklad, který se dělí uživatelské jméno UPN v _AzureActivity_ tabulky.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Regulární výrazy
Pokud vaše data lze identifikovat s regulárním výrazem, můžete použít [funkcí, které používají regulární výrazy](/azure/kusto/query/re2) extrahovat jednotlivé hodnoty. Následující příklad používá [extrahovat](/azure/kusto/query/extractfunction) rozdělit _UPN_ pole z _AzureActivity_ záznamy a pak se vraťte jedinečných uživatelů.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Umožňuje efektivní analýzy ve velkém měřítku, Azure Monitor používá re2 verzi regulárních výrazů, které je podobné, ale nejsou identické pro některé z dalších variant regulární výraz. Odkazovat [syntaxe výrazu re2](https://aka.ms/kql_re2syntax) podrobnosti.


## <a name="parse-delimited-data-in-a-query"></a>Analyzovat data s oddělovači v dotazu
Data s oddělovači odděluje pole běžný znak, jako je čárka v souboru CSV. Použití [rozdělit](/azure/kusto/query/splitfunction) funkci parsování oddělovači dat pomocí oddělovače, který zadáte. To může být použito s [rozšířit](/azure/kusto/query/extendoperator) operátor vrátí všechna pole v datech nebo zadat jednotlivá pole mají být zahrnuty ve výstupu.

> [!NOTE]
> Protože rozdělení vrací dynamický objekt, výsledky se možná muset být explicitně přetypován na datové typy, například řetězec se používá v operátory a filtry.

Vezměte v úvahu vlastní protokol s daty v následujícím formátu CSV.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Následující dotaz by tato data analyzovat a vytvořit souhrn podle série počítané vlastnosti. První řádek rozdělí _RawData_ vlastnost do pole řetězců. Každý další řádek poskytuje název pro jednotlivé vlastnosti a přidá je do výstupu je převést na správný typ dat pomocí funkcí.

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
Pokud data naformátovaná ve struktuře známé, je možné použít jednu z funkcí v [Průzkumník dat dotazovací jazyk](/azure/kusto/query/) k analýze předdefinované struktury:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [Adresa URL](/azure/kusto/query/parseurlfunction)
- [Dotaz adresy URL](/azure/kusto/query/parseurlqueryfunction)
- [Cesta k souboru](/azure/kusto/query/parsepathfunction)
- [Uživatelský agent](/azure/kusto/query/parse-useragentfunction)
- [Řetězec verze](/azure/kusto/query/parse-versionfunction)

Následující příklad dotazu analyzuje _vlastnosti_ pole _AzureActivity_ tabulku, která je rozdělen do formátu JSON. Uloží výsledky na dynamické vlastnost s názvem _parsedProp_, což zahrnuje uživatele se pojmenovaná hodnota v kódu JSON. Tyto hodnoty jsou použity pro filtrování a shrnutí výsledků dotazu.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Tyto analýzy funkce mohou být velmi náročná, procesoru, aby je bylo možné použít pouze v případě, že váš dotaz využívá více vlastností z formátovaná data. Jednoduché porovnávání vzorů zpracování v opačném případě bude rychlejší.

Následující příklad ukazuje rozpis řadič domény lístek TGT předběžného ověření typu. Typ existuje pouze v poli EventData, což je řetězec XML, ale je potřeba žádná další data z tohoto pole. V takovém případě [analyzovat](/azure/kusto/query/parseoperator) slouží k výběru požadované část data.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Pomocí funkce pro simulaci tabulku
Můžete mít více dotazů, které provádějí stejné analýze určité tabulce. V takovém případě [vytvořit funkci](functions.md) , která vrací analyzovaná data namísto replikace analýzy logika v jednotlivých dotazů. Alias funkce namísto původní tabulky můžete pak použít v jiných dotazech.

Vezměte v úvahu výše uvedený příklad vlastního protokolu oddělený čárkami. Chcete-li použít analyzovaná data ve více dotazů, vytvoření funkce pomocí následujícího dotazu a uložit ji s aliasem, který _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Teď můžete použít alias _MyCustomCSVLog_ místo skutečný název tabulky v dotazech takto.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Další postup
* Další informace o [protokolu dotazy](log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.