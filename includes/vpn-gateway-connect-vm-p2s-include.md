---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4999868170b73493f601f06e114a233a0f88fa35
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444047"
---
K virtuálnímu počítači nasazenému ve vaší virtuální síti se můžete připojit vytvořením Připojení ke vzdálené ploše tohoto virtuálního počítače. Nejlepším způsobem, jak na začátku ověřit, že se k virtuálnímu počítači můžete připojit, je použít k připojení privátní IP adresu místo názvu počítače. Tímto způsobem testujete, jestli se můžete připojit, a ne, jestli je správně nakonfigurovaný překlad IP adres.

1. Vyhledejte privátní IP adresu. Privátní IP adresu virtuálního počítače najdete tak, že si zobrazíte jeho vlastnosti na webu Azure Portal nebo pomocí PowerShellu.

   - Azure Portal – Vyhledejte virtuální počítač na webu Azure Portal. Zobrazte vlastnosti virtuálního počítače. Ve výpisu uvidíte privátní IP adresu.

   - PowerShell – Pomocí příkladu zobrazte seznam virtuálních počítačů a privátních IP adres z vašich skupin prostředků. Tento příklad nemusíte před použitím upravovat.

     ```powershell
     $VMs = Get-AzureRmVM
     $Nics = Get-AzureRmNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

2. Zkontrolujte, že jste k virtuální síti připojeni pomocí připojení VPN typu Point-to-Site.
3. Otevřete **Připojení ke vzdálené ploše** tak, že do vyhledávacího pole na hlavním panelu zadáte „RDP“ nebo „Připojení ke vzdálené ploše“ a pak vyberete Připojení ke vzdálené ploše. Připojení ke vzdálené ploše můžete otevřít také v PowerShellu pomocí příkazu mstsc. 
4. V Připojení ke vzdálené ploše zadejte privátní IP adresu virtuálního počítače. Můžete kliknout na Zobrazit možnosti a upravit další nastavení, pak se připojte.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Řešení potíží s připojením ke vzdálené ploše virtuálního počítače

Pokud se vám nedaří připojit k virtuálnímu počítači přes připojení VPN, zkontrolujte následující:

- Ověřte, že je úspěšně navázáno připojení VPN.
- Ověřte, že se připojujete k privátní IP adrese virtuálního počítače.
- Pomocí příkazu ipconfig zkontrolujte IPv4 adresu přiřazenou adaptéru Ethernet na počítači, ze kterého se připojujete. Pokud je IP adresa v rámci rozsahu adres virtuální sítě, ke které se připojujete, nebo v rámci rozsahu adres VPNClientAddressPool, tato situace se označuje jako překrývající se adresní prostor. Když se adresní prostor tímto způsobem překrývá, síťový provoz nemá přístup do Azure a zůstane v místní síti.
- Pokud se k virtuálnímu počítači můžete připojit s použitím privátní IP adresy, ale ne pomocí názvu počítače, ověřte, že jste správně nakonfigurovali DNS. Další informace o tom, jak funguje překlad IP adres pro virtuální počítače, najdete v tématu [Překlad IP adres pro virtuální počítače](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Ověřte, že se po zadání IP adres serveru DNS pro virtuální síť vygeneroval balíček pro konfiguraci klienta VPN. Pokud jste aktualizovali IP adresy serveru DNS, vygenerujte a nainstalujte nový balíček pro konfiguraci klienta VPN.
- Další informace o připojení ke vzdálené ploše najdete v tématu [Řešení potíží s připojením ke vzdálené ploše virtuálního počítače](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).