---
title: Konfigurace služby WinRM po vytvoření virtuálního počítače Azure | Azure Marketplace
description: Vysvětluje, jak nakonfigurovat vzdálenou správu systému Windows (WinRM) po vytvoření virtuálního počítače hostovaného v Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: 673fe1f31f6a8602225e7cde3bf1eb4c3b28b8a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278140"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurace služby WinRM po vytvoření virtuálního počítače

Tento článek vysvětluje, jak nakonfigurovat existující virtuální počítač (VM) hostovaný v Azure, aby povolil WinRM přes protokol HTTPS.  Tato konfigurace platí jenom pro virtuální počítače se systémem Windows a vyžaduje následující dvoustupňový proces:

1. Povolte přenosy portů pro protokol WinRM přes protokol HTTPS.  Toto nastavení nakonfigurujete pro virtuální počítač na webu Azure Portal.
2. Nakonfigurujte virtuální ms tak, aby povoloval službu WinRM spuštěním dodaných skriptů prostředí PowerShell.


## <a name="enabling-port-traffic"></a>Povolení přenosu portů

Protokol WinRM přes HTTPS používá port 5986, který není ve výchozím nastavení povolený na předkonfigurovaných virtuálních počítačích s Windows nabízených na Azure Marketplace. Chcete-li tento protokol povolit, přidejte pomocí následujících kroků nové pravidlo do skupiny zabezpečení sítě (NSG) s [portálem Azure](https://portal.azure.com).  Další informace o skupinách zabezpečení zabezpečení naleznete v [tématu Skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Přejděte k utoče **Virtuální počítače >**   < *název* >   virtuálního počítače>** Nastavení/sítě**.
2.  Kliknutím na název nsg (v tomto příkladu **testvm11002**) zobrazíte jeho vlastnosti:

    ![Vlastnosti skupiny zabezpečení sítě](./media/nsg-properties.png)
 
3. V části **Nastavení**vyberte **Příchozí pravidla zabezpečení,** abyste toto okno zobrazili.
4. Chcete-li vytvořit nové pravidlo `WinRM_HTTPS` volané pro port TCP 5986, klepněte na tlačítko **+Přidat.**

    ![Přidat pravidlo zabezpečení příchozí sítě](./media/nsg-new-rule.png)

5. Po dokončení dodání hodnot klepněte na **tlačítko OK.**  Seznam příchozích pravidel zabezpečení by měl obsahovat následující nové položky.

    ![Seznam pravidel zabezpečení příchozí sítě](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Konfigurace virtuálního počítače pro povolení služby WinRM 

Pomocí následujících kroků můžete povolit a nakonfigurovat funkci Vzdálené správy systému Windows na virtuálním počítači se systémem Windows.   

1. Navázání připojení ke vzdálené ploše k virtuálnímu počítači hostovanému v Azure.  Další informace najdete v tématu [Jak se připojit a přihlásit k virtuálnímu počítači Azure se systémem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Zbývající kroky se spustí na vašem virtuálním počítači.
2. Stáhněte si následující soubory a uložte je do složky na virtuálním počítači:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [Makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Otevřete **konzolu PowerShell** se zvýšenými oprávněními **(Spustit jako správce).** 
4. Spusťte následující příkaz, který zadá požadovaný parametr: plně kvalifikovaný název domény (FQDN) pro váš virtuální počítač: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Konfigurační skript prostředí Powershell 1](./media/powershell-file1.png)

    Tento skript závisí na další dva soubory jsou ve stejné složce.


## <a name="next-steps"></a>Další kroky

Po nakonfigurování služby WinRM jste připraveni [nasadit virtuální počítač z jeho základních virtuálních pevných disků](./cpp-deploy-vm-vhd.md).
