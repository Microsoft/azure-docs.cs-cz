---
title: Spouštění úloh na nákladově efektivních virtuálních počítačích s nízkou prioritou
description: Naučte se zřizovat virtuální počítače s nízkou prioritou a snížit tak náklady na Azure Batch úlohy.
author: mscurrell
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: seodec18
ms.openlocfilehash: bd5b73cf55110985a2e7eecbc161c77ca6d645cb
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568451"
---
# <a name="use-low-priority-vms-with-batch"></a>Použití virtuálních počítačů s nízkou prioritou se službou Batch

Azure Batch nabízí virtuálním počítačům s nízkou prioritou snížit náklady na úlohy Batch. Virtuální počítače s nízkou prioritou vytvářejí nové typy úloh služby Batch, a to díky tomu, že umožňují použití velkého množství výpočetního výkonu pro velmi nízké náklady.

Virtuální počítače s nízkou prioritou využívají nadbytečné kapacity v Azure. Když ve fondech zadáte virtuální počítače s nízkou prioritou, Azure Batch můžou tento přebytečný přístup použít, pokud je k dispozici.

Kompromisy pro použití virtuálních počítačů s nízkou prioritou je, že tyto virtuální počítače nemusí být vždy k dispozici, aby je bylo možné přidělit, nebo mohou být v závislosti na dostupné kapacitě přerušeny kdykoli. Z tohoto důvodu jsou virtuální počítače s nízkou prioritou vhodné pro určité typy úloh. Používejte virtuální počítače s nízkou prioritou pro úlohy dávkového a asynchronního zpracování, kde je čas dokončení úlohy flexibilní a práce se distribuuje napříč mnoha virtuálními počítači.

