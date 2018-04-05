---
title: Vytvoření, změnit nebo odstranit virtuální síť Azure | Microsoft Docs
description: Zjistěte, jak vytvářet, měnit nebo odstranit virtuální sítě v Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: ac0b15f120071093fd81de1d83cf2067ecbac269
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-change-or-delete-a-virtual-network"></a>Vytvoření, změnit nebo odstranit virtuální síť

Naučte se vytvářet a odstranit virtuální síť a změnit nastavení, například servery DNS a prostory IP adres pro existující virtuální síť.

Virtuální síť je reprezentace vlastní sítě v cloudu. Virtuální síť je to logická izolace cloudu Azure, který je vyhrazen pro vaše předplatné Azure. Pro každou virtuální síť, kterou vytvoříte můžete:
- Zvolte adresní prostor přiřadit. Adresní prostor se skládá z jedné nebo více rozsahů adres, které jsou definovány pomocí notace CIDR (Classless Inter-Domain směrování) zápisu, jako je 10.0.0.0/16.
- Zvolte pomocí serveru DNS poskytnutých platformou Azure nebo pomocí serveru DNS. Všechny prostředky, které jsou připojené k virtuální síti jsou přiřazené tento server DNS překládat názvy v rámci virtuální sítě.
- Virtuální síť segmentovat do podsítí, každou s vlastní rozsah adres, v rámci adresního prostoru virtuální sítě. Naučte se vytvářet, měnit a odstraňovat podsítě, najdete v tématu [přidat, změnit nebo odstranit podsítě](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v žádné části tohoto článku dokončete následující úlohy:

- Pokud nemáte účet Azure, si zaregistrovat [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí účtu Azure.
- Pokud pomocí příkazů prostředí PowerShell k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.2.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.26 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení s Azure.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Vyberte **+ vytvořit prostředek** > **sítě** > **virtuální síť**.
2. Zadejte nebo vyberte hodnoty pro následující nastavení a potom vyberte **vytvořit**:
    - **Název**: název musí být jedinečný v [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) vyberte možnost pro vytvoření virtuální sítě. Název nelze změnit po vytvoření virtuální sítě. Můžete vytvořit několik virtuálních sítí v čase. Pojmenování návrhy, najdete v části [konvence vytváření názvů](https://docs.microsoft.com/en-us/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Tyto zásady vytváření názvů vám pomohou lépe spravovat více virtuálních sítí.
    - **Adresní prostor**: adresní prostor virtuální sítě se skládá z jedné nebo více nepřekrývají rozsahy adres, které jsou určené v notaci CIDR. Rozsah adres, které definujete může být veřejné nebo privátní (RFC 1918). Zda je jako veřejné nebo soukromé definovat rozsah adres, rozsah adres je dostupný jenom z v rámci virtuální sítě od vzájemně propojena virtuální sítě a od všech sítích na pracovišti, které jste se připojili k virtuální síti. Nelze přidat následující rozsahy adres:
        - 224.0.0.0/4 (vícesměrové vysílání)
        - 255.255.255.255/32 (vysílání)
        - 127.0.0.0/8 (zpětné smyčky)
        - 169.254.0.0/16 (Link-local)
        - 168.63.129.16/32 (Internal DNS)

      I když při vytváření virtuální sítě můžete definovat pouze jednu adresu rozsahu, můžete přidat další rozsahy adres do adresního prostoru po vytvoření virtuální sítě. Informace o postupu přidání rozsah adres k existující virtuální síti, najdete v tématu [přidat nebo odebrat rozsah adres](#add-or-remove-an-address-range).

      >[!WARNING]
      >Pokud virtuální sítě má rozsahy adres, které se překrývají s jinou virtuální sítí nebo místní sítě, nemůže být připojen dvě sítě. Než můžete definovat rozsah adres, zvažte, jestli můžete chtít v budoucnu připojení virtuální sítě k jiným virtuálním sítím nebo místní sítě.
      >
      >

    - **Název podsítě**: název podsítě musí být jedinečný v rámci virtuální sítě. Název podsítě nelze změnit po vytvoření podsítě. Na portálu vyžaduje definování jednu podsíť když vytvoříte virtuální síť, i když není vyžadováno žádné podsítě virtuální sítě. Na portálu můžete definovat pouze jednu podsíť při vytváření virtuální sítě. Můžete přidat další podsítě virtuální sítě později, po vytvoření virtuální sítě. Chcete-li přidat podsíť k virtuální síti, najdete v části [spravovat podsítě](virtual-network-manage-subnet.md). Můžete vytvořit virtuální síť, která má několik podsítí pomocí příkazového řádku Azure CLI nebo prostředí PowerShell.

      >[!TIP]
      >V některých případech správci vytvářet různých podsítích filtrování nebo řídit směrování provozu mezi podsítěmi. Před definováním podsítě, zvažte, jak můžete filtrovat a směrovat provoz mezi podsítě. Další informace o filtrování přenosů mezi podsítěmi, najdete v části [skupin zabezpečení sítě](security-overview.md). Azure automaticky směrování provozu mezi podsítěmi, ale můžete přepsat Azure výchozí trasy. Další informace o Azures výchozí podsítě ke směrování provozu najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
      >

    - **Rozsah adres podsítě**: rozsahu musí být v rámci adresního prostoru, který jste zadali pro virtuální síť. Nejmenší oblast, kterou můžete zadat je /29, který poskytuje osm IP adresy podsítě. Azure si vyhrazuje adresu první a poslední v každé podsíti pro protokol shoda. Tři další adresy jsou vyhrazené pro použití služby Azure. V důsledku toho virtuální síť s rozsahem adres podsítě /29 má jenom tři použitelných IP adresách. Pokud máte v plánu pro připojení virtuální sítě k bráně VPN, musíte vytvořit podsíť brány. Další informace o [aspekty rozsah konkrétní adresu podsítě brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Rozsah adres můžete změnit po vytvoření podsítě pro určité podmínky. Zjistěte, jak změnit rozsah adres podsítě, najdete v tématu [spravovat podsítě](virtual-network-manage-subnet.md).
    - **Předplatné**: vyberte [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). V více než jedno předplatné, nelze použít stejné virtuální síti. Však můžete připojit virtuální síť v rámci jednoho předplatného k virtuálním sítím v jiných předplatných s [partnerský vztah virtuální sítě](virtual-network-peering-overview.md). Jakýmikoli prostředky Azure, který je připojen k virtuální síti musí být ve stejném předplatném jako virtuální síť.
    - **Skupina prostředků**: Vyberte existující [skupiny prostředků](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) nebo vytvořte novou. Prostředek služby Azure, který je připojen k virtuální síti může být ve stejné skupině prostředků jako virtuální síť nebo v jiné skupině prostředků.
    - **Umístění**: Vyberte Azure [umístění](https://azure.microsoft.com/regions/), označované také jako oblast. Virtuální síť může být pouze v jednom umístění Azure. Je však připojit virtuální síť na jednom místě k virtuální síti v jiném umístění s použitím brány VPN. Jakýmikoli prostředky Azure, který je připojen k virtuální síti musí být ve stejném umístění jako virtuální síť.

**Příkazy**

- Azure CLI: [vytvořit az sítě vnet](/cli/azure/network/vnet)
- PowerShell: [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Zobrazit virtuální sítě a nastavení

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** se zobrazí ve výsledcích hledání, vyberte ho.
2. Ze seznamu virtuálních sítí vyberte virtuální síť, kterou chcete zobrazit nastavení.
3. Pro virtuální síť, kterou jste vybrali jsou uvedeny následující nastavení:
    - **Přehled**: poskytuje informace o virtuální síť, včetně adresní prostor a servery DNS. Následující snímek obrazovky ukazuje přehled nastavení pro virtuální síť s názvem **MyVNet**:

        ![Přehled rozhraní sítě](./media/manage-virtual-network/vnet-overview.png)

      Virtuální síť můžete přesunout do jiné předplatné nebo prostředek skupiny výběrem **změnu** vedle **skupiny prostředků** nebo **název odběru**. Další postup přesunutí virtuální sítě najdete v tématu [přesunu prostředků do jiné skupině prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Článek uvádí předpoklady a postup přesunutí prostředků pomocí portálu Azure, PowerShell a rozhraní příkazového řádku Azure. Všechny prostředky, které jsou připojené k virtuální síti, musíte přesunout s virtuální sítí.
    - **Adresní prostor**: jsou uvedeny adresní prostory, které jsou přiřazeny k virtuální síti. Chcete-li se dozvědět, jak přidávat a odebírat rozsah adres do adresního prostoru, proveďte kroky v [přidat nebo odebrat rozsah adres](#add-or-remove-an-address-range).
    - **Připojená zařízení**: jsou uvedeny všechny prostředky, které jsou připojené k virtuální síti. Na předchozím snímku obrazovky tři síťová rozhraní a jedna služba Vyrovnávání zatížení jsou připojené k virtuální síti. Jsou uvedeny všechny nové prostředky, které můžete vytvořit a připojit k virtuální síti. Pokud odstraníte prostředek, který byl připojen k virtuální síti, se již v seznamu.
    - **Podsítě**: se zobrazí seznam podsítí, které existují v rámci virtuální sítě. Naučte se přidávat a odebírat podsíť, najdete v tématu [spravovat podsítě](virtual-network-manage-subnet.md).
    - **Servery DNS**: můžete určit, zda Azure interní server DNS nebo vlastního serveru DNS poskytuje překlad názvů pro zařízení, které jsou připojené k virtuální síti. Když vytvoříte virtuální síť pomocí portálu Azure, serverů Azure DNS se používají pro překlad názvu virtuální sítě, ve výchozím nastavení. Pokud chcete upravit servery DNS, proveďte kroky v [servery DNS změnu](#change-dns-servers) v tomto článku.
    - **Partnerské vztahy**: Pokud v odběru existují existující partnerských vztahů, jsou zde uvedeny. Můžete zobrazit nastavení pro existující partnerských vztahů, nebo vytvořit, změnit nebo odstranění partnerských vztahů. Další informace o partnerských vztahů, najdete v části [partnerský vztah virtuální sítě](virtual-network-peering-overview.md).
    - **Vlastnosti**: nastavení o virtuální síť, včetně ID prostředku virtuální sítě a předplatné Azure, které se zobrazí.
    - **Diagram**: diagram poskytuje vizuální reprezentace všechna zařízení, které jsou připojené k virtuální síti. Diagram má některé klíčové informace o zařízení. Ke správě zařízení v tomto zobrazení v diagramu, vyberte zařízení.
    - **Obecná nastavení Azure**: Další informace o běžných nastavení Azure, přečtěte si následující informace:
        *   [Protokol aktivit](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Řízení přístupu (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Značky](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Zámky.](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Skriptu pro automatizaci](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Příkazy**

- Azure CLI: [az sítě vnet zobrazit](/cli/azure/network/vnet#az_network_vnet_show)
- PowerShell: [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Přidat nebo odebrat rozsah adres

Můžete přidávat a odebírat rozsahy adres pro virtuální síť. Rozsah adres je třeba zadat v notaci CIDR a nesmí překrývat s další rozsahy adres v rámci stejné virtuální síti. Rozsahy adres, které definujete může být veřejné nebo privátní (RFC 1918). Zda je jako veřejné nebo soukromé definovat rozsah adres, rozsah adres je dostupný jenom z v rámci virtuální sítě od vzájemně propojena virtuální sítě a od všech sítích na pracovišti, které jste se připojili k virtuální síti. Nelze přidat následující rozsahy adres:

- 224.0.0.0/4 (vícesměrové vysílání)
- 255.255.255.255/32 (vysílání)
- 127.0.0.0/8 (zpětné smyčky)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (Internal DNS)

Přidat nebo odebrat rozsah adres:

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** se zobrazí ve výsledcích hledání, vyberte ho.
2. Ze seznamu virtuálních sítí vyberte virtuální síť, pro který chcete přidat nebo odebrat rozsah adres.
3. Vyberte **adresní prostor**v části **nastavení**.
4. Proveďte jednu z následujících možností:
    - **Přidat rozsah adres**: Zadejte nový rozsah adres. Rozsah adres se nesmí překrývat s existující rozsah adres, který je definován pro virtuální síť.
    - **Odebrat rozsah adres**: na pravé straně rozsah adres, které chcete odebrat, vyberte **...** , pak vyberte **odebrat**. Pokud existuje podsíť v rozsahu adres, nelze odebrat rozsah adres. Odebrat rozsah adres, je nutné nejprve odstranit všechny podsítě (a všechny prostředky ve podsítích), neexistuje v rozsahu adres.
5. Vyberte **Uložit**.

**Příkazy**

- Azure CLI: [aktualizace az sítě vnet](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="change-dns-servers"></a>Změnit servery DNS

Všechny virtuální počítače, které jsou připojené k virtuální síti registrace se servery DNS, které zadáte pro virtuální síť. Také používají zadaný server DNS pro rozlišení názvu. Každé síťové rozhraní (NIC) ve virtuálním počítači může mít svůj vlastní nastavení serveru DNS. Pokud síťový adaptér má svou vlastní nastavení serveru DNS, budou přepsat nastavení serveru DNS virtuální sítě. Další informace o nastavení DNS pro síťový adaptér, najdete v části [síťové rozhraní úlohy a nastavení](virtual-network-network-interface.md#change-dns-servers). Další informace o překladu názvů pro virtuální počítače a instance rolí ve službě Azure Cloud Services najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](virtual-networks-name-resolution-for-vms-and-role-instances.md). Pokud chcete přidat, změnit nebo odebrat DNS server:

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** se zobrazí ve výsledcích hledání, vyberte ho.
2. Ze seznamu virtuálních sítí vyberte virtuální síť, pro kterou chcete změnit serverů DNS.
3.  Vyberte **servery DNS**v části **nastavení**.
4. Vyberte jednu z následujících možností:
    - **Výchozí (zadaný Azure)**: všechny názvy prostředků a privátních IP adres se automaticky registruje na servery Azure DNS. Abyste mohli vyřešit názvy mezi všechny prostředky, které jsou připojené ke stejné virtuální síti. Tuto možnost nelze použít k překladu názvů virtuálních sítí. Překládat názvy virtuálních sítí, musíte použít vlastní server DNS.
    - **Vlastní**: můžete přidat jeden nebo více serverů, a to až do limitu Azure pro virtuální síť. Další informace o limity serveru DNS, najdete v části [Azure omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Máte následující možnosti:
        - **Přidat adresu**: server přidá do seznamu serverů DNS virtuální sítě. Tato možnost také zaregistruje DNS server s Azure. Pokud již jste registrováni DNS server s Azure, můžete vybrat tento server DNS v seznamu.
        - **Odebrat adresu**: u serveru, který chcete odebrat, vyberte **...** , pak **odebrat**. Odstranění serveru odstraní server pouze z tohoto seznamu virtuální sítě. DNS server zůstane registrované v Azure pro vaše virtuální sítě a použít.
        - **Změna pořadí adresy serverů DNS**: je důležité si ověřit, že můžete seznam serverů DNS ve správném pořadí pro vaše prostředí. Seznamy server DNS se používají v pořadí, ve kterém jsou uvedené. Nepracují jako instalace s kruhovým dotazováním. Pokud první server DNS v seznamu dostupný, klient použije tento server DNS, bez ohledu na to, zda je DNS server správně funguje. Odeberte všechny servery DNS, které jsou uvedeny a poté je přidejte zpátky v pořadí, ve kterém chcete.
        - **Změnit adresu**: zvýrazněte server DNS v seznamu a pak zadejte novou adresu.
5. Vyberte **Uložit**.
6. Restartujte virtuální počítače, které jsou připojené k virtuální síti, aby byly přiřazovány nové nastavení serveru DNS. Virtuální počítače dál používat své aktuální nastavení DNS, dokud nebudou restartovány.

**Příkazy**

- Azure CLI: [aktualizace az sítě vnet](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Odstranění virtuální sítě

Virtuální síť můžete odstranit pouze v případě, že neexistují žádné prostředky k němu připojená. Pokud jsou prostředky připojenými k žádné podsíti v rámci virtuální sítě, musíte nejprve odstranit prostředky, které jsou připojené k všech podsítí v rámci virtuální sítě. Kroky, které je třeba provést odstranění prostředku se liší v závislosti na prostředek. Chcete-li zjistit, jak odstranit prostředky, které jsou připojené k podsítím, přečtěte si dokumentaci pro každý typ prostředku, který chcete odstranit. Odstranění virtuální sítě:

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** se zobrazí ve výsledcích hledání, vyberte ho.
2. Seznam virtuálních sítí vyberte virtuální síť, kterou chcete odstranit.
3. Potvrďte, že neexistují žádné zařízení připojená k virtuální síti výběrem **připojená zařízení**v části **nastavení**. Připojená zařízení je nutné před odstraněním virtuální sítě odstranit. Pokud nejsou připojené zařízení, vyberte **přehled**.
4. Vyberte **Odstranit**.
5. Potvrďte odstranění virtuální sítě, vyberte **Ano**.

**Příkazy**

- Azure CLI: [odstranit virtuální síť azure sítě](/cli/azure/network/vnet#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork)

## <a name="permissions"></a>Oprávnění

K provádění úloh na virtuální sítě, musí mít váš účet přiřazenou k [Přispěvatel sítě](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena odpovídající oprávnění uvedených v následující tabulce:

|Operace                                    |   Název operace                    |
|-------------------------------------------  |   --------------------------------  |
|Microsoft.Network/virtualNetworks/read       |   Načíst virtuální síť               |
|Microsoft.Network/virtualNetworks/write      |   Vytvořit nebo aktualizovat virtuální sítě  |
|Microsoft.Network/virtualNetworks/delete     |   Odstranit virtuální síť            |

## <a name="next-steps"></a>Další postup

- Vytvoření virtuálního počítače a připojte ho k virtuální síti najdete v tématu [vytvoření virtuální sítě a připojení virtuálních počítačů](quick-create-portal.md#create-virtual-machines).
- Pro filtrování síťového provozu mezi podsítěmi v rámci virtuální sítě, najdete v části [vytvoření skupin zabezpečení sítě](virtual-networks-create-nsg-arm-pportal.md).
- Chcete-li peer virtuální sítě s jinou virtuální sítí, přečtěte si téma [vytvoření virtuální sítě partnerského vztahu](tutorial-connect-virtual-networks-portal.md).
- Další informace o možnosti připojení virtuální sítě k místní síti najdete v tématu [o službě VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
