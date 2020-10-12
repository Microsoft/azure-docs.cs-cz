---
title: Řešení potíží s připojením mezi virtuálními počítači Azure | Microsoft Docs
description: Naučte se řešit problémy s připojením, ke kterým může docházet mezi virtuálními počítači Azure.
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
ms.openlocfilehash: eb94b67b026ed108f31f6cd802010577665ec0d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87286083"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Řešení potíží s připojením mezi virtuálními počítači Azure

Může docházet k problémům s připojením mezi virtuálními počítači Azure (virtuální počítače). Tento článek popisuje kroky pro řešení potíží, které vám pomůžou tento problém vyřešit. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Příznak

Jeden virtuální počítač Azure se nemůže připojit k jinému virtuálnímu počítači Azure.

## <a name="troubleshooting-guidance"></a>Pokyny při řešení potíží 

1. [Ověřte, jestli je síťové rozhraní špatně nakonfigurované.](#step-1-check-whether-nic-is-misconfigured)
2. [Ověřte, jestli je síťový provoz blokovaný nástrojem NSG nebo UDR.](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Ověřte, jestli je síťový provoz zablokovaný branou firewall virtuálních počítačů.](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Ověřte, jestli aplikace nebo služba VM naslouchá na portu.](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Ověřte, jestli je problém způsobený serverem SNAT.](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Ověřte, jestli je provoz v rámci seznamů ACL pro klasický virtuální počítač blokovaný.](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Ověřte, jestli je pro klasický virtuální počítač vytvořený koncový bod.](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Zkuste se připojit ke sdílené síťové složce virtuálních počítačů.](#step-8-try-to-connect-to-a-vm-network-share)
9. [Ověřit připojení Inter-Vnet](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Při řešení tohoto problému postupujte podle těchto kroků. Po dokončení každého kroku ověřte, zda byl problém vyřešen. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Krok 1: ověření, jestli je síťové rozhraní špatně nakonfigurované

Postupujte podle kroků v části [Postup resetování síťového rozhraní pro virtuální počítač Azure s Windows](../virtual-machines/windows/reset-network-interface.md). 

Pokud k problému dochází poté, co upravíte síťové rozhraní (NIC), postupujte následovně:

**Virtuální počítače s více síťovými kartami**

1. Přidejte síťovou kartu.
2. Opravte problémy v chybném síťovém adaptéru nebo odstraňte chybnou síťovou kartu.  Pak znovu přidejte síťovou kartu.

Další informace najdete v tématu [přidání síťových rozhraní nebo jejich odebrání z virtuálních počítačů](virtual-network-network-interface-vm.md).

**Virtuální počítač s jednou síťovou kartou** 

- [Znovu nasadit virtuální počítač s Windows](../virtual-machines/windows/redeploy-to-new-node.md)
- [Znovu nasadit virtuální počítač se systémem Linux](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Krok 2: ověření, jestli je síťový provoz blokovaný pomocí NSG nebo UDR

K určení, zda existuje skupina zabezpečení sítě (NSG) User-Defined nebo trasa (UDR), která je v konfliktu s tokem provozu, použijte [Network Watcher protokolování protokolu IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) a [protokolování NSG Flow](../network-watcher/network-watcher-nsg-flow-logging-overview.md) .

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Krok 3: ověření, jestli je síťový provoz zablokovaný branou firewall virtuálních počítačů

Zakažte bránu firewall a potom otestujte výsledek. Pokud se problém vyřeší, ověřte nastavení brány firewall a pak bránu firewall znovu povolte.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Krok 4: ověření, jestli aplikace nebo služba virtuálních počítačů na portu naslouchá

Pomocí jedné z následujících metod můžete zjistit, jestli aplikace virtuálního počítače nebo služba naslouchá na portu.

- Spuštěním následujících příkazů ověřte, zda server naslouchá na tomto portu.

**Virtuální počítač s Windows**

```console
netstat –ano
```

**Virtuální počítači s Linuxem**

```console
netstat -l
```

- Spusťte na virtuálním počítači příkaz **Telnet** pro otestování portu. Pokud se test nezdařil, aplikace nebo služba není nakonfigurována pro naslouchání na tomto portu.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Krok 5: ověření, jestli potíže způsobují SNAT

V některých scénářích se virtuální počítač umístí za řešení vyrovnávání zatížení, které má závislost na prostředcích mimo Azure. V těchto scénářích platí, že pokud se setkáte s přerušovanými problémy s připojením, může problém způsobovat [vyčerpání portů SNAT](../load-balancer/load-balancer-outbound-connections.md). Pokud chcete tento problém vyřešit, vytvořte VIP (nebo ILPIP pro Classic) pro každý virtuální počítač, který je za nástrojem pro vyrovnávání zatížení, a zabezpečte ho pomocí NSG nebo seznamu ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Krok 6: ověření, zda je provoz v rámci seznamů ACL pro klasický virtuální počítač zablokován

Seznam řízení přístupu (ACL) poskytuje možnost selektivního povolení nebo odepření provozu pro koncový bod virtuálního počítače. Další informace najdete v tématu [Správa seznamu řízení přístupu (ACL) na koncovém bodu](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Krok 7: ověření, zda je koncový bod vytvořen pro klasický virtuální počítač

Všechny virtuální počítače, které vytvoříte v Azure pomocí modelu nasazení Classic, můžou automaticky komunikovat přes privátní síťový kanál s ostatními virtuálními počítači ve stejné cloudové službě nebo ve virtuální síti. Počítače v jiných virtuálních sítích ale vyžadují koncové body pro směrování příchozího síťového provozu do virtuálního počítače. Další informace najdete v tématu [nastavení koncových bodů](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Krok 8: Pokuste se připojit ke sdílené síťové složce virtuálních počítačů

Pokud se nemůžete připojit ke sdílené síťové složce virtuálních počítačů, problém může být způsoben nedostupnými síťovými kartami ve virtuálním počítači. Postup odstranění nedostupných síťových adaptérů najdete v tématu [odstranění nedostupných síťových karet](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics) .

### <a name="step-9-check-inter-vnet-connectivity"></a>Krok 9: ověření Inter-Vnetho připojení

K určení, jestli existuje NSG nebo UDR, která je v konfliktu s tokem provozu, použijte [Network Watcher protokolování protokolu IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) a [protokolování toku NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) . Můžete taky [ověřit konfiguraci Inter-Vnet](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.