Virtuální počítače s nízkou prioritou se v porovnání s vyhrazenými virtuálními počítači nabízejí výrazně snížené ceny. Podrobnosti o cenách najdete v tématu [ceny služby Batch](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> Virtuální počítače s [přímým](https://azure.microsoft.com/pricing/spot/) výskytem jsou teď dostupné pro [virtuální počítače s jednou instancí](../virtual-machines/spot-vms.md) a služby [VM Scale Sets](../virtual-machine-scale-sets/use-spot.md). Virtuální počítače s nízkou prioritou představují vývoj virtuálních počítačů s nízkou prioritou, ale liší se tím, že se ceny můžou lišit a volitelná maximální cena se dá nastavit při přidělování virtuálních počítačů na místě.
>
> Fondy Azure Batch začnou podporovat virtuální počítače s plochou během několika měsíců, které jsou všeobecně dostupné, s novými verzemi [rozhraní API a nástrojů služby Batch](./batch-apis-tools.md). Jakmile bude k dispozici podpora virtuálních počítačů, virtuální počítače s nízkou prioritou budou zastaralé – budou i nadále podporovány pomocí současných rozhraní API a verzí nástrojů po dobu nejméně 12 měsíců, aby bylo umožněno dostatek času pro migraci na virtuální počítače. 
>
> Virtuální počítače s přímým odkazem se pro fondy [konfigurací cloudových služeb](/rest/api/batchservice/pool/add#cloudserviceconfiguration) nepodporují. Pokud chcete použít virtuální počítače s plochou, bude nutné, aby fondy cloudových služeb byly migrovány do fondů [Konfigurace virtuálních počítačů](/rest/api/batchservice/pool/add#virtualmachineconfiguration) .

## <a name="use-cases-for-low-priority-vms"></a>Případy použití pro virtuální počítače s nízkou prioritou

S ohledem na charakteristiky virtuálních počítačů s nízkou prioritou, které úlohy můžou a nemůžou používat? Obecně platí, že úlohy dávkového zpracování jsou vhodné, protože úlohy jsou rozdělené do mnoha paralelních úkolů nebo jsou v mnoha úlohách škálované a distribuované napříč mnoha virtuálními počítači.

-   Aby bylo možné maximalizovat využívání nadbytečné kapacity v Azure, můžete škálovat vhodné úlohy.

-   Občas virtuální počítače nemusí být k dispozici nebo jsou přerušeny, což má za následek sníženou kapacitu pro úlohy a může vést k přerušení a spuštění úloh. Úlohy musí být proto flexibilní v době, kdy mohou být spuštěny.

-   U úloh s delšími úkoly může být ovlivněno více, pokud je přerušeno. Pokud dlouhodobě běžící úlohy implementují kontrolní body, aby při jejich provádění ušetřily průběh, je dopad přerušení snížený. Úlohy s kratší dobou běhu nejlépe fungují s virtuálními počítači s nízkou prioritou, protože dopad přerušení je mnohem menší.

-   Dlouhotrvající MPI úlohy, které využívají více virtuálních počítačů, nejsou vhodné pro použití virtuálních počítačů s nízkou prioritou, protože jeden zrušený virtuální počítač může vést k celé úloze, která se musí spustit znovu.

Některé příklady případů použití dávkového zpracování, vhodné pro použití virtuálních počítačů s nízkou prioritou:

-   **Vývoj a testování**: zejména v případě vývoje rozsáhlých řešení je možné realizovat významné úspory. Všechny typy testování můžou využívat výhod, ale rozsáhlé zátěžové testování a regresní testování jsou skvělé.

-   **Doplňování kapacity na vyžádání**: virtuální počítače s nízkou prioritou se dají použít k doplnění běžných vyhrazených virtuálních počítačů – Pokud jsou dostupné, můžou se úlohy škálovat, a proto dokončí rychleji, aby se snížily náklady. Pokud není k dispozici, pak bude k dispozici standardní hodnota vyhrazených virtuálních počítačů.

-   **Flexibilní doba provádění úlohy**: Pokud je potřeba, aby se úlohy dokončily, může dojít k tolerování potenciálního poklesu kapacity. Nicméně při přidávání úloh virtuálních počítačů s nízkou prioritou často běží rychleji a za nižší náklady.

Fondy Batch se dají nakonfigurovat tak, aby používaly virtuální počítače s nízkou prioritou, a to v závislosti na flexibilitě v době spuštění úlohy:

-   Virtuální počítače s nízkou prioritou se můžou používat jedině ve fondu. V tomto případě bude dávka obnovovat jakoukoli nedostupnou kapacitu, pokud je k dispozici. Tato konfigurace představuje nejlevnější způsob, jak spouštět úlohy, protože se používají jenom virtuální počítače s nízkou prioritou.

-   Virtuální počítače s nízkou prioritou se dají použít ve spojení s pevným směrným plánem vyhrazených virtuálních počítačů. Pevný počet vyhrazených virtuálních počítačů zajišťuje, že existuje vždy nějaká kapacita, aby bylo možné udržet průběh úloh.

-   Může se jednat o dynamickou kombinaci vyhrazených virtuálních počítačů s nízkou prioritou, takže virtuální počítače s nízkou prioritou se použijí jenom v případě, že jsou k dispozici, ale plně náročné vyhrazené virtuální počítače se v případě potřeby škálují. Tato konfigurace zajišťuje minimální dostupnou kapacitu, aby se úlohy udržovaly v průběhu.

## <a name="batch-support-for-low-priority-vms"></a>Podpora služby Batch pro virtuální počítače s nízkou prioritou

Azure Batch poskytuje několik funkcí, které usnadňují využívání virtuálních počítačů s nízkou prioritou a jejich výhody:

-   Fondy dávek můžou obsahovat vyhrazené virtuální počítače i virtuální počítače s nízkou prioritou. Počet jednotlivých typů virtuálních počítačů, které se dají zadat, když se fond vytvoří nebo změní kdykoli pro existující fond, pomocí operace explicitní změny velikosti nebo automatického škálování. Odeslání úlohy a úkolu může zůstat beze změny bez ohledu na typy virtuálních počítačů ve fondu. Fond můžete také nakonfigurovat tak, aby plně používal virtuální počítače s nízkou prioritou ke spouštění úloh co nejmenším možným způsobem, ale využívaly vyhrazené virtuální počítače v případě, že kapacita klesne pod minimální prahovou hodnotu, aby se úlohy udržovaly spuštěné.

-   Fondy Batch automaticky hledají cílový počet virtuálních počítačů s nízkou prioritou. Pokud dojde k přerušení virtuálních počítačů, potom se Batch pokusí nahradit ztracenou kapacitu a vrátit se k cíli.

-   Po přerušení úlohy Batch detekuje a automaticky znovu zařadí úlohy, které se mají znovu spustit.

-   Virtuální počítače s nízkou prioritou mají samostatnou vCPU kvótu, která se liší od jedné pro vyhrazené virtuální počítače. 
    Kvóta pro virtuální počítače s nízkou prioritou je vyšší než kvóta vyhrazených virtuálních počítačů, protože virtuální počítače s nízkou prioritou jsou levnější. Další informace najdete v tématu [kvóty a omezení služby Batch](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Virtuální počítače s nízkou prioritou se v současné době nepodporují pro účty Batch vytvořené v [režimu předplatného uživatele](accounts.md).

## <a name="create-and-update-pools"></a>Vytváření a aktualizace fondů

Fond dávek může obsahovat vyhrazené virtuální počítače i virtuální počítače s nízkou prioritou (označované také jako výpočetní uzly). Můžete nastavit cílový počet výpočetních uzlů pro virtuální počítače s nízkou prioritou. Cílový počet uzlů určuje počet virtuálních počítačů, které mají být ve fondu.

Například vytvoření fondu pomocí virtuálních počítačů cloudových služeb Azure s cílem 5 vyhrazených virtuálních počítačů a 20 virtuálních počítačů s nízkou prioritou:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Vytvoření fondu pomocí virtuálních počítačů Azure (v tomto případě virtuálních počítačů se systémem Linux) s cílem 5 vyhrazených virtuálních počítačů a 20 virtuálních počítačů s nízkou prioritou:

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

Aktuální počet uzlů pro virtuální počítače s nízkou prioritou můžete získat:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Uzly fondu mají vlastnost, která určuje, jestli je uzel vyhrazený virtuální počítač s nízkou prioritou:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

V případě fondů konfigurace virtuálních počítačů se při přerušení jednoho nebo více uzlů operace výpisu uzlů ve fondu pořád vrátí do těchto uzlů. Aktuální počet uzlů s nízkou prioritou zůstane beze změny, ale u těchto uzlů je stav nastaven na přerušený **stav.** Batch se pokusí najít náhradní virtuální počítače a v případě úspěchu uzly procházejí **vytvořením** a následným **spuštěním** stavů, než se stane k dispozici pro spuštění úlohy, stejně jako u nových uzlů.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Škálování fondu obsahujícího virtuální počítače s nízkou prioritou

Stejně jako u fondů, které se skládají výhradně z vyhrazených virtuálních počítačů, je možné škálovat fond obsahující virtuální počítače s nízkou prioritou voláním metody změny velikosti nebo pomocí automatického škálování.

Operace změny velikosti fondu má druhý volitelný parametr, který aktualizuje hodnotu **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Vzorec automatického škálování fondu podporuje virtuální počítače s nízkou prioritou následujícím způsobem:

-   Můžete získat nebo nastavit hodnotu proměnné definované službou **$TargetLowPriorityNodes**.

-   Můžete získat hodnotu proměnné definované službou **$CurrentLowPriorityNodes**.

-   Můžete získat hodnotu proměnné definované službou **$PreemptedNodeCount**. 
    Tato proměnná vrátí počet uzlů v zastaveném stavu a umožňuje horizontální navýšení nebo snížení kapacity počtu vyhrazených uzlů v závislosti na počtu nedostupných uzlů, které nejsou k dispozici.

## <a name="jobs-and-tasks"></a>Úlohy a úkoly

Úlohy a úlohy vyžadují pro uzly s nízkou prioritou trochu další konfiguraci. jediná podpora je následující:

-   Vlastnost JobManagerTask úlohy má novou vlastnost **AllowLowPriorityNode**. 
    Pokud má tato vlastnost hodnotu true, může být úkol správce úloh naplánován buď na vyhrazený, nebo na uzlu s nízkou prioritou. Pokud je tato vlastnost false, je úkol správce úloh naplánován pouze na vyhrazený uzel.

-   [Proměnná prostředí](batch-compute-node-environment-variables.md) je k dispozici pro aplikaci úkolu, aby mohla zjistit, jestli je spuštěná v uzlu s nízkou prioritou nebo vyhrazeným uzlem. Proměnná prostředí je AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Přerušení zpracování

Virtuální počítače se občas můžou považovat za příležitostné přerušení. Pokud k tomu dojde, úlohy, které byly spuštěny na virtuálních počítačích s přerušeným uzlem, se znovu zařadí do fronty a spustí se znovu.

V případě fondů konfigurace virtuálních počítačů Batch provede také následující akce:

-   Zrušené virtuální **počítače mají stav aktualizace na**přerušeno.
-   Virtuální počítač se efektivně odstraní, což vede ke ztrátě všech dat uložených místně na VIRTUÁLNÍm počítači.
-   Fond se neustále pokouší spojit s cílovým počtem dostupných uzlů s nízkou prioritou. Když se najde náhradní kapacita, uzly zachovají jejich ID, ale budou se znovu inicializovat, a to tak, že procházejí **vytvořením** a **spuštěním** stavů, než budou k dispozici pro plánování úloh.
-   Počty přerušení jsou k dispozici jako metrika v Azure Portal.

## <a name="metrics"></a>Metriky

Nové metriky jsou k dispozici v [Azure Portal](https://portal.azure.com) pro uzly s nízkou prioritou. Tyto metriky jsou:

- Počet uzlů s nízkou prioritou
- Počet jader s nízkou prioritou
- Počet zrušených uzlů

Chcete-li zobrazit metriky v Azure Portal:

1. Na portálu přejděte na účet Batch a zobrazte si nastavení účtu Batch.
2. V části **monitorování** vyberte **metriky** .
3. V seznamu **dostupné metriky** vyberte metriky, které si přejete.

![Snímek obrazovky znázorňující výběr metriky pro uzly s nízkou prioritou](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
- Začněte naplánování přesunu z virtuálních počítačů s nízkou prioritou na místo virtuálních počítačů. Pokud používáte virtuální počítače s nízkou prioritou s fondy **konfigurace cloudové služby** , naplánujte přesun na fondy **Konfigurace virtuálních počítačů** .
