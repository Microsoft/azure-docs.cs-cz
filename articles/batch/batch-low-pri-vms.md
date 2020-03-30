---
title: Spouštění úloh na cenově efektivních virtuálních počítačích s nízkou prioritou – Azure Batch | Dokumenty společnosti Microsoft
description: Zjistěte, jak zřídit virtuální počítače s nízkou prioritou, abyste snížili náklady na úlohy Azure Batch.
services: batch
author: mscurrell
manager: evansma
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/19/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9f4b9ed9254eaf950311dd27d5716c4681707614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053914"
---
# <a name="use-low-priority-vms-with-batch"></a>Použití virtuálních počítačů s nízkou prioritou se službou Batch

Azure Batch nabízí virtuální počítače s nízkou prioritou(VM), které snižují náklady na úlohy batch. Virtuální počítače s nízkou prioritou umožňují nové typy úloh Batch tím, že umožňují použití velkého množství výpočetního výkonu za velmi nízké náklady.
 
Virtuální počítače s nízkou prioritou využívají nadbytečnou kapacitu v Azure. Když zadáte virtuální počítače s nízkou prioritou ve fondech, Azure Batch můžete použít tento přebytek, pokud je k dispozici.
 
Kompromis pro použití virtuálních počítačů s nízkou prioritou je, že tyto virtuální počítače nemusí být k dispozici k přidělení nebo mohou být kdykoli v závislosti na dostupné kapacitě vyzývavé. Z tohoto důvodu jsou virtuální počítače s nízkou prioritou nejvhodnější pro určité typy úloh. Virtuální počítači s nízkou prioritou použijte pro dávkové a asynchronní úlohy zpracování, kde je doba dokončení úlohy flexibilní a práce je distribuována mezi mnoho virtuálních počítačů.
 
