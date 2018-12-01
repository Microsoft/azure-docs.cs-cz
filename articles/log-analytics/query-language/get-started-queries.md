---
title: Začínáme s dotazy ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek obsahuje kurz pro získávání spuštěn zápis dotazů v Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.openlocfilehash: cd5333cc36afa9c914d628fac8f1fd865d3f7663
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678861"
---
# <a name="get-started-with-queries-in-log-analytics"></a>Začínáme s dotazy v Log Analytics


> [!NOTE]
> By se měla Dokončit [začít používat portál Analytics](get-started-analytics-portal.md) před tímto kurzem.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

V tomto kurzu se dozvíte, psaní dotazů Azure Log Analytics. To se dozvíte, jak do:

- Zjistit strukturu dotazy.
- Řazení výsledků dotazu
- Filtrování výsledků dotazu
- Zadejte časový rozsah
- Vyberte pole, která chcete zahrnout do výsledků
- Definice a používání vlastních polí
- Agregace a skupinových výsledky


## <a name="writing-a-new-query"></a>Napište nový dotaz
Dotazy můžete spustit buď pomocí názvu tabulky nebo *hledání* příkazu. Měli byste začít s názvem tabulky, protože definuje vymazat obor pro dotaz a zvyšuje výkon dotazů a relevance výsledků.

> [!NOTE]
> Dotazovací jazyk Azure Log Analytics je velká a malá písmena. Klíčová slova jazyka jsou obvykle napsány malé. Při použití názvy tabulek nebo sloupců v dotazu, ujistěte se, že chcete mít správnou velikost, jak je znázorněno v podokně schématu.

### <a name="table-based-queries"></a>Dotazy založené na tabulku
Azure Log Analytics slouží k uspořádání dat v tabulkách, každá skládá z více sloupců. Všechny tabulky a sloupce se zobrazí v podokně schématu portálu Analytics. Identifikujte tabulku zajímá a potom se podívejte na části dat:

```Kusto
SecurityEvent
| take 10
```

Dotaz je znázorněno výše vrátí 10 výsledky z *SecurityEvent* tabulky v žádné konkrétní pořadí. To je velmi běžné způsob, jak provést první pohled na tabulku a pochopit jeho strukturu a obsahu. Podívejme se, jak je sestavena:

* Spustí dotaz s názvem tabulky *SecurityEvent* – Tato část definuje rozsah dotazu.
* Znak svislé čáry (|) odděluje příkazy, takže výstup první z nich ve vstupu následující příkaz. Můžete přidat libovolný počet potrubím elementy.
* Po přesměrování je **trvat** příkaz, který vrátí určitý počet libovolného záznamy z tabulky.

Ve skutečnosti spouštět dotaz i bez přidání `| take 10` –, který by stále platná, ale můžou se zobrazovat výsledky až 10 000.

### <a name="search-queries"></a>Vyhledávací dotazy
Vyhledávací dotazy jsou menší strukturované a obecně více vhodných pro hledání záznamů, které zahrnují určité hodnoty v některém ze svých sloupců:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Tento dotaz vyhledá *SecurityEvent* tabulky pro záznamy obsahující frázi "Kryptografický". Tyto záznamy se vrátí a zobrazí 10 záznamů. Pokud jsme vynechat, nechte `in (SecurityEvent)` částečně a pouze spustit `search "Cryptographic"`, hledání se přenášejí prostřednictvím *všechny* tabulek, které by trvat déle a být méně efektivní.

> [!NOTE]
> Ve výchozím nastavení, časový rozsah _posledních 24 hodin_ nastavena. Pokud chcete použít jiný rozsah, použijte Výběr času (umístěný vedle *Přejít* tlačítko) nebo přidat explicitní čas filtr rozsahu do dotazu.

## <a name="sort-and-top"></a>Řazení a nahoře
Zatímco **trvat** je užitečné získat o pár záznamů, výsledky jsou vybrané a zobrazí bez určitého pořadí. Získat seřazený zobrazení, může **řazení** podle preferované sloupce:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Který může vrátit ale příliš mnoho výsledků a může trvat nějakou dobu. Seřadí výše uvedeném dotazu *celý* SecurityEvent tabulku podle sloupce TimeGenerated. Portál Analytics pak omezení zobrazení na Zobrazit pouze 10 000 záznamů. Tento přístup není samozřejmě optimální.

Nejlepší způsob, jak získat jenom nejnovější 10 záznamů, je použití **horní**, který seřadí celou tabulku na straně serveru a pak vrátí prvních záznamů:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Sestupné pořadí, řazení, takže obvykle vynechat výchozí nastavení je **desc** argument. Výstup bude vypadat takto:

