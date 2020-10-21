---
title: Vytvoření virtuálního počítače Azure v Azure Marketplace pomocí vlastní image
description: Přečtěte si, jak publikovat nabídku virtuálního počítače a Azure Marketplace pomocí vlastní image.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 42022d1204c3b524ee2e9ef2770f616fba89dc8c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283891"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Jak vytvořit virtuální počítač pomocí vlastní image

Tento článek popisuje, jak vytvořit a nasadit image virtuálního počítače (VM) zadaného uživatelem.

> [!NOTE]
> Před zahájením tohoto postupu si přečtěte [technické požadavky](marketplace-virtual-machines.md#technical-requirements) pro nabídky virtuálních počítačů Azure, včetně požadavků na virtuální pevný disk (VHD).

Pokud místo toho chcete použít schválenou základní bitovou kopii, postupujte podle pokynů v tématu [Vytvoření image virtuálního počítače ze schválené základny](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Nakonfigurování virtuálního počítače

Tato část popisuje, jak změnit velikost, aktualizovat a zobecnit virtuální počítač Azure. Tyto kroky jsou nezbytné k přípravě nasazení virtuálního počítače na Azure Marketplace.

### <a name="size-the-vhds"></a>Velikost virtuálních pevných disků

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Nainstalovat nejaktuálnější aktualizace

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Provedení dalších kontrol zabezpečení

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Provádění vlastních konfigurací a naplánovaných úloh

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

## <a name="upload-the-vhd-to-azure"></a>Nahrání virtuálního pevného disku do Azure

Nakonfigurujte a připravte virtuální počítač, který se má nahrát, jak je popsáno v tématu [Příprava virtuálního pevného disku (VHD) s Windows pro nahrání do Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) nebo [Vytvoření a nahrání virtuálního pevného disku Linux](../virtual-machines/linux/create-upload-generic.md).

## <a name="extract-the-vhd-from-image-if-using-image-building-services"></a>Extrakce virtuálního pevného disku z Image (Pokud se používá služba pro vytváření imagí)

Pokud používáte službu pro vytváření imagí, jako je například [balíček](https://www.packer.io/), možná budete muset z image extrahovat virtuální pevný disk. Neexistuje žádný přímý způsob, jak to provést. Budete muset vytvořit virtuální počítač a extrahovat VHD z disku virtuálního počítače.

### <a name="create-the-vm-on-the-azure-portal"></a>Vytvoření virtuálního počítače na Azure Portal

Pomocí těchto kroků vytvořte na [Azure Portal](https://ms.portal.azure.com/)základní image virtuálního počítače.

1. Přihlaste se na [Azure Portal](https://ms.portal.azure.com/).
2. Vyberte **Virtuální počítače**.
3. Výběrem **+ Přidat** otevřete obrazovku **vytvořit virtuální počítač** .
4. Vyberte obrázek v rozevíracím seznamu nebo vyberte **Procházet všechny veřejné a soukromé image** , aby bylo možné vyhledávat nebo procházet všechny dostupné image virtuálních počítačů.
5. Virtuální počítač **2. generace** vytvoříte tak, že přejdete na kartu **Upřesnit** a vyberete možnost **Obecné 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Vyberte Obecné 1 nebo obecné 2.":::

6. Vyberte velikost virtuálního počítače, který se má nasadit.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Vyberte Obecné 1 nebo obecné 2.":::

7. Zadejte další požadované podrobnosti pro vytvoření virtuálního počítače.
8. Vyberte **zkontrolovat + vytvořit** a zkontrolujte své volby. Jakmile se zobrazí zpráva **ověření proběhlo** , vyberte **vytvořit**.

Azure zahájí zřizování virtuálního počítače, který jste zadali. V nabídce vlevo vyberte kartu **Virtual Machines** a sledujte její průběh. Po vytvoření se stav virtuálních počítačů změní na **spuštěno**.

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

Pokud se chcete připojit k virtuálnímu počítači s [Windows](../virtual-machines/windows/connect-logon.md) nebo [Linuxem](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) , přečtěte si následující dokumentaci.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Další kroky

- Doporučený další krok: [otestujte image virtuálního počítače](azure-vm-image-test.md) a ujistěte se, že splňuje požadavky na publikování Azure Marketplace. Tento údaj je nepovinný.
- Pokud image virtuálního počítače netestujete, pokračujte v [generování identifikátoru URI SAS](azure-vm-get-sas-uri.md).
- Pokud jste narazili na potíže s vytvářením nového virtuálního pevného disku s Azure, přečtěte si téma [Nejčastější dotazy Azure Marketplace k virtuálním](azure-vm-create-faq.md)počítačům s
