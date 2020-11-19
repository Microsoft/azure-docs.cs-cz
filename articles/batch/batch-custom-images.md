---
title: Vytvoření vlastního fondu imagí pomocí spravované image
description: Vytvořte vlastní fond imagí dávky ze spravované image a zřídíte výpočetní uzly se softwarem a daty pro vaši aplikaci.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 0a357a1d8a22341297f3bee73fb0867fb03f374f
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916572"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>Vytvoření vlastního fondu imagí pomocí spravované image

Pokud chcete vytvořit vlastní fond imagí pro virtuální počítače ve fondu Batch, můžete k vytvoření [Image Galerie sdílených imagí](batch-sig-images.md)použít spravovanou bitovou kopii. Podporuje se taky jenom spravovaná image, ale jenom pro verze rozhraní API, a to včetně 2019-08-01.

> [!IMPORTANT]
> Ve většině případů byste měli vytvořit vlastní image pomocí Galerie sdílených imagí. Pomocí Galerie sdílených imagí můžete vytvořit fondy rychleji, škálovat větší množství virtuálních počítačů a zvýšit spolehlivost při zřizování virtuálních počítačů. Další informace najdete v tématu [použití Galerie sdílených imagí k vytvoření vlastního fondu](batch-sig-images.md).

V tomto tématu se dozvíte, jak vytvořit vlastní fond imagí jenom pomocí spravované image.

## <a name="prerequisites"></a>Požadavky

- **Prostředek spravované image** Pokud chcete vytvořit fond virtuálních počítačů s použitím vlastní image, musíte mít nebo vytvořit prostředek spravované image ve stejném předplatném Azure a oblasti jako účet Batch. Image by se měla vytvořit ze snímků disku s operačním systémem virtuálního počítače a volitelně z připojených datových disků.
  - Pro každý vytvořený fond použijte jedinečnou vlastní image.
  - Pokud chcete vytvořit fond s imagí pomocí rozhraní API pro Batch, zadejte **ID prostředku** obrázku, který je ve formátu `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` .
  - Prostředek spravované Image by měl existovat po dobu života fondu, aby bylo možné provést horizontální navýšení kapacity a po odstranění fondu je možné ho odebrat.

- **Ověřování Azure Active Directory (Azure AD)**. Klientské rozhraní API služby Batch musí používat ověřování Azure AD. Podpora služby Azure AD ve službě Azure Batch je zdokumentovaná v tématu [Ověřování řešení služby Batch pomocí Active Directory](batch-aad-auth.md).

## <a name="prepare-a-managed-image"></a>Příprava spravované image

V Azure můžete připravit spravovanou bitovou kopii z těchto:

- Snímky operačního systému a datových disků virtuálního počítače Azure
- Zobecněný virtuální počítač Azure se spravovanými disky
- Zobecněný místní virtuální pevný disk nahraný do cloudu

Aby bylo možné škálovat fondy dávek spolehlivě pomocí spravované image, doporučujeme vytvořit spravovanou bitovou kopii *pouze* pomocí první metody: použití snímků disků virtuálního počítače. Následující kroky ukazují, jak připravit virtuální počítač, pořídit snímek a vytvořit spravovanou bitovou kopii ze snímku.

### <a name="prepare-a-vm"></a>Příprava virtuálního počítače

