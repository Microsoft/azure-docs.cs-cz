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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174873"
---
Vytvořte připojení ke vzdálené ploše pro připojení k virtuálnímu počítači, který je nasazený do vaší virtuální sítě. Nejlepší způsob, jak ověřit, že se můžete připojit k virtuálnímu počítači, je připojit se k jeho privátní IP adrese, nikoli k názvu počítače. Tímto způsobem testujete, zda se můžete připojit, nikoli zda je správně nakonfigurováno překlad názvů. 

1. Vyhledejte privátní IP adresu pro virtuální počítač. Pokud chcete najít privátní IP adresu virtuálního počítače, zobrazte vlastnosti virtuálního počítače na webu Azure Portal nebo použijte PowerShell.
2. Ověřte, že jste připojeni k virtuální síti pomocí připojení VPN bodu na místo. 
3. Chcete-li otevřít program Připojení ke vzdálené ploše, zadejte do vyhledávacího pole na hlavním panelu *položku Připojení k* programu *RDP* nebo Vzdálená plocha a vyberte **položku Připojení ke vzdálené ploše**. Můžete jej také otevřít pomocí příkazu **mstsc** v prostředí PowerShell. 
3. V **části Připojení ke vzdálené ploše**zadejte privátní IP adresu virtuálního počítače. V případě potřeby vyberte **Zobrazit možnosti,** chcete-li upravit další nastavení, pak se připojte.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Řešení potíží s připojením ke vzdálené ploše virtuálního počítače

Pokud máte potíže s připojením k virtuálnímu počítači přes připojení VPN, můžete zkontrolovat několik věcí. 

- Ověřte, že je úspěšně navázáno připojení VPN.
- Ověřte, že se připojujete k privátní IP adrese virtuálního virtuálního soudu.
- Zadáním **ipconfig** zkontrolujte adresu IPv4 přiřazenou adaptéru Ethernet v počítači, ze kterého se připojujete. K překrývajícímu se adresnímu prostoru dochází, když se adresa IP nachází v rozsahu adres virtuální sítě, ke které se připojujete, nebo v rozsahu adres vašeho fondu VPNClientAddressPool. Když se adresní prostor tímto způsobem překrývá, síťový provoz nemá přístup do Azure a zůstane v místní síti.
- Pokud se můžete připojit k virtuálnímu počítači pomocí privátní IP adresy, ale ne názvu počítače, ověřte, zda jste dns nakonfigurovali správně. Další informace o tom, jak funguje překlad IP adres pro virtuální počítače, najdete v tématu [Překlad IP adres pro virtuální počítače](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Po zadání IP adres SERVERU DNS pro virtuální síť ověřte, zda je konfigurační balíček klienta VPN vygenerován. Pokud aktualizujete adresy IP serveru DNS, vygenerujte a nainstalujte nový konfigurační balíček klienta VPN.

Další informace o řešení potíží najdete v tématu [Řešení potíží s připojením ke vzdálené ploše virtuálního počítače](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).