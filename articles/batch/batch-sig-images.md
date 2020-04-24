---
title: Vytvoření vlastního fondu pomocí Galerie sdílených imagí
description: Vytvořte fond služby Batch pomocí Galerie sdílených imagí a zřiďte vlastní image pro výpočetní uzly, které obsahují software a data, která pro vaši aplikaci potřebujete. Vlastní image představují účinný způsob konfigurace výpočetních uzlů pro spouštění úloh služby Batch.
ms.topic: article
ms.date: 08/28/2019
ms.openlocfilehash: 45f721dbdf11e0a6f58da71c644acf687dfadd49
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116515"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Vytvoření vlastního fondu pomocí Galerie sdílených imagí

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

## <a name="prerequisites"></a>Požadované součásti

> [!NOTE]
> Musíte se ověřit pomocí Azure AD. Pokud používáte ověřování Shared-Key-Authentication, zobrazí se chyba ověřování.  

* **Účet Azure Batch.** Pokud chcete vytvořit účet Batch, přečtěte si rychlý Start Batch pomocí [Azure Portal](quick-create-portal.md) nebo [Azure CLI](quick-create-cli.md).

* **Obrázek Galerie sdílených imagí** Chcete-li vytvořit sdílenou bitovou kopii, je nutné mít nebo vytvořit prostředek spravované bitové kopie. Image by se měla vytvořit ze snímků disku s operačním systémem virtuálního počítače a volitelně z připojených datových disků. Další informace najdete v tématu [Příprava spravované image](#prepare-a-managed-image).

> [!NOTE]
> Vaše sdílená bitová kopie musí být ve stejném předplatném jako účet Batch. Vaše sdílená image může být v různých oblastech, pokud má repliky ve stejné oblasti jako váš účet Batch.

## <a name="prepare-a-managed-image"></a>Příprava spravované image

V Azure můžete připravit spravovanou bitovou kopii z těchto:

* Snímky operačního systému a datových disků virtuálního počítače Azure
* Zobecněný virtuální počítač Azure se spravovanými disky
* Zobecněný místní virtuální pevný disk nahraný do cloudu

Abyste mohli škálovat fondy dávek spolehlivě s použitím vlastní image, doporučujeme vytvořit spravovanou image *jenom* pomocí první metody: použití snímků disků virtuálního počítače. Pokud chcete připravit virtuální počítač, pořídit snímek a vytvořit z snímku obrázek.

### <a name="prepare-a-vm"></a>Příprava virtuálního počítače

Pokud vytváříte nový virtuální počítač pro bitovou kopii, použijte jako základní image pro spravovanou bitovou kopii Azure Marketplace image, kterou služba Batch podporuje. Jako základní image se dají použít jenom image ze strany First stran. Úplný seznam Azure Marketplacech odkazů na Image podporovaných v Azure Batch najdete v tématu operace [výpisu SKU agenta uzlu](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> Nemůžete použít image třetí strany, která má další licenci a jako základní image. Informace o těchto obrázcích na webu Marketplace najdete v tématu pokyny pro virtuální počítače se systémem [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) nebo [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) .

* Ujistěte se, že je virtuální počítač vytvořený pomocí spravovaného disku. Toto je výchozí nastavení úložiště při vytváření virtuálního počítače.
* Do virtuálního počítače neinstalujte rozšíření Azure, jako je například rozšíření vlastních skriptů. Pokud image obsahuje předem nainstalovanou příponu, může Azure narazit na problémy při nasazování fondu služby Batch.
* Pokud používáte připojené datové disky, musíte je připojit a naformátovat na virtuálním počítači, aby je bylo možné použít.
* Ujistěte se, že základní bitová kopie operačního systému, kterou zadáte, používá výchozí dočasnou jednotku. Agent uzlu dávky aktuálně očekává výchozí dočasnou jednotku.
* Jakmile je virtuální počítač spuštěný, připojte se k němu přes RDP (pro Windows) nebo SSH (pro Linux). Nainstalujte potřebný software nebo zkopírujte požadovaná data.  

### <a name="create-a-vm-snapshot"></a>Vytvoření snímku virtuálního počítače

Snímek je plná kopie VHD, která je jen pro čtení. Pokud chcete vytvořit snímek s operačním systémem nebo datovými disky virtuálního počítače, můžete použít Azure Portal nebo nástroje příkazového řádku. Postup a možnosti vytvoření snímku najdete v tématu pokyny pro virtuální počítače se systémem [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) nebo [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) .

### <a name="create-an-image-from-one-or-more-snapshots"></a>Vytvoření obrázku z jednoho nebo více snímků

Pokud chcete vytvořit spravovanou image ze snímku, použijte nástroje příkazového řádku Azure, jako je příkaz [AZ image Create](/cli/azure/image) . Vytvořte bitovou kopii zadáním snímku disku s operačním systémem a volitelně jednoho nebo více snímků datových disků.

### <a name="create-a-shared-image-gallery"></a>Vytvoření galerie sdílených imagí

Po úspěšném vytvoření spravované image musíte vytvořit sdílenou galerii imagí, abyste mohli vlastní image zpřístupnit. Informace o tom, jak vytvořit sdílenou galerii imagí pro vaše image, najdete v tématu [Vytvoření sdílené Galerie imagí pomocí Azure CLI](../virtual-machines/linux/shared-images.md) nebo [vytvoření galerie sdílených imagí pomocí Azure Portal](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Vytvoření fondu ze sdílené Image pomocí Azure CLI

Pokud chcete vytvořit fond ze sdílené Image pomocí Azure CLI, použijte `az batch pool create` příkaz. V `--image` poli zadejte ID sdílené bitové kopie. Zajistěte, aby typ operačního systému a SKU odpovídaly verzím, které určuje.`--node-agent-sku-id`

> [!NOTE]
> Musíte se ověřit pomocí Azure AD. Pokud používáte ověřování Shared-Key-Authentication, zobrazí se chyba ověřování.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Vytvoření fondu ze sdílené Image pomocí jazyka C #

Alternativně můžete vytvořit fond ze sdílené Image pomocí sady C# SDK.

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

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Vytvoření fondu ze sdílené Image pomocí Azure Portal

Pomocí následujících kroků můžete vytvořit fond ze sdílené image v Azure Portal.

1. Otevřete [Azure Portal](https://portal.azure.com).
1. Přejít na **účty Batch** a vyberte svůj účet.
1. Vyberte **fondy** a potom **Přidat** a vytvořte nový fond.
1. V části **typ obrázku** vyberte **sdílená Galerie imagí**.
1. Dokončete zbývající části s informacemi o spravované imagi.
1. Vyberte **OK**.

![Vytvořte fond se ze sdílené image s portálem.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Předpoklady pro velké fondy

Pokud máte v úmyslu vytvořit fond se stovkami nebo tisíci virtuálních počítačů nebo více pomocí sdílené image, postupujte podle následujících pokynů.

* **Čísla repliky Galerie sdílených imagí**  Pro každý fond s až 600 instancemi doporučujeme, abyste zachovali aspoň jednu repliku. Pokud například vytváříte fond s 3000 virtuálními počítači, měli byste zachovat alespoň 5 replik vaší image. Vždycky Doporučujeme zachovat více replik než minimální požadavky pro lepší výkon.

* **Změnit časový limit.** Pokud fond obsahuje pevný počet uzlů (Pokud se nejedná o automatické škálování), zvětšete `resizeTimeout` vlastnost fondu v závislosti na velikosti fondu. U každého virtuálního počítače 1000 je doporučený časový limit pro změnu velikosti alespoň 15 minut. Například doporučený časový limit pro změnu velikosti pro fond s 2000 virtuálními počítači je nejméně 30 minut.

## <a name="next-steps"></a>Další kroky

* Podrobný přehled služby Batch najdete v tématu [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).
