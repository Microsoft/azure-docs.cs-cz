---
title: Azure Monitor pro monitorování stavu kontejnerů konfigurace | Dokumenty společnosti Microsoft
description: Tento článek obsahuje obsah popisující podrobnou konfiguraci monitorování stavu v Azure Monitor u kontejnerů.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055703"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Azure Monitor pro monitorování kontejnerů průvodce konfigurací konfigurace monitoru

Monitory jsou primárním prvkem pro měření stavu a zjišťování chyb v Azure Monitor u kontejnerů. Tento článek vám pomůže pochopit koncepty, jak se měří stav a prvky, které tvoří model stavu sledovat a sestavy o stavu clusteru Kubernetes s funkcí [Stav (náhled).](container-insights-health.md)

>[!NOTE]
>Funkce Stav je v tuto chvíli ve verzi Public Preview.
>

## <a name="monitors"></a>Monitory

Monitor měří stav některých aspektů spravovaného objektu. Monitory každý mají dva nebo tři stavy. Monitor bude v jednom a pouze v jednom z jeho potenciálních stavů v daném okamžiku. Když je monitor načten kontejnerizovaným agentem inicializován do stavu v pořádku. Stav se změní pouze v případě, že jsou zjištěny zadané podmínky pro jiný stav.

Celkový stav určitého objektu je určen ze stavu každého z jeho monitorů. Tato hierarchie je znázorněno v podokně hierarchie stavu v Azure Monitor pro kontejnery. Zásady pro zahrnutí stavu je součástí konfigurace agregační monitory.

## <a name="types-of-monitors"></a>Typy monitorů

|Monitorování | Popis | 
|--------|-------------|
| Monitor jednotky |Monitorování jednotky měří některé aspekty prostředku nebo aplikace. To může být kontrola čítače výkonu k určení výkonu prostředku nebo jeho dostupnosti. |
|Agregační monitor | Agregované monitory seskupí více monitorů a poskytují jeden agregovaný stav stavu. Monitorování jednotek jsou obvykle konfigurovány pod konkrétním agregačním monitorem. Například agregační monitorování uzlu shrnuje stav využití procesoru uzlu, využití paměti a stavu uzlu.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Agregovat zásady souhrnu stavu monitorování

Každý agregační monitorování definuje zásady souhrnu stavu, což je logika, která se používá k určení stavu agregované monitorování na základě stavu monitorů pod ním. Možné zásady souhrnu stavu pro agregované monitorování jsou následující:

#### <a name="worst-state-policy"></a>Nejhorší státní politika

Stav agregovaného monitorování odpovídá stavu podřízeného monitoru s nejhorším stavem. Toto je nejběžnější zásada používaná agregovanými monitory.

