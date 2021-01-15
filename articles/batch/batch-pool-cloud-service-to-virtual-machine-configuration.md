---
title: Migrace konfigurace fondu Batch z Cloud Services na Virtual Machines
description: Naučte se aktualizovat konfiguraci fondu na nejnovější a doporučenou konfiguraci.
ms.topic: how-to
ms.date: 1/6/2021
ms.openlocfilehash: d987a185efb6593fd541dd14fa74b6c4d3ca41be
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234303"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Migrace konfigurace fondu Batch z Cloud Services na Virtual Machines

Fondy Batch se dají vytvořit buď pomocí [cloudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) , nebo [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration). ' virtualMachineConfiguration ' je doporučená konfigurace, protože podporuje všechny funkce dávky. fondy ' cloudServiceConfiguration ' nepodporují všechny funkce a nejsou plánovány žádné nové funkce.

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

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [konfiguracích fondu](nodes-and-pools.md#configurations).
- Přečtěte si další informace o [osvědčených postupech pro fondy](best-practices.md#pools).
- REST API odkaz na [Přidání fondu](https://docs.microsoft.com/rest/api/batchservice/pool/add) a [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration).
