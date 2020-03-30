---
title: Automatické škálování výpočetních uzlů ve fondu dávek Azure | Dokumenty společnosti Microsoft
description: Povolte automatické škálování ve fondu cloudu a dynamicky upravte počet výpočetních uzlů ve fondu.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 10/24/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017,fasttrack-edit
ms.openlocfilehash: 398b6d9c3fc05a6cf164b4003f57b94ecd6c1972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054015"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Vytvoření automatického vzorce pro změnu měřítka výpočetních uzlů ve fondu dávek

Azure Batch můžete automaticky škálovat fondy na základě parametrů, které definujete. S automatickým škálováním batch dynamicky přidává uzly do fondu jako zvýšení požadavků na úlohy a odstraňuje výpočetní uzly při jejich snížení. Můžete ušetřit čas i peníze tím, že automaticky upraví počet výpočetních uzlů používaných vaší dávkové aplikace.

Automatické škálování ve fondu výpočetních uzlů povolíte tak, že s ním přidružíte *vzorec automatického škálování,* který definujete. Služba Batch používá vzorec automatického škálování k určení počtu výpočetních uzlů, které jsou potřebné ke spuštění úlohy. Výpočetní uzly mohou být vyhrazené uzly nebo [uzly s nízkou prioritou](batch-low-pri-vms.md). Batch reaguje na data metrik služby, která jsou shromažďována pravidelně. Pomocí těchto dat metrikbatch upraví počet výpočetních uzlů ve fondu na základě vzorce a v konfigurovatelném intervalu.

Můžete povolit automatické škálování při vytvoření fondu nebo v existujícím fondu. Můžete také změnit existující vzorec ve fondu, který je nakonfigurován pro automatické škálování. Batch umožňuje vyhodnotit vzorce před jejich přiřazením do fondů a sledovat stav automatického škálování.

Tento článek popisuje různé entity, které tvoří vzorce automatického škálování, včetně proměnných, operátorů, operací a funkcí. Popisujeme, jak získat různé metriky výpočetních prostředků a úloh v rámci batch. Pomocí těchto metrik můžete upravit počet uzlů fondu na základě využití prostředků a stavu úkolů. Pak popisujeme, jak vytvořit vzorec a povolit automatické škálování ve fondu pomocí rozhraní API batch REST a .NET. Nakonec dokončíme několik příkladů vzorců.

