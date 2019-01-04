---
title: Spouštění úloh na nákladově efektivní virtuální počítače s nízkou prioritou – Azure Batch | Dokumentace Microsoftu
description: Zjistěte, jak zřizovat virtuální počítače s nízkou prioritou, abyste snížili náklady na úlohy služby Azure Batch.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.custom: seodec18
ms.openlocfilehash: 17668470be3e997c215aacc4cc2c32c80de2dd81
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546789"
---
# <a name="use-low-priority-vms-with-batch"></a>Použití virtuálních počítačů s nízkou prioritou pomocí služby Batch

Služba Azure Batch nabízí virtuální počítače s nízkou prioritou (VM), abyste snížili náklady na úlohy služby Batch. Virtuální počítače s nízkou prioritou vytvořit nové typy dávky úlohy je možné tím, že velké množství výpočetního výkonu má být použit pro velmi nízkých nákladech.
 
Virtuální počítače s nízkou prioritou využít využívají nadbytečnou kapacitu v Azure. Při zadávání virtuálních počítačů s nízkou prioritou ve fondech Azure Batch můžete použít tento přebytek, pokud je k dispozici.
 
Kompromis pro použití virtuálních počítačů s nízkou prioritou je, že tyto virtuální počítače nemusí být možné přidělit nebo může dojít ke zrušení v okamžiku, v závislosti na dostupné kapacity. Z tohoto důvodu virtuální počítače s nízkou prioritou jsou nejvhodnější pro určité druhy úloh. Použití virtuálních počítačů s nízkou prioritou pro dávkové a asynchronní zpracování úloh, kde je flexibilní čas dokončení úlohy a pro úkony distribuované na více virtuálních počítačích.
 
