---
title: Vytvoření, změna nebo odstranění síťového rozhraní Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, co je síťové rozhraní a jak vytvořit, změnit nastavení pro a odstranit ho.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 74e09b4798a648b6a881fb05f1128831ad5f4aff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586442"
---
# <a name="create-change-or-delete-a-network-interface"></a>Vytvoření, změna nebo odstranění síťového rozhraní

Naučte se vytvářet, měnit nastavení a odstraňovat síťové rozhraní. Síťové rozhraní umožňuje virtuálnímu počítači Azure komunikovat s internetem, Azure a místními prostředky. Při vytváření virtuálního počítače pomocí Azure Portal vytvoří portál jedno síťové rozhraní s výchozími nastaveními. Místo toho se můžete rozhodnout vytvořit síťová rozhraní s vlastním nastavením a při vytváření virtuálního počítače přidat jedno nebo víc síťových rozhraní. Možná budete chtít změnit výchozí nastavení síťového rozhraní pro existující síťové rozhraní. Tento článek vysvětluje, jak vytvořit síťové rozhraní s vlastním nastavením, změnit stávající nastavení, například síťový filtr (skupina zabezpečení sítě), přiřazení podsítě, nastavení serveru DNS a předávání IP, a odstranit síťové rozhraní.

Pokud potřebujete přidat, změnit nebo odebrat IP adresy pro síťové rozhraní, přečtěte si téma [Správa IP adres](virtual-network-network-interface-addresses.md). Pokud potřebujete přidat síťová rozhraní do nebo odebrat síťová rozhraní z virtuálních počítačů, přečtěte si téma [Přidání nebo odebrání síťových rozhraní](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku proveďte následující úlohy:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.com a přihlaste se pomocí svého účtu Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy prostředí PowerShell, buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento kurz vyžaduje Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit, `az login` aby se vytvořilo připojení k Azure.

Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v [oprávněních](#permissions).

## <a name="create-a-network-interface"></a>Vytvoření síťového rozhraní

Při vytváření virtuálního počítače pomocí Azure Portal portál vytvoří síťové rozhraní s výchozími nastaveními. Pokud místo toho chcete zadat všechna nastavení síťového rozhraní, můžete vytvořit síťové rozhraní s vlastním nastavením a připojit síťové rozhraní k virtuálnímu počítači při vytváření virtuálního počítače (pomocí PowerShellu nebo rozhraní příkazového řádku Azure). Můžete také vytvořit síťové rozhraní a přidat ho k existujícímu virtuálnímu počítači (pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI). Informace o tom, jak vytvořit virtuální počítač se stávajícím síťovým rozhraním nebo přidat do nebo odebrat síťová rozhraní z existujících virtuálních počítačů, najdete v tématu [Přidání nebo odebrání síťových rozhraní](virtual-network-network-interface-vm.md). Před vytvořením síťového rozhraní musíte mít existující [virtuální síť](manage-virtual-network.md) ve stejném umístění a předplatném, ve kterém vytvoříte síťové rozhraní.

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *Síťová rozhraní*. Pokud se ve výsledcích hledání zobrazí **Síťová rozhraní** , vyberte ji.
2. V části **Síťová rozhraní** vyberte **+ Přidat** .
3. Zadejte nebo vyberte hodnoty pro následující nastavení a pak vyberte **vytvořit**:

    |Nastavení|Povinné?|Podrobnosti|
    |---|---|---|
    |Name|Yes|Název musí být jedinečný v rámci vybrané skupiny prostředků. V průběhu času pravděpodobně máte ve svém předplatném Azure několik síťových rozhraní. Návrhy při vytváření zásad vytváření názvů, které usnadňují správu několika síťových rozhraní, najdete v tématu zásady [vytváření názvů](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). Po vytvoření síťového rozhraní se název nedá změnit.|
    |Virtuální síť|Yes|Vyberte virtuální síť pro síťové rozhraní. Síťové rozhraní můžete přiřadit jenom k virtuální síti, která existuje ve stejném předplatném a umístění jako síťové rozhraní. Po vytvoření síťového rozhraní nemůžete změnit virtuální síť, ke které je přiřazená. Virtuální počítač, ke kterému přidáte síťové rozhraní, musí existovat také ve stejném umístění a předplatném jako síťové rozhraní.|
    |Podsíť|Yes|Vyberte podsíť ve virtuální síti, kterou jste vybrali. Můžete změnit podsíť, ke které je síťové rozhraní přiřazeno po jeho vytvoření.|
    |Přiřazení privátní IP adresy|Yes| V tomto nastavení zvolíte způsob přiřazení pro adresu IPv4. Zvolte jednu z následujících metod přiřazení: **dynamická:** při výběru této možnosti Azure automaticky přiřadí další dostupnou adresu z adresního prostoru vybrané podsítě. **Statická:** Když vyberete tuto možnost, musíte ručně přiřadit dostupnou IP adresu z adresního prostoru vybrané podsítě. Statické a dynamické adresy se nemění, dokud je nezměníte nebo neodstraníte síťové rozhraní. Způsob přiřazení můžete změnit po vytvoření síťového rozhraní. Server DHCP Azure tuto adresu přiřadí síťovému rozhraní v operačním systému virtuálního počítače.|
    |Skupina zabezpečení sítě|No| Nechejte nastavenou možnost **žádné**, vyberte existující [skupinu zabezpečení sítě](./network-security-groups-overview.md)nebo [vytvořte skupinu zabezpečení sítě](tutorial-filter-network-traffic.md). Skupiny zabezpečení sítě umožňují filtrovat síťový provoz v a ze síťového rozhraní. K síťovému rozhraní můžete použít žádnou skupinu zabezpečení sítě nebo jednu. Pro podsíť, ke které je síťové rozhraní přiřazeno, se dá použít taky nula nebo jedna skupina zabezpečení sítě. Když se skupina zabezpečení sítě použije na síťové rozhraní a podsíť, ke které je síťové rozhraní přiřazené, někdy dojde k neočekávaným výsledkům. Řešení potíží se skupinami zabezpečení sítě použitými pro síťová rozhraní a podsítě najdete v tématu [řešení potíží se skupinami zabezpečení sítě](diagnose-network-traffic-filter-problem.md).|
    |Předplatné|Yes|Vyberte jedno z vašich [předplatných](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)Azure. Virtuální počítač, ke kterému připojíte síťové rozhraní a virtuální síť, ke které ho připojujete, musí existovat ve stejném předplatném.|
    |Privátní IP adresa (IPv6)|No| Pokud toto políčko zaškrtnete, přiřadí se síťovému rozhraní adresa IPv6, kromě adresy IPv4 přiřazené síťovému rozhraní. Důležité informace o použití protokolu IPv6 se síťovými rozhraními najdete v části věnované protokolu IPv6 v tomto článku. Pro adresu IPv6 nelze vybrat metodu přiřazení. Pokud se rozhodnete přiřadit adresu IPv6, je přiřazena s dynamickou metodou.
    |Název IPv6 (zobrazí se jenom v případě, že je zaškrtnuté políčko **privátní IP adresa (IPv6)** ) |Ano, pokud je zaškrtnuto políčko **privátní IP adresa (IPv6)** .| Tento název je přiřazený k sekundární konfiguraci protokolu IP pro síťové rozhraní. Další informace o konfiguracích IP adres najdete v tématu [zobrazení nastavení síťového rozhraní](#view-network-interface-settings).|
    |Skupina prostředků|Yes|Vyberte existující [skupinu prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) nebo ji vytvořte. Síťové rozhraní může existovat ve stejné nebo jiné skupině prostředků, než je virtuální počítač, ke kterému ho připojíte, nebo k virtuální síti, ke které ho připojujete.|
    |Umístění|Ano|Virtuální počítač, ke kterému připojíte síťové rozhraní a virtuální síť, ke které ho připojujete, musí existovat ve stejném [umístění](https://azure.microsoft.com/regions), které se označuje také jako oblast.|

Portál neposkytuje k síťovému rozhraní možnost přiřazení veřejné IP adresy, když ji vytvoříte, přestože portál při vytváření virtuálního počítače pomocí portálu vytvoří veřejnou IP adresu a přiřadí ji k síťovému rozhraní. Informace o přidání veřejné IP adresy k síťovému rozhraní po jeho vytvoření najdete v tématu [Správa IP adres](virtual-network-network-interface-addresses.md). Pokud chcete vytvořit síťové rozhraní s veřejnou IP adresou, musíte k vytvoření síťového rozhraní použít rozhraní příkazového řádku nebo PowerShell.

Portál nenabízí možnost přiřazení síťového rozhraní ke skupinám zabezpečení aplikace při vytváření síťového rozhraní, ale Azure CLI a PowerShellu. Stávající síťové rozhraní můžete přiřadit skupině zabezpečení aplikace pomocí portálu, pokud je síťové rozhraní připojené k virtuálnímu počítači. Informace o tom, jak přiřadit síťové rozhraní ke skupině zabezpečení aplikace, najdete v tématu [přidání do skupin zabezpečení aplikace nebo jejich odebrání](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Až se síťové rozhraní připojí k virtuálnímu počítači a virtuální počítač se spustí poprvé, Azure přiřadí adresu MAC k síťovému rozhraní. Nemůžete zadat adresu MAC, kterou Azure přiřadí k síťovému rozhraní. Adresa MAC zůstane přiřazená síťovému rozhraní, dokud se neodstraní síťové rozhraní nebo se nezmění privátní IP adresa přiřazená k primární konfiguraci IP rozhraní primárního síťového rozhraní. Další informace o IP adresách a konfiguracích IP adres najdete v tématu [Správa IP adres](virtual-network-network-interface-addresses.md) .

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Zobrazit nastavení síťového rozhraní

Po vytvoření můžete zobrazit a změnit většinu nastavení síťového rozhraní. Portál nezobrazuje pro síťové rozhraní příponu DNS ani členství ve skupině zabezpečení aplikací. Pomocí příkazů PowerShellu nebo rozhraní [příkazového](#view-settings-commands) řádku Azure můžete zobrazit příponu DNS a členství ve skupinách zabezpečení aplikací.

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *Síťová rozhraní*. Pokud se ve výsledcích hledání zobrazí **Síťová rozhraní** , vyberte ji.
2. V seznamu vyberte síťové rozhraní, pro které chcete zobrazit nebo změnit nastavení.
3. Pro síťové rozhraní, které jste vybrali, se zobrazí následující položky:
   - **Přehled:** Poskytuje informace o síťovém rozhraní, jako jsou například IP adresy, které jsou mu přiřazeny, virtuální síti/podsíti, ke které je síťové rozhraní přiřazeno, a virtuálnímu počítači, ke kterému je síťové rozhraní připojené (Pokud je připojené k jednomu). Následující obrázek ukazuje nastavení přehledu pro síťové rozhraní s názvem **mywebserver256**: ![ Přehled síťového rozhraní.](./media/virtual-network-network-interface/nic-overview.png)

     Síťové rozhraní můžete přesunout do jiné skupiny prostředků nebo předplatného tak, že vyberete (**změnit**) vedle položky název **skupiny prostředků** nebo **předplatného**. Pokud síťové rozhraní přesunete do nového předplatného, musíte s ním přesunout všechny prostředky, které se vztahují k síťovému rozhraní. Pokud je síťové rozhraní připojené k virtuálnímu počítači, například musíte přesunout virtuální počítač a další prostředky související s virtuálním počítačem. Pokud chcete přesunout síťové rozhraní, přečtěte si téma [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). Článek obsahuje seznam požadovaných součástí a Postup přesunutí prostředků pomocí Azure Portal, PowerShellu a rozhraní příkazového řádku Azure CLI.
   - **Konfigurace protokolu IP:** Tady jsou uvedené veřejné a privátní IPv4 a IPv6 adresy přiřazené konfiguracím IP adres. Pokud je adresa IPv6 přiřazena ke konfiguraci protokolu IP, adresa se nezobrazí. Další informace o konfiguracích protokolu IP a způsobu přidání a odebrání IP adres najdete v tématu [Konfigurace IP adres pro síťové rozhraní Azure](virtual-network-network-interface-addresses.md). V této části jsou také konfigurovány předávání IP adres a přiřazování podsítí. Další informace o těchto nastaveních najdete v tématu [Povolení nebo zakázání předávání IP](#enable-or-disable-ip-forwarding) a [Změna přiřazení podsítě](#change-subnet-assignment).
   - **Servery DNS:** Můžete určit, který server DNS má síťové rozhraní přiřazené servery DHCP Azure. Síťové rozhraní může dědit nastavení z virtuální sítě, ke které je síťové rozhraní přiřazeno, nebo mít vlastní nastavení, které přepíše nastavení virtuální sítě, ke které je přiřazeno. Pokud chcete upravit zobrazené informace, přečtěte si téma [Změna serverů DNS](#change-dns-servers).
   - **Skupina zabezpečení sítě (NSG):** Zobrazuje, které NSG jsou přidružené k síťovému rozhraní (pokud existuje). NSG obsahuje pravidla příchozích a odchozích dat pro filtrování síťového provozu pro síťové rozhraní. Pokud je k síťovému rozhraní přidružená NSG, zobrazí se název přidruženého NSG. Pokud chcete upravit zobrazené informace, přečtěte si téma [přidružení nebo odrušení skupiny zabezpečení sítě](#associate-or-dissociate-a-network-security-group).
   - **Vlastnosti:** Zobrazí nastavení klíče síťového rozhraní, včetně jeho adresy MAC (prázdné, pokud síťové rozhraní není připojené k virtuálnímu počítači) a předplatné, ve kterém existuje.
   - **Platná pravidla zabezpečení:**  Pravidla zabezpečení jsou uvedena, pokud je síťové rozhraní připojené k běžícímu virtuálnímu počítači a NSG je přidružen k síťovému rozhraní, podsíti, ke které je přiřazen, nebo k oběma. Další informace o tom, co se zobrazuje, najdete v tématu [zobrazení efektivních pravidel zabezpečení](#view-effective-security-rules). Další informace o skupin zabezpečení sítě najdete v tématu [skupiny zabezpečení sítě](./network-security-groups-overview.md).
   - **Efektivní trasy:** Pokud je síťové rozhraní připojené k běžícímu virtuálnímu počítači, jsou uvedené trasy. Trasy jsou kombinací výchozích tras Azure, všech uživatelem definovaných tras a všech tras protokolu BGP, které mohou existovat pro podsíť, ke které je síťové rozhraní přiřazeno. Další informace o tom, co se zobrazuje, najdete v tématu [zobrazení efektivních tras](#view-effective-routes). Další informace o výchozích trasách Azure a uživatelem definovaných trasách najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
Nastavení běžných Azure Resource Manager: Další informace o běžných nastaveních Azure Resource Manager najdete v tématu [Protokol aktivit](../azure-monitor/essentials/platform-logs-overview.md), [řízení přístupu (IAM)](../role-based-access-control/overview.md), [značky](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [zámky](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)a [skript Automation](../azure-resource-manager/templates/export-template-portal.md).

<a name="view-settings-commands"></a>**Příkaz**

Pokud je adresa IPv6 přiřazena síťovému rozhraní, vrátí výstup PowerShellu skutečnost, že adresa je přiřazena, ale nevrátí přiřazenou adresu. Podobně rozhraní příkazového řádku vrací skutečnost, že adresa je přiřazena, ale ve výstupu pro danou adresu vrací *hodnotu null* .

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network nic list](/cli/azure/network/nic) , chcete-li zobrazit síťová rozhraní v předplatném; [AZ Network nic show zobrazí](/cli/azure/network/nic) nastavení pro síťové rozhraní.|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) zobrazí síťová rozhraní v předplatném nebo nastavení zobrazení pro síťové rozhraní.|

## <a name="change-dns-servers"></a>Změna serverů DNS

Server DNS je přiřazený serverem DHCP Azure pro síťové rozhraní v operačním systému virtuálního počítače. Přiřazený server DNS je bez ohledu na to, že je nastavení serveru DNS pro síťové rozhraní. Další informace o nastavení překladu názvů pro síťové rozhraní najdete v tématu [překlad názvů pro virtuální počítače](virtual-networks-name-resolution-for-vms-and-role-instances.md). Síťové rozhraní může dědit nastavení z virtuální sítě nebo použít vlastní jedinečné nastavení, které přepíše nastavení pro virtuální síť.

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *Síťová rozhraní*. Pokud se ve výsledcích hledání zobrazí **Síťová rozhraní** , vyberte ji.
2. Ze seznamu vyberte síťové rozhraní, pro které chcete změnit server DNS.
3. V části **Nastavení** vyberte **servery DNS** .
4. Vyberte jednu z těchto akcí:
   - **Zdědit z virtuální sítě**: tuto možnost vyberte, pokud chcete zdědit nastavení serveru DNS definované pro virtuální síť, ke které je síťové rozhraní přiřazené. Na úrovni virtuální sítě je definovaný buď vlastní server DNS, nebo server DNS zadaný v Azure. Server DNS poskytovaný Azure může přeložit názvy hostitelů pro prostředky přiřazené ke stejné virtuální síti. K překladu prostředků přiřazených k různým virtuálním sítím je nutné použít plně kvalifikovaný název domény.
   - **Vlastní**: můžete nakonfigurovat vlastní server DNS pro překlad názvů napříč několika virtuálními sítěmi. Zadejte IP adresu serveru, který chcete použít jako server DNS. Adresa serveru DNS, kterou zadáte, se přiřadí jenom tomuto síťovému rozhraní a přepíše všechna nastavení DNS pro virtuální síť, ke které je síťové rozhraní přiřazené.
     >[!Note]
     >Pokud virtuální počítač používá síťovou kartu, která je součástí skupiny dostupnosti, zdědí se všechny servery DNS, které jsou zadané pro každý z virtuálních počítačů ze všech síťových adaptérů, které jsou součástí skupiny dostupnosti.
5. Vyberte **Uložit**.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network nic Update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Povolení nebo zakázání předávání IP

Předávání IP adres umožňuje, aby byl virtuální počítač připojen k síťovému rozhraní:
- Příjem síťového provozu, který není určen pro jednu z IP adres přiřazených ke kterékoli konfiguraci protokolu IP přiřazené k síťovému rozhraní.
- Odešlete síťový provoz s jinou zdrojovou IP adresou, než je ta přiřazená k jedné z konfigurací IP síťového rozhraní.

Nastavení musí být povoleno pro každé síťové rozhraní, které je připojeno k virtuálnímu počítači, který přijímá provoz, který musí virtuální počítač přesměrovat. Virtuální počítač může přesměrování provozu, ať už má několik síťových rozhraní nebo jedno připojené síťové rozhraní. I když je předávání IP nastaveno na Azure, musí virtuální počítač také spustit aplikaci schopnou přesměrovat provoz, jako je brána firewall, optimalizace sítě WAN a aplikace pro vyrovnávání zatížení. Když na virtuálním počítači běží síťové aplikace, virtuální počítač se často označuje jako virtuální síťové zařízení. Můžete si prohlédnout seznam připravených k nasazení síťových virtuálních zařízení v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Předávání IP se obvykle používá s uživatelsky definovanými trasami. Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem](virtual-networks-udr-overview.md).

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *Síťová rozhraní*. Pokud se ve výsledcích hledání zobrazí **Síťová rozhraní** , vyberte ji.
2. Vyberte síťové rozhraní, pro které chcete povolit nebo zakázat předávání IP pro.
3. V části **Nastavení** vyberte **Konfigurace protokolu IP** .
4. Chcete-li změnit nastavení, vyberte možnost **povoleno** nebo **zakázáno** (výchozí nastavení).
5. Vyberte **Uložit**.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network nic Update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Změna přiřazení podsítě

Můžete změnit podsíť, ale ne virtuální síť, ke které je přiřazeno síťové rozhraní.

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *Síťová rozhraní*. Pokud se ve výsledcích hledání zobrazí **Síťová rozhraní** , vyberte ji.
2. Vyberte síťové rozhraní, pro které chcete změnit přiřazení podsítě.
3. V části **Nastavení** vyberte **Konfigurace protokolu IP** . Pokud jsou vedle nich k dispozici libovolné privátní IP adresy pro všechny konfigurace protokolu IP **(statické)** , je třeba změnit metodu přiřazení IP adresy na Dynamic, a to provedením následujících kroků. Aby bylo možné změnit přiřazení podsítě pro síťové rozhraní, musí se všem privátním IP adresám přiřadit metoda dynamického přiřazení. Pokud jsou adresy přiřazovány s dynamickou metodou, pokračujte krokem pět. Pokud se k metodě statického přiřazení přiřadí nějaké adresy IPv4, proveďte následující kroky a změňte metodu přiřazení na dynamickou:
   - V seznamu konfigurací protokolu IP vyberte konfiguraci protokolu IP, pro kterou chcete změnit metodu přiřazování adresy IPv4.
   - Jako metodu **přiřazování** privátní IP adresy vyberte **Dynamická** . Nemůžete přiřadit adresu IPv6 pomocí metody statického přiřazení.
   - Vyberte **Uložit**.
4. Vyberte podsíť, do které chcete přesunout síťové rozhraní z rozevíracího seznamu **podsíť** .
5. Vyberte **Uložit**. Nové dynamické adresy se přiřazují z rozsahu adres podsítě pro novou podsíť. Po přiřazení síťového rozhraní k nové podsíti můžete v případě, že zvolíte, přiřadit statickou IPv4 adresu z nového rozsahu adres podsítě. Další informace o přidání, změně a odebrání IP adres pro síťové rozhraní najdete v tématu [Správa IP adres](virtual-network-network-interface-addresses.md).

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network nic IP-config Update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Přidat nebo odebrat ze skupin zabezpečení aplikace

Síťové rozhraní můžete přidat do skupiny zabezpečení aplikace pomocí portálu, nebo odebrat jenom síťové rozhraní, pokud je síťové rozhraní připojené k virtuálnímu počítači. Pomocí PowerShellu nebo rozhraní příkazového řádku Azure můžete přidat síťové rozhraní do nebo odebrat síťové rozhraní ze skupiny zabezpečení aplikace, ať už je síťové rozhraní připojené k virtuálnímu počítači, nebo ne. Přečtěte si další informace o [skupinách zabezpečení aplikací](./network-security-groups-overview.md#application-security-groups) a o tom, jak [vytvořit skupinu zabezpečení aplikace](manage-network-security-group.md).

1. V poli *Hledat prostředky, služby a dokumenty* v horní části portálu začněte zadávat název virtuálního počítače, který má síťové rozhraní, které chcete přidat, nebo odebrat ze skupiny zabezpečení aplikace. Jakmile se ve výsledcích hledání zobrazí název vašeho virtuálního počítače, vyberte ho.
2. V části **NASTAVENÍ** vyberte **Sítě**.  Vyberte **skupiny zabezpečení** aplikace a pak nakonfigurujte skupiny zabezpečení **aplikací**, které zvolí skupiny zabezpečení aplikace, do kterých chcete přidat síťové rozhraní, nebo zrušte výběr skupin zabezpečení aplikace, ze kterých chcete síťové rozhraní odebrat, a pak vyberte **Uložit**. Do stejné skupiny zabezpečení aplikace lze přidat pouze síťová rozhraní, která existují ve stejné virtuální síti. Skupina zabezpečení aplikace musí existovat ve stejném umístění jako síťové rozhraní.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network nic Update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Přidružit nebo oddělit skupinu zabezpečení sítě

1. Do vyhledávacího pole v horní části portálu zadejte *Síťová rozhraní* do vyhledávacího pole. Pokud se ve výsledcích hledání zobrazí **Síťová rozhraní** , vyberte ji.
2. Vyberte síťové rozhraní v seznamu, ke kterému chcete přidružit skupinu zabezpečení sítě, nebo zrušte přidružení skupiny zabezpečení sítě k.
3. V části **Nastavení** vyberte **Skupina zabezpečení sítě** .
4. Vyberte **Upravit**.
5. Vyberte **Skupina zabezpečení sítě** a potom vyberte skupinu zabezpečení sítě, kterou chcete přidružit k síťovému rozhraní, nebo vyberte možnost **žádné**, chcete-li oddělit skupinu zabezpečení sítě.
6. Vyberte **Uložit**.

**Příkazy**

- Azure CLI: [AZ Network nic Update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Odstranění síťového rozhraní

Síťové rozhraní můžete odstranit, pokud není připojené k virtuálnímu počítači. Pokud je síťové rozhraní připojené k virtuálnímu počítači, musíte nejdřív umístit virtuální počítač do zastaveného (uvolněného) stavu a potom odpojit síťové rozhraní od virtuálního počítače. Chcete-li odpojit síťové rozhraní od virtuálního počítače, proveďte kroky v části [odpojení síťového rozhraní od virtuálního počítače](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Síťové rozhraní nelze z virtuálního počítače odpojit, pokud je však jediným síťovým rozhraním připojeným k virtuálnímu počítači. K virtuálnímu počítači musí být vždycky připojené aspoň jedno síťové rozhraní. Odstraněním virtuálního počítače odpojíte všechna síťová rozhraní, která jsou k němu připojená, ale neodstraníte síťová rozhraní.

1. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *Síťová rozhraní*. Pokud se ve výsledcích hledání zobrazí **Síťová rozhraní** , vyberte ji.
2. V seznamu vyberte síťové rozhraní, které chcete odstranit.
3. V části **Přehled** vyberte **Odstranit**.
4. Výběrem **Ano** potvrďte odstranění síťového rozhraní.

Po odstranění síťového rozhraní se uvolní všechny adresy MAC nebo IP, které jsou mu přiřazeny.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ Network nic DELETE](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Řešení potíží s připojením

Pokud nemůžete komunikovat s virtuálním počítačem nebo z něj, může problém způsobovat pravidla zabezpečení skupiny zabezpečení sítě nebo trasy platné pro síťové rozhraní. Máte následující možnosti, které vám pomůžou problém vyřešit:

### <a name="view-effective-security-rules"></a>Zobrazit platná pravidla zabezpečení

Platná pravidla zabezpečení pro každé síťové rozhraní připojené k virtuálnímu počítači jsou kombinací pravidel, která jste vytvořili ve skupině zabezpečení sítě a [výchozích pravidel zabezpečení](./network-security-groups-overview.md#default-security-rules). Princip efektivních pravidel zabezpečení pro síťové rozhraní vám může pomáhat s určením, proč nemůžete komunikovat s virtuálním počítačem nebo na něm. Můžete zobrazit platná pravidla pro jakékoli síťové rozhraní, které je připojené k běžícímu virtuálnímu počítači.

1. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, pro který chcete zobrazit platná pravidla zabezpečení. Pokud neznáte název virtuálního počítače, do vyhledávacího pole zadejte *virtuální počítače* . Když se **virtuální počítače** zobrazí ve výsledcích hledání, vyberte ji a pak ze seznamu vyberte virtuální počítač.
2. V části **Nastavení** vyberte **sítě** .
3. Vyberte název síťového rozhraní.
4. V části **Podpora a řešení potíží** vyberte **platná pravidla zabezpečení** .
5. Projděte si seznam platných pravidel zabezpečení, abyste zjistili, jestli pro požadovanou příchozí a odchozí komunikaci existují správná pravidla. Přečtěte si další informace o tom, co se zobrazuje v seznamu v tématu [Přehled skupin zabezpečení sítě](./network-security-groups-overview.md).

Funkce ověření toku protokolu IP v Azure Network Watcher vám také pomůže určit, jestli pravidla zabezpečení zabraňují komunikaci mezi virtuálním počítačem a koncovým bodem. Další informace najdete v tématu [ověření toku protokolu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Příkazy**

- Azure CLI: [AZ Network nic list-efektivní-NSG](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Zobrazit efektivní trasy

Platné trasy pro síťová rozhraní připojená k virtuálnímu počítači jsou kombinací výchozích tras, všech tras, které jste vytvořili, a všech tras, které se šíří z místních sítí prostřednictvím protokolu BGP přes bránu virtuální sítě Azure. Princip efektivních tras pro síťové rozhraní vám může přispět k určení, proč nemůžete komunikovat s virtuálním počítačem nebo z něj. Můžete si prohlédnout efektivní trasy pro jakékoli síťové rozhraní, které je připojené k běžícímu virtuálnímu počítači.

1. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, pro který chcete zobrazit platná pravidla zabezpečení. Pokud neznáte název virtuálního počítače, do vyhledávacího pole zadejte *virtuální počítače* . Když se **virtuální počítače** zobrazí ve výsledcích hledání, vyberte ji a pak ze seznamu vyberte virtuální počítač.
2. V části **Nastavení** vyberte **sítě** .
3. Vyberte název síťového rozhraní.
4. V části **Podpora a řešení potíží** vyberte **platné trasy** .
5. Projděte si seznam efektivních tras, abyste zjistili, jestli pro požadovanou příchozí a odchozí komunikaci existují správné trasy. Přečtěte si další informace o tom, co vidíte v seznamu v tématu [Přehled směrování](virtual-networks-udr-overview.md).

Funkce dalšího segmentu pro Azure Network Watcher vám také pomůže určit, jestli trasy brání komunikaci mezi virtuálním počítačem a koncovým bodem. Další informace najdete v tématu [Další segment směrování](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Příkazy**

- Azure CLI: [AZ Network nic show-efektivní-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Oprávnění

Aby bylo možné provádět úlohy na síťových rozhraních, musí být váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, která má přiřazená příslušná oprávnění uvedená v následující tabulce:

| Akce                                                                     | Name                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft. Network/networkInterfaces/Read                                   | Získat síťové rozhraní                                     |
| Microsoft. Network/networkInterfaces/Write                                  | Vytvořit nebo aktualizovat síťové rozhraní                        |
| Microsoft. Network/networkInterfaces/JOIN/Action                            | Připojení síťového rozhraní k virtuálnímu počítači           |
| Microsoft. Network/networkInterfaces/DELETE                                 | Odstranit síťové rozhraní                                  |
| Microsoft. Network/networkInterfaces/joinViaPrivateIp/Action                | Připojit prostředek k síťovému rozhraní prostřednictvím serve...     |
| Microsoft. Network/networkInterfaces/effectiveRouteTable/Action             | Získat efektivní směrovací tabulku síťového rozhraní               |
| Microsoft. Network/networkInterfaces/effectiveNetworkSecurityGroups/Action  | Získání efektivních skupin zabezpečení síťového rozhraní           |
| Microsoft. Network/networkInterfaces/loadBalancers/Read                     | Získat nástroje pro vyrovnávání zatížení síťového rozhraní                      |
| Microsoft. Network/networkInterfaces/serviceAssociations/Read               | Získat přidružení služby                                   |
| Microsoft. Network/networkInterfaces/serviceAssociations/Write              | Vytvořit nebo aktualizovat přidružení služby                    |
| Microsoft. Network/networkInterfaces/serviceAssociations/DELETE             | Odstranit přidružení služby                                |
| Microsoft. Network/networkInterfaces/serviceAssociations/Validate/Action    | Ověřit přidružení služby                              |
| Microsoft. Network/networkInterfaces/IPConfiguration/Read                  | Získání konfigurace IP adresy síťového rozhraní                    |

## <a name="next-steps"></a>Další kroky

- Vytvoření virtuálního počítače s několika síťovými kartami pomocí [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [PowerShellu](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Vytvoření virtuálního počítače s jedním síťovým ADAPTÉRem s více adresami IPv4 pomocí [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) nebo [PowerShellu](virtual-network-multiple-ip-addresses-powershell.md)
- Vytvoření virtuálního počítače s jedním síťovým ADAPTÉRem s privátní adresou IPv6 (za Azure Load Balancer) pomocí [Azure CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShellu](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)nebo [šablony Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Vytvoření síťového rozhraní pomocí [PowerShellu](powershell-samples.md) nebo ukázkových skriptů [Azure CLI](cli-samples.md) nebo použití [šablony Azure správce prostředků](template-samples.md)
- Vytvoření a přiřazení [definic Azure Policy](./policy-reference.md) pro virtuální sítě