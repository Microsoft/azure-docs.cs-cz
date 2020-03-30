---
title: Zřízení vlastního fondu ze spravované bitové kopie – Azure Batch | Dokumenty společnosti Microsoft
description: Vytvořte fond dávek z prostředku spravované bitové kopie pro zřízení výpočetních uzlů se softwarem a daty pro vaši aplikaci.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: labrenne
ms.openlocfilehash: 1ef6be2ba9364203dceba54ab51325c05dbbbe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020144"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Vytvoření fondu virtuálních počítačů pomocí spravované bitové kopie

Chcete-li vytvořit vlastní image pro virtuální počítače fondu dávek (virtuální počítače), můžete použít buď [Sdílenou galerii obrázků](batch-sig-images.md)nebo *prostředek spravované bitové kopie.*

> [!TIP]
> Ve většině případů byste měli vytvořit vlastní obrázky pomocí Galerie sdílených obrázků. Pomocí Galerie sdílených obrázků můžete zřídit fondy rychleji, škálovat větší množství virtuálních počítačů a mít lepší spolehlivost při zřizování virtuálních počítačů. Další informace najdete [v tématu Vytvoření vlastního fondu pomocí Galerie sdílených obrázků](batch-sig-images.md).

## <a name="prerequisites"></a>Požadavky

- **Prostředek spravované bitové kopie**. Chcete-li vytvořit fond virtuálních počítačů pomocí vlastní image, musíte mít nebo vytvořit prostředek spravované image ve stejném předplatném Azure a oblasti jako účet Batch. Bitová kopie by měla být vytvořena ze snímků disku operačního systému virtuálního počítače a volitelně z připojených datových disků. Další informace a kroky k přípravě spravované bitové kopie naleznete v následující části.
  - Pro každý fond, který vytvoříte, použijte jedinečnou vlastní bitovou kopii.
  - Chcete-li vytvořit fond s obrazem pomocí dávkových api, zadejte **ID** `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`prostředku bitové kopie, která je ve formuláři . Chcete-li použít portál, použijte **název** obrázku.  
  - Prostředek spravované bitové kopie by měl existovat po dobu životnosti fondu, aby bylo možné vertikálně navýšit kapacitu, a po odstranění fondu jej lze odebrat.

