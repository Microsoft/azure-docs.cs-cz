---
title: Azure Lab Services – nahrání vlastní image do galerie sdílených imagí
description: Popisuje, jak nahrát vlastní image do galerie sdílených imagí. IT oddělení IT uvidí import imagí, obzvláště užitečné.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: cd701215eb375b7f9b867ba05082afc7ed348ff7
ms.sourcegitcommit: 5b69ba21787c07547edfbfd5254eaf34315cfadd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712390"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Nahrání vlastní image do galerie sdílených imagí

Galerie sdílených imagí je dostupná pro import vlastních imagí pro vytváření cvičení v Azure Lab Services. IT oddělení v oblasti IT uvidí import imagí obzvláště užitečné z následujících důvodů: 

* Nemusíte vytvářet image ručně pomocí virtuálního počítače šablony testovacího prostředí.
* Můžete nahrávat obrázky vytvořené pomocí jiných nástrojů, jako je SCCM, Endpoint Manager atd.

Tento článek popisuje kroky, které je možné provést k uvedení vlastní image a jejímu použití v Azure Lab Services. 

> [!IMPORTANT]
> Při přesunu imagí z fyzického testovacího prostředí na az Labs je potřeba změnit jejich strukturu appropriatly. Nepoužívejte jednoduše opakované využití stávajících imagí z fyzických cvičení. <br/>Podrobnosti najdete v příspěvku na blogu, který se [přesouvá z fyzického testovacího prostředí na Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) Blogový příspěvek.

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Přenesení vlastní image z fyzického testovacího prostředí

Následující kroky ukazují, jak naimportovat vlastní image, která začíná z fyzického testovacího prostředí. Virtuální pevný disk se pak vytvoří z tohoto prostředí a naimportuje do galerie sdílených imagí v Azure, aby se mohl použít v Azure Lab Services.

Existuje mnoho možností pro vytvoření VHD z fyzického testovacího prostředí. Následující kroky ukazují, jak vytvořit virtuální pevný disk z virtuálního počítače s Windows Hyper-V:

1. Začněte s VIRTUÁLNÍm počítačem Hyper-V ve vašem fyzickém testovacím prostředí, které se vytvořilo z image.
    1. Virtuální počítač musí být vytvořený jako virtuální počítač 1. generace.
    1. Virtuální počítač musí používat pevnou velikost disku. V tomto okně můžete také zadat velikost disku. Velikost disku nesmí být větší než 128 GB.<br/>    
    Azure Lab Services nepodporuje image s velikostí disku > 128 GB. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Připojit virtuální pevný disk":::   
    1. Image virtuálního počítače obvyklým způsobem.
1. [Připojte se k virtuálnímu počítači a připravte ho pro Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).
    1. [Nastavení konfigurací Windows pro Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#set-windows-configurations-for-azure)
    1. [Zkontrolujte služby systému Windows, které jsou potřeba k zajištění připojení virtuálního počítače.](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#check-the-windows-services)
    1. [Aktualizovat nastavení registru vzdálené plochy](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#update-remote-desktop-registry-settings)
    1. [Konfigurace pravidel brány Windows Firewall](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#configure-windows-firewall-rules)
    1. Nainstalovat aktualizace Windows
    1. [Nainstalujte agenta virtuálního počítače Azure a další konfiguraci, jak je znázorněno zde.](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#complete-the-recommended-configurations) 
    
    Výše uvedené kroky vytvoří specializovanou bitovou kopii. Pokud vytváříte zobecněnou bitovou kopii, budete také muset spustit [Nástroj Sysprep](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#determine-when-to-use-sysprep). <br/>
        Pokud chcete udržovat adresář uživatele (který může obsahovat soubory, informace o uživatelském účtu atd.), který je potřeba pro software obsažený v imagi, měli byste vytvořit specializovaný obrázek.
1. Vzhledem k tomu, že **technologie Hyper-V** vytvoří ve výchozím nastavení soubor **VHDX** , budete ho muset převést na soubor VHD.
    1. Přejděte na **akci Správce technologie Hyper-V**  ->  **Action**  ->  **Upravit disk**.
    1. Tady budete mít možnost **převést** disk z VHDX na VHD.
    1. Když se pokusíte zvětšit velikost disku, ujistěte se, že nepřekračuje 128 GB.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Připojit virtuální pevný disk" Azure Portal. Jak už bylo zmíněno, velikost musí být > 128 GB.
1. Pořídit snímek spravovaného disku.
    To se dá udělat buď z PowerShellu, pomocí Azure Portal, nebo v rámci Průzkumník služby Storage, jak je popsáno v tématu [vytvoření snímku pomocí portálu nebo PowerShellu](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).
1. V galerii sdílených imagí vytvořte definici a verzi Image:
    1. [Vytvořte definici obrázku](https://docs.microsoft.com/azure/virtual-machines/windows/shared-images-portal#create-an-image-definition).
    1. Je nutné zadat také zde, zda vytváříte specializovanou a zobecněnou bitovou kopii.
1. Vytvořte testovací prostředí v Azure Lab Services a vyberte vlastní image z Galerie sdílených imagí.

    Pokud jste rozšířili disk po instalaci operačního systému na původní virtuální počítač Hyper-V, budete muset také rozšířit jednotku C v systému Windows tak, aby používala nepřidělené místo na disku. Pokud to chcete provést, přihlaste se k virtuálnímu počítači šablony po vytvoření testovacího prostředí a pak postupujte podle kroků uvedených v části [Rozšířené základní svazek](https://docs.microsoft.com/windows-server/storage/disk-management/extend-a-basic-volume). K tomu můžete využít tyto možnosti uživatelského rozhraní a také pomocí prostředí PowerShell.

## <a name="next-steps"></a>Další kroky

* [Přehled Galerie sdílených imagí](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)
* [Jak používat galerii sdílených imagí](how-to-use-shared-image-gallery.md)
