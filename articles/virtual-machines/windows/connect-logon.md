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
ms.date: 04/11/2018
ms.author: cynthn
ms.openlocfilehash: 7c495a74ccbcad3ee18147726742ee59b65f1c0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Jak se připojit a přihlásit k virtuálnímu počítači s Windows v Azure
Pomocí tlačítka **Připojit** na webu Azure Portal spustíte z počítače s Windows relaci Vzdálené plochy (protokol RDP). Nejdřív se k virtuálnímu počítači připojíte a pak se k němu přihlásíte.

Pokud se pokoušíte připojit k virtuálnímu počítači s Windows z Macu, budete muset nainstalovat klienta protokolu RDP pro Mac jako [Vzdálená plocha Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači
1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com/).
2. V nabídce vlevo klikněte na **Virtual Machines**.
3. Ze seznamu vyberte virtuální počítač.
4. Nahoře na stránce pro virtuální počítač, klikněte ![Obrázek na tlačítko Připojit.](./media/connect-logon/connect.png) .
2. V **připojit k virtuálnímu počítači** vyberte požadované možnosti a klikněte na tlačítko **soubor RDP Stáhnout**.
2. Otevřete stažený soubor RDP a klikněte na tlačítko **Connect** po zobrazení výzvy. 
2. Zobrazí se upozornění, že soubor `.rdp` je od neznámého vydavatele. To je normální. Pokračujte kliknutím na **Připojit** v okně Připojení ke vzdálené ploše.
   
    ![Snímek obrazovky upozornění na neznámého vydavatele](./media/connect-logon/rdp-warn.png)
3. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte přihlašovací údaje k účtu virtuálního počítače a pak klikněte na **OK**.
   
     **Místní účet** – Většinou je to uživatelské jméno a heslo k místnímu účtu, které jste zadávali při vytváření virtuálního počítače. V tomto případě je doménou název virtuálního počítače ve formátu *název_virtuálního_počítače*&#92;*uživatelské_jméno*.  
   
    **Virtuální počítač připojený k doméně** – Pokud virtuální počítač patří do domény, zadejte uživatelské jméno ve formátu *doména*&amp;#92;*uživatelské_jméno*. Účet také musí být členem skupiny Administrators nebo musí mít udělené oprávnění ke vzdálenému přístupu k virtuálnímu počítači.
   
    **Řadič domény** – Pokud je virtuální počítač řadičem domény, zadejte uživatelské jméno a heslo účtu správce domény pro danou doménu.
4. Kliknutím na **Ano** ověřte identitu virtuálního počítače a dokončete přihlášení.
   
   ![Snímek obrazovky zobrazující zprávu o ověření identity virtuálního počítače](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Pokud je tlačítko **Připojit** na portálu zašedlé a nejste připojení k Azure přes [Express Route](../../expressroute/expressroute-introduction.md) nebo připojení [S2S (Site-to-site) VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), musíte před použitím protokolu RDP vytvořit veřejnou IP adresu a přiřadit ji svému virtuálnímu počítači. Podle potřeby si můžete přečíst další informace o [veřejných IP adresách v Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 


## <a name="next-steps"></a>Další postup
Pokud při připojování nastanou problémy, přečtěte si článek [Řešení problémů s připojením ke Vzdálené ploše](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tento článek vás provede diagnostikou a řešením běžných problémů.

