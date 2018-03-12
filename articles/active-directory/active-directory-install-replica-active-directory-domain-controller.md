---
title: "Instalace repliky řadiče domény pro doménu služby Active Directory v místě na virtuálních počítačích Azure | Microsoft Docs"
description: "Postup instalace repliky řadiče domény pro doménu služby Active Directory v místě na virtuálních počítačích Azure (VM) ve virtuální sítě Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: f4e64fbc6c2fda026297b69bd54471d49b6785a1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Instalace repliky řadiče domény služby Active Directory v virtuální sítě Azure
Tento článek popisuje postup instalace dalších řadičů domény (řadiče domény), který se má použít jako repliku řadiče domény pro doménu služby Active Directory v místě na virtuálních počítačích Azure (VM) ve virtuální sítě Azure. Můžete také [nainstalovat doménovou strukturu Windows Server Active Directory na virtuální síť Azure](active-directory-new-forest-virtual-machine.md). Jak nainstalovat na virtuální síť Azure Active Directory Domain Services (AD DS), najdete v článku [pokyny pro nasazení systému Windows Server Active Directory ve virtuálních počítačích Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagram scénáře
V tomto scénáři externí uživatelé potřebovat přístup k aplikacím, které běží na servery připojené k doméně. Virtuální počítače, na kterých běží na aplikační servery a řadičů domény repliky jsou nainstalovány ve virtuální sítě Azure. Virtuální síť může být připojen k místní síti pomocí [ExpressRoute](../expressroute/expressroute-locations-providers.md), nebo můžete použít [site-to-site VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) připojení, jak je znázorněno: 

![Diagram pf repliky řadiče domény služby Active Directory síť Azure][1]

Aplikační servery a řadiče domény jsou nasazeny v rámci samostatné cloudové služby k distribuci výpočetní zpracování a v rámci skupiny dostupnosti pro lepší odolnost proti chybám.
Řadiče domény replikovat mezi sebou a se místní řadiče domény pomocí replikace služby Active Directory. Žádné nástrojů pro synchronizaci, je potřeba.

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>Vytvořit síť místní služby Active Directory pro virtuální síť Azure
Můžete vytvořit lokality ve službě Active Directory, představující oblast sítě odpovídající virtuální síť. Tento web, může vám pomůžou optimalizovat ověřování, replikace a další operace umístění řadiče domény. Následující kroky popisují, jak vytvořit web a pro další informace viz [přidání nového serveru](https://technet.microsoft.com/library/cc781496.aspx).

1. Otevřete lokality a služby Active Directory: **správce serveru** > **nástroje** > **lokality a služby Active Directory**.
2. Vytvořit web představující oblast, kde jste vytvořili virtuální sítě Azure: klikněte na tlačítko **lokality** > **akce** > **nové lokality** > zadejte název nové lokality, jako je například Azure USA – západ > vyberte propojení lokalit > **OK**.
3. Vytvořte podsíť a přidružit nové lokality: dvakrát klikněte na **lokality** > klikněte pravým tlačítkem na **podsítě** > **novou podsíť** > zadejte rozsah IP adres ve virtuální síti (například 10.1.0.0/16 v diagramu scénář) > vyberte nový web Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Vytvoření virtuální sítě Azure
Vytvoření virtuální sítě Azure a nastavením sítě site-to-site VPN, postupujte podle kroků zahrnut ve článku [vytvořit připojení Site-to-Site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). 

Můžete také nakonfigurovat bránu virtuální sítě k vytvoření bezpečného připojení site-to-site VPN. Vytvořte připojení site-to-site VPN mezi nové virtuální sítě a místní zařízení VPN. Pokyny najdete v tématu [konfigurovat bránu virtuální sítě](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="create-azure-vms-for-the-dc-roles"></a>Vytvořit virtuální počítače Azure pro role řadiče domény
Chcete-li vytvořit virtuální počítače na hostiteli role řadiče domény, opakujte kroky v [vytvoření virtuálního počítače s Windows pomocí portálu Azure](../virtual-machines/windows/quick-create-portal.md) podle potřeby. Nasaďte aspoň dva virtuální řadiče domény zajistit odolnost proti chybám a redundance. Pokud virtuální síť Azure má aspoň dva řadiče domény, které jsou nakonfigurované podobně, můžete umístit virtuální počítače, které běží tyto řadiče domény v sadu dostupnosti pro lepší odolnost proti chybám.

K vytvoření virtuálních počítačů pomocí prostředí Windows PowerShell místo portálu Azure, najdete v části [použití Azure PowerShell k vytvoření a nastavení virtuálních počítačích se systémem Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Vyhradit statickou IP adresu pro virtuální počítače, které budou spouštět roli řadiče domény. Můžete vyhradit statickou IP adresu, stažení služby instalace webové platformy společnosti Microsoft a [nainstalovat Azure PowerShell](/powershell/azure/overview) a spusťte rutinu Set-AzureStaticVNetIP. Příklad:

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
Další informace o nastavení statické IP adresy najdete v tématu [nakonfigurovat statické interní IP adresy pro virtuální počítač](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Nainstalovat službu AD DS na virtuálních počítačích Azure
Přihlaste se k virtuálnímu počítači a ověřte, že máte připojení site-to-site VPN nebo ExpressRoute připojení k prostředkům ve vaší místní síti. Potom nainstalujte službu AD DS na virtuálních počítačích Azure. Můžete použít stejný postup, který použijete k instalaci dalšího řadiče domény v místní síti (uživatelského rozhraní, prostředí Windows PowerShell nebo pomocí souboru odpovědí). Při instalaci služby AD DS, zkontrolujte, zda že zadáte nový svazek pro umístění databáze AD, protokoly a adresáře SYSVOL. Pokud potřebujete aktualizačnímu programu na instalaci služby AD DS, najdete v části [instalace služby Active Directory Domain Services (úroveň 100)](https://technet.microsoft.com/library/hh472162.aspx) nebo [instalace řadiče domény repliky Windows serveru 2012 v existující doméně (úroveň 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Znovu nakonfigurujte server DNS pro virtuální síť
1. Získat seznam názvů virtuálních sítí v [portál Azure](https://portal.azure.com), vyhledejte *virtuální sítě*, pak vyberte **virtuální sítě** pro zobrazení seznamu. 
2. Otevřete virtuální síť, kterou chcete spravovat, a potom [změnit konfiguraci IP adresy serverů DNS pro vaši virtuální síť](../virtual-network/manage-virtual-network.md#change-dns-servers) pro používání statických IP adres přiřazené repliky řadiče domény místo IP adresy pro místní servery DNS.
3. Zajistit, aby všechny řadiče domény virtuální počítače replik na virtuální síť nakonfigurované pro použití serverů DNS ve virtuální síti:
  1. Vyberte **virtuální počítače**.
  2. Vyberte virtuální počítače a pak vyberte **restartujte**. 
  3. Počkejte, dokud je virtuální počítač **systémem** znovu a pak se přihlaste do ní.

## <a name="create-vms-for-application-servers"></a>Vytvořit virtuální počítače pro aplikační servery

Chcete-li vytvořit virtuální počítače k hostování role aplikačního serveru, opakujte kroky v [vytvoření virtuálního počítače s Windows pomocí portálu Azure](../virtual-machines/windows/quick-create-portal.md) podle potřeby. K vytvoření virtuálních počítačů pomocí Microsoft PowerShell místo portálu Azure, najdete v části [použití Azure PowerShell k vytvoření a konfiguraci virtuálních počítačích se systémem Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Následující tabulka obsahuje návrhy nastavení.

| Nastavení | Hodnoty |
| --- | --- |
|  **Vyberte bitovou kopii** | Windows Server 2012 R2 Datacenter |
|  **Konfigurace virtuálního počítače** |<p>Název virtuálního počítače: Zadejte název bez přípony (například AppServer1).</p><p>Nové uživatelské jméno: Zadejte název uživatele. Tento uživatel bude členem místní skupiny Administrators na virtuálním počítači. Je nutné tento název se přihlásit k virtuálnímu počítači poprvé. Předdefinovaný účet s názvem správce nebude fungovat.</p><p>Nové heslo nebo potvrďte: Zadejte heslo</p> |
|  **Konfigurace virtuálního počítače** |<p>Cloudové služby: Zvolte **vytvořte novou cloudovou službu** pro první virtuální počítač, vyberte tento stejný název cloudové služby při vytváření další virtuální počítače, který bude hostitelem aplikace.</p><p>Název DNS cloudové služby: Zadejte globálně jedinečného názvu</p><p>Oblasti nebo skupiny vztahů nebo virtuální sítě: Zadejte název virtuální sítě (například WestUSVNet).</p><p>Účet úložiště: Zvolte **použít účet úložiště automaticky generované** pro první virtuální počítač a potom vyberte tento stejný název účtu úložiště při vytváření další virtuální počítače, který bude hostovat aplikaci.</p><p>Skupinu dostupnosti: Zvolit **vytvořit skupinu dostupnosti**.</p><p>Název sady dostupnosti: Zadejte název pro dostupnost nastavit při vytváření první virtuální počítač a potom vyberte stejný název, když vytvoříte další virtuální počítače.</p> |
|  **Konfigurace virtuálního počítače** |<p>Vyberte <b>nainstalujte agenta virtuálního počítače</b> a další rozšíření, budete potřebovat.</p> |
  
Po zřízení každý virtuální počítač, přihlaste se a připojení k doméně. 
1. V **správce serveru** &gt; **místní Server** &gt; **pracovní skupiny** &gt; **změnu...** , vyberte **domény**.
2. Zadejte název vaší domény místně. 
3. Zadejte přihlašovací údaje uživatele domény.
4. Restartujte virtuální počítač.

## <a name="additional-resources"></a>Další zdroje informací:

* Další informace o používání prostředí Windows PowerShell najdete v tématu [Začínáme s Azure rutiny](/powershell/azure/overview) a [Reference k rutině Azure](/powershell/azure/get-started-azureps).
* [Pokyny pro nasazení systému Windows Server Active Directory na virtuálních počítačích Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Postup nahrání existujících řadičů domény místní technologie Hyper-V do Azure pomocí Azure PowerShell](http://support.microsoft.com/kb/2904015)
* [Instalace nové doménové struktury služby Active Directory na virtuální síť Azure](active-directory-new-forest-virtual-machine.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IaaS IT specialista: Základy (01) virtuálního počítače](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS IT specialista: (05) vytváření virtuální sítě a připojení mezi různými místy](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Rutiny pro správu Azure](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
