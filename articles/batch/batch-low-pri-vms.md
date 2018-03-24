---
title: Spuštění úloh služby Azure Batch na virtuálních počítačích nákladově efektivní nízkou prioritu | Microsoft Docs
description: Zjistěte, jak zřídit virtuální počítače s nízkou prioritou snížit náklady na úloh služby Azure Batch.
services: batch
author: mscurrell
manager: timlt
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.openlocfilehash: 68240e29429b4c6321e8627b62ad65ce7ecb468e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="use-low-priority-vms-with-batch"></a>Pomocí služby Batch použijte virtuální počítače s nízkou prioritou

Azure Batch nabízí nízkou prioritu virtuální počítače (VM) k snížit náklady na úloh služby Batch. Virtuální počítače s nízkou prioritou zkontrolujte nové typy dávky úlohy je možné povolením velké množství výpočetního výkonu, které má být použit pro velmi nízkou cenu.
 
Virtuální počítače s nízkou prioritou využít výhod kapacitní v Azure. Když zadáte nízkou prioritu virtuálních počítačů ve fondech, Azure Batch můžete použít tento přebytek, pokud je k dispozici.
 
Kompromis pro používání virtuálních počítačů s nízkou prioritou je, že tyto virtuální počítače nemusí být k dispozici přidělování, nebo může být zrušené kdykoli, v závislosti na dostupné kapacity. Z toho důvodu jsou nejvhodnější pro některé typy úloh virtuálních počítačů s nízkou prioritou. Použijte virtuální počítače nízkou prioritu pro batch a asynchronní zpracování úloh, kde čas dokončení úlohy je flexibilní a práce je distribuován do mnoha virtuálních počítačů.
 
