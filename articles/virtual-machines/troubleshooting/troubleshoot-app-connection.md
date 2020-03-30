---
title: Poradce při potížích s přístupem k aplikacím virtuálních aplikací v Azure | Dokumenty společnosti Microsoft
description: Pomocí těchto podrobných kroků pro řešení potíží můžete izolovat problémy při připojování k aplikacím spuštěným na virtuálních počítačích v Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: nelze spustit aplikaci, program se neotevře, naslouchací port je blokován, nelze spustit program, blokovat port pro poslech
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: caf73ffbc18a603ace22acfbd0da490048da698a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058122"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Řešení potíží s připojením aplikace na virtuálních počítačích v Azure

Existují různé důvody, proč se nejde spustit nebo se připojit k aplikaci spuštěné na virtuálním počítači Azure (VM). Důvody zahrnují aplikace není spuštěna nebo naslouchání na očekávané porty, naslouchání port blokován nebo pravidla sítě není správně předávání přenosů do aplikace. Tento článek popisuje metodický přístup k nalezení a opravě problému.

Pokud máte problémy s připojením k virtuálnímu počítači pomocí RDP nebo SSH, podívejte se nejprve na jeden z následujících článků:

* [Poradce při potížích s připojením ke vzdálené ploše k virtuálnímu počítači Azure založenému na Windows](troubleshoot-rdp-connection.md)
* [Poradce při potížích s připojením zabezpečeného prostředí (SSH) k virtuálnímu počítači Azure založenému na Linuxu](troubleshoot-ssh-connection.md).

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [MSDN Azure a zásobníku přetečení fóra](https://azure.microsoft.com/support/forums/). Případně můžete také soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.

## <a name="quick-start-troubleshooting-steps"></a>Postup řešení potíží s rychlým spuštěním
Pokud máte problémy s připojením k aplikaci, vyzkoušejte následující obecné kroky řešení potíží. Po každém kroku zkuste připojit k aplikaci znovu:

* Restartování virtuálního počítače
* Znovu vytvořit pravidla koncového bodu / brány firewall / pravidla skupiny zabezpečení sítě (NSG)
  * [Model Správce prostředků – správa skupin zabezpečení sítě](../../virtual-network/manage-network-security-group.md)
  * [Klasický model – Správa koncových bodů cloudových služeb](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Připojení z jiného umístění, například z jiné virtuální sítě Azure
* Opětovné nasazení virtuálního počítače
  * [Opětovné nasazení virtuálního aplikace pro Windows](redeploy-to-new-node-windows.md)
  * [Opětovné nasazení virtuálního počítače s Linuxem](redeploy-to-new-node-linux.md)
* Opětovné vytvoření virtuálního počítače

Další informace naleznete [v tématu Poradce při potížích s připojením koncového bodu (RDP/SSH/HTTP atd. selhání)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Podrobný přehled řešení potíží
Existují čtyři hlavní oblasti řešení potíží s přístupem k aplikaci, která běží na virtuálním počítači Azure.

![Poradce při potížích nelze spustit aplikaci](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Aplikace spuštěná na virtuálním počítači Azure.
   * Je samotná aplikace spuštěna správně?
2. Virtuální počítač Azure.
   * Je samotný virtuální virtuální hotel správně spuštěn a reaguje na požadavky?
3. Koncové body sítě Azure.
   * Koncové body cloudové služby pro virtuální počítače v modelu klasického nasazení.
   * Skupiny zabezpečení sítě a příchozí pravidla naváděcí ho programu NAT pro virtuální počítače v modelu nasazení Správce prostředků.
   * Může přenos toku od uživatelů do virtuálního počítačů/aplikace na očekávané porty?
4. Zařízení edge internetu.
   * Jsou pravidla brány firewall na místě brání provoz z tekoucí správně?

Pro klientské počítače, které přistupují k aplikaci přes síť VPN nebo ExpressRoute připojení, hlavní oblasti, které mohou způsobit problémy, jsou aplikace a virtuální počítač Azure.

Chcete-li zjistit zdroj problému a jeho opravu, postupujte takto.

## <a name="step-1-access-application-from-target-vm"></a>Krok 1: Přístup k aplikaci z cílového virtuálního virtuálního mísy
Pokuste se o přístup k aplikaci pomocí příslušného klientského programu z virtuálního počítači, na kterém je spuštěna. Použijte název místního hostitele, místní IP adresu nebo adresu zpětné smyčky (127.0.0.1).

![spuštění aplikace přímo z virtuálního virtuálního mísa](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Například pokud je aplikace webový server, otevřete prohlížeč na virtuálním počítači a pokuste se o přístup k webové stránce hostované na virtuálním počítači.

Pokud máte přístup k aplikaci, přejděte ke [kroku 2](#step2).

Pokud nemáte přístup k aplikaci, ověřte následující nastavení:

* Aplikace je spuštěna na cílovém virtuálním počítači.
* Aplikace naslouchá očekávaným portům TCP a UDP.

Na virtuálních počítačích se systémem Windows i Linux použijte příkaz **netstat -a** k zobrazení aktivních naslouchacích portů. Zkontrolujte výstup pro očekávané porty, na kterých by měla být naslouchání aplikace. Restartujte aplikaci nebo ji nakonfigurujte tak, aby podle potřeby používala očekávané porty, a zkuste znovu získat přístup k aplikaci místně.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>Krok 2: Přístup k aplikaci z jiného virtuálního počítače ve stejné virtuální síti
Pokuste se získat přístup k aplikaci z jiného virtuálního počítače, ale ve stejné virtuální síti, pomocí názvu hostitele virtuálního počítače nebo jeho Azure přiřazené veřejné, privátní nebo zprostředkovatelip adresy. Pro virtuální počítače vytvořené pomocí klasického modelu nasazení nepoužívejte veřejnou IP adresu cloudové služby.

![spuštění aplikace z jiného virtuálního virtuálního aplikace](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Pokud je například aplikace webový server, zkuste získat přístup k webové stránce z prohlížeče na jiném virtuálním počítači ve stejné virtuální síti.

Pokud máte přístup k aplikaci, přejděte ke [kroku 3](#step3).

Pokud nemáte přístup k aplikaci, ověřte následující nastavení:

* Brána firewall hostitele na cílovém virtuálním počítači umožňuje příchozí požadavek a odchozí přenosy odpovědí.
* Software pro detekci vniknutí nebo sledování sítě spuštěný na cílovém virtuálním počítači povoluje přenos.
* Koncové body cloudových služeb nebo skupiny zabezpečení sítě povolují přenos:
  * [Klasický model – Správa koncových bodů cloudových služeb](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Model Správce prostředků – správa skupin zabezpečení sítě](../../virtual-network/manage-network-security-group.md)
* Samostatná součást spuštěná ve vašem virtuálním počítači v cestě mezi testovacím virtuálním počítačem a virtuálním počítačem, jako je například vyrovnávání zatížení nebo brána firewall, povoluje přenos.

Ve virtuálním počítači se systémem Windows můžete pomocí brány Windows Firewall s pokročilým zabezpečením určit, zda pravidla brány firewall vylučují příchozí a odchozí přenosy vaší aplikace.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>Krok 3: Přístup k aplikaci mimo virtuální síť
Pokuste se o přístup k aplikaci z počítače mimo virtuální síť jako virtuální počítač, na kterém je aplikace spuštěna. Jako původní klientský počítač použijte jinou síť.

![spuštění aplikace z počítače mimo virtuální síť](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Pokud je například aplikace webový server, zkuste získat přístup k webové stránce z prohlížeče spuštěného v počítači, který není ve virtuální síti.

Pokud nemáte přístup k aplikaci, ověřte následující nastavení:

* Pro virtuální počítače vytvořené pomocí klasického modelu nasazení:
  
  * Ověřte, zda konfigurace koncového bodu pro virtuální počítače umožňuje příchozí provoz, zejména protokol (TCP nebo UDP) a čísla veřejného a soukromého portu.
  * Ověřte, zda seznamy řízení přístupu (ACL) v koncovém bodě nebrání příchozímu provozu z Internetu.
  * Další informace najdete v tématu [Jak nastavit koncové body na virtuální počítač](../windows/classic/setup-endpoints.md).
* Pro virtuální počítače vytvořené pomocí modelu nasazení Správce prostředků:
  
  * Ověřte, zda konfigurace příchozího pravidla NAT pro virtuální počítače povoluje příchozí provoz, zejména protokol (TCP nebo UDP) a čísla veřejných a soukromých portů.
  * Ověřte, zda skupiny zabezpečení sítě povolují příchozí a odchozí přenosy odpovědí.
  * Další informace naleznete v [tématu Co je skupina zabezpečení sítě?](../../virtual-network/security-overview.md)

Pokud je virtuální počítač nebo koncový bod členem sady s vyrovnáváním zatížení:

* Ověřte, zda jsou protokol sondy (TCP nebo UDP) a číslo portu správné.
* Pokud se protokol a port sondy liší od protokolu a portu nastaveného zatížení s vyrovnáváním zatížení:
  * Ověřte, zda aplikace naslouchá na protokolu sondy (TCP nebo UDP) a číslo portu (použijte **netstat –a** na cílovém virtuálním počítači).
  * Ověřte, zda brána firewall hostitele na cílovém virtuálním počítači povoluje příchozí sonda a přenosy odpovědí odchozí sondy.

Pokud máte přístup k aplikaci, ujistěte se, že vaše zařízení edge internet umožňuje:

* Odchozí aplikace požadavek provoz z klientského počítače do virtuálního počítače Azure.
* Příchozí provoz odezvy aplikace z virtuálního počítače Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Krok 4 Pokud nemáte přístup k aplikaci, zkontrolujte nastavení pomocí protokolu IP Verify. 

Další informace najdete v tématu [Přehled monitorování sítě Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Další zdroje
[Poradce při potížích s připojením ke vzdálené ploše k virtuálnímu počítači Azure založenému na Windows](troubleshoot-rdp-connection.md)

[Poradce při potížích s připojením zabezpečeného prostředí (SSH) k virtuálnímu počítači Azure založenému na Linuxu](troubleshoot-ssh-connection.md)


