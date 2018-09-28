---
title: Řešení potíží s připojením mezi virtuálními počítači Azure | Dokumentace Microsoftu
description: Zjistěte, jak řešení problémů s připojením mezi virtuálními počítači Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 02f6e2648c007cfe87984e8de886b8b76e1e8d16
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409911"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Řešení potíží s připojením mezi virtuálními počítači Azure

Problémy s připojením mezi virtuálními počítači Azure (VM) se mohou vyskytnout. Tento článek obsahuje postup řešení potíží, které vám pomohou vyřešit tento problém. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Příznak

Jeden virtuální počítač Azure se nemůže připojit k jinému virtuálnímu počítači Azure.

## <a name="troubleshooting-guidance"></a>Pokyny při řešení potíží 

1. [Zkontrolujte, jestli síťová karta je špatně nakonfigurovaný.](#step-1-check-whether-nic-is-misconfigured)
2. [Zkontrolujte, zda síťový provoz blokuje skupina zabezpečení sítě nebo UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Zkontrolujte, zda síťový provoz je blokován bránou firewall virtuálního počítače](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Zkontrolujte, zda virtuální počítač aplikace nebo služba naslouchá na portu](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Zkontrolujte, zda je problém způsoben SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Zkontrolujte, zda seznamy ACL blokovány přenosy pro klasický virtuální počítač](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Zkontrolujte, zda je koncový bod vytvořen pro klasický virtuální počítač](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Pokuste se připojit ke sdílení sítě virtuálních počítačů](#step-8-try-to-connect-to-a-vm-network-share)
9. [Zkontrolujte připojení sítěmi](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Postupujte podle těchto kroků k vyřešení tohoto problému. Po dokončení každého kroku, zkontrolujte, zda byl problém vyřešen. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Krok 1: Zkontrolujte, jestli síťová karta je špatně nakonfigurovaný.

Postupujte podle kroků v [resetování síťové rozhraní pro virtuální počítač Windows Azure](../virtual-machines/windows/reset-network-interface.md). 

Pokud k problému dochází, když upravíte síťové rozhraní (NIC), postupujte podle těchto kroků:

**Virtuální počítače s více síťovými Kartami**

1. Přidat síťový adaptér.
2. Opravte problémy v chybný síťovou kartu nebo odeberte chybný síťový adaptér  Poté znovu přidejte síťové rozhraní.

Další informace najdete v tématu [síťová rozhraní pro přidání nebo odebrání z virtuálních počítačů](virtual-network-network-interface-vm.md).

**Jedné síťové karty virtuálního počítače** 

- [Opětovné nasazení virtuálního počítače Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Opětovné nasazení virtuálního počítače s Linuxem](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Krok 2: Kontrola, zda síťový provoz blokuje skupina zabezpečení sítě nebo UDR

Použití [ověření toku protokolu IP sítě sledovacích procesů](../network-watcher/network-watcher-ip-flow-verify-overview.md) a [protokolování toků NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) k určení, zda je skupina zabezpečení sítě (NSG) nebo uživatelem definované trasy (UDR) který je v konfliktu s tokem provozu.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Krok 3: Kontrola, zda síťový provoz je blokován bránou firewall virtuálního počítače

Zakázat bránu firewall a pak výsledek testu. Pokud se problém nevyřeší, zkontrolujte nastavení brány firewall a potom znovu povolit bránu firewall.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Krok 4: Kontrola, jestli virtuální počítač aplikace nebo služba naslouchá na portu

Chcete-li zkontrolovat, jestli virtuální počítač aplikace nebo služba naslouchá na portu můžete použít jednu z následujících metod.

- Spusťte následující příkazy ke kontrole, jestli server naslouchá na portu.

**Virtuální počítač s Windows**

    netstat –ano

**Virtuální počítač s Linuxem**

    netstat -l

- Spustit **telnet** příkaz samotného testování na port virtuálního počítače. Pokud se test nezdaří, aplikace nebo služba není nakonfigurovaná k naslouchání na portu.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Krok 5: Zkontrolujte, zda je problém způsoben SNAT

V některých případech je virtuální počítač umístěn za řešení vyrovnávání zatížení, které obsahuje závislost na prostředky mimo Azure. V těchto scénářích platí, pokud máte problémy s přerušovaným připojením, problém může být způsobeno [vyčerpání portů SNAT](../load-balancer/load-balancer-outbound-connections.md). Chcete-li vyřešit tento problém, vytvořte virtuální IP adresy (nebo ILPIP v případě klasického modelu) pro každý virtuální počítač, který je za nástrojem pro vyrovnávání zatížení a zabezpečení s využitím skupiny zabezpečení sítě nebo seznamu ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Krok 6: Zkontrolujte, zda seznamy ACL blokovány přenosy pro klasický virtuální počítač

Seznam řízení přístupu (ACL) umožňuje selektivně povolovat nebo zamítat provoz pro koncový bod virtuálního počítače. Další informace najdete v tématu [spravovat seznam ACL v koncovém bodě](../virtual-machines/windows/classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Krok 7: Zkontrolujte, zda je koncový bod vytvořen pro klasický virtuální počítač

Všechny virtuální počítače, které vytvoříte v Azure s použitím modelu nasazení classic můžou automaticky komunikovat prostřednictvím kanálu privátní sítě s jinými virtuálními počítači ve stejné cloudové službě nebo virtuální síti. Ale v jiných sítích virtuálních počítačů vyžadují koncové body pro přesměrování příchozí síťový provoz do virtuálního počítače. Další informace najdete v tématu [jak nastavit koncové body](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Krok 8: Zkuste se připojit ke sdílení sítě virtuálních počítačů

Pokud se nemůžete připojit ke sdílení sítě virtuálních počítačů, problém může být způsobeno není k dispozici síťových adaptérů ve virtuálním počítači. Odstranit síťové karty není k dispozici, najdete v článku [odstranění není k dispozici síťové karty](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Krok 9: Kontrola sítěmi připojení

Použití [ověření toku protokolu IP sítě sledovacích procesů](../network-watcher/network-watcher-ip-flow-verify-overview.md) a [protokolování toků NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) k určení, zda je skupina zabezpečení sítě nebo UDR, který je v konfliktu s tokem provozu. Můžete si taky ověřit konfiguraci sítěmi [tady](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.