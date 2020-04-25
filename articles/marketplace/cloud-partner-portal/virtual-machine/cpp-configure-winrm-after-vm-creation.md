---
title: Konfigurace WinRM po vytvoření virtuálního počítače Azure | Azure Marketplace
description: Vysvětluje, jak nakonfigurovat Vzdálená správa systému Windows (WinRM) Po vytvoření virtuálního počítače hostovaného v Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: 3a67371ce6f951a9e446ab639ea5b59248b79565
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144147"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurace WinRM po vytvoření virtuálního počítače

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek virtuálních počítačů Azure do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Postupujte podle pokynů v tématu [Vytvoření virtuálního počítače Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) pro správu migrovaných nabídek.

Tento článek vysvětluje, jak nakonfigurovat stávající virtuální počítač hostovaný v Azure, aby povoloval službu WinRM přes protokol HTTPS.  Tato konfigurace se vztahuje pouze na virtuální počítače se systémem Windows a vyžaduje následující proces se dvěma kroky:

1. Povolte přenos portů pro protokol WinRM přes HTTPS.  Toto nastavení pro virtuální počítač budete konfigurovat v Azure Portal.
2. Nakonfigurujte virtuální počítač tak, aby povoloval WinRM spuštěním zadaných skriptů PowerShellu.


## <a name="enabling-port-traffic"></a>Povolení přenosů portů

Protokol WinRM přes HTTPS používá port 5986, který není ve výchozím nastavení povolený u předem nakonfigurovaných virtuálních počítačů s Windows, které jsou nabízené na Azure Marketplace. K povolení tohoto protokolu použijte následující postup k přidání nového pravidla do skupiny zabezpečení sítě (NSG) pomocí [Azure Portal](https://portal.azure.com).  Další informace o skupin zabezpečení sítě najdete v tématu [skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview).

1. Přejděte na **virtuální počítače s >**   < *název* >   virtuálního počítače **> nastavení/síť**.
2. Kliknutím na název NSG (v tomto příkladu **testvm11002**) zobrazíte jeho vlastnosti:

    ![Vlastnosti skupiny zabezpečení sítě](./media/nsg-properties.png)
 
3. V části **Nastavení**vyberte **příchozí pravidla zabezpečení** , aby se zobrazilo toto okno.
4. Kliknutím na **+ Přidat** vytvořte nové pravidlo s názvem `WinRM_HTTPS` pro port TCP 5986.

    ![Přidat pravidlo zabezpečení příchozí sítě](./media/nsg-new-rule.png)

5. Až skončíte s doplňováním hodnot, klikněte na **OK** .  Seznam příchozích pravidel zabezpečení by měl obsahovat následující nové položky.

    ![Výpis pravidel zabezpečení příchozí sítě](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Konfigurace virtuálního počítače pro povolení služby WinRM 

Pomocí následujícího postupu povolíte a nakonfigurujete funkci Vzdálená správa systému Windows na VIRTUÁLNÍm počítači s Windows.   

1. Navažte připojení vzdálené plochy k VIRTUÁLNÍmu počítači hostovanému v Azure.  Další informace najdete v tématu [jak se připojit a přihlásit k virtuálnímu počítači Azure s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Zbývající kroky se spustí na vašem VIRTUÁLNÍm počítači.
2. Stáhněte si následující soubory a uložte je do složky na vašem VIRTUÁLNÍm počítači:
    - [ConfigureWinRM. ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [Makecert. exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf. cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Otevřete **konzolu PowerShellu** se zvýšenými oprávněními (**Spustit jako správce**). 
4. Spusťte následující příkaz a zadejte požadovaný parametr: plně kvalifikovaný název domény (FQDN) pro váš virtuální počítač: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Konfigurační skript prostředí PowerShell 1](./media/powershell-file1.png)

    Tento skript závisí na dalších dvou souborech ve stejné složce.


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete WinRM, budete připraveni [nasadit virtuální počítač z jeho základních VHD](./cpp-deploy-vm-vhd.md).
