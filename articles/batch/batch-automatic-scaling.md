---
title: Automatické škálování výpočetních uzlů ve fondu služby Azure Batch | Dokumentace Microsoftu
description: Povolte automatické škálování v cloudu fondu dynamicky upravit počet výpočetních uzlů ve fondu.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 06/20/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa5588ae31e63ae54e654ef26563c7570fe4cd13
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459838"
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Vytvořit vzorec automatického škálování pro škálování výpočetních uzlů ve fondu služby Batch

Služba Azure Batch může automaticky škálovat fondů na základě parametrů, které definujete. Automatické škálování Batch dynamicky přidá uzly fondu jako úloha požadavky na zvýšení a odebere výpočetních uzlů podle jejich snížit. Ušetříte čas i peníze automaticky úpravou počet výpočetních uzlů, které používá vaše aplikace Batch. 

Povolit automatické škálování fondu výpočetních uzlů se přidruží *vzorec automatického škálování* , které definujete. K určení počtu výpočetních uzlů, které jsou potřebné ke spuštění vaší úlohy používá služba Batch vzorec automatického škálování. Výpočetní uzly mohou být vyhrazené uzly nebo [uzly s nízkou prioritou](batch-low-pri-vms.md). Batch jsou reaguje na pravidelné shromažďování dat metrik služby. Na základě těchto dat metrik služby Batch přizpůsobí počet výpočetních uzlů ve fondu na základě vzorce a s konfigurovatelným intervalem.

Můžete povolit automatické škálování, buď při vytváření fondu, nebo na existující fond. Můžete také změnit existující vzorec ve fondu, který je nakonfigurovaný pro automatické škálování. Batch vám umožní vyhodnotit vzorce před přiřazením do fondů a monitorovat stav spuštění automatického škálování.

Tento článek popisuje různé entity, které tvoří vzorce automatického škálování, včetně proměnných, operátory, operacemi a funkcemi. Pojednává o získání různých výpočetních prostředků a úloh metriky v rámci služby Batch. Tyto metriky můžete upravit počet uzlů váš fond na základě využití a úlohy stavu prostředku. Pak zjistíte, jak vytvořit vzorec a povolit automatické škálování fondu s použitím Batch REST a rozhraní API pro .NET. Nakonec jsme skončí s několika Příklad vzorce.

