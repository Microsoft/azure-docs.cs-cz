---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6d0737a7300b2a6025f776c1ed65a05cacf2141a
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323638"
---
Vytvořte připojení ke vzdálené ploše pro připojení k virtuálnímu počítači, který je nasazený do virtuální sítě. Je nejlepší způsob, jak ověřit, že se že můžete připojit k virtuálnímu počítači připojit pomocí jeho privátní IP adresu místo názvu počítače. Tímto způsobem testujete zobrazíte, pokud je možné připojit, zda není správně nakonfigurovaný překlad názvů. 

1. Vyhledejte privátní IP adresu pro virtuální počítač. Vyhledejte privátní IP adresu virtuálního počítače, zobrazit vlastnosti pro virtuální počítač na webu Azure Portal nebo pomocí prostředí PowerShell.
2. Ověřte, že jste připojeni k vaší virtuální sítě s připojením Point-to-Site VPN. 
3. Chcete-li spustit nástroj připojení ke vzdálené ploše, zadejte *RDP* nebo *připojení ke vzdálené ploše* do vyhledávacího pole na hlavním panelu, vyberte **připojení ke vzdálené ploše**. Lze jej otevřít také pomocí **mstsc** příkazu v Powershellu. 
3. V **připojení ke vzdálené ploše**, zadejte privátní IP adresu virtuálního počítače. V případě potřeby vyberte **zobrazit možnosti** upravit další nastavení, připojte se.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Řešení potíží s připojením ke vzdálené ploše virtuálního počítače

Pokud máte potíže s připojením k virtuálnímu počítači přes připojení VPN, existuje několik věcí, které můžete zkontrolovat. 

- Ověřte, že je úspěšně navázáno připojení VPN.
- Ověřte, že se připojujete k privátní IP adresu pro virtuální počítač.
- Zadejte **ipconfig** zkontrolujte IPv4 adresu přiřazenou adaptéru Ethernet na počítači, ze kterého se připojujete. Překrývající se adresní prostor nastane, pokud IP adresa je v rozsahu adres virtuální sítě, které se připojujete, nebo rozsahu adres vpnclientaddresspool. Když se adresní prostor tímto způsobem překrývá, síťový provoz nemá přístup do Azure a zůstane v místní síti.
- Pokud se můžete připojit k virtuálnímu počítači s použitím privátní IP adresu, ale ne název počítače, ověřte, že jste správně nakonfigurovali DNS. Další informace o tom, jak funguje překlad IP adres pro virtuální počítače, najdete v tématu [Překlad IP adres pro virtuální počítače](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Ověřte, že konfigurační balíček klienta VPN se vygeneruje po zadejte IP adresy serverů DNS pro virtuální síť. Při aktualizaci IP adresy serverů DNS, vygenerujte a nainstalujte nový balíček pro konfiguraci klienta VPN.

Další informace o řešení potíží najdete v tématu [Řešení potíží s připojením ke vzdálené ploše virtuálního počítače](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).