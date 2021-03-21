---
title: Automatické škálování výpočetních uzlů ve fondu služby Azure Batch
description: Povolte automatické škálování v cloudovém fondu, abyste mohli dynamicky upravovat počet výpočetních uzlů ve fondu.
ms.topic: how-to
ms.date: 11/23/2020
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 06f717e7c3ab8285b494f89c39838af6b0d96c8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381422"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Vytvoření automatického vzorce pro škálování výpočetních uzlů ve fondu služby Batch

Azure Batch může automaticky škálovat fondy na základě parametrů, které definujete, a ušetřit tak čas a peníze. Díky automatickému škálování Batch dynamicky přidává uzly do fondu, protože vydávají požadavky na úlohy a odebírá výpočetní uzly, protože požadavky na úlohy se snížily.

Pokud chcete povolit automatické škálování u fondu výpočetních uzlů, přidružte fond ke *vzorci automatického škálování* , který definujete. Služba Batch používá ke zjištění, kolik uzlů je potřebných ke spuštění vašich úloh, vzorec automatického škálování. Tyto uzly můžou být vyhrazené uzly nebo [uzly s nízkou prioritou](batch-low-pri-vms.md). Služba Batch pak bude pravidelně kontrolovat data metrik služby a použije je k úpravě počtu uzlů ve fondu na základě vzorce a v intervalu, který definujete.

Automatické škálování můžete povolit při vytváření fondu nebo ho použít pro existující fond. Batch vám umožní vyhodnotit vzorce před jejich přiřazením ke fondům a monitorovat stav automatického škálování běhu. Po nakonfigurování fondu pomocí automatického škálování můžete změnit vzorec později.