- **Ověřování služby Azure Active Directory (AAD).** Rozhraní API klienta dávky musí používat ověřování AAD. Podpora Azure Batch pro AAD je dokumentována v [řešeních služby Authenticate Batch se službou Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Příprava vlastního obrázku

V Azure můžete připravit spravovanou image z:

- Snímky operačního systému Azure VM a datových disků
- Generalizovaný virtuální počítač Azure se spravovanými disky
- Generalizovaný místní virtuální pevný disk nahraný do cloudu

Chcete-li spolehlivě škálovat dávkové fondy pomocí vlastní image, doporučujeme vytvořit spravovanou bitovou kopii *pouze* pomocí první metody: pomocí snímků disků virtuálního počítače. Postupujte podle následujících kroků k přípravě virtuálního počítače, pořízení snímku a vytvoření obrazu ze snímku.

### <a name="prepare-a-vm"></a>Příprava virtuálního počítače

Pokud vytváříte nový virtuální počítač pro image, použijte image Azure Marketplace první strany podporovanou batch jako základní image pro spravovanou image. Jako základní obrázek lze použít pouze obrázky první strany. Úplný seznam odkazů na image Azure Marketplace podporovaných Azure Batch najdete v tématu Operace [skutnice prodejů uzla uzlu seznamu.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Jako základní bitovou kopii nelze použít bitovou kopii třetí strany, která má další licence a nákupní podmínky. Informace o těchto inacích Marketplace najdete v návodu k [Linuxu](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) nebo virtuálním počítačům [s Windows.](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)

- Ujistěte se, že virtuální modul je vytvořen pomocí spravovaného disku. Toto je výchozí nastavení úložiště při vytváření virtuálního virtuálního soudu.
- Neinstalujte rozšíření Azure, jako je například rozšíření vlastní skript, na virtuální počítač. Pokud bitová kopie obsahuje předinstalovanou rozšíření, Azure může dojít k problémům při nasazování fondu batch.
- Při použití připojených datových disků je potřeba připojit a naformátovat disky z virtuálního počítače, abyste je měli používat.
- Ujistěte se, že základní bitová kopie operačního systému, kterou zadáte, používá výchozí dočasnou jednotku. Agent uzlu Batch aktuálně očekává výchozí dočasnou jednotku.
- Jakmile je virtuální počítač spuštěný, připojte se k němu přes RDP (pro Windows) nebo SSH (pro Linux). Nainstalujte potřebný software nebo zkopírujte požadovaná data.  

### <a name="create-a-vm-snapshot"></a>Vytvoření snímku virtuálního počítače

Snímek je úplná kopie virtuálního pevného disku jen pro čtení. Chcete-li vytvořit snímek operačního systému virtuálního počítače nebo datových disků, můžete použít portál Azure nebo nástroje příkazového řádku. Kroky a možnosti vytvoření snímku najdete v návodu pro [virtuální počítače s Linuxem](../virtual-machines/linux/snapshot-copy-managed-disk.md) nebo [Windows.](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Vytvoření obrázku z jednoho nebo více snímků

Chcete-li vytvořit spravovanou bitovou kopii ze snímku, použijte nástroje příkazového řádku Azure, jako je příkaz [az image create.](/cli/azure/image) Bitovou kopii můžete vytvořit zadáním snímku disku operačního systému a volitelně jednoho nebo více snímků datového disku.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Vytvoření fondu z vlastní bitové kopie na portálu

Jakmile uložíte vlastní bitovou kopii a znáte jeho ID nebo název prostředku, vytvořte z ní fond dávek. Následující kroky ukazují, jak vytvořit fond z webu Azure Portal.

> [!NOTE]
> Pokud vytváříte fond pomocí jednoho z dávkových api, ujistěte se, že identita, kterou používáte pro ověřování AAD, má oprávnění k prostředku bitové kopie. Viz [Ověření řešení dávkových služeb pomocí služby Active Directory](batch-aad-auth.md).
>
> Prostředek pro spravovanou bitovou kopii musí existovat po dobu životnosti fondu. Pokud je základní prostředek odstraněn, nelze škálovat fond.

1. Na webu Azure Portal přejděte ke svému účtu Batch. Tento účet musí být ve stejném předplatném a oblasti jako skupina prostředků obsahující vlastní image.
2. V okně **Nastavení** vlevo vyberte položku nabídky **Bazény.**
3. V okně **Fondy** vyberte příkaz **Přidat.**
4. V okně **Přidat fond** vyberte v rozevíracím seznamu **Typ obrázku** **položku Vlastní bitová kopie (Linux/Windows).** V rozevíracím seznamu **vlastní chod virtuálního** zařízení vyberte název obrázku (krátký tvar ID prostředku).
5. Vyberte správnou **aplikaci Publisher/Offer/Sku** pro vlastní obrázek.
6. Zadejte zbývající požadovaná nastavení, včetně **velikosti uzlu**, **cílových uzly**a **uzlů s nízkou prioritou**, a také všechna požadovaná volitelná nastavení.

    Například pro vlastní bitovou kopii Microsoft Windows Server Datacenter 2016 se zobrazí okno **Přidat fond,** jak je znázorněno níže:

    ![Přidání fondu z vlastní bitové kopie systému Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Chcete-li zkontrolovat, zda je existující fond založen na vlastní bitové kopii, podívejte se na vlastnost **Operační systém** v části souhrn prostředků v okně **Fondu.** Pokud byl fond vytvořen z vlastní bitové kopie, je nastaven na **vlastní image virtuálního virtuálního mísy**.

Všechny vlastní obrázky přidružené k fondu jsou zobrazeny v okně **Vlastnosti** fondu.

## <a name="considerations-for-large-pools"></a>Důležité informace pro velké fondy

Pokud plánujete vytvořit fond se stovkami virtuálních her nebo více pomocí vlastní image, je důležité postupovat podle předchozích pokynů použít image vytvořenou ze snímku virtuálního počítače.

Všimněte si také následující chod:

- **Omezení velikosti** – Batch omezuje velikost fondu na 2500 vyhrazených výpočetních uzlů nebo 1000 uzlů s nízkou prioritou při použití vlastní image.

  Pokud k vytvoření více fondů použijete stejný obrázek (nebo více bitových kopií na základě stejného podkladového snímku), celkový počet výpočetních uzlů ve fondech nesmí překročit předchozí limity. Nedoporučujeme používat bitovou kopii nebo jeho základní snímek pro více než jeden fond.

  Limity mohou být sníženy, pokud nakonfigurujete fond s [příchozími fondy NAT](pool-endpoint-configuration.md).

- **Změna časového času velikosti** – pokud fond obsahuje pevný počet uzlů (není automatické škálování), zvýšit resizeTimeout vlastnost fondu na hodnotu, jako je například 20-30 minut. Pokud váš fond nedosáhne cílové velikosti v časovém odpočívat, proveďte další [operaci změny velikosti](/rest/api/batchservice/pool/resize).

  Pokud plánujete fond s více než 300 výpočetních uzlů, budete muset změnit velikost fondu vícekrát k dosažení cílové velikosti.
  
Pomocí [Galerie sdílených obrázků](batch-sig-images.md)můžete vytvořit větší fondy s přizpůsobenými bitovými kopiemi spolu s dalšími replikami sdílených bitových kopií. Pomocí sdílených bitových kopií je doba potřebný pro fond k dosažení ustáleného stavu až o 25 % rychlejší a latence nečinnosti virtuálního uživatele je až o 30 % kratší.

## <a name="considerations-for-using-packer"></a>Důležité informace o používání packeru

Vytvoření prostředku spravované bitové kopie přímo s Packer lze provést pouze s režimem předplatného uživatele Dávkové účty. U účtů dávkového servisního režimu je třeba nejprve vytvořit virtuální pevný disk a poté importovat virtuální pevný disk do spravovaného prostředku bitové kopie. V závislosti na režimu přidělení fondu (předplatné uživatele nebo dávková služba) se postup vytvoření prostředku spravované bitové kopie bude lišit.

Ujistěte se, že prostředek použitý k vytvoření spravované bitové kopie existuje po dobu životnosti libovolného fondu odkazující na vlastní bitovou kopii. Pokud tak neučiníte, může dojít k selhání přidělení fondu nebo k chybám změny velikosti.

Pokud je obrázek nebo základní prostředek odebrán, může `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`se stát, že se zobrazí chyba podobná: . Pokud se zobrazí tato chyba, ujistěte se, že základní prostředek nebyl odebrán.

Další informace o použití Packer u vytvoření virtuálního počítače, najdete v [tématu vytvoření bitové kopie Linuxu s Packer](../virtual-machines/linux/build-image-with-packer.md) nebo [sestavení bitové kopie systému Windows s Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Další kroky

Podrobný přehled batch, najdete v tématu [Vývoj rozsáhlých paralelních výpočetních řešení s Batch](batch-api-basics.md).
