---
title: Přihlaste se k classic virtuálního počítače Azure | Microsoft Docs
description: Použití portálu Azure k přihlášení k Windows virtuální počítač vytvořený s modelem nasazení classic.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 87ecc65d2d4802ae826f3260b66b26e0bbe414e6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012355"
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Přihlášení k virtuálnímu počítači s Windows pomocí portálu Azure
Na portálu Azure můžete použít **Connect** tlačítko spuštění relace vzdálené plochy a přihlaste se k virtuální počítač s Windows.

Chcete se připojit k virtuální počítač s Linuxem? V tématu [přihlášení do virtuálního počítače se systémem Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Informace o tom, jak se přihlásit k virtuálnímu počítači pomocí modelu Resource Manager najdete v tématu [zde](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači
1. Přihlaste se k portálu Azure.
2. Klikněte na virtuální počítač, který chcete získat přístup. Název je uveden v **všechny prostředky** podokně.

    ![Virtuální počítač – umístění](./media/connect-logon/azureportaldashboard.png)

1. Klikněte **Connect** tlačítko na stránce vlastností virtuálního počítače. 
2. V **připojit k virtuálnímu počítači** , zachovat, vyberte požadované možnosti a klikněte na tlačítko **soubor RDP Stáhnout**.
2. Otevřete stažený soubor RDP a klikněte na tlačítko **Connect** po zobrazení výzvy. 
2. Zobrazí se upozornění, že soubor `.rdp` je od neznámého vydavatele. To je normální. Pokračujte kliknutím na **Připojit** v okně Připojení ke vzdálené ploše.
   
    ![Snímek obrazovky upozornění na neznámého vydavatele](./media/connect-logon/rdp-warn.png)
3. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte přihlašovací údaje k účtu virtuálního počítače a pak klikněte na **OK**.
   
     **Místní účet** – Většinou je to uživatelské jméno a heslo k místnímu účtu, které jste zadávali při vytváření virtuálního počítače. V tomto případě je doménou název virtuálního počítače ve formátu *název_virtuálního_počítače*&#92;*uživatelské_jméno*.  
   
    **Virtuální počítač připojený k doméně** – Pokud virtuální počítač patří do domény, zadejte uživatelské jméno ve formátu *doména*&amp;#92;*uživatelské_jméno*. Účet také musí být členem skupiny Administrators nebo musí mít udělené oprávnění ke vzdálenému přístupu k virtuálnímu počítači.
   
    **Řadič domény** – Pokud je virtuální počítač řadičem domény, zadejte uživatelské jméno a heslo účtu správce domény pro danou doménu.
4. Kliknutím na **Ano** ověřte identitu virtuálního počítače a dokončete přihlášení.
   
   ![Snímek obrazovky zobrazující zprávu o ověření identity virtuálního počítače](./media/connect-logon/cert-warning.png)

## <a name="next-steps"></a>Další postup
* Pokud **Connect** tlačítko je neaktivní nebo máte další problémy s připojením ke vzdálené ploše, zkuste resetuje se konfigurace. Klikněte na tlačítko **obnovte vzdálený přístup** na řídicím panelu virtuálního počítače.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Řešení problémů s heslo zkuste resetovat ho. Klikněte na tlačítko **resetovat heslo** podél levého okraje virtuálního počítače v části řídicího panelu, **podporu + Poradce při potížích s**.

    ![Resetování hesla](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Pokud tyto tipy nefungují nebo nejsou, co potřebujete, najdete v části [řešení potíží s vzdálené plochy připojení k systému Windows Azure virtuální počítač](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Tento článek vás provede diagnostikou a řešením běžných problémů.
