---
title: Vytvoření, změna nebo odstranění virtuální sítě Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak vytvořit, změnit nebo odstranit virtuální síť v Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 70523dc12f3f20362fcf4a2c3cb456a182038e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280232"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Vytvoření, změna nebo odstranění virtuální sítě

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Naučte se vytvářet a odstraňovat virtuální síť a měnit nastavení existující virtuální sítě, jako jsou servery DNS a adresní prostory IP. Pokud s virtuálními sítěmi teču, můžete se o nich dozvědět víc v [přehledu virtuální sítě](virtual-networks-overview.md) nebo v [kurzu](quick-create-portal.md). Virtuální síť obsahuje podsítě. Informace o vytváření, změně a odstraňování podsítí naleznete v tématu [Správa podsítí](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v libovolné části tohoto článku proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud pomocí příkazů Prostředí PowerShell k dokončení úloh v tomto článku, buď spustit příkazy v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud pomocí příkazů rozhraní Příkazového řádku Azure (CLI) k dokončení úloh v tomto článku, buď spustit příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také `az login` spustit k vytvoření připojení s Azure.
- Účet, ke kterému se přihlásíte nebo se s ním připojíte, musí být přiřazen k roli [síťového přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v [části Oprávnění](#permissions).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Vyberte **+ Vytvořit prostředek** > **síť** > **ová virtuální síť**.
2. Zadejte nebo vyberte hodnoty pro následující nastavení a pak vyberte **Vytvořit**:
   - **Název**: Název musí být jedinečný ve [skupině prostředků,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) kterou vyberete k vytvoření virtuální sítě. Po vytvoření virtuální sítě nelze název změnit. V průběhu času můžete vytvořit více virtuálních sítí. Návrhy pojmenování naleznete v [tématu Konvence pojmenování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources). Dodržování konvence pojmenování může usnadnit správu více virtuálních sítí.
   - **Adresní prostor**: Adresní prostor pro virtuální síť se skládá z jednoho nebo více nepřekrývajících se rozsahů adres, které jsou zadány v zápisu CIDR. Rozsah adres, který definujete, může být veřejný nebo soukromý (RFC 1918). Ať už definujete rozsah adres jako veřejný nebo soukromý, rozsah adres je dostupný pouze z virtuální sítě, z propojených virtuálních sítí a ze všech místních sítí, které jste připojili k virtuální síti. Nelze přidat následující rozsahy adres:
     - 224.0.0.0/4 (Vícesměrové vysílání)
     - 255.255.255.255/32 (Vysílání)
     - 127.0.0.0/8 (Zpětná smyčka)
     - 169.254.0.0/16 (Link-local)
     - 168.63.129.16/32 (interní sonda dns, DHCP a azure nástroje pro [vyrovnávání zatížení)](../load-balancer/load-balancer-custom-probe-overview.md#probesource)

     I když můžete definovat pouze jeden rozsah adres při vytváření virtuální sítě na portálu, můžete přidat další rozsahy adres do adresního prostoru po vytvoření virtuální sítě. Informace o tom, jak přidat rozsah adres do existující virtuální sítě, najdete v [tématu Přidání nebo odebrání rozsahu adres](#add-or-remove-an-address-range).

     >[!WARNING]
     >Pokud má virtuální síť rozsahy adres, které se překrývají s jinou virtuální sítí nebo místní sítí, nelze tyto dvě sítě připojit. Před definováním rozsahu adres zvažte, zda chcete v budoucnu připojit virtuální síť k jiným virtuálním sítím nebo místním sítím.
     >
     >

     - **Název podsítě**: Název podsítě musí být jedinečný v rámci virtuální sítě. Název podsítě nelze po vytvoření podsítě změnit. Portál vyžaduje, abyste při vytváření virtuální sítě definovali jednu podsíť, i když virtuální síť nemusí mít žádné podsítě. Na portálu můžete definovat pouze jednu podsíť při vytváření virtuální sítě. Další podsítě můžete přidat do virtuální sítě později po vytvoření virtuální sítě. Pokud chcete přidat podsíť do virtuální sítě, přečtěte si informace [o správě podsítí](virtual-network-manage-subnet.md). Pomocí rozhraní Azure CLI nebo PowerShellu můžete vytvořit virtuální síť, která má více podsítí.

       >[!TIP]
       >Správci někdy vytvářejí různé podsítě pro filtrování nebo řízení směrování provozu mezi podsítěmi. Před definováním podsítí zvažte, jak můžete chtít filtrovat a směrovat provoz mezi podsítěmi. Další informace o filtrování provozu mezi podsítěmi naleznete v [tématu Skupiny zabezpečení sítě](security-overview.md). Azure automaticky směruje provoz mezi podsítěmi, ale můžete přepsat výchozí trasy Azure. Další informace o směrování provozu výchozí podsítě Azures najdete v [tématu Přehled směrování](virtual-networks-udr-overview.md).
       >

     - **Rozsah adres podsítě**: Rozsah musí být v adresním prostoru, který jste zadali pro virtuální síť. Nejmenší rozsah, který můžete zadat, je /29, který poskytuje osm ADRES IP pro podsíť. Azure si vyhrazuje první a poslední adresu v každé podsíti pro shodu protokolu. Tři další adresy jsou vyhrazeny pro využití služby Azure. V důsledku toho virtuální síť s rozsahem adres podsítě /29 má pouze tři použitelné IP adresy. Pokud plánujete připojit virtuální síť k bráně VPN, musíte vytvořit podsíť brány. Přečtěte si další informace o [konkrétních aspektech rozsahu adres pro podsítě brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Rozsah adres můžete změnit po vytvoření podsítě za určitých podmínek. Informace o změně rozsahu adres podsítě naleznete v tématu [Správa podsítí](virtual-network-manage-subnet.md).
     - **Předplatné**: Vyberte [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Stejnou virtuální síť nelze použít ve více než jednom předplatném Azure. Virtuální síť v jednom předplatném však můžete připojit k virtuálním sítím v jiných předplatných s [partnerským vztahem virtuální sítě](virtual-network-peering-overview.md). Všechny prostředky Azure, které se připojíte k virtuální síti, musí být ve stejném předplatném jako virtuální síť.
     - **Skupina prostředků**: Vyberte existující [skupinu prostředků](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) nebo vytvořte novou. Prostředek Azure, který připojíte k virtuální síti, může být ve stejné skupině prostředků jako virtuální síť nebo v jiné skupině prostředků.
     - **Umístění**: Vyberte [umístění](https://azure.microsoft.com/regions/)Azure , označované také jako oblast. Virtuální síť může být v jenom v jednom umístění Azure. Virtuální síť v jednom umístění však můžete připojit k virtuální síti v jiném umístění pomocí brány VPN. Všechny prostředky Azure, které se připojíte k virtuální síti, musí být ve stejném umístění jako virtuální síť.

**Příkazy**

- Azure CLI: [vytvoření síťové honetu az](/cli/azure/network/vnet)
- Prostředí PowerShell: [Nová virtuální síť Az](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Zobrazení virtuálních sítí a nastavení

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě.* Když se **virtuální sítě** zobrazí ve výsledcích hledání, vyberte je.
2. Ze seznamu virtuálních sítí vyberte virtuální síť, pro kterou chcete zobrazit nastavení.
3. Pro vybranou virtuální síť jsou uvedena následující nastavení:
   - **Přehled**: Obsahuje informace o virtuální síti, včetně adresního prostoru a serverů DNS. Následující snímek obrazovky ukazuje nastavení přehledu pro virtuální síť s názvem **MyVNet**:

     ![Přehled síťového rozhraní](./media/manage-virtual-network/vnet-overview.png)

     Virtuální síť můžete přesunout do jiného předplatného nebo skupiny prostředků výběrem **možnosti Změnit** vedle **skupiny prostředků** nebo **názvu předplatného**. Informace o přesunutí virtuální sítě najdete v tématu [Přesunutí prostředků do jiné skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json). V článku jsou uvedeny požadavky a jak přesunout prostředky pomocí portálu Azure, PowerShellu a azure CLI. Všechny prostředky, které jsou připojeny k virtuální síti se musí pohybovat s virtuální sítí.
   - **Adresní prostor**: Jsou uvedeny adresní prostory, které jsou přiřazeny k virtuální síti. Chcete-li se dozvědět, jak přidat a odebrat rozsah adres do adresního prostoru, proveďte kroky v [části Přidání nebo odebrání rozsahu adres](#add-or-remove-an-address-range).
   - **Připojená zařízení**: Všechny prostředky, které jsou připojeny k virtuální síti jsou uvedeny. Na předchozím snímku obrazovky jsou k virtuální síti připojena tři síťová rozhraní a jeden nástroj pro vyrovnávání zatížení. Všechny nové prostředky, které vytvoříte a připojíte k virtuální síti, jsou uvedeny. Pokud odstraníte prostředek, který byl připojen k virtuální síti, už se v seznamu nezobrazí.
   - **Podsítě**: Zobrazí se seznam podsítí, které existují ve virtuální síti. Informace o přidání a odebrání podsítě naleznete v tématu [Správa podsítí](virtual-network-manage-subnet.md).
   - **Servery DNS**: Můžete určit, jestli interní server DNS Azure nebo vlastní server DNS poskytuje překlad názvů pro zařízení připojená k virtuální síti. Když vytvoříte virtuální síť pomocí portálu Azure, servery DNS Azure se ve výchozím nastavení používají pro překlad názvů v rámci virtuální sítě. Chcete-li upravit servery DNS, proveďte kroky v tomto článku [změnit servery DNS.](#change-dns-servers)
   - **Partnerských vztahů**: Pokud existují existující partnerských společností v předplatném, jsou uvedeny zde. Můžete zobrazit nastavení existujících partnerských stran nebo vytvořit, změnit nebo odstranit partnerské partnery. Další informace o partnerských serverech najdete v [tématu Partnerský vztah virtuální sítě](virtual-network-peering-overview.md).
   - **Vlastnosti**: Zobrazí nastavení o virtuální síti, včetně ID prostředku virtuální sítě a předplatného Azure, ve které se nachází.
   - **Diagram**: Diagram poskytuje vizuální reprezentaci všech zařízení, která jsou připojena k virtuální síti. Diagram má některé klíčové informace o zařízeních. Chcete-li spravovat zařízení v tomto zobrazení, vyberte v diagramu zařízení.
   - **Společná nastavení Azure**: Další informace o běžných nastaveních Azure najdete v následujících informacích:
     - [Protokol aktivit](../azure-monitor/platform/platform-logs-overview.md)
     - [Řízení přístupu (IAM)](../role-based-access-control/overview.md)
     - [Značky](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Zámky](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Automatizační skript](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Příkazy**

- Azure CLI: [az síť ová síť show](/cli/azure/network/vnet)
- Prostředí PowerShell: [Virtuální síť Get-Az](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Přidání nebo odebrání rozsahu adres

Můžete přidat a odebrat rozsahy adres pro virtuální síť. Rozsah adres musí být zadán v zápisu CIDR a nemůže se překrývat s jinými rozsahy adres v rámci stejné virtuální sítě. Rozsahy adres, které definujete, mohou být veřejné nebo soukromé (RFC 1918). Ať už definujete rozsah adres jako veřejný nebo soukromý, rozsah adres je dostupný pouze z virtuální sítě, z propojených virtuálních sítí a ze všech místních sítí, které jste připojili k virtuální síti. 

Rozsah adres pro virtuální síť můžete snížit, pokud stále obsahuje rozsahy všech přidružených podsítí. Kromě toho můžete rozšířit rozsah adres, například změna /16 na /8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Nelze přidat následující rozsahy adres:

- 224.0.0.0/4 (Vícesměrové vysílání)
- 255.255.255.255/32 (Vysílání)
- 127.0.0.0/8 (Zpětná smyčka)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (interní sonda dns, DHCP a azure nástroje pro [vyrovnávání zatížení)](../load-balancer/load-balancer-custom-probe-overview.md#probesource)

Přidání nebo odebrání rozsahu adres:

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě.* Když se **virtuální sítě** zobrazí ve výsledcích hledání, vyberte je.
2. Ze seznamu virtuálních sítí vyberte virtuální síť, pro kterou chcete přidat nebo odebrat rozsah adres.
3. V části **NASTAVENÍ**vyberte **možnost Adresní prostor**.
4. Dokončete jednu z následujících možností:
    - **Přidání rozsahu adres**: Zadejte nový rozsah adres. Rozsah adres se nemůže překrývat s existujícím rozsahem adres, který je definován pro virtuální síť.
    - **Odebrání rozsahu adres**: Vpravo od rozsahu adres, který chcete odebrat, vyberte **...** a vyberte **možnost Odebrat**. Pokud podsíť existuje v rozsahu adres, nelze rozsah adres odebrat. Chcete-li odebrat rozsah adres, musíte nejprve odstranit všechny podsítě (a všechny prostředky v podsítích), které existují v rozsahu adres.
5. Vyberte **Uložit**.

**Příkazy**

- Azure CLI: [aktualizace síťové sítě az](/cli/azure/network/vnet)
- Prostředí PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Změna serverů DNS

Všechny virtuální počítače, které jsou připojené k registru virtuální sítě se servery DNS, které zadáte pro virtuální síť. Používají také zadaný server DNS pro překlad názvů. Každé síťové rozhraní (NIC) ve virtuálním počítače může mít vlastní nastavení serveru DNS. Pokud má síťová karta vlastní nastavení serveru DNS, přepíší nastavení serveru DNS pro virtuální síť. Další informace o nastavení dns síťové sítě naleznete v [tématu Úlohy a nastavení síťového rozhraní](virtual-network-network-interface.md#change-dns-servers). Další informace o překladu názvů pro virtuální počítače a instance rolí v Azure Cloud Services najdete v [tématu Překlad názvů pro virtuální počítače a instance rolí](virtual-networks-name-resolution-for-vms-and-role-instances.md). Přidání, změna nebo odebrání serveru DNS:

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě.* Když se **virtuální sítě** zobrazí ve výsledcích hledání, vyberte je.
2. Ze seznamu virtuálních sítí vyberte virtuální síť, pro kterou chcete změnit servery DNS.
3. V části **NASTAVENÍ**vyberte **servery DNS**.
4. Vyberte jednu z následujících možností:
   - **Výchozí (za předpokladu, Že Azure):** Všechny názvy prostředků a privátní IP adresy se automaticky zaevidují na serverech Azure DNS. Můžete přeložit názvy mezi všechny prostředky, které jsou připojeny ke stejné virtuální síti. Tuto možnost nelze použít k překladu názvů ve virtuálních sítích. Chcete-li přeložit názvy ve virtuálních sítích, musíte použít vlastní server DNS.
   - **Vlastní**: Můžete přidat jeden nebo více serverů až do limitu Azure pro virtuální síť. Další informace o limitech serverů DNS najdete v [tématu Limity Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Máte následující možnosti:
   - **Přidání adresy**: Přidá server do seznamu serverů DNS virtuální sítě. Tato možnost také zaregistruje DNS server s Azure. Pokud jste už v Azure zaregistrovali DNS server, můžete tento server DNS vybrat v seznamu.
   - **Odebrat adresu**: Vedle serveru, který chcete odebrat, vyberte **...**, a pak **odebrat**. Odstraněním serveru odeberete server pouze z tohoto seznamu virtuálních sítí. DNS server zůstane registrovaný v Azure, aby se mohly používat vaše další virtuální sítě.
   - **Přiobjednání adres serverů DNS**: Je důležité ověřit, zda jsou servery DNS uvedeny ve správném pořadí pro vaše prostředí. Seznamy serverů DNS se používají v uvedeném pořadí. Nefungují jako kruhové nastavení. Pokud je možné dosáhnout prvního serveru DNS v seznamu, klient tento server DNS použije bez ohledu na to, zda server DNS pracuje správně. Odeberte všechny uvedené servery DNS a přidejte je zpět v požadovaném pořadí.
   - **Změna adresy**: Zvýrazněte server DNS v seznamu a zadejte novou adresu.
5. Vyberte **Uložit**.
6. Restartujte virtuální počítače, které jsou připojené k virtuální síti, aby jim bylo přiřazeno nové nastavení serveru DNS. Virtuální počítače nadále používat své aktuální nastavení DNS, dokud nejsou restartovány.

**Příkazy**

- Azure CLI: [aktualizace síťové sítě az](/cli/azure/network/vnet)
- Prostředí PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Odstranění virtuální sítě

Virtuální síť můžete odstranit pouze v případě, že k ní nejsou připojeny žádné prostředky. Pokud jsou prostředky připojené k libovolné podsíti v rámci virtuální sítě, musíte nejprve odstranit prostředky, které jsou připojeny ke všem podsítím v rámci virtuální sítě. Kroky k odstranění prostředku se liší v závislosti na prostředku. Chcete-li se dozvědět, jak odstranit prostředky připojené k podsítím, přečtěte si dokumentaci ke každému typu prostředku, který chcete odstranit. Odstranění virtuální sítě:

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě.* Když se **virtuální sítě** zobrazí ve výsledcích hledání, vyberte je.
2. Ze seznamu virtuálních sítí vyberte virtuální síť, kterou chcete odstranit.
3. Výběrem možnosti **Připojená zařízení**v části **NASTAVENÍ**zkontrolujte, zda k virtuální síti nejsou připojena žádná zařízení . Pokud jsou připojená zařízení, musíte je před odstraněním virtuální sítě odstranit. Pokud nejsou připojena žádná připojená zařízení, vyberte **Přehled**.
4. Vyberte **Odstranit**.
5. Chcete-li potvrdit odstranění virtuální sítě, vyberte **ano**.

**Příkazy**

- Azure CLI: [odstranění virtuální sítě Azure](/cli/azure/network/vnet)
- Prostředí PowerShell: [Odebrat virtuální síť Az](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Oprávnění

Chcete-li provádět úkoly ve virtuálních sítích, musí být váš účet přiřazen k roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, které jsou přiřazeny příslušné akce uvedené v následující tabulce:

| Akce                                  |   Name (Název)                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Čtení virtuální sítě              |
|Microsoft.Network/virtualNetworks/write  |   Vytvoření nebo aktualizace virtuální sítě  |
|Microsoft.Network/virtualNetworks/delete |   Odstranění virtuální sítě            |

## <a name="next-steps"></a>Další kroky

- Vytvoření virtuální sítě pomocí ukázkových skriptů [PowerShellu](powershell-samples.md) nebo [Azure CLI](cli-samples.md) nebo pomocí šablon Azure [Resource Manageru](template-samples.md)
- Vytvoření a použití [zásad Azure](policy-samples.md) pro virtuální sítě
