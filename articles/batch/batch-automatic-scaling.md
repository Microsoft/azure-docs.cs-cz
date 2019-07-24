---
title: Automatické škálování výpočetních uzlů ve fondu Azure Batch | Microsoft Docs
description: Povolte automatické škálování v cloudovém fondu, abyste mohli dynamicky upravovat počet výpočetních uzlů ve fondu.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
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
ms.openlocfilehash: 489a3935605432b485f7b0866668f6dbfaac686b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323754"
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Vytvoření vzorce automatického škálování pro škálování výpočetních uzlů ve fondu služby Batch

Azure Batch může automaticky škálovat fondy na základě parametrů, které definujete. Díky automatickému škálování Batch dynamicky přidává uzly do fondu, protože požadavky na úlohy zvyšují a odstraňují výpočetní uzly při jejich zmenšování. Můžete ušetřit čas i peníze tím, že automaticky upravíte počet výpočetních uzlů používaných vaší aplikací Batch. 

Automatické škálování se povoluje u fondu výpočetních uzlů tím, že se k němu přidruží *vzorec automatického škálování* , který definujete. Služba Batch používá ke zjištění počtu výpočetních uzlů potřebných ke spuštění vašich úloh vzorec automatického škálování. Výpočetní uzly můžou být vyhrazené uzly nebo [uzly s nízkou prioritou](batch-low-pri-vms.md). Služba Batch odpoví na data metrik služby, která se pravidelně shromažďují. Pomocí těchto dat metrik služby Batch upraví počet výpočetních uzlů ve fondu na základě vzorce a nastavitelného intervalu.

Automatické škálování můžete povolit buď při vytvoření fondu, nebo v existujícím fondu. Můžete také změnit existující vzorec ve fondu, který je nakonfigurován pro automatické škálování. Batch vám umožní vyhodnotit vzorce před jejich přiřazením ke fondům a monitorovat stav automatického škálování běhu.

Tento článek popisuje různé entity, které tvoří vzorce automatického škálování, včetně proměnných, operátorů, operací a funkcí. Probereme, jak v rámci služby Batch získat různé výpočetní prostředky a metriky úloh. Tyto metriky můžete použít k úpravě počtu uzlů fondu na základě využití prostředků a stavu úlohy. Pak popíšeme, jak vytvořit vzorec a povolit automatické škálování pro fond pomocí rozhraní API dávky i rozhraní .NET. Nakonec jsme dokončili několik vzorových vzorců.

