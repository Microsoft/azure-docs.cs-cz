---
title: Běžné problémy při vytváření virtuálních pevných disků (FAQ) pro Azure Marketplace
description: Nejčastější dotazy týkající se vytváření virtuálních pevných disků a souvisejících problémů.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: dsindona
ms.reviewer: v-divte; v-miclar
ms.openlocfilehash: 192fea4101fb972ec66bb5f21a2a83f9903f0855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278105"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Běžné problémy při vytváření virtuálního pevného disku (FAQ)

Následující nejčastější dotazy (FAQ) pokrývají běžné problémy, se kterými se vyskytly při vytváření virtuálního pevného disku (VHD) a virtuálního počítače pro nabídky virtuálních počítačů. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Jak vytvořit virtuální počítač z portálu Azure pomocí virtuálního pevného disku, který se nahraje do úložiště premium?

Azure Marketplace momentálně nepodporuje vytváření nabídek virtuálních her z ibi, které se zvedat ve spravovaném úložišti nebo z Azure Premium Storage.  Další informace o těchto možnostech úložiště najdete [v tématu Přehled spravovaných disků Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Můžete použít generace 2 virtuálních měn pro nabídky?

Ne, jsou podporovány pouze virtuální disponály generace 1.  V současné době však spolupracujeme s týmem platformy Microsoft Azure na prozkoumání podpory pro virtuální počítače generace 2.  Další informace o rozdílech najdete v [tématu Mám vytvořit virtuální počítač generace 1 nebo 2 v technologii Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Jak změníte název hostitele?

To nemůžeš.  Po vytvoření virtuálního počítačů uživatelé (včetně vlastníků) nemůže aktualizovat název hostitele.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Jak resetujete službu Vzdálená plocha nebo její přihlašovací heslo?

Následující články vysvětlují, jak provádět obnovení rds pro virtuální počítače se systémem Windows a Linux:   

- [Jak resetovat službu Vzdálená plocha nebo její přihlašovací heslo ve virtuálním počítači s Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Jak resetovat heslo virtuálního počítače linuxu nebo klíč SSH, opravit konfiguraci SSH a zkontrolovat konzistenci disku pomocí rozšíření VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Jak generujete nové certifikáty SSH?

Generování certifikátů je vysvětleno v článku [Získání identifikátoru URI podpisu sdíleného přístupu pro bitovou kopii virtuálního počítače](./cpp-get-sas-uri.md) v následující části [Vytvoření technických prostředků pro nabídku virtuálního počítače](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak nakonfigurujete virtuální privátní síť (VPN) pro práci s virtuálními počítači?

Pokud používáte model nasazení Azure Resource Manager, pak máte tři běžné možnosti nastavení VPN:
- [Vytvoření brány VPN založené na trase pomocí portálu Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Vytvoření brány VPN založené na směrování pomocí PowerShellu](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Vytvoření brány VPN založené na trasách pomocí cli](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Co jsou zásady podpory Microsoftu pro spouštění serverového softwaru Microsoft u virtuálních aplikací založených na Azure?

Tyto zásady podpory jsou podrobně popsány v článku [Podpora softwaru serveru Microsoft pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Mají k nim virtuální počítače přidružené jedinečné identifikátory?

Ano, pokud hostované v Azure.  Azure přiřadí jedinečný identifikátor s názvem Jedinečné ID virtuálního počítače Azure ke každému novému prostředku virtuálního počítače, který se vytvoří.  Další informace načtou na blogový příspěvek [jedinečného ID virtuálního počítače Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Tento identifikátor můžete také získat programově prostřednictvím [rozhraní List API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Jak ve virtuálním virtuálním ms spravujete rozšíření vlastního skriptu v úlohě při spuštění?

V následujícím článku najdete podrobnosti o použití rozšíření Custom Script Extension pomocí modulu Azure PowerShell, šablon Azure Resource Manager a podrobností o postupech řešení potíží v systémech Windows: [Vlastní rozšíření skriptu pro Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Jsou 32bitové aplikace nebo služby podporované na Azure Marketplace?

Obecně to možné není.  Podporované operační systémy a standardní služby pro virtuální počítače Azure jsou všechny 64bitové.  Z technického hlediska však většina 64bitových operačních systémů podporuje spouštění 32bitových verzí aplikací pro zpětnou kompatibilitu.  Použití 32bitových aplikací jako součásti řešení virtuálního počítače však není *podporováno,* a proto se důrazně nedoporučuje.  Místo toho překompilujte aplikaci jako 64bitový projekt.

Další informace najdete v těchto článcích:
- [Spouštění 32bitových aplikací](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Podpora 32bitových operačních systémů ve virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Podpora serverového softwaru Microsoft pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Pokaždé, když se snažím vytvořit obrázek z mých Virtuální `.VHD is already registered with image repository as the resource` chudinských vozidel, dostanu chybu v Prostředí PowerShell. Nevytvořil jsem žádnou bitovou kopii dříve, ani jsem nenašel žádnou bitovou kopii s tímto názvem v Azure. Jak tento problém vyřeším?

K tomuto problému obvykle dochází, pokud uživatel zřídit virtuální počítač z virtuálního pevného disku, který má zámek na něj.  Ověřte, že z tohoto virtuálního pevného disku není přidělen žádný virtuální virtuální disk, a opakujte operaci.  Pokud tento problém přetrvává, otevřete lístek podpory, jak je vysvětleno v [podpoře portálu partnerů cloudu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