![Příklad nejhoršího stavu souhrnu souhrnného souhrnného souhrnného souhrnného souhrnného souhrnného ukazatele](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Procentuální politika

Zdrojový objekt odpovídá nejhoršímu stavu jednoho člena zadaného procenta cílových objektů v nejlepším stavu. Tato zásada se používá v případě, že určité procento cílových objektů musí být v pořádku, aby cílový objekt byl považován za zdravý. Procento zásadseřadí monitory v sestupném pořadí podle závažnosti stavu a agregační monitor je vypočítán jako nejhorší stav N% (N je diktováno parametrem konfigurace *StateThresholdPercentage*).

Předpokládejme například, že existuje pět instancí kontejneru image kontejneru a jejich jednotlivé stavy jsou **kritické**, **Upozornění**, **V pořádku,** **V pořádku**, **V pořádku**.  Stav monitorování využití procesoru kontejneru bude **kritický**, protože nejhorší stav 90 % kontejnerů je **kritický** při řazení v sestupném pořadí podle závažnosti.

## <a name="understand-the-monitoring-configuration"></a>Principy konfigurace monitorování

Azure Monitor pro kontejnery obsahuje řadu klíčových scénářů monitorování, které jsou nakonfigurované následujícím způsobem.

### <a name="unit-monitors"></a>Jednotkové monitory

|**Název monitoru** | Typ monitoru | **Popis** | **Parametr** | **Hodnotu** |
|-----------------|--------------|-----------------|---------------|-----------|
|Využití paměti uzlu |Monitor jednotky |Tento monitor vyhodnocuje využití paměti uzlu každou minutu pomocí cadvisor hlášeny data. |ConsecutiveSamplesForStateTransition<br> FailifGreaterThanPercentage<br> WarnifGreaterThanPercentage | 3<br> 90<br> 80  ||
|Využití procesoru uzlu |Monitor jednotky |Tento monitor kontroluje využití procesoru uzlu každou minutu pomocí cadvisor vykázaných dat. | ConsecutiveSamplesForStateTransition<br> FailifGreaterThanPercentage<br> WarnifGreaterThanPercentage | 3<br> 90<br> 80  ||
|Stav uzlu |Monitor jednotky |Tento monitor kontroluje stavuzlu hlášený kubernetes.<br> V současné době jsou kontrolovány následující podmínky uzlu: Disk tlak, tlak paměti, TLAK PID, Mimo disk, Síť není k dispozici, Připraveno stav pro uzel.<br> Pokud je mimo výše uvedené podmínky **splněny** *podmínky není k* *dispozici* , monitor se změní na **kritický** stav.<br> Pokud se jiné podmínky rovnají **true**, kromě stavu **Připraveno,** monitor se změní na stav **Upozornění.** | NodeConditionTypeForFailedState | outofdisk,síť není k dispozici ||
|Využití paměti kontejneru |Monitor jednotky |Toto monitorování hlásí kombinovaný stav využití paměti (RSS) instancí kontejneru.<br> Provádí jednoduché porovnání, které porovnává každý vzorek na jednu prahovou hodnotu a zadaný konfiguračníparametr **ConsecutiveSamplesForStateTransition**.<br> Jeho stav se vypočítá jako nejhorší stav 90 % instancí kontejneru (StateThresholdPercentage), seřazený v sestupném pořadí podle závažnosti stavu kontejneru (tj. kritický, upozornění, v pořádku).<br> Pokud není přijat žádný záznam z instance kontejneru, je stav instance kontejneru hlášen jako **Neznámý**a má vyšší prioritu v pořadí řazení nad **kritickým** stavem.<br> Stav každé instance kontejneru se vypočítá pomocí prahových hodnot určených v konfiguraci. Pokud je použití nad kritickou prahovou hodnotou (90 %), pak je instance v **kritickém** stavu, pokud je menší než kritická prahová hodnota (90 %) ale větší než prahová hodnota upozornění (80 %), pak je instance ve stavu **Upozornění.** V opačném případě je ve stavu **V pořádku.** |ConsecutiveSamplesForStateTransition<br> FailIflessThanPercentage<br> Procento státní hranice<br> WarnifGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Využití procesoru kontejneru |Monitor jednotky |Toto monitorování hlásí kombinovaný stav využití procesoru instancí kontejneru.<br> Provádí jednoduché porovnání, které porovnává každý vzorek na jednu prahovou hodnotu a zadaný konfiguračníparametr **ConsecutiveSamplesForStateTransition**.<br> Jeho stav se vypočítá jako nejhorší stav 90 % instancí kontejneru (StateThresholdPercentage), seřazený v sestupném pořadí podle závažnosti stavu kontejneru (tj. kritický, upozornění, v pořádku).<br> Pokud není přijat žádný záznam z instance kontejneru, je stav instance kontejneru hlášen jako **Neznámý**a má vyšší prioritu v pořadí řazení nad **kritickým** stavem.<br> Stav každé instance kontejneru se vypočítá pomocí prahových hodnot určených v konfiguraci. Pokud je použití nad kritickou prahovou hodnotou (90 %), pak je instance v **kritickém** stavu, pokud je menší než kritická prahová hodnota (90 %) ale větší než prahová hodnota upozornění (80 %), pak je instance ve stavu **Upozornění.** V opačném případě je ve stavu **V pořádku.** |ConsecutiveSamplesForStateTransition<br> FailIflessThanPercentage<br> Procento státní hranice<br> WarnifGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Systémové pracovní pody připraveny |Monitor jednotky |Toto monitorování hlásí stav na základě procenta podů ve stavu připraven v daném pracovním vytížení. Jeho stav je nastaven na **kritický,** pokud méně než 100% lusků je ve **stavu Zdraví** |ConsecutiveSamplesForStateTransition<br> FailIflessThanPercentage |2<br> 100 ||
|Stav Kube API |Monitor jednotky |Tento monitor hlásí stav služby Kube API. Monitor je v kritickém stavu v případě, že koncový bod Kube API není k dispozici. Pro tento konkrétní monitor je stav určen vytvořením dotazu na koncový bod uzlů pro server kube-api. Cokoli jiného než kód odezvy OK změní monitor na **kritický** stav. | Žádné vlastnosti konfigurace |||

### <a name="aggregate-monitors"></a>Agregované monitory

|**Název monitoru** | **Popis** | **Algoritmus** |
|-----------------|-----------------|---------------|
|Node |Tento monitor je agregací všech monitorů uzlů. Odpovídá stavu dětského monitoru s nejhorším zdravotním stavem:<br> Využití procesoru uzlu<br> Využití paměti uzlu<br> Stav uzlu | Nejhorší|
|Fond uzlů |Toto monitorování hlásí kombinovaný stav všech uzlů ve *fondu agentů*fondu uzlů . Toto je monitorování tří stavů, jehož stav je založen na nejhorším stavu 80 % uzlů ve fondu uzlů seřazených v sestupném pořadí podle závažnosti stavů uzlů (tj. kritický, upozornění, v pořádku).|Procento |
|Uzly (nadřazený fond uzlů) |Toto je souhrnné monitorování všech fondů uzlů. Jeho stav je založen na nejhorším stavu jeho podřízených monitorů (to znamená, že fondy uzlů v clusteru). |Nejhorší |
|Cluster (nadřazená uzly/<br> Kubernetesova infrastruktura) |Toto je nadřazený monitor, který odpovídá stavu podřízeného monitoru s nejhorším stavem, což je infrastruktura kubernetes a uzly. |Nejhorší |
|Kubernetes infrastruktura |Toto monitorování hlásí kombinovaný stav součástí spravované infrastruktury clusteru. jeho stav se počítá jako "nejhorší z" stavů podřízeného monitoru, tj. |Nejhorší|
|Zatížení systému |Tento monitor hlásí stav zatížení kube systému. Tento monitor odpovídá stavu podřízeného monitoru s nejhorším stavem, tedy **pody v pohotovostním stavu** (monitorování a kontejnery v pracovní zátěži). |Nejhorší |
|Kontejner |Toto monitorování hlásí celkový stav kontejneru v daném pracovním vytížení. Tento monitor odpovídá stavu podřízeného monitoru s nejhorším stavem, tedy **monitorováním využití procesoru** a **využití paměti.** |Nejhorší |

## <a name="next-steps"></a>Další kroky

Zobrazit [stav clusteru monitoru](container-insights-health.md) se dozvíte o zobrazení stavu clusteru Kubernetes.
