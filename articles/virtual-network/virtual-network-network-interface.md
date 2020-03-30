---
title: Vytvoření, změna nebo odstranění síťového rozhraní Azure
titlesuffix: Azure Virtual Network
description: Zjistěte, co je síťové rozhraní a jak vytvořit, změnit nastavení a odstranit jedno.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 11e6285ef70ffde5344add951801997f8541eaad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244937"
---
# <a name="create-change-or-delete-a-network-interface"></a>Vytvoření, změna nebo odstranění síťového rozhraní

Přečtěte si, jak vytvořit, změnit nastavení a odstranit síťové rozhraní. Síťové rozhraní umožňuje virtuálnímu počítači Azure komunikovat s internetovými, Azure a místními prostředky. Při vytváření virtuálního počítače pomocí portálu Azure portál vytvoří jedno síťové rozhraní s výchozím nastavením pro vás. Místo toho můžete zvolit vytvoření síťových rozhraní s vlastním nastavením a přidat jedno nebo více síťových rozhraní do virtuálního počítače při jeho vytváření. Můžete také změnit výchozí nastavení síťového rozhraní pro existující síťové rozhraní. Tento článek vysvětluje, jak vytvořit síťové rozhraní s vlastním nastavením, změnit stávající nastavení, například přiřazení síťového filtru (skupina zabezpečení sítě), přiřazení podsítě, nastavení serveru DNS a předávání IP adres a odstranit síťové rozhraní.