> [!IMPORTANT]
> Při vytváření účtu Batch můžete určit [konfiguraci účtu](batch-api-basics.md#account), která určuje, zda jsou fondy přiděleny v předplatném služby Batch (výchozí) nebo v uživatelském předplatném. Pokud jste vytvořili dávkový účet s výchozí konfigurací dávkové služby, je váš účet omezen na maximální počet jader, které lze použít ke zpracování. Dávková služba škáluje výpočetní uzly pouze do tohoto limitu jádra. Z tohoto důvodu služba Batch nemusí dosáhnout cílového počtu výpočetních uzlů určených vzorcem automatického škálování. Informace o zobrazení a zvýšení kvót účtu najdete v [tématu Kvóty a limity pro službu Azure Batch.](batch-quota-limit.md)
>
>Pokud jste svůj účet vytvořili v konfiguraci Uživatelské předplatné, váš účet bude mít podíl na základní kvótě předplatného. Další informace najdete v tématu [Omezení virtuálních počítačů](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Automatické škálování vzorců

Vzorec automatického škálování je hodnota řetězce, kterou definujete a která obsahuje jeden nebo více příkazů. Vzorec automatického škálování je přiřazen k elementu [autoScaleFormula][rest_autoscaleformula] fondu (Batch REST) nebo [vlastnosti CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (Batch .NET). Služba Batch používá vzorec k určení cílového počtu výpočetních uzlů ve fondu pro další interval zpracování. Řetězec vzorce nesmí překročit 8 kB, může obsahovat až 100 příkazů, které jsou odděleny středníky a mohou obsahovat konce řádků a komentáře.

Automatické škálování vzorců si můžete myslet jako jazyk automatického škálování dávky. Příkazy vzorců jsou volně formátované výrazy, které mohou zahrnovat jak proměnné definované službou (proměnné definované službou Batch), tak uživatelem definované proměnné (proměnné, které definujete). Mohou provádět různé operace s těmito hodnotami pomocí předdefinovaných typů, operátorů a funkcí. Příkaz může mít například následující podobu:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Vzorce obecně obsahují více příkazů, které provádějí operace s hodnotami, které jsou získány v předchozích příkazech. Nejprve například získáme `variable1`hodnotu pro , a `variable2`pak ji předáme funkci, která má být naplněna :

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Zahrňte tyto příkazy do vzorce automatického škálování, abyste dorazili na cílový počet výpočetních uzlů. Vyhrazené uzly a uzly s nízkou prioritou mají své vlastní cílové nastavení, takže můžete definovat cíl pro každý typ uzlu. Vzorec automatického škálování může obsahovat cílovou hodnotu pro vyhrazené uzly, cílovou hodnotu pro uzly s nízkou prioritou nebo obojí.

Cílový počet uzlů může být vyšší, nižší nebo stejný jako aktuální počet uzlů tohoto typu ve fondu. Dávka vyhodnocuje vzorec automatického škálování fondu v určitém intervalu (viz [intervaly automatického škálování).](#automatic-scaling-interval) Dávka upraví cílový počet jednotlivých typů uzlů ve fondu na číslo, které vzorec automatického škálování určuje v době vyhodnocení.

### <a name="sample-autoscale-formulas"></a>Ukázkové vzorce automatického škálování

Níže jsou uvedeny příklady dvou vzorců automatického škálování, které lze upravit tak, aby fungovaly pro většinu scénářů. Proměnné `startingNumberOfVMs` a `maxNumberofVMs` v ukázkových vzorcích lze přizpůsobit vašim potřebám.

#### <a name="pending-tasks"></a>Čekající úkoly

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

S tímto vzorcem automatického škálování je fond původně vytvořen pomocí jednoho virtuálního virtuálního virtuálního soudu. Metrika `$PendingTasks` definuje počet úloh, které jsou spuštěny nebo ve frontě. Vzorec vyhledá průměrný počet čekajících úkolů za posledních 180 sekund a odpovídajícím způsobem nastaví `$TargetDedicatedNodes` proměnnou. Vzorec zajišťuje, že cílový počet vyhrazených uzlů nikdy nepřekročí 25 virtuálních virtuálních modulů. Při odeslání nových úkolů se fond automaticky zvětšuje. Po dokončení úkolů se virtuální aplikace stanou jedním po druhém volnými a vzorec automatického škálování zmenší fond.

Tento vzorec škáluje vyhrazené uzly, ale lze jej upravit tak, aby se vztahoval i na uzly s nízkou prioritou.

#### <a name="preempted-nodes"></a>Předpisované uzly 

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Tento příklad vytvoří fond, který začíná 25 uzly s nízkou prioritou. Pokaždé, když je uzel s nízkou prioritou preempted, je nahrazen vyhrazeným uzlovým. Stejně jako v `maxNumberofVMs` prvním příkladu proměnná zabraňuje fondu z překročení 25 virtuálních her. Tento příklad je užitečný pro využití virtuálních počítačů s nízkou prioritou a také zajištění, že pouze pevný počet preemptions dojde po dobu životnosti fondu.

## <a name="variables"></a>Proměnné

Ve vzorcích automatického škálování můžete použít proměnné **definované službou** i **uživatelem.** Proměnné definované službou jsou integrovány do služby Batch. Některé proměnné definované službou jsou pro čtení a zápis a některé jsou jen pro čtení. Uživatelem definované proměnné jsou proměnné, které definujete. V ukázkovém vzorci zobrazeném v předchozí části `$TargetDedicatedNodes` a `$PendingTasks` jsou proměnné definované službou. Proměnné `startingNumberOfVMs` a `maxNumberofVMs` jsou uživatelem definované proměnné.

> [!NOTE]
> Proměnné definované službou jsou vždy předchází znak dolaru ($). U uživatelem definovaných proměnných je znak dolaru volitelný.
>
>

Následující tabulky zobrazují proměnné jen pro čtení a jen pro čtení, které jsou definovány službou Batch.

Můžete získat a nastavit hodnoty těchto proměnných definovaných službou pro správu počtu výpočetních uzlů ve fondu:

| Proměnné definované službou pro čtení a zápis | Popis |
| --- | --- |
| $TargetDedicatedNodes |Cílový počet vyhrazených výpočetních uzlů pro fond. Počet vyhrazených uzlů je určen jako cíl, protože fond nemusí vždy dosáhnout požadovaného počtu uzlů. Například pokud cílový počet vyhrazených uzlů je změněn hodnocení automatického škálování před fondu dosáhl počáteční cíl, pak fond nemusí dosáhnout cíle. <br /><br /> Fond v účtu vytvořeném pomocí konfigurace dávkové služby nemusí dosáhnout svého cíle, pokud cíl překročí uzel dávkového účtu nebo kvótu jádra. Fond v účtu vytvořeném pomocí konfigurace uživatelského předplatného nemusí dosáhnout svého cíle, pokud cíl překročí kvótu sdíleného jádra pro předplatné.|
| $TargetLowPriorityNodes |Cílový počet výpočetních uzlů s nízkou prioritou pro fond. Počet uzlů s nízkou prioritou je určen jako cíl, protože fond nemusí vždy dosáhnout požadovaného počtu uzlů. Například pokud cílový počet uzlů s nízkou prioritou je změněn hodnocení automatického škálování před fondu dosáhl počáteční cíl, pak fond nemusí dosáhnout cíle. Fond také nemusí dosáhnout svého cíle, pokud cíl překročí uzel účtu Batch nebo kvótu jádra. <br /><br /> Další informace o výpočetních uzlech s nízkou prioritou najdete v [tématu Použití virtuálních počítačů s nízkou prioritou s batch](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akce, ke které dochází při výpočetní uzly jsou odebrány z fondu. Možné hodnoty:<ul><li>**přeřadit do fronty**– výchozí hodnota. Okamžitě ukončí úlohy a vrátí je zpět do fronty úloh, aby byly přeplánovány. Tato akce zajišťuje, že cílový počet uzlů je dosaženo co nejrychleji, ale může být méně efektivní, protože všechny spuštěné úlohy budou přerušeny a musí být restartovány, plýtvání jakoukoli práci, kterou již udělali. <li>**terminate**--okamžitě ukončí úlohy a odebere je z fronty úloh.<li>**dokončení úkolu**--Čeká na dokončení aktuálně spuštěných úloh a potom odebere uzel z fondu. Tuto možnost použijte, chcete-li zabránit přerušení a opětovnému zařazení úloh do fronty, a plýtvat jakoukoli prací, kterou úkol provedl. <li>**retaineddata**--Waits for all the local task retained data on the node to be cleaned up before removing the node from the pool.</ul> |

> [!NOTE]
> Proměnnou `$TargetDedicatedNodes` lze také zadat `$TargetDedicated`pomocí aliasu . Podobně lze `$TargetLowPriorityNodes` proměnnou zadat pomocí `$TargetLowPriority`aliasu . Pokud jsou plně pojmenovaná proměnná i její alias nastaveny vzorcem, bude mít přednost hodnota přiřazená plně pojmenované proměnné.
>
>

Můžete získat hodnotu těchto proměnných definovaných službou provádět úpravy, které jsou založeny na metriky ze služby Batch:

| Proměnné definované službou jen pro čtení | Popis |
| --- | --- |
| $CPUPercent |Průměrné procento využití procesoru. |
| $WallClockSeconds |Počet spotřebovaných sekund. |
| $MemoryBytes |Průměrný počet použitých megabajtů. |
| $DiskBytes |Průměrný počet gigabajtů použitých na místních discích. |
| $DiskReadBytes |Počet přečtených bajtů. |
| $DiskWriteBytes |Počet zapsaných bajtů. |
| $DiskReadOps |Počet provedených operací čtení disku. |
| $DiskWriteOps |Počet provedených operací zápisu disku. |
| $NetworkInBytes |Počet příchozích bajtů. |
| $NetworkOutBytes |Počet odchozích bajtů. |
| $SampleNodeCount |Počet výpočetních uzlů. |
| $ActiveTasks |Počet úloh, které jsou připraveny k provedení, ale ještě nejsou spuštěny. Počet $ActiveTasks zahrnuje všechny úkoly, které jsou v aktivním stavu a jejichž závislosti byly splněny. Všechny úkoly, které jsou v aktivním stavu, ale jejichž závislosti nebyly splněny, jsou vyloučeny z $ActiveTasks počet. U úlohy s více instancemi bude $ActiveTasks zahrnovat počet instancí nastavených na úkolu.|
| $RunningTasks |Počet úloh ve spuštěném stavu. |
| $PendingTasks |Součet $ActiveTasks a $RunningTasks. |
| $SucceededTasks |Počet úkolů, které byly úspěšně dokončeny. |
| $FailedTasks |Počet neúspěšných úkolů. |
| $CurrentDedicatedNodes |Aktuální počet vyhrazených výpočetních uzlů. |
| $CurrentLowPriorityNodes |Aktuální počet výpočetních uzlů s nízkou prioritou, včetně všech uzlů, které byly předpovězeny. |
| $PreemptedNodeCount | Počet uzlů ve fondu, které jsou v preempted stavu. |

> [!TIP]
> Proměnné definované jen pro čtení, definované službou, které jsou zobrazeny v předchozí tabulce, jsou *objekty,* které poskytují různé metody pro přístup k datům přidruženým k jednotlivým. Další informace naleznete v [tématu Získání ukázkových dat](#getsampledata) dále v tomto článku.
>
>

## <a name="types"></a>Typy

Tyto typy jsou podporovány ve vzorci:

* double
* doubleVec
* doubleVecList
* řetězec
* časové razítko-- časové razítko je složená struktura, která obsahuje následující členy:

  * year
  * ukazatel měsíce (1-12)
  * den (1-31)
  * den v týdnu (ve formátu čísla; například 1 pro pondělí)
  * hodina (ve 24hodinovém číselném formátu, například 13 znamená 1 PM)
  * minuta (00-59)
  * druhý (00-59)
* časový interval

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

## <a name="operations"></a>Provoz

Tyto operace jsou povoleny u typů, které jsou uvedeny v předchozí části.

| Operace | Podporovaní operátoři | Typ výsledku |
| --- | --- | --- |
| dvojitý *operátor* dvojitý |+, -, *, / |double |
| dvojitý časový interval *operátora* |* |časový interval |
| doubleVec *operátor* double |+, -, *, / |doubleVec |
| doubleVec *operátor* doubleVec |+, -, *, / |doubleVec |
| *operátor* časového intervalu double |*, / |časový interval |
| časový interval *operátoru* časového intervalu |+, - |časový interval |
| časové razítko *operátoru* časového intervalu |+ |časové razítko |
| časový interval *operátoru* časového razítka |+ |časové razítko |
| časové razítko *operátoru* časového razítka |- |časový interval |
| *operátor*double |-, ! |double |
| časový interval *operátora* |- |časový interval |
| dvojitý *operátor* dvojitý |<, <=, ==, >=, >, != |double |
| řetězec *operátoru* řetězce |<, <=, ==, >=, >, != |double |
| časové razítko *operátoru* časového razítka |<, <=, ==, >=, >, != |double |
| časový interval *operátoru* časového intervalu |<, <=, ==, >=, >, != |double |
| dvojitý *operátor* dvojitý |&&, &#124;&#124; |double |

Při testování double s ternární`double ? statement1 : statement2`operátor ( ), nenulová **je true**a **nula**je false .

## <a name="functions"></a>Funkce
Tyto předdefinované **funkce** jsou k dispozici pro použití při definování vzorce automatického škálování.

| Funkce | Návratový typ | Popis |
| --- | --- | --- |
| avg(doubleVecList) |double |Vrátí průměrnou hodnotu pro všechny hodnoty v doubleVecList. |
| len (doubleVecList) |double |Vrátí délku vektoru, který je vytvořen z doubleVecList. |
| lg(double) |double |Vrátí základnu protokolu 2 double. |
| lg (doubleVecList) |doubleVec |Vrátí základnu protokolu z hlediska komponenty 2 doubleVecList. Vec(double) musí být explicitně předánpro parametr. V opačném případě se předpokládá dvojitá verze lg(double). |
| ln(dvojnásobek) |double |Vrátí přirozený protokol double. |
| ln(doubleVecList) |doubleVec |Vrátí přirozený protokol double. |
| log(double) |double |Vrátí základnu protokolu 10 double. |
| log(doubleVecList) |doubleVec |Vrátí základnu protokolu z hlediska komponenty 10 doubleVecList. Vec(double) musí být explicitně předánpro parametr single double. V opačném případě se předpokládá verze double log(double). |
| max (doubleVecList) |double |Vrátí maximální hodnotu v doubleVecList. |
| min(doubleVecList) |double |Vrátí minimální hodnotu v doubleVecList. |
| norma (doubleVecList) |double |Vrátí dvě normy vektoru, který je vytvořen z doubleVecList. |
| percentil (doubleVec v, double p) |double |Vrátí element percentilu vektoru v. |
| rand() |double |Vrátí náhodnou hodnotu mezi 0,0 a 1,0. |
| rozsah (doubleVecList) |double |Vrátí rozdíl mezi hodnotami min a max v doubleVecList. |
| std(doubleVecList) |double |Vrátí směrodatnou odchylku vzorku hodnot v doubleVecList. |
| stop() | |Zastaví vyhodnocení výrazu automatického škálování. |
| sum (doubleVecList) |double |Vrátí součet všech součástí doubleVecList. |
| time(řetězec dateTime="") |časové razítko |Vrátí časové razítko aktuálního času, pokud nejsou předány žádné parametry, nebo časové razítko řetězce dateTime, pokud je předán. Podporované formáty dateTime jsou W3C-DTF a RFC 1123. |
| val (doubleVec v, double i) |double |Vrátí hodnotu prvku, který je v umístění i ve vector v, s počátečním indexem nula. |

Některé funkce popsané v předchozí tabulce mohou přijmout seznam jako argument. Seznam oddělený čárkami je libovolná kombinace *double* a *doubleVec*. Například:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

*DoubleVecList* hodnota je převedena na jeden *doubleVec* před vyhodnocením. Například if `v = [1,2,3]`, `avg(v)` pak volání `avg(1,2,3)`je ekvivalentní volání . Volání `avg(v, 7)` je ekvivalentní `avg(1,2,3,7)`volání .

## <a name="obtain-sample-data"></a><a name="getsampledata"></a>Získat ukázková data

Vzorce automatického škálování fungují na základě dat metrik (ukadencí), která poskytuje služba Batch. Vzorec zvětšuje nebo zmenšuje velikost fondu na základě hodnot, které získá ze služby. Proměnné definované službou, které byly popsány dříve jsou objekty, které poskytují různé metody pro přístup k datům, která je přidružena k tomuto objektu. Například následující výraz zobrazuje požadavek na získání posledních pěti minut využití procesoru:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metoda | Popis |
| --- | --- |
| GetSample() |Metoda `GetSample()` vrátí vektor vzorků dat.<br/><br/>Ukázka dat metrik v hodnotě 30 sekund. Jinými slovy, vzorky se získávají každých 30 sekund. Ale jak je uvedeno níže, existuje zpoždění mezi tím, kdy je vzorek shromážděn a kdy je k dispozici pro vzorec. Jako takové mohou být ne všechny vzorky za dané časové období k dispozici pro hodnocení podle vzorce.<ul><li>`doubleVec GetSample(double count)`<br/>Určuje počet vzorků, které mají být získány z nejnovějších vzorků, které byly shromážděny.<br/><br/>`GetSample(1)`vrátí poslední dostupný vzorek. Pro metriky, jako `$CPUPercent`je , by však neměla být použita, protože není možné zjistit, *kdy* byl vzorek shromážděn. Může být nedávné, nebo z důvodu problémů se systémem, může být mnohem starší. V takových případech je lepší použít časový interval, jak je uvedeno níže.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Určuje časový rámec pro shromažďování ukázkových dat. Volitelně také určuje procento vzorků, které musí být k dispozici v požadovaném časovém rámci.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`vrátí 20 vzorků, pokud jsou všechny vzorky za posledních 10 minut přítomny v historii CPUPercent. Pokud by však nebyla k dispozici poslední minuta historie, bylo by vráceno pouze 18 vzorků. V tomto případě:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`by se nezdaří, protože pouze 90 procent vzorků jsou k dispozici.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`by uspěly.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Určuje časový rámec pro shromažďování dat s časem zahájení i časem ukončení.<br/><br/>Jak bylo uvedeno výše, existuje prodleva mezi odběrem vzorku a jeho dostupností pro vzorec. Zvažte toto zpoždění `GetSample` při použití metody. Viz `GetSamplePercent` níže. |
| GetSamplePeriod() |Vrátí období vzorků, které byly odebrány v historické ukázkové datové sadě. |
| Počet() |Vrátí celkový počet vzorků v historii metriky. |
| HistoryBeginTime() |Vrátí časové razítko nejstarší dostupné ukázky dat pro metriku. |
| GetSamplePercent() |Vrátí procento vzorků, které jsou k dispozici pro daný časový interval. Například:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Vzhledem `GetSample` k tomu, že metoda selže, `samplePercent` pokud procento vrácených vzorků je menší než zadaná, můžete použít metodu `GetSamplePercent` ke kontrole jako první. Potom můžete provést alternativní akci, pokud jsou k dispozici nedostatečné vzorky, bez zastavení automatické škálování hodnocení. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Vzorky, procento vzorku a metoda *GetSample()*
Základní operace vzorce automatického škálování je získat data metriky úloh a prostředků a potom upravit velikost fondu na základě těchto dat. Proto je důležité mít jasnou představu o tom, jak vzorce automatického škálování interagují s daty metrik (ukázky).

**ukázky**

Služba Batch pravidelně obceje ukázky metrik úkolů a prostředků a zpřístupňuje je pro vzorce automatického škálování. Tyto ukázky jsou zaznamenány každých 30 sekund službou Batch. Obvykle však dochází ke zpoždění mezi tím, kdy byly tyto vzorky zaznamenány, a tím, kdy jsou zpřístupněny (a lze je číst) ve vzorcích automatického škálování. Navíc z důvodu různých faktorů, jako je síť nebo jiné problémy s infrastrukturou, vzorky nemusí být zaznamenány pro určitý interval.

**Procento vzorku**

Při `samplePercent` předání metodě `GetSample()` nebo `GetSamplePercent()` je metoda volána, _procento_ odkazuje na porovnání mezi celkovým možným počtem vzorků, které jsou zaznamenány službou Batch, a počtem vzorků, které jsou k dispozici pro vzorec automatického škálování.

Podívejme se na 10-minutové časový rozpětí jako příklad. Vzhledem k tomu, že vzorky jsou zaznamenány každých 30 sekund v rámci 10minutového časovým rozpětí, maximální celkový počet vzorků, které jsou zaznamenány batch, bude 20 vzorků (2 za minutu). Však vzhledem k vlastní latence mechanismu vytváření sestav a další problémy v rámci Azure, může být pouze 15 ukázky, které jsou k dispozici pro váš vzorec automatického škálování pro čtení. Například pro toto 10minutové období může být ve vašem vzorci k dispozici pouze 75 % z celkového počtu zaznamenaných vzorků.

**GetSample() a rozsahy vzorků**

Vaše vzorce automatického škálování budou růst a &mdash; zmenšuje vaše fondy přidání uzlů nebo odebrání uzlů. Vzhledem k tomu, že uzly vás stojí peníze, chcete zajistit, aby vzorce používaly inteligentní metodu analýzy, která je založena na dostatečných datech. Proto doporučujeme použít analýzu trendového typu ve vzorcích. Tento typ zvětšuje a zmenšuje fondy na základě řady odebraných vzorků.

Chcete-li tak `GetSample(interval look-back start, interval look-back end)` učinit, použijte k vrácení vektoru vzorků:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Když je výše uvedený řádek vyhodnocen dávkou, vrátí rozsah vzorků jako vektor hodnot. Například:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Po shromáždění vektoru vzorků pak můžete použít funkce `min()` `max()`jako `avg()` , a odvodit smysluplné hodnoty ze shromážděného rozsahu.

Pro další zabezpečení můžete vynutit selhání vyhodnocení vzorce, pokud je pro určité časové období k dispozici méně než určité procento vzorku. Pokud vynutíte selhání vyhodnocení vzorce, dáte Batchovi pokyn, aby přestal a další hodnocení vzorce, pokud zadané procento vzorků není k dispozici. V tomto případě není provedena žádná změna velikosti fondu. Chcete-li zadat požadované procento vzorků pro úspěšné vyhodnocení, zadejte jej jako třetí parametr . `GetSample()` Zde je specifikován požadavek 75 procent vzorků:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Vzhledem k tomu, že může být zpoždění v dostupnosti vzorku, je důležité vždy zadat časový rozsah s čas emitování zpět, který je starší než jedna minuta. Trvá přibližně jednu minutu, než se vzorky rozšíří systémem, takže vzorky v rozmezí `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` nemusí být k dispozici. Opět můžete použít procentuální `GetSample()` parametr vynutit konkrétní požadavek na procento vzorku.

> [!IMPORTANT]
> **Důrazně doporučujeme,** abyste **se nespoléhali *pouze* na `GetSample(1)` vzorce automatického škálování**. Je to `GetSample(1)` proto, že v podstatě říká službě Batch: "Dejte mi poslední vzorek, který máte, bez ohledu na to, jak dlouho jste ho získali." Vzhledem k tomu, že se jedná pouze o jeden vzorek a může se jedná o starší vzorek, nemusí být reprezentativní pro širší obraz nedávného stavu úkolu nebo prostředků. Pokud používáte `GetSample(1)`, ujistěte se, že je součástí většího příkazu a není jediným datovým bodem, na který vzorec závisí.
>
>

## <a name="metrics"></a>Metriky

Metriky zdrojů i úkolů můžete použít při definování vzorce. Cílový počet vyhrazených uzlů ve fondu upravíte na základě dat metrik, které získáte a vyhodnotíte. Další informace o [jednotlivých metrikách](#variables) najdete v části Proměnné výše.

<table>
  <tr>
    <th>Metrika</th>
    <th>Popis</th>
  </tr>
  <tr>
    <td><b>Zdrojů</b></td>
    <td><p>Metriky prostředků jsou založeny na procesoru, šířku pásma, využití paměti výpočetních uzlů a počet uzlů.</p>
        <p> Tyto proměnné definované službou jsou užitečné pro provádění úprav na základě počtu uzlů:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Tyto proměnné definované službou jsou užitečné pro provádění úprav na základě využití prostředků uzlu:</p>
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
    <td><p>Metriky úkolů jsou založeny na stavu úkolů, jako je aktivní, čekající na vyřízení a dokončení. Následující proměnné definované službou jsou užitečné pro provádění úprav velikosti fondu na základě metrik úkolů:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Napsání vzorce automatického škálování

Vzorec automatického škálování vytvoříte vytvořením příkazů, které používají výše uvedené součásti, a pak tyto příkazy zkombinujte do úplného vzorce. V této části vytvoříme ukázkový vzorec automatického škálování, který může provádět některá rozhodnutí o měřítku v reálném světě.

Nejprve definujte požadavky na náš nový vzorec automatického škálování. Vzorec by měl:

1. Zvyšte cílový počet vyhrazených výpočetních uzlů ve fondu, pokud je vysoké využití procesoru.
1. Snižte cílový počet vyhrazených výpočetních uzlů ve fondu, když je nízké využití procesoru.
1. Vždy omezte maximální počet vyhrazených uzlů na 400.
1. Při snižování počtu uzlů neodstraňujte uzly, které jsou spuštěny úkoly; V případě potřeby počkejte, až budou úkoly dokončeny, a odeberte uzly.

Chcete-li zvýšit počet uzlů během vysoké využití procesoru, definujte příkaz, který naplňuje uživatelem definovanou proměnnou (`$totalDedicatedNodes`) hodnotou, která je 110 procent aktuálního cílového počtu vyhrazených uzlů, ale pouze v případě, že minimální průměrné využití procesoru během posledních 10 minut bylo vyšší než 70 procent. V opačném případě použijte hodnotu pro aktuální počet vyhrazených uzlů.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Chcete-li *snížit* počet vyhrazených uzlů během nízké využití procesoru, `$totalDedicatedNodes` další příkaz v našem vzorci nastaví stejnou proměnnou na 90 procent aktuálního cílového počtu vyhrazených uzlů, pokud průměrné využití procesoru za posledních 60 minut bylo pod 20 procent. V opačném případě použijte `$totalDedicatedNodes` aktuální hodnotu, kterou jsme naplnili ve výše uvedeném příkazu.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nyní omezte cílový počet vyhrazených výpočetních uzlů na maximálně 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Zde je kompletní vzorec:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Vytvoření fondu s podporou automatického škálování pomocí sad Batch SDK

Automatické škálování fondu lze nakonfigurovat pomocí libovolné sady [Batch SDK](batch-apis-tools.md#azure-accounts-for-batch-development), rutin prostředí [Batch REST API](https://docs.microsoft.com/rest/api/batchservice/) PowerShell a [rozhraní PŘÍKAZOVÉHO NASTAVENÍ](batch-cli-get-started.md) [dávky](batch-powershell-cmdlets-get-started.md). V této části můžete zobrazit příklady pro rozhraní .NET a Python.

### <a name="net"></a>.NET

Chcete-li vytvořit fond s povoleným automatickým škálováním v rozhraní .NET, postupujte takto:

1. Vytvořte fond pomocí [batchclient.pooloperations.createpool .](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool)
1. Nastavte vlastnost [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) na `true`.
1. Nastavte vlastnost [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) pomocí vzorce automatického škálování.
1. (Nepovinné) Nastavte vlastnost [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (výchozí hodnota je 15 minut).
1. Potvrzení fondu pomocí [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) nebo [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Následující fragment kódu vytvoří fond s podporou automatického škálování v rozhraní .NET. Vzorec automatického škálování fondu nastaví cílový počet vyhrazených uzlů na 5 v pondělí a 1 každý druhý den v týdnu. Interval [automatického škálování](#automatic-scaling-interval) je nastaven na 30 minut. V tomto a další c# výstřižky v tomto článku `myBatchClient` je správně inicializované instance [BatchClient][net_batchclient] třídy.

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
> Při vytváření fondu s povolenou automatickým škálování nezadávejte parametr _targetDedicatedNodes_ ani parametr _targetLowPriorityNodes_ při volání **fondu CreatePool**. Místo toho zadejte vlastnosti **AutoScaleEnabled** a **AutoScaleFormula** ve fondu. Hodnoty pro tyto vlastnosti určují cílový počet jednotlivých typů uzlů. Chcete-li ručně změnit velikost fondu s povolenou automatickým škálováním (například s [BatchClient.PoolOperations.ResizePoolAsync),][net_poolops_resizepoolasync] **nejprve zakažte** automatické škálování ve fondu a pak změňte jeho velikost.
>
>

#### <a name="automatic-scaling-interval"></a>Interval automatického škálování

Ve výchozím nastavení služba Batch upravuje velikost fondu podle vzorce automatického škálování každých 15 minut. Tento interval lze konfigurovat pomocí následujících vlastností fondu:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (dávka .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (ROZHRANÍ REST API)

Minimální interval je pět minut a maximální je 168 hodin. Pokud je zadán interval mimo tento rozsah, služba Batch vrátí chybu chybného požadavku (400).

> [!NOTE]
> Automatické škálování není aktuálně určena k reakci na změny za méně než minutu, ale je určena k postupnéúpravě velikosti fondu při spuštění úlohy.
>
>

### <a name="python"></a>Python

Podobně můžete vytvořit fond s podporou automatického škálování s sadou Python SDK pomocí:

1. Vytvořte fond a určete jeho konfiguraci.
1. Přidejte fond do klienta služby.
1. Povolte automatické škálování ve fondu pomocí vzorce, který píšete.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18; 
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5; 
             $isWorkingWeekdayHour = $workHours && $isWeekday; 
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10), 
                                            pool_enable_auto_scale_options=None, 
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Další příklady použití sady Python SDK najdete v [úložišti Batch Python Quickstart](https://github.com/Azure-Samples/batch-python-quickstart) na GitHubu.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Povolení automatického škálování v existujícím fondu

Každá sada Batch SDK poskytuje způsob, jak povolit automatické škálování. Například:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (dávka .NET)
* [Povolení automatickéškálování ve fondu][rest_enableautoscale] (ROZHRANÍ REST API)

Když povolíte automatické škálování v existujícím fondu, mějte na paměti následující body:

* Pokud je automatické škálování aktuálně zakázáno ve fondu při vystavení požadavku na povolení automatického škálování, je nutné při vystavení požadavku zadat platný vzorec automatického škálování. Volitelně můžete zadat interval vyhodnocení automatického škálování. Pokud nezadáte interval, použije se výchozí hodnota 15 minut.
* Pokud je ve fondu aktuálně povoleno automatické škálování, můžete zadat vzorec automatického škálování, interval hodnocení nebo obojí. Je nutné zadat alespoň jednu z těchto vlastností.

  * Pokud zadáte nový interval vyhodnocení automatického škálování, bude stávající plán hodnocení zastaven a spuštěn nový plán. Čas zahájení nového plánu je čas, kdy byl vydán požadavek na povolení automatického škálování.
  * Pokud vyneche vzorec automatického škálování nebo interval vyhodnocení, služba Batch nadále používá aktuální hodnotu tohoto nastavení.

> [!NOTE]
> Pokud jste zadali hodnoty pro *targetDedicatedNodes* nebo *targetLowPriorityNodes* parametry **CreatePool** metody při vytváření fondu v rozhraní .NET nebo pro srovnatelné parametry v jiném jazyce, pak tyto hodnoty jsou ignorovány při vyhodnocování vzorce automatického škálování.
>
>

Tento fragment kódu jazyka C# používá knihovnu [Batch .NET][net_api] k povolení automatického škálování v existujícím fondu:

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

Chcete-li aktualizovat vzorec v existujícím fondu s povoleným automatickým škálováním, zavolejte operaci a znovu povolte automatické škálování s novým vzorcem. Pokud je například `myexistingpool` automatické škálování již povoleno při spuštění následujícího kódu .NET, jeho vzorec `myNewFormula`automatického škálování je nahrazen obsahem aplikace .

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aktualizace intervalu automatického škálování

Chcete-li aktualizovat interval vyhodnocení automatického škálování existujícího fondu s povoleným automatickým škálováním, zavolejte operaci a znovu povolte automatické škálování s novým intervalem. Chcete-li například nastavit interval vyhodnocení automatického škálování na 60 minut pro fond, který je již povoleno automatické škálování v rozhraní .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Vyhodnocení vzorce automatického škálování

Vzorec můžete vyhodnotit před jeho použitím ve fondu. Tímto způsobem můžete otestovat vzorec a zjistit, jak jeho příkazy vyhodnocují před uvažováním vzorce do produkčního prostředí.

Chcete-li vyhodnotit vzorec automatického škálování, musíte nejprve povolit automatické škálování ve fondu s platným vzorcem. Chcete-li otestovat vzorec ve fondu, který ještě nemá povoleno automatické `$TargetDedicatedNodes = 0` škálování, použijte jednořádkový vzorec při prvním povolení automatického škálování. Potom použijte jednu z následujících možností k vyhodnocení vzorce, který chcete testovat:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) nebo [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Tyto metody Batch .NET vyžadují ID existujícího fondu a řetězec obsahující vzorec automatického škálování k vyhodnocení.

* [Vyhodnocení vzorce automatického škálování](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    V tomto požadavku rozhraní REST API zadejte ID fondu v identifikátoru URI a vzorec automatického škálování v elementu *autoScaleFormula* těla požadavku. Odpověď operace obsahuje všechny informace o chybě, které mohou souviset se vzorcem.

V tomto fragmentu kódu [Batch .NET][net_api] vyhodnotíme vzorec automatického škálování. Pokud fond nemá automatické škálování povoleno, povoleme nejprve.

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

Úspěšné vyhodnocení vzorce zobrazeného v tomto fragmentu kódu přináší výsledky podobné:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Získání informací o spuštění automatického škálování

Chcete-li zajistit, aby vzorec fungoval podle očekávání, doporučujeme pravidelně kontrolovat výsledky spuštění automatického škálování, které batch provádí ve vašem fondu. Chcete-li tak učinit, získat (nebo aktualizovat) odkaz na fond a zkontrolujte vlastnosti jeho poslední spuštění automatického škálování.

V batch .NET má vlastnost [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) několik vlastností, které poskytují informace o nejnovějším automatickém škálování prováděném ve fondu:

* [Automatické měřítkoRun.Časové razítko](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [Automatické škálování.Výsledky](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [Automatické škálování.Chyba](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

V rozhraní REST API [vrátí informace o](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) požadavku fondu získat informace o fondu, který zahrnuje nejnovější informace o automatickém škálování ve vlastnosti [autoScaleRun.](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool)

Následující fragment kódu jazyka C# používá knihovnu Batch .NET k tisku informací o posledním spuštění automatického škálování ve fondu _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Ukázkový výstup předchozího úryvku:

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

## <a name="example-autoscale-formulas"></a>Příklad vzorců automatického škálování

Podívejme se na několik vzorců, které ukazují různé způsoby, jak upravit množství výpočetních prostředků ve fondu.

### <a name="example-1-time-based-adjustment"></a>Příklad 1: Úprava založená na čase

Předpokládejme, že chcete upravit velikost fondu na základě dne v týdnu a denní doby. Tento příklad ukazuje, jak odpovídajícím způsobem zvýšit nebo snížit počet uzlů ve fondu.

Vzorec nejprve získá aktuální čas. Pokud je den v týdnu (1-5) a v pracovní době (od 8:00 do 18:00), velikost cílového fondu je nastavena na 20 uzlů. V opačném případě je nastavena na 10 uzlů.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```
`$curTime`lze upravit tak, aby odrážely `time()` místní časové `TimeZoneInterval_Hour` pásmo přidáním k produktu a posunu UTC. Například použít `$curTime = time() + (-6 * TimeInterval_Hour);` pro horské letní čas (MDT). Mějte na paměti, že posun bude nutné upravit na začátku a na konci letního času (pokud je k dispozici).

### <a name="example-2-task-based-adjustment"></a>Příklad 2: Úprava založená na úkolu

V tomto příkladu je velikost fondu upravena na základě počtu úkolů ve frontě. Komentáře i zalomení řádků jsou přijatelné v řetězcích vzorců.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Příklad 3: Účtování paralelních úloh

Tento příklad upravuje velikost fondu na základě počtu úkolů. Tento vzorec také bere v úvahu [MaxTasksPerComputeNode][net_maxtasks] hodnotu, která byla nastavena pro fond. Tento přístup je užitečný v situacích, kdy bylo ve vašem fondu povoleno [paralelní provádění úloh.](batch-parallel-node-tasks.md)

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

Tento příklad ukazuje fragment kódu Jazyka C# se vzorcem automatického škálování, který nastaví velikost fondu na zadaný počet uzlů pro počáteční časové období. Potom upraví velikost fondu na základě počtu spuštěných a aktivních úloh po uplynutí počátečního časového období.

Vzorec v následujícím fragmentu kódu:

* Nastaví počáteční velikost fondu na čtyři uzly.
* Neupraví velikost fondu během prvních 10 minut životního cyklu fondu.
* Po 10 minutách získá maximální hodnotu počtu spuštěných a aktivních úloh za posledních 60 minut.
  * Pokud jsou obě hodnoty 0 (označující, že v posledních 60 minutách nebyly spuštěny nebo aktivní žádné úkoly), je velikost fondu nastavena na hodnotu 0.
  * Pokud je některá z hodnot větší než nula, nebude provedena žádná změna.

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

## <a name="next-steps"></a>Další kroky

* [Maximalizace využití výpočetních prostředků Azure Batch pomocí souběžných úloh uzlů](batch-parallel-node-tasks.md) obsahuje podrobnosti o tom, jak můžete provádět více úloh současně na výpočetních uzlech ve vašem fondu. Kromě automatického škálování může tato funkce pomoci snížit dobu trvání úlohy u některých úloh, což vám ušetří peníze.
* Pro další zvýšení efektivity, ujistěte se, že vaše aplikace Batch dotazy dávkové služby v nejoptimálnějším způsobem. Najdete [v tématu dotaz služby Azure Batch efektivně](batch-efficient-list-queries.md) se dozvíte, jak omezit množství dat, která prochází drátem při dotazování stav potenciálně tisíce výpočetních uzlů nebo úkolů.

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
