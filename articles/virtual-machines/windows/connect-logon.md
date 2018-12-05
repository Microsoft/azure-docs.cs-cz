---
title: Připojení k virtuálnímu počítači s Windows Serverem | Dokumentace Microsoftu
description: Zjistěte, jak se připojit a přihlásit k virtuálnímu počítači s Windows pomocí portálu Azure a modelu nasazení s využitím Resource Manageru.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: cynthn
ms.openlocfilehash: fdd0c82f64b55c801ef04f1d533ed91683a07f9a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867065"
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Jak se připojit a přihlásit k virtuálnímu počítači s Windows v Azure
Pomocí tlačítka **Připojit** na webu Azure Portal spustíte z počítače s Windows relaci Vzdálené plochy (protokol RDP). Nejdřív připojit k virtuálnímu počítači a potom přihlásíte.

Pro připojení k virtuálnímu počítači s Windows z Macu, budete muset nainstalovat klienta protokolu RDP pro Mac, jako je [Vzdálená plocha od Microsoftu](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači
1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com/).
2. V nabídce vlevo vyberte **virtuálních počítačů**.
3. Ze seznamu vyberte virtuální počítač.
4. Nahoře na stránce pro virtuální počítač, vyberte **připojit**.
2. Na **připojit k virtuálnímu počítači** stránky, vyberte požadované možnosti a vyberte **soubor stáhnout RDP**.
2. Otevřete stažený soubor RDP a vyberte **připojit** po zobrazení výzvy. 
2. Zobrazí se upozornění, že soubor .rdp je od neznámého vydavatele. To se očekává. V **připojení ke vzdálené ploše** okně **připojit** pokračujte.
   
    ![Snímek obrazovky upozornění na neznámého vydavatele](./media/connect-logon/rdp-warn.png)
3. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte přihlašovací údaje pro účet na virtuálním počítači a pak vyberte **OK**.
   
     **Místní účet**: Toto je obvykle místní účet uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. V tomto případě je doménou název virtuálního počítače ve formátu *název_virtuálního_počítače*&#92;*uživatelské_jméno*.  
   
    **Virtuální počítač připojený k doméně**: Pokud virtuální počítač patří do domény, zadejte uživatelské jméno ve formátu *domény*&#92;*uživatelské jméno*. Účet také musí být členem skupiny Administrators nebo musí mít udělené oprávnění ke vzdálenému přístupu k virtuálnímu počítači.
   
    **Řadič domény**: Pokud virtuální počítač je řadič domény, zadejte uživatelské jméno a heslo účtu správce domény pro tuto doménu.
4. Vyberte **Ano** k ověření identity virtuálního počítače a dokončete přihlášení.
   
   ![Snímek obrazovky zobrazující zprávu o ověření identity virtuálního počítače](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Pokud **připojit** tlačítko na portálu se šedě a nejste připojení k Azure prostřednictvím [Express Route](../../expressroute/expressroute-introduction.md) nebo [Site-to-Site VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) připojení, budete muset vytvořit a virtuálnímu počítači přiřadíte veřejnou IP adresu před použitím protokolu RDP. Další informace najdete v tématu [veřejné IP adresy v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Připojení k virtuálnímu počítači pomocí Powershellu

Pokud používáte prostředí PowerShell a instalace modulu AzureRM může připojit pomocí `Get-AzureRmRemoteDesktopFile` rutiny, jak je znázorněno níže.

V tomto příkladu se okamžitě spustí připojení RDP, který vás provede podobné výzvy jako výše.

```powershell
Get-AzureRmRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Můžete také uložit soubor RDP pro budoucí použití.

```powershell
Get-AzureRmRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Další postup
Pokud máte potíže s připojením, přečtěte si téma [připojení ke vzdálené ploše řešení potíží s](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

