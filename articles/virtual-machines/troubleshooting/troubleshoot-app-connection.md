---
title: Řešení potíží s přístupem k aplikacím VM v Azure | Microsoft Docs
description: Pomocí těchto podrobných kroků pro řešení potíží izolujte problémy při připojování k aplikacím běžícím na virtuálních počítačích v Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nelze spustit aplikaci, program nebude otevřen, blokovaný port zablokován, nelze spustit program, port naslouchání zablokován
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a2fe1b8bdc80a5265add22bd3602050034471fe2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87036433"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Řešení potíží s připojením aplikace na virtuálních počítačích v Azure

Pokud nemůžete spustit aplikaci spuštěnou na virtuálním počítači Azure nebo se k ní připojit, máte k dispozici různé důvody. Důvody zahrnují, že aplikace neběží nebo naslouchá na očekávaných portech, naslouchajícímu portu blokováno nebo Síťová pravidla nemusejí správně předávat provoz do aplikace. Tento článek popisuje přístup metodologick k vyhledání a vyřešení problému.

Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači pomocí protokolu RDP nebo SSH, přečtěte si jeden z následujících článků jako první:

* [Řešení potíží s připojením ke vzdálené ploše na virtuálním počítači Azure se systémem Windows](troubleshoot-rdp-connection.md)
* [Řešení potíží s připojením Secure Shell (SSH) k virtuálnímu počítači Azure](troubleshoot-ssh-connection.md)se systémem Linux.

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete se obrátit na odborníky na Azure na [webu MSDN Azure a ve Stack Overflowch fórech](https://azure.microsoft.com/support/forums/). Případně můžete také použít incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

## <a name="quick-start-troubleshooting-steps"></a>Postup řešení potíží s rychlým zahájením
Pokud máte problémy s připojením k aplikaci, zkuste následující obecné kroky pro řešení potíží. Po každém kroku se znovu pokuste připojit k aplikaci:

* Restartování virtuálního počítače
* Opětovné vytvoření pravidel koncového bodu/brány firewall/skupiny zabezpečení sítě (NSG)
  * [Model Správce prostředků – Správa skupin zabezpečení sítě](../../virtual-network/manage-network-security-group.md)
  * [Klasický model – správa koncových bodů Cloud Services](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Připojení z jiného umístění, například z jiné virtuální sítě Azure
* Opětovné nasazení virtuálního počítače
  * [Znovu nasadit virtuální počítač s Windows](redeploy-to-new-node-windows.md)
  * [Znovu nasadit virtuální počítač se systémem Linux](redeploy-to-new-node-linux.md)
* Znovu vytvořit virtuální počítač

Další informace najdete v tématu [řešení potíží s připojením koncových bodů (RDP/SSH/http atd.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Podrobný přehled řešení potíží
Existují čtyři hlavní oblasti pro řešení potíží s přístupem k aplikaci, která běží na virtuálním počítači Azure.

![řešení potíží nemůže spustit aplikaci](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Aplikace spuštěná na virtuálním počítači Azure.
   * Běží aplikace správně?
2. Virtuální počítač Azure.
   * Spouští se virtuální počítač správně a reaguje na požadavky?
3. Koncové body sítě Azure.
   * Koncové body cloudových služeb pro virtuální počítače v modelu nasazení Classic.
   * Skupiny zabezpečení sítě a příchozí pravidla NAT pro virtuální počítače v modelu nasazení Správce prostředků.
   * Může přenos toků z uživatelů do virtuálního počítače nebo aplikace na očekávaných portech?
4. Vaše zařízení Internet Edge.
   * Jsou pravidla brány firewall na místě, která brání správnému přenosu dat ze služby Flow?

Pro klientské počítače, které přistupují k aplikaci přes připojení VPN typu Site-to-site nebo ExpressRoute, můžou hlavní oblasti, které způsobují problémy, být aplikace a virtuální počítač Azure.

Chcete-li zjistit zdroj problému a jeho opravu, postupujte podle těchto kroků.

## <a name="step-1-access-application-from-target-vm"></a>Krok 1: přístup k aplikaci z cílového virtuálního počítače
Pokuste se o přístup k aplikaci přes příslušný klientský program z virtuálního počítače, na kterém je spuštěná. Použijte místní název hostitele, místní IP adresu nebo adresu zpětné smyčky (127.0.0.1).

![spustit aplikaci přímo z virtuálního počítače](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Například pokud je aplikace webovým serverem, otevřete na virtuálním počítači prohlížeč a pokuste se získat přístup k webové stránce hostované na virtuálním počítači.

Pokud máte přístup k aplikaci, přejděte ke [kroku 2](#step2).

Pokud nemůžete získat přístup k aplikaci, ověřte následující nastavení:

* Aplikace běží na cílovém virtuálním počítači.
* Aplikace naslouchá na očekávaných portech TCP a UDP.

Na virtuálních počítačích s Windows i Linuxem Zobrazte aktivní naslouchající porty pomocí příkazu **netstat-a** . Prověřte výstup očekávaných portů, na kterých by měla vaše aplikace naslouchat. Restartujte aplikaci nebo ji nakonfigurujte tak, aby podle potřeby používala očekávané porty, a znovu se pokuste o přístup k aplikaci.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>Krok 2: přístup k aplikaci z jiného virtuálního počítače ve stejné virtuální síti
Pokuste se o přístup k aplikaci z jiného virtuálního počítače, ale ve stejné virtuální síti, použijte název hostitele virtuálního počítače nebo jeho IP adresu veřejného, privátního nebo poskytovatele přiřazenou k Azure. Pro virtuální počítače vytvořené pomocí modelu nasazení Classic nepoužívejte veřejnou IP adresu cloudové služby.

![spuštění aplikace z jiného virtuálního počítače](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Například pokud je aplikace webovým serverem, zkuste získat přístup k webové stránce z prohlížeče na jiném virtuálním počítači ve stejné virtuální síti.

Pokud máte přístup k aplikaci, přejděte ke [kroku 3](#step3).

Pokud nemůžete získat přístup k aplikaci, ověřte následující nastavení:

* Hostitelská brána firewall na cílovém virtuálním počítači povoluje přenosy příchozích požadavků a odchozích odpovědí.
* Provoz je povolený v případě zjištění neoprávněného vniknutí nebo softwaru pro monitorování sítě běžícího na cílovém virtuálním počítači.
* Přenosy umožňují Cloud Services koncové body nebo skupiny zabezpečení sítě:
  * [Klasický model – správa koncových bodů Cloud Services](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Model Správce prostředků – Správa skupin zabezpečení sítě](../../virtual-network/manage-network-security-group.md)
* Provoz se zakládá na samostatné součásti běžící na vašem VIRTUÁLNÍm počítači v cestě mezi testovacím VIRTUÁLNÍm počítačem a vaším VIRTUÁLNÍm počítačem, jako je třeba nástroj pro vyrovnávání zatížení nebo brána firewall.

Na virtuálním počítači se systémem Windows použijte bránu Windows Firewall s pokročilým zabezpečením k určení, zda pravidla brány firewall vyloučí příchozí a odchozí přenosy vaší aplikace.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>Krok 3: přístup k aplikaci mimo virtuální síť
Pokuste se o přístup k aplikaci z počítače mimo virtuální síť jako virtuální počítač, na kterém je aplikace spuštěná. Použijte jinou síť jako původní klientský počítač.

![spuštění aplikace z počítače mimo virtuální síť](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Například pokud je aplikace webovým serverem, pokuste se získat přístup k webové stránce z prohlížeče běžícího na počítači, který není ve virtuální síti.

Pokud nemůžete získat přístup k aplikaci, ověřte následující nastavení:

* Pro virtuální počítače vytvořené pomocí modelu nasazení Classic:
  
  * Ověřte, jestli konfigurace koncového bodu pro virtuální počítač povoluje příchozí provoz, obzvláště protokol (TCP nebo UDP) a čísla veřejných a privátních portů.
  * Ověřte, že seznamy řízení přístupu (ACL) na koncovém bodu nebrání příchozímu provozu z Internetu.
  * Další informace najdete v tématu [nastavení koncových bodů na virtuální počítač](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints).
* Pro virtuální počítače vytvořené pomocí modelu nasazení Správce prostředků:
  
  * Ověřte, jestli Konfigurace příchozího pravidla NAT pro virtuální počítač povoluje příchozí přenosy, zejména protokol (TCP nebo UDP), a čísla veřejných a privátních portů.
  * Ověřte, že skupiny zabezpečení sítě povolují příchozí požadavek a provoz odchozích odpovědí.
  * Další informace najdete v tématu [co je skupina zabezpečení sítě](../../virtual-network/security-overview.md) .

Pokud je virtuální počítač nebo koncový bod členem sady s vyrovnáváním zatížení:

* Ověřte správnost protokolu sondy (TCP nebo UDP) a čísla portu.
* Pokud je protokol a port sondy jiný než protokol sady s vyrovnáváním zatížení a port:
  * Ověřte, že aplikace naslouchá na protokolu sondy (TCP nebo UDP) a číslo portu (použijte **netstat – a** na cílovém virtuálním počítači).
  * Ověřte, že hostitelská brána firewall na cílovém virtuálním počítači povoluje příchozí požadavek sondy a odchozí přenosy odezvy sondy.

Pokud máte přístup k aplikaci, ujistěte se, že vaše zařízení Internet Edge umožňuje:

* Odchozí požadavky aplikace z klientského počítače na virtuální počítač Azure.
* Příchozí přenos odpovědí aplikace z virtuálního počítače Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Krok 4 Pokud nemůžete získat přístup k aplikaci, zkontrolujte nastavení pomocí ověřování IP. 

Další informace najdete v tématu [Přehled monitorování sítě Azure](../../network-watcher/network-watcher-monitoring-overview.md). 

## <a name="additional-resources"></a>Další zdroje
[Řešení potíží s připojením ke vzdálené ploše na virtuálním počítači Azure se systémem Windows](troubleshoot-rdp-connection.md)

[Řešení potíží s připojením Secure Shell (SSH) k virtuálnímu počítači Azure se systémem Linux](troubleshoot-ssh-connection.md)
