---
title: Běžné problémy při vytváření virtuálního pevného disku (FAQ)
description: Nejčastější dotazy k běžným problémům při vytváření virtuálního pevného disku (VHD).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: f4d30cdb931d6523eba3aac003caeee38a8c024d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653524"
---
# <a name="common-issues-during-vhd-creation"></a>Běžné problémy při vytváření virtuálního pevného disku

Tyto nejčastější dotazy se týkají běžných problémů, se kterými se můžete setkat při vytváření virtuálního pevného disku (VHD) pro nabídku virtuálních počítačů Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Návody vytvořit virtuální počítač z Azure Portal pomocí virtuálního pevného disku ve službě Premium Storage?

Azure Marketplace v současné době nepodporuje vytváření nabídek virtuálních počítačů z imagí ve spravovaném úložišti nebo z Azure Premium Storage. Podrobnosti najdete v tématu [Přehled Azure Managed disks](../../virtual-machines/managed-disks-overview.md).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Můžu pro nabídky použít virtuální počítače generace 2?

Ne, podporují se jenom virtuální pevné disky 1. generace. Momentálně ale spolupracujeme s týmem Microsoft Azure platformou, abychom prošetřili podporu pro virtuální počítače 2. generace. Podrobnosti najdete v tématu [Vytvoření virtuálního počítače generace 1 nebo 2 v Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) .

## <a name="how-do-i-change-the-name-of-the-host"></a>Návody změnit název hostitele?

Nemůžete. Po vytvoření virtuálního počítače už uživatelé (včetně vlastníků) nemůžou aktualizovat název hostitele.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Návody obnovit službu Vzdálená plocha nebo přihlašovací heslo?

Tyto články vysvětlují, jak provést obnovení služby RDS pro virtuální počítače se systémem Windows a Linux:

* [Jak resetovat službu Vzdálená plocha nebo její přihlašovací heslo ve virtuálním počítači s Windows](/azure/virtual-machines/troubleshooting/reset-rdp)
* [Postup resetování hesla nebo klíče SSH pro Linux, oprava konfigurace SSH a kontrola konzistence disku pomocí rozšíření VMAccess](/azure/virtual-machines/extensions/vmaccess)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Návody generovat nové certifikáty SSH?

Generování certifikátů je vysvětleno v [certifikátu pro image virtuálních počítačů Azure](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Návody nakonfigurovat virtuální privátní síť (VPN) pro práci s vlastními virtuálními počítači?

Pokud používáte model nasazení Azure Resource Manager, máte k dispozici tři možnosti:

* [Vytvoření brány sítě VPN založené na trasách pomocí Azure Portal](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Vytvoření brány sítě VPN založené na trasách pomocí Azure PowerShell](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [Vytvoření brány sítě VPN založené na trasách pomocí rozhraní příkazového řádku](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Jaké jsou zásady podpory Microsoftu pro používání serverového softwaru Microsoftu na virtuálních počítačích založených na Azure?

Podrobnosti najdete na [webu podpora serverového softwaru Microsoftu pro Microsoft Azure virtuálních počítačů](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Mají virtuální počítače přidružené jedinečné identifikátory?

Ano, pokud je hostována v Azure. Azure přiřadí jedinečný identifikátor, který se označuje jako [jedinečné ID virtuálního počítače Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/), pro každý vytvořený nový prostředek virtuálního počítače. Podrobnosti najdete v tématu jedinečné ID virtuálního počítače Azure. Tento identifikátor lze také získat prostřednictvím [rozhraní API seznamu](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Jak se ve virtuálním počítači dá spravovat rozšíření vlastních skriptů v úloze po spuštění?

Podrobnosti o použití rozšíření vlastních skriptů pomocí modulu Azure PowerShell, Azure Resource Manager šablon a postupu při řešení potíží v systémech Windows najdete v tématu [rozšíření vlastních skriptů pro Windows](/azure/virtual-machines/extensions/custom-script-windows).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Jsou 32 aplikace nebo služby podporované v Azure Marketplace?

Obecně to možné není. Podporované operační systémy a standardní služby pro virtuální počítače Azure jsou v 64 bitů. I když většina 64 operačních systémů podporuje 32 verzí aplikací pro zpětnou kompatibilitu, použití 32 bitových aplikací jako součást řešení pro virtuální počítače se nepodporuje a důrazně se nedoporučuje. Znovu vytvořte aplikaci jako 64 projekt.

Další informace najdete v těchto článcích:

* [Spouštění 32 aplikací](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Podpora 32bitových operačních systémů ve virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Podpora serverového softwaru Microsoft pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Chyba: virtuální pevný disk je už zaregistrovaný s úložištěm imagí jako prostředek

Pokaždé, když se pokusím vytvořit image z mých virtuálních pevných disků, zobrazí se chyba "virtuální pevný disk je už v úložišti imagí zaregistrovaný jako prostředek" v Azure PowerShell. Nevytvořil (a) jsem obrázek, ale v Azure nebyl nalezen žádný obrázek s tímto názvem. Jak to můžu vyřešit?

K tomuto problému obvykle dochází, pokud jste vytvořili virtuální počítač z virtuálního pevného disku, který je na něm zamčený. Potvrďte, že z tohoto virtuálního pevného disku není přidělený žádný virtuální počítač, a potom zkuste operaci zopakovat. Pokud se tento problém opakuje, otevřete lístek podpory. Viz [Podpora partnerského centra](support.md).
