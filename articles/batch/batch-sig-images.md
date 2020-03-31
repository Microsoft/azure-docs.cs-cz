---
title: Vytvoření vlastního fondu – Azure Batch | Dokumenty společnosti Microsoft
description: Vytvořte fond dávek s Galerií sdílených bitových kopií pro zřízení vlastních ikcí pro výpočetní uzly, které obsahují software a data, která potřebujete pro vaši aplikaci. Vlastní image jsou efektivní způsob, jak nakonfigurovat výpočetní uzly pro spuštění úloh batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: labrenne
ms.openlocfilehash: 2cff6a0e48fc7bf58a642f509fcda6b114e002ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022932"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Vytvoření vlastního fondu pomocí Galerie sdílených obrázků

Když vytvoříte fond Azure Batch pomocí konfigurace virtuálního počítače, zadáte image virtuálního počítače, která poskytuje operační systém pro každý výpočetní uzel ve fondu. Fond virtuálních počítačů můžete vytvořit buď s podporovanou image Azure Marketplace, nebo vytvořit vlastní bitovou kopii pomocí [Galerie sdílených bitových obrázků](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Výhody galerie sdílených obrázků

Při použití Galerie sdílených obrázků pro vlastní bitovou kopii máte kontrolu nad typem a konfigurací operačního systému a také nad typem datových disků. Sdílená bitová kopie může obsahovat aplikace a referenční data, která budou k dispozici ve všech uzlech fondu dávek, jakmile jsou zřízeny.

Můžete mít také více verzí bitové kopie podle potřeby pro vaše prostředí. Když k vytvoření virtuálního počítače použijete verzi image, verze image se použije k vytvoření nových disků pro virtuální počítače.

Použití sdílené image šetří čas při přípravě výpočetních uzlů fondu ke spuštění dávkové úlohy. Je možné použít image Azure Marketplace a nainstalovat software na každém výpočetním uzlu po zřízení, ale použití sdílené image je obvykle efektivnější. Kromě toho můžete zadat více replik pro sdílenou image, takže při vytváření fondů s mnoha virtuálními počítači (více než 600 virtuálních počítače), ušetříte čas při vytváření fondu.

Použití sdílené bitové kopie nakonfigurované pro váš scénář může poskytnout několik výhod:

* **Používejte stejné obrázky v různých oblastech.** Repliky sdílených bitových obrázků můžete vytvářet v různých oblastech, aby všechny fondy využívaly stejnou bitovou kopii.
* **Konfigurace operačního systému (OS).** Konfiguraci disku operačního systému bitové kopie můžete přizpůsobit.
* **Předinstalujte aplikace.** Předinstalace aplikací na disku operačního systému je efektivnější a méně náchylná k chybám než instalace aplikací po zřízení výpočetních uzlů pomocí úlohy spuštění.
* **Zkopírujte velké množství dat jednou.** Zkopírováním statických dat do spravované sdílené bitové kopie na datové disky spravované bitové kopie. To je třeba provést pouze jednou a zpřístupní data pro každý uzel fondu.
* **Růst bazény do větších velikostí.** Pomocí Galerie sdílených obrázků můžete vytvářet větší fondy s přizpůsobenými bitovými kopiemi spolu s dalšími replikami sdílených bitových kopií.
* **Lepší výkon než vlastní image.** Pomocí sdílených bitových kopií je doba potřebný pro fond k dosažení ustáleného stavu až o 25 % rychlejší a latence nečinnosti virtuálního uživatele je až o 30 % kratší.
* **Správa verzí obrázků a seskupování pro snadnější správu.** Definice seskupení obrázků obsahuje informace o tom, proč byl obrázek vytvořen, k jakému osu je určen, a informace o používání obrázku. Seskupování obrázků umožňuje snadnější správu obrázků. Další informace naleznete v [tématu Definice obrázků](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Požadavky

* **Účet Azure Batch.** Pokud chcete vytvořit dávkový účet, přečtěte si rychlé spuštění dávky pomocí [portálu Azure nebo](quick-create-portal.md) [azure cli](quick-create-cli.md).

* **Obrázek galerie sdílených obrázků**. Chcete-li vytvořit sdílenou bitovou kopii, musíte mít nebo vytvořit prostředek spravované bitové kopie. Bitová kopie by měla být vytvořena ze snímků disku operačního systému virtuálního počítače a volitelně z připojených datových disků. Další informace naleznete v [tématu Příprava spravované bitové kopie](#prepare-a-managed-image).

> [!NOTE]
> Sdílená bitová kopie musí mít stejné předplatné jako účet Batch. Sdílená bitová kopie může být v různých oblastech, pokud má repliky ve stejné oblasti jako váš účet Batch.

## <a name="prepare-a-managed-image"></a>Příprava spravované horečné bitové kopie

V Azure můžete připravit spravovanou image z:

* Snímky operačního systému Azure VM a datových disků
* Generalizovaný virtuální počítač Azure se spravovanými disky
* Generalizovaný místní virtuální pevný disk nahraný do cloudu

Chcete-li spolehlivě škálovat dávkové fondy pomocí vlastní image, doporučujeme vytvořit spravovanou bitovou kopii *pouze* pomocí první metody: pomocí snímků disků virtuálního počítače. Postupujte podle následujících kroků k přípravě virtuálního počítače, pořízení snímku a vytvoření obrazu ze snímku.

### <a name="prepare-a-vm"></a>Příprava virtuálního počítače

Pokud vytváříte nový virtuální počítač pro image, použijte image Azure Marketplace první strany podporovanou batch jako základní image pro spravovanou image. Jako základní obrázek lze použít pouze obrázky první strany. Úplný seznam odkazů na image Azure Marketplace podporovaných Azure Batch najdete v tématu Operace [skutnice prodejů uzla uzlu seznamu.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Jako základní bitovou kopii nelze použít bitovou kopii třetí strany, která má další licence a nákupní podmínky. Informace o těchto inacích Marketplace najdete v návodu k [Linuxu](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) nebo virtuálním počítačům [s Windows.](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)

* Ujistěte se, že virtuální modul je vytvořen pomocí spravovaného disku. Toto je výchozí nastavení úložiště při vytváření virtuálního virtuálního soudu.
* Neinstalujte rozšíření Azure, jako je například rozšíření vlastní skript, na virtuální počítač. Pokud bitová kopie obsahuje předinstalovanou rozšíření, Azure může dojít k problémům při nasazování fondu batch.
* Při použití připojených datových disků je potřeba připojit a naformátovat disky z virtuálního počítače, abyste je měli používat.
* Ujistěte se, že základní bitová kopie operačního systému, kterou zadáte, používá výchozí dočasnou jednotku. Agent uzlu Batch aktuálně očekává výchozí dočasnou jednotku.
* Jakmile je virtuální počítač spuštěný, připojte se k němu přes RDP (pro Windows) nebo SSH (pro Linux). Nainstalujte potřebný software nebo zkopírujte požadovaná data.  

### <a name="create-a-vm-snapshot"></a>Vytvoření snímku virtuálního počítače

Snímek je úplná kopie virtuálního pevného disku jen pro čtení. Chcete-li vytvořit snímek operačního systému virtuálního počítače nebo datových disků, můžete použít portál Azure nebo nástroje příkazového řádku. Kroky a možnosti vytvoření snímku najdete v návodu pro [virtuální počítače s Linuxem](../virtual-machines/linux/snapshot-copy-managed-disk.md) nebo [Windows.](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Vytvoření obrázku z jednoho nebo více snímků

Chcete-li vytvořit spravovanou bitovou kopii ze snímku, použijte nástroje příkazového řádku Azure, jako je příkaz [az image create.](/cli/azure/image) Vytvořte bitovou kopii zadáním snímku disku operačního systému a volitelně jednoho nebo více snímků datového disku.

### <a name="create-a-shared-image-gallery"></a>Vytvoření galerie sdílených obrázků

Po úspěšném vytvoření spravované bitové kopie je třeba vytvořit galerii sdílených obrázků, aby byla vlastní bitová kopie k dispozici. Informace o tom, jak vytvořit sdílenou galerii obrázků pro vaše bitové kopie, najdete [v tématu Vytvoření galerie sdílených bitových kopií pomocí azure cli](../virtual-machines/linux/shared-images.md) nebo [vytvoření galerie sdílených bitových kopií pomocí portálu Azure](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Vytvoření fondu ze sdílené bitové kopie pomocí příkazového příkazového příkazu Azure

Chcete-li vytvořit fond ze sdílené image pomocí `az batch pool create` příkazu Azure CLI, použijte příkaz. V poli zadejte ID sdíleného obrázku. `--image` Ujistěte se, že typ operačního systému a skladová položka odpovídají verzím určeným`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Vytvoření fondu ze sdíleného obrázku pomocí c #

Případně můžete vytvořit fond ze sdílené image pomocí sady C# SDK.

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

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Vytvoření fondu ze sdílené bitové kopie pomocí portálu Azure

Pomocí následujících kroků vytvořte fond ze sdílené image na webu Azure Portal.

1. Otevřete [portál Azure](https://portal.azure.com).
1. Přejděte na **Dávkové účty** a vyberte svůj účet.
1. Vyberte **fondy** a pak **Přidat** k vytvoření nového fondu.
1. V části **Typ obrázku** vyberte **Sdílená galerie obrázků**.
1. Dokončete zbývající části informacemi o spravované bitové kopii.
1. Vyberte **OK**.

![Vytvořte fond s z sdílené image s portálem.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Důležité informace pro velké fondy

Pokud plánujete vytvořit fond se stovkami nebo tisíci virtuálních aplikací nebo více pomocí sdílené image, použijte následující pokyny.

* **Čísla replik Galerie sdílených obrázků.**  Pro každý fond s až 600 instancemi doporučujeme zachovat alespoň jednu repliku. Například pokud vytváříte fond s 3000 virtuálních počítačích, měli byste zachovat alespoň 5 repliky image. Vždy doporučujeme zachovat více replik než minimální požadavky pro lepší výkon.

* **Změna velikosti časového ochron.** Pokud váš fond obsahuje pevný počet uzlů (pokud není automatické `resizeTimeout` škálování), zvýšit vlastnost fondu v závislosti na velikosti fondu. Pro každých 1000 virtuálních počítače je doporučený časový čas velikosti alespoň 15 minut. Například doporučený časový čas změny velikosti pro fond s 2000 virtuálními počítačemi je alespoň 30 minut.

## <a name="next-steps"></a>Další kroky

* Podrobný přehled batch, najdete v tématu [Vývoj rozsáhlých paralelních výpočetních řešení s Batch](batch-api-basics.md).
