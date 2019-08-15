---
title: Vytvoření Azure Batch fondu pomocí Galerie sdílených imagí | Microsoft Docs
description: Vytvořte fond služby Batch pomocí Galerie sdílených imagí a zřiďte vlastní image pro výpočetní uzly, které obsahují software a data, která pro vaši aplikaci potřebujete. Vlastní image představují účinný způsob konfigurace výpočetních uzlů pro spouštění úloh služby Batch.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/14/2019
ms.author: lahugh
ms.openlocfilehash: 00da17512cbc2e713955ea83c7d9fa7517958169
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036703"
---
# <a name="use-the-shared-image-gallery-to-create-a-pool"></a>Vytvoření fondu pomocí Galerie sdílených imagí

Když vytváříte fond Azure Batch s použitím konfigurace virtuálního počítače, zadáte image virtuálního počítače, která poskytuje operační systém pro každý výpočetní uzel ve fondu. Můžete vytvořit fond virtuálních počítačů s podporovaným Azure Marketplace imagí nebo vytvořit vlastní image pomocí [Galerie sdílených imagí](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Výhody Galerie sdílených imagí

Když použijete galerii sdílených imagí pro vlastní image, budete mít kontrolu nad typem a konfigurací operačního systému a také s typem datových disků. Sdílená image může zahrnovat aplikace a referenční data, která jsou k dispozici ve všech uzlech fondu služby Batch hned po zřízení.

Můžete mít také více verzí bitové kopie, které jsou potřeba pro vaše prostředí. Když použijete verzi image k vytvoření virtuálního počítače, verze image se použije k vytvoření nových disků pro virtuální počítač. 

Použití sdíleného obrázku šetří čas při přípravě výpočetních uzlů fondu na spuštění úlohy služby Batch. Po zřízení je možné použít bitovou kopii Azure Marketplace a nainstalovat software na každý výpočetní uzel, ale použití sdílené Image je obvykle efektivnější. Kromě toho můžete pro sdílenou image zadat několik replik, takže když vytvoříte fondy s mnoha virtuálními počítači (víc než 600 virtuálních počítačů), ušetříte čas při vytváření fondu.

Použití sdílené bitové kopie nakonfigurované pro váš scénář může mít několik výhod:

* **Používejte stejné obrázky v různých oblastech.** Můžete vytvářet repliky sdílených imagí v různých oblastech, aby všechny vaše fondy využily stejný obrázek.
* **Nakonfigurujte operační systém (OS).** Můžete přizpůsobit konfiguraci disku s operačním systémem image.
* **Před instalací aplikací.** Předinstalace aplikací na disk s operačním systémem je efektivnější a méně náchylná k chybám než instalace aplikací po zřízení výpočetních uzlů s počátečním úkolem.
* **Kopírování velkých objemů dat jednou.** Nastavte statickou datovou část spravované sdílené image tak, že ji zkopírujete do datových disků spravované image. To je nutné provést pouze jednou a zpřístupnit data pro každý uzel fondu.
* **Rozšiřte fondy na větší velikosti.** Pomocí Galerie sdílených imagí můžete vytvořit větší fondy s přizpůsobenými imagemi společně s více replikami sdílených imagí.
* **Lepší výkon než vlastní image.** Při použití sdílených imagí je čas potřebný k dosažení stabilního stavu až o 25% rychlejší a latence nečinnosti virtuálního počítače je kratší než 30%.
* **Správa verzí obrázků a seskupování pro snadnější správu.** Definice seskupení imagí obsahuje informace o tom, proč se image vytvořila, v jakém operačním systému je, a informace o použití image. Seskupení imagí umožňuje snazší správu imagí. Další informace najdete v tématu [definice imagí](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Požadavky

* **Účet Azure Batch.** Pokud chcete vytvořit účet Batch, přečtěte si rychlý Start Batch pomocí [Azure Portal](quick-create-portal.md) nebo [Azure CLI](quick-create-cli.md).

* **Obrázek Galerie sdílených imagí** Další informace a kroky pro přípravu sdílené image najdete v tématu [Vytvoření sdílené Image Galerie pomocí Azure CLI](../virtual-machines/linux/shared-images.md) nebo [vytvoření galerie sdílených imagí pomocí Azure Portal](../virtual-machines/linux/shared-images-portal.md).

> [!NOTE]
> Vaše sdílená bitová kopie musí být ve stejném předplatném jako účet Batch. Vaše sdílená image může být v různých oblastech, pokud má repliky ve stejné oblasti jako váš účet Batch.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Vytvoření fondu ze sdílené Image pomocí Azure CLI

Pokud chcete vytvořit fond ze sdílené Image pomocí Azure CLI, použijte `az batch pool create` příkaz. V `--image` poli zadejte ID sdílené bitové kopie. Zajistěte, aby typ operačního systému a SKU odpovídaly verzím, které určuje.`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Vytvoření fondu ze sdílené Image pomocíC#

Alternativně můžete vytvořit fond ze sdílené Image pomocí C# sady SDK.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="considerations-for-large-pools"></a>Předpoklady pro velké fondy

Pokud máte v úmyslu vytvořit fond se stovkami nebo tisíci virtuálních počítačů nebo více pomocí sdílené image, postupujte podle následujících pokynů.

* **Čísla repliky Galerie sdílených imagí**  Pro každý fond s až 600 instancemi doporučujeme, abyste zachovali aspoň jednu repliku. Pokud například vytváříte fond s 3000 virtuálními počítači, měli byste zachovat alespoň 5 replik vaší image. Vždycky Doporučujeme zachovat více replik než minimální požadavky pro lepší výkon.

* **Změnit časový limit** Pokud fond obsahuje pevný počet uzlů (Pokud se nejedná o automatické škálování), zvětšete `resizeTimeout` vlastnost fondu v závislosti na velikosti fondu. U každého virtuálního počítače 1000 je doporučený časový limit pro změnu velikosti alespoň 15 minut. Například doporučený časový limit pro změnu velikosti pro fond s 2000 virtuálními počítači je nejméně 30 minut.

## <a name="next-steps"></a>Další postup

* Podrobný přehled služby Batch najdete v tématu [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).
