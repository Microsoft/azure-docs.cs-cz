---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 820a6a4da9f5c466e694f247d09393474d8464ee
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418266"
---
K virtuálnímu počítači nasazenému ve vaší virtuální síti se můžete připojit vytvořením Připojení ke vzdálené ploše tohoto virtuálního počítače. Nejlepším způsobem, jak na začátku ověřit, že se k virtuálnímu počítači můžete připojit, je použít k připojení privátní IP adresu místo názvu počítače. Tímto způsobem testujete, jestli se můžete připojit, a ne, jestli je správně nakonfigurovaný překlad IP adres.

1. Vyhledejte privátní IP adresu. Privátní IP adresu virtuálního počítače je možné vyhledat více způsoby. Níže uvádíme kroky pro Azure Portal a PowerShell.

   - Azure Portal – Vyhledejte virtuální počítač na webu Azure Portal. Zobrazte vlastnosti virtuálního počítače. Ve výpisu uvidíte privátní IP adresu.

   - PowerShell – Pomocí příkladu zobrazte seznam virtuálních počítačů a privátních IP adres z vašich skupin prostředků. Tento příklad nemusíte před použitím upravovat.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

2. Zkontrolujte, že jste k virtuální síti připojeni pomocí připojení VPN.
3. Otevřete **Připojení ke vzdálené ploše** tak, že do vyhledávacího pole na hlavním panelu zadáte „RDP“ nebo „Připojení ke vzdálené ploše“ a pak vyberete Připojení ke vzdálené ploše. Připojení ke vzdálené ploše můžete otevřít také v PowerShellu pomocí příkazu mstsc. 
4. V Připojení ke vzdálené ploše zadejte privátní IP adresu virtuálního počítače. Můžete kliknout na Zobrazit možnosti a upravit další nastavení, pak se připojte.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Řešení potíží s připojením ke vzdálené ploše virtuálního počítače

Pokud se vám nedaří připojit k virtuálnímu počítači přes připojení VPN, zkontrolujte následující:

- Ověřte, že je úspěšně navázáno připojení VPN.
- Ověřte, že se připojujete k privátní IP adrese virtuálního počítače.
- Pokud se k virtuálnímu počítači můžete připojit s použitím privátní IP adresy, ale ne pomocí názvu počítače, ověřte, že jste správně nakonfigurovali DNS. Další informace o tom, jak funguje překlad IP adres pro virtuální počítače, najdete v tématu [Překlad IP adres pro virtuální počítače](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Další informace o připojení ke vzdálené ploše najdete v tématu [Řešení potíží s připojením ke vzdálené ploše virtuálního počítače](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).