---
title: Migrace konfigurace fondu Batch z Cloud Services na Virtual Machines
description: Naučte se aktualizovat konfiguraci fondu na nejnovější a doporučenou konfiguraci.
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200564"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Migrace konfigurace fondu Batch z Cloud Services do virtuálního počítače

V současné době je možné vytvořit fondy Batch pomocí [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) nebo [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration). Doporučujeme použít jenom konfiguraci virtuálního počítače, protože tato konfigurace podporuje všechny funkce dávky.

Fondy konfigurací Cloud Services nepodporují některé z aktuálních funkcí Batch a nepodporují žádné nově přidané funkce. [Po 29. února 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/)už nebudete moct vytvářet nové fondy ' CloudServiceConfiguration ' ani přidávat nové uzly do stávajících fondů.

Pokud vaše řešení Batch aktuálně používají fondy ' cloudServiceConfiguration ', doporučujeme změnit na ' virtualMachineConfiguration ', jakmile to bude možné. To vám umožní těžit ze všech funkcí služby Batch, jako je rozšířený [Výběr řady virtuálních počítačů](batch-pool-vm-sizes.md), virtuálních počítačů se systémem Linux, [kontejnerů](batch-docker-container-workloads.md), [Azure Resource Manager virtuální sítě](batch-virtual-network.md)a [šifrování disku uzlu](disk-encryption.md).

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Vytvoření fondu pomocí konfigurace virtuálního počítače

Existující aktivní fond, který používá ' cloudServiceConfiguration ', nelze přepnout na použití ' virtualMachineConfiguration '. Místo toho budete muset vytvořit nové fondy. Jakmile vytvoříte nové fondy ' virtualMachineConfiguration ' a replikete všechny úlohy a úlohy, můžete odstranit starou ' cloudServiceConfiguration'pools ', který už nepoužíváte.

Všechna rozhraní API pro Batch, nástroje příkazového řádku, Azure Portal a uživatelské rozhraní Batch Explorer umožňují vytvořit fondy pomocí příkazu ' virtualMachineConfiguration '.

Návod k procesu vytváření fondů, které používají virtualMachineConfiguration, najdete v kurzu [.NET](tutorial-parallel-dotnet.md) nebo v [kurzu pro Python](tutorial-parallel-python.md).

## <a name="pool-configuration-differences"></a>Rozdíly v konfiguraci fondu

Mezi tyto klíčové rozdíly mezi těmito dvěma konfigurace patří:

- uzly fondu ' cloudServiceConfiguration ' používají pouze operační systém Windows. fondy virtualMachineConfiguration můžou používat operační systém Linux nebo Windows.
- Ve srovnání s fondy cloudServiceConfiguration mají fondy virtualMachineConfiguration bohatší sadu funkcí, jako je podpora kontejnerů, datových disků a šifrování disku.
- Časy spuštění fondu a uzlů se mohou mírně lišit mezi fondy ' cloudServiceConfiguration ' a fondy ' virtualMachineConfiguration '.
- uzly fondu virtualMachineConfiguration využívají spravované disky s operačním systémem. [Typ spravovaného disku](../virtual-machines/disks-types.md) , který se používá pro každý uzel, závisí na velikosti virtuálního počítače zvolené pro fond. Pokud je pro fond zadaná velikost virtuálního počítače, například Standard_D2s_v3, použije se jednotka SSD služby Premium. Pokud je zadaná velikost virtuálního počítače bez s, například Standard_D2_v3, použije se standardní HDD.

   > [!IMPORTANT]
   > Stejně jako u Virtual Machines a Virtual Machine Scale Sets se na spravovaný disk s operačním systémem, který se používá pro každý uzel, účtují náklady, což je další náklady na virtuální počítače. Pro uzly ' cloudServiceConfiguration ' nejsou k dispozici žádné náklady na disk s operačním systémem, protože disk s operačním systémem je vytvořen v uzlech místní SSD.

## <a name="azure-data-factory-custom-activity-pools"></a>Azure Data Factory vlastní fondy aktivit

Fondy Azure Batch se dají použít ke spouštění Data Factory vlastních aktivit. Všechny fondy ' cloudServiceConfiguration ' používané ke spouštění vlastních aktivit budou muset být odstraněny a vytvořily se nové fondy ' virtualMachineConfiguration '.

Když vytváříte nové fondy, které budou spouštět Data Factory vlastní aktivity, postupujte podle těchto postupů:

- Před vytvořením nových fondů pozastavte všechny kanály a odstraní staré, aby se zajistilo, že nedojde k přerušení žádného spuštění.
- Stejné ID fondu se dá použít k tomu, abyste se vyhnuli změnám konfigurace propojených služeb.
- Obnoví kanály, když se vytvoří nové fondy.

Další informace o použití Azure Batch ke spouštění Data Factory vlastní aktivity najdete v tématu [Azure Batch propojená služba](../data-factory/compute-linked-services.md#azure-batch-linked-service) a  [vlastní aktivity v kanálu Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [konfiguracích fondu](nodes-and-pools.md#configurations).
- Přečtěte si další informace o [osvědčených postupech pro fondy](best-practices.md#pools).
- Další informace najdete v tématu REST API Reference pro [Přidání fondu](/rest/api/batchservice/pool/add) a [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).