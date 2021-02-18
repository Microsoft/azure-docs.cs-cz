---
title: Konfigurace monitorování v Azure Monitor pro virtuální počítače stav hosta pomocí pravidel shromažďování dat (Preview)
description: Popisuje, jak upravit výchozí monitorování v Azure Monitor pro virtuální počítače škálování hosta ve velkém rozsahu pomocí šablon Správce prostředků.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 2001fece40267ca2e3256e699d2dc253ceb10f0c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612677"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-using-data-collection-rules-preview"></a>Konfigurace monitorování v Azure Monitor pro virtuální počítače stav hosta pomocí pravidel shromažďování dat (Preview)
[Azure monitor pro virtuální počítače stav hosta](vminsights-health-overview.md) umožňuje zobrazit stav virtuálního počítače podle definice sady měření výkonu, které jsou odebírány v pravidelných intervalech. Tento článek popisuje, jak můžete upravit výchozí monitorování napříč několika virtuálními počítači pomocí pravidel shromažďování dat.


## <a name="monitors"></a>Monitory
Stav virtuálního počítače je určen [souhrnem stavu](vminsights-health-overview.md#health-rollup-policy) z každého z jeho monitorování. Existují dva typy monitorování v Azure Monitor pro virtuální počítače stav hosta, jak je znázorněno v následující tabulce.

| Monitor | Description |
|:---|:---|
| Monitorování jednotky | Měří určitý aspekt prostředku nebo aplikace. Může se jednat o kontrolu čítače výkonu za účelem zjištění výkonu nebo dostupnosti prostředku. |
| Souhrnné monitorování | Seskupuje více monitorování a poskytuje jeden souhrnný stav. Souhrnné monitorování může obsahovat jedno nebo více monitorování jednotky a další souhrnná monitorování. |

Sada monitorování, kterou používá Azure Monitor pro virtuální počítače stav hosta a jejich konfigurace, se nedá přímo změnit. Můžete vytvořit [přepsání](#overrides) , ale změnit chování výchozí konfigurace. Přepsání jsou definována v pravidlech shromažďování dat. Můžete vytvořit několik pravidel shromažďování dat, která obsahují více přepsání, abyste dosáhli požadované konfigurace monitorování.

## <a name="monitor-properties"></a>Vlastnosti monitorování
Následující tabulka popisuje vlastnosti, které lze konfigurovat na jednotlivých monitorech.

| Vlastnost | Monitory | Description |
|:---|:---|:---|
| Povoleno | Agregace<br>Jednotka | Pokud je nastaveno na true, vypočítá se monitorování stavu a přispívá se do stavu virtuálního počítače. Může aktivovat výstrahu, že je povolené upozornění. |
| Zobrazení výstrah | Agregace<br>Jednotka | Pokud je nastaveno na true, pro monitorování se aktivuje výstraha, když se přesune do stavu není v pořádku. Pokud je hodnota false, bude stav monitorování stále přispívat k stavu virtuálního počítače, který by mohl aktivovat výstrahu. |
| Upozornění | Jednotka | Kritéria pro stav upozornění. Pokud žádný není, monitorování nikdy nezadá stav upozornění. |
| Kritické | Jednotka | Kritéria pro kritický stav Pokud žádný není, monitorování nikdy nevstoupí do kritického stavu. |
| Frekvence vyhodnocování | Jednotka | Četnost vyhodnocování stavu. |
| Lookback | Jednotka | Velikost okna lookback v sekundách Podrobný popis naleznete v tématu [monitorConfiguration element](#monitorconfiguration-element) . |
| Typ vyhodnocení | Jednotka | Definuje, která hodnota se má použít ze sady ukázek. Podrobný popis naleznete v tématu [monitorConfiguration element](#monitorconfiguration-element) . |
| Minimální ukázka | Jednotka | Minimální počet hodnot, které se mají použít k výpočtu hodnoty. |
| Vzorek maxima | Jednotka | Maximální počet hodnot, které se mají použít pro výpočet hodnoty. |


## <a name="default-configuration"></a>Výchozí konfigurace
Následující tabulka uvádí výchozí konfiguraci jednotlivých monitorů. Tato výchozí konfigurace se nedá změnit přímo, ale můžete definovat [přepsání](#overrides) , které upraví konfiguraci monitorování pro některé virtuální počítače.


| Monitor | Povoleno | Zobrazení výstrah | Upozornění | Kritické | Frekvence vyhodnocování | Lookback | Typ vyhodnocení | Minimální ukázka | Maximální počet vzorků |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| Využití procesoru  | Ano | Ne | Žádné | \> 90%    | 60 s | 240 sec | Min | 2 | 3 |
| Dostupná paměť | Ano | Ne | Žádné | \< 100 MB | 60 s | 240 sec | Maximum | 2 | 3 |
| Systém souborů      | Ano | Ne | Žádné | \< 100 MB | 60 s | 120 sec | Maximum | 1 | 1 |


## <a name="overrides"></a>Přepsání
*Přepsání* změní jednu nebo více vlastností monitorování. Přepsání například může zakázat monitor, který je povolen ve výchozím nastavení, definovat kritéria upozornění pro monitorování nebo změnit kritickou prahovou hodnotu monitoru. 

Přepsání jsou definována v [pravidle shromažďování dat (DCR)](../agents/data-collection-rule-overview.md). Můžete vytvořit více chcete odeslat obecnou s různými sadami přepsání a použít je na více virtuálních počítačů. Pro virtuální počítač můžete použít sadu DCR tak, že vytvoříte přidružení, jak je popsáno v tématu [Konfigurace shromažďování dat pro agenta Azure monitor (Preview)](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations).


## <a name="multiple-overrides"></a>Vícenásobné přepsání

Jeden monitor může mít více přepsání. Pokud přepsání definují různé vlastnosti, výsledná konfigurace je kombinací všech přepsání.

Například `memory|available` monitorování neurčuje prahovou hodnotu upozornění nebo ve výchozím nastavení povolí upozorňování. Vezměte v úvahu následující přepsání, která se vztahují na toto monitorování:

- Override 1 definuje `alertConfiguration.isEnabled` hodnotu vlastnosti jako `true`
- Přepsání 2 definuje `monitorConfiguration.warningCondition` s podmínkou prahová hodnota `< 250` .

Výslednou konfigurací by bylo monitorování, které přejde do stavu varování, pokud je k dispozici méně než 250Mb paměti a vytvoří výstrahu závažnosti 2 a také přejde do kritického stavu, pokud je k dispozici méně než 100 MB dostupné paměti, a vytvoří Závažnost výstrahy 1 (nebo změní stávající výstrahu ze závažnosti 2 na hodnotu 1, pokud již existovala).

Pokud dvě přepsání definují stejnou vlastnost na stejném monitorování, bude mít jedna hodnota přednost. Přepsání se použijí v závislosti na jejich [oboru](#scopes-element), od nejobecnosti po nejvíce k nejpřesnější. To znamená, že nejpřesnější přepsání budou mít největší pravděpodobnost použití. Konkrétní objednávka je následující:

1. Globální 
2. Předplatné
3. Skupina prostředků
4. Virtuální počítač. 

Pokud více přepsání na stejné úrovni oboru definuje stejnou vlastnost na stejném monitorování, pak se aplikují v pořadí, ve kterém se nacházejí v DCR. Pokud jsou přepsání v různých chcete odeslat obecnou, budou použita v abecedním pořadí identifikátorů prostředků DCR.


## <a name="data-collection-rule-configuration"></a>Konfigurace pravidla shromažďování dat
Prvky JSON v pravidle shromažďování dat, které definují přepsání, jsou popsány v následujících částech. V [pravidle kolekce ukázkových dat](#sample-data-collection-rule)je k dispozici kompletní příklad.

## <a name="extensions-structure"></a>struktura rozšíření
Stav hosta se implementuje jako rozšíření agenta Azure Monitor, takže přepsání jsou definovaná v `extensions` elementu pravidla shromažďování dat. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| Prvek | Povinné | Popis |
|:---|:---|:---|
| `name` | Ano | Uživatelem definovaný řetězec pro rozšíření |
| `streams` | Yes | Seznam datových proudů, na které se budou posílat data o stavu hosta To musí zahrnovat **Microsoft-HealthStateChange**.  |
| `extensionName` | Yes | Název rozšíření Toto musí být **HealthExtension**. |
| `extensionSettings` | Yes | Pole `healthRuleOverride` prvků, které se má použít pro výchozí konfiguraci. |


## <a name="extensionsettings-element"></a>element platný ExtensionSettings
Obsahuje nastavení pro rozšíření.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| Prvek | Povinné | Popis |
|:---|:---|:---|
| `schemaVersion` | Ano | Řetězec definovaný společností Microsoft pro reprezentaci očekávaného schématu elementu. V současné době musí být nastavené na 1,0. |
| `contentVersion` | No | Řetězec definovaný uživatelem pro sledování různých verzí konfigurace stavu (v případě potřeby). |
| `healthRuleOverrides` | Yes | Pole `healthRuleOverride` prvků, které se má použít pro výchozí konfiguraci. |

## <a name="healthrulesoverrides-element"></a>element healthRulesOverrides
Obsahuje jeden nebo více `healthRuleOverride` prvků, které definují přepsání.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| Prvek | Povinné | Popis |
|:---|:---|:---|
| `scopes` | Ano | Seznam jednoho nebo více oborů, které určují virtuální počítače, na které se toto přepsání vztahuje. I když je DCR přidružená k virtuálnímu počítači, musí virtuální počítač spadat do oboru, aby bylo přepsání použito. |
| `monitors` | Yes | Seznam jednoho nebo více řetězců, které definují, které monitory budou toto přepsání přijímat.  |
| `monitorConfiguration` | No | Konfigurace monitorování včetně stavů a způsobu jejich výpočtu. |
| `alertConfiguration` | No | Konfigurace výstrah pro monitorování. |
| `isEnabled` | No | Určuje, zda je monitorování povoleno nebo nikoli. Zakázané přepínače monitoru pro speciální *zakázaný* stav a stav zakázáno, pokud nejsou znovu povoleny. Pokud tento parametr vynecháte, bude monitorování dědit jeho stav z nadřazeného monitorování v hierarchii. |


## <a name="scopes-element"></a>Scope – element
Každé přepsání má jeden nebo více oborů, které definují, na které virtuální počítače se má přepsání použít. Oborem může být předplatné, skupina prostředků nebo jeden virtuální počítač. I v případě, že je přepsání v sadě DCR přidružené ke konkrétnímu virtuálnímu počítači, použije se pouze pro tento virtuální počítač, pokud je virtuální počítač v jednom z oborů přepsání. To vám umožní široce přidružit menší počet chcete odeslat obecnou k sadě virtuálních počítačů, ale poskytovat podrobnou kontrolu nad přiřazením každého přepsání v rámci DCR. Možná budete chtít vytvořit malou sadu chcete odeslat obecnou a přidružit ji k sadě virtuálních počítačů pomocí zásad a zároveň určit přepsání monitorování stavu pro různé podmnožiny těchto virtuálních počítačů pomocí elementu scopes.

V následující tabulce jsou uvedeny příklady různých oborů.

| Obor | Příklad |
|:---|:---|
| Jeden virtuální počítač | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| Všechny virtuální počítače ve skupině prostředků | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| Všechny virtuální počítače v rámci předplatného | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| Všechny virtuální počítače, ke kterým je pravidlo shromažďování dat přidružené | `*` |


## <a name="monitors-element"></a>monitory – element
Seznam jednoho nebo více řetězců, které definují, které monitory v hierarchii stavu obdrží toto přepsání. Každý prvek může být název monitorování nebo název typu, který odpovídá jednomu nebo více monitorům, které obdrží toto přepsání. 

```json
"monitors": [
    "<monitor name>"
 ],
```



Následující tabulka uvádí aktuální dostupné názvy monitorování.

| Název typu | Název | Description |
|:---|:---|:---|
| kořen | kořen | Monitor nejvyšší úrovně představující stav virtuálního počítače | |
| využití CPU | využití CPU | Monitorování využití procesoru. | |
| logické disky | logické disky | Agregované monitorování stavu všech monitorovaných disků ve virtuálním počítači s Windows. | |
| logické disky\|* | logické disky \| C:<br>logický disk \| D: | Agregované monitorování stavu sledování daného disku na virtuálním počítači s Windows. | 
| na logických discích – \| * \| volné místo | logické disky \| C: \| volné místo<br>logické disky \| D: \| volné místo | Monitorování volného místa na disku na virtuálním počítači s Windows |
| systémy souborů | systémy souborů | Agregované monitorování pro stav všech systémů souborů na virtuálním počítači se systémem Linux. |
| systémy souborů\|* | systémy souborů\|/<br>/MNT systému souborů \| | Agregovaný monitor sledování stavu systému souborů virtuálního počítače se systémem Linux. | systémy souborů|/var/log |
| systém souborů \| * \| – volné místo | systém souborů \| / \| – volné místo<br>systém souborů \| /mnt \| volné místo | Monitorování volného místa na disku v systému souborů virtuálních počítačů se systémem Linux. | 
| paměť | paměť | Agregované monitorování stavu paměti virtuálního počítače. | |
| \|dostupná paměť| \|dostupná paměť | Sledování dostupné paměti na virtuálním počítači. | |


## <a name="alertconfiguration-element"></a>element alertConfiguration
Určuje, zda má být z monitorování vytvořena výstraha.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| Prvek | Povinné | Popis | 
|:---|:---|:---|
| `isEnabled` | No | Pokud je hodnota nastavena na true, monitor při přechodu do stavu v pořádku nebo varování vygeneruje výstrahu a vyřeší upozornění. Pokud je hodnota false nebo vynechána, nevygeneruje se žádná výstraha.  |


## <a name="monitorconfiguration-element"></a>element monitorConfiguration
Platí jenom pro monitory jednotek. Definuje konfiguraci monitorování včetně stavů a způsobu jejich výpočtu.

Parametry definují algoritmus pro výpočet hodnoty metriky pro porovnání s mezními hodnotami. Místo toho, aby se jednalo o jednu ukázku dat ze základní metriky, monitor vyhodnocuje několik ukázek metrik obdržených v okně z doby hodnocení a `lookbackSec` před. Budou se brát v úvahu všechny vzorky přijaté v daném časovém rámci, a pokud je počet vzorků větší než `maxSamples` , starší vzorky `maxSamples` se ignorují. 

V případě, že je v intervalu lookback méně vzorků než `minSamples` , monitorování se přepne do *neznámého* stavu, což znamená, že k informovanému rozhodnutí o stavu základních metrik není dostatek dat. Pokud `minSamples` je k dispozici větší počet vzorků, je agregační funkce určená parametrem evaluationType spuštěná v rámci sady pro výpočet jedné hodnoty.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| Prvek | Povinné | Popis | 
|:---|:---|:---|
| `evaluationFrequencySecs` | No | Definuje četnost pro vyhodnocení stavu. Každé monitorování je vyhodnoceno v době spuštění agenta a v pravidelných intervalech, které jsou definovány tímto parametrem poté. |
| `lookbackSecs`   | No | Velikost okna lookback v sekundách |
| `evaluationType` | No | `min` – převzít minimální hodnotu z celé sady vzorků<br>`max` -přijmout maximální hodnotu z celé sady vzorků<br>`avg` – přebírat průměr hodnot sady ukázek<br>`all` – Porovná každou jednotlivou hodnotu v množině prahových hodnot. Monitoruje stav přepínačů, pokud a pouze v případě, že všechny ukázky v sadě nastavují mezní podmínku. |
| `minSamples`     | No | Minimální počet hodnot, které se mají použít k výpočtu hodnoty. |
| `maxSamples`     | No | Maximální počet hodnot, které se mají použít pro výpočet hodnoty. |
| `warningCondition`  | No | Prahová a srovnávací logika pro podmínku upozornění |
| `criticalCondition` | No | Prahová a srovnávací logika pro kritickou podmínku. |


## <a name="warningcondition-element"></a>element warningCondition
Definuje logiku prahové hodnoty a porovnání pro podmínku upozornění. Pokud tento prvek není zahrnutý, monitorování se nikdy nepřepne do stavu upozornění.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Vlastnost | Povinné | Popis | 
|:---|:---|:---|
| `isEnabled` | No | Určuje, zda je povolena podmínka. Pokud je nastavená **hodnota false**, je podmínka zakázaná, i když je možné nastavit mezní hodnoty a vlastnosti operátoru. |
| `threshold` | No | Definuje prahovou hodnotu pro porovnání vyhodnocené hodnoty. |
| `operator`  | No | Definuje relační operátor pro použití ve výrazu prahové hodnoty. Možné hodnoty: >, <, >=, <=, = =. |


## <a name="criticalcondition-element"></a>element criticalCondition
Definuje logiku prahové hodnoty a porovnání pro kritickou podmínku. Pokud tento prvek není zahrnutý, monitorování se nikdy nepřepne do kritického stavu.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Vlastnost | Povinné | Popis | 
|:---|:---|:---|
| `isEnabled` | No | Určuje, zda je povolena podmínka. Pokud je nastavená **hodnota false**, je podmínka zakázaná, i když je možné nastavit mezní hodnoty a vlastnosti operátoru. |
| `threshold` | No | Definuje prahovou hodnotu pro porovnání vyhodnocené hodnoty. |
| `operator`  | No | Definuje relační operátor pro použití ve výrazu prahové hodnoty. Možné hodnoty: >, <, >=, <=, = =. |

## <a name="sample-data-collection-rule"></a>Pravidlo shromažďování ukázkových dat
Pravidlo shromažďování ukázkových dat, které povoluje monitorování hostů, najdete v tématu [Povolení virtuálního počítače pomocí šablony Správce prostředků](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template).


## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [pravidlech shromažďování dat](../agents/data-collection-rule-overview.md).