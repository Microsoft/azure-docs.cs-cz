---
title: Azure Monitor pro konfiguraci monitorování stavu kontejnerů | Microsoft Docs
description: Tento článek poskytuje obsah popisující podrobnou konfiguraci monitorování stavu v Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: d2d602d767fa6a39b7f72650c426c90be210a6ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405046"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Průvodce konfigurací monitorování stavu kontejnerů Azure Monitor

Monitory jsou primárním prvkem pro měření stavu a zjišťování chyb v Azure Monitor pro kontejnery. Tento článek vám pomůže porozumět konceptům, jak se měří stav, a prvků, které tvoří model stavu pro monitorování a hlášení stavu clusteru Kubernetes s funkcí [stavu (Preview)](container-insights-health.md) .

>[!NOTE]
>Funkce Health je v tuto chvíli ve verzi Public Preview.
>

## <a name="monitors"></a>Monitory

Monitorování měří stav některých aspektů spravovaného objektu. Monitorování mají dva nebo tři stavy. Monitorování bude v daném okamžiku v jednom a pouze jednom z jeho možných stavů. Když je monitor načtený agentem kontejneru, je inicializován do stavu v pořádku. Stav se změní pouze v případě, že jsou zjištěny zadané podmínky pro jiný stav.

Celkový stav konkrétního objektu se určí na základě stavu každého z jeho monitorování. Tato hierarchie je znázorněna v podokně hierarchie stavu v Azure Monitor pro kontejnery. Zásady pro zahrnutí stavu jsou součástí konfigurace agregovaných monitorování.

## <a name="types-of-monitors"></a>Typy monitorování

|Monitorování | Popis | 
|--------|-------------|
| Monitorování jednotky |Monitorování jednotek měří určitý aspekt prostředku nebo aplikace. To může být zkontrolováno čítače výkonu k určení výkonu prostředku nebo jeho dostupnosti. |
|Agregované monitorování | Agregované monitory seskupují více monitorování, aby poskytovaly jediný stav agregovaného stavu. Monitory jednotek se obvykle konfigurují v rámci konkrétního agregovaného monitorování. Například monitorování agregované pro uzel shrnuje stav využití CPU uzlu, využití paměti a stav uzlu.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Souhrnná zásada Shrnutí stavu monitorování

Každé agregované monitorování definuje zásady souhrnu stavu, což je logika, která se používá k určení stavu agregovaného monitorování na základě stavu monitorování. Možné souhrnné zásady stavu pro agregované monitorování jsou následující:

#### <a name="worst-state-policy"></a>Zásada nejhorší stav

Stav agregovaného monitorování odpovídá stavu podřízeného monitorování s nejhorším stavem. Toto jsou nejběžnější zásady používané agregovanými monitory.

