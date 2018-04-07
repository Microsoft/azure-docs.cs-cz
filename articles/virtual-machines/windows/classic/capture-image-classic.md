---
title: Vytvořte bitovou kopii virtuálního počítače Windows Azure | Microsoft Docs
description: Zachycení image virtuálního počítače Azure s Windows vytvořeného pomocí modelu klasického nasazení
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 24366d3adf63fb6fafb8afa9f1a9fb359e88d8ed
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Zachycení image virtuálního počítače Azure s Windows vytvořeného pomocí modelu klasického nasazení
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Informace o modelu Resource Manager, najdete v části [zaznamenat bitovou kopii spravované zobecněný virtuálního počítače v Azure](../capture-image-resource.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Tento článek ukazuje, jak zachytit virtuální počítač Azure se systémem Windows, můžete ho použít jako obrázek pro vytvoření dalších virtuálních počítačů. Tento image obsahuje disk operačního systému a datové disky, které jsou připojené k virtuálnímu počítači. Neobsahuje síťových konfigurací, takže budete muset nastavit konfiguraci sítě, když vytvoříte další virtuální počítače, které použít bitovou kopii.

Obrázek v Azure uloží **Image virtuálních počítačů (klasické)**, **výpočetní** služba, která je uvedena při zobrazení všech služeb Azure. Toto je na stejném místě, kde jsou uložené všechny Image, které jste odeslali. Podrobnosti o bitových kopiích naleznete v tématu [o bitové kopie u virtuálních počítačů](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Než začnete
Tento postup předpokládá, že jste vytvořili virtuální počítač Azure a nakonfigurovat operačního systému, včetně připojení jakýchkoli datových disků. Pokud jste to ještě neudělali, najdete v následujících článcích informace o vytváření a příprava virtuálního počítače:

* [Vytvořit virtuální počítač z bitové kopie](createportal.md)
* [Tom, jak připojit datový disk k virtuálnímu počítači](attach-disk.md)
* Ujistěte se, že role serveru jsou podporované pomocí nástroje Sysprep. Další informace najdete v tématu [podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Tento proces odstraní původní virtuální počítač po jejím zaznamenání.
>
>

Před zachycením bitové kopie virtuálního počítače Azure, se doporučuje zálohovat cílového virtuálního počítače. Virtuální počítače Azure můžete zálohovat pomocí služby Azure Backup. Podrobnosti najdete v článku [Zálohování virtuálních počítačů Azure](../../../backup/backup-azure-arm-vms.md). Další řešení jsou k dispozici od certifikovaných partnerů. Pokud chcete zjistit, co je právě k dispozici, prohledejte web Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Zachytit virtuální počítač
1. V [portál Azure](http://portal.azure.com), **Connect** k virtuálnímu počítači. Pokyny najdete v tématu [jak se přihlásit k virtuálnímu počítači s Windows serverem][How to sign in to a virtual machine running Windows Server].
2. Otevřete okno příkazového řádku jako správce.
3. Změňte adresář na `%windir%\system32\sysprep`, a poté spusťte sysprep.exe.
4. **Nástroj pro přípravu systému** zobrazí se dialogové okno. Udělejte toto:

   * V **Akce čištění systému**, vyberte **prostředí Out-of-Box zadejte systému (při prvním zapnutí)** a ujistěte se, že **generalizace** je zaškrtnuté. Další informace o používání nástroje Sysprep najdete v tématu [postup použití nástroje Sysprep: Úvod][How to Use Sysprep: An Introduction].
   * V **možnosti vypnutí**, vyberte **vypnutí**.
   * Klikněte na **OK**.

   ![Spusťte nástroj Sysprep](./media/capture-image/SysprepGeneral.png)
5. Nástroj Sysprep vypne virtuální počítač, který změní stav virtuálního počítače na portálu Azure **Zastaveno**.
6. Na portálu Azure klikněte na tlačítko **virtuálních počítačů (klasické)** a vyberte virtuální počítač, který chcete zaznamenat. **Image virtuálních počítačů (klasické)** skupina je uvedena v části **výpočetní** při prohlížení **všechny služby**.

7. Na panelu příkazů klikněte na tlačítko **zaznamenat**.

   ![Zachytit virtuální počítač](./media/capture-image/CaptureVM.png)

   **Zachytit virtuální počítač** zobrazí se dialogové okno.

8. V **název bitové kopie**, zadejte název pro novou bitovou kopii. V **popisek bitové kopie**, zadejte popisek pro novou bitovou kopii.

9. Klikněte na tlačítko **na virtuální počítač jste spustit program Sysprep**. Toto políčko odkazuje na akce pomocí nástroje Sysprep v krocích 3 až 5. Obrázek _musí_ zobecněny spuštěním nástroje Sysprep předtím, než přidáte bitovou kopii systému Windows Server do sady vlastních bitových kopií.

10. Po dokončení zachytávání, bude k dispozici v novou bitovou kopii **Marketplace**v **výpočetní**, **Image virtuálních počítačů (klasické)** kontejneru.

    ![Zachycení Image úspěšné](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Další postup
Obrázek je připravený k použití pro vytvoření virtuálního počítače. To provedete tak, že vyberete vytvoříte virtuální počítač **všechny služby** položky nabídky v dolní části nabídky služeb, pak **Image virtuálních počítačů (klasické)** v **výpočetní** skupiny. Pokyny najdete v tématu [vytvořit virtuální počítač z bitové kopie](createportal.md).

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
