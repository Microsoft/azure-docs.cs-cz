---
title: Připojení k virtuálnímu virtuálnímu virtuálnímu mněmu se systémem Windows Server
description: Zjistěte, jak se připojit a přihlásit k virtuálnímu počítači s Windows pomocí portálu Azure a modelu nasazení Správce prostředků.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266764"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Jak se připojit a přihlásit k virtuálnímu počítači Azure se systémem Windows
Pomocí tlačítka **Připojit** na webu Azure Portal spustíte z počítače s Windows relaci Vzdálené plochy (protokol RDP). Nejprve se připojíte k virtuálnímu počítači a pak se přihlásíte.

Chcete-li se připojit k virtuálnímu počítači se systémem Windows z Počítače Mac, budete muset nainstalovat klienta RDP pro Mac, například [vzdálenou plochu .](https://aka.ms/rdmac)

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači
1. Přejděte na [portál Azure](https://portal.azure.com/) a připojte se k virtuálnímu počítači. Vyhledejte a vyberte **virtuální počítače**.
2. Ze seznamu vyberte virtuální počítač.
3. Na začátku stránky virtuálního počítače vyberte **Připojit**.
4. Na stránce **Připojit k virtuálnímu počítači** vyberte **protokol RDP**a pak vyberte příslušnou **adresu IP** a **číslo portu**. Ve většině případů by měla být použita výchozí adresa IP a port. Vyberte **stáhnout soubor RDP**. Pokud má virtuální virtuální připojení nastavena zásada just-in-time, musíte nejprve vybrat tlačítko **Požádat o přístup** a požádat o přístup, než budete moct stáhnout soubor RDP. Další informace o zásadách just-in-time najdete v tématu [Správa přístupu k virtuálnímu počítači pomocí zásad just in time](../../security-center/security-center-just-in-time.md).
5. Otevřete stažený soubor RDP a po zobrazení výzvy vyberte **Připojit.** Zobrazí se upozornění, `.rdp` že soubor pochází od neznámého vydavatele. To se očekává. V okně **Připojení ke vzdálené ploše** pokračujte výběrem **možnosti Připojit.**
   
    ![Snímek obrazovky upozornění na neznámého vydavatele](./media/connect-logon/rdp-warn.png)
3. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte přihlašovací údaje pro účet ve virtuálním počítači a pak vyberte **OK**.
   
     **Místní účet**: Toto je obvykle uživatelské jméno místního účtu a heslo, které jste zadali při vytváření virtuálního počítače. V tomto případě je doménou název virtuálního počítače ve formátu *název_virtuálního_počítače*&#92;*uživatelské_jméno*.  
   
    **Doména připojená k virtuálnímu jevu**: Pokud virtuální virtuální_mon patří do domény, zadejte uživatelské jméno ve formátu *Domain*&#92;*Username*. Účet také musí být členem skupiny Administrators nebo musí mít udělené oprávnění ke vzdálenému přístupu k virtuálnímu počítači.
   
    **Řadič domény**: Pokud je virtuální virtuální počítače řadič domény, zadejte uživatelské jméno a heslo účtu správce domény pro tuto doménu.
4. Výběrem **možnosti Ano** ověřte identitu virtuálního počítače a dokončete přihlášení.
   
   ![Snímek obrazovky zobrazující zprávu o ověření identity virtuálního počítače](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Pokud je tlačítko **Připojit** na portálu šedé a nejste připojeni k Azure prostřednictvím [expresní trasy](../../expressroute/expressroute-introduction.md) nebo připojení [VPN site-to-site,](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) budete muset vytvořit a přiřadit virtuálnímu počítači veřejnou IP adresu, než budete moct používat RDP. Další informace najdete [v tématu Veřejné IP adresy v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Připojení k virtuálnímu počítači pomocí PowerShellu

 

Pokud používáte PowerShell a máte nainstalovaný modul Azure PowerShell, můžete se také připojit pomocí `Get-AzRemoteDesktopFile` rutiny, jak je znázorněno níže.

Tento příklad okamžitě spustí připojení RDP a provede vás podobnými výzvami, jak je uvedeno výše.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Soubor RDP můžete také uložit pro budoucí použití.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Další kroky
Pokud máte potíže s připojením, [přečtěte si článek Poradce při potížích s připojením ke vzdálené ploše](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

