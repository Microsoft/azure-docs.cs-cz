---
title: Běžné dotazy k virtuálnímu počítači v Azure Marketplace
description: Při vytváření virtuálního počítače v Azure Marketplace se zjistily běžné otázky.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 88ec58ca9bcfa5c64036d3b65e77f3248e26357d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124947"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Běžné dotazy k virtuálnímu počítači v Azure Marketplace

Tyto nejčastější dotazy se týkají běžných problémů, se kterými se můžete setkat při vytváření nabídky virtuálního počítače v Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Návody nakonfigurovat virtuální privátní síť (VPN) pro práci s vlastními virtuálními počítači?

Pokud používáte model nasazení Azure Resource Manager, máte k dispozici tři možnosti:

- [Vytvoření brány sítě VPN založené na trasách pomocí Azure Portal](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Vytvoření brány sítě VPN založené na trasách pomocí Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Vytvoření brány sítě VPN založené na trasách pomocí rozhraní příkazového řádku](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Jaké jsou zásady podpory Microsoftu pro používání serverového softwaru Microsoftu na virtuálních počítačích založených na Azure?

Podrobnosti najdete na [webu podpora serverového softwaru Microsoftu pro Microsoft Azure virtuálních počítačů](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Jak se ve virtuálním počítači dá spravovat rozšíření vlastních skriptů v úloze po spuštění?

Podrobnosti o použití rozšíření vlastních skriptů pomocí modulu Azure PowerShell, Azure Resource Manager šablon a postupu při řešení potíží v systémech Windows najdete v tématu [rozšíření vlastních skriptů pro Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Jsou 32 aplikace nebo služby podporované v Azure Marketplace?

Ne. Podporované operační systémy a standardní služby pro virtuální počítače Azure jsou v 64 bitů. I když většina 64 operačních systémů podporuje 32 verzí aplikací pro zpětnou kompatibilitu, použití 32 bitových aplikací jako součást řešení pro virtuální počítače se nepodporuje a důrazně se nedoporučuje. Znovu vytvořte aplikaci jako 64 projekt.

Další informace najdete v těchto článcích:

- [Spouštění 32 aplikací](/windows/desktop/WinProg64/running-32-bit-applications)
- [Podpora 32bitových operačních systémů ve virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Podpora serverového softwaru Microsoft pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Chyba: virtuální pevný disk je už zaregistrovaný s úložištěm imagí jako prostředek

Pokaždé, když se pokusím vytvořit image z mých virtuálních pevných disků, zobrazí se chyba "virtuální pevný disk je už v úložišti imagí zaregistrovaný jako prostředek" v Azure PowerShell. Nevytvořil (a) jsem obrázek, ale v Azure nebyl nalezen žádný obrázek s tímto názvem. Jak to můžu vyřešit?

K tomuto problému obvykle dochází, pokud jste vytvořili virtuální počítač z virtuálního pevného disku, který je na něm zamčený. Potvrďte, že z tohoto virtuálního pevného disku není přidělený žádný virtuální počítač, a potom zkuste operaci zopakovat. Pokud se tento problém opakuje, otevřete lístek podpory. Viz [Podpora partnerského centra](support.md).

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s certifikacemi virtuálních počítačů](azure-vm-create-certification-faq.md)