Pokud potřebujete přidat, změnit nebo odebrat adresy IP pro síťové rozhraní, přečtěte si informace [o správě adres IP](virtual-network-network-interface-addresses.md). Pokud potřebujete přidat síťová rozhraní nebo odebrat síťová rozhraní z virtuálních počítačů, přečtěte si informace o [přidání nebo odebrání síťových rozhraní](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v libovolné části tohoto článku proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud pomocí příkazů Prostředí PowerShell k dokončení úloh v tomto článku, buď spustit příkazy v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud pomocí příkazů rozhraní Příkazového řádku Azure (CLI) k dokončení úloh v tomto článku, buď spustit příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také `az login` spustit k vytvoření připojení s Azure.

Účet, ke kterému se přihlásíte nebo se s ním připojíte, musí být přiřazen k roli [síťového přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v [části Oprávnění](#permissions).

## <a name="create-a-network-interface"></a>Vytvoření síťového rozhraní

Při vytváření virtuálního počítače pomocí portálu Azure portál vytvoří síťové rozhraní s výchozím nastavením pro vás. Pokud byste raději zadali všechna nastavení síťového rozhraní, můžete vytvořit síťové rozhraní s vlastními nastaveními a připojit síťové rozhraní k virtuálnímu počítači při vytváření virtuálního počítače (pomocí PowerShellu nebo rozhraní příkazového řádku Azure). Můžete také vytvořit síťové rozhraní a přidat ho do existujícího virtuálního počítače (pomocí PowerShellu nebo rozhraní příkazového řádku Azure). Informace o tom, jak vytvořit virtuální počítač s existujícím síťovým rozhraním nebo přidat do nebo odebrat síťová rozhraní z existujících virtuálních počítačů, najdete v tématu [Přidání nebo odebrání síťových rozhraní](virtual-network-network-interface-vm.md). Před vytvořením síťového rozhraní musíte mít existující [virtuální síť](manage-virtual-network.md) ve stejném umístění a předplatné, ve které vytvoříte síťové rozhraní.

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *síťová rozhraní*. Když se ve výsledcích hledání zobrazí **síťová rozhraní,** vyberte je.
2. V části **Síťová rozhraní**vyberte **+ Přidat** .
3. Zadejte nebo vyberte hodnoty pro následující nastavení a pak vyberte **Vytvořit**:

    |Nastavení|Povinné?|Podrobnosti|
    |---|---|---|
    |Name (Název)|Ano|Název musí být jedinečný v rámci vybrané skupiny prostředků. V průběhu času budete mít pravděpodobně několik síťových rozhraní ve vašem předplatném Azure. Návrhy při vytváření konvence pojmenování usnadňující správu několika síťových rozhraní naleznete [v tématu Konvence pojmenování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). Název nelze po vytvoření síťového rozhraní změnit.|
    |Virtuální síť|Ano|Vyberte virtuální síť pro síťové rozhraní. Síťové rozhraní můžete přiřadit pouze k virtuální síti, která existuje ve stejném předplatném a umístění jako síťové rozhraní. Po vytvoření síťového rozhraní nelze změnit virtuální síť, ke které je přiřazeno. Virtuální počítač, do který přidáte síťové rozhraní, musí také existovat ve stejném umístění a předplatné jako síťové rozhraní.|
    |Podsíť|Ano|Vyberte podsíť ve vybrané virtuální síti. Můžete změnit podsíť, ke které je síťové rozhraní přiřazeno po jeho vytvoření.|
    |Přiřazení privátní IP adresy|Ano| V tomto nastavení vybíráte metodu přiřazení pro adresu IPv4. Zvolte z následujících metod přiřazení: **Dynamic:** Při výběru této možnosti Azure automaticky přiřadí další dostupnou adresu z adresního prostoru vybrané podsítě. **Statické:** Při výběru této možnosti je nutné ručně přiřadit dostupnou adresu IP z adresního prostoru vybrané podsítě. Statické a dynamické adresy se nezmění, dokud je nezměníte nebo dokud nebude odstraněno síťové rozhraní. Metodu přiřazení můžete změnit po vytvoření síťového rozhraní. Server Azure DHCP přiřadí tuto adresu síťovému rozhraní v rámci operačního systému virtuálního počítače.|
    |Skupina zabezpečení sítě|Ne| Ponechte nastavenou na **žádnou**, vyberte existující [skupinu zabezpečení sítě](security-overview.md)nebo [vytvořte skupinu zabezpečení sítě](tutorial-filter-network-traffic.md). Skupiny zabezpečení sítě umožňují filtrovat síťový provoz v síťovém rozhraní a z něze. Na síťové rozhraní můžete použít nulovou nebo jednu skupinu zabezpečení sítě. Nula nebo jedna skupina zabezpečení sítě lze také použít pro podsíť, ke které je síťové rozhraní přiřazeno. Pokud je skupina zabezpečení sítě použita na síťové rozhraní a podsíť, ke které je síťové rozhraní přiřazeno, někdy dojde k neočekávaným výsledkům. Informace o řešení potíží se skupinami zabezpečení sítě použitými v síťových rozhraních a podsítích naleznete [v tématu Poradce při potížích se skupinami zabezpečení sítě](diagnose-network-traffic-filter-problem.md).|
    |Předplatné|Ano|Vyberte jedno ze svých [předplatných](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)Azure . Virtuální počítač, ke kterému připojíte síťové rozhraní, a virtuální síť, ke které ho připojujete, musí existovat ve stejném předplatném.|
    |Soukromá IP adresa (IPv6)|Ne| Pokud toto políčko zaškrtnete, bude síťovému rozhraní přiřazena adresa IPv6, která je přiřazena síťovému rozhraní. Důležité informace o používání iPv6 se síťovými rozhraními naleznete v části IPv6 v tomto článku. Pro adresu IPv6 nelze vybrat metodu přiřazení. Pokud se rozhodnete přiřadit adresu IPv6, bude přiřazena dynamická metoda.
    |Název IPv6 (zobrazí se pouze v případě, že je zaškrtnuto políčko **Soukromá IP adresa (IPv6)** |Ano, pokud je zaškrtnuté políčko **Soukromá IP adresa (IPv6).**| Tento název je přiřazen k sekundární konfiguraci protokolu IP pro síťové rozhraní. Další informace o konfiguracích IP adres naleznete v [tématu Zobrazení nastavení síťového rozhraní](#view-network-interface-settings).|
    |Skupina prostředků|Ano|Vyberte existující [skupinu prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) nebo ji vytvořte. Síťové rozhraní může existovat ve stejné nebo jiné skupině prostředků než virtuální počítač, ke kterému jej připojíte, nebo virtuální síť, ke které jej připojujete.|
    |Umístění|Ano|Virtuální počítač, ke kterému připojíte síťové rozhraní, a virtuální síť, ke které ho připojujete, musí existovat ve stejném [umístění](https://azure.microsoft.com/regions), označovaném také jako oblast.|

Portál neposkytuje možnost přiřadit veřejné IP adresu síťovému rozhraní při jeho vytvoření, i když portál vytvoří veřejnou IP adresu a přiřadí ji k síťovému rozhraní při vytváření virtuálního počítače pomocí portálu. Informace o tom, jak přidat veřejnou IP adresu do síťového rozhraní po jejím vytvoření, naleznete v [tématu Správa adres IP](virtual-network-network-interface-addresses.md). Pokud chcete vytvořit síťové rozhraní s veřejnou IP adresou, musíte k vytvoření síťového rozhraní použít rozhraní rozhraní cli nebo PowerShell.

Portál neposkytuje možnost přiřadit síťové rozhraní skupinám zabezpečení aplikací při vytváření síťového rozhraní, ale azure CLI a PowerShell ano. Existující síťové rozhraní můžete přiřadit skupině zabezpečení aplikace pomocí portálu, pokud je však síťové rozhraní připojeno k virtuálnímu počítači. Informace o tom, jak přiřadit síťové rozhraní skupině zabezpečení aplikace, najdete v tématu [Přidání do skupin zabezpečení aplikací nebo jejich odebrání .](#add-to-or-remove-from-application-security-groups)

>[!Note]
> Azure přiřadí mac adresu síťovému rozhraní až po připojení síťového rozhraní k virtuálnímu počítači a virtuální počítač se spustí poprvé. Nelze zadat adresu MAC, kterou Azure přiřadí síťovému rozhraní. Adresa MAC zůstává přiřazena k síťovému rozhraní, dokud není síťové rozhraní odstraněno nebo dokud se nezmění privátní adresa IP přiřazená primární konfiguraci IP primárního síťového rozhraní. Další informace o adresách IP a konfiguracích IP adres naleznete v [tématu Správa adres IP](virtual-network-network-interface-addresses.md)

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[Nové rozhraní AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Zobrazit nastavení síťového rozhraní

Většinu nastavení síťového rozhraní můžete zobrazit a změnit po jeho vytvoření. Na portálu se nezobrazuje přípona DNS nebo členství ve skupině zabezpečení aplikace pro síťové rozhraní. [Příkazy](#view-settings-commands) PowerShellu nebo Azure CLI můžete použít k zobrazení přípony DNS a členství ve skupině zabezpečení aplikace.

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *síťová rozhraní*. Když se ve výsledcích hledání zobrazí **síťová rozhraní,** vyberte je.
2. V seznamu vyberte síťové rozhraní, pro které chcete zobrazit nebo změnit nastavení.
3. Pro vybrané síťové rozhraní jsou uvedeny následující položky:
   - **Přehled:** Obsahuje informace o síťovém rozhraní, například o adresách IP, které jsou mu přiřazeny, o virtuální síti nebo podsíti, ke které je síťové rozhraní přiřazeno, a o virtuálním počítači, ke kterému je síťové rozhraní připojeno (pokud je k němu připojeno). Následující obrázek znázorňuje nastavení přehledu síťového rozhraní ![s názvem **mywebserver256**: Přehled síťového rozhraní](./media/virtual-network-network-interface/nic-overview.png)

     Síťové rozhraní můžete přesunout do jiné skupiny prostředků nebo předplatného výběrem možnosti **(změnit)** vedle **skupiny prostředků** nebo **názvu předplatného**. Pokud přesunete síťové rozhraní, je nutné přesunout všechny prostředky související se síťovým rozhraním s ním. Pokud je například síťové rozhraní připojené k virtuálnímu počítači, musíte také přesunout virtuální počítač a další prostředky související s virtuálním počítačem. Pokud chcete přesunout síťové rozhraní, přečtěte si informace o [přesunutí prostředku do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). V článku jsou uvedeny požadavky a jak přesunout prostředky pomocí portálu Azure, PowerShellu a příkazového příkazového příkazu k azure.
   - **Konfigurace IP:** Veřejné a soukromé adresy IPv4 a IPv6 přiřazené konfiguracím IP adres jsou uvedeny zde. Pokud je konfigurace PROTOKOLU IP přiřazena adresa IPv6, adresa se nezobrazí. Další informace o konfiguracích IP adres a o tom, jak přidat a odebrat IP adresy, [najdete v tématu Konfigurace IP adres pro síťové rozhraní Azure](virtual-network-network-interface-addresses.md). Předávání IP adres a přiřazení podsítě jsou také konfigurovány v této části. Další informace o těchto nastaveních naleznete v [tématu Povolení nebo zakázání předávání IP](#enable-or-disable-ip-forwarding) adres a [změna přiřazení podsítě](#change-subnet-assignment).
   - **Servery DNS:** Můžete určit, který server DNS přiřadí síťové mutovi servery Azure DHCP. Síťové rozhraní může dědit nastavení z virtuální sítě, ke které je síťové rozhraní přiřazeno, nebo mít vlastní nastavení, které přepíše nastavení pro virtuální síť, které je přiřazeno. Informace o úpravě zobrazeného nastavení naleznete v [tématu Změna serverů DNS](#change-dns-servers).
   - **Skupina zabezpečení sítě (NSG):** Zobrazí, který síťový název je přidružen k síťovému rozhraní (pokud existuje). Skupina zabezpečení sítě obsahuje příchozí a odchozí pravidla pro filtrování síťového provozu pro síťové rozhraní. Pokud je k síťovému rozhraní přidružensoubor nsg, zobrazí se název přidruženého souboru zabezpečení sítě. Chcete-li upravit zobrazené informace, přečtěte si informace [o přidružení nebo oddělení skupiny zabezpečení sítě](#associate-or-dissociate-a-network-security-group).
   - **Vlastnosti:** Zobrazí nastavení klíčů o síťovém rozhraní, včetně jeho adresy MAC (prázdné, pokud síťové rozhraní není připojeno k virtuálnímu počítači) a předplatné, ve které existuje.
   - **Účinná bezpečnostní pravidla:**  Pravidla zabezpečení jsou uvedena, pokud je síťové rozhraní připojeno ke spuštěnému virtuálnímu počítači a skupina zabezpečení sítě je přidružena k síťovému rozhraní, podsíti, ke které je přiřazena, nebo k oběma. Další informace o tom, co se zobrazuje, najdete v [tématu Zobrazení účinných pravidel zabezpečení](#view-effective-security-rules). Další informace o skupinách nsg naleznete v tématu [Skupiny zabezpečení sítě](security-overview.md).
   - **Efektivní trasy:** Trasy jsou uvedeny, pokud je síťové rozhraní připojeno ke spuštěnému virtuálnímu počítači. Trasy jsou kombinací výchozích tras Azure, všech uživatelem definovaných tras a všech tras Protokolu BGP, které mohou existovat pro podsíť, ke které je síťové rozhraní přiřazeno. Další informace o tom, co se zobrazuje, najdete v [tématu Zobrazení efektivních tras](#view-effective-routes). Další informace o výchozích trasách Azure a uživatelem definovaných trasách najdete v [tématu Přehled směrování](virtual-networks-udr-overview.md).
Společná nastavení Správce prostředků Azure: Další informace o běžných nastaveních Správce prostředků Azure najdete v [tématu protokol aktivit](../azure-monitor/platform/platform-logs-overview.md), [řízení přístupu (IAM),](../role-based-access-control/overview.md) [značky](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [zámky](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)a [skript automatizace](../azure-resource-manager/templates/export-template-portal.md).

<a name="view-settings-commands"></a>**Příkazy**

Pokud je adresa IPv6 přiřazena k síťovému rozhraní, výstup prostředí PowerShell vrátí skutečnost, že je adresa přiřazena, ale nevrátí přiřazenou adresu. Podobně cli vrátí skutečnost, že adresa je přiřazena, ale vrátí *null* ve svém výstupu pro adresu.

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network nic seznam](/cli/azure/network/nic) pro zobrazení síťových rozhraní v předplatném; [Az network nic zobrazit](/cli/azure/network/nic) nastavení pro síťové rozhraní|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) pro zobrazení síťových rozhraní v nastavení předplatného nebo zobrazení pro síťové rozhraní|

## <a name="change-dns-servers"></a>Změna serverů DNS

Server DNS je serverem Azure DHCP přiřazen k síťovému rozhraní v operačním systému virtuálního počítače. Přiřazený server DNS je bez ohledu na nastavení serveru DNS pro síťové rozhraní. Další informace o nastavení překladu názvů pro síťové rozhraní najdete v [tématu Překlad názvů pro virtuální počítače](virtual-networks-name-resolution-for-vms-and-role-instances.md). Síťové rozhraní můžete dědit nastavení z virtuální sítě nebo použít vlastní jedinečné nastavení, které přepsat nastavení pro virtuální síť.

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *síťová rozhraní*. Když se ve výsledcích hledání zobrazí **síťová rozhraní,** vyberte je.
2. Ze seznamu vyberte síťové rozhraní, pro které chcete změnit server DNS.
3. V části **NASTAVENÍ**vyberte **servery DNS** .
4. Vyberte buď:
   - **Dědit z virtuální sítě**: Tuto možnost zvolte, chcete-li dědit nastavení serveru DNS definované pro virtuální síť, ke které je síťové rozhraní přiřazeno. Na úrovni virtuální sítě je definován vlastní server DNS nebo server DNS poskytovaný Azure. Server DNS poskytovaný Azure může přeložit názvy hostitelů pro prostředky přiřazené ke stejné virtuální síti. FQDN musí být použit k vyřešení pro prostředky přiřazené k různým virtuálním sítím.
   - **Vlastní**: Vlastní: Vlastní server DNS můžete nakonfigurovat tak, aby přecvál názvy ve více virtuálních sítích. Zadejte ADRESU IP serveru, který chcete použít jako server DNS. Zadaná adresa serveru DNS je přiřazena pouze tomuto síťovému rozhraní a přepíše všechna nastavení DNS pro virtuální síť, ke které je síťové mu přiřazeno.
     >[!Note]
     >Pokud virtuální počítač používá nic, která je součástí skupiny dostupnosti, všechny servery DNS, které jsou určené pro každý z virtuálních počítačů ze všech síťových aplikací, které jsou součástí skupiny dostupnosti, budou zděděny.
5. Vyberte **Uložit**.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az síť nic aktualizace](/cli/azure/network/nic)|
|PowerShell|[Rozhraní Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Povolení nebo zakázání předávání IP adres

Předávání IP adres umožňuje virtuálnímu počítači, ke kterému je připojeno síťové rozhraní:
- Příjem síťového provozu, který není určen pro jednu z adres IP přiřazených žádné konfiguraci protokolu IP přiřazené síťovému rozhraní.
- Odešlete síťový provoz s jinou zdrojovou adresou IP, než která je přiřazena jedné z konfigurací IP síťového rozhraní.

Nastavení musí být povoleno pro každé síťové rozhraní připojené k virtuálnímu počítači, který přijímá přenosy, které virtuální počítač potřebuje předat dál. Virtuální počítač může předávat přenosy bez ohledu na to, zda k němu je připojeno více síťových rozhraní nebo jedno síťové rozhraní. Zatímco předávání IP je nastavení Azure, virtuální počítač musí také spustit aplikaci schopnou předávat přenosy, jako je brána firewall, optimalizace sítě WAN a aplikace pro vyrovnávání zatížení. Když virtuální počítač běží síťové aplikace, virtuální počítač se často označuje jako virtuální síťové zařízení. Seznam síťových virtuálních zařízení připravených k nasazení můžete zobrazit na [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Předávání IP se obvykle používá s uživatelem definovanými trasami. Další informace o uživatelem definovaných trasách naleznete v [tématu Uživatelem definované trasy](virtual-networks-udr-overview.md).

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *síťová rozhraní*. Když se ve výsledcích hledání zobrazí **síťová rozhraní,** vyberte je.
2. Vyberte síťové rozhraní, pro které chcete povolit nebo zakázat předávání IP adres.
3. V části **NASTAVENÍ** **vyberte konfigurace IP** adres.
4. Chcete-li toto nastavení změnit, vyberte **Možnost Povoleno** nebo **Zakázáno** (výchozí nastavení).
5. Vyberte **Uložit**.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az síť nic aktualizace](/cli/azure/network/nic)|
|PowerShell|[Rozhraní Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Změnit přiřazení podsítě

Můžete změnit podsíť, ale ne virtuální síť, ke které je přiřazeno síťové rozhraní.

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *síťová rozhraní*. Když se ve výsledcích hledání zobrazí **síťová rozhraní,** vyberte je.
2. Vyberte síťové rozhraní, pro které chcete změnit přiřazení podsítě.
3. V části **NASTAVENÍ** **vyberte konfigurace protokolu IP** . Pokud všechny privátní IP adresy pro všechny uvedené konfigurace IP mají **(Statické)** vedle nich, je nutné změnit metodu přiřazení IP adresy dynamické provedením následujícíkroky. Všechny privátní adresy IP musí být přiřazeny pomocí metody dynamického přiřazení, aby bylo možné změnit přiřazení podsítě pro síťové rozhraní. Pokud jsou adresy přiřazeny dynamickou metodou, pokračujte krokem pět. Pokud jsou některé adresy IPv4 přiřazeny metodou statického přiřazení, proveďte následující kroky a změňte metodu přiřazení na dynamickou:
   - Ze seznamu konfigurací IP vyberte konfiguraci PROTOKOLU IP, pro kterou chcete změnit metodu přiřazení adresy IPv4.
   - Vyberte **dynamické** pro metodu **přiřazení** privátní IP adresy. Adresu IPv6 nelze přiřadit statickou metodou přiřazení.
   - Vyberte **Uložit**.
4. V rozevíracím seznamu **Podsíť** vyberte podsíť, do které chcete síťové rozhraní přesunout.
5. Vyberte **Uložit**. Nové dynamické adresy jsou přiřazeny z rozsahu adres podsítě pro novou podsíť. Po přiřazení síťového rozhraní k nové podsíti můžete přiřadit statickou adresu IPv4 z nového rozsahu adres podsítě, pokud se rozhodnete. Další informace o přidávání, změně a odebírání ADRES IP pro síťové rozhraní naleznete v [tématu Správa adres IP](virtual-network-network-interface-addresses.md).

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az síť nic ip-config aktualizace](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Přidání nebo odebrání ze skupin zabezpečení aplikací

Síťové rozhraní můžete přidat nebo odebrat ze skupiny zabezpečení aplikace pomocí portálu pouze v případě, že je síťové rozhraní připojeno k virtuálnímu počítači. Pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure můžete přidat síťové rozhraní nebo odebrat síťové rozhraní ze skupiny zabezpečení aplikace, ať už je síťové rozhraní připojené k virtuálnímu počítači nebo ne. Přečtěte si další informace o [skupinách zabezpečení aplikací](security-overview.md#application-security-groups) a o tom, jak [vytvořit skupinu zabezpečení aplikací](manage-network-security-group.md).

1. V poli *Hledat prostředky, služby a dokumenty* v horní části portálu začněte psát název virtuálního počítače, který má síťové rozhraní, které chcete přidat nebo odebrat ze skupiny zabezpečení aplikace. Až se ve výsledcích hledání zobrazí název virtuálního počítače, vyberte ho.
2. V části **NASTAVENÍ** vyberte **Sítě**.  Vyberte **Skupiny zabezpečení aplikací** a **potom Nakonfigurujte skupiny zabezpečení aplikací,** které volí skupiny zabezpečení aplikací, do kterých chcete přidat síťové rozhraní, nebo zrušte výběr skupin zabezpečení aplikací, ze kterých chcete síťové rozhraní odebrat, a pak vyberte **uložit**. Do stejné skupiny zabezpečení aplikace lze přidat pouze síťová rozhraní, která existují ve stejné virtuální síti. Skupina zabezpečení aplikace musí existovat ve stejném umístění jako síťové rozhraní.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az síť nic aktualizace](/cli/azure/network/nic)|
|PowerShell|[Rozhraní Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Přidružení nebo oddělení skupiny zabezpečení sítě

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *síťová rozhraní.* Když se ve výsledcích hledání zobrazí **síťová rozhraní,** vyberte je.
2. V seznamu, ke kterému chcete přidružit skupinu zabezpečení sítě, vyberte síťové rozhraní nebo od ní distancte.
3. V části **NASTAVENÍ**vyberte **položku Skupina zabezpečení sítě** .
4. Vyberte **Upravit**.
5. Vyberte **skupinu zabezpečení sítě** a pak vyberte skupinu zabezpečení sítě, kterou chcete přidružit k síťovému rozhraní, nebo vyberte **možnost Žádná**, chcete-li oddělit skupinu zabezpečení sítě.
6. Vyberte **Uložit**.

**Příkazy**

- Azure CLI: [aktualizace síťové sítě az](/cli/azure/network/nic#az-network-nic-update)
- Prostředí PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Odstranění síťového rozhraní

Síťové rozhraní můžete odstranit, pokud není připojené k virtuálnímu počítači. Pokud je síťové rozhraní připojené k virtuálnímu počítači, musíte nejprve umístit virtuální počítač do zastaveného (adlocated) stavu a potom odpojit síťové rozhraní od virtuálního počítače. Chcete-li odpojit síťové rozhraní od virtuálního počítače, proveďte kroky v [části Odpojit síťové rozhraní od virtuálního počítače](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Síťové rozhraní nelze odpojit od virtuálního počítače, pokud se jedná o jediné síťové rozhraní připojené k virtuálnímu počítači. Virtuální počítač musí mít vždy alespoň jedno síťové rozhraní, které je k němu připojeno. Odstraněním virtuálního počítače odpojíte všechna síťová rozhraní, která jsou k němu připojena, ale neodstraníte síťová rozhraní.

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *síťová rozhraní*. Když se ve výsledcích hledání zobrazí **síťová rozhraní,** vyberte je.
2. V seznamu vyberte síťové rozhraní, které chcete odstranit.
3. V části **Přehled** Vyberte **odstranit**.
4. Výběrem **možnosti Ano** potvrďte odstranění síťového rozhraní.

Když odstraníte síťové rozhraní, budou uvolněny všechny mac nebo IP adresy, které jsou mu přiřazeny.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az síť nic odstranit](/cli/azure/network/nic)|
|PowerShell|[Odebrat rozhraní AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Řešení problémů s připojením

Pokud se vám nedaří komunikovat s virtuálním počítačem nebo z ní, může být příčinou problému pravidla zabezpečení skupiny zabezpečení sítě nebo trasy účinné pro síťové rozhraní. K vyřešení problému můžete pomoci následující možnosti:

### <a name="view-effective-security-rules"></a>Zobrazení účinných pravidel zabezpečení

Účinná pravidla zabezpečení pro každé síťové rozhraní připojené k virtuálnímu počítači jsou kombinací pravidel, která jste vytvořili ve skupině zabezpečení sítě, a [výchozích pravidel zabezpečení](security-overview.md#default-security-rules). Principy efektivních pravidel zabezpečení pro síťové rozhraní vám mohou pomoci určit, proč nemůžete komunikovat s virtuálním počítačem nebo z ní. Můžete zobrazit účinná pravidla pro libovolné síťové rozhraní, které je připojeno ke spuštěnému virtuálnímu počítači.

1. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, pro který chcete zobrazit platná pravidla zabezpečení. Pokud neznáte název virtuálního počítače, zadejte *virtuální počítače* do vyhledávacího pole. Když se **virtuální počítače** zobrazí ve výsledcích hledání, vyberte ho a pak vyberte virtuální počítač ze seznamu.
2. V části **NASTAVENÍ**vyberte **Možnost Síť** .
3. Vyberte název síťového rozhraní.
4. V části **PODPORA + ODSTRAŇOVÁNÍ PROBLÉMŮ**vyberte možnost Platná pravidla **zabezpečení** .
5. Zkontrolujte seznam účinných pravidel zabezpečení a zjistěte, zda pro požadovanou příchozí a odchozí komunikaci existují správná pravidla. Další informace o tom, co vidíte v seznamu, najdete v [přehledu skupiny zabezpečení sítě](security-overview.md).

Funkce ověření toku IP služby Azure Network Watcher vám může také pomoct určit, pokud pravidla zabezpečení brání komunikaci mezi virtuálním počítačem a koncovým bodem. Další informace naleznete v tématu [ověření toku IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Příkazy**

- Azure CLI: [az network nic seznam-efektivní-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- Prostředí PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Zobrazení efektivních tras

Efektivní trasy pro síťová rozhraní připojená k virtuálnímu počítači jsou kombinací výchozích tras, všech tras, které jste vytvořili, a všech tras šířených z místních sítí prostřednictvím protokolu BGP prostřednictvím brány virtuální sítě Azure. Pochopení efektivní trasy pro síťové rozhraní vám může pomoci určit, proč nejste schopni komunikovat do nebo z virtuálního počítače. Můžete zobrazit efektivní trasy pro libovolné síťové rozhraní, které je připojeno ke spuštěnému virtuálnímu počítači.

1. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, pro který chcete zobrazit platná pravidla zabezpečení. Pokud neznáte název virtuálního počítače, zadejte *virtuální počítače* do vyhledávacího pole. Když se **virtuální počítače** zobrazí ve výsledcích hledání, vyberte ho a pak vyberte virtuální počítač ze seznamu.
2. V části **NASTAVENÍ**vyberte **Možnost Síť** .
3. Vyberte název síťového rozhraní.
4. V části **PODPORA + ODSTRAŇOVÁNÍ PROBLÉMŮ**vyberte možnost Efektivní **trasy** .
5. Zkontrolujte seznam efektivních tras a zjistěte, zda pro požadovanou příchozí a odchozí komunikaci existují správné trasy. Další informace o tom, co vidíte v seznamu, najdete v [přehledu směrování](virtual-networks-udr-overview.md).

Další funkce směrování služby Azure Network Watcher vám může také pomoct určit, jestli trasy brání komunikaci mezi virtuálním počítačem a koncovým bodem. Další informace naleznete v tématu [Další směrování](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Příkazy**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- Prostředí PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Oprávnění

Chcete-li provádět úkoly v síťových rozhraních, musí být váš účet přiřazen k roli [síťového přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, které jsou přiřazena příslušná oprávnění uvedená v následující tabulce:

| Akce                                                                     | Name (Název)                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Získat síťové rozhraní                                     |
| Microsoft.Network/networkInterfaces/write                                  | Vytvoření nebo aktualizace síťového rozhraní                        |
| Microsoft.Network/networkInterfaces/join/action                            | Připojení síťového rozhraní k virtuálnímu počítači           |
| Microsoft.Network/networkInterface/delete                                 | Odstranit síťové rozhraní                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Připojte prostředek k síťovému rozhraní přes Servi ...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Získání efektivní hospo-               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Získání efektivních skupin zabezpečení síťového rozhraní           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Získání vyrovnávání zatížení síťového rozhraní                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Získat přidružení služeb                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Vytvoření nebo aktualizace přidružení služby                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Odstranit přidružení služby                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Ověřit přidružení služby                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Získání konfigurace IP adresy síťového rozhraní                    |

## <a name="next-steps"></a>Další kroky

- Vytvoření virtuálního počítače s více síťovémi síťové karty pomocí [azure cli](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [PowerShellu](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Vytvoření jednoho virtuálního počítače pro nenosné konto s více adresami IPv4 pomocí [azure cli](virtual-network-multiple-ip-addresses-cli.md) nebo [PowerShellu](virtual-network-multiple-ip-addresses-powershell.md)
- Vytvoření jednoho virtuálního počítače pro vlastní virtuální sítě s privátní adresou IPv6 (za azure balancer) pomocí šablony [Azure CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)nebo [Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Vytvoření síťového rozhraní pomocí ukázkových skriptů [PowerShellu](powershell-samples.md) nebo [Azure CLI](cli-samples.md) nebo pomocí šablony Azure [Resource Manager](template-samples.md)
- Vytvoření a použití [zásad Azure](policy-samples.md) pro virtuální sítě
