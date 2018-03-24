---
title: Nainstalovat doménovou strukturu služby Active Directory na virtuální síť Azure | Microsoft Docs
description: Kurz, který vysvětluje, jak vytvořit novou doménovou strukturu služby Active Directory na virtuálním počítači (VM) na virtuální síť Azure.
services: active-directory, virtual-network
keywords: 'služby Active directory virtuálního počítače, instalace služby active directory doménové struktury, videa služby azure active directory '
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
tags: ''
ms.assetid: eb7170d0-266a-4caa-adce-1855589d65d1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: joflore
ms.openlocfilehash: 0b98b475f49847589398ca743fc3ac3b8bcbb381
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Instalace nové doménové struktury služby Active Directory na virtuální síť Azure
Tento článek ukazuje, jak vytvořit nového prostředí služby Active Directory pro Windows Server na virtuálním počítači (VM) na [virtuální síť Azure](../virtual-network/virtual-networks-overview.md). V takovém případě virtuální síť Azure není připojen k místní síti.

Může být také zájem o tyto související články:

* Video, které představuje tyto kroky, najdete v části [instalace nové doménové struktury služby Active Directory na virtuální síť Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* Volitelně můžete [konfigurace VPN typu site-to-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) a pak nainstalovat novou doménovou strukturu nebo rozšířit doménovou strukturu místní virtuální sítě Azure. Tyto pokyny najdete v části [instalaci řadiče domény repliky Active Directory ve virtuální síti Azure](active-directory-install-replica-active-directory-domain-controller.md).
* Koncepční informace o instalaci služby Active Directory Domain Services (AD DS) na virtuální síť Azure, najdete v části [pokyny pro nasazení systému Windows Server Active Directory ve virtuálních počítačích Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagram scénáře
V tomto scénáři externí uživatelé potřebovat přístup k aplikacím, které běží na servery připojené k doméně. Virtuální počítače, které používat aplikačních serverů a virtuálních počítačů, které spustit řadiče domény jsou nainstalované v vlastní cloudové služby v rámci virtuální sítě Azure. Jsou taky součástí sadu dostupnosti pro lepší odolnost proti chybám.

![Doménové struktury Active Directory na virtuální počítače ve virtuální síti Azure ][1] 7

## <a name="how-does-this-differ-from-on-premises"></a>Jak to se liší od místně?
Není k dispozici mnoho rozdíl mezi instalací řadiče domény v Azure a místní. Hlavní rozdíly jsou uvedené v následující tabulce.

| Konfigurace... | Lokálně | Virtuální síť Azure |
| --- | --- | --- |
| **IP adresa pro řadič domény** |Přiřadit statickou IP adresu ve vlastnostech síťového adaptéru |Spusťte rutinu Set-AzureStaticVNetIP přiřadit statickou IP adresu |
| **Překladač služby DNS klienta** |Nastavení adresy serveru preferované a alternativní server DNS v síti adaptér vlastnosti členy domény |Nastavit adresu serveru DNS na vlastnosti virtuální sítě |
| **Úložiště databáze služby Active Directory** |Volitelně můžete změnit výchozí umístění úložiště z C:\ |Budete muset změnit výchozí umístění úložiště z C:\ |

## <a name="create-an-azure-virtual-network"></a>Vytvoření virtuální sítě Azure
1. Přihlaste se k portálu Azure.
2. Vytvořte virtuální síť. Klikněte na tlačítko **sítě** > **vytvořit virtuální síť**. Použijte hodnoty v následující tabulce dokončete průvodce.

   | Na této stránce průvodce... | Zadejte tyto hodnoty |
   | --- | --- |
   |  **Podrobnosti virtuální sítě** |<p>Název: Zadejte název pro vaši virtuální síť</p><p>Oblast: Vyberte nejbližší oblast</p> |
   |  **DNS a sítě VPN** |<p>Ponechat prázdné DNS server</p><p>Nemáte vyberte buď možnost VPN</p> |
   |  **Adresní prostory virtuální sítě** |<p>Název podsítě: Zadejte název pro podsíť</p><p>Počáteční IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p> |

## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Vytvořit virtuální počítače ke spuštění řadič domény a role serveru DNS
Opakujte tyto kroky k vytvoření virtuálních počítačů k hostování role řadiče domény, podle potřeby. Měli byste nasadit aspoň dva virtuální řadiče domény zajistit odolnost proti chybám a redundance. Pokud virtuální síť Azure zahrnuje aspoň dva řadiče domény, které jsou nakonfigurované podobně (to znamená, že jsou oba GC spuštění serveru DNS, a ani jeden z nich obsahuje všechny FSMO role a tak dále) pak umístit virtuální počítače, které běží ty řadiče domény v sadu dostupnosti pro lepší odolnost proti chybám.

Vytvoření virtuálních počítačů místo uživatelského rozhraní pomocí prostředí Windows PowerShell naleznete v tématu [vytvoření virtuálního počítače pomocí prostředí PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md) ukázka.

1. Na portálu Azure vyberte **nový** > **výpočetní**a potom vyberte virtuální počítač. Použijte následující hodnoty a dokončete průvodce. Přijměte výchozí hodnotu pro nastavení, není-li navrhované nebo vyžaduje jinou hodnotu.

   | Na této stránce průvodce... | Zadejte tyto hodnoty |
   | --- | --- |
   |  **Vyberte bitovou kopii** |Windows Server 2012 R2 Datacenter |
   |  **Konfigurace virtuálního počítače** |<p>Název virtuálního počítače: Zadejte název bez přípony (například AzureDC1).</p><p>Nové uživatelské jméno: Zadejte název uživatele. Tento uživatel bude členem místní skupiny Administrators na virtuálním počítači. Je nutné tento název se přihlásit k virtuálnímu počítači poprvé. Předdefinovaný účet s názvem správce nebude fungovat.</p><p>Nové heslo nebo potvrďte: Zadejte heslo</p> |
   |  **Konfigurace virtuálního počítače** |<p>Cloudové služby: Zvolte <b>vytvořte novou cloudovou službu</b> pro první virtuální počítač, vyberte tento stejný název cloudové služby při vytváření další virtuální počítače, který bude hostitelem role řadiče domény.</p><p>Název DNS cloudové služby: Zadejte globálně jedinečného názvu</p><p>Oblasti nebo skupiny vztahů nebo virtuální sítě: Zadejte název virtuální sítě (například WestUSVNet).</p><p>Účet úložiště: Zvolte <b>použít účet úložiště automaticky generované</b> pro první virtuální počítač a potom vyberte tento stejný název účtu úložiště při vytváření další virtuální počítače, který bude hostitelem role řadiče domény.</p><p>Skupinu dostupnosti: Zvolit <b>vytvořit skupinu dostupnosti</b>.</p><p>Název sady dostupnosti: Zadejte název pro dostupnost nastavit při vytváření první virtuální počítač a potom vyberte stejný název, když vytvoříte další virtuální počítače.</p> |
   |  **Konfigurace virtuálního počítače** |<p>Vyberte <b>nainstalujte agenta virtuálního počítače</b> a další rozšíření, budete potřebovat.</p> |
2. Připojte disk pro každý virtuální počítač, který se spustí roli serveru řadiče domény. Další disk je nutný k uložení databáze AD, protokoly a adresáře SYSVOL. Zadejte velikost disku (například 10 GB) a nechte **předvoleb mezipaměti hostitele** nastavena na **žádné**. Pokyny najdete v tématu [jak připojit datový Disk do virtuálního počítače s Windows](../virtual-machines/windows/attach-managed-disk-portal.md).
3. Po prvním přihlášení k virtuálnímu počítači, otevřete **správce serveru** > **Souborová služba a služba úložiště** k vytvoření svazku na tento disk pomocí systému souborů NTFS.
4. Vyhradit statickou IP adresu pro virtuální počítače, které budou spouštět roli řadiče domény. Můžete vyhradit statickou IP adresu, stažení služby instalace webové platformy společnosti Microsoft a [nainstalovat Azure PowerShell](/powershell/azure/overview) a spusťte rutinu Set-AzureStaticVNetIP. Příklad:

    `Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM`

Další informace o nastavení statické IP adresy najdete v tématu [nakonfigurovat statické interní IP adresy pro virtuální počítač](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>Nainstalujte Windows Server Active Directory
Pomocí stejné rutiny k [instalaci služby AD DS](https://technet.microsoft.com/library/jj574166.aspx) použít místní (tedy můžete uživatelského rozhraní, soubor odpovědí nebo prostředí Windows PowerShell). Musíte zadat přihlašovací údaje správce pro instalaci nové doménové struktury. Zadejte umístění pro databázi služby Active Directory, protokolů a adresáře SYSVOL, změňte výchozí umístění úložiště z jednotky operačního systému na další datový disk, který jste připojili k virtuálnímu počítači.

Po dokončení instalace řadiče domény znovu připojit k virtuálnímu počítači a přihlaste se k řadiči domény. Nezapomeňte zadat přihlašovací údaje do domény.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Obnovit server DNS pro virtuální síť Azure
1. Obnovte nastavení služby pro předávání DNS na novém serveru řadiče domény a DNS.
   1. Ve Správci serveru klikněte na tlačítko **nástroje** > **DNS**.
   2. V **Správce DNS**, klikněte pravým tlačítkem na název serveru DNS a klikněte na **vlastnosti**.
   3. Na **předávání** , klikněte na IP adresu předávání a klikněte na tlačítko **upravit**.  Vyberte IP adresu a klikněte na tlačítko **odstranit**.
   4. Klikněte na tlačítko **OK** zavřete editor a **Ok** zavřete vlastnosti serveru DNS.
2. Aktualizujte nastavení serveru DNS virtuální sítě.
   1. Klikněte na tlačítko **virtuální sítě** > dvakrát klikněte na virtuální sítě, které jste vytvořili > **konfigurace** > **servery DNS**, zadejte název a IP adresy jednoho z virtuálních počítačů používající role řadiče domény a DNS serveru a klikněte na tlačítko **Uložit**.
   2. Vyberte virtuální počítač a klikněte na **restartujte** k aktivaci virtuálního počítače ke konfiguraci nastavení překladu DNS s IP adresou nového serveru DNS.

## <a name="create-vms-for-domain-members"></a>Vytváření virtuálních počítačů pro členy domény
1. Opakujte tyto kroky k vytvoření virtuálních počítačů do spustit jako aplikační servery. Přijměte výchozí hodnotu pro nastavení, není-li navrhované nebo vyžaduje jinou hodnotu.

   | Na této stránce průvodce... | Zadejte tyto hodnoty |
   | --- | --- |
   |  **Vyberte bitovou kopii** |Windows Server 2012 R2 Datacenter |
   |  **Konfigurace virtuálního počítače** |<p>Název virtuálního počítače: Zadejte název bez přípony (například AppServer1).</p><p>Nové uživatelské jméno: Zadejte název uživatele. Tento uživatel bude členem místní skupiny Administrators na virtuálním počítači. Je nutné tento název se přihlásit k virtuálnímu počítači poprvé. Předdefinovaný účet s názvem správce nebude fungovat.</p><p>Nové heslo nebo potvrďte: Zadejte heslo</p> |
   |  **Konfigurace virtuálního počítače** |<p>Cloudové služby: Zvolte **vytvořte novou cloudovou službu** pro první virtuální počítač, vyberte tento stejný název cloudové služby při vytváření další virtuální počítače, který bude hostitelem aplikace.</p><p>Název DNS cloudové služby: Zadejte globálně jedinečného názvu</p><p>Oblasti nebo skupiny vztahů nebo virtuální sítě: Zadejte název virtuální sítě (například WestUSVNet).</p><p>Účet úložiště: Zvolte **použít účet úložiště automaticky generované** pro první virtuální počítač a potom vyberte tento stejný název účtu úložiště při vytváření další virtuální počítače, který bude hostovat aplikaci.</p><p>Skupinu dostupnosti: Zvolit **vytvořit skupinu dostupnosti**.</p><p>Název sady dostupnosti: Zadejte název pro dostupnost nastavit při vytváření první virtuální počítač a potom vyberte stejný název, když vytvoříte další virtuální počítače.</p> |
   |  **Konfigurace virtuálního počítače** |<p>Vyberte <b>nainstalujte agenta virtuálního počítače</b> a další rozšíření, budete potřebovat.</p> |
2. Po zřízení každý virtuální počítač, přihlaste se a připojení k doméně. V **správce serveru**, klikněte na tlačítko **místní Server** > **pracovní skupiny** > **změnu...** a pak vyberte **domény** a zadejte název vaší domény místně. Zadejte přihlašovací údaje uživatele domény a pak restartujte počítač k dokončení připojení k doméně.

Vytvoření virtuálních počítačů místo uživatelského rozhraní pomocí prostředí Windows PowerShell naleznete v tématu [použití Azure PowerShell k vytvoření a nastavení virtuálních počítačích se systémem Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Další informace o používání prostředí Windows PowerShell najdete v tématu [Začínáme s Azure rutiny](/powershell/azure/overview) a [Reference k rutině Azure](/powershell/azure/get-started-azureps).

## <a name="see-also"></a>Viz také
* [Postup instalace nové doménové struktury služby Active Directory na virtuální síť Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* [Pokyny pro nasazení systému Windows Server Active Directory na virtuálních počítačích Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Konfigurace VPN typu Site-to-Site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Instalaci řadiče domény repliky Active Directory v virtuální sítě Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IaaS IT specialista: Základy (01) virtuálního počítače](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS IT specialista: (05) vytváření virtuální sítě a připojení mezi různými místy](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Přehled služby Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Referenční informace k rutinám Azure](/powershell/azure/get-started-azureps)
* [Nastavení statické IP adresy virtuálních počítačů Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
* [Tom, jak přiřadit statickou IP adresu pro virtuální počítač Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
* [Instalace nové doménové struktury Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
* [Úvod do virtualizace služby Active Directory Domain Services (AD DS) (úroveň 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