Pokud vytváříte nový virtuální počítač pro bitovou kopii, použijte jako základní image pro spravovanou bitovou kopii Azure Marketplace image, kterou služba Batch podporuje. Jako základní image se dají použít jenom image ze strany First stran. Úplný seznam Azure Marketplacech odkazů na Image podporovaných v Azure Batch najdete v tématu operace [výpisu SKU agenta uzlu](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> Nemůžete použít image třetí strany, která má další licenci a jako základní image. Informace o těchto obrázcích na webu Marketplace najdete v tématu pokyny pro virtuální počítače se systémem [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) nebo [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) .

- Ujistěte se, že je virtuální počítač vytvořený pomocí spravovaného disku. Toto je výchozí nastavení úložiště při vytváření virtuálního počítače.
- Do virtuálního počítače neinstalujte rozšíření Azure, jako je například rozšíření vlastních skriptů. Pokud image obsahuje předem nainstalovanou příponu, může Azure narazit na problémy při nasazování fondu služby Batch.
- Pokud používáte připojené datové disky, musíte je připojit a naformátovat na virtuálním počítači, aby je bylo možné použít.
- Ujistěte se, že základní bitová kopie operačního systému, kterou zadáte, používá výchozí dočasnou jednotku. Agent uzlu dávky aktuálně očekává výchozí dočasnou jednotku.
- Ujistěte se, že disk s operačním systémem není zašifrovaný.
- Jakmile je virtuální počítač spuštěný, připojte se k němu přes RDP (pro Windows) nebo SSH (pro Linux). Nainstalujte potřebný software nebo zkopírujte požadovaná data.  

### <a name="create-a-vm-snapshot"></a>Vytvoření snímku virtuálního počítače

Snímek je plná kopie VHD, která je jen pro čtení. Pokud chcete vytvořit snímek s operačním systémem nebo datovými disky virtuálního počítače, můžete použít Azure Portal nebo nástroje příkazového řádku. Postup a možnosti vytvoření snímku najdete v tématu pokyny pro virtuální počítače se systémem [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) nebo [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) .

### <a name="create-an-image-from-one-or-more-snapshots"></a>Vytvoření obrázku z jednoho nebo více snímků

Pokud chcete vytvořit spravovanou image ze snímku, použijte nástroje příkazového řádku Azure, jako je příkaz [AZ image Create](/cli/azure/image) . Můžete vytvořit image zadáním snímku disku s operačním systémem a volitelně jednoho nebo více snímků datových disků.

## <a name="create-a-pool-from-a-managed-image"></a>Vytvoření fondu ze spravované image

Po nalezení ID prostředku spravované image vytvořte z této image vlastní fond imagí. Následující kroky ukazují, jak vytvořit vlastní fond imagí pomocí služby Batch nebo služby Batch Management.

> [!NOTE]
> Ujistěte se, že identita, kterou používáte pro ověřování Azure AD, má oprávnění k prostředku image. Viz [ověřování řešení služby Batch pomocí služby Active Directory](batch-aad-auth.md).
>
> Pro dobu života fondu musí existovat prostředek pro spravovanou bitovou kopii. Pokud se základní prostředek odstraní, nelze škálovat fond.

### <a name="batch-service-net-sdk"></a>Sada .NET SDK služby Batch

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
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
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
```

### <a name="batch-management-rest-api"></a>Rozhraní REST API pro správu služby Batch

Identifikátor URI v REST API

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

Text požadavku

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>Předpoklady pro velké fondy

Pokud máte v úmyslu vytvořit fond se stovkami virtuálních počítačů nebo více pomocí vlastní image, je důležité postupovat podle předchozích pokynů pro použití image vytvořené z snímku virtuálního počítače.

Všimněte si také následujících informací:

- **Omezení velikosti** – při použití vlastní image dávkově omezuje velikost fondu na 2500 vyhrazených výpočetních uzlů nebo 1000 uzlů s nízkou prioritou.

  Použijete-li pro vytvoření více fondů stejnou bitovou kopii (nebo více imagí založenou na stejném základním snímku), nemůžou celkové výpočetní uzly ve fondech překročit předchozí omezení. Pro více než jeden fond nedoporučujeme používat obrázek ani jeho základní snímek.

  Omezení se můžou snížit, pokud konfigurujete fond s [příchozími fondy NAT](pool-endpoint-configuration.md).

- **Změnit časový limit** – Pokud váš fond obsahuje pevný počet uzlů (neprovádí automatické škálování), zvyšte vlastnost resizeTimeout fondu na hodnotu, například 20-30 minut. Pokud váš fond nedosahuje své cílové velikosti v rámci časového limitu, proveďte jinou [operaci změny velikosti](/rest/api/batchservice/pool/resize).

  Pokud plánujete fond s více než 300 výpočetními uzly, možná budete muset změnit velikost fondu vícekrát, aby se dosáhlo cílové velikosti.
  
Pomocí [Galerie sdílených imagí](batch-sig-images.md)můžete vytvořit větší fondy s přizpůsobenými imagemi společně s více replikami sdílených imagí. Při použití sdílených imagí je čas potřebný k dosažení stabilního stavu až o 25% rychlejší a latence nečinnosti virtuálního počítače je kratší než 30%.

## <a name="considerations-for-using-packer"></a>Předpoklady pro použití balíčku

Vytvoření prostředku spravované image přímo pomocí balíčku se dá provést jenom s účty Batch v režimu předplatného uživatele. Pro účty režimu služby Batch je třeba nejprve vytvořit virtuální pevný disk a potom importovat virtuální pevný disk do prostředku spravované bitové kopie. V závislosti na režimu přidělování fondů (předplatné uživatele nebo služba Batch) se postup vytvoření prostředku spravované image liší.

Zajistěte, aby prostředek použitý k vytvoření spravované image existoval pro životnost všech fondů odkazujících na vlastní image. V takovém případě může dojít k selhání alokace fondu nebo selhání velikosti.

Pokud je bitová kopie nebo podkladový prostředek odebrána, může se zobrazit chyba podobná této: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed` . Pokud se zobrazí tato chyba, ujistěte se, že se původní prostředek neodebral.

Další informace o použití balíčku k vytvoření virtuálního počítače najdete v tématu [Vytvoření image pro Linux pomocí balíčku](../virtual-machines/linux/build-image-with-packer.md) nebo [Vytvoření image Windows s balíčkem](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Další kroky

- Naučte se používat [galerii sdílených imagí](batch-sig-images.md) k vytvoření vlastního fondu.
- Podrobný přehled služby Batch najdete v tématu [pracovní postup služby Batch a prostředky](batch-service-workflow-features.md).