> [!IMPORTANT]
> Při vytváření účtu Batch můžete zadat [konfiguraci účtu](batch-api-basics.md#account), která určuje, jestli se fondy přiřazují v předplatném služby Batch (výchozí), nebo v předplatném uživatele. Pokud jste vytvořili účet Batch s výchozí konfigurací služby Batch, je účet omezený na maximální počet jader, které se dají použít ke zpracování. Služba Batch škáluje výpočetní uzly až do limitu jader. Z tohoto důvodu služba Batch nedosáhne cílového počtu výpočetních uzlů určených vzorcem automatického škálování. Informace o zobrazení a zvýšení kvót účtu najdete v tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md) .
>
>Pokud jste vytvořili účet s konfigurací předplatného uživatele, pak váš účet sdílí v rámci kvóty základní pro předplatné. Další informace najdete v tématu [Omezení virtuálních počítačů](../azure-subscription-service-limits.md#virtual-machines-limits) v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Vzorce automatického škálování
Vzorec automatického škálování je řetězcová hodnota, kterou definujete, který obsahuje jeden nebo více příkazů. Vzorec automatického škálování je přiřazený k vlastnosti [autoScaleFormula][rest_autoscaleformula] element (Batch REST) or [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] fondu (Batch .NET). Služba Batch pomocí vzorce určí cílový počet výpočetních uzlů ve fondu pro další interval zpracování. Řetězec vzorce nesmí překročit 8 KB, může obsahovat až 100 příkazů, které jsou odděleny středníky a mohou obsahovat konce řádků a komentáře.

Vzorce automatického škálování můžete představit jako jazyk automatického škálování Batch. Příkazy vzorců jsou výrazy ve formátu Free, které mohou zahrnovat proměnné definované službou (proměnné definované službou Batch) a uživatelem definované proměnné (proměnné, které definujete). Mohou provádět různé operace s těmito hodnotami pomocí integrovaných typů, operátorů a funkcí. Například příkaz může mít následující tvar:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Vzorce obecně obsahují více příkazů, které provádějí operace s hodnotami získanými v předchozích příkazech. Například nejdřív získáme hodnotu pro `variable1`a pak ji předáte do funkce k naplnění: `variable2`

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Do vzorce automatického škálování zahrňte tyto příkazy, které budou doručeny do cílového počtu výpočetních uzlů. Vyhrazené uzly a uzly s nízkou prioritou mají své vlastní nastavení cíle, abyste mohli definovat cíl pro každý typ uzlu. Vzorec automatického škálování může zahrnovat cílovou hodnotu pro vyhrazené uzly, cílovou hodnotu uzlů s nízkou prioritou nebo obojí.

Cílový počet uzlů může být vyšší, nižší nebo stejný jako aktuální počet uzlů daného typu ve fondu. Batch vyhodnocuje vzorec automatického škálování fondu v určitém intervalu (viz [automatické intervaly škálování](#automatic-scaling-interval)). Batch upraví cílové číslo každého typu uzlu ve fondu na číslo, které vzorec automatického škálování určuje v době vyhodnocení.

### <a name="sample-autoscale-formulas"></a>Vzorové vzorce automatického škálování

Níže jsou uvedeny příklady dvou vzorců automatického škálování, které lze upravit tak, aby fungovaly pro většinu scénářů. Proměnné `startingNumberOfVMs` a`maxNumberofVMs` ukázkové vzorce lze upravit podle svých potřeb.

#### <a name="pending-tasks"></a>Nedokončené úlohy
```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Pomocí tohoto vzorce automatického škálování se zpočátku vytvoří fond s jedním virtuálním počítačem. `$PendingTasks` Metrika definuje počet úloh, které jsou spuštěny nebo zařazeny do fronty. Vzorec najde průměrný počet nedokončených úloh za posledních 180 sekund a nastaví `$TargetDedicatedNodes` proměnnou odpovídajícím způsobem. Vzorec zajistí, že cílový počet vyhrazených uzlů nikdy nepřekračuje 25 virtuálních počítačů. Při odeslání nových úloh se fond automaticky zvětšuje. Po dokončení úloh se virtuální počítače uvolní o jeden po jednom a vzorec automatického škálování zmenší fond.

Tento vzorec škáluje vyhrazené uzly, ale je možné ho upravit tak, aby se mohly použít i pro škálování uzlů s nízkou prioritou.

#### <a name="preempted-nodes"></a>Přepnuté uzly 
```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
```

Tento příklad vytvoří fond, který začíná s 25 uzly s nízkou prioritou. Pokaždé, když je uzel s nízkou prioritou přerušený, nahradí se vyhrazeným uzlem. Stejně jako v prvním příkladu `maxNumberofVMs` proměnná zabraňuje fondu v překročení 25 virtuálních počítačů. Tento příklad je vhodný pro využití virtuálních počítačů s nízkou prioritou a zároveň zajišťuje, aby po dobu životnosti fondu probíhal pouze pevný počet přerušení.

## <a name="variables"></a>Proměnné
Ve vzorcích automatického škálování můžete použít jak **definované služby** , tak **uživatelem definované** proměnné. Proměnné definované službou jsou integrované do služby Batch. Některé proměnné definované službou jsou pro čtení i zápis a některé jsou jen pro čtení. Uživatelsky definované proměnné jsou proměnné, které definujete. V ukázkovém vzorci uvedeném v předchozí části `$TargetDedicatedNodes` a `$PendingTasks` jsou proměnné definované službou. Proměnné `startingNumberOfVMs` a`maxNumberofVMs` jsou uživatelsky definované proměnné.

> [!NOTE]
> Proměnné definované službou vždy předcházejí znakem dolaru ($). V případě uživatelem definovaných proměnných je znak dolaru volitelný.
>
>

V následujících tabulkách jsou uvedeny proměnné pro čtení i zápis i jen pro čtení, které jsou definovány službou Batch.

Můžete získat a nastavit hodnoty těchto proměnných definovaných službou pro správu počtu výpočetních uzlů ve fondu:

| Proměnné definované službou pro čtení i zápis | Popis |
| --- | --- |
| $TargetDedicatedNodes |Cílový počet vyhrazených výpočetních uzlů pro fond. Počet vyhrazených uzlů je určen jako cíl, protože fond nemusí vždy dosáhnout požadovaného počtu uzlů. Například pokud je cílový počet vyhrazených uzlů upraven pomocí vyhodnocení automatického škálování předtím, než fond dosáhne počátečního cíle, fond nemusí dosáhnout cíle. <br /><br /> Fond v účtu vytvořeném s konfigurací služby Batch nemusí dosáhnout svého cíle, pokud cíl překročí uzel účtu Batch nebo kvótu jádra. Fond v účtu vytvořeném s konfigurací předplatného uživatele nemusí dosáhnout svého cíle, pokud cíl překračuje kvótu Shared Core pro předplatné.|
| $TargetLowPriorityNodes |Cílový počet výpočetních uzlů s nízkou prioritou pro fond. Počet uzlů s nízkou prioritou je zadán jako cíl, protože fond nemusí vždy dosáhnout požadovaného počtu uzlů. Například pokud je cílový počet uzlů s nízkou prioritou změněn pomocí vyhodnocení automatického škálování předtím, než fond dosáhne počátečního cíle, fond nemusí dosáhnout cíle. Fond také nemůže dosáhnout svého cíle, pokud cíl překračuje kvótu pro uzel účtu Batch nebo jader. <br /><br /> Další informace o výpočetních uzlech s nízkou prioritou najdete v tématu [použití virtuálních počítačů s nízkou prioritou ve službě Batch (Preview)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akce, ke které dojde, když jsou výpočetní uzly odebrány z fondu. Možné hodnoty jsou:<ul><li>**requeue**--ukončí úlohy okamžitě a umístí je zpátky do fronty úloh, aby byly přeplánovány.<li>**ukončit**– ukončí úlohy okamžitě a odebere je z fronty úloh.<li>**taskcompletion**– čeká na dokončení aktuálně spuštěných úloh a pak odebere uzel z fondu.<li>**retaineddata**– čeká na vyčištění všech místních dat zadržených úlohami na uzlu, který se má vyčistit před odebráním uzlu z fondu.</ul> |

Hodnotu těchto proměnných definovaných službou můžete získat tak, aby byly úpravy založené na metrikách ze služby Batch:

| Proměnné definované službou jen pro čtení | Popis |
| --- | --- |
| $CPUPercent |Průměrné procento využití procesoru. |
| $WallClockSeconds |Počet sekund spotřebovaných. |
| $MemoryBytes |Průměrný počet použitých megabajtů. |
| $DiskBytes |Průměrný počet gigabajtů použitých na místních discích. |
| $DiskReadBytes |Počet přečtených bajtů. |
| $DiskWriteBytes |Počet zapsaných bajtů. |
| $DiskReadOps |Počet provedených operací čtení disku. |
| $DiskWriteOps |Počet provedených operací zápisu disku. |
| $NetworkInBytes |Počet příchozích bajtů. |
| $NetworkOutBytes |Počet odchozích bajtů. |
| $SampleNodeCount |Počet výpočetních uzlů. |
| $ActiveTasks |Počet úloh, které jsou připravené ke spuštění, ale ještě nejsou spuštěny. Počet $ActiveTasks zahrnuje všechny úlohy, které jsou v aktivním stavu a jejichž závislosti byly splněny. Všechny úlohy, které jsou v aktivním stavu, ale jejichž závislosti nejsou splněné, jsou vyloučené z $ActiveTasks počet.|
| $RunningTasks |Počet úloh ve spuštěném stavu. |
| $PendingTasks |Součet $ActiveTasks a $RunningTasks. |
| $SucceededTasks |Počet úloh, které byly úspěšně dokončeny. |
| $FailedTasks |Počet úkolů, které selhaly. |
| $CurrentDedicatedNodes |Aktuální počet vyhrazených výpočetních uzlů. |
| $CurrentLowPriorityNodes |Aktuální počet výpočetních uzlů s nízkou prioritou včetně všech uzlů, které byly předem přerušené. |
| $PreemptedNodeCount | Počet uzlů ve fondu, které jsou ve stavu pre-přerušené. |

> [!TIP]
> Proměnné definované jen pro čtení, které jsou uvedeny v předchozí tabulce, jsou *objekty* , které poskytují různé metody pro přístup k datům, která jsou k nim přidružená. Další informace najdete v části [získání ukázkových dat](#getsampledata) dále v tomto článku.
>
>

## <a name="types"></a>Druhy
Ve vzorci jsou podporovány tyto typy:

* double
* doubleVec
* doubleVecList
* řetězec
* časové razítko – časové razítko je složená struktura, která obsahuje následující členy:

  * rok
  * měsíc (1-12)
  * den (1-31)
  * Weekday (ve formátu číslo, například 1 pro pondělí)
  * Hour (ve 24hodinovém formátu čísla; například 13 znamená 1 ODP.)
  * minuta (00-59)
  * sekunda (00-59)
* timeinterval

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
Tyto operace jsou povoleny u typů, které jsou uvedeny v předchozí části.

| Operace | Podporované operátory | Typ výsledku |
| --- | --- | --- |
| Double – *operátor* Double |+, -, *, / |double |
| *operátor* dvojité TimeInterval |* |timeinterval |
| *operátor* doubleVec Double |+, -, *, / |doubleVec |
| *operátor* doubleVec doubleVec |+, -, *, / |doubleVec |
| *operátor* TimeInterval Double |*, / |timeinterval |
| *operátor* TimeInterval TimeInterval |+, - |timeinterval |
| časové  razítko operátoru TimeInterval |+ |timestamp |
| *operátor* časového razítka TimeInterval |+ |timestamp |
| časové  razítko operátoru časového razítka |- |timeinterval |
| *operátor*Double |-, ! |double |
| *operátor*TimeInterval |- |timeinterval |
| Double – *operátor* Double |<, <=, ==, >=, >, != |double |
| řetězec *operátoru* řetězce |<, <=, ==, >=, >, != |double |
| časové  razítko operátoru časového razítka |<, <=, ==, >=, >, != |double |
| *operátor* TimeInterval TimeInterval |<, <=, ==, >=, >, != |double |
| Double – *operátor* Double |&&, &#124;&#124; |double |

Při testování typu Double pomocí ternárního operátoru`double ? statement1 : statement2`(), nenulová hodnota je **true**a nula je **false**.

## <a name="functions"></a>Funkce
Tyto předdefinované **funkce** jsou k dispozici pro použití při definování vzorce automatického škálování.

| Funkce | Návratový typ | Popis |
| --- | --- | --- |
| průměr (doubleVecList) |double |Vrátí průměrnou hodnotu pro všechny hodnoty v doubleVecList. |
| len (doubleVecList) |double |Vrátí délku vektoru, který je vytvořen z doubleVecList. |
| LG (Double) |double |Vrátí základ protokolu 2 pro Double. |
| LG (doubleVecList) |doubleVec |Vrátí základ protokolu doubleVecList, který je součástí komponenty. Vec (Double) se musí explicitně předat pro parametr. V opačném případě se předpokládá LG verze (Double). |
| LN (Double) |double |Vrátí přirozený logaritmus typu Double. |
| LN (doubleVecList) |doubleVec |Vrátí základ protokolu doubleVecList, který je součástí komponenty. Vec (Double) se musí explicitně předat pro parametr. V opačném případě se předpokládá LG verze (Double). |
| protokol (Double) |double |Vrátí základ protokolu 10 pro dvojitou hodnotu. |
| protokol (doubleVecList) |doubleVec |Vrátí základ protokolu doubleVecList, který je součástí komponenty. Vec (Double) je nutné explicitně předat pro jeden parametr typu Double. V opačném případě se předpokládá, že je použita dvojitá verze protokolu (Double). |
| Max (doubleVecList) |double |Vrátí maximální hodnotu v doubleVecList. |
| minimum (doubleVecList) |double |Vrátí minimální hodnotu v doubleVecList. |
| norma (doubleVecList) |double |Vrátí dvě normy vektoru, který je vytvořen z doubleVecList. |
| percentil (doubleVec v, Double p) |double |Vrátí percentil prvku Vector v. |
| Rand () |double |Vrátí náhodnou hodnotu mezi 0,0 a 1,0. |
| Rozsah (doubleVecList) |double |Vrátí rozdíl mezi hodnotami min a Max v doubleVecList. |
| STD (doubleVecList) |double |Vrátí směrodatnou odchylku hodnot v doubleVecList. |
| stop() | |Zastaví vyhodnocení výrazu automatického škálování. |
| Sum (doubleVecList) |double |Vrátí součet všech komponent doubleVecList. |
| čas (String dateTime = "") |timestamp |Vrátí časové razítko aktuálního času, pokud nejsou předány žádné parametry, nebo časové razítko řetězce dateTime, pokud je předán. Podporované formáty data a času jsou W3C-DTF a RFC 1123. |
| Val (doubleVec v, Double i) |double |Vrátí hodnotu elementu, který je v umístění i ve vektoru v, s počátečním indexem nula. |

Některé z funkcí, které jsou popsány v předchozí tabulce, mohou seznam přijmout jako argument. Seznam oddělený čárkami je libovolná kombinace typu *Double* a *doubleVec*. Příklad:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Hodnota *doubleVecList* je před vyhodnocením převedena na jednu *doubleVec* . Například pokud `v = [1,2,3]`, pak volání `avg(v)` je ekvivalentní volání `avg(1,2,3)`. Volání `avg(v, 7)` je ekvivalentní volání `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Získat ukázková data
Vzorce automatického škálování se chovají na základě dat metrik (ukázek) poskytovaných službou Batch. Vzorec zvětšuje nebo zmenšuje velikost fondu na základě hodnot, které získá ze služby. Proměnné definované službou, které byly popsány dříve, jsou objekty, které poskytují různé metody pro přístup k datům přidruženým k danému objektu. Například následující výraz ukazuje požadavek na získání posledních pěti minut využití CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metoda | Popis |
| --- | --- |
| Getsample () |`GetSample()` Metoda vrací vektor ukázek dat.<br/><br/>Vzorek je na data metriky o hodnotě 30 sekund. Jinými slovy jsou vzorky získány každých 30 sekund. Jak je uvedeno níže, nastane zpoždění mezi tím, kdy je vzorek shromážděn a kdy je k dispozici pro vzorec. V takovém případě nemusí být pro vyhodnocení vzorce k dispozici všechny vzorky za dané časové období.<ul><li>`doubleVec GetSample(double count)`<br/>Určuje počet vzorků, které se mají získat z nejaktuálnějších ukázek, které byly shromážděny.<br/><br/>`GetSample(1)`Vrátí poslední dostupnou ukázku. Pro metriky, `$CPUPercent`jako by však nemělo být použito, protože není možné zjistit, *kdy* byla ukázka shromážděna. Může to být nedávno nebo v důsledku systémových problémů může být to mnohem starší. V takových případech je lepší použít časový interval, jak je znázorněno níže.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Určuje časový rámec shromažďování ukázkových dat. Volitelně také Určuje procentuální hodnotu vzorků, které musí být k dispozici v požadovaném časovém rámci.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`Vrátí 20 vzorků, pokud jsou v historii CPUPercent k dispozici všechny ukázky za posledních 10 minut. Pokud poslední minuta historie nebyla dostupná, ale vrátí se jenom 18 vzorků. V tomto případě:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`selže, protože je k dispozici pouze 90% vzorků.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`bylo úspěšné.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Určuje časový rámec pro shromažďování dat s časem zahájení i časem ukončení.<br/><br/>Jak je uvedeno výše, nastane zpoždění mezi tím, kdy je shromážděna ukázka, a když je k dispozici pro vzorec. Zvažte tuto prodlevu při použití `GetSample` metody. Viz `GetSamplePercent` níže. |
| GetSamplePeriod() |Vrátí období vzorků, které byly získány v historické ukázkové sadě dat. |
| Count() |Vrátí celkový počet vzorků v historii metrik. |
| HistoryBeginTime() |Vrátí časové razítko ukázky nejstarších dostupných dat pro danou metriku. |
| GetSamplePercent() |Vrátí procentuální hodnotu vzorků, které jsou k dispozici v daném časovém intervalu. Příklad:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Vzhledem k tomu, že `samplePercent` `GetSamplePercent` metodasenezdařila,pokudprocentovrácenýchvzorkůjemenšínežzadané,můžetepoužítmetodu`GetSample` ke kontrole prvního. Pak můžete provést alternativní akci, pokud nejsou k dispozici dostatečné vzorky, aniž by došlo k zastavení automatického vyhodnocení měřítka. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Ukázky, procentuální vzorek a metoda *getsample ()*
Základní operací vzorce automatického škálování je získat data metrik úlohy a prostředku a pak upravit velikost fondu na základě těchto dat. V takovém případě je důležité mít jasné informace o tom, jak vzorce automatického škálování pracují s daty metrik (ukázky).

**Ukázky**

Služba Batch pravidelně přebírá ukázky metrik úloh a prostředků a zpřístupňuje je pro vzorce automatického škálování. Tyto ukázky se zaznamenávají každých 30 sekund službou Batch. Je však obvykle prodleva mezi tím, kdy byly tyto ukázky zaznamenány, a když jsou zpřístupněny pro (a lze je číst) ve vzorcích automatického škálování. Vzhledem k různým faktorům, jako jsou například síťové nebo jiné problémy s infrastrukturou, nemusí být vzorky pro určitý interval zaznamenávány.

**Vzorek v procentech**

V `samplePercent` případě, že je `GetSample()` Metoda předána `GetSamplePercent()` metodě nebo je volána metoda, odkazuje _procento_ na porovnání mezi celkovým možným počtem vzorků zaznamenaným službou Batch a počtem vzorků, které jsou k dispozici pro vzorec automatického škálování.

Pojďme se podívat jako na příklad s časovým intervalem na 10 minut. Vzhledem k tomu, že jsou vzorky zaznamenávány každých 30 sekund v rozmezí od 10 minut, je maximální celkový počet vzorků zaznamenaných dávkou dávek 20 vzorků (2 za minutu). Z důvodu základní latence mechanismu vytváření sestav a dalších problémů v rámci Azure může být k dispozici pouze 15 vzorků, které jsou k dispozici pro váš vzorec automatického škálování pro čtení. Například pro tuto dobu 10 minut může být pro vzorec k dispozici pouze 75% celkového počtu zaznamenaných vzorků.

**Getsample () a vzorové rozsahy**

Vzorce automatického škálování budou zvětšované a zmenšování fondů &mdash; přidáním uzlů nebo odebráním uzlů. Vzhledem k tomu, že se v uzlech stojí peníze, chcete zajistit, aby vzorce používaly inteligentní analýzu, která je založená na dostatečném množství dat. Proto doporučujeme, abyste ve vzorcích použili analýzu trendů. Tento typ rozroste a zmenší vaše fondy na základě škály shromážděných vzorků.

K tomu použijte `GetSample(interval look-back start, interval look-back end)` k vrácení vektoru vzorků:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Když je výše uvedený řádek vyhodnocován pomocí Batch, vrátí rozsah ukázek jako vektor hodnot. Příklad:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Po shromáždění vektoru ukázek pak můžete použít funkce jako `min()`, `max()`a `avg()` k odvození smysluplných hodnot z shromážděného rozsahu.

Pro zvýšení zabezpečení můžete vynutit, aby vyhodnocení vzorce nebylo úspěšné, pokud je pro konkrétní časové období k dispozici méně než určitá procentuální hodnota vzorku. Pokud vynutíte vyhodnocení vzorce jako neúspěšné, dáte pokyn dávce k zastavení dalšího vyhodnocení vzorce, pokud není k dispozici zadané procento vzorků. V takovém případě se u velikosti fondu neprovádí žádná změna. Chcete-li určit požadované procento vzorků pro úspěšné vyhodnocení, zadejte jej jako třetí parametr `GetSample()`. Zde je stanoven požadavek 75% vzorků:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Vzhledem k tomu, že může dojít ke zpoždění v ukázce, je důležité vždy zadat časový rozsah s časem zahájení vyhledávání, který je starší než jedna minuta. Může trvat přibližně jednu minutu, než se vzorky rozšíří přes systém, takže vzorky v tomto rozsahu `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` nemusí být k dispozici. Znovu můžete použít procentuální parametr `GetSample()` pro vynucení konkrétního procentuálního vzorku požadavku.

> [!IMPORTANT]
> **Důrazně doporučujeme** , abyste **se vyhnuli spoléhání *jenom* na `GetSample(1)` ve vzorcích automatického škálování**. Je to proto `GetSample(1)` , že v podstatě říkáme službě Batch, "dát mi poslední vzorek, který máte, bez ohledu na to, jak dlouho jste ho načetli." Vzhledem k tomu, že se jedná jenom o jednotlivou ukázku a může se jednat o starší vzorek, nemusí být reprezentativní pro větší obrázek nedávných úkolů nebo stavů prostředků. Pokud použijete `GetSample(1)`, ujistěte se, že je součástí většího příkazu, a ne jediného datového bodu, na kterém se vzorec spoléhá.
>
>

## <a name="metrics"></a>Metriky
Při definování vzorce můžete použít metriky prostředků i úloh. Cílový počet vyhrazených uzlů ve fondu můžete upravit na základě dat metrik, které získáte a vyhodnocujete. Další informace o jednotlivých metrikách najdete v části [proměnné](#variables) výše.

<table>
  <tr>
    <th>Metrika</th>
    <th>Popis</th>
  </tr>
  <tr>
    <td><b>Prostředek</b></td>
    <td><p>Metriky prostředků jsou založené na procesoru, šířce pásma, využití paměti výpočetních uzlů a počtu uzlů.</p>
        <p> Tyto proměnné definované službou jsou užitečné při provádění úprav v závislosti na počtu uzlů:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Tyto proměnné definované službou jsou užitečné při provádění úprav na základě využití prostředků uzlu:</p>
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
    <td><b>Úloha</b></td>
    <td><p>Metriky úloh jsou založené na stavu úkolů, například aktivní, čeká na vyřízení a dokončeno. Následující proměnné definované službou jsou užitečné při vytváření úprav velikosti fondu na základě metrik úloh:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Zápis vzorce automatického škálování
Vzorec automatického škálování vytvoříte tak, že vytvoříte příkazy, které používají výše uvedené komponenty, a pak tyto příkazy spojíte do kompletního vzorce. V této části vytvoříme ukázkový vzorec automatického škálování, který může provádět některá rozhodnutí o škálování v reálném čase.

Nejdřív definujte požadavky pro náš nový vzorec automatického škálování. Vzorec by měl:

1. Pokud je využití procesoru vysoké, zvyšte cílový počet vyhrazených výpočetních uzlů ve fondu.
2. Zmenšete cílový počet vyhrazených výpočetních uzlů ve fondu, pokud je nízká úroveň využití procesoru.
3. Vždy omezte maximální počet vyhrazených uzlů na 400.

Pokud chcete zvýšit počet uzlů během vysokého využití procesoru, definujte příkaz, který naplní uživatelsky definovanou proměnnou (`$totalDedicatedNodes`) s hodnotou, která je 110% aktuálního cílového počtu vyhrazených uzlů, ale pouze v případě, že minimální průměrné využití procesoru v rámci posledních 10 minut bylo vyšší než 70 procent. V opačném případě použijte hodnotu pro aktuální počet vyhrazených uzlů.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Pokud chcete *snížit* počet vyhrazených uzlů během nízkého využití procesoru, další příkaz v našem vzorci nastaví stejnou `$totalDedicatedNodes` proměnnou na 90% aktuálního cílového počtu vyhrazených uzlů, pokud průměrné využití CPU za posledních 60 minut bylo menší než 20. procent. V opačném případě použijte aktuální hodnotu `$totalDedicatedNodes` , kterou jsme naplnili v příkazu výše.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nyní omezte cílový počet vyhrazených výpočetních uzlů na maximum 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Toto je kompletní vzorec:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Vytvoření fondu s podporou automatického škálování pomocí .NET

Pokud chcete vytvořit fond s povoleným automatickým škálováním v .NET, postupujte podle těchto kroků:

1. Vytvořte fond pomocí [BatchClient. PoolOperations. CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Nastavte vlastnost [CloudPool. AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) na `true`.
3. Nastavte vlastnost [CloudPool. AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) vzorcem automatického škálování.
4. Volitelné Nastavte vlastnost [CloudPool. AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (výchozí hodnota je 15 minut).
5. Potvrďte fond pomocí [CloudPool. Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) nebo [commitasync vyvolá výjimka](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Následující fragment kódu vytvoří fond s povoleným autoškálou v .NET. Vzorec automatického škálování fondu nastavuje cílový počet vyhrazených uzlů na hodnotu 5 v pondělí a 1 každý druhý den v týdnu. [Interval automatického škálování](#automatic-scaling-interval) je nastavený na 30 minut. V tomto článku a dalších C# fragmentech kódu v tomto článku `myBatchClient` je správně inicializovaná instance třídy [BatchClient][net_batchclient] .

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
> Při vytváření fondu s povoleným autoškálou, nezadávejte parametr _targetDedicatedNodes_ ani parametr _TargetLowPriorityNodes_ pro volání **CreatePool**. Místo toho zadejte vlastnosti **AutoScaleEnabled** a **AutoScaleFormula** ve fondu. Hodnoty pro tyto vlastnosti určují cílové číslo každého typu uzlu. Chcete-li ručně změnit velikost fondu s povoleným automatickým škálováním (například pomocí [BatchClient. PoolOperations. ResizePoolAsync][net_poolops_resizepoolasync]),  nejprve zakažte automatické škálování ve fondu a pak změňte jeho velikost.
>
>

Kromě rozhraní Batch .NET můžete nakonfigurovat automatické škálování pomocí kterékoli z dalších [sad SDK pro Batch](batch-apis-tools.md#azure-accounts-for-batch-development), [dávek](https://docs.microsoft.com/rest/api/batchservice/) [prostředí PowerShell](batch-powershell-cmdlets-get-started.md)a rutiny Batch PowerShellu a rozhraní příkazového [řádku Batch](batch-cli-get-started.md) .


### <a name="automatic-scaling-interval"></a>Interval automatického škálování
Služba Batch standardně upravuje velikost fondu podle vzorce automatického škálování každých 15 minut. Tento interval lze konfigurovat pomocí následujících vlastností fondu:

* [CloudPool. AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST API)

Minimální interval je pět minut a maximální hodnota je 168 hodin. Pokud je zadaný interval mimo tento rozsah, služba Batch vrátí chybovou chybu žádosti (400).

> [!NOTE]
> Automatické škálování není v současné době určeno k reakci na změny za méně než minutu, ale místo toho je určeno k postupnému přizpůsobení velikosti fondu při spuštění úlohy.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Povolit automatické škálování u existujícího fondu

Každá sada Batch SDK nabízí způsob, jak povolit automatické škálování. Příklad:

* [BatchClient. PoolOperations. EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [Povolení automatického škálování u fondu][rest_enableautoscale] (REST API)

Pokud povolíte automatické škálování u existujícího fondu, pamatujte na tyto body:

* Pokud je ve fondu aktuálně zakázané automatické škálování, když vydáte požadavek na povolení automatického škálování, musíte při vystavení žádosti zadat platný vzorec automatického škálování. Volitelně můžete zadat interval vyhodnocování automatického škálování. Pokud interval neurčíte, použije se výchozí hodnota 15 minut.
* Pokud je ve fondu aktuálně povoleno automatické škálování, můžete zadat vzorec automatického škálování, interval vyhodnocování nebo obojí. Je nutné zadat alespoň jednu z těchto vlastností.

  * Pokud zadáte nový interval vyhodnocování automatického škálování, stávající plán vyhodnocení se zastaví a spustí se nový plán. Nový čas zahájení plánu je čas, kdy byl vydán požadavek na povolení automatického škálování.
  * Vynecháte-li vzorec automatického škálování nebo interval vyhodnocení, služba Batch bude nadále používat aktuální hodnotu tohoto nastavení.

> [!NOTE]
> Pokud jste zadali hodnoty parametrů *targetDedicatedNodes* nebo *targetLowPriorityNodes* metody **CreatePool** při vytváření fondu v rozhraní .NET nebo pro srovnatelné parametry v jiném jazyce, pak tyto hodnoty jsou ignoruje se při vyhodnocení vzorce automatického škálování.
>
>

Tento C# fragment kódu používá knihovnu [Batch .NET][net_api] k povolení automatického škálování v existujícím fondu:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aktualizace vzorce automatického škálování

Chcete-li aktualizovat vzorec v existujícím fondu s povoleným automatickým škálováním, zavolejte operaci, aby bylo možné znovu povolit automatické škálování pomocí nového vzorce. Pokud je například automatické škálování již povoleno `myexistingpool` při spuštění následujícího kódu .NET, je jeho vzorec automatického škálování nahrazen `myNewFormula`obsahem.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aktualizace intervalu automatického škálování

Chcete-li aktualizovat interval vyhodnocování automatického škálování stávajícího fondu s povoleným automatickým škálováním, zavolejte operaci, aby bylo možné znovu povolit automatické škálování s novým intervalem. Například pokud chcete nastavit interval vyhodnocování automatického škálování na 60 minut pro fond, který už je v .NET povolený pomocí automatického škálování:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Vyhodnocení vzorce automatického škálování

Vzorec můžete vyhodnotit před jeho použitím v rámci fondu. Tímto způsobem můžete otestovat vzorec, abyste viděli, jak se jeho příkazy vyhodnocují před vložením vzorce do produkčního prostředí.

Aby bylo možné vyhodnotit vzorec automatického škálování, musíte nejprve povolit automatické škálování ve fondu s platným vzorcem. Chcete-li otestovat vzorec ve fondu, ve kterém ještě není povolené automatické škálování, použijte při prvním zapnutí automatického `$TargetDedicatedNodes = 0` škálování vzorec s jedním řádkem. Pak použijte jednu z následujících hodnot pro vyhodnocení vzorce, který chcete testovat:

* [BatchClient. PoolOperations. EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) nebo [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Tyto metody služby Batch .NET vyžadují ID existujícího fondu a řetězec obsahující vzorec automatického škálování, který se má vyhodnotit.

* [Vyhodnocení vzorce automatického škálování](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    V této REST API žádosti zadejte ID fondu v identifikátoru URI a vzorec automatického škálování v elementu *autoScaleFormula* textu požadavku. Odezva operace obsahuje všechny informace o chybě, které se mohou vztahovat ke vzorci.

V tomto fragmentu kódu [Batch .NET][net_api] vyhodnocujeme vzorec automatického škálování. Pokud fond nemá povolené automatické škálování, doporučujeme ho nejdřív povolit.

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

Úspěšné vyhodnocení vzorce uvedeného v tomto fragmentu kódu generuje podobné výsledky:

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

Abyste měli jistotu, že vzorec funguje podle očekávání, doporučujeme, abyste pravidelně kontrolovali výsledky spuštění automatického škálování, které tato dávková operace provádí ve vašem fondu. Provedete to tak, že načtete (nebo aktualizujete) odkaz na fond a prohlédnete si vlastnosti posledního spuštění automatického škálování.

Ve službě Batch .NET má vlastnost [CloudPool. AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) několik vlastností, které poskytují informace o nejnovějším automatickém spuštění škálování provedené ve fondu:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

V REST API vrátí informace o žádosti [o fond](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) informace o fondu, který obsahuje nejnovější informace o spuštění automatického škálování ve vlastnosti [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) .

Následující C# fragment kódu používá knihovnu Batch .NET k tisku informací o posledním spuštění automatického škálování ve fondu _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Ukázkový výstup předcházejícího fragmentu kódu:

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

## <a name="example-autoscale-formulas"></a>Ukázka vzorců automatického škálování
Pojďme se podívat na několik vzorců, které ukazují různé způsoby, jak upravit množství výpočetních prostředků ve fondu.

### <a name="example-1-time-based-adjustment"></a>Příklad 1: Úprava na základě času
Předpokládejme, že chcete upravit velikost fondu na základě dne v týdnu a denního času. Tento příklad ukazuje, jak odpovídajícím způsobem zvýšit nebo snížit počet uzlů ve fondu.

Vzorec nejprve získá aktuální čas. Pokud se jedná o pracovní den (1-5) a v pracovní době (od 8 do 6 ODP), je velikost cílového fondu nastavená na 20 uzlů. V opačném případě je nastaveno na 10 uzlů.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Příklad 2: Úpravy založené na úlohách
V tomto příkladu se velikost fondu upraví na základě počtu úloh ve frontě. V řetězcích vzorců jsou přijatelné jak komentáře, tak i zalomení řádků.

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

### <a name="example-3-accounting-for-parallel-tasks"></a>Příklad 3: Monitorování účtů pro paralelní úkoly
Tento příklad upravuje velikost fondu na základě počtu úkolů. Tento vzorec také bere v úvahu hodnotu [MaxTasksPerComputeNode][net_maxtasks] , která byla pro fond nastavena. Tento přístup je užitečný v situacích, kdy je na vašem fondu povolených [paralelního spouštění úkolů](batch-parallel-node-tasks.md) .

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

### <a name="example-4-setting-an-initial-pool-size"></a>Příklad 4: Nastavení počáteční velikosti fondu
Tento příklad ukazuje fragment C# kódu se vzorcem automatického škálování, který nastaví velikost fondu na zadaný počet uzlů pro počáteční časové období. Pak upraví velikost fondu na základě počtu spuštěných a aktivních úloh po uplynutí počátečního časového období.

Vzorec v následujícím fragmentu kódu:

* Nastaví počáteční velikost fondu na čtyři uzly.
* Neupravuje velikost fondu během prvních 10 minut životního cyklu fondu.
* Po 10 minutách získá maximální hodnotu počtu spuštěných a aktivních úloh během posledních 60 minut.
  * Pokud jsou obě hodnoty 0 (což značí, že v posledních 60 minutách nebyly spuštěné nebo aktivní žádné úlohy), je velikost fondu nastavená na 0.
  * Pokud je jedna z hodnot větší než nula, žádná změna se neprovádí.

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
* [Maximalizujte využití výpočetních prostředků Azure Batch pomocí souběžných úloh uzlů](batch-parallel-node-tasks.md) obsahuje podrobné informace o tom, jak můžete na výpočetních uzlech ve fondu provádět více úloh současně. Kromě automatického škálování může tato funkce pomáhat snížit dobu trvání úloh u některých úloh a ušetřit tak peníze.
* Pro další zvýšení efektivity zajistěte, aby se v aplikaci Batch dotazoval na službu Batch optimálním způsobem. Pokud se chcete dozvědět, jak omezit množství dat, která se při dotazování na stav potenciálně tisíc výpočetních uzlů nebo úkolů přecházejí, podívejte se na téma [dotazování služby Azure Batch](batch-efficient-list-queries.md) .

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
