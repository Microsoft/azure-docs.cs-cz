---
title: Zřízení vlastního fondu ze spravované image – Azure Batch | Microsoft Docs
description: Vytvořte fond služby Batch z prostředku spravovaného obrazu a zřídíte výpočetní uzly se softwarem a daty pro vaši aplikaci.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: lahugh
ms.openlocfilehash: 82a5f79ca7b7a16cd8f7294ebd1f70816b40ad82
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "73519231"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Vytvoření fondu virtuálních počítačů pomocí spravované image

Pokud chcete vytvořit vlastní image pro virtuální počítače (VM) fondu služby Batch, můžete použít buď [galerii sdílených imagí](batch-sig-images.md), nebo prostředek *spravované image* .

> [!TIP]
> Ve většině případů byste měli vytvořit vlastní image pomocí Galerie sdílených imagí. Pomocí Galerie sdílených imagí můžete vytvořit fondy rychleji, škálovat větší množství virtuálních počítačů a zvýšit spolehlivost při zřizování virtuálních počítačů. Další informace najdete v tématu [použití Galerie sdílených imagí k vytvoření vlastního fondu](batch-sig-images.md).

## <a name="prerequisites"></a>Předpoklady

- **Prostředek spravované image** Pokud chcete vytvořit fond virtuálních počítačů s použitím vlastní image, musíte mít nebo vytvořit prostředek spravované image ve stejném předplatném Azure a oblasti jako účet Batch. Image by se měla vytvořit ze snímků disku s operačním systémem virtuálního počítače a volitelně z připojených datových disků. Další informace a kroky pro přípravu spravované image najdete v následující části.
  - Pro každý vytvořený fond použijte jedinečnou vlastní image.
  - Chcete-li vytvořit fond s imagí pomocí rozhraní API pro dávkování, zadejte **ID prostředku** obrázku, který je ve formátu `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Chcete-li použít portál, použijte **název** bitové kopie.  
  - Prostředek spravované Image by měl existovat po dobu života fondu, aby bylo možné provést horizontální navýšení kapacity a po odstranění fondu je možné ho odebrat.

- **Ověřování Azure Active Directory (AAD)** . Rozhraní API pro klienta Batch musí používat ověřování AAD. Podpora Azure Batch pro AAD je popsaná v dokumentaci k [ověřování řešení služby Batch pomocí služby Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Příprava vlastní image

V Azure můžete připravit spravovanou bitovou kopii z těchto:

- Snímky operačního systému a datových disků virtuálního počítače Azure
- Zobecněný virtuální počítač Azure se spravovanými disky
- Zobecněný místní virtuální pevný disk nahraný do cloudu

Abyste mohli škálovat fondy dávek spolehlivě s použitím vlastní image, doporučujeme vytvořit spravovanou image *jenom* pomocí první metody: použití snímků disků virtuálního počítače. Pokud chcete připravit virtuální počítač, pořídit snímek a vytvořit z snímku obrázek.

### <a name="prepare-a-vm"></a>Příprava virtuálního počítače

Pokud vytváříte nový virtuální počítač pro bitovou kopii, použijte jako základní image pro spravovanou bitovou kopii Azure Marketplace image, kterou služba Batch podporuje. Jako základní image se dají použít jenom image ze strany First stran. Úplný seznam Azure Marketplacech odkazů na Image podporovaných v Azure Batch najdete v tématu operace [výpisu SKU agenta uzlu](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> Nemůžete použít image třetí strany, která má další licenci a jako základní image. Informace o těchto obrázcích na webu Marketplace najdete v tématu pokyny pro virtuální počítače se systémem [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) nebo [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) .

- Ujistěte se, že je virtuální počítač vytvořený pomocí spravovaného disku. Toto je výchozí nastavení úložiště při vytváření virtuálního počítače.
- Do virtuálního počítače neinstalujte rozšíření Azure, jako je například rozšíření vlastních skriptů. Pokud image obsahuje předem nainstalovanou příponu, může Azure narazit na problémy při nasazování fondu služby Batch.
- Pokud používáte připojené datové disky, musíte je připojit a naformátovat na virtuálním počítači, aby je bylo možné použít.
- Ujistěte se, že základní bitová kopie operačního systému, kterou zadáte, používá výchozí dočasnou jednotku. Agent uzlu dávky aktuálně očekává výchozí dočasnou jednotku.
- Jakmile je virtuální počítač spuštěný, připojte se k němu přes RDP (pro Windows) nebo SSH (pro Linux). Nainstalujte potřebný software nebo zkopírujte požadovaná data.  

### <a name="create-a-vm-snapshot"></a>Vytvoření snímku virtuálního počítače

Snímek je plná kopie VHD, která je jen pro čtení. Pokud chcete vytvořit snímek s operačním systémem nebo datovými disky virtuálního počítače, můžete použít Azure Portal nebo nástroje příkazového řádku. Postup a možnosti vytvoření snímku najdete v tématu pokyny pro virtuální počítače se systémem [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) nebo [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) .

### <a name="create-an-image-from-one-or-more-snapshots"></a>Vytvoření obrázku z jednoho nebo více snímků

Pokud chcete vytvořit spravovanou image ze snímku, použijte nástroje příkazového řádku Azure, jako je příkaz [AZ image Create](/cli/azure/image) . Můžete vytvořit image zadáním snímku disku s operačním systémem a volitelně jednoho nebo více snímků datových disků.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Vytvoření fondu z vlastní image na portálu

Po uložení vlastní image a zjištění jejího ID nebo názvu prostředku vytvořte z této image fond služby Batch. Následující kroky ukazují, jak vytvořit fond z Azure Portal.

> [!NOTE]
> Pokud vytváříte fond pomocí jednoho z rozhraní API služby Batch, ujistěte se, že identita, kterou používáte pro ověřování AAD, má oprávnění k prostředku image. Viz [ověřování řešení služby Batch pomocí služby Active Directory](batch-aad-auth.md).
>
> Pro dobu života fondu musí existovat prostředek pro spravovanou bitovou kopii. Pokud se základní prostředek odstraní, nelze škálovat fond.

1. Na webu Azure Portal přejděte ke svému účtu Batch. Tento účet musí být ve stejném předplatném a oblasti jako skupina prostředků obsahující vlastní image.
2. V okně **Nastavení** na levé straně vyberte položku nabídky **fondy** .
3. V okně **fondy** vyberte příkaz **Přidat** .
4. V okně **Přidat fond** vyberte v rozevíracím seznamu **typ obrázku** **vlastní image (Linux/Windows)** . V rozevíracím seznamu **vlastní image virtuálního počítače** vyberte název Image (krátký tvar ID prostředku).
5. Vyberte pro vlastní image správného **vydavatele/nabídku/SKU** .
6. Zadejte zbývající požadovaná nastavení, včetně **velikosti uzlu**, **cílových vyhrazených uzlů**a **uzlů s nízkou prioritou**, a také všech požadovaných volitelných nastavení.

    Například pro vlastní image Microsoft Windows Server Datacenter 2016 se zobrazí okno **Přidat fond** , jak je znázorněno níže:

    ![Přidat fond z vlastní image Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Pokud chcete zjistit, jestli je existující fond založený na vlastní imagi, podívejte se do vlastnosti **operačního systému** v části Souhrn prostředků okna **fondu** . Pokud byl fond vytvořen z vlastní image, je nastaven na **vlastní image virtuálního počítače**.

Všechny vlastní image přidružené k fondu se zobrazí v okně **vlastností** fondu.

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

Pokud je bitová kopie nebo podkladový prostředek odebrána, může se zobrazit chyba podobná této: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Pokud se zobrazí tato chyba, ujistěte se, že se původní prostředek neodebral.

Další informace o použití balíčku k vytvoření virtuálního počítače najdete v tématu [Vytvoření image pro Linux pomocí balíčku](../virtual-machines/linux/build-image-with-packer.md) nebo [Vytvoření image Windows s balíčkem](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Další kroky

Podrobný přehled služby Batch najdete v tématu [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).
