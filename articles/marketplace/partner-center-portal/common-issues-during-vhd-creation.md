---
title: Běžné problémy při vytváření virtuálního pevného disku (FAQ)
description: Nejčastější dotazy týkající se běžných problémů při vytváření virtuálního pevného disku (VHD).
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266263"
---
# <a name="common-issues-during-vhd-creation"></a>Běžné problémy při vytváření virtuálního pevného disku

> [!NOTE]
> Přesouváme správu nabídek virtuálních virtuálních zařízení Azure z portálu partnerů cloudu do Centra partnerů. Dokud nebudou vaše nabídky migrovány, pokračujte v běžných [problémech během vytváření virtuálních pevných disků (FAQ)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) na portálu cloudových partnerů a spravujte své nabídky.

Tyto často kladené otázky (FAQ) pokrývají běžné problémy, se kterými se můžete setkat při vytváření virtuálního pevného disku (VHD) pro vaši nabídku virtuálního počítače Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Jak vytvořím virtuální počítač z portálu Azure pomocí virtuálního pevného disku v úložišti premium?

Azure Marketplace momentálně nepodporuje vytváření nabídek virtuálních her z ibi ve spravovaném úložišti nebo z Azure Premium Storage. Podrobnosti najdete [v tématu Přehled spravovaných disků Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Můžu pro nabídky použít virtuální chod generace 2?

Ne, jsou podporovány pouze virtuální disponály generace 1. V současné době však spolupracujeme s týmem platformy Microsoft Azure na prozkoumání podpory virtuálních počítačů generace 2. Podrobnosti najdete [v tématu Mám vytvořit virtuální počítač generace 1 nebo 2 v hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Jak změním název hostitele?

To nemůžeš. Po vytvoření virtuálního počítačů uživatelé (včetně vlastníků) nemohou aktualizovat název hostitele.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Jak resetuji službu Vzdálená plocha nebo její přihlašovací heslo?

Tyto články vysvětlují, jak provádět obnovení rds pro virtuální počítače se systémem Windows a Linux:

* [Jak resetovat službu Vzdálená plocha nebo její přihlašovací heslo ve virtuálním počítači s Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Jak resetovat heslo virtuálního počítače linuxu nebo klíč SSH, opravit konfiguraci SSH a zkontrolovat konzistenci disku pomocí rozšíření VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Jak vygenerujem nové certifikáty SSH?

Generování certifikátů je vysvětleno v [certifikaci image virtuálního počítače Azure](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak nakonfiguruji virtuální privátní síť (VPN) pro práci s virtuálními počítači?

Pokud používáte model nasazení Azure Resource Manager, máte tři možnosti:

* [Vytvoření brány VPN založené na trase pomocí portálu Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Vytvoření brány VPN založené na trase pomocí Azure PowerShellu](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Vytvoření brány VPN založené na trasách pomocí cli](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Co jsou zásady podpory Microsoftu pro spouštění serverového softwaru Microsoft u virtuálních aplikací založených na Azure?

Podrobnosti najdete na [webu podpory softwaru microsoftu pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Mají k nim virtuální počítače přidružené jedinečné identifikátory?

Ano, pokud hostované v Azure. Azure přiřadí jedinečný identifikátor, nazývaný [Jedinečné ID virtuálního počítače Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/), každému nově vytvořenému prostředku virtuálního počítače. Podrobnosti najdete v tématu Jedinečné ID virtuálního počítače. Tento identifikátor můžete také získat prostřednictvím [rozhraní LIST API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Jak ve virtuálním virtuálním ms spravovat rozšíření vlastního skriptu v úlohě při spuštění?

Podrobnosti o použití rozšíření Vlastní skript pomocí modulu Azure PowerShell, šablony Azure Resource Manager a postupy řešení potíží v systémech Windows, najdete v [tématu Vlastní skript rozšíření pro Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Jsou 32bitové aplikace nebo služby podporované na Azure Marketplace?

Obecně to možné není. Podporované operační systémy a standardní služby pro virtuální počítače Azure jsou všechny 64bitové. Ačkoli většina 64bitových operačních systémů podporuje 32bitové verze aplikací pro zpětnou kompatibilitu, použití 32bitových aplikací jako součásti řešení virtuálních aplikací není podporováno a důrazně nedoporučuje. Znovu vytvořte aplikaci jako 64bitový projekt.

Další informace najdete v těchto článcích:

* [Spouštění 32bitových aplikací](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Podpora 32bitových operačních systémů ve virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Podpora serverového softwaru Microsoft pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Chyba: Virtuální pevný disk je již registrován v úložišti obrázků jako prostředek.

Pokaždé, když se pokusím vytvořit image z mých virtuálních pevných disků, zobrazí se chyba "Virtuální pevný disk je již registrován pomocí úložiště obrázků jako prostředek" v Azure PowerShellu. Nevytvořil jsem žádnou bitovou kopii dříve, ani jsem nenašel žádnou bitovou kopii s tímto názvem v Azure. Jak to můžu vyřešit?

Tento problém se obvykle zobrazí, pokud jste vytvořili virtuální počítač z virtuálního počítače, který má zámek na to. Zkontrolujte, zda z tohoto virtuálního pevného disku není přidělen žádný virtuální virtuální hod, a opakujte operaci. Pokud tento problém přetrvává, otevřete lístek podpory. Viz [Podpora partnerského centra](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
