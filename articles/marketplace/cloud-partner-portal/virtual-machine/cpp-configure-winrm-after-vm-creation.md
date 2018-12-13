---
title: Konfigurace WinRM po vytvoření virtuálního počítače Azure | Dokumentace Microsoftu
description: Vysvětluje postup konfigurace vzdálené správy Windows (WinRM) po vytvoření virtuálních počítačů hostovaných v Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 6350d5bde737c46cf14a9aef75a7ec57260a6afa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196888"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurace WinRM po vytvoření virtuálního počítače

Tento článek vysvětluje, jak nakonfigurovat existující hostované v Azure virtuální počítač (VM) Chcete-li povolit službu WinRM prostřednictvím protokolu HTTPS.  Tato konfigurace se vztahuje pouze na virtuálních počítačů s Windows a vyžaduje následující dvoustupňový proces:

1. Povolení provozu na portu pro WinRM přes protokol HTTPS.  Toto nastavení pro váš virtuální počítač můžete nakonfigurovat na webu Azure Portal.
2. Konfigurace virtuálního počítače povolte WinRM spuštěním zadané skripty prostředí PowerShell.


## <a name="enabling-port-traffic"></a>Povolení přenosu portu

Služba WinRM přes protokol HTTPS používá port 5896, které není povoleno ve výchozím nastavení na předem nakonfigurovaných virtuálních počítačů Windows, které nabízíme na webu Azure Marketplace. Pokud chcete povolit tento protokol, následujícím postupem přidáte nové pravidlo na skupinu zabezpečení sítě (NSG) s [webu Azure portal](https://portal.azure.com).  Další informace o skupinách Nsg najdete v tématu [skupiny zabezpečení](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Přejděte do okna **virtuální počítače >**  <*název virtuálního počítače*>  **> Nastavení/síť**.
2.  Klikněte na název skupiny zabezpečení sítě (v tomto příkladu **testvm11002**) zobrazíte jeho vlastnosti:

    ![Vlastnosti skupiny zabezpečení sítě](./media/nsg-properties.png)
 
3. V části **nastavení**vyberte **příchozí pravidla zabezpečení** k zobrazení tohoto okna.
4. Klikněte na tlačítko **+ přidat** vytvořit nové pravidlo s názvem `WinRM_HTTPS` TCP port 5986.

    ![Přidat příchozí pravidlo zabezpečení sítě](./media/nsg-new-rule.png)

5. Klikněte na tlačítko **OK** po dokončení zadávání hodnot.  Seznam pravidla zabezpečení příchozích dat by měl obsahovat následující nové položky.

    ![Seznam pravidla zabezpečení příchozích dat](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Konfigurovat virtuální počítač povolit službu WinRM 

Pomocí následujících kroků povolte a nakonfigurujte funkci Vzdálená správa Windows na vašem virtuálním počítači Windows.   

1. Připojení vzdálené plochy k virtuálnímu počítači s hostovaným v Azure.  Další informace najdete v tématu [jak se připojit a přihlaste se k virtuálnímu počítači Azure s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Ve zbývajících krocích se spustí na virtuálním počítači.
2. Stáhnout následující soubory a uložit je do složky na svém virtuálním počítači:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Otevřít **konzolu Powershellu** se zvýšenými oprávněními (**spustit jako správce**). 
4. Spuštěním následujícího příkazu, zadáním povinný parametr: plně kvalifikovaný název domény (FQDN) pro virtuální počítač: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Skript prostředí PowerShell konfigurace 1](./media/powershell-file1.png)

    Tento skript závisí na příslušné dva soubory se ve stejné složce.


## <a name="next-steps"></a>Další postup

Po nakonfigurování služby WinRM, budete chtít [nasazení virtuálního počítače z jeho základních virtuální pevné disky](./cpp-deploy-vm-vhd.md).
