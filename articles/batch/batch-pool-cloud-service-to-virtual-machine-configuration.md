---
title: Migrace konfigurace fondu Batch z Cloud Services na Virtual Machines
description: Naučte se aktualizovat konfiguraci fondu na nejnovější a doporučenou konfiguraci.
ms.topic: how-to
ms.date: 2/16/2021
ms.openlocfilehash: 9cbcf3864526bd8f8132f3b0f729e2d728e07bb8
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546036"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Migrace konfigurace fondu Batch z Cloud Services na Virtual Machines

Fondy Batch se dají vytvořit buď pomocí [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) , nebo [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration). ' virtualMachineConfiguration ' je doporučená konfigurace, protože podporuje všechny funkce dávky. fondy ' cloudServiceConfiguration ' nepodporují všechny funkce a nejsou plánovány žádné nové funkce.

Pokud používáte fondy ' cloudServiceConfiguration ', důrazně doporučujeme, abyste přešli na používání fondů ' virtualMachineConfiguration '. To vám umožní těžit ze všech funkcí služby Batch, jako je rozšířený [Výběr řady virtuálních počítačů](batch-pool-vm-sizes.md), virtuálních počítačů se systémem Linux, [kontejnerů](batch-docker-container-workloads.md), [Azure Resource Manager virtuální sítě](batch-virtual-network.md)a [šifrování disku uzlu](disk-encryption.md).

Tento článek popisuje, jak migrovat do ' virtualMachineConfiguration '.

## <a name="new-pools-are-required"></a>Vyžadují se nové fondy.

Existující aktivní fondy nejde aktualizovat z ' cloudServiceConfiguration ' na ' virtualMachineConfiguration ', musí být vytvořeny nové fondy. Vytváření fondů pomocí příkazu ' virtualMachineConfiguration ' je podporováno všemi rozhraními API Batch, nástroji příkazového řádku, Azure Portal a ROZHRANÍm Batch Explorer.

**Kurzy pro [.NET](tutorial-parallel-dotnet.md) a [Python](tutorial-parallel-python.md) poskytují příklady vytváření fondů pomocí příkazu ' virtualMachineConfiguration '.**

## <a name="pool-configuration-differences"></a>Rozdíly v konfiguraci fondu

Při aktualizaci konfigurace fondu byste měli zvážit toto:

- uzly fondu ' cloudServiceConfiguration ' jsou vždy operační systémy Windows, fondy ' virtualMachineConfiguration ' mohou být buď systémy Linux nebo Windows OS.
- Ve srovnání s fondy cloudServiceConfiguration mají fondy virtualMachineConfiguration bohatší sadu funkcí, jako je podpora kontejnerů, datových disků a šifrování disku.
- uzly fondu virtualMachineConfiguration využívají spravované disky s operačním systémem. [Typ spravovaného disku](../virtual-machines/disks-types.md) , který se používá pro každý uzel, závisí na velikosti virtuálního počítače zvolené pro fond. Pokud je pro fond zadaná velikost virtuálního počítače, například Standard_D2s_v3, použije se jednotka SSD služby Premium. Pokud je zadaná velikost virtuálního počítače bez s, například Standard_D2_v3, použije se standardní HDD.

   > [!IMPORTANT]
   > Stejně jako u Virtual Machines a Virtual Machine Scale Sets se na spravovaný disk s operačním systémem, který se používá pro každý uzel, účtují náklady, což je další náklady na virtuální počítače. Pro uzly cloudServiceConfiguration se neúčtují žádné náklady na disk s operačním systémem, protože disk s operačním systémem se vytváří v uzlech místní SSD.

- Časy spuštění fondu a uzlů se mohou mírně lišit mezi fondy ' cloudServiceConfiguration ' a fondy ' virtualMachineConfiguration '.

## <a name="azure-data-factory-custom-activity-pools"></a>Azure Data Factory vlastní fondy aktivit

Fondy Azure Batch se dají použít ke spouštění Data Factory vlastních aktivit. Všechny fondy ' cloudServiceConfiguration ' používané ke spouštění vlastních aktivit budou muset být odstraněny a vytvořily se nové fondy ' virtualMachineConfiguration '.

- Kanály by se měly pozastavit před odstraněním nebo vytvořením, aby se zajistilo, že nedojde k přerušení žádného spuštění.
- Stejné ID fondu se dá použít k tomu, abyste se vyhnuli změnám konfigurace propojených služeb.
- Obnoví kanály, když se vytvoří nové fondy.

Další informace o použití Azure Batch ke spouštění Data Factory vlastní aktivity:

- [Propojená služba Azure Batch](../data-factory/compute-linked-services.md#azure-batch-linked-service)
- [Vlastní aktivity v kanálu Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [konfiguracích fondu](nodes-and-pools.md#configurations).
- Přečtěte si další informace o [osvědčených postupech pro fondy](best-practices.md#pools).
- REST API odkaz na [Přidání fondu](/rest/api/batchservice/pool/add) a [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).