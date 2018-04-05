---
title: Automatické škálování výpočetních uzlů ve fondu Azure Batch | Microsoft Docs
description: Povolte automatické škálování ve fondu cloudu dynamicky upravit počet výpočetních uzlů ve fondu.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 06/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1114ea90ae6976a3bc3580ebae5fd853de0274a1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Vytvořit automatické škálování vzorec škálování výpočetních uzlů ve fondu služby Batch

Azure Batch může automaticky škálovat fondů na základě parametrů, které definujete. Automatické škálování, Batch uzlů dynamicky přidá k fondu jako zvýšení požadavky úloh a odebere výpočetní uzly, jak se snížit. Čas a peníze můžete uložit tak, že automaticky upraví počet výpočetních uzlů, které používá vaše aplikace Batch. 

Povolit automatické škálování ve fondu výpočetních uzlů tím, že přidružíte ho *vzorec škálování* , které definujete. Služba Batch používá vzorec škálování můžete určit počet výpočetních uzlů, které jsou potřebné k provedení úlohy. Výpočetní uzly může být vyhrazený uzly nebo [nízkou prioritu uzly](batch-low-pri-vms.md). Batch odpoví na data metriky služby, která se shromažďují pravidelně. Na základě těchto dat metriky Batch upraví počet výpočetních uzlů ve fondu v závislosti na vaší vzorec a v konfigurovatelných intervalech.

Můžete povolit automatické škálování buď při vytváření fondu, nebo na existující fond. Můžete také změnit existující vzorec ve fondu, který je nakonfigurován pro automatické škálování. Dávky můžete vyhodnotit vzorcích před jejich přiřazení do fondů a monitorovat stav spuštění automatické škálování.

Tento článek popisuje různé entitami, které tvoří vaše vzorce automatického škálování, včetně proměnné, operátory, operace a funkce. Probereme, jak získat různé výpočetní prostředek a úloha metriky v rámci dávky. Tyto metriky můžete upravit počet uzlů váš fond na základě využití a úlohy stavu prostředků. Potom jsme popisují, jak vytvořit vzorec a povolte automatické škálování ve fondu pomocí Batch REST a rozhraní API technologie .NET. Dokončíme nakonec, se několik vzorci příklad.