Virtuální počítače s nízkou prioritou nabízí za výrazně sníženou cenu ve srovnání s vyhrazených virtuálních počítačích. Podrobnosti o cenách najdete v tématu [ceny služby Batch](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Případy použití pro virtuální počítače s nízkou prioritou

Vzhledem k vlastnostem virtuálních počítačů s nízkou prioritou, co úlohy lze a nelze je použít? Obecně platí zpracování úloh služby batch skvěle hodí, úlohy se rozdělují do mnoho paralelních úloh a existuje mnoho úloh, které se škálovat na více systémů a distribuované napříč mnoha virtuálních počítačů.

-   Pokud chcete maximalizovat využití nadbytečné kapacity v Azure, můžete vhodný úlohy horizontální navýšení kapacity.

-   V některých virtuálních počítačů možná není k dispozici nebo zrušené, povede k snížení kapacity pro úlohy a může vést k přerušení úloh a opakování. Proto musí být flexibilní v době, kterou můžete využít ke spuštění úlohy.

-   Úlohy s delší úlohy může mít vliv více, pokud dojde k přerušení. Pokud dlouho běžící úlohy implementovat vytváření kontrolních bodů průběhu si uložte, protože se provedou, se snižuje dopad přerušení. Úkoly s kratší dobu provádění obvykle fungují nejlépe s virtuálními počítači s nízkou prioritou, protože dopadu přerušení je mnohem méně.

-   Dlouhotrvajících úloh MPI, které využívají více virtuálních počítačů nejsou vhodné pro použití virtuálních počítačů s nízkou prioritou, protože jeden virtuální počítač ke zrušení přidělením může vést k celé úlohy by bylo nutné znovu spustit.

Příklady případů použití dávkové zpracování a vhodné k použití virtuálních počítačů s nízkou prioritou jsou:

-   **Vývoj a testování**: Zejména pokud velkých řešeních jsou, a proto produkt můžete realizovat výraznou slevu. Můžete využívat všechny druhy testování, ale ve velkém měřítku zátěžové testování a regresní testování jsou skvělé používá.

-   **Doplnění kapacitou na vyžádání**: Virtuální počítače s nízkou prioritou lze použít k doplnění pravidelně vyhrazených virtuálních počítačů – Pokud je k dispozici, můžete škálovat a proto rychleji dokončit za nižší cenu; úlohy Pokud není k dispozici, zůstává k dispozici základní vyhrazených virtuálních počítačích.

-   **Čas spuštění úlohy flexibilní**: Pokud existuje určitá flexibilita v čase mají úlohy dokončí, pak potenciální drops kapacity může tolerovat; ale přidání virtuálních počítačů s nízkou prioritou často úloha rychleji a s nižšími náklady.

Fondy služby batch lze nakonfigurovat pro použití virtuálních počítačů s nízkou prioritou několika různými způsoby v závislosti na flexibilitu v čase spuštění úlohy:

-   Virtuální počítače s nízkou prioritou lze použít pouze v rámci fondu. V takovém případě Batch obnoví všechny preempted kapacity, pokud je k dispozici. Tato konfigurace je nejlevnější způsob, jak spouštět úlohy, jako jsou virtuální počítače jenom s nízkou prioritou.

-   Virtuální počítače s nízkou prioritou lze použít ve spojení s pevnou směrného plánu vyhrazených virtuálních počítačů. Pevný počet vyhrazených virtuálních počítačů zajistí, že budou vždy některé schopnost zachovat průběh úlohy.

-   Může být dynamické kombinaci s nízkou prioritou a vyhrazených virtuálních počítačů, tak, aby virtuální počítače levnější s nízkou prioritou se používají pouze pokud je k dispozici, ale úplné ceny vyhrazených virtuálních počítačů se v případě potřeby vertikálně navýšit. Tato konfigurace zajišťuje minimální množství kapacity k dispozici zachovat průběh úlohy.

## <a name="batch-support-for-low-priority-vms"></a>Podpora služby batch pro virtuální počítače s nízkou prioritou

Azure Batch poskytuje několik funkcí, které usnadňují využití a těžit z virtuálních počítačů s nízkou prioritou:

-   Fondy služby batch můžou obsahovat vyhrazených virtuálních počítačů a virtuálních počítačů s nízkou prioritou. Počty jednotlivých typů virtuálních počítačů lze zadat, pokud je fond vytvořen nebo změněnen kdykoli pro už existující fond, pomocí operace změny velikosti explicitní nebo použití automatického škálování. Odeslání úloh a může zůstat beze změny, bez ohledu na typy virtuálních počítačů ve fondu. Můžete také nakonfigurovat fond zcela používat virtuální počítače s nízkou prioritou ke spouštění úloh levně, ale aktivovat vyhrazených virtuálních počítačích, pokud kapacitu klesne pod minimální prahové hodnoty, aby spuštěné úlohy.

-   Fondy služby batch automaticky vyhledat cílový počet virtuálních počítačů s nízkou prioritou. Pokud zrušené virtuální počítače dávky pokusí nahradit ztracenou kapacitu a vrátíte se do cíle.

-   Přerušení úloh služby Batch rozpozná a automaticky requeues úlohy spustit znovu.

-   Virtuální počítače s nízkou prioritou mají kvótu samostatných virtuálních procesorů, které se liší od jedné vyhrazených virtuálních počítačích. 
    Kvóta pro virtuální počítače s nízkou prioritou je vyšší než kvóta pro vyhrazené virtuální počítače, protože virtuální počítače s nízkou prioritou nižší náklady. Další informace najdete v tématu [Batch, kvóty a omezení služby](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Virtuální počítače s nízkou prioritou nejsou aktuálně podporovány pro účty Batch vytvořené v [režimu předplatného uživatele](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Vytvoření a aktualizaci fondy

Fond služby Batch můžou obsahovat virtuální počítače vyhrazené a s nízkou prioritou (také označované jako výpočetní uzly). Cílový počet výpočetních uzlů můžete nastavit pro virtuální počítače vyhrazené a s nízkou prioritou. Cílový počet uzlů určuje počet virtuálních počítačů, které chcete mít ve fondu.

Například k vytvoření fondu pomocí virtuálních počítačů Azure cloudových služeb s cílem 5 vyhrazených virtuálních počítačů a 20 virtuálních počítačů s nízkou prioritou:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Vytvoření fondu pomocí virtuálních počítačů Azure (v tomto případě virtuální počítače s Linuxem) s cílem 5 vyhrazené virtuální počítače a 20 virtuálních počítačů s nízkou prioritou:

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

Pro virtuální počítače vyhrazené a s nízkou prioritou můžete získat aktuální počet uzlů:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Uzly fondu mají vlastnost umožňující označit, pokud uzel je virtuální počítač vyhrazený nebo s nízkou prioritou:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Při zrušené jeden nebo více uzlů ve fondu, vrátí operaci seznam uzlů ve fondu pořád těchto uzlů. Aktuální počet uzlů s nízkou prioritou se nezmění, ale ty uzly, mají jejich stav nastaven **přepnuto** stavu. Batch se pokusí najít náhradní virtuální počítače, a v případě úspěšného ověření uzly projít **vytváření** a potom **počáteční** státy, než se náplní k dispozici pro provádění úkolů, stejně jako nové uzly.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Škálování fondu obsahujících virtuální počítače s nízkou prioritou

Jako s fondy výhradně skládající se z vyhrazených virtuálních počítačů, je možné škálovat fond obsahující virtuální počítače s nízkou prioritou, voláním metody změny velikosti nebo použití automatického škálování.

Operace změny velikosti fondu trvá druhý volitelný parametr, který aktualizuje hodnotu **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Vzorec automatického škálování fondu podporuje virtuální počítače s nízkou prioritou následujícím způsobem:

-   Můžete získat nebo nastavit hodnotu proměnné definované službou **$TargetLowPriorityNodes**.

-   Můžete získat hodnotu proměnné definované službou **$CurrentLowPriorityNodes**.

-   Můžete získat hodnotu proměnné definované službou **$PreemptedNodeCount**. 
    Tato proměnná vrátí počet uzlů v preempted stavu a umožňuje vertikálně navyšovat nebo snižovat počet vyhrazených uzlů v závislosti na počtu ke zrušení přidělením uzly, které jsou k dispozici.

## <a name="jobs-and-tasks"></a>Úlohy a úkoly

Úlohy a úkoly vyžaduje trochu další konfigurace pro uzly s nízkou prioritou; podporují jenom vypadá takto:

-   Vlastnost JobManagerTask úlohy má nové vlastnosti **AllowLowPriorityNode**. 
    Když tato vlastnost hodnotu true, úkol Správce úloh může být naplánována na vyhrazený nebo s nízkou prioritou uzlu. Pokud je tato vlastnost hodnotu false, úkol Správce úloh je naplánováno na vyhrazených uzlů.

-   [Proměnnou prostředí](batch-compute-node-environment-variables.md) je k dispozici pro aplikaci úkolu, takže můžete určit, zda je spuštěn na uzlu s nízkou prioritou nebo vyhrazené. Proměnná prostředí je AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Přerušování zpracování

Virtuálních počítačů může někdy dojít ke zrušení přidělením; Když se stane přerušení, Batch provede následující akce:

-   Preempted virtuálních počítačů mají jejich stav aktualizuje na **přepnuto**.
-   Úkoly byly spuštěné na virtuálních počítačích ke zrušení přidělením uzel, úkoly se zařadí do fronty a spusťte znovu.
-   Virtuální počítač je skutečně odstraněn, což vede ke ztrátě všech dat uložených místně na virtuálním počítači.
-   Fond se průběžně pokusí kontaktovat cílový počet uzlů s nízkou prioritou dostupné. Po nalezení náhradní kapacity uzlů zachovat jejich ID, ale jsou opětovně inicializovány, prostřednictvím **vytváření** a **počáteční** stavy předtím, než jsou k dispozici pro plánování úloh.
-   Přerušení počty jsou k dispozici jako metriku na webu Azure Portal.

## <a name="metrics"></a>Metriky

Jsou k dispozici v nové metriky [webu Azure portal](https://portal.azure.com) pro uzly s nízkou prioritou. Tyto metriky jsou:

- Počet uzlů s nízkou prioritou
- Počet jader s nízkou prioritou 
- Ke zrušení přidělením počet uzlů

Pokud chcete zobrazit metriky na webu Azure Portal:

1. Přejděte do svého účtu Batch na portálu a zobrazit nastavení pro účet Batch.
2. Vyberte **metriky** z **monitorování** oddílu.
3. Vyberte metriky, které očekáváte od **dostupné metriky** seznamu.

![Metriky pro uzly s nízkou prioritou](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Další postup

* Přečtěte si téma [Přehled funkcí Batch pro vývojáře](batch-api-basics.md), kde jsou základní informace pro každého, kdo se připravuje použít Batch. Článek obsahuje podrobné informace o prostředcích služby Batch, jako jsou fondy, uzly a úlohy, a mnoha funkcích rozhraní API, které můžete použít při vytváření aplikace Batch.
* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
