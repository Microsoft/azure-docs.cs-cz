---
title: Vytvořte nabídku virtuálních počítačů Azure (VM) z schválené základní Azure Marketplace
description: Přečtěte si, jak vytvořit nabídku virtuálního počítače z schválené základny.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: c28595458fea61f66b6930ce72fa0702e00aecd9
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129273"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Postup vytvoření virtuálního počítače pomocí schválené základní třídy

Tento článek popisuje, jak pomocí Azure vytvořit virtuální počítač s předem nakonfigurovaným schváleným operačním systémem. Pokud to není kompatibilní s vaším řešením, je možné [vytvořit a nakonfigurovat místní virtuální počítač](azure-vm-create-using-own-image.md) pomocí schváleného operačního systému a potom ho nakonfigurovat a připravit na nahrání, jak je popsáno v tématu [Příprava virtuálního pevného disku (VHD) Windows pro nahrání do Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!NOTE]
> Před zahájením tohoto postupu si přečtěte [technické požadavky](marketplace-virtual-machines.md#technical-requirements) pro nabídky virtuálních počítačů Azure, včetně požadavků na virtuální pevný disk (VHD).

## <a name="select-an-approved-base-image"></a>Výběr schválené základní image

Jako základ vyberte jednu z následujících imagí systému Windows nebo Linux.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure nabízí řadu schválených distribucí Linux. Aktuální seznam najdete v tématu [Linux v distribucích, které jsou schváleny v Azure](../virtual-machines/linux/endorsed-distros.md).

## <a name="create-vm-on-the-azure-portal"></a>Vytvoření virtuálního počítače na Azure Portal

1. Přihlaste se na [Azure Portal](https://ms.portal.azure.com/).
2. Vyberte **Virtuální počítače** .
3. Výběrem **+ Přidat** otevřete obrazovku **vytvořit virtuální počítač** .
4. Vyberte obrázek v rozevíracím seznamu nebo vyberte **Procházet všechny veřejné a soukromé image** , aby bylo možné vyhledávat nebo procházet všechny dostupné image virtuálních počítačů.
5. Virtuální počítač **2. generace** vytvoříte tak, že přejdete na kartu **Upřesnit** a vyberete možnost **Obecné 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Vyberte Obecné 1 nebo obecné 2.":::

6. Vyberte velikost virtuálního počítače, který se má nasadit.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Vyberte Obecné 1 nebo obecné 2.":::

7. Zadejte další požadované podrobnosti pro vytvoření virtuálního počítače.
8. Vyberte **zkontrolovat + vytvořit** a zkontrolujte své volby. Jakmile se zobrazí zpráva **ověření proběhlo** , vyberte  **vytvořit** .

Azure zahájí zřizování virtuálního počítače, který jste zadali. V nabídce vlevo vyberte kartu **Virtual Machines** a sledujte její průběh. Po vytvoření se stav virtuálního počítače změní na **spuštěno** .


## <a name="configure-the-vm"></a>Nakonfigurování virtuálního počítače

Tato část popisuje, jak změnit velikost, aktualizovat a zobecnit virtuální počítač Azure. Tyto kroky jsou nezbytné k přípravě nasazení virtuálního počítače na Azure Marketplace.

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

Pokud se chcete připojit k virtuálnímu počítači s [Windows](../virtual-machines/windows/connect-logon.md) nebo [Linuxem](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) , přečtěte si následující dokumentaci.

### <a name="install-the-most-current-updates"></a>Nainstalovat nejaktuálnější aktualizace

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Provedení dalších kontrol zabezpečení

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Provádění vlastních konfigurací a naplánovaných úloh

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Další kroky

- Doporučený další krok: [otestujte image virtuálního počítače](azure-vm-image-test.md) a ujistěte se, že splňuje požadavky na publikování Azure Marketplace. Tento údaj je nepovinný.
- Pokud image virtuálního počítače netestujete, pokračujte v [generování identifikátoru URI SAS](azure-vm-get-sas-uri.md).
- Pokud jste narazili na potíže s vytvářením nového virtuálního pevného disku s Azure, přečtěte si téma [Nejčastější dotazy Azure Marketplace k virtuálním](azure-vm-create-faq.md)počítačům s