> [!IMPORTANT]
> Při vytváření účtu Batch, můžete zadat [konfigurace účtu](batch-api-basics.md#account), která určuje, zda jsou fondy přidělené v předplatném služby Batch (výchozí) nebo ve vašem předplatném uživatele. Pokud jste vytvořili vašeho účtu Batch s výchozí konfiguraci služby Batch, váš účet je omezený na maximální počet jader, které lze použít ke zpracování. Služba Batch škáluje výpočetní uzly pouze do tohoto limitu jádra. Z tohoto důvodu nemusí kontaktovat službu Batch cílovým počtem výpočetních uzlů určeného vzorec škálování. V tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md) informace o prohlížení a zvýšení kvóty vašeho účtu.
>
>Pokud jste vytvořili účet s konfigurace uživatele předplatného, váš účet sdílí v základní kvóta pro předplatné. Další informace najdete v tématu [Omezení virtuálních počítačů](../azure-subscription-service-limits.md#virtual-machines-limits) v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Automatické škálování vzorce
Vzorec automatického škálování je řetězcová hodnota, kterou definujete, který obsahuje jeden nebo více příkazů. Vzorec automatického škálování je přiřazen fondu [autoScaleFormula] [ rest_autoscaleformula] – element (Batch REST) nebo [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] vlastnost (Batch .NET). Služba Batch používá vzorec k určení cílovým počtem výpočetních uzlů ve fondu pro další interval zpracování. Vzorce řetězec nesmí být delší než 8 KB, může obsahovat až 100 příkazy, které jsou odděleny středníky a může obsahovat konce řádků a komentáře.

Automatické škálování vzorců si můžete představit jako Batch škálování "jazyk." Vzorce příkazy jsou volné formátovaných výrazů, které můžou zahrnovat proměnných definovaných servisu (proměnné definované ve službě Batch) a uživatelem definované proměnné (proměnné, které definujete). Pomocí předdefinovaných typů, operátory a funkce můžou dělat různé operace na tyto hodnoty. Příkaz například může mít tento tvar:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Vzorce obvykle obsahují více příkazů, které provádějí operace na hodnotách, které byly získány v předchozích příkazech. Například nejprve jsme získat hodnotu `variable1`, předejte ji do funkce k naplnění `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Zahrňte tyto příkazy vzorec škálování přijaty ve cílovým počtem výpočetních uzlů. Vyhrazené uzlů a uzly nízkou prioritu mít vlastní nastavení cílové tak, aby můžete definovat cíl pro každý typ uzlu. Vzorec škálování může zahrnovat cílovou hodnotu pro vyhrazené uzly a cílovou hodnotu pro uzly nízkou prioritu.

Cílový počet uzlů může být vyšší, nižší nebo stejná jako aktuální počet uzlů typu ve fondu. Batch vyhodnocuje vzorec škálování fondu v určitých intervalech (viz [automatického škálování intervaly](#automatic-scaling-interval)). Batch upraví cílový počet každý typ uzlu ve fondu na číslo, které určuje vzorec škálování v době vyhodnocení.

### <a name="sample-autoscale-formula"></a>Vzorec škálování ukázka

Tady je příklad vzorec škálování, který lze upravit fungovat pro většinu scénářů. Proměnné `startingNumberOfVMs` a `maxNumberofVMs` v příkladu lze upravit vzorec vašim potřebám. Tento vzorec škáluje vyhrazené uzly, ale můžete upravit tak, aby platí pro škálování nízkou prioritu také uzly. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Pomocí tohoto vzorce automatického škálování fondu původně vytvořena s jeden virtuální počítač. `$PendingTasks` Metrika definuje počet úloh, které jsou spuštěné nebo zařazené ve frontě. Vzorec najde průměrný počet úkolů čekajících na zpracování v posledních 180 sekund a nastaví `$TargetDedicatedNodes` proměnná odpovídajícím způsobem. Vzorec zajistí, že cílový počet vyhrazených uzlů nikdy nepřesáhne 25 virtuálních počítačů. Nové úkoly jsou předloženy, fondu automaticky rozšiřovat. Jako dokončení úkolů virtuální počítače se stanou volné jeden po druhém a vzorec automatického škálování zmenšuje fondu.

## <a name="variables"></a>Proměnné
Můžete je používat **služby definované** a **uživatelem definované** proměnné ve vzorcích škálování. Proměnné definované služby jsou součástí služby Batch. Některé proměnné definované služby jsou pro čtení a zápis a některé jsou jen pro čtení. Uživatelem definované proměnné jsou proměnné, které definujete. Ve vzorci příklad uvedené v předchozí části `$TargetDedicatedNodes` a `$PendingTasks` jsou proměnných definovaných servisu. Proměnné `startingNumberOfVMs` a `maxNumberofVMs` jsou uživatelem definované proměnné.

> [!NOTE]
> Proměnné definované služby jsou vždy uvedeny znak dolaru ($). Uživatelem definované proměnné znak dolaru je volitelné.
>
>

Proměnné pro čtení i zápis i jen pro čtení, které jsou definovány pomocí služby Batch naleznete v následujících tabulkách.

Můžete získat a nastavit hodnoty těchto proměnných definovaných služby ke správě počet výpočetních uzlů ve fondu:

| Proměnné definované služby pro čtení a zápis | Popis |
| --- | --- |
| $TargetDedicatedNodes |Cílový počet vyhrazených výpočetních uzlech fondu. Počet vyhrazených uzlů je zadán jako cíl, protože fond nemusí vždy dosáhnout požadovaného počtu uzlů. Například pokud cílový počet vyhrazených uzlů je upraveném škálování zkušební verzi, než fondu dosáhl počáteční cíl, pak fondu nemusí mít přístup cíl. <br /><br /> Fond na účtu vytvoří s konfigurací služby Batch nemusí dosáhnout cíli, pokud cílový překročí kvótu uzlu nebo core účtu Batch. Fond na účtu vytvořené pomocí konfigurace předplatného uživatele nemusí dosáhnout cíli, pokud cílový překročí kvótu sdílené jader pro předplatné.|
| $TargetLowPriorityNodes |Cílový počet nízkou prioritu výpočetních uzlech fondu. Počet uzlů nízkou prioritu je zadán jako cíl, protože fond nemusí vždy dosáhnout požadovaného počtu uzlů. Například pokud cílový počet uzlů nízkou prioritu je upraveném škálování zkušební verzi, než fondu dosáhl počáteční cíl, pak fondu nemusí mít přístup cíl. Fond nemusí také dosáhnout cíli, pokud cílový překročí kvótu uzlu nebo core účtu Batch. <br /><br /> Další informace na nízkou prioritu výpočetních uzlů najdete v tématu [použít nízkou prioritu virtuálních počítačů pomocí služby Batch (Preview)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akce, která nastane, když se odebere z fondu, výpočetních uzlů. Možné hodnoty:<ul><li>**requeue**– okamžitě ukončí úlohy a uloží je zpět na fronty úloh tak, aby se přeplánovat.<li>**Ukončit**– okamžitě ukončí úlohy a odebere je ze fronty úloh.<li>**taskcompletion**– čeká aktuálně spuštěných úloh na dokončení a poté odstraní uzel z fondu.<li>**retaineddata**– čeká na všechny místní data zachována úkolů na uzlu, který má být vyčištěna, před odebráním uzel z fondu.</ul> |

Můžete získat hodnotu tyto proměnné definované služby provádět úpravy, které jsou založeny na metriky ze služby Batch:

| Jen pro čtení služby definované proměnné | Popis |
| --- | --- |
| $CPUPercent |Průměrná procento využití procesoru. |
| $WallClockSeconds |Počet sekund využívat. |
| $MemoryBytes |Průměrný počet MB použít. |
| $DiskBytes |Průměrný počet gigabajtů použít na místních discích. |
| $DiskReadBytes |Počet přečtených bajtů. |
| $DiskWriteBytes |Počet zapsaných bajtů. |
| $DiskReadOps |Počet operací čtení disku provést. |
| $DiskWriteOps |Počet operací zápisu disku provést. |
| $NetworkInBytes |Počet přijatých bajtů. |
| $NetworkOutBytes |Počet bajtů. |
| $SampleNodeCount |Počet výpočetních uzlů. |
| $ActiveTasks |Počet úloh, které jsou připravené k provedení, ale ještě nejsou provádění. Počet $ActiveTasks zahrnuje všechny úlohy, které jsou v aktivním stavu a jehož závislosti byly splněny. Všechny úlohy, které jsou v aktivním stavu, ale nebyly splněny jehož závislosti jsou vyloučeny z $ActiveTasks počet.|
| $RunningTasks |Počet úloh ve spuštěném stavu. |
| $PendingTasks |Součet $ActiveTasks a $RunningTasks. |
| $SucceededTasks |Počet úloh, které bylo úspěšně dokončeno. |
| $FailedTasks |Počet úloh, které se nezdařilo. |
| $CurrentDedicatedNodes |Aktuální počet vyhrazených výpočetních uzlů. |
| $CurrentLowPriorityNodes |Aktuální počet nízkou prioritu výpočetní uzly, včetně všech uzlech, které byly zrušeny. |
| $PreemptedNodeCount | Počet uzlů ve fondu, které jsou ve stavu, zrušeny. |

> [!TIP]
> Jen pro čtení, služby definované proměnné, které jsou uvedeny v předchozí tabulce jsou *objekty* které poskytují různé metody pro přístup k datům spojené s každou. Další informace najdete v tématu [získat ukázková data](#getsampledata) dále v tomto článku.
>
>

## <a name="types"></a>Typy
Tyto typy jsou podporovány v vzorec:

* Double
* doubleVec
* doubleVecList
* řetězec
* časové razítko – časové razítko je složené struktura, která obsahuje následující členy:

  * rok
  * měsíc (1-12)
  * den (1-31)
  * den v týdnu (ve formátu číslo; příklad: 1 pro pondělí)
  * hodinu (ve 24hodinovém formátu číslo; například 13 znamená 13: 00)
  * minut (00-59)
  * druhý (00-59)
* TimeInterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Operace
Tyto operace jsou povoleny na typy, které jsou uvedeny v předchozí části.

| Operace | Podporované operátory | Typ výsledku |
| --- | --- | --- |
| dvojité *operátor* double |+, -, *, / |Double |
| dvojité *operátor* timeinterval |* |TimeInterval |
| doubleVec *operátor* double |+, -, *, / |doubleVec |
| doubleVec *operátor* doubleVec |+, -, *, / |doubleVec |
| TimeInterval *operátor* double |*, / |TimeInterval |
| TimeInterval *operátor* timeinterval |+, - |TimeInterval |
| TimeInterval *operátor* časové razítko |+ |časové razítko |
| časové razítko *operátor* timeinterval |+ |časové razítko |
| časové razítko *operátor* časové razítko |- |TimeInterval |
| *operátor*double |-, ! |Double |
| *operator*timeinterval |- |TimeInterval |
| dvojité *operátor* double |<, <=, ==, >=, >, != |Double |
| řetězec *operátor* řetězec |<, <=, ==, >=, >, != |Double |
| časové razítko *operátor* časové razítko |<, <=, ==, >=, >, != |Double |
| TimeInterval *operátor* timeinterval |<, <=, ==, >=, >, != |Double |
| dvojité *operátor* double |&&, &#124;&#124; |Double |

Při testování dvojitou s Ternární operátor (`double ? statement1 : statement2`), nenulové hodnoty je **true**, a je nula **false**.

## <a name="functions"></a>Funkce
Tyto předdefinované **funkce** jsou k dispozici pro použití k definování vzorec automatické škálování.

| Funkce | Návratový typ | Popis |
| --- | --- | --- |
| AVG(doubleVecList) |Double |Vrátí průměrnou hodnotu pro všechny hodnoty v doubleVecList. |
| Len(doubleVecList) |Double |Vrátí délku vektor, který je vytvořený z doubleVecList. |
| LG(Double) |Double |Vrátí protokol základní 2 dvojitou hodnotu. |
| LG(doubleVecList) |doubleVec |Vrátí základní 2 doubleVecList component-wise protokolu. Pro parametr musí být explicitně předán vec(double). Jinak se předpokládá, double lg(double) verze. |
| ln(Double) |Double |Vrátí přirozené protokol dvojitou hodnotu. |
| ln(doubleVecList) |doubleVec |Vrátí základní 2 doubleVecList component-wise protokolu. Pro parametr musí být explicitně předán vec(double). Jinak se předpokládá, double lg(double) verze. |
| log(Double) |Double |Vrátí protokol základní 10 dvojitou hodnotu. |
| log(doubleVecList) |doubleVec |Vrátí základní 10 doubleVecList component-wise protokolu. Pro parametr jeden double musí být explicitně předán vec(double). Jinak se předpokládá, double log(double) verze. |
| Max(doubleVecList) |Double |Vrátí maximální hodnotu ve doubleVecList. |
| min(doubleVecList) |Double |Vrátí minimální hodnotu ve doubleVecList. |
| Norm(doubleVecList) |Double |Vrátí dva norm vektoru, který je vytvořený z doubleVecList. |
| percentil (doubleVec v dvojité p) |Double |Vrátí element percentilu vektoru v. |
| rand() |Double |Vrátí náhodná hodnota mezi 0,0 a 1,0. |
| Range(doubleVecList) |Double |Vrátí rozdíl mezi minimální a maximální hodnoty doubleVecList. |
| STD(doubleVecList) |Double |Vrátí směrodatnou odchylku ukázkové hodnoty v doubleVecList. |
| stop() | |Zastaví vyhodnocování výrazu automatické škálování. |
| sum(doubleVecList) |Double |Vrátí součet všech součástí doubleVecList. |
| čas (řetězce data a času = "") |časové razítko |Vrátí časového razítka aktuální čas, pokud jsou předány bez parametrů nebo časového razítka řetězce data a času, je-li předán. Data a času podporované formáty jsou W3C DTF a RFC 1123. |
| Val (doubleVec v dvojité i) |Double |Vrátí hodnotu elementu, který je v umístění i u funkce vector v, počáteční index nula. |

Některé funkce, které jsou popsané v předchozí tabulce může přijmout seznam jako argument. Čárkami oddělený seznam je libovolnou kombinaci *dvojité* a *doubleVec*. Příklad:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*DoubleVecList* hodnota je převedena na jednu *doubleVec* před vyhodnocení. Například pokud `v = [1,2,3]`, pak volání `avg(v)` je ekvivalentní volání `avg(1,2,3)`. Volání metody `avg(v, 7)` je ekvivalentní volání `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Získat ukázková data
Vzorce automatického škálování fungují na metriky dat (Ukázky), které poskytuje služba Batch. Vzorec zvětšováním nebo zmenšováním velikost fondu na základě hodnot, které se získá od služby. Proměnné definované služby, které bylo popsáno dříve jsou objekty, které poskytují různé metody pro přístup k datům, která souvisí s Tento objekt. Například následující výraz zobrazuje požadavek na načtení za posledních pět minut využití procesoru:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metoda | Popis |
| --- | --- |
| GetSample() |`GetSample()` Metoda vrátí Vektor vzorků dat.<br/><br/>Ukázka je 30 sekund vhodné dat metriky. Jinými slovy ukázky jsou získávány každých 30 sekund. Ale dole uvedených položek, dochází ke zpoždění mezi při shromažďování ukázku a kdy je k dispozici pro vzorec. Ne všechny ukázky pro dané časové období jako takový, může být dostupné pro vyhodnocení podle vzorce.<ul><li>`doubleVec GetSample(double count)`<br/>Určuje počet vzorků získat z nejnovější vzorků, které byly shromážděny.<br/><br/>`GetSample(1)` vrací posledního vzorku k dispozici. Pro metriky `$CPUPercent`, ale to nesmí použít, protože není možné vědět *při* vzorku nebyla shromážděna. Může to být poslední, nebo kvůli problémům s systému, může být mnohem starší. Je lepší v takových případech používat časový interval, jak je uvedeno níže.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Určuje časový rámec pro shromažďování ukázková data. Volitelně můžete také určuje procento vzorků, které musí být k dispozici v požadovaný časový rámec.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` Vrátí 20 vzorků v případě se nacházejí v historii CPUPercent všechny ukázky pro posledních 10 minut. Pokud poslední minutu historie nebyl k dispozici, ale pouze 18 ukázky by byla vrácena. V takovém případě:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` selže, protože nejsou k dispozici pouze 90 procent ukázky.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` mohla být úspěšná.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Určuje časový rámec pro shromažďování dat, s počáteční čas a koncový čas.<br/><br/>Jak je uvedeno nahoře, dochází ke zpoždění mezi při shromažďování ukázku a kdy je k dispozici pro vzorec. Vezměte v úvahu tato prodleva při použití `GetSample` metoda. V tématu `GetSamplePercent` níže. |
| GetSamplePeriod() |Vrátí dobu vzorků, které byly provedeny v datové sadě historických ukázka. |
| Count() |Vrátí celkový počet vzorků, které se v historii metriky. |
| HistoryBeginTime() |Vrátí časového razítka nejstarší ukázková data k dispozici pro metriku. |
| GetSamplePercent() |Vrátí podíl vzorků, které jsou k dispozici v daném časovém intervalu. Příklad:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Protože `GetSample` metoda selže, pokud procento vzorků, které se vrátí, je menší než `samplePercent` zadána, můžete použít `GetSamplePercent` metoda nejprve ke kontrole. Poté můžete provádět alternativní akce když dostatek ukázky jsou přítomny, bez zastavení automatické škálování vyhodnocení. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Ukázky, ukázka procento a *GetSample()* – metoda
Základní provoz od vzorce automatického škálování je získat data úlohy a prostředku metriky a upravte velikost fondu podle data. Jako takový je důležité mít vědět, jak vzorce automatického škálování pracovat s daty metrik (Ukázky).

**Ukázky**

Služba Batch pravidelně trvá ukázky úlohy a prostředku metriky a zpřístupňuje je vaše vzorce automatického škálování. Tyto ukázky se službou Batch zaznamenávají každých 30 sekund. Je však obvykle zpoždění mezi při nebyly zaznamenány tyto ukázky a když jsou zpřístupněné (a mohou být přečteny) vzorcích škálování. Kromě toho z důvodu různých faktorech, například sítě nebo jiných problémů s infrastrukturou, nemusí být ukázky zaznamenány pro konkrétním intervalu.

**Ukázka procento**

Při `samplePercent` je předán `GetSample()` metoda nebo `GetSamplePercent()` metoda je volána, _procent_ odkazuje na porovnání mezi celkový možný počet vzorků, které se zaznamenávají službou Batch a počet ukázky, které jsou k dispozici pro vzorec škálování.

Podívejme se na časový interval 10 minut jako příklad. Protože vzorky se zaznamenávají každých 30 sekund v rámci časový interval 10 minut, bude maximální celkový počet vzorků, které se zaznamenávají dávkou 20 vzorků (2 za minutu). Však z důvodu čekací doby vyplývajících mechanismus vytváření sestav a další otázky v rámci Azure, může existovat pouze 15 vzorků, které jsou k dispozici pro vzorec škálování pro čtení. Ano například tuto dobu 10 minut 75 % celkového počtu vzorků, které zaznamenávají může být dostupné pro vzorce.

**GetSample() a ukázkové rozsahů**

Vaše vzorce automatického škálování se chystáte se zvětšování a zmenšování fondech &mdash; uzly pro přidání nebo odebrání uzlů. Vzhledem k tomu, že uzly náklady peníze, chcete zajistit, že vaše vzorce použijte metodu inteligentního analýzy, která je založená na dostatečného množství dat. Proto doporučujeme použít k analýze trendů typu ve vzorcích. Tento typ zvětšování a zmenší fondech na základě rozsahu shromažďovaných vzorků.

Chcete-li to provést, použijte `GetSample(interval look-back start, interval look-back end)` vrátit vektoru vzorků:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Při výše řádku je vyhodnocován Batch, vrátí oblast ukázky vektor hodnot. Příklad:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Jakmile jste získány vektoru vzorků, pak můžete použít funkce jako `min()`, `max()`, a `avg()` odvodit smysluplné hodnoty od shromážděných rozsahu.

Pro dodatečné zabezpečení můžete vynutit vzorců nezdaří, pokud je nižší než určité procento ukázka je dostupná pro konkrétní časové období. Pokud vynutíte vzorců selhání, dáte pokyn dávky na vyhodnocení vzorec ustanou další, pokud zadané procento vzorků, které není k dispozici. V takovém případě je provedena žádná změna velikosti fondu. Pokud chcete zadat požadované procento ukázky pro vyhodnocení proběhla úspěšně, je zadat jako třetí parametr `GetSample()`. Zde je zadán požadavek 75 procent ukázky:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Protože ukázka dostupnosti může dojít ke zpoždění, je důležité vždy zadejte časový interval s časem zahájení vzhled zpět, která jsou starší než jedna minuta. Ho trvá přibližně jednu minutu ukázek rozšíří v rámci systému, takže ukázky v rozsahu `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` nemusí být k dispozici. Znovu, můžete použít parametr procento `GetSample()` vynutit požadavek procento konkrétní vzorek.

> [!IMPORTANT]
> Jsme **důrazně doporučujeme** , které **se spoléhat *pouze* na `GetSample(1)` ve vzorcích škálování**. Důvodem je, že `GetSample(1)` v podstatě je uveden ve službě Batch, "Udělení mi poslední ukázkové, budete mít, bez ohledu na to, jak dávno načíst." Vzhledem k tomu, že je pouze jeden vzorku a může být starší ukázkové, nemusí být zástupce větší přehled o poslední úlohu nebo stav prostředku. Pokud používáte `GetSample(1)`, ujistěte se, že je součástí větší prohlášení a není pouze datový bod, který vzorec spoléhá na.
>
>

## <a name="metrics"></a>Metriky
Prostředek a úloha metriky můžete použít, když definujete vzorec. Můžete upravit cílový počet vyhrazených uzlů ve fondu na základě metriky dat, který můžete získat a vyhodnocení. Najdete v článku [proměnné](#variables) části výše pro další informace o jednotlivé metriky.

<table>
  <tr>
    <th>Metrika</th>
    <th>Popis</th>
  </tr>
  <tr>
    <td><b>Prostředek</b></td>
    <td><p>Metrika prostředků jsou založené na procesoru, šířky pásma, využití paměti výpočetních uzlů a počet uzlů.</p>
        <p> Tyto proměnné definované služby jsou užitečné pro provádění úprav na základě počtu uzlu:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Tyto proměnné definované služby jsou užitečné pro provádění úprav na základě využití prostředků uzlu:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Úkol</b></td>
    <td><p>Metriky úloh jsou na základě stavu úkolů, například aktivní, čeká na vyřízení a byla dokončena. Následující proměnné definované služby jsou užitečné pro provádění úprav velikost fondu na základě metriky úloh:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Zápis vzorec škálování
Sestavení se vzorec škálování vytvořením příkazy, které používají komponenty výše a potom kombinace těchto příkazů do dokončení vzorce. V této části vytvoříme vzorec škálování příklad, který může provádět některé reálného škálování rozhodnutí.

Nejdříve definujme požadavky pro naše nové vzorec škálování. Vzorec proveďte následující kroky:

1. Zvýšit číslo cílového vyhrazené výpočetních uzlů ve fondu, pokud využití CPU je vysoké.
2. Při nízkém využití procesoru, snížit cílový počet vyhrazených výpočetních uzlů ve fondu.
3. Maximální počet vyhrazených uzlů vždy omezte na 400.

Pokud chcete zvýšit počet uzlů během vysoké využití procesoru, zadejte příkaz, který naplní uživatelem definované proměnné (`$totalDedicatedNodes`) s hodnotou, která je 110 procent aktuální cílový počet vyhrazených uzlů, ale jenom v případě minimální průměrné využití procesoru během posledních 10 minut byla vyšší než 70 procent. Jinak použijte hodnotu pro aktuální počet vyhrazených uzlů.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

K *snížit* počet vyhrazených uzlů při nízkém zatížení procesoru, další příkaz v našem vzorec nastaví stejné `$totalDedicatedNodes` proměnné až 90 procent aktuální cílový počet vyhrazených uzlů, pokud průměrné využití procesoru za posledních 60 minut se v části 20 procent. Jinak použijte aktuální hodnota `$totalDedicatedNodes` , jsme naplněno v příkazu výše.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nyní omezte cílový počet vyhrazených výpočetní uzly maximálně 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Tady je kompletní vzorec:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Vytvoření fondu povoleno škálování s rozhraním .NET

K vytvoření fondu pomocí automatické škálování povolené v rozhraní .NET, postupujte takto:

1. Vytvoření fondu s [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Nastavte [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) vlastnost `true`.
3. Nastavte [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) vlastnost s vzorec škálování.
4. (Volitelné) Nastavte [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) vlastnosti (výchozí hodnota je 15 minut).
5. Potvrdit fondu s [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) nebo [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Následující fragment kódu vytvoří fond škálování povolené v rozhraní .NET. Vzorec škálování fondu nastaví cílový počet vyhrazených uzlů až 5 pro pondělí a 1 na každý den v týdnu. [Interval automatického škálování](#automatic-scaling-interval) nastavena na 30 minut. V tomto a dalších C# fragmenty kódu v tomto článku `myBatchClient` je správně inicializována instanci [BatchClient] [ net_batchclient] třídy.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "small", // single-core, 1.75 GB memory, 225 GB disk
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Když vytvoříte fond povoleno automatické škálování, nezadávejte _targetDedicatedComputeNodes_ parametr nebo _targetLowPriorityComputeNodes_ parametr při volání **CreatePool** . Místo toho zadat **AutoScaleEnabled** a **AutoScaleFormula** vlastnosti ve fondu. Hodnoty pro tyto vlastnosti určují cílový počet každý typ uzlu. Také do ručně škálování povoleným změny velikosti fondu (například s [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), první **zakázat** automatické škálování na fond a jeho velikost.
>
>

Kromě Batch .NET, můžete použít některý z dalších [SDK služby Batch](batch-apis-tools.md#azure-accounts-for-batch-development), [Batch REST](https://docs.microsoft.com/rest/api/batchservice/), [rutiny prostředí PowerShell Batch](batch-powershell-cmdlets-get-started.md)a [Batch CLI](batch-cli-get-started.md) na Konfigurace automatického škálování.


### <a name="automatic-scaling-interval"></a>Interval automatického škálování
Ve výchozím nastavení služba Batch upraví velikost fondu podle jeho vzorec škálování každých 15 minut. Tento interval je možné konfigurovat pomocí následujících vlastností fondu:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

Minimální interval je pět minut a maximální hodnota je 168 hodin. Pokud je zadán interval mimo tento rozsah, služba Batch vrátí chybu chybný požadavek (400).

> [!NOTE]
> Automatické škálování není určen aktuálně reagovat na změny v méně než minutu, ale je určený upravit velikost fondu postupně jako při spuštění úloh.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Povolit automatické škálování na existující fond

Každý Batch SDK poskytuje způsob, jak povolit automatické škálování. Příklad:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [Povolit automatické škálování ve fondu][rest_enableautoscale] (REST API)

Když povolíte automatické škálování na existující fond, mějte na paměti následující body:

* Když automatické škálování je aktuálně zakázaná ve fondu Pokud vydáte požadavek na povolení automatického škálování, je nutné zadat platný škálování vzorec Pokud vydáte požadavek. Volitelně můžete zadat interval testování škálování. Pokud nezadáte intervalu, je použita výchozí hodnota 15 minut.
* Pokud automatického škálování je aktuálně povoleno ve fondu, můžete vzorec škálování, intervalu vyhodnocení nebo obojí. Musíte zadat alespoň jednu z těchto vlastností.

  * Pokud zadáte nový vyhodnocení interval škálování, je zastavena existující plán vyhodnocení a spuštění nový plán. Nový plán spuštění je čas, kdy byl vydán požadavek na povolení automatické škálování.
  * Pokud vynecháte vzorec škálování nebo vyhodnocení interval, služba Batch budou nadále používat aktuální hodnota tohoto nastavení.

> [!NOTE]
> Pokud jste zadali hodnoty *targetDedicatedComputeNodes* nebo *targetLowPriorityComputeNodes* parametry **CreatePool** metoda při vytváření fondu v Rozhraní .NET, nebo pro porovnatelný z hlediska parametrů v jiném jazyce, pak tyto hodnoty jsou ignorovány, když vzorec automatického škálování je vyhodnocena.
>
>

Používá tento fragment kódu jazyka C# [Batch .NET] [ net_api] knihovnu, která má-li povolit automatické škálování na existující fond:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aktualizace se vzorec škálování

Aktualizovat vzorec na existující fond povoleno automatické škálování, voláním operace povolili automatické škálování znovu s novou vzorec. Například, pokud je již zapnuta automatické škálování `myexistingpool` při spuštění následující kódu .NET, jeho vzorec škálování se nahradí obsah `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aktualizovat interval škálování

Aktualizovat interval škálování vyhodnocení existujícího fondu povoleno automatické škálování, voláním operace povolili automatické škálování znovu s novou intervalu. Chcete-li například nastavit interval testování škálování na 60 minut pro fond, který je již povolen škálování v rozhraní .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Vyhodnocení se vzorec škálování

Vzorce můžete vyhodnotit před každým jejím použitím do fondu. Tímto způsobem můžete otestovat vzorec zobrazíte, jak vyhodnotit jeho příkazy před zahájením vzorec uvedení do produkčního prostředí.

Abyste mohli vyhodnotit vzorec škálování, je třeba nejprve povolit automatické škálování ve fondu s platný vzorec. K otestování vzorec na fond, který ještě nemá povoleno automatické škálování, použijte vzorec jednořádkové `$TargetDedicatedNodes = 0` když poprvé povolíte automatické škálování. Pak použijte jednu z následujících vyhodnotit vzorec, který chcete testovat:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) nebo [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Tyto metody Batch .NET, vyžadují ID existující fond a řetězec obsahující vzorec škálování k vyhodnocení.

* [Vyhodnocení vzorec automatické škálování](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    V této žádosti o volání rozhraní REST API, zadejte ID fondu v identifikátoru URI a vzorec škálování v *autoScaleFormula* element textu požadavku. Odpověď operace obsahuje veškeré informace o chybě, která se může vztahovat na vzorec.

V tomto [Batch .NET] [ net_api] fragment kódu, vyhodnocení se vzorec škálování. Pokud fond nemá povoleno automatické škálování, jsme ji povolit nejdřív.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = {pool.CurrentDedicatedNodes};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Úspěšné vyhodnocení vzorec tento fragment kódu ukazuje vytváří výsledky podobně jako:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Získat informace o spuštění automatického škálování

Aby se zajistilo, že vzorec funguje podle očekávání, doporučujeme, abyste pravidelně Kontrola výsledků spustí automatické škálování, které Batch provádí fondu. Ano, získat (nebo aktualizujte) odkaz do fondu a zkontrolujte vlastnosti jeho poslední škálování spustit.

V Batch .NET [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) vlastnost má několik vlastností, které obsahují informace o nejnovější automatické škálování spustit provádět ve fondu:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

V rozhraní API REST [získat informace o fondu](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) požadavek vrací informace o fondu, který obsahuje nejnovější automatické škálování spouštění informace [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun) vlastnost.

Následující fragment kódu jazyka C# pomocí knihovny Batch .NET tisknout informace o poslední automatické škálování, spusťte na fond _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Ukázkový výstup předchozí fragment kódu:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Příklad vzorce automatického škálování
Podívejme se na několik vzorce, které jsou ukázány různé způsoby, jak nastavit šířku výpočetní prostředky ve fondu.

### <a name="example-1-time-based-adjustment"></a>Příklad 1: Založené na čase úpravy
Předpokládejme, že chcete upravovat velikost fondu na základě den v týdnu a čas. Tento příklad ukazuje, jak zvýšení nebo snížení počtu uzlů ve fondu odpovídajícím způsobem.

Vzorec nejdřív získá aktuální čas. Pokud je jeden den v týdnu (1-5) a v rámci pracovní dobu (8: 00 do 18: 00), velikost cílového fondu nastavena na 20 uzlů. Jinak je nastavena na 10 uzly.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Příklad 2: Úprava založený na úlohách
V tomto příkladu se upraví velikost fondu na základě počtu úloh ve frontě. Komentáře a zalomení řádků jsou přípustné v řetězcích vzorce.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Příklad 3: Monitorování účtů pro paralelní úlohy
Tento příklad upraví velikost fondu na základě počtu úloh. Tento vzorec také bere v úvahu [MaxTasksPerComputeNode] [ net_maxtasks] hodnotu, která je nastavená pro fond. Tento přístup je užitečné v situacích, kde [paralelní provádění úkolů](batch-parallel-node-tasks.md) bylo povoleno na váš fond.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Příklad 4: Nastavení velikosti počáteční fondu
Tento příklad ukazuje fragmentu kódu C# pomocí vzorce automatického škálování, která nastaví velikost fondu na zadaný počet uzlů na počáteční čas období. Pak se upraví velikost fondu na základě počtu spuštěná a aktivní úlohy po uplynutí počáteční časové období.

Vzorec v následující fragment kódu:

* Nastaví velikost počáteční fondu čtyř uzlů.
* V rámci prvních 10 minut životního cyklu fondu nepřizpůsobí velikost fondu.
* Po 10 minutách, získá maximální hodnotu počtu spuštěných a aktivní úlohy v rámci za posledních 60 minut.
  * Pokud jsou obě hodnoty 0 (což znamená, že žádné úlohy byly spuštěné nebo aktivní za posledních 60 minut), velikost fondu je nastavena na hodnotu 0.
  * Pokud je buď hodnota větší než nula, nebudou provedeny žádné změny.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Další postup
* [Maximalizovat využití prostředků Azure Batch výpočetní uzel souběžných úloh](batch-parallel-node-tasks.md) obsahuje podrobnosti o tom, jak můžete spustit více úkolů současně na výpočetních uzlech ve fondu. Kromě automatického škálování tato funkce může pomoci snížit dobu trvání úlohy pro některé úlohy, ušetřit peníze.
* Pro jiné podpůrná efektivitu zajistěte, aby aplikace Batch dotazuje služba Batch optimální způsobem. V tématu [efektivní dotazování na službu Azure Batch](batch-efficient-list-queries.md) se dozvíte, jak omezit množství dat, která protne sítě při dotazu na stav potenciálně tisíce výpočetních uzlů nebo úlohy.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