![Prvních 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Kdy: filtrování na podmínce
Filtry, jak je uvedeno podle názvu, filtrovat data podle určité podmínky. Toto je nejběžnější způsob, jak omezit výsledky dotazu na příslušné informace.

Přidání filtru do dotazu, použijte **kde** operátor za nímž následuje jedna nebo více podmínek. Například následující dotaz vrátí pouze *SecurityEvent* záznamy, jejichž _úroveň_ rovná _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Při psaní podmínky filtru, můžete použít následující výrazy:

| Výraz | Popis | Příklad: |
|:---|:---|:---|
| == | Kontroly rovnosti<br>(malá a velká písmena) | `Level == 8` |
| =~ | Kontroly rovnosti<br>(velká a malá písmena) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| ! =, <> | Kontrola nerovnosti<br>(i výraz je shodný s) | `Level != 4` |
| *a*, *nebo* | Vyžaduje mezi podmínky| `Level == 16 or CommandLine != ""` |

Chcete-li filtrovat podle několika podmínek, můžete použít **a**:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

nebo kanálu více **kde** prvky jednu po druhé:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Hodnoty mohou mít různé typy, takže možná budete muset k provádění porovnání na správný typ přetypování. Například SecurityEvent *úroveň* sloupec je typu řetězec, proto musíte přetypovat na číselný typ, jako *int* nebo *dlouhé*, než budete moct použít na něm číselné operátory: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Zadejte časový rozsah

### <a name="time-picker"></a>Výběr času
Výběr času vedle tlačítka Spustit je a indikuje, že jsme při dotazování pouze záznamy z posledních 24 hodin. Toto je výchozí časový rozsah, použije na všechny dotazy. Chcete-li získat pouze záznamy z poslední hodina, vyberte _za poslední hodinu_ a spusťte dotaz znovu.

![Výběr času](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtr času v dotazu
Můžete také definovat vlastní časový rozsah tak, že přidáte filtr času v dotazu. Doporučujeme umístit filtr času bezprostředně za název tabulky: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

Ve výše uvedené filtr času `ago(30m)` znamená "30 minutami", takže tento dotaz vrátí pouze záznamy z posledních 30 minut. Jiné jednotky doby zahrnout dny (2d) (25 mil.) minut a sekund (10 s).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt a rozšířit: vyberte a výpočetní sloupce
Použití **projektu** vybrat konkrétní sloupce se mají zahrnout do výsledků:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Předchozí příklad generuje tento výstup:

![Protokolovat výsledky analýzy projektu](media/get-started-queries/project.png)

Můžete také použít **projektu** přejmenování sloupců a definovat nové značky. Následující příklad používá projektu provést následující kroky:

* Vyberte pouze *počítače* a *TimeGenerated* původní sloupce.
* Přejmenovat *aktivity* sloupec *EventDetails*.
* Vytvoří nový sloupec s názvem *EventCode*. **Substring()** funkce se používá ke stahování jenom první čtyři znaky z pole aktivity.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**rozšíření** uchovává všechny původní sloupce sady výsledků dotazu a definuje další značky. Následující dotaz používá **rozšířit** přidáte *localtime* sloupec, který obsahuje lokalizované hodnoty TimeGenerated.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend localtime = TimeGenerated-8h
```

## <a name="summarize-aggregate-groups-of-rows"></a>Shrnutí: agregační skupiny řádků
Použití **shrnout** identifikovat skupinami záznamů, podle jednoho nebo více sloupců, a na ně vztahují agregace. Nejběžnější použití nástroje **shrnout** je *počet*, který vrátí počet výsledků v každé skupině.

Následující dotaz kontroluje všechny *výkonu* záznamy od poslední hodiny skupin je podle *ObjectName*a vrátí počet záznamů v každé skupině: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Někdy je vhodné definovat skupiny tak, že více dimenzí. Každá jedinečná kombinace těchto hodnot definuje samostatnou skupinu:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Dalším běžným způsobem použití je provést matematické a statistické výpočty s každou skupinu. Například následující vypočítá průměr *CounterValue* pro jednotlivé počítače:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Bohužel nemají smysl výsledky tohoto dotazu, protože jsme kombinovat společně různých čítačů výkonu. Chcete-li to lépe vystihuje, byste měli vypočítat průměr odděleně pro každou kombinaci *CounterName* a *počítače*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Vytvořit souhrn podle sloupce pro čas
Seskupování výsledků můžete také založené na sloupec času nebo jinou hodnotu. Jednoduše sumarizace `by TimeGenerated` ale by vytvořit skupiny pro každou jeden milisekund v příslušném časovém rozsahu, protože to jsou jedinečné hodnoty. 

Chcete-li vytvořit skupiny založené na průběžné hodnoty, je nejlepší přerušit jednotkami pomocí rozsahu **bin**. Následující dotaz analyzuje *výkonu* záznamy, které měří volná paměť (*počet MB k dispozici*) v určitém počítači. Vypočítá průměrnou hodnotu pro každé období, pokud 1 hodina, posledních 7 dnech:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Aby byl srozumitelnější výstup, vyberete zobrazíte jako čas – graf, zobrazuje dostupné paměti v průběhu času:

![Log Analytics paměti v čase](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Další postup

- Další informace o [zápis vyhledávacích dotazů](search-queries.md)