> [!IMPORTANT]
> Při vytváření účtu Batch můžete zadat [režim přidělování fondů](accounts.md), který určuje, jestli se fondy přidělují v předplatném služby Batch (výchozí) nebo v předplatném uživatele. Pokud jste vytvořili účet Batch s výchozí konfigurací služby Batch, je účet omezený na maximální počet jader, které se dají použít ke zpracování. Služba Batch škáluje výpočetní uzly až do limitu jader. Z tohoto důvodu služba Batch nedosáhne cílového počtu výpočetních uzlů určených vzorcem automatického škálování. Informace o zobrazení a zvýšení kvót účtu najdete v tématu [kvóty a omezení pro službu Azure Batch](batch-quota-limit.md) .
>
>Pokud jste účet vytvořili v režimu předplatného uživatele, pak váš účet sdílí v rámci kvóty základní pro předplatné. Další informace najdete v tématu [Omezení virtuálních počítačů](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="autoscale-formulas"></a>Vzorce automatického škálování

Vzorec automatického škálování je řetězcová hodnota, kterou definujete, který obsahuje jeden nebo více příkazů. Vzorec automatického škálování je přiřazen prvku [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) fondu (Batch REST) nebo [CloudPool. autoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (Batch .NET). Služba Batch pomocí vzorce určí cílový počet výpočetních uzlů ve fondu pro další interval zpracování. Řetězec vzorce nemůže přesáhnout 8 KB, může obsahovat až 100 příkazů, které jsou odděleny středníky a mohou obsahovat konce řádků a komentáře.

Vzorce automatického škálování můžete představit jako jazyk automatického škálování Batch. Příkazy vzorců jsou prázdné výrazy, které mohou zahrnovat proměnné definované službou (definované službou Batch) i proměnné definované uživatelem. Vzorce mohou provádět různé operace s těmito hodnotami pomocí integrovaných typů, operátorů a funkcí. Například příkaz může mít následující tvar:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Vzorce obecně obsahují více příkazů, které provádějí operace s hodnotami získanými v předchozích příkazech. Například nejdřív získáme hodnotu pro `variable1` a pak ji předáte do funkce k naplnění `variable2` :

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Do vzorce automatického škálování zahrňte tyto příkazy, které budou doručeny do cílového počtu výpočetních uzlů. Vyhrazené uzly a uzly s nízkou prioritou mají vlastní nastavení cíle. Vzorec automatického škálování může zahrnovat cílovou hodnotu pro vyhrazené uzly, cílovou hodnotu uzlů s nízkou prioritou nebo obojí.

Cílový počet uzlů může být vyšší, nižší nebo stejný jako aktuální počet uzlů daného typu ve fondu. Batch vyhodnocuje vzorec automatického škálování fondu v určitých [intervalech automatického škálování](#automatic-scaling-interval). Batch upraví cílové číslo každého typu uzlu ve fondu na číslo, které vzorec automatického škálování určuje v době vyhodnocení.

### <a name="sample-autoscale-formulas"></a>Vzorové vzorce automatického škálování

Níže jsou uvedeny příklady dvou vzorců automatického škálování, které lze upravit tak, aby fungovaly pro většinu scénářů. Proměnné `startingNumberOfVMs` a `maxNumberofVMs` ukázkové vzorce lze upravit podle svých potřeb.

#### <a name="pending-tasks"></a>Nedokončené úlohy

Pomocí tohoto vzorce automatického škálování se zpočátku vytvoří fond s jedním virtuálním počítačem. `$PendingTasks`Metrika definuje počet úloh, které jsou spuštěny nebo zařazeny do fronty. Vzorec najde průměrný počet nedokončených úloh za posledních 180 sekund a nastaví `$TargetDedicatedNodes` proměnnou odpovídajícím způsobem. Vzorec zajistí, že cílový počet vyhrazených uzlů nikdy nepřekračuje 25 virtuálních počítačů. Při odeslání nových úloh se fond automaticky zvětšuje. Po dokončení úloh se virtuální počítače uvolní a vzorec automatického škálování zmenší fond.

Tento vzorec škáluje vyhrazené uzly, ale je možné ho upravit tak, aby se mohly použít i pro škálování uzlů s nízkou prioritou.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Přepnuté uzly

Tento příklad vytvoří fond, který začíná s 25 uzly s nízkou prioritou. Pokaždé, když je uzel s nízkou prioritou přerušený, nahradí se vyhrazeným uzlem. Stejně jako v prvním příkladu `maxNumberofVMs` Proměnná zabraňuje fondu v překročení 25 virtuálních počítačů. Tento příklad je vhodný pro využití virtuálních počítačů s nízkou prioritou a zároveň zajišťuje, aby po dobu životnosti fondu probíhal pouze pevný počet přerušení.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Přečtěte si další informace o [tom, jak vytvořit vzorce automatického škálování](#write-an-autoscale-formula) a zobrazit další [Příklady vzorců automatického škálování](#example-autoscale-formulas) dále v tomto tématu.

## <a name="variables"></a>Proměnné

Ve vzorcích automatického škálování můžete použít jak **definované služby** , tak **uživatelem definované** proměnné.

Proměnné definované službou jsou integrované do služby Batch. Některé proměnné definované službou jsou pro čtení i zápis a některé jsou jen pro čtení.

Uživatelsky definované proměnné jsou proměnné, které definujete. Ve výše uvedeném příkladu vzorce `$TargetDedicatedNodes` a `$PendingTasks` jsou proměnné definované službou, zatímco `startingNumberOfVMs` a `maxNumberofVMs` jsou uživatelsky definované proměnné.

> [!NOTE]
> Proměnné definované službou vždy předcházejí znakem dolaru ($). V případě uživatelem definovaných proměnných je znak dolaru volitelný.

V následujících tabulkách jsou uvedeny proměnné pro čtení i zápis a jen pro čtení, které jsou definovány službou Batch.

### <a name="read-write-service-defined-variables"></a>Proměnné definované službou pro čtení i zápis

Můžete získat a nastavit hodnoty těchto proměnných definovaných službou pro správu počtu výpočetních uzlů ve fondu.

| Proměnná | Popis |
| --- | --- |
| $TargetDedicatedNodes |Cílový počet vyhrazených výpočetních uzlů pro fond. Tento parametr je zadán jako cíl, protože fond nemusí vždy dosáhnout požadovaného počtu uzlů. Například pokud je cílový počet vyhrazených uzlů upraven pomocí vyhodnocení automatického škálování předtím, než fond dosáhne počátečního cíle, fond nemusí dosáhnout cíle. <br /><br /> Fond v účtu vytvořeném v režimu služby Batch nemusí dosáhnout svého cíle, pokud cíl překročí uzel účtu Batch nebo kvótu jádra. Fond v účtu vytvořeném v režimu předplatného uživatele nemusí dosáhnout svého cíle, pokud cíl překračuje kvótu Shared Core pro předplatné.|
| $TargetLowPriorityNodes |Cílový počet výpočetních uzlů s nízkou prioritou pro fond. Tato hodnota je určená jako cíl, protože fond nemusí vždy dosáhnout požadovaného počtu uzlů. Například pokud je cílový počet uzlů s nízkou prioritou změněn pomocí vyhodnocení automatického škálování předtím, než fond dosáhne počátečního cíle, fond nemusí dosáhnout cíle. Fond také nemůže dosáhnout svého cíle, pokud cíl překračuje kvótu pro uzel účtu Batch nebo jader. <br /><br /> Další informace o výpočetních uzlech s nízkou prioritou najdete v tématu [použití virtuálních počítačů s nízkou prioritou ve službě Batch](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Akce, ke které dojde, když jsou výpočetní uzly odebrány z fondu. Možné hodnoty:<ul><li>**requeue**: výchozí hodnota. Ukončí úlohy okamžitě a umístí je zpátky do fronty úloh, aby byly přeplánovány. Tato akce zajistí, že se cílový počet uzlů dosáhne co nejrychleji. Může však být méně efektivní, protože všechny spuštěné úlohy budou přerušeny a pak bude nutné je zcela restartovat. <li>**ukončit**: ukončí úlohy okamžitě a odebere je z fronty úloh.<li>**taskcompletion**: čeká na dokončení aktuálně spuštěných úloh a pak odebere uzel z fondu. Tuto možnost použijte, pokud nechcete, aby se úlohy přerušily a znovu zařadily do fronty, což vylučuje veškerou práci, kterou úloha provedla.<li>**retaineddata**: čeká na vyčištění všech místních dat, která jsou na uzlu zachována před odebráním uzlu z fondu.</ul> |

> [!NOTE]
> `$TargetDedicatedNodes`Proměnnou lze také zadat pomocí aliasu `$TargetDedicated` . Podobně `$TargetLowPriorityNodes` lze proměnnou zadat pomocí aliasu `$TargetLowPriority` . Pokud je plně pojmenovaná proměnná i její alias nastavené vzorcem, bude mít přednost hodnota přiřazená plně pojmenované proměnné.

### <a name="read-only-service-defined-variables"></a>Proměnné definované službou jen pro čtení

Hodnotu těchto proměnných definovaných službou můžete získat tak, aby byly úpravy založené na metrikách ze služby Batch.

> [!IMPORTANT]
> Úkoly uvolnění úloh nejsou aktuálně zahrnuty do proměnných, které poskytují počty úloh, například $ActiveTasks a $PendingTasks. V závislosti na vašem vzorci automatického škálování to může způsobit odebrání uzlů bez dostupných uzlů pro spouštění úloh uvolnění úloh.

| Proměnná | Popis |
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
| $ActiveTasks |Počet úloh, které jsou připravené ke spuštění, ale ještě nejsou spuštěny. To zahrnuje všechny úkoly, které jsou v aktivním stavu a jejichž závislosti jsou splněné. Všechny úlohy, které jsou v aktivním stavu, ale jejichž závislosti nejsou splněné, jsou vyloučené z $ActiveTasks počet. V případě úlohy s více instancemi bude $ActiveTasks obsahovat počet instancí nastavených v úkolu.|
| $RunningTasks |Počet úloh ve spuštěném stavu. |
| $PendingTasks |Součet $ActiveTasks a $RunningTasks. |
| $SucceededTasks |Počet úloh, které byly úspěšně dokončeny. |
| $FailedTasks |Počet úkolů, které selhaly. |
| $TaskSlotsPerNode |Počet slotů úloh, které lze použít ke spuštění souběžných úloh na jednom výpočetním uzlu ve fondu. |
| $CurrentDedicatedNodes |Aktuální počet vyhrazených výpočetních uzlů. |
| $CurrentLowPriorityNodes |Aktuální počet výpočetních uzlů s nízkou prioritou, včetně všech zrušených uzlů. |
| $PreemptedNodeCount | Počet uzlů ve fondu, které jsou v zastaveném stavu. |

> [!TIP]
> Tyto proměnné definované službou jen pro čtení jsou *objekty* , které poskytují různé metody pro přístup k datům, která jsou k nim přidružená. Další informace najdete v části [získání ukázkových dat](#obtain-sample-data) dále v tomto článku.

> [!NOTE]
> Použijte `$RunningTasks` při škálování na základě počtu úloh spuštěných v určitém časovém okamžiku a `$ActiveTasks` při škálování na základě počtu úloh, které jsou zařazeny do fronty ke spuštění.

## <a name="types"></a>Typy

Vzorce automatického škálování podporují následující typy:

- double
- doubleVec
- doubleVecList
- řetězec
- časové razítko – složená struktura obsahující následující členy:
  - year
  - měsíc (1-12)
  - den (1-31)
  - Weekday (ve formátu číslo, například 1 pro pondělí)
  - Hour (ve 24hodinovém formátu čísla; například 13 znamená 1 ODP.)
  - minuta (00-59)
  - sekunda (00-59)
- timeinterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

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
| časové razítko *operátoru* TimeInterval |+ |časové razítko |
| *operátor* časového razítka TimeInterval |+ |časové razítko |
| časové razítko *operátoru* časového razítka |- |timeinterval |
| *operátor* Double |-, ! |double |
| *operátor* TimeInterval |- |timeinterval |
| Double – *operátor* Double |<, <=, = =, >=, >,! = |double |
| řetězec *operátoru* řetězce |<, <=, = =, >=, >,! = |double |
| časové razítko *operátoru* časového razítka |<, <=, = =, >=, >,! = |double |
| *operátor* TimeInterval TimeInterval |<, <=, = =, >=, >,! = |double |
| Double – *operátor* Double |&&  &#124;&#124; |double |

Při testování typu Double pomocí ternárního operátoru ( `double ? statement1 : statement2` ), nenulová hodnota je **true** a nula je **false**.

## <a name="functions"></a>Functions

Při definování vzorce automatického škálování můžete použít tyto předdefinované **funkce** .

| Funkce | Návratový typ | Description |
| --- | --- | --- |
| průměr (doubleVecList) |double |Vrátí průměrnou hodnotu pro všechny hodnoty v doubleVecList. |
| len (doubleVecList) |double |Vrátí délku vektoru, který je vytvořen z doubleVecList. |
| LG (Double) |double |Vrátí základ protokolu 2 pro Double. |
| LG (doubleVecList) |doubleVec |Vrátí základ protokolu doubleVecList, který je součástí komponenty. Vec (Double) se musí explicitně předat pro parametr. V opačném případě se předpokládá LG verze (Double). |
| LN (Double) |double |Vrátí přirozený logaritmus typu Double. |
| LN (doubleVecList) |doubleVec |Vrátí přirozený logaritmus typu Double. |
| protokol (Double) |double |Vrátí základ protokolu 10 pro dvojitou hodnotu. |
| protokol (doubleVecList) |doubleVec |Vrátí základ protokolu doubleVecList, který je součástí komponenty. Vec (Double) je nutné explicitně předat pro jeden parametr typu Double. V opačném případě se předpokládá, že je použita dvojitá verze protokolu (Double). |
| Max (doubleVecList) |double |Vrátí maximální hodnotu v doubleVecList. |
| minimum (doubleVecList) |double |Vrátí minimální hodnotu v doubleVecList. |
| norma (doubleVecList) |double |Vrátí dvě normy vektoru, který je vytvořen z doubleVecList. |
| percentil (doubleVec v, Double p) |double |Vrátí percentil prvku Vector v. |
| rand() |double |Vrátí náhodnou hodnotu mezi 0,0 a 1,0. |
| Rozsah (doubleVecList) |double |Vrátí rozdíl mezi hodnotami min a Max v doubleVecList. |
| STD (doubleVecList) |double |Vrátí směrodatnou odchylku hodnot v doubleVecList. |
| stop () | |Zastaví vyhodnocení výrazu automatického škálování. |
| Sum (doubleVecList) |double |Vrátí součet všech komponent doubleVecList. |
| čas (String dateTime = "") |časové razítko |Vrátí časové razítko aktuálního času, pokud nejsou předány žádné parametry, nebo časové razítko řetězce dateTime, pokud je předáno. Podporované formáty data a času jsou W3C-DTF a RFC 1123. |
| Val (doubleVec v, Double i) |double |Vrátí hodnotu elementu, který je v umístění i ve vektoru v, s počátečním indexem nula. |

Některé z funkcí, které jsou popsány v předchozí tabulce, mohou seznam přijmout jako argument. Seznam oddělený čárkami je libovolná kombinace typu *Double* a *doubleVec*. Například:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Hodnota *doubleVecList* je před vyhodnocením převedena na jednu *doubleVec* . Například pokud `v = [1,2,3]` , pak volání `avg(v)` je ekvivalentní volání `avg(1,2,3)` . Volání `avg(v, 7)` je ekvivalentní volání `avg(1,2,3,7)` .

## <a name="metrics"></a>Metriky

Při definování vzorce můžete použít metriky prostředků i úloh. Cílový počet vyhrazených uzlů ve fondu můžete upravit na základě dat metrik, které získáte a vyhodnocujete. Další informace o jednotlivých metrikách najdete v části [proměnné](#variables) výše.

<table>
  <tr>
    <th>Metric</th>
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
    <td><b>Úkol</b></td>
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

## <a name="obtain-sample-data"></a>Získat ukázková data

Základní operací vzorce automatického škálování je získat data metrik úloh a prostředků (ukázky) a pak upravit velikost fondu na základě těchto dat. V takovém případě je důležité jasně pochopit, jak vzorce automatického škálování pracují s ukázkami.

### <a name="methods"></a>Metody

Vzorce automatického škálování fungují na ukázkách dat metrik poskytovaných službou Batch. Vzorec zvětší nebo zmenší velikost fondu na základě hodnot, které získá. Proměnné definované službou jsou objekty, které poskytují metody pro přístup k datům přidruženým k danému objektu. Například následující výraz ukazuje požadavek na získání posledních pěti minut využití CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

K získání ukázkových dat o proměnných definovaných službou lze použít následující metody.

| Metoda | Popis |
| --- | --- |
| Getsample () |`GetSample()`Metoda vrací vektor ukázek dat.<br/><br/>Vzorek je na data metriky o hodnotě 30 sekund. Jinými slovy jsou vzorky získány každých 30 sekund. Jak je uvedeno níže, nastane zpoždění mezi tím, kdy je vzorek shromážděn a kdy je k dispozici pro vzorec. V takovém případě nemusí být pro vyhodnocení vzorce k dispozici všechny vzorky za dané časové období.<ul><li>`doubleVec GetSample(double count)`: Určuje počet vzorků, které se mají získat z posledních shromážděných ukázek. `GetSample(1)` Vrátí poslední dostupnou ukázku. Metriky, jako `$CPUPercent` například, by však `GetSample(1)` neměly být použity, protože není možné zjistit, *kdy* byla ukázka shromážděna. Může to být nedávno nebo z důvodu systémových problémů může být to mnohem starší. V takových případech je lepší použít časový interval, jak je znázorněno níže.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Určuje časový rámec pro shromažďování ukázkových dat. Volitelně také Určuje procentuální hodnotu vzorků, které musí být k dispozici v požadovaném časovém rámci. Například `$CPUPercent.GetSample(TimeInterval_Minute * 10)` vrátí 20 vzorků, pokud jsou v historii k dispozici všechny ukázky za posledních 10 minut `CPUPercent` . Pokud poslední minuta historie není dostupná, vrátí se jenom 18 ukázek. V tomto případě `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` selže, protože je k dispozici pouze 90% ukázek, ale to `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` bylo úspěšné.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Určuje časový rámec pro shromažďování dat s časem zahájení i časem ukončení. Jak je uvedeno výše, nastane zpoždění mezi tím, kdy je shromážděna ukázka, a když bude k dispozici pro vzorec. Zvažte tuto prodlevu při použití `GetSample` metody. Viz `GetSamplePercent` níže. |
| GetSamplePeriod() |Vrátí období vzorků, které byly získány v historické ukázkové sadě dat. |
| Count () |Vrátí celkový počet vzorků v historii metrik. |
| HistoryBeginTime() |Vrátí časové razítko ukázky nejstarších dostupných dat pro danou metriku. |
| GetSamplePercent() |Vrátí procentuální hodnotu vzorků, které jsou k dispozici v daném časovém intervalu. Například, `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Vzhledem k tomu, že `GetSample` metoda se nezdařila, pokud procento vrácených vzorků je menší než `samplePercent` zadané, můžete použít `GetSamplePercent` metodu ke kontrole prvního. Pak můžete provést alternativní akci, pokud nejsou k dispozici dostatečné vzorky, aniž by došlo k zastavení automatického vyhodnocení měřítka. |

### <a name="samples"></a>ukázky

Služba Batch pravidelně přebírá ukázky metrik úloh a prostředků a zpřístupňuje je pro vzorce automatického škálování. Tyto ukázky se zaznamenávají každých 30 sekund službou Batch. Je však obvykle prodleva mezi tím, kdy byly tyto ukázky zaznamenány, a když jsou zpřístupněny pro (a lze je číst) ve vzorcích automatického škálování. Vzorky se navíc nemusí zaznamenávat pro určitý interval, protože jde o faktory, jako jsou třeba síťové nebo jiné problémy s infrastrukturou.

### <a name="sample-percentage"></a>Vzorek v procentech

`samplePercent`V případě, že je metoda předána `GetSample()` metodě nebo `GetSamplePercent()` je volána metoda, odkazuje _procento_ na porovnání mezi celkovým možným počtem vzorků zaznamenaným službou Batch a počtem vzorků, které jsou k dispozici pro vzorec automatického škálování.

Pojďme se podívat jako na příklad s časovým intervalem na 10 minut. Vzhledem k tomu, že jsou vzorky zaznamenávány každých 30 sekund v rozmezí 10 minut, maximální celkový počet vzorků zaznamenaných dávkou by byl 20 vzorků (2 za minutu). Z důvodu základní latence mechanismu vytváření sestav a dalších problémů v rámci Azure může být k dispozici pouze 15 vzorků, které jsou k dispozici pro váš vzorec automatického škálování pro čtení. Například pro tuto dobu 10 minut může být pro vzorec k dispozici pouze 75% celkového počtu zaznamenaných vzorků.

### <a name="getsample-and-sample-ranges"></a>Getsample () a vzorové rozsahy

Vzorce automatického škálování budou rozšiřovat a zmenšovat své fondy přidáním nebo odebráním uzlů. Vzhledem k tomu, že se v uzlech stojí peníze, ujistěte se, že vzorce využívají inteligentní způsob analýzy, který je založený na dostatečném množství dat. Ve vzorcích doporučujeme použít analýzu trendů. Tento typ rozroste a zmenší vaše fondy na základě škály shromážděných vzorků.

K tomu použijte `GetSample(interval look-back start, interval look-back end)` k vrácení vektoru vzorků:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Když je výše uvedený řádek vyhodnocován pomocí Batch, vrátí rozsah ukázek jako vektor hodnot. Například:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Po shromáždění vektoru ukázek pak můžete použít funkce jako `min()` , `max()` a `avg()` k odvození smysluplných hodnot z shromážděného rozsahu.

Pro zvýšení zabezpečení můžete vynutit, aby vyhodnocení vzorce nebylo úspěšné, pokud je pro konkrétní časové období k dispozici méně než určitá procentuální hodnota vzorku. Pokud vynutíte vyhodnocení vzorce jako neúspěšné, dáte pokyn dávce k zastavení dalšího vyhodnocení vzorce, pokud není k dispozici zadané procento vzorků. V takovém případě se u velikosti fondu neprovádí žádná změna. Chcete-li určit požadované procento vzorků pro úspěšné vyhodnocení, zadejte jej jako třetí parametr `GetSample()` . Zde je stanoven požadavek 75% vzorků:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Vzhledem k tomu, že může dojít ke zpoždění v ukázce, měli byste vždycky zadat časový rozsah s časem zahájení vyhledávání, který je starší než jedna minuta. Může trvat přibližně jednu minutu, než se vzorky rozšíří přes systém, takže vzorky v tomto rozsahu `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` nemusí být k dispozici. Znovu můžete použít procentuální parametr `GetSample()` pro vynucení konkrétního procentuálního vzorku požadavku.

> [!IMPORTANT]
> Důrazně doporučujeme, abyste **se vyhnuli spoléhání *jenom* na `GetSample(1)` ve vzorcích automatického škálování**. Je to proto `GetSample(1)` , že v podstatě říkáme službě Batch, "dát mi poslední vzorek, který máte, bez ohledu na to, jak dlouho jste ho načetli." Vzhledem k tomu, že se jedná jenom o jednotlivou ukázku a může se jednat o starší vzorek, nemusí být reprezentativní pro větší obrázek nedávných úkolů nebo stavů prostředků. Pokud použijete, ujistěte se `GetSample(1)` , že je součástí většího příkazu, a ne jediného datového bodu, na kterém se vzorec spoléhá.

## <a name="write-an-autoscale-formula"></a>Zápis vzorce automatického škálování

Vzorec automatického škálování vytvoříte tak, že vytvoříte příkazy, které používají výše uvedené komponenty, a pak tyto příkazy spojíte do kompletního vzorce. V této části vytvoříme ukázkový vzorec automatického škálování, který může provádět rozhodování o škálování v reálném čase a provádět úpravy.

Nejdřív definujte požadavky pro náš nový vzorec automatického škálování. Vzorec by měl:

- Pokud je využití procesoru vysoké, zvyšte cílový počet vyhrazených výpočetních uzlů ve fondu.
- Zmenšete cílový počet vyhrazených výpočetních uzlů ve fondu, pokud je nízká úroveň využití procesoru.
- Vždy omezte maximální počet vyhrazených uzlů na 400.
- Při snižování počtu uzlů neodstraňujte uzly, na kterých běží úlohy. v případě potřeby před odebráním uzlů počkejte na dokončení úkolů.

Prvním příkazem v našem vzorci se zvýší počet uzlů během vysokého využití procesoru. Definujeme příkaz, který naplní uživatelsky definovanou proměnnou ( `$totalDedicatedNodes` ) hodnotou, která je 110% aktuálního cílového počtu vyhrazených uzlů, ale jenom v případě, že minimální průměrné využití CPU za posledních 10 minut bylo vyšší než 70%. V opačném případě používá hodnotu pro aktuální počet vyhrazených uzlů.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Pokud chcete snížit počet vyhrazených uzlů během nízkého využití procesoru, další příkaz v našem vzorci nastaví stejnou `$totalDedicatedNodes` proměnnou na 90% aktuálního cílového počtu vyhrazených uzlů, pokud průměrné využití procesoru za posledních 60 minut bylo menší než 20 procent. V opačném případě používá aktuální hodnotu `$totalDedicatedNodes` , kterou jsme naplnili v příkazu výše.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nyní omezíme cílový počet vyhrazených výpočetních uzlů na maximum 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Nakonec zajistíme, aby se uzly neodebraly, dokud se jejich úkoly nedokončí.

```
$NodeDeallocationOption = taskcompletion;
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
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> Pokud se rozhodnete, můžete do řetězců vzorců zahrnout jak komentáře, tak i zalomení řádků. Upozorňujeme také, že chybějící středníky mohou způsobit chyby vyhodnocení.

## <a name="automatic-scaling-interval"></a>Interval automatického škálování

Služba Batch standardně upravuje velikost fondu podle vzorce automatického škálování každých 15 minut. Tento interval lze konfigurovat pomocí následujících vlastností fondu:

- [CloudPool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Minimální interval je pět minut a maximální hodnota je 168 hodin. Pokud je zadaný interval mimo tento rozsah, služba Batch vrátí chybovou chybu žádosti (400).

> [!NOTE]
> Automatické škálování není v současné době určeno k reakci na změny za méně než minutu, ale místo toho je určeno k postupnému přizpůsobení velikosti fondu při spuštění úlohy.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Vytvoření fondu s povoleným autoškálou pomocí sad SDK služby Batch

Automatické škálování fondu se dá nakonfigurovat pomocí kterékoli sady [SDK Batch](batch-apis-tools.md#azure-accounts-for-batch-development), rutiny [Batch REST API](/rest/api/batchservice/) [Batch PowerShellu](batch-powershell-cmdlets-get-started.md)a rozhraní příkazového [řádku Batch](batch-cli-get-started.md). V této části vidíte příklady pro .NET i Python.

### <a name="net"></a>.NET

Pokud chcete vytvořit fond s povoleným automatickým škálováním v .NET, postupujte podle těchto kroků:

1. Vytvořte fond pomocí [BatchClient. PoolOperations. CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Nastavte vlastnost [CloudPool. AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) na `true` .
1. Nastavte vlastnost [CloudPool. AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) vzorcem automatického škálování.
1. Volitelné Nastavte vlastnost [CloudPool. AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (výchozí hodnota je 15 minut).
1. Potvrďte fond pomocí [CloudPool. Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) nebo [commitasync vyvolá výjimka](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

Následující příklad vytvoří fond s povoleným autoškálou v .NET. Vzorec automatického škálování fondu nastavuje cílový počet vyhrazených uzlů na hodnotu 5 v pondělí a 1 každý druhý den v týdnu. [Interval automatického škálování](#automatic-scaling-interval) je nastavený na 30 minut. V tomto článku a dalších fragmentech kódu jazyka C# v tomto článku `myBatchClient` je správně inicializovaná instance třídy [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) .

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
> Při vytváření fondu s povoleným autoškálou, nezadávejte parametr _targetDedicatedNodes_ ani parametr _TargetLowPriorityNodes_ pro volání **CreatePool**. Místo toho zadejte vlastnosti **AutoScaleEnabled** a **AutoScaleFormula** ve fondu. Hodnoty pro tyto vlastnosti určují cílové číslo každého typu uzlu.
>
> Chcete-li ručně změnit velikost fondu s povoleným automatickým škálováním (například pomocí [BatchClient. PoolOperations. ResizePoolAsync](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)), musíte nejprve zakázat automatické škálování ve fondu a následně změnit jeho velikost.

### <a name="python"></a>Python

Vytvoření fondu s podporou automatického škálování pomocí Python SDK:

1. Vytvořte fond a určete jeho konfiguraci.
1. Přidejte fond do klienta služby.
1. Povolí automatické škálování ve fondu se vzorem, který zapisujete.

Následující příklad znázorňuje tyto kroky.

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
> Další příklady použití sady Python SDK najdete v části [rychlý Start úložiště Pythonu pro Batch](https://github.com/Azure-Samples/batch-python-quickstart) na GitHubu.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Povolit automatické škálování u existujícího fondu

Každá sada Batch SDK nabízí způsob, jak povolit automatické škálování. Například:

- [BatchClient. PoolOperations. EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .NET)
- [Povolit automatické škálování ve fondu](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST API)

Pokud povolíte automatické škálování u existujícího fondu, pamatujte na toto:

- Pokud je ve fondu aktuálně zakázané automatické škálování, při vystavení žádosti musíte zadat platný vzorec automatického škálování. Volitelně můžete zadat interval automatického škálování. Pokud interval neurčíte, použije se výchozí hodnota 15 minut.
- Pokud je ve fondu aktuálně povoleno automatické škálování, můžete zadat nový vzorec, nový interval nebo obojí. Je nutné zadat alespoň jednu z těchto vlastností.
  - Pokud zadáte nový interval automatického škálování, zastaví se stávající plán a spustí se nový plán. Nový čas zahájení plánu je čas, kdy byl vydán požadavek na povolení automatického škálování.
  - Pokud vynecháte vzorec nebo interval automatického škálování, bude služba Batch dál používat aktuální hodnotu tohoto nastavení.

> [!NOTE]
> Pokud jste zadali hodnoty parametrů *targetDedicatedNodes* nebo *targetLowPriorityNodes* metody **CreatePool** při vytváření fondu v rozhraní .NET nebo u srovnatelných parametrů v jiném jazyce, budou tyto hodnoty při vyhodnocení vzorce automatického škálování ignorovány.

Tento příklad v jazyce C# používá knihovnu [Batch .NET](/dotnet/api/microsoft.azure.batch) k povolení automatického škálování v existujícím fondu.

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

Chcete-li aktualizovat vzorec v existujícím fondu s povoleným automatickým škálováním, zavolejte operaci, aby bylo možné znovu povolit automatické škálování pomocí nového vzorce. Pokud je například automatické škálování již povoleno `myexistingpool` při spuštění následujícího kódu .NET, je jeho vzorec automatického škálování nahrazen obsahem `myNewFormula` .

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

Vzorec můžete vyhodnotit před jeho použitím v rámci fondu. To vám umožní otestovat výsledky vzorce před jeho vložením do produkčního prostředí.

Než budete moct vyhodnotit vzorec automatického škálování, musíte nejdřív povolit automatické škálování ve fondu s platným vzorcem, jako je například vzorec s jedním řádkem `$TargetDedicatedNodes = 0` . Pak použijte jednu z následujících hodnot pro vyhodnocení vzorce, který chcete testovat:

- [BatchClient. PoolOperations. EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) nebo [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Tyto metody služby Batch .NET vyžadují ID existujícího fondu a řetězec obsahující vzorec automatického škálování, který se má vyhodnotit.

- [Vyhodnocení vzorce automatického škálování](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    V této REST API žádosti zadejte ID fondu v identifikátoru URI a vzorec automatického škálování v elementu *autoScaleFormula* textu požadavku. Odezva operace obsahuje všechny informace o chybě, které se mohou vztahovat ke vzorci.

Tento příklad [dávky .NET](/dotnet/api/microsoft.azure.batch) vyhodnocuje vzorec automatického škálování. Pokud fond už nepoužívá automatické škálování, doporučujeme ho nejdřív povolit.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
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

Abyste měli jistotu, že vzorec funguje podle očekávání, doporučujeme, abyste pravidelně kontrolovali výsledky spuštění automatického škálování, které tato dávková operace provádí ve vašem fondu. Chcete-li tak učinit, Získejte (nebo aktualizujte) odkaz na fond a pak zkontrolujte vlastnosti posledního spuštění automatického škálování.

Ve službě Batch .NET má vlastnost [CloudPool. AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) několik vlastností, které poskytují informace o nejnovějším automatickém spuštění škálování provedené ve fondu:

- [AutoScaleRun. timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun. Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun. Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

V REST API vrátí informace o žádosti [o fond](/rest/api/batchservice/get-information-about-a-pool) informace o fondu, který obsahuje nejnovější informace o spuštění automatického škálování ve vlastnosti [autoScaleRun](/rest/api/batchservice/get-information-about-a-pool) .

Následující příklad jazyka C# používá knihovnu Batch .NET k tisku informací o posledním spuštění automatického škálování na _myPool_ fondu.

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Ukázkový výstup z předchozího příkladu:

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

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Získat historii spuštění automatického škálování pomocí událostí automatického škálování fondu
Pomocí dotazu na [PoolAutoScaleEvent](batch-pool-autoscale-event.md)můžete také zaškrtnout historii automatického škálování. Služba Batch generuje tuto událost za účelem zaznamenávání každého výskytu vyhodnocování a provádění vzorce automatického škálování, což může být užitečné při řešení potenciálních problémů.

Ukázková událost pro PoolAutoScaleEvent:
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
```

## <a name="example-autoscale-formulas"></a>Ukázka vzorců automatického škálování

Pojďme se podívat na několik vzorců, které ukazují různé způsoby, jak upravit množství výpočetních prostředků ve fondu.

### <a name="example-1-time-based-adjustment"></a>Příklad 1: úpravy založené na čase

Předpokládejme, že chcete upravit velikost fondu na základě dne v týdnu a denního času. Tento příklad ukazuje, jak odpovídajícím způsobem zvýšit nebo snížit počet uzlů ve fondu.

Vzorec nejprve získá aktuální čas. Pokud se jedná o pracovní den (1-5) a v pracovní době (od 8 do 6 ODP), je velikost cílového fondu nastavená na 20 uzlů. V opačném případě je nastaveno na 10 uzlů.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` dá se upravit tak, aby odráželo vaše místní časové pásmo přidáním `time()` do produktu `TimeZoneInterval_Hour` a vašeho posunu UTC. Například použijte `$curTime = time() + (-6 * TimeInterval_Hour);` pro horská oblast (MDT) (letní čas). Mějte na paměti, že posun by musel být upraven na začátku a na konci letního času (Pokud je k dispozici).

### <a name="example-2-task-based-adjustment"></a>Příklad 2: úpravy založené na úlohách

V tomto příkladu v jazyce C# se velikost fondu upraví na základě počtu úloh ve frontě. Do řetězců vzorců jsme zahrnuli jak komentáře, tak i zalomení řádků.

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

### <a name="example-3-accounting-for-parallel-tasks"></a>Příklad 3: monitorování účtů pro paralelní úlohy

Tento příklad v jazyce C# upravuje velikost fondu na základě počtu úkolů. Tento vzorec také bere v úvahu hodnotu [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode) , která byla pro fond nastavena. Tento přístup je užitečný v situacích, kdy je na vašem fondu povolených [paralelního spouštění úkolů](batch-parallel-node-tasks.md) .

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Příklad 4: nastavení počáteční velikosti fondu

Tento příklad ukazuje příklad C# s vzorcem automatického škálování, který nastaví velikost fondu na zadaný počet uzlů pro počáteční časové období. Pak upraví velikost fondu na základě počtu spuštěných a aktivních úloh.

Konkrétně tento vzorec provede následující akce:

- Nastaví počáteční velikost fondu na čtyři uzly.
- Neupravuje velikost fondu během prvních 10 minut životního cyklu fondu.
- Po 10 minutách získá maximální hodnotu počtu spuštěných a aktivních úloh během posledních 60 minut.
  - Pokud jsou obě hodnoty 0 (což značí, že v posledních 60 minutách nebyly spuštěné nebo aktivní žádné úlohy), je velikost fondu nastavená na 0.
  - Pokud je jedna z hodnot větší než nula, žádná změna se neprovádí.

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

- Naučte se [spouštět více úloh současně na výpočetních uzlech ve fondu](batch-parallel-node-tasks.md). Společně s automatickým škálováním můžete díky tomu snížit dobu trvání úloh u některých úloh a ušetřit tak peníze.
- Naučte se [efektivně zadávat dotazy na službu Azure Batch](batch-efficient-list-queries.md) pro další efektivitu.