Virtuální počítače s nízkou prioritou jsou nabízena za výrazně sníženou cenu ve srovnání s vyhrazených virtuálních počítačích. Podrobnosti o cenách najdete v části [ceny služby Batch](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Případy použití pro virtuální počítače s nízkou prioritou

Vzhledem k vlastnostem virtuálních počítačů, nízkou prioritu, co úlohy můžete a nelze je použít? Obecně platí zpracování úloh služby batch jsou vhodné, úlohy jsou rozdělená do mnoho paralelních úloh nebo existuje mnoho úloh, které jsou škálovat na více systémů a distribuován do mnoha virtuálních počítačů.

-   Pokud chcete maximalizovat využití přebytečných kapacity v Azure, můžete škálovat vhodný úlohy.

-   Příležitostně virtuální počítače nemusí být k dispozici nebo zrušené, což má za následek menší kapacitu pro úlohy a může dojít k přerušení úloh a opakování. Proto musí být flexibilní v době, kterou můžete využít ke spuštění úlohy.

-   Úlohy se delší úloh může být ovlivněno více, pokud dojde k přerušení. Pokud se dlouho běžící, že úlohy implementovat vytváření kontrolních bodů uložit průběh jako jejich provedení, se snižuje dopad přerušení. Úlohy s kratší časy spouštění zpravidla fungují s nízkou prioritou virtuálních počítačů, protože dopad přerušení je mnohem menší.

-   Dlouho běžící úloh MPI, které využívají víc virtuálních počítačů nejsou skvěle hodí pro použití virtuálních počítačů nízkou prioritu, protože jeden zrušené virtuální počítač může vést k celou museli znovu spustit úlohu.

Některé příklady případy použití zpracování dávky dobře vhodné použít nízkou prioritu virtuální počítače jsou:

-   **Vývoj a testování**: zejména, pokud jsou vyvíjených rozsáhlé řešení, výrazné úspory může být dosaženo. Všechny typy testování může přinést výhody, ale ve velkém měřítku zátěžové testování a testování regrese jsou skvělý používá.

-   **Dodávání kapacity na vyžádání**: virtuální počítače s nízkou prioritou lze použít k doplnění regular vyhrazených virtuálních počítačích – Pokud je k dispozici, můžete škálovat a proto dokončení rychlejší nižší náklady úlohy; Pokud není k dispozici, je použito účaří vyhrazených virtuálních počítačích zůstává k dispozici .

-   **Čas spuštění úlohy flexibilní**: Pokud je flexibilitu při čas úlohy mít k dokončení, pak lze tolerovat potenciální vyřazuje kapacity; ale s přidáním virtuálních počítačů nízkou prioritu úlohy často spustit rychlejší a nižší náklady.

Fondy batch lze nakonfigurovat k využívání virtuálních počítačů nízkou prioritu několika různými způsoby v závislosti na flexibilitu v čase spuštění úlohy:

-   Virtuální počítače s nízkou prioritou lze použít pouze ve fondu. V takovém případě Batch obnoví všechny preempted kapacity, pokud je k dispozici. Tato konfigurace je nejlevnější způsob k provedení úlohy se používají pouze nízkou prioritu virtuálních počítačů.

-   Virtuální počítače s nízkou prioritou lze použít ve spojení s pevnou směrného plánu vyhrazených virtuálních počítačů. Pevný počet vyhrazených virtuálních počítačích zajistí, že je vždy některé kapacity zachovat úlohy pokročíte.

-   Může být dynamické směs vyhrazené a nízkou prioritu virtuálních počítačů, tak, aby virtuální počítače levnějších nízkou prioritu se používají výhradně, pokud je k dispozici, ale jsou vyžádání škálovat za cenu úplné vyhrazených virtuálních počítačích. Tato konfigurace zajišťuje minimální množství kapacity zachovat úlohy pokročíte k dispozici.

## <a name="batch-support-for-low-priority-vms"></a>Batch podporu pro virtuální počítače s nízkou prioritou

Azure Batch poskytuje několik možností, které usnadňují spotřebovávají a těžit z virtuálních počítačů nízkou prioritu:

-   Fondy batch může obsahovat vyhrazených virtuálních počítačích a virtuální počítače s nízkou prioritou. Počet jednotlivých typů virtuálních počítačů lze zadat, pokud fond je vytvořené nebo změněné kdykoli pro existující fond, pomocí operace změny velikosti explicitní nebo pomocí automatické škálování. Odeslání úlohy a úlohy může zůstat beze změny, bez ohledu na typy virtuálních počítačů ve fondu. Můžete také nakonfigurovat fond úplně použít ke spuštění úloh levné jako možný, ale otočení až vyhrazených virtuálních počítačích, pokud je kapacita klesne pod minimální prahové hodnoty, aby úlohy spuštěné virtuální počítače s nízkou prioritou.

-   Fondy batch automaticky hledat cílový počet virtuálních počítačů nízkou prioritu. Pokud přerušené virtuální počítače, pokusí Batch nahradit ztraceny kapacitu a vrátíte se k cíli.

-   Když úlohy jsou přerušení, Batch zjistí a automaticky requeues úlohy spustit znovu.

-   Virtuální počítače s nízkou prioritou mají kvóty samostatné virtuální procesor, který se liší od pro vyhrazených virtuálních počítačích. 
    Kvótu pro virtuální počítače s nízkou prioritou je vyšší, než se kvóty pro vyhrazených virtuálních počítačích, protože virtuální počítače s nízkou prioritou nižší náklady. Další informace najdete v tématu [Batch, kvóty a omezení služby](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Virtuální počítače s nízkou prioritou nejsou aktuálně podporovány pro účty Batch vytvořené v [režim předplatné uživatele](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Vytváření a aktualizaci fondy

Fondu služby Batch může obsahovat vyhrazené a nízkou prioritu virtuálních počítačů (také označované jako výpočetní uzly). Pro vyhrazené i nízkou prioritu virtuální počítače můžete nastavit cílovým počtem výpočetních uzlů. Cílový počet uzlů určuje počet virtuálních počítačů, které chcete mít ve fondu.

Například vytvoření fondu pomocí služby Azure cloud virtuálních počítačů s cílem 5 vyhrazené virtuální počítače a 20 virtuálních počítačů nízkou prioritu:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4") // WS 2012 R2
);
```

Vytvoření fondu pomocí virtuální počítače Azure (v tomto případě virtuální počítače s Linuxem) s cílem 5 vyhrazené virtuální počítače a 20 virtuálních počítačů nízkou prioritu:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04.0-LTS",
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

Aktuální počet uzlů můžete získat pro vyhrazené i nízkou prioritu virtuální počítače:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Uzly fondu mají vlastnost označující, zda je uzel vyhrazené nebo nízkou prioritu virtuálního počítače:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Při přerušené jeden nebo více uzlů ve fondu, operaci seznamu uzlů ve fondu stále vrátí tyto uzly. Aktuální počet uzlů nízkou prioritu zůstává beze změny, ale tyto uzly mají jejich stav nastavit na **přepnuto** stavu. Batch, pokusí se najít nahrazení virtuálních počítačů a v případě úspěšného uzly projít **vytváření** a potom **počáteční** stavy poté jsou dostupné pro provádění úkolů, stejně jako nové uzly.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Škálování fond, který obsahuje virtuální počítače s nízkou prioritou

Jako s fondy výhradně skládající se z vyhrazených virtuálních počítačích, je možné škálovat fond, který obsahuje virtuální počítače s nízkou prioritou, voláním metody změny velikosti nebo pomocí automatického škálování.

Operace změny velikosti fondu trvá druhý volitelný parametr, který aktualizuje hodnotu **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Vzorec škálování fondu podporuje virtuální počítače s nízkou prioritou následujícím způsobem:

-   Můžete získat nebo nastavit hodnotu proměnné definované služby **$TargetLowPriorityNodes**.

-   Můžete získat hodnoty proměnné definované služby **$CurrentLowPriorityNodes**.

-   Můžete získat hodnoty proměnné definované služby **$PreemptedNodeCount**. 
    Tato proměnná vrátí počet uzlů v preempted stavu a umožňuje škálování nahoru nebo dolů počet vyhrazených uzlů, v závislosti na počtu zrušené uzlů, které jsou k dispozici.

## <a name="jobs-and-tasks"></a>Úlohy a úkoly

Úlohy a úkoly vyžadují málo další konfiguraci pro uzly nízkou prioritu; podporuje se jen vypadá takto:

-   Vlastnost JobManagerTask úlohy má novou vlastnost **AllowLowPriorityNode**. 
    Pokud tato vlastnost hodnotu true, úkolu Správce úloh bude naplánována na vyhrazené nebo nízkou prioritu uzlu. Pokud je tato vlastnost hodnotu false, úkolu Správce úloh je naplánováno na vyhrazené uzel.

-   [Proměnnou prostředí](batch-compute-node-environment-variables.md) je k dispozici pro aplikace úkolů, aby mohla určit, zda je spuštěn na nízkou prioritu, nebo vyhrazený uzlu. Proměnná prostředí je AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Zpracování přerušení

Virtuální počítače může být někdy zrušené; Když se stane přerušování, Batch provede následující akce:

-   Preempted virtuální počítače mají jejich stav, aktualizovat, aby **přepnuto**.
-   Pokud úlohy byly spuštěné na virtuálních počítačích zrušené uzlu, jsou tyto úlohy zařazena a spusťte znovu.
-   Virtuální počítač je efektivně odstranit, vede ke ztrátě všech dat uložených místně na virtuálním počítači.
-   Fond se průběžně pokusí kontaktovat cílový počet nízkou prioritu uzlů, které jsou k dispozici. Když se najde náhradní kapacity, zachovat jejich ID uzlu, ale jsou opětovně inicializovány, projít **vytváření** a **počáteční** stavy předtím, než jsou k dispozici pro plánování úkolů.
-   Přerušování počty jsou k dispozici jako metrika na portálu Azure.

## <a name="metrics"></a>Metriky

Jsou k dispozici v nové metriky [portál Azure](https://portal.azure.com) pro uzly nízkou prioritu. Jsou tyto metriky:

- Počet uzlů s nízkou prioritou
- Počet jader s nízkou prioritou 
- Zrušené počet uzlů

Chcete-li zobrazit metriky na portálu Azure:

1. Přejděte na svůj účet Batch na portálu a zobrazit nastavení vašeho účtu Batch.
2. Vyberte **metriky** z **monitorování** části.
3. Vybrat metriky, které mají **dostupné metriky** seznamu.

![Metriky pro uzly s nízkou prioritou](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Další postup

* Přečtěte si téma [Přehled funkcí Batch pro vývojáře](batch-api-basics.md), kde jsou základní informace pro každého, kdo se připravuje použít Batch. Článek obsahuje podrobné informace o prostředcích služby Batch, jako jsou fondy, uzly a úlohy, a mnoha funkcích rozhraní API, které můžete použít při vytváření aplikace Batch.
* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
