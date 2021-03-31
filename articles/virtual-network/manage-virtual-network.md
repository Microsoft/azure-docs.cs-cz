---
title: Vytvoření, změna nebo odstranění služby Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Vytvoření a odstranění virtuální sítě a změna nastavení, jako jsou servery DNS a adresní prostory IP adres pro existující virtuální síť.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 1ec4e26e266a1777857fe0e890b093f60a3597f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612520"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Vytvoření, změna nebo odstranění virtuální sítě

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Přečtěte si, jak vytvořit a odstranit virtuální síť a změnit nastavení, jako jsou servery DNS a adresní prostory IP adres pro existující virtuální síť. Pokud s virtuálními sítěmi začínáte, můžete o nich získat další informace v [přehledu virtuální sítě](virtual-networks-overview.md) nebo v tématu dokončení [kurzu](quick-create-portal.md). Virtuální síť obsahuje podsítě. Informace o tom, jak vytvářet, měnit a odstraňovat podsítě, najdete v tématu [Správa podsítí](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v jakékoli části tohoto článku proveďte následující úlohy:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.com a přihlaste se pomocí svého účtu Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy prostředí PowerShell, buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit, `az login` aby se vytvořilo připojení k Azure.
- Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v [oprávněních](#permissions).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Vyberte **+ vytvořit prostředek**  >  **sítě**  >  **virtuální síť**.
2. Zadejte nebo vyberte hodnoty pro následující nastavení a pak vyberte **vytvořit**:
   - **Název**: název musí být jedinečný ve [skupině prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) , kterou jste vybrali k vytvoření virtuální sítě v nástroji. Po vytvoření virtuální sítě už název nemůžete změnit. V průběhu času můžete vytvořit více virtuálních sítí. Návrhy pojmenování najdete v tématu zásady [vytváření názvů](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources). Následující zásady vytváření názvů vám můžou usnadnit správu více virtuálních sítí.
   - **Adresní prostor**: adresní prostor pro virtuální síť se skládá z jednoho nebo více nepřekrývajících se rozsahů adres, které jsou zadány v zápisu CIDR. Rozsah adres, který definujete, může být veřejný nebo privátní (RFC 1918). Bez ohledu na to, jestli jste rozsah adres definovali jako veřejný nebo soukromý, rozsah adres je dosažitelný jenom z virtuální sítě, ze vzájemně propojených virtuálních sítí a z jakýchkoli místních sítí, které jste připojili k virtuální síti. Nemůžete přidat tyto rozsahy adres:
     - 224.0.0.0/4 (vícesměrové vysílání)
     - 255.255.255.255/32 (všesměrové vysílání)
     - 127.0.0.0/8 (zpětná smyčka)
     - 169.254.0.0/16 (místní propojení)
     - 168.63.129.16/32 (interní služba DNS, DHCP a [Test stavu](../load-balancer/load-balancer-custom-probe-overview.md#probesource)Azure Load Balancer)

     I když při vytváření virtuální sítě na portálu můžete definovat pouze jeden rozsah adres, můžete přidat další rozsah adres do adresního prostoru po vytvoření virtuální sítě. Informace o tom, jak přidat rozsah adres do existující virtuální sítě, najdete v tématu [Přidání nebo odebrání rozsahu adres](#add-or-remove-an-address-range).

     > [!WARNING]
     > Pokud má virtuální síť rozsahy adres, které se překrývají s jinou virtuální sítí nebo místní sítí, nelze tyto dvě sítě připojit. Než nadefinujete rozsah adres, zvažte, jestli možná budete chtít virtuální síť připojit k jiným virtuálním sítím nebo místním sítím v budoucnu. Microsoft doporučuje konfigurovat rozsahy adres virtuální sítě s privátním adresním prostorem nebo veřejným adresním prostorem vlastněným vaší organizací.
     >

     - **Název podsítě**: název podsítě musí být v rámci virtuální sítě jedinečný. Po vytvoření podsítě už název podsítě nemůžete změnit. Portál vyžaduje, abyste při vytváření virtuální sítě definovali jednu podsíť, a to i v případě, že virtuální síť nemusí mít žádné podsítě. Na portálu můžete při vytváření virtuální sítě definovat jednu nebo víc podsítí. Další podsítě můžete do virtuální sítě přidat později po vytvoření virtuální sítě. Informace o přidání podsítě do virtuální sítě najdete v tématu [Správa podsítí](virtual-network-manage-subnet.md). Virtuální síť s více podsítěmi můžete vytvořit pomocí rozhraní příkazového řádku Azure CLI nebo PowerShellu.

       >[!TIP]
       >V některých případech můžou správci vytvořit různé podsítě pro filtrování nebo řízení směrování provozu mezi podsítěmi. Před definováním podsítí zvažte, jak budete chtít filtrovat a směrovat provoz mezi vašimi podsítěmi. Další informace o filtrování provozu mezi podsítěmi najdete v tématu [skupiny zabezpečení sítě](./network-security-groups-overview.md). Azure automaticky směruje provoz mezi podsítěmi, ale můžete přepsat výchozí trasy Azure. Další informace o směrování provozu ve výchozí podsíti Azure najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
       >

     - **Rozsah adres podsítě**: rozsah musí spadat do adresního prostoru, který jste zadali pro virtuální síť. Nejmenší rozsah, který můžete zadat, je/29, který poskytuje osm IP adres pro podsíť. Azure rezervuje v každé podsíti první a poslední adresu pro shodu protokolu. Tři další adresy jsou rezervované pro využití služeb Azure. V důsledku toho má virtuální síť s rozsahem adres podsítě/29 jenom tři použitelné IP adresy. Pokud plánujete připojit virtuální síť k bráně sítě VPN, musíte vytvořit podsíť brány. Přečtěte si další informace o [konkrétních požadavcích na rozsah adres pro podsítě brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Rozsah adres můžete po vytvoření podsítě změnit za určitých podmínek. Informace o tom, jak změnit rozsah adres podsítě, najdete v tématu [Správa podsítí](virtual-network-manage-subnet.md).
     - **Předplatné**: vyberte [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Stejnou virtuální síť nemůžete použít ve více než jednom předplatném Azure. Virtuální síť v jednom předplatném ale můžete připojit k virtuálním sítím v jiných předplatných s [partnerským vztahem virtuální sítě](virtual-network-peering-overview.md). Libovolný prostředek Azure, který se připojujete k virtuální síti, musí být ve stejném předplatném jako virtuální síť.
     - **Skupina prostředků**: Vyberte existující [skupinu prostředků](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) nebo vytvořte novou. Prostředek Azure, který se připojujete k virtuální síti, může být ve stejné skupině prostředků jako virtuální síť nebo v jiné skupině prostředků.
     - **Umístění**: vyberte [umístění](https://azure.microsoft.com/regions/)Azure, označované také jako oblast. Virtuální síť může být jenom v jednom umístění Azure. Můžete ale připojit virtuální síť v jednom umístění k virtuální síti v jiném umístění pomocí brány VPN. Libovolný prostředek Azure, který se připojujete k virtuální síti, musí být ve stejném umístění jako virtuální síť.

**Příkazy**

- Azure CLI: [AZ Network VNet Create](/cli/azure/network/vnet)
- PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Zobrazit virtuální sítě a nastavení

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
2. V seznamu virtuálních sítí vyberte virtuální síť, pro kterou chcete zobrazit nastavení.
3. Pro vybranou virtuální síť jsou uvedena následující nastavení:
   - **Přehled**: obsahuje informace o virtuální síti, včetně adresního prostoru a serverů DNS. Následující snímek obrazovky ukazuje nastavení přehledu pro virtuální síť s názvem **MyVNet**:

     ![Přehled síťového rozhraní](./media/manage-virtual-network/vnet-overview.png)

     Virtuální síť můžete přesunout do jiného předplatného nebo skupiny prostředků tak, že vyberete **změnit** vedle položky **Skupina prostředků** nebo **název předplatného**. Informace o tom, jak přesunout virtuální síť, najdete v tématu [Přesunutí prostředků do jiné skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Článek obsahuje seznam požadovaných součástí a Postup přesunutí prostředků pomocí Azure Portal, PowerShellu a rozhraní příkazového řádku Azure CLI. Všechny prostředky, které jsou připojené k virtuální síti, se musí přesouvat s virtuální sítí.
   - **Adresní prostor**: zobrazí se adresní prostory, které jsou přiřazené k virtuální síti. Pokud chcete zjistit, jak přidat a odebrat rozsah adres do adresního prostoru, proveďte kroky v tématu [Přidání nebo odebrání rozsahu adres](#add-or-remove-an-address-range).
   - **Připojená zařízení**: zobrazí se všechny prostředky, které jsou připojené k virtuální síti. Na předchozím snímku obrazovky jsou k virtuální síti připojena tři síťová rozhraní a jeden nástroj pro vyrovnávání zatížení. Zobrazí se všechny nové prostředky, které vytvoříte a připojíte k virtuální síti. Pokud odstraníte prostředek, který byl připojen k virtuální síti, již se v seznamu nezobrazí.
   - **Podsítě**: zobrazí se seznam podsítí, které existují ve virtuální síti. Informace o tom, jak přidat a odebrat podsíť, najdete v tématu [Správa podsítí](virtual-network-manage-subnet.md).
   - **Servery DNS**: můžete určit, jestli má interní server DNS nebo vlastní server DNS k dispozici překlad IP adres pro zařízení, která jsou připojená k virtuální síti. Když vytvoříte virtuální síť pomocí Azure Portal, servery DNS Azure se ve výchozím nastavení používají k překladu názvů v rámci virtuální sítě. Chcete-li upravit servery DNS, proveďte kroky v části [Změna serverů DNS](#change-dns-servers) v tomto článku.
   - **Partnerské vztahy**: Pokud existují existující partnerské vztahy v rámci předplatného, jsou zde uvedeny. Můžete zobrazit nastavení pro existující partnerské vztahy nebo vytvořit, změnit nebo odstranit partnerské vztahy. Další informace o partnerských vztazích najdete v tématu věnovaném [partnerským vztahům virtuální sítě](virtual-network-peering-overview.md).
   - **Vlastnosti**: zobrazí nastavení virtuální sítě, včetně ID prostředku virtuální sítě a předplatného Azure, ve kterém se nachází.
   - **Diagram**: diagram poskytuje vizuální znázornění všech zařízení, která jsou připojená k virtuální síti. Diagram obsahuje některé klíčové informace o zařízení. Chcete-li spravovat zařízení v tomto zobrazení, vyberte v diagramu zařízení.
   - **Běžná nastavení Azure**: Další informace o běžných nastaveních Azure najdete v následujících informacích:
     - [Protokol aktivit](../azure-monitor/essentials/platform-logs-overview.md)
     - [Řízení přístupu (IAM)](../role-based-access-control/overview.md)
     - [Značky](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Zámky](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Automatizační skript](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Příkazy**

- Azure CLI: [AZ Network VNet show](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Přidat nebo odebrat rozsah adres

Můžete přidat a odebrat rozsahy adres pro virtuální síť. V zápisu CIDR se musí zadat rozsah adres a nemůže se překrývat s ostatními rozsahy adres ve stejné virtuální síti. Rozsahy adres, které definujete, můžou být veřejné nebo privátní (RFC 1918). Bez ohledu na to, jestli jste rozsah adres definovali jako veřejný nebo soukromý, rozsah adres je dosažitelný jenom z virtuální sítě, ze vzájemně propojených virtuálních sítí a z jakýchkoli místních sítí, které jste připojili k virtuální síti. 

Rozsah adres pro virtuální síť můžete snížit, pokud stále obsahuje rozsahy všech přidružených podsítí. Navíc můžete rozsah adres zvětšit, například změnou a/16 na/8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Nemůžete přidat tyto rozsahy adres:

- 224.0.0.0/4 (vícesměrové vysílání)
- 255.255.255.255/32 (všesměrové vysílání)
- 127.0.0.0/8 (zpětná smyčka)
- 169.254.0.0/16 (místní propojení)
- 168.63.129.16/32 (interní služba DNS, DHCP a [Test stavu](../load-balancer/load-balancer-custom-probe-overview.md#probesource)Azure Load Balancer)

Postup přidání nebo odebrání rozsahu adres:

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
2. V seznamu virtuálních sítí vyberte virtuální síť, pro kterou chcete přidat nebo odebrat rozsah adres.
3. V části **Nastavení** vyberte **adresní prostor**.
4. Proveďte jednu z následujících možností:
    - **Přidejte rozsah adres**: zadejte nový rozsah adres. Rozsah adres se nemůže překrývat s existujícím rozsahem adres, který je definován pro virtuální síť.
    - **Odeberte rozsah adres**: na pravé straně rozsahu adres, který chcete odebrat, vyberte **...** a pak vyberte **Odebrat**. Pokud podsíť existuje v rozsahu adres, nemůžete tento rozsah adres odebrat. Chcete-li odebrat rozsah adres, je nutné nejprve odstranit všechny podsítě (a všechny prostředky v podsítích), které existují v rozsahu adres.
5. Vyberte **Uložit**.

**Příkazy**

- Azure CLI: [AZ Network VNet Update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Změna serverů DNS

Všechny virtuální počítače, které jsou připojené k virtuální síti, se zaregistrují na serverech DNS, které zadáte pro virtuální síť. K překladu IP adres používají taky zadaný server DNS. Každé síťové rozhraní (NIC) ve virtuálním počítači může mít vlastní nastavení serveru DNS. Pokud má síťový adaptér vlastní nastavení serveru DNS, přepíše nastavení serveru DNS pro virtuální síť. Další informace o nastavení DNS síťových adaptérů najdete v tématu [úlohy a nastavení síťového rozhraní](virtual-network-network-interface.md#change-dns-servers). Další informace o překladu názvů pro virtuální počítače a instance rolí v Azure Cloud Services najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](virtual-networks-name-resolution-for-vms-and-role-instances.md). Přidání, změna nebo odebrání serveru DNS:

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
2. V seznamu virtuálních sítí vyberte virtuální síť, pro kterou chcete změnit servery DNS.
3. V části **Nastavení** vyberte **servery DNS**.
4. Vyberte jednu z následujících možností:
   - **Výchozí (poskytováno Azure)**: všechny názvy prostředků a privátní IP adresy se automaticky zaregistrují na Azure DNS servery. Můžete přeložit názvy mezi prostředky, které jsou připojené ke stejné virtuální síti. Tuto možnost nelze použít k překladu názvů mezi virtuálními sítěmi. Chcete-li přeložit názvy mezi virtuálními sítěmi, je nutné použít vlastní server DNS.
   - **Vlastní**: můžete přidat jeden nebo více serverů, až do limitu Azure pro virtuální síť. Další informace o omezeních serveru DNS najdete v tématu [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Máte tyto možnosti:
   - **Přidat adresu**: Přidá server do seznamu serverů DNS virtuální sítě. Tato možnost také zaregistruje server DNS pomocí Azure. Pokud jste už zaregistrovali server DNS s Azure, můžete v něm vybrat tento server DNS.
   - **Odebrat adresu**: vedle serveru, který chcete odebrat, vyberte **...** a pak **odeberte**. Odstranění serveru odebere server jenom z tohoto seznamu virtuálních sítí. Server DNS zůstává zaregistrovaný v Azure pro vaše jiné virtuální sítě, které se mají používat.
   - **Změna pořadí adres serverů DNS**: je důležité ověřit, že vaše servery DNS vypíšete ve správném pořadí pro vaše prostředí. Seznamy serverů DNS se používají v pořadí, v jakém jsou určeny. Nefungují jako nastavení kruhového dotazování. Pokud je možné dosáhnout prvního serveru DNS v seznamu, klient použije tento server DNS bez ohledu na to, jestli server DNS funguje správně. Odeberte všechny servery DNS, které jsou v seznamu, a pak je přidejte zpět v požadovaném pořadí.
   - **Změnit adresu**: zvýrazněte v seznamu Server DNS a pak zadejte novou adresu.
5. Vyberte **Uložit**.
6. Restartujte virtuální počítače, které jsou připojené k virtuální síti, takže se jim přiřadí nové nastavení serveru DNS. Virtuální počítače budou nadále používat aktuální nastavení DNS, dokud nebudou restartováni.

**Příkazy**

- Azure CLI: [AZ Network VNet Update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Odstranění virtuální sítě

Virtuální síť můžete odstranit jenom v případě, že k ní nejsou připojené žádné prostředky. Pokud jsou k dispozici prostředky připojené k jakékoli podsíti v rámci virtuální sítě, je třeba nejprve odstranit prostředky, které jsou připojeny ke všem podsítím v rámci virtuální sítě. Postup odstranění prostředku se liší v závislosti na prostředku. Pokud se chcete dozvědět, jak odstranit prostředky, které jsou připojené k podsítím, přečtěte si dokumentaci pro každý typ prostředku, který chcete odstranit. Odstranění virtuální sítě:

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
2. V seznamu virtuálních sítí vyberte virtuální síť, kterou chcete odstranit.
3. Výběrem možnosti **připojená zařízení** v části **Nastavení** ověřte, že se k virtuální síti nepřipojují žádná zařízení. Pokud máte připojená zařízení, musíte je odstranit, abyste mohli odstranit virtuální síť. Pokud neexistují žádná připojená zařízení, vyberte **Přehled**.
4. Vyberte **Odstranit**.
5. Pokud chcete potvrdit odstranění virtuální sítě, vyberte **Ano**.

**Příkazy**

- Azure CLI: [odstranění síťové virtuální sítě Azure](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Oprávnění

Aby bylo možné provádět úlohy s virtuálními sítěmi, musí být váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, která je přiřazena k příslušným akcím uvedeným v následující tabulce:

| Akce                                  |   Name                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft. Network/virtualNetworks/Read   |   Čtení virtuální sítě              |
|Microsoft. Network/virtualNetworks/Write  |   Vytvoření nebo aktualizace virtuální sítě  |
|Microsoft. Network/virtualNetworks/DELETE |   Odstranění virtuální sítě            |

## <a name="next-steps"></a>Další kroky

- Vytvoření virtuální sítě pomocí [PowerShellu](powershell-samples.md) nebo ukázkových skriptů [Azure CLI](cli-samples.md) nebo používání [šablon Azure správce prostředků](template-samples.md)
- Vytvoření a přiřazení [definic Azure Policy](./policy-reference.md) pro virtuální sítě