![Příklad souhrnného stavu souhrnu agregovaného monitorování](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Procentuální zásady

Zdrojový objekt se shoduje s nejhorším stavem jednoho člena zadaného procenta cílových objektů v nejlepším stavu. Tato zásada se používá v případě, že určité procento cílových objektů musí být v pořádku, aby cílový objekt byl považován za v pořádku. Procentní zásady řadí monitory v sestupném pořadí podle závažnosti a agregovaný stav monitorování je vypočítán jako nejhorší stav N% (N je určen parametrem konfigurace *StateThresholdPercentage*).

Předpokládejme například, že existuje pět instancí kontejneru image kontejneru a jejich jednotlivé stavy jsou **kritická**, **upozorňující**, **v**dobrém stavu **v** **dobrém stavu.**  Stav monitorování využití CPU kontejneru bude **kritický**, protože nejhorší stav 90% kontejnerů je při seřazení v sestupném pořadí závažnosti **kritický** .

## <a name="understand-the-monitoring-configuration"></a>Pochopení konfigurace monitorování

Azure Monitor for Containers zahrnuje řadu klíčových scénářů monitorování, které jsou nakonfigurované následujícím způsobem.

### <a name="unit-monitors"></a>Monitory jednotek

|**Název monitorování** | Typ monitorování | **Popis** | **Parametr** | **Hodnota** |
|-----------------|--------------|-----------------|---------------|-----------|
|Využití paměti uzlu |Monitorování jednotky |Toto monitorování vyhodnocuje využití paměti uzlu každou minutu pomocí cadvisor nahlášených dat. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Využití procesoru uzlů |Monitorování jednotky |Toto monitorování zkontroluje využití CPU v uzlu každou minutu pomocí cadvisor nahlášených dat. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Stav uzlu |Monitorování jednotky |Toto monitorování ověří podmínky uzlu hlášené nástrojem Kubernetes.<br> V současné době jsou zkontrolovány následující podmínky uzlu: tlak na disk, tlak, tlak PID, nedostatek disku, nedostupná síť, stav připraveno pro uzel.<br> Pokud má *nedostatek místa na disku* nebo v síti **hodnotu true**, monitorování se změní na **kritický** stav, pokud není *k dispozici* žádný z výše uvedených podmínek.<br> Pokud se jakékoli jiné podmínky rovnají hodnotě **true**, kromě stavu **připraveno** , monitorování se změní na stav **Upozornění** . | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|Využití paměti kontejneru |Monitorování jednotky |Toto monitorování hlásí kombinovaný stav využití paměti (RSS) instancí kontejneru.<br> Provádí jednoduché porovnání, které porovnává jednotlivé vzorky s jedinou prahovou hodnotou, kterou určuje parametr konfigurace **ConsecutiveSamplesForStateTransition**.<br> Jeho stav je počítáno jako nejhorší stav 90% instancí kontejneru (StateThresholdPercentage) seřazený v sestupném pořadí podle závažnosti stavu kontejneru (tj. kritická, varovná, v pořádku).<br> Pokud z instance kontejneru není přijat žádný záznam, je stav instance kontejneru hlášen jako **Neznámý**a má vyšší prioritu v pořadí řazení v **kritickém** stavu.<br> Každý jednotlivý stav instance kontejneru se počítá pomocí prahových hodnot uvedených v konfiguraci. Pokud je využití nad kritickou prahovou hodnotou (90%), pak je instance v **kritickém** stavu, pokud je menší než kritická prahová hodnota (90%). ale větší než prahová hodnota pro upozornění (80%), instance je ve stavu **Upozornění** . V opačném případě je v **dobrém** stavu. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Využití CPU kontejneru |Monitorování jednotky |Toto monitorování hlásí kombinovaný stav využití procesoru instancí kontejneru.<br> Provádí jednoduché porovnání, které porovnává jednotlivé vzorky s jedinou prahovou hodnotou, kterou určuje parametr konfigurace **ConsecutiveSamplesForStateTransition**.<br> Jeho stav je počítáno jako nejhorší stav 90% instancí kontejneru (StateThresholdPercentage) seřazený v sestupném pořadí podle závažnosti stavu kontejneru (tj. kritická, varovná, v pořádku).<br> Pokud z instance kontejneru není přijat žádný záznam, je stav instance kontejneru hlášen jako **Neznámý**a má vyšší prioritu v pořadí řazení v **kritickém** stavu.<br> Každý jednotlivý stav instance kontejneru se počítá pomocí prahových hodnot uvedených v konfiguraci. Pokud je využití nad kritickou prahovou hodnotou (90%), pak je instance v **kritickém** stavu, pokud je menší než kritická prahová hodnota (90%). ale větší než prahová hodnota pro upozornění (80%), instance je ve stavu **Upozornění** . V opačném případě je v **dobrém** stavu. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Systémová úloha – Příprava na přípravu |Monitorování jednotky |Toto monitorování hlásí stav na základě procenta lusků ve stavu připraveno v daném zatížení. Stav je nastaven na **kritickou** , pokud je méně než 100% lusků v **dobrém** stavu. |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Stav rozhraní API pro Kube |Monitorování jednotky |Toto monitorování hlásí stav služby Kube API. Monitorování je v kritickém stavu v případě, že koncový bod rozhraní API Kube není k dispozici. U tohoto konkrétního monitorování je stav určen vytvořením dotazu na koncový bod Nodes pro server Kube-API. Cokoli jiného než kód odpovědi OK změní monitor na **kritický** stav. | Žádné vlastnosti konfigurace |||

### <a name="aggregate-monitors"></a>Agregované monitory

|**Název monitorování** | **Popis** | **Algoritmus** |
|-----------------|-----------------|---------------|
|Uzel |Toto monitorování je souhrnem všech monitorů uzlů. Odpovídá stavu podřízeného monitorování s nejhorším stavem:<br> Využití procesoru uzlů<br> Využití paměti uzlu<br> Stav uzlu | Nejhorší část|
|Fond uzlů |Toto monitorování hlásí kombinovaný stav všech uzlů ve fondu uzlů *neznámá*. Toto je tři monitorování stavu, jejichž stav je založen na nejhorším stavu 80% uzlů v rámci fondu uzlů seřazený v sestupném pořadí závažností stavů uzlů (tzn. kritická, varovná, v pořádku).|Procento |
|Uzly (nadřazený uzel fondu uzlů) |Toto je agregované monitorování všech fondů uzlů. Jeho stav vychází z nejhoršího stavu svých podřízených monitorování (to znamená fondů uzlů přítomných v clusteru). |Nejhorší část |
|Cluster (nadřazený uzel uzlů/<br> Infrastruktura Kubernetes) |Toto je nadřazené monitorování, které odpovídá stavu podřízeného monitorování s nejhorším stavem, který je Kubernetes infrastrukturou a uzly. |Nejhorší část |
|Infrastruktura Kubernetes |Toto monitorování hlásí kombinovaný stav komponent spravované infrastruktury clusteru. jeho stav je vypočítán jako nejhorší z těchto stavů monitorování, tj. Kube – systémové úlohy a stav serveru rozhraní API. |Nejhorší část|
|Systémová úloha |Toto monitorování hlásí stav úlohy Kube-System. Toto monitorování odpovídá stavu podřízeného monitorování s nejhorším stavem, který je **ve stavu připraveno** (monitorování a kontejnery v rámci úlohy). |Nejhorší část |
|Kontejner |Toto monitorování hlásí celkový stav kontejneru v daném zatížení. Toto monitorování odpovídá stavu podřízeného monitorování s nejhorším stavem, který je **využití procesoru** a monitorování **využití paměti** . |Nejhorší část |

## <a name="next-steps"></a>Další kroky

Seznamte se s [monitorováním stavu clusteru](container-insights-health.md) , abyste se seznámili se zobrazením stavu clusteru Kubernetes.
