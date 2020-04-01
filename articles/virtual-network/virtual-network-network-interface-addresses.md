---
title: Konfigurace IP adres pro síťové rozhraní Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přidat, změnit a odebrat privátní a veřejné IP adresy pro síťové rozhraní.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: b5655a58c3538ac47e8649619b079dc46ee01242
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473219"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Přidání, změna nebo odebrání IP adres pro síťové rozhraní Azure

Přečtěte si, jak přidat, změnit a odebrat veřejné a soukromé IP adresy pro síťové rozhraní. Privátní IP adresy přiřazené k síťovému rozhraní umožňují virtuálnímu počítači komunikovat s jinými prostředky ve virtuální síti Azure a připojených sítích. Privátní IP adresa také umožňuje odchozí komunikaci do Internetu pomocí nepředvídatelné IP adresy. [Veřejná IP adresa](virtual-network-public-ip-address.md) přiřazená síťovému rozhraní umožňuje příchozí komunikaci s virtuálním počítačem z Internetu. Adresa také umožňuje odchozí komunikaci z virtuálního počítače do Internetu pomocí předvídatelné IP adresy. Podrobnosti najdete [v tématu Principy odchozích připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pokud potřebujete vytvořit, změnit nebo odstranit síťové rozhraní, přečtěte si článek [Správa síťového rozhraní.](virtual-network-network-interface.md) Pokud potřebujete přidat síťová rozhraní nebo odebrat síťová rozhraní z virtuálního počítače, přečtěte si článek [Přidat nebo odebrat síťová rozhraní.](virtual-network-network-interface-vm.md)

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v libovolné části tohoto článku proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud pomocí příkazů Prostředí PowerShell k dokončení úloh v tomto článku, buď spustit příkazy v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud pomocí příkazů rozhraní Příkazového řádku Azure (CLI) k dokončení úloh v tomto článku, buď spustit příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také `az login` spustit k vytvoření připojení s Azure.

Účet, ke kterému se přihlásíte nebo se s ním připojíte, musí být přiřazen k roli [síťového přispěvatele](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v [oprávněních síťového rozhraní](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Přidání IP adres

Můžete přidat tolik [privátní](#private) a [veřejné](#public) [IPv4](#ipv4) adresy podle potřeby do síťového rozhraní, v rámci omezení uvedených v článku omezení [Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) Privátní adresu IPv6 můžete přidat do jedné [sekundární konfigurace protokolu IP](#secondary) (pokud neexistují žádné sekundární konfigurace PROTOKOLU IP) pro existující síťové rozhraní. Každé síťové rozhraní může mít maximálně jednu privátní adresu IPv6. Volitelně můžete přidat veřejnou adresu IPv6 do konfigurace síťového rozhraní IPv6. Podrobnosti o používání adres IPv6 najdete v [tématu IPv6.](#ipv6)

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *síťová rozhraní*. Když se ve výsledcích hledání zobrazí **síťová rozhraní,** vyberte je.
2. Ze seznamu vyberte síťové rozhraní, pro které chcete přidat adresu IPv4.
3. V části **NASTAVENÍ**vyberte **konfigurace IP**.
4. V části **Konfigurace IP**vyberte **+ Přidat**.
5. Zadejte následující a pak vyberte **OK**:

   |Nastavení|Povinné?|Podrobnosti|
   |---|---|---|
   |Name (Název)|Ano|Musí být jedinečný pro síťové rozhraní|
   |Typ|Ano|Vzhledem k tomu, že přidáváte konfiguraci protokolu IP do existujícího síťového rozhraní a každé síťové rozhraní musí mít [primární](#primary) konfiguraci PROTOKOLU IP, je jedinou možností **sekundární**.|
   |Metoda přiřazení privátní IP adresy|Ano|[**Dynamické**](#dynamic): Azure přiřadí další dostupnou adresu pro rozsah adres podsítě, ve které se síťové rozhraní nasazuje. [**Statické**](#static): Přiřadíte nepoužitou adresu pro rozsah adres podsítě, ve které je síťové rozhraní nasazeno.|
   |Veřejná IP adresa|Ne|**Zakázáno:** Ke konfiguraci IP není aktuálně přidružen žádný prostředek veřejné IP adresy. **Povoleno:** Vyberte existující veřejnou IP adresu IPv4 nebo vytvořte novou. Chcete-li se dozvědět, jak vytvořit veřejnou IP adresu, přečtěte si článek [Veřejné IP adresy.](virtual-network-public-ip-address.md#create-a-public-ip-address)|
6. Ručně přidejte sekundární privátní IP adresy do operačního systému virtuálního počítače vyplněním pokynů v článku [Přiřadit více IP adres operačním u operačních systémů virtuálních počítačů.](virtual-network-multiple-ip-addresses-portal.md#os-config) Před ručním přidáním IP adres do operačního systému virtuálního počítače najdete [soukromé](#private) IP adresy. Do operačního systému virtuálního počítače nepřidávejte žádné veřejné IP adresy.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Doplněk AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Změna nastavení adresy IP

Možná budete muset změnit metodu přiřazení adresy IPv4, změnit statickou adresu IPv4 nebo změnit veřejnou IP adresu přiřazenou síťovému rozhraní. Pokud měníte privátní adresu IPv4 sekundární konfigurace IP přidružené k sekundárnímu síťovému rozhraní ve virtuálním počítači (další informace o [primárních a sekundárních síťových rozhraních),](virtual-network-network-interface-vm.md)umístěte virtuální počítač do zastaveného (přiděleného) stavu před dokončením následujících kroků:

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *síťová rozhraní*. Když se ve výsledcích hledání zobrazí **síťová rozhraní,** vyberte je.
2. V seznamu vyberte síťové rozhraní, pro které chcete zobrazit nebo změnit nastavení adresy IP.
3. V části **NASTAVENÍ**vyberte **konfigurace IP**.
4. Ze seznamu vyberte konfiguraci protokolu IP, kterou chcete upravit.
5. Změňte nastavení podle potřeby pomocí informací o nastavení v kroku 5 [přidání konfigurace IP](#add-ip-addresses).
6. Vyberte **Uložit**.

>[!NOTE]
>Pokud má primární síťové rozhraní více konfigurací IP adres a změníte privátní IP adresu primární konfigurace IP, je nutné ručně znovu přiřadit primární a sekundární IP adresy k síťovému rozhraní v systému Windows (není vyžadováno pro Linux). Informace o ručním přiřazení adres IP k síťovému rozhraní v operačním systému naleznete v tématu [Přiřazení více adres IP virtuálním počítačům](virtual-network-multiple-ip-addresses-portal.md#os-config). Před ručním přidáním IP adres do operačního systému virtuálního počítače najdete v tématu [Privátní](#private) IP adresy. Do operačního systému virtuálního počítače nepřidávejte žádné veřejné IP adresy.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az síť nic ip-config aktualizace](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Odebrání adres IP

[Soukromé](#private) a [veřejné](#public) IP adresy můžete odebrat ze síťového rozhraní, ale síťové rozhraní musí mít vždy přiřazenou alespoň jednu privátní adresu IPv4.

1. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *síťová rozhraní*. Když se ve výsledcích hledání zobrazí **síťová rozhraní,** vyberte je.
2. Vyberte síťové rozhraní, které chcete ze seznamu odebrat adresy IP.
3. V části **NASTAVENÍ**vyberte **konfigurace IP**.
4. Vyberte [sekundární](#secondary) konfiguraci IP (nelze odstranit [primární](#primary) konfiguraci), kterou chcete odstranit, vyberte **odstranit**a pak klepnutím na tlačítko **Ano**, potvrzení odstranění. Pokud konfigurace měla přiřazen prostředek veřejné IP adresy, je prostředek oddělen od konfigurace IP, ale prostředek není odstraněn.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az síť nic ip-config odstranit](/cli/azure/network/nic/ip-config)|
|PowerShell|[Odebrat-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Konfigurace IP adres

[Soukromé](#private) a (volitelně) [veřejné](#public) IP adresy jsou přiřazeny k jedné nebo více konfiguracím IP přiřazených k síťovému rozhraní. Existují dva typy konfigurací IP adres:

### <a name="primary"></a>Primární

Každému síťovému rozhraní je přiřazena jedna primární konfigurace PROTOKOLU IP. Primární konfigurace IP adresy:

- Má [přiřazenu soukromou](#private) adresu [IPv4.](#ipv4) K primární konfiguraci PROTOKOLU IP nelze přiřadit soukromou adresu [IPv6.](#ipv6)
- Může mít také [přiřazenu veřejnou](#public) adresu IPv4. Veřejnou adresu IPv6 nelze přiřadit k primární konfiguraci protokolu IP (IPv4). 

### <a name="secondary"></a>Sekundární

Kromě primární konfigurace PROTOKOLU IP může být v síťovém rozhraní přiřazena nulová nebo více sekundárních konfigurací PROTOKOLU IP. Sekundární konfigurace IP:

- Musí mít přiřazenou soukromou adresu IPv4 nebo IPv6. Pokud je adresa IPv6, síťové rozhraní může mít pouze jednu sekundární konfiguraci IP. Pokud je adresa IPv4, síťové rozhraní může mít přiřazeno více sekundárních konfigurací IP adres. Další informace o tom, kolik privátní ch a veřejných adres IPv4 lze přiřadit k síťovému rozhraní, najdete v článku [omezení Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)
- Může mít také přiřazenu veřejnou adresu IPv4 nebo IPv6. Přiřazení více adres IPv4 k síťovému rozhraní je užitečné ve scénářích, jako jsou:
  - Hostování více webů nebo služeb s různými IP adresami a certifikáty TLS/SSL na jednom serveru.
  - Virtuální počítač sloužící jako síťové virtuální zařízení, jako je brána firewall nebo nástroj pro vyrovnávání zatížení.
  - Možnost přidat libovolnou privátní adresy IPv4 pro libovolné síťové rozhraní do back-endového fondu Azure Load Balancer. V minulosti bylo možné do back-endového fondu přidat pouze primární adresu IPv4 pro primární síťové rozhraní. Další informace o tom, jak vyvážit zatížení více konfigurací IPv4, najdete v článku [Vyrovnávání zatížení více konfigurací IP.](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
  - Možnost vyrovnávání zatížení jednu adresu IPv6 přiřazenou síťovému rozhraní. Další informace o tom, jak vyvážit zatížení na soukromou adresu IPv6, najdete v článku [Adresa IPv6 vyrovnávání zatížení.](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="address-types"></a>Typy adres

[Konfiguraci PROTOKOLU IP](#ip-configurations)můžete přiřadit následující typy adres IP :

### <a name="private"></a>Private

[Privátní adresy IPv4](#ipv4) nebo IPv6 umožňují virtuálnímu počítači komunikovat s jinými prostředky ve virtuální síti nebo v jiných připojených sítích. 

Ve výchozím nastavení přiřazují servery DHCP Azure privátní adresu IPv4 pro [primární konfiguraci IP sítě](#primary) Azure síťovému rozhraní Azure síťovému rozhraní v rámci operačního systému virtuálního počítače. Pokud to není nutné, nikdy byste neměli ručně nastavit IP adresu síťového rozhraní v operačním systému virtuálního počítače.

> [!WARNING]
> Pokud se adresa IPv4 nastavená jako primární IP adresa síťového rozhraní v rámci operačního systému virtuálního počítače někdy liší od privátní adresy IPv4 přiřazené k primární konfiguraci IP primárního síťového rozhraní připojeného k virtuálnímu počítači v rámci Azure, ztratíte připojení k virtuálnímu počítači.

Existují scénáře, kde je nutné ručně nastavit IP adresu síťového rozhraní v rámci operačního systému virtuálního počítače. Například je nutné ručně nastavit primární a sekundární IP adresy operačního systému Windows při přidávání více IP adres do virtuálního počítače Azure. U virtuálního počítače s Linuxem může být nutné ručně nastavit sekundární ADRESY IP. Podrobnosti najdete [v tématu Přidání IP adres do operačního systému virtuálního počítačů.](virtual-network-multiple-ip-addresses-portal.md#os-config) Pokud budete někdy potřebovat změnit adresu přiřazenou konfiguraci IP adresy, doporučujeme:

1. Ujistěte se, že virtuální počítač přijímá adresu ze serverů Azure DHCP. Jakmile budete mít, změňte přiřazení IP adresy zpět na DHCP v rámci operačního systému a restartujte virtuální počítač.
2. Zastavit (navrátit) virtuální počítač.
3. Změňte IP adresu pro konfiguraci IP adres v Azure.
4. Spusťte virtuální počítač.
5. [Ručně nakonfigurujte](virtual-network-multiple-ip-addresses-portal.md#os-config) sekundární IP adresy v rámci operačního systému (a také primární IP adresu v systému Windows) tak, aby odpovídaly tomu, co jste nastavili v Azure.

Podle předchozích kroků privátní IP adresa přiřazená síťovému rozhraní v rámci Azure a v rámci operačního systému virtuálního počítače zůstává stejná. Pokud chcete mít přehled o tom, pro které virtuální počítače v rámci předplatného jste ručně nastavili IP adresy v rámci operačního systému, zvažte přidání [značky](../azure-resource-manager/management/tag-resources.md) Azure do virtuálních počítačů. Můžete například použít "Přiřazení IP adresy: Statické". Tímto způsobem můžete snadno najít virtuální počítače v rámci předplatného, které jste ručně nastavit IP adresu v rámci operačního systému.

Kromě povolení virtuálního počítače ke komunikaci s jinými prostředky v rámci stejné nebo připojené virtuální sítě, privátní IP adresa také umožňuje virtuální mu komunikovat odchozí k Internetu. Odchozí připojení jsou zdrojová síťová adresa přeložená Azure na nepředvídatelnou veřejnou IP adresu. Další informace o odchozím připojení k Internetu azure, přečtěte si článek [odchozí připojení k Internetu Azure.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Příchozí adresu virtuálního počítače nelze z Internetu komunikovat. Pokud odchozí připojení vyžadují předvídatelnou veřejnou IP adresu, přidružte prostředek veřejné IP adresy k síťovému rozhraní.

### <a name="public"></a>Public

Veřejné IP adresy přiřazené prostřednictvím prostředku veřejné IP adresy umožňují příchozí připojení k virtuálnímu počítači z Internetu. Odchozí připojení k Internetu používají předvídatelnou adresu IP. Podrobnosti [najdete v tématu Principy odchozích připojení v Azure.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ke konfiguraci IP adresy můžete přiřadit veřejnou IP adresu, ale není to nutné. Pokud virtuálnímu počítači nepřiřadíte veřejnou IP adresu tak, že přizpůsobujete prostředek veřejné IP adresy, může virtuální počítač stále komunikovat odchozí do Internetu. V tomto případě je privátní IP adresa zdrojová síťová adresa přeložená Azure na nepředvídatelnou veřejnou IP adresu. Další informace o veřejných prostředcích IP adres naleznete v [tématu Veřejný prostředek ip adresy](virtual-network-public-ip-address.md).

Počet privátních a veřejných ADRES IP, které lze přiřadit k síťovému rozhraní, je tento počet limitů. Podrobnosti načtete článek [Omezení Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

> [!NOTE]
> Azure překládá privátní IP adresu virtuálního počítače na veřejnou IP adresu. V důsledku toho operační systém virtuálního počítače neví o žádné veřejné IP adrese, která je mu přiřazena, takže není třeba někdy ručně přiřadit veřejnou IP adresu v rámci operačního systému.

## <a name="assignment-methods"></a>Metody přiřazení

Veřejné a soukromé IP adresy jsou přiřazovány jednou z následujících metod přiřazení:

### <a name="dynamic"></a>Dynamická

Dynamické soukromé adresy IPv4 a IPv6 (volitelně) jsou přiřazeny ve výchozím nastavení.

- **Pouze veřejné**: Azure přiřadí adresu z rozsahu jedinečné ho každý region Azure. Informace o tom, které oblasti jsou přiřazeny jednotlivým oblastem, najdete [v tématu Rozsahy IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adresa se může změnit, když je virtuální počítač zastaven (napravený) a pak se znovu spustí. Veřejnou adresu IPv6 nelze přiřadit ke konfiguraci protokolu IP pomocí obou metod přiřazení.
- **Pouze soukromé**: Azure rezervuje první čtyři adresy v každém rozsahu adres podsítě a nepřiřazuje adresy. Azure přiřadí prostředku další dostupnou adresu z rozsahu adres podsítě. Pokud je například rozsah adres podsítě 10.0.0.0/16 a adresy 10.0.0.0.4–10.0.0.14 už jsou přiřazené (.0–.3 jsou vyhrazené), Azure prostředku přiřadí adresu 10.0.0.15. Dynamická metoda přidělování je výchozí metoda. Jakmile jsou dynamické IP adresy přiřazené, uvolní se pouze v případě odstranění síťového rozhraní, jeho přiřazení k jiné podsíti ve stejné virtuální síti nebo změně metody přidělování na statickou a zadání jiné IP adresy. Když změníte metodu přidělování z dynamické na statickou, Azure ve výchozím nastavení jako statickou IP adresu přiřadí dříve dynamicky přiřazenou adresu. 

### <a name="static"></a>Statická

Ke konfiguraci PROTOKOLU IP můžete (volitelně) přiřadit veřejnou nebo soukromou statickou adresu IPv4 nebo IPv6. Další informace o tom, jak Azure přiřazuje statické veřejné adresy IPv4, najdete [v tématu Veřejné IP adresy](virtual-network-public-ip-address.md).

- **Pouze veřejné**: Azure přiřadí adresu z rozsahu jedinečné ho každý region Azure. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064). Adresa se nezmění, dokud nebude odstraněn prostředek veřejné IP adresy, ke které je přiřazen, nebo dokud se metoda přiřazení nezmění na dynamická. Pokud je prostředek veřejné IP adresy přidružen ke konfiguraci IP adresy, musí být před změnou metody přiřazení oddělen od konfigurace PROTOKOLU IP.
- **Pouze soukromé**: Vyberete a přiřadíte adresu z rozsahu adres podsítě. Adresa, kterou přiřadíte, může být jakákoli adresa v rozsahu adres podsítě kromě prvních čtyř adres, která aktuálně není přiřazená k žádnému jinému prostředku v této podsíti. Statické adresy se uvolní pouze v případě odstranění síťového rozhraní. Pokud změníte metodu přidělení na statickou, Azure dynamicky přiřadí dříve přiřazenou dynamickou IP adresu jako statickou adresu, i když adresa není další dostupná adresa v rozsahu adres podsítě. Adresa se změní také v případě přiřazení síťového rozhraní k jiné podsíti ve stejné virtuální síti. Pokud však chcete síťové rozhraní přiřadit k jiné podsíti, musíte nejprve změnit metodu přidělování ze statické na dynamickou. Jakmile přiřadíte síťové rozhraní k jiné podsíti, můžete metodu přidělování změnit zpět na statickou a přiřadit IP adresu z rozsahu adres nové podsítě.

## <a name="ip-address-versions"></a>Verze IP adres

Při přiřazování adres můžete zadat následující verze:

### <a name="ipv4"></a>IPv4

Každé síťové rozhraní musí mít jednu [primární](#primary) konfiguraci IP adresy s přiřazenou [privátní](#private) adresou [IPv4.](#ipv4) Můžete přidat jednu nebo více [sekundárních](#secondary) konfigurací IP adres, z nichž každá má privátní protokol IPv4 a (volitelně) [veřejnou](#public) IP adresu IPv4.

### <a name="ipv6"></a>IPv6

Jedné sekundární konfiguraci protokolu IP síťového rozhraní můžete přiřadit nulovou nebo jednu soukromou adresu [IPv6.](#ipv6) Síťové rozhraní nemůže mít žádné existující sekundární konfigurace IP adres. Každé síťové rozhraní může mít maximálně jednu privátní adresu IPv6. Volitelně můžete přidat veřejnou adresu IPv6 do konfigurace síťového rozhraní IPv6.

> [!NOTE]
> I když můžete vytvořit síťové rozhraní s adresou IPv6 pomocí portálu, nelze přidat existující síťové rozhraní do nového nebo existujícího virtuálního počítače pomocí portálu. Pomocí Prostředí PowerShell nebo rozhraní příkazového řádku Azure vytvořte síťové rozhraní s privátní adresou IPv6 a při vytváření virtuálního počítače připojte síťové rozhraní. Síťové rozhraní nelze připojit k privátní adrese IPv6, která je k němu přiřazena k existujícímu virtuálnímu počítači. Privátní adresu IPv6 nelze přidat do konfigurace protokolu IP pro libovolné síťové rozhraní připojené k virtuálnímu počítači pomocí nástrojů (portál, rozhraní CLI nebo prostředí PowerShell).

Veřejnou adresu IPv6 nelze přiřadit k primární nebo sekundární konfiguraci IP adresy.

## <a name="skus"></a>SKU

Veřejná IP adresa je vytvořena se základní nebo standardní skladovou položkou. Další informace o rozdílech skladových položk naleznete v [tématu Správa veřejných IP adres](virtual-network-public-ip-address.md).

> [!NOTE]
> Při přiřazování veřejné IP adresy standardní SKU k síťovému rozhraní virtuálního počítače je potřeba explicitně povolit plánovaný provoz pomocí [skupiny zabezpečení sítě](security-overview.md#network-security-groups). Komunikace s prostředkem nebude možná, dokud nevytvoříte a nepřiřadíte skupinu zabezpečení sítě a explicitně nepovolíte požadovaný provoz.

## <a name="next-steps"></a>Další kroky
Chcete-li vytvořit virtuální počítač s různými konfiguracemi IP adres, přečtěte si následující články:

|Úkol|Nástroj|
|---|---|
|Vytvoření virtuálního počítače s několika síťovými rozhraními|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Vytvoření jednoho virtuálního virtuálního počítače nic s více adresami IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Vytvoření jednoho virtuálního počítače nic s privátní adresou IPv6 (za Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [šablona Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
