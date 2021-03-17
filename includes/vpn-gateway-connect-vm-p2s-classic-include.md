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
ms.openlocfilehash: 4fc14083b0912cf18f98c229adcb297b89bc8971
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559273"
---
Vytvořte Připojení ke vzdálené ploše pro připojení k virtuálnímu počítači, který je nasazený do vaší virtuální sítě. Nejlepším způsobem, jak ověřit, že se můžete připojit k VIRTUÁLNÍmu počítači, je připojit se ke svému privátní IP adrese, a ne jeho název počítače. Tímto způsobem otestujete, jestli se můžete připojit a jestli je správně nakonfigurovaný překlad názvů. 

1. Vyhledejte privátní IP adresu pro virtuální počítač. Pokud chcete najít privátní IP adresu virtuálního počítače, zobrazte si vlastnosti virtuálního počítače ve Azure Portal nebo použijte PowerShell.
2. Ověřte, že jste k virtuální síti připojeni pomocí připojení VPN typu Point-to-site. 
3. Připojení ke vzdálené ploše spustíte tak, že do vyhledávacího pole na hlavním panelu zadáte *RDP* nebo *připojení ke vzdálené ploše* a pak vyberete **připojení ke vzdálené ploše**. Můžete ho také otevřít pomocí příkazu **mstsc** v prostředí PowerShell. 
3. V **připojení ke vzdálené ploše** zadejte privátní IP adresu virtuálního počítače. V případě potřeby vyberte **Zobrazit možnosti** pro úpravu dalších nastavení a pak připojit.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Řešení potíží s připojením ke vzdálené ploše virtuálního počítače

Pokud máte potíže s připojením k virtuálnímu počítači přes připojení k síti VPN, můžete si ověřit několik věcí. 

- Ověřte, že je úspěšně navázáno připojení VPN.
- Ověřte, že se připojujete k privátní IP adrese pro virtuální počítač.
- Zadáním **příkazu ipconfig** zkontrolujete IPv4 adresu přiřazenou adaptéru Ethernet na počítači, ze kterého se připojujete. K překrývající se adresnímu prostoru dochází v případě, že se IP adresa nachází v rozsahu adres virtuální sítě, ke které se připojujete, nebo v rámci rozsahu adres vaší VPNClientAddressPool. Když se adresní prostor tímto způsobem překrývá, síťový provoz nemá přístup do Azure a zůstane v místní síti.
- Pokud se k VIRTUÁLNÍmu počítači můžete připojit pomocí privátní IP adresy, ale ne názvu počítače, ověřte, jestli jste správně nakonfigurovali DNS. Další informace o tom, jak funguje překlad IP adres pro virtuální počítače, najdete v tématu [Překlad IP adres pro virtuální počítače](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Ověřte, že se konfigurační balíček klienta VPN vygeneroval po zadání IP adres serveru DNS pro virtuální síť. Pokud aktualizujete IP adresy serveru DNS, vygenerujte a nainstalujte nový balíček pro konfiguraci klienta VPN.

Další informace o řešení potíží najdete v tématu [Řešení potíží s připojením ke vzdálené ploše virtuálního počítače](../articles/virtual-machines/troubleshooting/troubleshoot-rdp-connection.md).