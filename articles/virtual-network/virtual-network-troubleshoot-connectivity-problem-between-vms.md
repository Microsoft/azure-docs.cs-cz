---
title: Řešení problémů s připojením mezi virtuálními počítači Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak řešit problémy s připojením mezi virtuálními počítači Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ab3ae45081ecc481cb90af8961174e23c86e84b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056805"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Řešení potíží s připojením mezi virtuálními počítači Azure

Může dojít k problémům s připojením mezi virtuálními počítači Azure (VMs). Tento článek obsahuje postup řešení potíží, který vám pomůže tento problém vyřešit. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Příznak

Jeden virtuální počítač Azure se nemůže připojit k jinému virtuálnímu počítači Azure.

## <a name="troubleshooting-guidance"></a>Pokyny při řešení potíží 

1. [Zkontrolujte, zda je nic nesprávně nakonfigurována](#step-1-check-whether-nic-is-misconfigured)
2. [Zkontrolujte, zda je síťový provoz blokován sítí NSG nebo UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Kontrola, jestli je síťový provoz blokován bránou firewall virtuálního počítače](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Zkontrolujte, jestli na portu naslouchá aplikace nebo služba virtuálního počítače.](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Zkontrolujte, zda je problém způsoben SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Zkontrolujte, jestli je provoz blokován seznamy ACL pro klasický virtuální virtuální virtuální měnový virtuální mísa.](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Zkontrolujte, jestli je koncový bod vytvořen pro klasický virtuální virtuální ms.](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Pokuste se připojit ke sdílené složce sítě virtuálních můe](#step-8-try-to-connect-to-a-vm-network-share)
9. [Kontrola připojení mezi virtuálními sítěmi](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Chcete-li problém vyřešit, postupujte takto. Po dokončení každého kroku zkontrolujte, zda byl problém vyřešen. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Krok 1: Kontrola, zda je nenakonfigurovaná

Postupujte podle pokynů v části [Jak obnovit síťové rozhraní pro virtuální počítač Azure Windows](../virtual-machines/windows/reset-network-interface.md). 

Pokud k problému dojde po úpravě síťového rozhraní (NIC), postupujte takto:

**Virtuální virtuální ms s více kontomičnými kont.**

1. Přidejte nic.
2. Opravte problémy ve špatné nic nebo odstraňte špatnou nic.  Pak znovu přidejte nic.

Další informace najdete [v tématu Přidání síťových rozhraní do virtuálních počítačů nebo jejich odebrání z virtuálních počítačů](virtual-network-network-interface-vm.md).

**Virtuální virtuální ms s jednou samostatnou nenokovou konto** 

- [Opětovné nasazení virtuálního aplikace pro Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Opětovné nasazení virtuálního počítače s Linuxem](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Krok 2: Zkontrolujte, zda je síťový provoz blokován sítí NSG nebo UDR

Pomocí [sledování sítě ip flow Verify](../network-watcher/network-watcher-ip-flow-verify-overview.md) a [NSG Flow Logging](../network-watcher/network-watcher-nsg-flow-logging-overview.md) k určení, zda existuje skupina zabezpečení sítě (NSG) nebo uživatelem definovaná trasa (UDR), která narušuje tok provozu.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Krok 3: Kontrola, jestli je síťový provoz blokován bránou firewall virtuálního počítače

Zakažte bránu firewall a výsledek otestujte. Pokud je problém vyřešen, ověřte nastavení brány firewall a znovu povolte bránu firewall.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Krok 4: Kontrola, jestli na portu naslouchá aplikace nebo služba virtuálního počítači

Ke kontrole, jestli na portu naslouchá aplikace nebo služba virtuálního počítače, můžete použít jednu z následujících metod.

- Spusťte následující příkazy a zkontrolujte, zda server naslouchá na tomto portu.

**Virtuální montovna systému Windows**

    netstat –ano

**Virtuální počítač s Linuxem**

    netstat -l

- Spusťte příkaz **telnet** na samotném virtuálním počítači a otestujte port. Pokud se test nezdaří, aplikace nebo služba není nakonfigurována tak, aby naslouchala na tomto portu.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Krok 5: Zkontrolujte, zda je problém způsoben SNAT

V některých scénářích virtuální počítač je umístěn za řešení vyrovnávání zatížení, které má závislost na prostředky mimo Azure. V těchto scénářích pokud dochází k občasné problémy s připojením, problém může být způsoben [vyčerpání portu SNAT](../load-balancer/load-balancer-outbound-connections.md). Chcete-li tento problém vyřešit, vytvořte VIP (nebo ILPIP pro klasické) pro každý virtuální virtuální ms, který je za nástroji pro vyrovnávání zatížení a zabezpečit pomocí nsg nebo ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Krok 6: Zkontrolujte, jestli je provoz blokován seznamy ACl pro klasický virtuální virtuální virtuální měnový virtuální mísa

Seznam řízení přístupu (ACL) poskytuje možnost selektivně povolit nebo odepřít provoz pro koncový bod virtuálního počítače. Další informace naleznete [v tématu Správa přístupového bodu na koncovém bodu](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Krok 7: Zkontrolujte, jestli je koncový bod vytvořen pro klasický virtuální virtuální měnový virtuální mísu.

Všechny virtuální počítače, které vytvoříte v Azure pomocí klasického modelu nasazení, můžou automaticky komunikovat přes privátní síťový kanál s jinými virtuálními počítači ve stejné cloudové službě nebo virtuální síti. Počítače v jiných virtuálních sítích však vyžadují koncové body k přesměrování příchozího síťového provozu na virtuální počítač. Další informace naleznete v tématu [Jak nastavit koncové body](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Krok 8: Pokuste se připojit ke sdílené složce sítě virtuálních můe

Pokud se nemůžete připojit ke sdílené složce sítě virtuálních počítačů, problém může být způsoben nedostupnými síťovými aplikacemi ve virtuálním počítači. Informace o odstranění nedostupných nicotnic naleznete [v tématu Jak odstranit nedostupné síťové karty.](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Krok 9: Kontrola připojení mezi virtuálními sítěmi

Pomocí [sledování sítě IP ověření](../network-watcher/network-watcher-ip-flow-verify-overview.md) a [protokolování toku nsg](../network-watcher/network-watcher-nsg-flow-logging-overview.md) k určení, zda je nsg nebo UDR, který narušuje tok provozu. Zde můžete také ověřit konfiguraci mezi [virtuálními sítěmi](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.