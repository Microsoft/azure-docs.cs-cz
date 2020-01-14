---
title: Běžné problémy při vytváření virtuálního pevného disku (FAQ) pro Azure Marketplace
description: Nejčastější dotazy týkající se vytvoření virtuálního pevného disku a souvisejících problémů.
services: Azure Marketplace
author: MaggiePucciEvans
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/02/2018
ms.author: evansma
ms.reviewer: v-divte; v-miclar
ms.openlocfilehash: 152a2f6fda081b2612197cc4848207b336adf8dc
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934266"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Běžné problémy při vytváření virtuálního pevného disku (FAQ)

Na následujících nejčastějších dotazech se týkají běžné problémy zjištěné během vytváření virtuálních pevných disků (VHD) a virtuálních počítačů pro nabídky virtuálních počítačů. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Jak vytvořit virtuální počítač z Azure Portal pomocí virtuálního pevného disku, který se nahrává do služby Premium Storage?

Azure Marketplace v současné době nepodporuje vytváření nabídek virtuálních počítačů z imagí umístěných ve spravovaném úložišti nebo z Azure Premium Storage.  Další informace o těchto možnostech úložiště najdete v tématu [Přehled Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Můžete pro nabídky použít virtuální počítače generace 2?

Ne, podporují se jenom virtuální pevné disky 1. generace.  Momentálně ale spolupracujeme s týmem Microsoft Azure platformou, abychom prošetřili podporu pro virtuální počítače 2. generace.  Další informace o rozdílech najdete v tématu [Vytvoření virtuálního počítače generace 1 nebo 2 v technologii Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) .


## <a name="how-do-you-change-the-name-of-the-host"></a>Jak můžete změnit název hostitele?

Nemůžete.  Po vytvoření virtuálního počítače uživatelé (včetně vlastníků) nemůžou aktualizovat název hostitele.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Jak resetujete službu Vzdálená plocha nebo přihlašovací heslo?

Následující články vysvětlují, jak provést obnovení služby RDS pro virtuální počítače se systémem Windows a Linux:   

- [Jak resetovat službu Vzdálená plocha nebo její přihlašovací heslo ve virtuálním počítači s Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Postup resetování hesla nebo klíče SSH pro Linux, oprava konfigurace SSH a kontrola konzistence disku pomocí rozšíření VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Jak vygenerujete nové certifikáty SSH?

Generování certifikátů je vysvětleno v článku [Získání identifikátoru URI sdíleného přístupového podpisu pro vaši image virtuálního počítače](./cpp-get-sas-uri.md) v následující části [Vytvoření technických prostředků pro nabídku virtuálních počítačů](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak nakonfigurujete virtuální privátní síť (VPN) pro práci s vlastními virtuálními počítači?

Pokud používáte model nasazení Azure Resource Manager, máte k dispozici tři běžné možnosti nastavení sítě VPN:
- [Vytvoření brány sítě VPN založené na trasách pomocí Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Vytvoření brány sítě VPN založené na trasách pomocí prostředí PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Vytvoření brány sítě VPN založené na trasách pomocí rozhraní příkazového řádku](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Jaké jsou zásady podpory Microsoftu pro používání serverového softwaru Microsoftu na virtuálních počítačích založených na Azure?

Tyto zásady podpory jsou podrobně popsané v článku [podpora serverového softwaru společnosti Microsoft pro Microsoft Azure virtuálních počítačů](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Mají virtuální počítače přidružené jedinečné identifikátory?

Ano, pokud je hostována v Azure.  Azure přiřadí jedinečný identifikátor nazvaný jedinečné ID virtuálního počítače Azure ke každému nově vytvořenému prostředku virtuálního počítače.  Další informace najdete v blogu o [jedinečném ID virtuálního počítače Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Tento identifikátor lze také získat programově prostřednictvím [rozhraní list API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Jak ve virtuálním počítači spravujete rozšíření vlastních skriptů v úloze po spuštění?

Následující článek podrobně popisuje, jak používat rozšíření vlastních skriptů pomocí modulu Azure PowerShell, Azure Resource Manager šablony a podrobně popisuje postup řešení potíží v systémech Windows: [rozšíření vlastních skriptů pro Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/) .


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Jsou 32 aplikace nebo služby podporované v Azure Marketplace?

Obecně to možné není.  Podporované operační systémy a standardní služby pro virtuální počítače Azure jsou v 64 bitů.  Z technického hlediska ale většina 64 operačních systémů podporuje spouštění 32 verzí aplikací z důvodu zpětné kompatibility.  Používání 32ch aplikací v rámci řešení virtuálních počítačů se ale nepodporuje, takže se proto *důrazně nedoporučuje*.  Místo toho aplikaci znovu zkompilujte jako 64 projekt.

Další informace najdete v těchto článcích:
- [Spouštění 32 aplikací](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Podpora pro 32 operační systémy ve virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Podpora serverového softwaru Microsoft pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Pokaždé, když se pokusím vytvořit image z mých virtuálních pevných disků, zobrazí se chyba `.VHD is already registered with image repository as the resource` PowerShellu. Nevytvořil (a) jsem obrázek, ale v Azure nebyl nalezen žádný obrázek s tímto názvem. Návody vyřešit tento problém?

K tomuto problému obvykle dochází v případě, že uživatel zřídil virtuální počítač z virtuálního pevného disku, který na něm má zámek.  Ověřte, že tento virtuální pevný disk nemá přidělený žádný virtuální počítač, a potom zkuste operaci zopakovat.  Pokud s tím budou dál problémy, otevřete lístek podpory, jak je vysvětleno v tématu [podpora portál partnerů cloudu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

