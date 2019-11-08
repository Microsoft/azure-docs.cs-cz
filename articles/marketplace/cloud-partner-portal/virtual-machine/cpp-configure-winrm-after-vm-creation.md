---
title: Konfigurace WinRM po vytvoření virtuálního počítače Azure | Azure Marketplace
description: Vysvětluje, jak nakonfigurovat Vzdálená správa systému Windows (WinRM) Po vytvoření virtuálního počítače hostovaného v Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: ae5a55c6d640852cbd873bc6b36e502b5fe17165
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817951"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurace WinRM po vytvoření virtuálního počítače

Tento článek vysvětluje, jak nakonfigurovat stávající virtuální počítač hostovaný v Azure, aby povoloval službu WinRM přes protokol HTTPS.  Tato konfigurace se vztahuje pouze na virtuální počítače se systémem Windows a vyžaduje následující proces se dvěma kroky:

1. Povolte přenos portů pro protokol WinRM přes HTTPS.  Toto nastavení pro virtuální počítač budete konfigurovat v Azure Portal.
2. Nakonfigurujte virtuální počítač tak, aby povoloval WinRM spuštěním zadaných skriptů PowerShellu.


## <a name="enabling-port-traffic"></a>Povolení přenosů portů

Protokol WinRM přes HTTPS používá port 5896, který není ve výchozím nastavení povolený u předem nakonfigurovaných virtuálních počítačů s Windows, které jsou nabízené na Azure Marketplace. K povolení tohoto protokolu použijte následující postup k přidání nového pravidla do skupiny zabezpečení sítě (NSG) pomocí [Azure Portal](https://portal.azure.com).  Další informace o skupin zabezpečení sítě najdete v tématu [skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Přejděte na **virtuální počítače** s oknem >  <*název virtuálního počítače*>   **> nastavení/síť**.
2.  Kliknutím na název NSG (v tomto příkladu **testvm11002**) zobrazíte jeho vlastnosti:

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
