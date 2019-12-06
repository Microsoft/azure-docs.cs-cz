---
title: Připojení k virtuálnímu počítači s Windows serverem
description: Zjistěte, jak se připojit a přihlásit k virtuálnímu počítači s Windows pomocí Azure Portal a modelu nasazení Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 152df830f11cd5a73235559c5c5d65ced44f22fa
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842114"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Jak se připojit a přihlásit k virtuálnímu počítači Azure s Windows
Pomocí tlačítka **Připojit** na webu Azure Portal spustíte z počítače s Windows relaci Vzdálené plochy (protokol RDP). Nejdřív se připojíte k virtuálnímu počítači a potom se přihlásíte.

Pokud se chcete připojit k virtuálnímu počítači s Windows z počítače Mac, budete muset nainstalovat klienta RDP pro Mac, například [Vzdálená plocha Microsoft](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači
1. Pokud se chcete připojit k virtuálnímu počítači, otevřete [Azure Portal](https://portal.azure.com/) . Vyhledejte a vyberte **virtuální počítače**.
2. Ze seznamu vyberte virtuální počítač.
3. Na stránce začátek virtuálního počítače vyberte **připojit**.
4. Na stránce **připojit k virtuálnímu počítači** vyberte **RDP**a pak vyberte příslušnou **IP adresu** a **číslo portu**. Ve většině případů by se měla používat výchozí IP adresa a port. Vyberte **stáhnout soubor RDP**. Pokud má virtuální počítač nastavenou sadu zásad za běhu, musíte nejdřív před stažením souboru RDP vybrat tlačítko **požádat o přístup** a požádat ho o přístup. Další informace o zásadách za běhu najdete v tématu [Správa přístupu k virtuálním počítačům pomocí zásad podle časových údajů](../../security-center/security-center-just-in-time.md).
5. Otevřete stažený soubor RDP a po zobrazení výzvy vyberte **připojit** . Zobrazí se upozornění, že soubor `.rdp` pochází od neznámého vydavatele. To se očekává. V okně **připojení ke vzdálené ploše** pokračujte výběrem **připojit** .
   
    ![Snímek obrazovky upozornění na neznámého vydavatele](./media/connect-logon/rdp-warn.png)
3. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte přihlašovací údaje pro účet na virtuálním počítači a pak vyberte **OK**.
   
     **Místní účet**: obvykle se jedná o uživatelské jméno a heslo místního účtu, které jste zadali při vytváření virtuálního počítače. V tomto případě je doménou název virtuálního počítače ve formátu *název_virtuálního_počítače*&#92;*uživatelské_jméno*.  
   
    **Virtuální počítač připojený k doméně**: Pokud virtuální počítač patří do domény, zadejte uživatelské jméno ve formátu *doména*&#92;*uživatelské_jméno*. Účet také musí být členem skupiny Administrators nebo musí mít udělené oprávnění ke vzdálenému přístupu k virtuálnímu počítači.
   
    **Řadič domény**: Pokud je virtuální počítač řadičem domény, zadejte uživatelské jméno a heslo účtu správce domény pro tuto doménu.
4. Vyberte **Ano** , pokud chcete ověřit identitu virtuálního počítače a dokončit přihlášení.
   
   ![Snímek obrazovky zobrazující zprávu o ověření identity virtuálního počítače](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Pokud je tlačítko **připojit** na portálu šedé a nejste připojeni k Azure prostřednictvím [expresního postupu](../../expressroute/expressroute-introduction.md) nebo připojení [VPN typu Site-to-site](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) , budete muset před použitím protokolu RDP vytvořit a přidružit virtuální počítač k veřejné IP adrese. Další informace najdete v tématu [veřejné IP adresy v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Připojení k virtuálnímu počítači pomocí PowerShellu

 

Pokud používáte PowerShell a máte nainstalovaný modul Azure PowerShell, můžete se také připojit pomocí rutiny `Get-AzRemoteDesktopFile`, jak je znázorněno níže.

V tomto příkladu se okamžitě spustí připojení RDP, které vás provede podobnými výzvami.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Můžete také uložit soubor RDP pro budoucí použití.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Další kroky
Pokud máte potíže s připojením, přečtěte si téma [řešení potíží s připojením ke vzdálené ploše](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

