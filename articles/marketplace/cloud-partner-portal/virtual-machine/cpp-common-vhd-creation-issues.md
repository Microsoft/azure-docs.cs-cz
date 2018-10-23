---
title: Běžné problémy při vytváření virtuálního pevného disku (FAQ) pro Azure Marketplace | Dokumentace Microsoftu
description: Nejčastější dotazy ohledně vytváření virtuálního pevného disku a související problémy.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: Patrick.Butler
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: 535a947f7a4b9c750d585ce854a14be80c4a135c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639634"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Běžné problémy při vytváření virtuálního pevného disku (FAQ)

Následující nejčastější dotazy (FAQ) titulní běžné problémy při došlo k virtuálního pevného disku (VHD) a virtuální počítač (VM) vytvoření nabídky virtuálních počítačů. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Jak vytvořit virtuální počítač z portálu Azure pomocí virtuálního pevného disku, který se nahraje do služby storage úrovně premium?

Azure Marketplace v současné době nepodporuje vytvoření nabídky virtuálních počítačů z imagí umístěných na spravovaného úložiště nebo ze služby Azure Premium Storage.  Další informace o těchto možnostech úložiště najdete v tématu [Přehled služby Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) a [vysoce výkonné úložiště úrovně Premium a spravovaným diskům pro virtuální počítače](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Můžete použít pro nabídky virtuální počítače generace 2?

Ne, generování pouze 1 virtuální pevné disky jsou podporovány.  Ale Pracujeme s týmem platformy Microsoft Azure k prozkoumání podporu pro virtuální počítače generace 2.  Další informace o rozdílech najdete v tématu [bych si měl vytvořit virtuální počítač generace 1 nebo 2 v Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Jak můžete změnit název hostitele?

Nemůžete.  Po vytvoření virtuálního počítače, uživatele (včetně vlastníky) nelze aktualizovat název hostitele.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Jak můžete obnovit službu Vzdálená plocha nebo jeho heslo přihlášení?

Následující články popisují, jak provádět obnovení nastavení vzdálené plochy pro virtuální počítače s Windows a Linux:   

- [Jak resetovat službu Vzdálená plocha nebo její přihlašovací heslo ve virtuálním počítači s Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Jak obnovit virtuální počítač s Linuxem heslo nebo klíč SSH, opravit konfiguraci SSH a zkontrolovat konzistenci disků pomocí rozšíření VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Jak můžete generovat nové certifikáty SSH?

Generování certifikátů je vysvětleno v článku [Get URI sdíleného přístupového podpisu pro vaši image virtuálního počítače](./cpp-get-sas-uri.md) v další části [vytvářejí technické materiály pro nabídky virtuálních počítačů](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Jak konfigurovat virtuální privátní sítě (VPN) pro práci s svoje virtuální počítače?

Pokud používáte model nasazení Azure Resource Manageru, máte tři běžné možnosti nastavení sítě VPN:
- [Vytvoření trasové brány VPN pomocí webu Azure portal](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Vytvoření trasové brány VPN pomocí Powershellu](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Vytvoření pomocí rozhraní příkazového řádku brány VPN založené na trasách](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Co jsou zásady podpory Microsoftu pro spouštění serverového softwaru Microsoftu na virtuálních počítačích založené na Azure?

Podpora, tyto zásady jsou podrobně popsané v článku [podpora serverového softwaru Microsoftu pro Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Mají virtuální počítače k nim má přiřazené jedinečné identifikátory?

Ano, pokud je hostovaná v Azure.  Azure přiřadí jedinečný identifikátor s názvem virtuálního počítače Azure jedinečné ID pro každý nový prostředek virtuálního počítače, který je vytvořen.  Další informace najdete v blogovém příspěvku [jedinečné ID virtuálního počítače Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Můžete také získat prostřednictvím kódu programu pomocí tohoto identifikátoru [rozhraní API seznamu](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Ve virtuálním počítači jak se spravují pomocí rozšíření vlastních skriptů v úloze po spuštění?

Následující článek podrobně popisuje, jak použít rozšíření vlastních skriptů pomocí modulu Azure PowerShell, šablon Azure Resource Manageru a podrobnosti o řešení potíží v systémech Windows: [vlastní skript rozšíření pro Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Jsou 32bitových aplikací nebo služeb se podporuje na webu Azure Marketplace?

V obecně to možné není.  Podporované operační systémy a standardní služby pro virtuální počítače Azure jsou všechny 64-bit.  Však z hlediska technické nejvíce 64bitové operační systémy podporují spuštění 32bitové verze aplikací z důvodu zpětné kompatibility.  Nicméně použití 32-bit aplikací jako součást řešení pro virtuální počítač není podporována a proto je *důrazně nedoporučuje*.  Místo toho znovu zkompilujte aplikaci jako 64-bit projektu.

Další informace najdete v následujících článcích:
- [Spuštění 32bitové aplikace](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Podpora pro 32bitové operační systémy ve službě Azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Podpora serverového softwaru Microsoftu pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Pokaždé, když se snažím vytvořit image ze své virtuální pevné disky, zobrazí chyba `.VHD is already registered with image repository as the resource` v prostředí PowerShell. Nebyl vytvořen žádný obrázek před ani nebyla v Azure najít žádné image s tímto názvem. Jak vyřešit tento problém?

Tomuto problému obvykle dojde, když uživatel zřízení virtuálního počítače z virtuálního pevného disku, který má zámek na něm.  Ověřte, že neexistuje žádný virtuální počítač přiděluje z tohoto virtuálního pevného disku a pak zkuste operaci zopakovat.  Pokud se problém nevyřeší, otevřete lístek podpory, jak je vysvětleno v [podporu pro portál partnerů cloudu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

