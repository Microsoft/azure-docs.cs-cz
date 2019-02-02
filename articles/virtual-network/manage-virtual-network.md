---
title: Vytvoření, změna nebo odstranění služby Azure virtual network
titlesuffix: Azure Virtual Network
description: Zjistěte, jak vytvořit, změnit nebo odstranit virtuální síť v Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: jdial
ms.openlocfilehash: 10717ccbfa05f204e60eb6d8cde5a83e9714b197
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562602"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Vytvoření, změna nebo odstranění virtuální sítě

Zjistěte, jak vytvořit a odstranit virtuální síť a změnit nastavení, jako jsou servery DNS a prostory IP adres pro existující virtuální sítě. Pokud začínáte s virtuálními sítěmi, další informace o nich v [Přehled virtuálních sítí](virtual-networks-overview.md) nebo provedením [kurzu](quick-create-portal.md). Virtuální síť obsahuje podsítě. Zjistěte, jak vytvářet, měnit a odstraňovat podsítě, najdete v článku [Správa podsítí](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Před zahájením

Před dokončením kroků v jakékoli části tohoto článku, proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud používáte příkazy prostředí PowerShell k dokončení úkolů v tomto článku, buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje použití Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.
- Účet přihlásit nebo připojit k Azure, musíte být přiřazeni k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Vyberte **+ vytvořit prostředek** > **sítě** > **virtuální síť**.
2. Zadejte nebo vyberte hodnoty pro následující nastavení a pak vyberte **vytvořit**:
    - **Název**: Název musí být jedinečný v [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) , že jste vybrali pro vytvoření virtuální sítě. Název nelze změnit po vytvoření virtuální sítě. Můžete vytvořit několik virtuálních sítí v čase. Návrhy pojmenování, naleznete v tématu [zásady vytváření názvů](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Tyto zásady vytváření názvů vám pomohou lépe spravovat více virtuálních sítí.
    - **Adresní prostor**: Adresní prostor pro virtuální síť se skládá z jednoho nebo více překrývat rozsahy adres, které jsou určené v zápisu CIDR. Rozsah adres, které definujete, může být veřejné a privátní (RFC 1918). Zda definovat rozsah adres jako veřejný nebo privátní, rozsah adres je dostupný pouze z v rámci virtuální sítě z propojených virtuálních sítí a ze všech místních sítí připojené k virtuální síti. Nelze přidat následující rozsahy adres:
        - 224.0.0.0/4 (vícesměrové vysílání)
        - 255.255.255.255/32 (vysílání)
        - 127.0.0.0/8 (adresu zpětné smyčky)
        - 169.254.0.0/16 (Link-local)
        - 168.63.129.16/32 (interního serveru DNS, DHCP a nástroje pro vyrovnávání zatížení Azure [sondu stavu](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

      Přestože můžete definovat pouze jednu adresu rozsahu, při vytváření virtuální sítě, můžete přidat více rozsahů adres do adresního prostoru po vytvoření virtuální sítě. Zjistěte, jak přidat rozsah adres do existující virtuální sítě, najdete v článku [přidat nebo odebrat rozsah adres](#add-or-remove-an-address-range).

      >[!WARNING]
      >Pokud virtuální síť má rozsahy adres, které se překrývají s jinou virtuální sítí nebo sítí, nemůže být připojen dvě sítě. Předtím, než je definovat rozsah adres, zvažte, jestli můžete chtít v budoucnu připojení virtuální sítě do jiných virtuálních sítí nebo místních sítí.
      >
      >

    - **Název podsítě**: Název podsítě musí být v rámci virtuální sítě jedinečný. Název podsítě nelze změnit po vytvoření podsítě. Na portálu vyžaduje definovat jednu podsíť při vytváření virtuální sítě, i když není virtuální síť musí mít žádné podsítě. Na portálu můžete definovat jenom jednu podsíť, při vytváření virtuální sítě. Můžete přidat další podsítě do virtuální sítě později, po vytvoření virtuální sítě. Přidání podsítě do virtuální sítě, najdete v článku [Správa podsítí](virtual-network-manage-subnet.md). Můžete vytvořit virtuální síť, která má více podsítí pomocí Azure CLI nebo Powershellu.

      >[!TIP]
      >V některých případech správci vytvářet různé podsítě a filtrovat řídit směrování provozu mezi podsítěmi. Před definováním podsítí, zvažte, jak můžete filtrovat a směrovat provoz mezi podsítěmi. Další informace o filtrování provozu mezi podsítěmi, najdete v článku [skupiny zabezpečení sítě](security-overview.md). Azure automaticky směruje provoz mezi podsítěmi, ale můžete přepsat Azure výchozí trasy. Další informace o směrování Azures výchozí podsíť provozu najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
      >

    - **Rozsah adres podsítě**: Rozsah musí být v rámci adresního prostoru, které jste zadali pro virtuální síť. Nejmenší oblast, kterou můžete určit je minimální velikostí/29, který nabízí osm IP adres podsítě. Azure si vyhrazuje první a poslední adresa v každé podsíti pro udržování souladu s protokoly. Tři další adresy jsou vyhrazené pro použití služby Azure. V důsledku toho virtuální síť s rozsahem adres podsíť/29 má pouze tři použitelných IP adresách. Pokud máte v plánu připojit virtuální sítě k bráně VPN, musíte vytvořit podsíť brány. Další informace o [aspekty konkrétní adresu rozsahu podsítí brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Rozsah adres můžete změnit po vytvoření podsítě za určitých podmínek. Zjistěte, jak změnit rozsah adres podsítě, najdete v článku [Správa podsítí](virtual-network-manage-subnet.md).
    - **Předplatné**: Vyberte [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Stejné virtuální síti nelze použít ve více než jedno předplatné Azure. Ale můžete propojit virtuální sítě v rámci jednoho předplatného k virtuálním sítím v jiných předplatných s [partnerský vztah virtuální sítě](virtual-network-peering-overview.md). Prostředek Azure, který jste připojení k virtuální síti musí být ve stejném předplatném jako virtuální síť.
    - **Skupina prostředků**: Vyberte existující [skupiny prostředků](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) nebo vytvořte novou. Prostředek Azure, který jste připojení k virtuální síti může být ve stejné skupině prostředků jako virtuální síť nebo v jiné skupině prostředků.
    - **Umístění**: Vyberte Azure [umístění](https://azure.microsoft.com/regions/), označovaný také jako oblast. Virtuální síť může být pouze v jednom umístění Azure. Můžete však připojení virtuální sítě v jednom umístění k virtuální síti v jiném umístění s použitím brány VPN. Prostředek Azure, který jste připojení k virtuální síti musí být ve stejném umístění jako virtuální síť.

**Příkazy**

- Azure CLI: [az network vnet vytvořit](/cli/azure/network/vnet)
- PowerShell: [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Zobrazit virtuální sítě a nastavení

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Seznam virtuálních sítí vyberte virtuální síť, kterou chcete zobrazit nastavení.
3. Pro virtuální síť, kterou jste vybrali, jsou uvedeny následující nastavení:
    - **Přehled**: Poskytuje informace o službě virtual network, včetně adresní prostor a servery DNS. Následující snímek obrazovky ukazuje přehled nastavení pro virtuální síť s názvem **MyVNet**:

        ![Přehled rozhraní sítě](./media/manage-virtual-network/vnet-overview.png)

      Virtuální síť můžete přesunout do jiné předplatné nebo prostředek skupiny tak, že vyberete **změnu** vedle **skupiny prostředků** nebo **název předplatného**. Zjistěte, jak přesunout virtuální sítě, najdete v článku [prostředky přesunout do jiné skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek uvádí předpoklady a postup přesunutí prostředků pomocí webu Azure portal, Powershellu a Azure CLI. Všechny prostředky, které jsou připojené k virtuální síti nutné přesunout s virtuální sítí.
    - **Adresní prostor**: Adresní prostory, které jsou přiřazeny k virtuální síti jsou uvedeny. Informace o přidávání a odebírání rozsah adres do adresního prostoru, proveďte kroky v [přidat nebo odebrat rozsah adres](#add-or-remove-an-address-range).
    - **Připojená zařízení**: Jsou uvedeny všechny prostředky, které jsou připojené k virtuální síti. Na předchozím snímku obrazovky tři síťová rozhraní a jedna služba Vyrovnávání zatížení jsou připojené k virtuální síti. Jsou uvedeny všechny nové prostředky, které můžete vytvořit a připojit k virtuální síti. Při odstranění prostředku, která byla připojená k virtuální síti, už se zobrazí v seznamu.
    - **Podsítě**: Seznam podsítí, které existují v rámci virtuální sítě se zobrazí. Zjistěte, jak přidat a odebrat podsíť, najdete v článku [Správa podsítí](virtual-network-manage-subnet.md).
    - **Servery DNS**: Můžete určit, zda Azure interní server DNS nebo vlastního serveru DNS nabízí řešení názvů pro zařízení, které jsou připojené k virtuální síti. Když vytvoříte virtuální síť pomocí webu Azure portal, servery DNS Azure se používají pro překlad v rámci virtuální sítě, ve výchozím nastavení. Chcete-li upravit servery DNS, proveďte kroky v [servery DNS změnu](#change-dns-servers) v tomto článku.
    - **Partnerské vztahy**: Pokud v předplatném existující partnerské vztahy, jsou zde uvedeny. Můžete zobrazit nastavení pro existující partnerských vztahů, nebo vytvořit, změnit nebo odstranit partnerské vztahy. Další informace o partnerských vztahů, naleznete v tématu [partnerský vztah virtuálních sítí](virtual-network-peering-overview.md).
    - **Vlastnosti**: O službě virtual network, včetně ID prostředku virtuální sítě a předplatné Azure, které se zobrazí nastavení.
    - **Diagram**: Diagram nabízí vizuální znázornění všech zařízení, které jsou připojené k virtuální síti. Diagram má některé klíčové informace o zařízení. Ke správě zařízení v tomto zobrazení v diagramu, vyberte zařízení.
    - **Obecná nastavení Azure**: Další informace o běžných nastavení služby Azure, najdete v následující informace:
        *   [Protokol aktivit](../azure-monitor/platform/activity-logs-overview.md)
        *   [Řízení přístupu (IAM)](../role-based-access-control/overview.md)
        *   [Značky](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Zámky](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Automatizační skript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Příkazy**

- Azure CLI: [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show)
- PowerShell: [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Přidání nebo odebrání rozsah adres

Můžete přidávat a odebírat rozsahy adres pro virtuální síť. Rozsah adres musí zadat v notaci CIDR a nemůže překrývat s rozsahy jiných adres v rámci stejné virtuální síti. Rozsahy adres, které definujete, může být veřejné a privátní (RFC 1918). Zda definovat rozsah adres jako veřejný nebo privátní, rozsah adres je dostupný pouze z v rámci virtuální sítě z propojených virtuálních sítí a ze všech místních sítí připojené k virtuální síti. 

Rozsah adres pro virtuální síť můžete zmenšit, pokud nemáte k dispozici žádné podsítě s ním spojená. V opačném případě můžete pouze rozšířit rozsah adres, například změna /16 k /8. Může začínat malé adresu rozsahu a potom ji dále rozšířit nebo přidat další oblasti.

<!-- the last two sentences above are added per GitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Nelze přidat následující rozsahy adres:

- 224.0.0.0/4 (vícesměrové vysílání)
- 255.255.255.255/32 (vysílání)
- 127.0.0.0/8 (adresu zpětné smyčky)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (interního serveru DNS, DHCP a nástroje pro vyrovnávání zatížení Azure [sondu stavu](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

Přidání nebo odebrání rozsah adres:

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Seznam virtuálních sítí vyberte virtuální síť, pro který chcete přidat nebo odebrat rozsah adres.
3. Vyberte **adresní prostor**v části **nastavení**.
4. Proveďte jeden z následujících možností:
    - **Přidat rozsah adres**: Zadejte nový rozsah adres. Rozsah adres se nesmí překrývat s existující rozsah adres, který je definován pro virtuální síť.
    - **Odebere rozsah adres**: Na pravé straně rozsah adres, které chcete odebrat, vyberte **...** a pak vyberte **odebrat**. Pokud podsíť existuje v rozsahu adres, nelze odebrat rozsah adres. Odebrat rozsah adres, musíte nejprve odstranit žádné podsítě (a všechny prostředky v podsítě), která existují v rozsahu adres.
5. Vyberte **Uložit**.

**Příkazy**

- Azure CLI: [az network vnet aktualizace](/cli/azure/network/vnet)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="change-dns-servers"></a>Změnit servery DNS

Všechny virtuální počítače, které jsou připojeny k registru virtuální sítě se servery DNS, které zadáte pro virtuální síť. Také používají zadaný server DNS pro překlad názvů. Každé síťové rozhraní (NIC) ve virtuálním počítači můžete mít svůj vlastní nastavení serveru DNS. Pokud síťové rozhraní má svůj vlastní nastavení serveru DNS, jejich přepsat nastavení serveru DNS pro virtuální síť. Další informace o nastavení DNS pro síťové rozhraní, naleznete v tématu [síťová rozhraní úlohách a nastaveních](virtual-network-network-interface.md#change-dns-servers). Další informace o překlad názvů pro virtuální počítače a instance rolí v cloudových službách Azure najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](virtual-networks-name-resolution-for-vms-and-role-instances.md). Pokud chcete přidat, změnit nebo odebrat DNS server:

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Seznam virtuálních sítí vyberte virtuální síť, pro kterou chcete změnit serverů DNS.
3.  Vyberte **servery DNS**v části **nastavení**.
4. Vyberte jednu z následujících možností:
    - **Výchozí (poskytováno Azure)**: Všechny názvy prostředků a privátních IP adres jsou automaticky registrované servery Azure DNS. Lze přeložit názvy mezi všechny prostředky, které jsou připojené ke stejné virtuální síti. Tuto možnost nelze použít k překladu názvů mezi virtuálními sítěmi. K překladu názvů mezi virtuálními sítěmi, musíte použít vlastní server DNS.
    - **Vlastní**: Můžete přidat jeden nebo více serverů, až do limitu Azure pro virtuální síť. Další informace o omezeních server DNS, najdete v článku [omezení Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Máte následující možnosti:
        - **Přidat adresu**: Přidá server do seznamu serverů DNS virtuální sítě. Tato možnost také zaregistruje DNS server s využitím Azure. Pokud už zaregistrovaný DNS server s využitím Azure, můžete vybrat tento server DNS v seznamu.
        - **Odebrat adresu**: U serveru, který chcete odebrat, vyberte **...** , pak **odebrat**. Odstraněním serveru dojde k odebrání serveru pouze z tohoto seznamu virtuální sítě. DNS server zůstávají registrované v Azure pro vaše virtuální sítě a použít.
        - **Změna pořadí adresy serverů DNS**: Je důležité ověřit, že můžete seznam serverů DNS ve správném pořadí pro vaše prostředí. Seznamy serveru DNS se používají v pořadí, v jakém jsou uvedeny. Nelze je použít jako instalace kruhové dotazování. Pokud byly v seznamu první server DNS, klient použije tento server DNS, bez ohledu na to, zda je DNS server funguje správně. Odeberte všechny servery DNS, které jsou uvedeny a potom je přidat zpět v pořadí, ve kterém chcete.
        - **Změnit adresu**: Server DNS v seznamu zvýrazněte a pak zadejte novou adresu.
5. Vyberte **Uložit**.
6. Restartování virtuálních počítačů, které jsou připojené k virtuální síti, takže jsou přiřazeny nové nastavení serveru DNS. Virtuální počítače i nadále používat své aktuální nastavení služby DNS, dokud se restartují.

**Příkazy**

- Azure CLI: [az network vnet aktualizace](/cli/azure/network/vnet)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Odstranění virtuální sítě

Virtuální síť můžete odstranit pouze v případě, že neexistují žádné prostředky, které jsou k němu připojená. Pokud existují prostředky, které jsou připojené k žádné podsíti v rámci virtuální sítě, musíte nejprve odstranit prostředky, které jsou připojené do všech podsítí ve virtuální síti. Kroky, jak jste se odstranit prostředek se liší v závislosti na prostředku. Zjistěte, jak odstranit prostředky, které jsou připojené k podsítím, najdete v dokumentaci pro každý typ prostředku, který chcete odstranit. Pokud chcete odstranit virtuální síť:

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Seznam virtuálních sítí vyberte virtuální síť, kterou chcete odstranit.
3. Potvrďte, že neexistují žádná zařízení připojené k virtuální síti tak, že vyberete **připojená zařízení**v části **nastavení**. Připojená zařízení je nutné před odstraněním virtuální sítě odstranit. Pokud neexistují žádná připojená zařízení, vyberte **přehled**.
4. Vyberte **Odstranit**.
5. Pokud chcete potvrdit odstranění virtuální sítě, vyberte **Ano**.

**Příkazy**

- Azure CLI: [azure network vnet delete](/cli/azure/network/vnet#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork)

## <a name="permissions"></a>Oprávnění

K provedení úlohy ve virtuálních sítích, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušné akce uvedené v následující tabulce:

| Akce                                  |   Název                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Načíst virtuální sítě              |
|Microsoft.Network/virtualNetworks/write  |   Vytvořit nebo aktualizovat virtuální síť  |
|Microsoft.Network/virtualNetworks/delete |   Odstranění virtuální sítě            |

## <a name="next-steps"></a>Další postup

- Vytvoření virtuální sítě pomocí [PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablon Resource Manageru](template-samples.md)
- Vytvoření a použití [Azure policy](policy-samples.md) pro virtuální sítě