Virtuální virtuální mase s nízkou prioritou se nabízejí za výrazně sníženou cenu ve srovnání s vyhrazenými virtuálními min. Podrobnosti o cenách najdete [v tématu Dávkové ceny](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> [Virtuální virtuální aplikace spot](https://azure.microsoft.com/pricing/spot/) jsou teď dostupné pro [virtuální aplikace s jednou instancí](https://docs.microsoft.com/azure/virtual-machines/linux/spot-vms) a [škálovací sady virtuálních vod](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot). Spotové virtuální aplikace jsou evolucí virtuálních virtuálních aplikací s nízkou prioritou, ale liší se v tom, že ceny se můžou lišit a při přidělování virtuálních virtuálních aplikací spotů se dá nastavit volitelná maximální cena.
>
> Fondy Azure Batch začnou podporovat virtuální počítače Spot během několika měsíců od jejich obecně dostupnosti, s novými verzemi [dávkových rozhraní API a nástrojů](https://docs.microsoft.com/azure/batch/batch-apis-tools). Jakmile je podpora virtuálních her spot k dispozici, virtuální chods s nízkou prioritou se zastaralá – budou nadále podporovány pomocí aktuálních rozhraní API a verzí nástrojů po dobu nejméně 12 měsíců, aby byl dostatek času na migraci na virtuální chod spot. 
>
> Virtuální počítače spot nebudou podporovány pro fondy [konfigurace cloudové služby.](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) Chcete-li používat virtuální počítače Spot, fondy cloudových služeb bude nutné migrovat do fondů [konfigurace virtuálních počítačů.](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)


## <a name="use-cases-for-low-priority-vms"></a>Případy použití pro virtuální virtuální společnosti s nízkou prioritou

Vzhledem k charakteristikám virtuálních aplikací s nízkou prioritou je jaké úlohy je můžou a nemůžou používat? Obecně platí, že úlohy dávkového zpracování jsou vhodné, protože úlohy jsou rozděleny do mnoha paralelních úloh nebo existuje mnoho úloh, které jsou škálovány a distribuovány mezi mnoho virtuálních virtuálních zařízení.

-   Chcete-li maximalizovat využití nadbytečné kapacity v Azure, vhodné úlohy můžete škálovat.

-   V některých případě virtuální chodnemusí být k dispozici nebo jsou preempted, což má za následek sníženou kapacitu pro úlohy a může vést k přerušení úkolu a opakování. Pracovní místa proto musí být flexibilní v době, kterou mohou trvat běžet.

-   Úlohy s delšími úkoly mohou mít větší dopad, pokud budou přerušeny. Pokud dlouhotrvající úlohy implementovat vytváření kontrolních bodů uložit průběh při jejich provádění, pak dopad přerušení je snížena. Úkoly s kratší dobou provádění mají tendenci fungovat nejlépe s virtuálními kartami s nízkou prioritou, protože dopad přerušení je mnohem menší.

-   Dlouhotrvající úlohy MPI, které využívají více virtuálních počítačů, nejsou vhodné pro použití virtuálních počítačů s nízkou prioritou, protože jeden předem vystudovaný virtuální počítač může vést k tomu, že celá úloha bude muset znovu spustit.

Některé příklady případů použití dávkového zpracování vhodné pro použití virtuálních zařízení s nízkou prioritou jsou:

-   **Vývoj a testování**: Zejména v případě, že se vyvíjejí rozsáhlá řešení, lze dosáhnout významných úspor. Všechny typy testování mohou být přínosem, ale rozsáhlé testování zatížení a regresní testování jsou skvělé použití.

-   **Doplnění kapacity na vyžádání**: Virtuální počítači s nízkou prioritou lze použít k doplnění běžných vyhrazených virtuálních počítačů – pokud jsou k dispozici, úlohy se můžou škálovat a dokončit se tak rychleji za nižší náklady. není-li k dispozici, zůstane k dispozici základní linie vyhrazených virtuálních mk.

-   **Flexibilní doba provádění úloh**: Pokud je v době, kdy musí pracovní místa dokončit, flexibilita, může být tolerována potenciální pokles kapacity; však s přidáním úlohy virtuálních aplikací s nízkou prioritou často běží rychleji a za nižší náklady.

Fondy dávek lze nakonfigurovat tak, aby používaly virtuální počítače s nízkou prioritou několika způsoby, v závislosti na flexibilitě v době provádění úloh:

-   Virtuální virtuální ms s nízkou prioritou lze použít výhradně ve fondu. V tomto případě Batch obnoví všechny preempted kapacity, pokud jsou k dispozici. Tato konfigurace je nejlevnější způsob, jak provádět úlohy, protože se používají pouze virtuální počítače s nízkou prioritou.

-   Virtuální virtuální mase s nízkou prioritou se můžou používat ve spojení s pevným směrným plánem vyhrazených virtuálních aplikací. Pevný počet vyhrazených virtuálních zařízení zajišťuje, že je vždy určitá kapacita pro udržení průběhu úlohy.

-   Může existovat dynamická kombinace vyhrazených virtuálních aplikací s nízkou prioritou, takže levnější virtuální aplikace s nízkou prioritou se používají pouze v případě, že jsou k dispozici, ale vyhrazené virtuální virtuální hry s plnou cenou se v případě potřeby navíjejí. Tato konfigurace udržuje minimální množství kapacity k dispozici, aby úlohy postupuje.

## <a name="batch-support-for-low-priority-vms"></a>Dávková podpora pro virtuální virtuální mích s nízkou prioritou

Azure Batch poskytuje několik funkcí, které usnadňují využití a využití virtuálních počítačů s nízkou prioritou:

-   Dávkové fondy mohou obsahovat vyhrazené virtuální chody i virtuální virtuální aplikace s nízkou prioritou. Počet jednotlivých typů virtuálních počítače lze určit při vytvoření fondu nebo kdykoli změnit pro existující fond, pomocí explicitní operace změny velikosti nebo pomocí automatického škálování. Odeslání úlohy a úkolu může zůstat beze změny, bez ohledu na typy virtuálních počítačů ve fondu. Fond můžete také nakonfigurovat tak, aby zcela používal virtuální počítače s nízkou prioritou ke spouštění úloh co nejlevněji, ale pokud kapacita klesne pod minimální prahovou hodnotu, můžete spustit vyhrazené virtuální počítače, aby úlohy zůstaly spuštěné.

-   Fondy dávek automaticky vyhledávají cílový počet virtuálních aplikací s nízkou prioritou. Pokud virtuální chody jsou preempted, pak Batch pokusí nahradit ztracenou kapacitu a vrátit se k cíli.

-   Při přerušení úloh batch detekuje a automaticky znovu zařadí úlohy do fronty, aby se znovu spouštěly.

-   Virtuální počítače s nízkou prioritou mají samostatnou kvótu virtuálního procesoru, která se liší od kvóty pro vyhrazené virtuální počítače. 
    Kvóta pro virtuální chods s nízkou prioritou je vyšší než kvóta pro vyhrazené virtuální chody, protože virtuální chodů s nízkou prioritou stojí méně. Další informace naleznete v [tématu Kvóty a omezení služby Dávkové služby](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Virtuální počítačů s nízkou prioritou nejsou aktuálně podporovány pro dávkové účty vytvořené v [režimu předplatného uživatele](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Vytváření a aktualizace fondů

Fond dávek může obsahovat vyhrazené virtuální počítače i virtuální počítače s nízkou prioritou (označované také jako výpočetní uzly). Můžete nastavit cílový počet výpočetních uzlů pro vyhrazené i nízkoprioritní virtuální počítače. Cílový počet uzlů určuje počet virtuálních her, které chcete mít ve fondu.

Například k vytvoření fondu pomocí virtuálních počítačů cloudové služby Azure s cílem 5 vyhrazených virtuálních počítačů a 20 virtuálních počítačích s nízkou prioritou:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Vytvoření fondu pomocí virtuálních počítačů Azure (v tomto případě virtuálních počítačů s Linuxem) s cílem 5 vyhrazených virtuálních počítačů a 20 virtuálních počítačů s nízkou prioritou:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

Můžete získat aktuální počet uzlů pro vyhrazené i nízkoprioritní virtuální aplikace:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Uzly fondu mají vlastnost označující, pokud je uzel vyhrazený nebo s nízkou prioritou virtuálního mísa:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Pokud jeden nebo více uzlů ve fondu jsou preempted, operace uzlů seznamu ve fondu stále vrací tyto uzly. Aktuální počet uzlů s nízkou prioritou zůstane beze změny, ale tyto uzly mají svůj stav nastaven na stav **Preempted.** Batch se pokusí najít náhradní virtuální choda a v případě úspěchu uzly projít **vytváření** a potom **spuštění** stavy před tím, než bude k dispozici pro spuštění úlohy, stejně jako nové uzly.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Škálování fondu obsahujícího virtuální virtuální ms s nízkou prioritou

Stejně jako u fondů, které se skládají výhradně z vyhrazených virtuálních počítače, je možné škálovat fond obsahující virtuální počítače s nízkou prioritou voláním metody Resize nebo pomocí automatického škálování.

Operace změny velikosti fondu trvá druhý volitelný parametr, který aktualizuje hodnotu **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Vzorec automatického škálování fondu podporuje virtuální chods s nízkou prioritou takto:

-   Můžete získat nebo nastavit hodnotu proměnné definované službou **$TargetLowPriorityNodes**.

-   Můžete získat hodnotu proměnné definované službou **$CurrentLowPriorityNodes**.

-   Můžete získat hodnotu proměnné definované službou **$PreemptedNodeCount**. 
    Tato proměnná vrátí počet uzlů v preempted stavu a umožňuje vertikálně navýšit nebo snížit počet vyhrazených uzlů, v závislosti na počtu předpisovaných uzlů, které nejsou k dispozici.

## <a name="jobs-and-tasks"></a>Pracovní místa a úkoly

Úlohy a úlohy vyžadují malou dodatečnou konfiguraci pro uzly s nízkou prioritou; jediná podpora je následující:

-   Vlastnost JobManagerTask úlohy má novou vlastnost **AllowLowPriorityNode**. 
    Pokud je tato vlastnost pravdivá, úloha správce úloh může být naplánována na vyhrazeném uzlu nebo uzlu s nízkou prioritou. Pokud je tato vlastnost false, úloha správce úloh je naplánována pouze na vyhrazený uzel.

-   [Proměnná prostředí](batch-compute-node-environment-variables.md) je k dispozici pro aplikaci úlohy, aby mohla určit, zda je spuštěna na uzlu s nízkou prioritou nebo vyhrazeném uzlu. Proměnná prostředí je AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Manipulace s předkupním právem

Virtuální body mohou být občas preempted; dojde-li k preventivnímu vyprazdnění, batch provádí následující akce:

-   Předpočitatá virtuální počítačová společnost má svůj stav aktualizován na **Preempted**.
-   Pokud byly úlohy spuštěny na počítačích s předplyňovanými uzly, pak jsou tyto úlohy znovu zařazeny do fronty a znovu spuštěny.
-   Virtuální počítač se efektivně odstraní, což vede ke ztrátě všech dat uložených místně na virtuálním počítači.
-   Fond se neustále pokouší dosáhnout cílového počtu uzlů s nízkou prioritou, které jsou k dispozici. Když je nalezena náhradní kapacita, uzly zachovat jejich ID, ale jsou znovu inicializovány, prochází **vytváření** a **spuštění** stavy dříve, než jsou k dispozici pro plánování úkolů.
-   Počty preemptionu jsou k dispozici jako metrika na webu Azure Portal.

## <a name="metrics"></a>Metriky

Nové metriky jsou dostupné na [webu Azure Portal](https://portal.azure.com) pro uzly s nízkou prioritou. Jedná se o tyto metriky:

- Počet uzlů s nízkou prioritou
- Počet jader s nízkou prioritou 
- Počet předplyňovaných uzlů

Zobrazení metrik na webu Azure Portal:

1. Přejděte na portál u svého účtu Batch a zobrazte nastavení účtu Batch.
2. V části **Monitorování** **vyberte Metriky.**
3. V seznamu Dostupné metriky vyberte metriky, které si **přejete.**

![Metriky pro uzly s nízkou prioritou](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [Přehled funkcí Batch pro vývojáře](batch-api-basics.md), kde jsou základní informace pro každého, kdo se připravuje použít Batch. Článek obsahuje podrobné informace o prostředcích služby Batch, jako jsou fondy, uzly a úlohy, a mnoha funkcích rozhraní API, které můžete použít při vytváření aplikace Batch.
* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
* Začněte plánovat přechod z virtuálních aplikací s nízkou prioritou na virtuální virtuální chod spotů. Pokud používáte virtuální počítače s nízkou prioritou s fondy **konfigurace cloudové služby,** pak plánujete přejít do fondů **konfigurace virtuálního počítače.**
