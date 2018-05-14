---
title: Řešení potíží s připojením mezi virtuálními počítači Azure | Microsoft Docs
description: Naučte se řešení problémů s připojením mezi virtuálními počítači Azure.
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
ms.openlocfilehash: 6decb0e9188db00608be35d9ba4e84df92ceb671
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Řešení potíží s připojením mezi virtuálními počítači Azure

Může dojít k potížím s připojením mezi virtuálními počítači Azure (VM). Tento článek obsahuje postup pro odstraňování potíží při řešení tohoto problému. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Příznaky

Jeden virtuální počítač Azure se nemůže připojit k jiným virtuálním Počítačem Azure.

## <a name="troubleshooting-guidance"></a>Pokyny při řešení potíží 

1. [Zkontrolujte, zda síťový adaptér je špatně nakonfigurovaný.](#step-1-check-whether-nic-is-misconfigured)
2. [Zkontrolujte, zda síťový provoz je blokována NSG nebo UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Zkontrolujte, zda síťový provoz je blokována bránou firewall virtuálních počítačů](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Zkontrolujte, zda virtuální počítač aplikace nebo služba naslouchá na portu](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Zkontrolujte, zda je problém způsoben překládat pomocí SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Zkontrolujte, zda je přenos dat blokován nastavením seznamy ACL pro klasické virtuální počítač.](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Zkontrolujte, zda je pro klasické virtuální počítač vytvořen koncový bod](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Pokuste se připojit k síťové sdílené složky virtuálních počítačů](#step-8-try-to-connect-to-a-vm-network-share)
9. [Zkontrolujte připojení Inter-Vnet](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Postupujte podle těchto kroků k vyřešení tohoto problému. Po dokončení každého kroku zkontrolujte, zda byl problém vyřešen. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Krok 1: Zkontrolujte, zda síťový adaptér je špatně nakonfigurovaný.

Postupujte podle kroků v [jak resetovat síťové rozhraní pro virtuální počítač s Windows Azure](../virtual-machines/windows/reset-network-interface.md). 

Pokud k problému dochází po úpravě síťové rozhraní (NIC), postupujte podle těchto kroků:

**Virtuální počítače více síťovými Kartami**

1. Přidejte síťový adaptér.
2. Opravte problémy v chybný síťový adaptér nebo odeberte síťovou kartu chybný.  Poté znovu přidejte síťový adaptér.

Další informace najdete v tématu [síťová rozhraní pro přidání nebo odebrání z virtuálních počítačů](virtual-network-network-interface-vm.md).

**Jedním seskupování virtuálních počítačů** 

- [Znovu nasaďte Windows virtuálního počítače.](../virtual-machines/windows/redeploy-to-new-node.md)
- [Opětovné nasazení virtuálního počítače s Linuxem](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Krok 2: Kontrola, zda síťový provoz je blokována NSG nebo UDR

Použití [ověřte toku IP sledovací proces sítě](../network-watcher/network-watcher-ip-flow-verify-overview.md) a [protokolování toku NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) k určení, zda je skupina zabezpečení sítě (NSG) nebo uživatelem definované trasy (UDR) který je v konfliktu s přenosy.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Krok 3: Zkontrolujte, zda síťový provoz je blokována bránou firewall virtuálních počítačů

Zakažte bránu firewall a poté otestujte výsledek. Pokud se problém nevyřeší, ověřte nastavení brány firewall a pak znovu povolit bránu firewall.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Krok 4: Kontrola, zda virtuální počítač aplikace nebo služba naslouchá na portu

Jeden z následujících metod můžete zkontrolovat, zda virtuální počítač aplikace nebo služba naslouchá na portu.

- Spusťte následující příkazy ke kontrole, jestli server naslouchá na tomto portu.

**Virtuální počítač s Windows**

    netstat –ano

**Virtuální počítač s Linuxem**

    netstat -l

- Spustit **telnet** příkaz na virtuálním počítači samotné k otestování portu. Pokud se test nezdaří, aplikace nebo služby není nakonfigurován pro naslouchání na tomto portu.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Krok 5: Zkontrolujte, zda je problém způsoben překládat pomocí SNAT

V některých případech je virtuální počítač umístěn za řešení vyrovnávání zatížení, který má závislost na prostředky mimo Azure. V těchto scénářích platí, pokud dojde k problémům nepřerušované připojení problém může být způsobeno [překládat pomocí SNAT port vyčerpání](../load-balancer/load-balancer-outbound-connections.md). K vyřešení problému, vytvořte pro každý virtuální počítač, který je za zařízením na Vyrovnávání zatížení a zabezpečení pomocí NSG nebo seznamu ACL VIP (nebo splnění pro classic). 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Krok 6: Zkontrolujte, zda je přenos dat blokován nastavením seznamy ACL pro klasické virtuální počítač.

Seznam řízení přístupu (ACL) umožňuje selektivně povolit nebo zakázat přenosů pro koncový bod virtuálního počítače. Další informace najdete v tématu [spravovat seznam ACL u koncového bodu](../virtual-machines/windows/classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Krok 7: Zkontrolujte, zda je pro klasické virtuální počítač vytvořen koncový bod

Všechny virtuální počítače, které vytvoříte v Azure pomocí modelu nasazení classic může automaticky komunikovat přes kanál privátní síť s dalšími virtuálními počítači ve stejné cloudové služby nebo virtuální sítě. Počítače v jiných virtuálních sítích však vyžadují koncové body směrovat příchozí síťový provoz do virtuálního počítače. Další informace najdete v tématu [jak nastavit koncové body](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Krok 8: Pokuste se připojit k síťové sdílené složky virtuálních počítačů

Pokud se nemůžete připojit ke sdílené síťové složce virtuálních počítačů, problém může být způsobeno není k dispozici síťové adaptéry ve virtuálním počítači. Pokud chcete odstranit není k dispozici síťové karty, najdete v části [jak odstranit není k dispozici síťové karty](../virtual-machines/windows/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Krok 9: Zkontrolujte Inter-Vnet připojení

Použití [ověřte toku IP sledovací proces sítě](../network-watcher/network-watcher-ip-flow-verify-overview.md) a [protokolování toku NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) k určení, zda je skupina NSG nebo UDR, který je v konfliktu s přenosy. Můžete si taky ověřit konfiguraci Inter-Vnet [zde](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.