> [!IMPORTANT]
> Při vytváření účtu Batch můžete zadat [konfiguraci účtu](batch-api-basics.md#account), který určuje, jestli se fondy přidělují v předplatném služby Batch (výchozí) nebo v předplatném uživatele. Pokud jste vytvořili účet Batch ve výchozí konfiguraci služby Batch, váš účet je omezena na maximální počet jader, které lze použít ke zpracování. Služba Batch škálování výpočetních uzlů pouze do tohoto limitu core. Z tohoto důvodu služba Batch nemusí dosáhnout cílového počtu výpočetních uzlů, které jsou určené vzorec automatického škálování. Zobrazit [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md) informace o zobrazení a zvýšení kvóty vašeho účtu.
>
>Pokud jste účet vytvořili s konfigurací předplatné uživatele, váš účet sdílejí kvótu jader pro předplatné. Další informace najdete v tématu [Omezení virtuálních počítačů](../azure-subscription-service-limits.md#virtual-machines-limits) v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Automatické škálování vzorce
Automatické škálování vzorec je řetězec, který definujete, který obsahuje jeden nebo více příkazů. Vzorec automatického škálování je přiřazená fondu [autoScaleFormula] [ rest_autoscaleformula] – element (Batch REST) nebo [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] vlastnosti (Batch .NET). Služba Batch vzorec používá k určení cílového počtu výpočetních uzlů ve fondu pro další interval zpracování. Vzorec řetězec může mít maximálně 8 KB, může obsahovat až 100 příkazy, které jsou odděleny středníky a může obsahovat konce řádků a komentáře.

Automatické škálování vzorce si můžete představit jako Batch autoscale "jazyk." Vzorec příkazy jsou zdarma formátovaných výrazy, které mohou obsahovat služby definované proměnné (proměnné definovány službou Batch) a uživatelem definované proměnné (proměnné, které definujete). Pomocí předdefinované typy, operátory a funkce mohou provádět různé operace na tyto hodnoty. Příkaz může být třeba provést následující formulář:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Vzorce obvykle obsahují více příkazů, které provádějí operace na hodnotách, které jsou získány v předchozích příkazů. Například nejprve získáme hodnotu `variable1`, poté jej nepředávejte funkci k naplnění `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Zahrňte tyto příkazy vzorec automatického škálování můžete přejít na cílový počet výpočetních uzlů. Vyhrazené uzly a uzly s nízkou prioritou mají své vlastní cílové nastavení tak, že můžete definovat cíl pro každý typ uzlu. Jeho vzorec může obsahovat cílovou hodnotu pro vyhrazené uzly a cílovou hodnotu pro uzly s nízkou prioritou.

Cílový počet uzlů může být vyšší, nižší nebo stejná jako aktuální počet uzlů ve fondu tohoto typu. Batch vyhodnocuje vzorec automatického škálování fondu v určených intervalech (viz [automatického škálování intervalech](#automatic-scaling-interval)). Batch nastaví cílový počet každého typu uzlu ve fondu, aby číslo, které určuje vzorec automatického škálování v době vyhodnocení.

### <a name="sample-autoscale-formula"></a>Ukázka vzorec automatického škálování

Tady je příklad jeho vzorec, který je možné upravit fungovat pro většinu scénářů. Proměnné `startingNumberOfVMs` a `maxNumberofVMs` v příkladu vzorec je možné upravit podle vašich potřeb. Tento vzorec škálování vyhrazené uzly, ale můžete upravit tak, aby použít také škálování uzlů s nízkou prioritou. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Pomocí tohoto vzorce automatického škálování je zpočátku fond vytvořit s jeden virtuální počítač. `$PendingTasks` Metrika definuje počet úloh, které jsou spuštěné nebo zařazené ve frontě. Vzorec vyhledá průměrný počet čekající úlohy v posledních 180 sekund a nastaví `$TargetDedicatedNodes` proměnné odpovídajícím způsobem. Vzorec zajišťuje, že cílový počet vyhrazených uzlů nikdy nepřesáhne 25 virtuálních počítačů. Jako nové úkoly se odešlou, fond se automaticky rozšíří. Dokončení úkolů virtuální počítače budou zdarma jednu po druhé a zmenšuje vzorec automatického škálování fondu.

## <a name="variables"></a>Proměnné
Je možné použít jak **definovaných službou** a **uživatelem definované** proměnné ve vzorcích používat automatické škálování. Proměnné definované služba integrovaná do služby Batch. Některé služby definované proměnné jsou pro čtení i zápis a některé jsou jen pro čtení. Uživatelem definované proměnné jsou proměnné, které definujete. V příkladu vzorec je znázorněno v předchozí části `$TargetDedicatedNodes` a `$PendingTasks` jsou služby definované proměnné. Proměnné `startingNumberOfVMs` a `maxNumberofVMs` jsou uživatelem definované proměnné.

> [!NOTE]
> Proměnné definované službou vždy předchází znak dolaru ($). Pro uživatelem definované proměnné je volitelný znak dolaru.
>
>

Následující tabulky popisují čtení i zápis i čtení proměnné, které jsou definovány službou Batch.

Můžete získávat a nastavovat hodnoty těchto proměnných definovaných službou pro správu počet výpočetních uzlů ve fondu:

| Služba definované proměnné pro čtení i zápis | Popis |
| --- | --- |
| $TargetDedicatedNodes |Cílový počet vyhrazené výpočetní uzly fondu. Počet vyhrazených uzlů je zadat jako cíl, protože fond nemusí vždy dosáhnout požadovaného počtu uzlů. Například pokud cílový počet vyhrazených uzlů se změní podle hodnocení automatického škálování, před fond dosáhl počáteční cíle, pak fondu nemusí mít přístup cíl. <br /><br /> Fond v rámci účtu vytvořeného s konfigurací služba Batch nemusí dosáhnout svůj cíl, pokud cíl překračuje kvótu uzlu nebo core účtu Batch. Fond v rámci účtu vytvořeného s konfigurací předplatné uživatele nemusí dosáhnout svůj cíl, pokud cíl překračuje kvótu sdílené jader pro předplatné.|
| $TargetLowPriorityNodes |Cílový počet s nízkou prioritou výpočetní uzly fondu. Počet uzlů s nízkou prioritou je zadat jako cíl, protože fond nemusí vždy dosáhnout požadovaného počtu uzlů. Například pokud cílový počet uzlů s nízkou prioritou se změní podle hodnocení automatického škálování, před fond dosáhl počáteční cíle, pak fondu nemusí mít přístup cíl. Fond nemusí také dosáhnout svůj cíl, pokud cíl překračuje kvótu uzlu nebo core účtu Batch. <br /><br /> Další informace o s nízkou prioritou výpočetních uzlů, naleznete v tématu [používejte virtuální počítače s nízkou prioritou službou Batch (Preview)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akce, která nastane, pokud výpočetní uzly se odebere z fondu. Možné hodnoty:<ul><li>**znovuzařazení do fronty**– okamžitě ukončí úkoly a umístí je zpět ve frontě úloh tak, aby se přeplánovat.<li>**Ukončit**– okamžitě ukončí úkoly a odebere z fronty úloh.<li>**taskcompletion**– čeká aktuálně spuštěné úkoly k dokončení a pak taky odebere uzel z fondu.<li>**retaineddata**– čeká na všechny místní data zachována úkolů na uzel, který má být vyčištění před odebráním uzlu z fondu.</ul> |

Můžete získat hodnotu z těchto proměnných definovaných službou s úpravami, které jsou na základě metrik ze služby Batch:

| Jen pro čtení služby definované proměnné | Popis |
| --- | --- |
| $CPUPercent |Průměrné procento využití procesoru. |
| $WallClockSeconds |Počet sekund využívat. |
| $MemoryBytes |Průměrný počet megabajtů použít. |
| $DiskBytes |Průměrný počet gigabajtů na místní disky používá. |
| $DiskReadBytes |Počet přečtených bajtů. |
| $DiskWriteBytes |Počet zapsaných bajtů. |
| $DiskReadOps |Počet operací čtení disku. |
| $DiskWriteOps |Počet provedených operací zápisu disku. |
| $NetworkInBytes |Počet bajtů. |
| $NetworkOutBytes |Počet bajtů. |
| $SampleNodeCount |Počet výpočetních uzlů. |
| $ActiveTasks |Počet úloh, které jsou připravené ke spuštění, ale nejsou ještě spouští. Počet $ActiveTasks zahrnuje všechny úkoly, které jsou ve stavu aktivní a byly splněny jejich závislosti. Od počtu $ActiveTasks nevylučují se žádné úlohy, které jsou v aktivním stavu, ale nebyly splněny jejich závislosti.|
| $RunningTasks |Počet úloh ve spuštěném stavu. |
| $PendingTasks |Součet $ActiveTasks a $RunningTasks. |
| $SucceededTasks |Počet úloh, které byly úspěšně dokončeny. |
| $FailedTasks |Počet úloh, které se nezdařilo. |
| $CurrentDedicatedNodes |Aktuální počet vyhrazených výpočetních uzlů. |
| $CurrentLowPriorityNodes |Aktuální počet s nízkou prioritou výpočetních uzlů, včetně všechny uzly, které byly dojde ke zrušení. |
| $PreemptedNodeCount | Počet uzlů ve fondu, které jsou ve stavu, dojde ke zrušení. |

> [!TIP]
> Jen pro čtení, služby definované proměnné, které jsou uvedeny v předchozí tabulce jsou *objekty* , které poskytují přístup k datům, které jsou spojené s jednotlivými různými způsoby. Další informace najdete v tématu [získat ukázková data](#getsampledata) dále v tomto článku.
>
>

## <a name="types"></a>Typy
Podporovány jsou tyto typy ve vzorci:

* double
* doubleVec
* doubleVecList
* řetězec
* časové razítko – časové razítko je složené strukturu, která obsahuje následující členy:

  * za rok
  * měsíc (1-12)
  * den (1-31)
  * den v týdnu (ve formátu číslo; například 1, pro pondělí)
  * hodina (ve 24hodinovém formátu; například 13 znamená, že 13: 00)
  * minut (00 až 59)
  * sekundy (00 až 59)
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
Tyto operace jsou povolené na typy, které jsou uvedené v předchozí části.

| Operace | Podporované operátory | Typ výsledku |
| --- | --- | --- |
| dvojité *operátor* double |+, -, *, / |double |
| dvojité *operátor* timeinterval |* |TimeInterval |
| doubleVec *operátor* double |+, -, *, / |doubleVec |
| doubleVec *operátor* doubleVec |+, -, *, / |doubleVec |
| TimeInterval *operátor* double |*, / |TimeInterval |
| TimeInterval *operátor* timeinterval |+, - |TimeInterval |
| TimeInterval *operátor* časové razítko |+ |časové razítko |
| časové razítko *operátor* timeinterval |+ |časové razítko |
| časové razítko *operátor* časové razítko |- |TimeInterval |
| *operátor*double |-, ! |double |
| *operátor*timeinterval |- |TimeInterval |
| dvojité *operátor* double |<, <=, ==, >=, >, != |double |
| řetězec *operátor* řetězec |<, <=, ==, >=, >, != |double |
| časové razítko *operátor* časové razítko |<, <=, ==, >=, >, != |double |
| TimeInterval *operátor* timeinterval |<, <=, ==, >=, >, != |double |
| dvojité *operátor* double |&&, &#124;&#124; |double |

Při testování s tříhodnotový operátor double (`double ? statement1 : statement2`) nenulový je **true**, a nula je **false**.

## <a name="functions"></a>Functions
Tyto předdefinované **funkce** jsou k dispozici pro použití při definování automatické vzorec škálování.

| Funkce | Návratový typ | Popis |
| --- | --- | --- |
| AVG(doubleVecList) |double |Vrátí průměrnou hodnotu pro všechny hodnoty v doubleVecList. |
| Len(doubleVecList) |double |Vrátí délku vektoru, který je vytvořen z doubleVecList. |
| LG(Double) |double |Vrátí protokol základní 2 double. |
| LG(doubleVecList) |doubleVec |Vrátí základní 2 doubleVecList protokolu podle komponent. Vec(double) musí být explicitně předaná v parametru. V opačném případě se předpokládá double lg(double) verze. |
| ln(Double) |double |Vrátí přirozený logaritmus double. |
| ln(doubleVecList) |doubleVec |Vrátí základní 2 doubleVecList protokolu podle komponent. Vec(double) musí být explicitně předaná v parametru. V opačném případě se předpokládá double lg(double) verze. |
| log(Double) |double |Vrátí protokol základní 10 double. |
| log(doubleVecList) |doubleVec |Vrátí základní 10 doubleVecList protokolu podle komponent. Vec(double) musí být explicitně předána pro jeden parametr double. V opačném případě se předpokládá double log(double) verze. |
| Max(doubleVecList) |double |Vrátí maximální hodnotu doubleVecList. |
| min(doubleVecList) |double |Vrátí minimální hodnotu doubleVecList. |
| Norm(doubleVecList) |double |Vrátí dva normály vektor, který je vytvořen z doubleVecList. |
| percentil (doubleVec v dvojitých p) |double |Vrátí hodnotu percentilu prvku vektoru v. |
| rand() |double |Vrací náhodnou hodnota v rozmezí 0,0 až 1,0. |
| Range(doubleVecList) |double |Vrátí rozdíl mezi minimální a maximální hodnoty doubleVecList. |
| STD(doubleVecList) |double |Vrátí směrodatnou odchylku ukázkové hodnoty v doubleVecList. |
| stop() | |Zastaví vyhodnocení výrazu automatického škálování. |
| SUM(doubleVecList) |double |Vrátí součet všech součástí doubleVecList. |
| čas (řetězec datum a čas = "") |časové razítko |Vrátí časové razítko od aktuálního času, pokud jsou předány žádné parametry nebo časové razítko řetězce data a času, pokud je předán. Formáty podporované data a času jsou W3C DTF a RFC 1123. |
| Val (doubleVec v dvojitých i) |double |Vrátí hodnotu prvku, který je na místě i ve vektoru v, s počáteční index 0. |

Některé funkce, které jsou popsány v předchozí tabulce může přijmout seznam jako argument. Čárkami oddělený seznam je kombinací *double* a *doubleVec*. Příklad:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*DoubleVecList* hodnotu převést na jediné *doubleVec* před hodnocení. Například pokud `v = [1,2,3]`, následným voláním `avg(v)` je ekvivalentní volání `avg(1,2,3)`. Volání `avg(v, 7)` je ekvivalentní volání `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Získat ukázková data
Vzorce automatického škálování reagovat na data metrik (Ukázky), které poskytuje služba Batch. Vzorec se zvětší nebo zmenší velikost fondu na základě hodnot, které získá od služby. Služby definované proměnné, které bylo popsáno dříve, jsou objekty, které poskytují různé metody pro přístup k datům, která souvisí s tímto objektem. Například následující výraz ukazuje požadavek na získání posledních pět minut využití procesoru:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metoda | Popis |
| --- | --- |
| GetSample() |`GetSample()` Metoda vrátí Vektor údajům.<br/><br/>Ukázka je 30 sekund za data metrik. Jinými slovy ukázky jsou získány každých 30 sekund. Ale jak je uvedeno níže, dochází ke zpoždění mezi při ukázku shromažďovaných a když je k dispozici na vzorec. Všechny ukázky pro zadané časové období v důsledku toho může být k dispozici pro vyhodnocení podle vzorce.<ul><li>`doubleVec GetSample(double count)`<br/>Určuje počet vzorků, které mají získat z poslední ukázky, které byly shromážděny.<br/><br/>`GetSample(1)` vrací posledního vzorku o určitý k dispozici. Pro metriky, jako jsou `$CPUPercent`, ale to neměli použít, protože není možné zjistit *při* ukázka byla shromážděna. Může být nedávný časový údaj, nebo kvůli problémům s systému, může být mnohem starší. Je lepší v takových případech použití časový interval, jak je znázorněno níže.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Určuje časový rámec pro shromažďování ukázková data. Volitelně můžete také určuje procento vzorků, které musí být k dispozici v požadovaný časový rámec.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` vracel 20 ukázky, pokud jsou k dispozici v historii CPUPercent všechny ukázky pro posledních 10 minut. Pokud poslední minuty historie nebyl k dispozici, ale pouze 18 ukázky by vrátila. V tomto případě:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` selže, protože nejsou k dispozici pouze 90 % vzorků.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` by uspěl.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Určuje časový rámec pro shromažďování dat, s počáteční čas a koncový čas.<br/><br/>Jak je uvedeno výše, dochází ke zpoždění mezi při ukázku shromažďovaných a když je k dispozici na vzorec. Vezměte v úvahu tomuto zpoždění dochází při použití `GetSample` metody. Zobrazit `GetSamplePercent` níže. |
| GetSamplePeriod() |Vrátí dobu ukázky, které byly provedeny v historických ukázkovou datovou sadu. |
| Count() |Vrátí celkový počet vzorků v historii metriky. |
| HistoryBeginTime() |Vrátí časové razítko nejstaršího vzorku dostupná data pro metriku. |
| GetSamplePercent() |Vrátí podíl vzorků, které jsou k dispozici pro daném časovém intervalu. Příklad:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Protože `GetSample` metoda selže, pokud je procento vzorků menší než `samplePercent` zadaný, můžete použít `GetSamplePercent` metoda a proveďte nejprve kontrolu. Poté můžete provádět alternativní akci, je-li dostatek ukázky jsou k dispozici, bez zastavení automatické škálování hodnocení. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Ukázky, procentuální vzorek a *GetSample()* – metoda
Základní provoz se vzorec automatického škálování je získat data metrik úloh a prostředků a potom upravte velikost fondu na základě těchto dat. V důsledku toho je důležité mít pochopili, jak vzorce automatického škálování interakci s daty metrik (Ukázky).

**Ukázky**

Služba Batch pravidelně trvá ukázky metrik úloh a prostředků a zpřístupní je vaše vzorce automatického škálování. Tyto ukázky jsou zaznamenány každých 30 sekund pomocí služby Batch. Existuje ale obvykle zpoždění mezi kdy byly zaznamenány tyto ukázky a když jsou k dispozici (a mohli číst) vzorce automatického škálování. Kromě toho různých faktorech, jako je například síť nebo jiných problémů s infrastrukturou, ukázky nemusí zaznamenána pro konkrétní interval.

**Procento vzorku**

Při `samplePercent` je předán `GetSample()` metoda nebo `GetSamplePercent()` metoda je volána, _procent_ odkazuje na porovnání mezi službou celkový počet vzorků, které jsou zaznamenány službou Batch a počet ukázky, které jsou k dispozici na váš vzorec automatického škálování.

Podívejme se na časový interval během 10 minut jako příklad. Vzhledem k tomu, že ukázky jsou zaznamenány v rámci časový interval během 10 minut každých 30 sekund, maximální celkový počet vzorků, které jsou zaznamenány službou Batch by 20 ukázky (2 za minutu). Ale kvůli spojená latence mechanismu generování sestav a dalších problémů v rámci Azure, může existovat pouze 15 ukázky, které jsou k dispozici na váš vzorec automatického škálování pro čtení. Tedy například za toto období během 10 minut, 75 % z celkového počtu vzorků, které jsou zaznamenány může být k dispozici pro váš vzorec.

**Ukázka a GetSample() rozsahy**

Vaše vzorce automatického škálování se chystáte zvětšování a zmenšování fondech &mdash; přidávání uzlů nebo odebrání uzlů. Protože uzly nákladů peníze, budete chtít zajistit, že vaše vzorce použít metodu inteligentní analýzy, která je založena na dostatečným množstvím dat. Proto doporučujeme použít analýzu trendů typu ve vzorcích používat. Tento typ rozrůstá nebo zmenšuje vašich fondů na základě rozsahu shromážděných vzorků.

Chcete-li tak učinit, použijte `GetSample(interval look-back start, interval look-back end)` vrátit vektor ukázky:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Při vyhodnocování řádek výše službou Batch, vrátí celou řadu ukázek jako vektor hodnot. Příklad:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Jakmile jste shromážděna vektoru vzorků, pak můžete použít funkce, jako je `min()`, `max()`, a `avg()` odvodit smysluplné hodnoty ze shromážděných rozsahu.

Za účelem zvýšení zabezpečení můžete vynutit vzorců selhat, pokud je nižší než určité procento ukázka je k dispozici pro určité časové období. Při vynucení vzorců selhání dáte pokyn dávkové hodnocení vzorce přestanou dále, pokud zadané procento vzorků, které není k dispozici. V takovém případě se neprovedly žádné změny velikosti fondu. K určení požadovaných procento vzorků pro hodnocení úspěšné, zadejte jako třetí parametr `GetSample()`. Tady je určen požadavek 75 procent vzorků:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Ukázka dostupnosti může dojít ke zpoždění, proto je důležité vždy určit časový rozsah s časem zahájení zpětným vzhled, který je starší než jednu minutu. Trvá přibližně jednu minutu pro ukázky, které rozšíří v rámci systému, takže ukázky v rozsahu `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` nemusí být k dispozici. Znovu, můžete použít parametr procento `GetSample()` vynutit požadavek procento konkrétní vzorek.

> [!IMPORTANT]
> Jsme **důrazně doporučujeme** , kterou jste **nespoléhejte *pouze* na `GetSample(1)` ve vzorcích používat automatické škálování**. Důvodem je, že `GetSample(1)` v podstatě uvádí, že ve službě Batch, "Dáme mě poslední ukázky jste mají, bez ohledu na to, jak dlouho před jejím načtení." Protože je pouze jeden vzorek a může být starší vzorku, nemusí být zástupce větší přehled o poslední úlohu nebo stav prostředku. Pokud používáte `GetSample(1)`, ujistěte se, že je součástí větší příkaz a ne pouze data bodu závislou na váš vzorec.
>
>

## <a name="metrics"></a>Metriky
Metriky prostředků a úloh můžete použít, když definujete vzorec. Můžete upravit cílový počet vyhrazených uzlů ve fondu na základě dat metrik, které můžete získat a vyhodnocení. Zobrazit [proměnné](#variables) výše uvedené části Další informace o jednotlivých metrik.

<table>
  <tr>
    <th>Metrika</th>
    <th>Popis</th>
  </tr>
  <tr>
    <td><b>Prostředek</b></td>
    <td><p>Metriky prostředků jsou založené na procesoru, šířky pásma, využití paměti výpočetních uzlů a počet uzlů.</p>
        <p> Tyto služby definované proměnné jsou užitečné pro provádění úprav na základě počtu uzlů:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Tyto služby definované proměnné jsou užitečné pro provádění úprav na základě využití prostředků uzlu:</p>
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
    <td><p>Metriky úlohy jsou na základě stavu úkolů, například aktivní, čekající a dokončeny. Následující služby definované proměnné jsou užitečné pro provádění úprav velikost fondu na základě metrik úloh:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Zápis se vzorec automatického škálování
Kompilují se vzorec automatického škálování vytvořením příkazy, které používají součásti výše a potom kombinace těchto příkazů do dokončení vzorce. V této části vytvoříme jeho příkladu vzorec, který může provádět některé reálné škálování rozhodnutí.

Nejdříve definujme požadavky pro náš nový vzorec automatického škálování. Vzorec by:

1. Pokud je vysoké využití procesoru, zvětšete cílový počet vyhrazené výpočetní uzly ve fondu.
2. Cílový počet vyhrazené výpočetní uzly ve fondu snížit, až bude malé využití procesoru.
3. Maximální počet vyhrazených uzlů vždy omezují na 400.

Chcete-li zvýšit počet uzlů během vysoké využití procesoru, definovat příkaz, který naplní uživatelem definované proměnné (`$totalDedicatedNodes`) s hodnotou, která je 110 procent společností z žebříčku číslo aktuální cílové vyhrazené uzly, ale pouze tehdy, pokud minimální průměrné využití procesoru během posledních 10 minut byla vyšší než 70 procent. V opačném případě použijte hodnotu pro aktuální počet vyhrazené uzly.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

K *snížit* počet vyhrazených uzlů při nízké využití procesoru, další příkaz ve vzorci nastaví stejný `$totalDedicatedNodes` proměnné až 90 procent aktuální cílový počet vyhrazených uzlů, pokud průměrné využití procesoru v minulosti 60 minuty se v části 20 procent. Jinak použijte aktuální hodnotu `$totalDedicatedNodes` , naplnili jsme v příkazu výše.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nyní omezte cílový počet vyhrazené výpočetní uzly až na 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Tady je úplný vzorec:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Vytvoření fondu s povoleným automatickým Škálováním pomocí .NET

K vytvoření fondu s automatickým Škálováním povolené v rozhraní .NET, postupujte podle těchto kroků:

1. Vytvoření fondu s [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Nastavte [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) vlastnost `true`.
3. Nastavte [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) vlastnost s vzorec automatického škálování.
4. (Volitelné) Nastavte [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) vlastnosti (výchozí hodnota je 15 minut).
5. Potvrdit fond s [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) nebo [commitasync vyvolá](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Následující fragment kódu vytvoří fond povolené automatické škálování v rozhraní .NET. Vzorec automatického škálování fondu nastaví cílový počet na 5 v pondělí vyhrazené uzly a 1 na každý den v týdnu. [Interval automatické škálování](#automatic-scaling-interval) nastavení je 30 minut. V tomto a dalších C# fragmenty kódu v tomto článku `myBatchClient` je správně inicializována instancí [BatchClient] [ net_batchclient] třídy.

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Při vytváření fondu povoleným automatickým Škálováním, nezadávejte _targetDedicatedComputeNodes_ parametr nebo _targetLowPriorityComputeNodes_ parametru při volání **CreatePool** . Místo toho zadat **AutoScaleEnabled** a **AutoScaleFormula** vlastnosti ve fondu. Hodnoty pro tyto vlastnosti určují cílový počet každého typu uzlu. Také do ručně automatickým Škálováním podporou změny velikosti fondu (třeba index Mei [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), první **zakázat** automatické škálování na fond a potom změňte jeho velikost.
>
>

Kromě služby Batch .NET, můžete použít některý z nich [sad SDK služby Batch](batch-apis-tools.md#azure-accounts-for-batch-development), [Batch REST](https://docs.microsoft.com/rest/api/batchservice/), [rutiny prostředí PowerShell služby Batch](batch-powershell-cmdlets-get-started.md)a [rozhraní příkazového řádku služby Batch](batch-cli-get-started.md) do Konfigurace automatického škálování.


### <a name="automatic-scaling-interval"></a>Interval automatického škálování
Ve výchozím nastavení služba Batch upraví velikost fondu podle jeho vzorec automatického škálování každých 15 minut. Tento interval je možné konfigurovat pomocí následujících vlastností fondu:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

Minimální interval je 5 minut a maximum 168 hodin je. Pokud je zadán interval mimo tento rozsah, služba Batch vrátí chybu chybný požadavek (400).

> [!NOTE]
> Automatické škálování není aktuálně určen reakce na změny v méně než minutu, ale je určena a upravte velikost fondu postupně při spuštění úlohy.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Povolit automatické škálování na existující fond

Jednotlivých sadách SDK služby Batch poskytuje způsob, jak povolit automatické škálování. Příklad:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [Povolit automatické škálování ve fondu][rest_enableautoscale] (REST API)

Když povolíte automatické škálování na existující fond, mějte na paměti následující body:

* Pokud automatické škálování je momentálně zakázané ve fondu Pokud vydáte požadavek na zapnutí automatického škálování, musíte zadat vzorec automatického škálování platný, pokud vydáte požadavek. Volitelně můžete zadat interval vyhodnocování automatického škálování. Pokud není zadán interval, je použita výchozí hodnota 15 minut.
* Pokud automatické škálování je aktuálně povolené ve fondu, můžete zadat jeho vzorec a interval testování. Musíte zadat alespoň jednu z těchto vlastností.

  * Pokud chcete zadat nový interval vyhodnocování automatického škálování, stávající plán vyhodnocení se zastaví a nový plán se spustí. Nový plán počáteční čas je čas, kdy byl vydán požadavek na zapnutí automatického škálování.
  * Pokud vynecháte vzorec automatického škálování nebo vyhodnocení interval, služba Batch nadále používá aktuální hodnotu daného nastavení.

> [!NOTE]
> Pokud jste zadali hodnoty *targetDedicatedComputeNodes* nebo *targetLowPriorityComputeNodes* parametry **CreatePool** metoda při vytváření fondu v .NET, nebo srovnatelný parametrů v jiném jazyce, pak tyto hodnoty jsou ignorovány, pokud je vyhodnocen vzorec automatického škálování.
>
>

Tento fragment kódu jazyka C# používá [Batch .NET] [ net_api] knihovny, které chcete povolit automatické škálování na existujícího fondu:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aktualizace se vzorec automatického škálování

Pokud chcete aktualizovat vzorec na existující fond povolené automatické škálování, volání operace, které chcete povolit automatické škálování s nový vzorec. Například, pokud automatického škálování už má povolenou `myexistingpool` při spuštění následující kód .NET svůj vzorec automatického škálování je nahrazena obsah `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aktualizovat interval automatického škálování

Pokud chcete aktualizovat interval vyhodnocování automatického škálování existujícího fondu, povolené automatické škálování, volejte operace chcete povolit automatické škálování znovu s novou intervalu. Chcete-li například nastavit interval vyhodnocování automatického škálování až 60 minut pro fond, který je již povolené automatické škálování v rozhraní .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Vyhodnocení se vzorec automatického škálování

Vzorce můžete vyhodnotit před každým jejím použitím do fondu. Tímto způsobem můžete otestovat vzorec tak, aby naleznete v tématu Jak její příkazy vyhodnotí před vzorec zadat do produkčního prostředí.

Vyhodnocování se vzorec automatického škálování, je nutné nejprve povolit automatické škálování fondu s platný vzorec. K otestování vzorec ve fondu s povoleným automatickým Škálováním ještě nemáte, použijte jeden jednořádkový vzorec `$TargetDedicatedNodes = 0` když poprvé povolíte automatické škálování. Potom použijte jednu z následujících vyhodnotit vzorec, který chcete testovat:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) nebo [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    ID existujícího fondu a řetězec obsahující vzorec automatického škálování pro vyhodnocení, požadují tyto metody rozhraní Batch .NET.

* [Vyhodnocení se vzorec automatického škálování](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    V tomto požadavku REST API, zadejte ID fondu v identifikátoru URI a vzorec automatického škálování v *autoScaleFormula* elementu těla požadavku. Odpověď operace obsahuje jakékoli informace o chybách, které můžou souviset s vzorec.

V tomto [Batch .NET] [ net_api] fragment kódu, vyhodnotíme jeho vzorec. Pokud fond nemá povoleným automatickým Škálováním, můžeme ho nejdřív povolit.

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
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
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

Úspěšné hodnocení vzorce ukazuje tento fragment kódu vytvoří podobné výsledky:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Získejte informace o spuštění automatického škálování

Aby bylo zajištěno, že váš vzorec funguje podle očekávání, doporučujeme vám pravidelně kontrolovat výsledky spuštění automatického škálování, které provádí dávkové ve vašem fondu. Ano, získat (nebo aktualizovat) odkaz na fond a podívejte se na vlastnosti jeho poslední automatického škálování spustit.

V Batch .NET [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) vlastnost má několik vlastností, které obsahují informace o nejnovější automatické škálování provede spuštění ve fondu:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

V rozhraní REST API [získat informace o fondu](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) vrátí informace o fondu, který obsahuje nejnovější automatické škálování spuštění informace v požadavku [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun) vlastnost.

Následující fragment kódu jazyka C# používá knihovnu Batch .NET tisknout informace o poslední automatickým Škálováním spouštět ve fondu _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Ukázkový výstup předchozím fragmentu kódu:

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
Podívejme se na několik vzorců, které ukazují různé způsoby, jak upravit množství výpočetních prostředků ve fondu.

### <a name="example-1-time-based-adjustment"></a>Příklad 1: Úpravy podle času
Předpokládejme, že chcete upravovat velikost fondu na základě den v týdnu a denní dobu. Tento příklad ukazuje, jak zvýšit nebo snížit počet uzlů ve fondu odpovídajícím způsobem.

Vzorec nejprve získá aktuální čas. Pokud je jeden den v týdnu (1-5) a v rámci pracovní doby (8: 00 do 18: 00), cílovou velikost fondu je nastavena na 20 uzlů. V opačném případě je nastavena na 10 uzlů.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Příklad 2: Úprava založené na úlohách
V tomto příkladu se upraví velikost fondu na základě počtu úloh ve frontě. Komentáře a zalomení řádků jsou přijatelné vzorců řetězce.

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
Tento příklad upraví velikost fondu na základě počtu úloh. Tento vzorec také bere v úvahu [MaxTasksPerComputeNode] [ net_maxtasks] hodnotu, která byla nastavena pro fond. Tento přístup je užitečné v situacích, kdy [paralelní provádění úkolů](batch-parallel-node-tasks.md) bylo povoleno ve vašem fondu.

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

### <a name="example-4-setting-an-initial-pool-size"></a>Příklad 4: Nastavení velikosti počáteční vytvoření fondu
Tento příklad ukazuje fragment kódu jazyka C# pomocí vzorce automatického škálování, které nastaví velikost fondu na zadaný počet uzlů pro počáteční časové období. Potom upraví velikost fondu na základě počtu spuštění a aktivních úloh po uplynutí počáteční čas období.

Vzorce v následujícím fragmentu kódu:

* Nastaví velikost počáteční vytvoření fondu na čtyři uzly.
* Během prvních 10 minut cyklu fondu nepřizpůsobí velikost fondu.
* Po 10 minutách, získá maximální hodnotu počtu spuštěné a aktivní úlohy za posledních 60 minut.
  * Pokud jsou obě hodnoty 0 (to znamená, že nejsou žádné úkoly byly spuštěné nebo aktivní za posledních 60 minut), je velikost fondu nastavit na hodnotu 0.
  * Pokud je buď hodnota větší než nula, se neprovedly žádné změny.

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
* [Maximalizujte využití prostředků výpočetní služby Azure Batch s souběžné úlohy uzlu](batch-parallel-node-tasks.md) obsahuje podrobnosti o tom, jak můžete spustit několik úloh současně na výpočetních uzlech ve fondu. Kromě automatického škálování tato funkce může pomoct snížit dobu trvání úlohy pro některé úlohy, což vám ušetří peníze.
* Pro jiné podpůrná efektivitu Ujistěte se, že aplikace Batch dotazuje službu Batch optimální způsobem. Zobrazit [efektivní dotazování na službu Azure Batch](batch-efficient-list-queries.md) se naučíte, chcete-li omezit množství dat, která se přenášejí při dotazu na stav tisíce výpočetních uzlů nebo úlohy.

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
