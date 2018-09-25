---
title: Konfigurace IP adres pro rozhraní sítě Azure | Dokumentace Microsoftu
description: Zjistěte, jak přidat, změnit a Odstranit privátní a veřejné IP adresy pro síťové rozhraní.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 3a74450ca8025f07b00dc18c9b81b147afa7439c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975294"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Přidání, změna nebo odebrání IP adres pro rozhraní sítě Azure

Zjistěte, jak přidat, změnit a odstranit veřejných a privátních IP adres pro síťové rozhraní. Privátní IP adresy přiřazené k síťovému rozhraní umožňuje virtuálnímu počítači komunikovat s ostatními prostředky ve virtuální síti Azure a připojených sítích. Privátní IP adresu, taky umožňuje odchozí komunikaci na Internetu pomocí nepředvídatelné IP adresu. A [veřejnou IP adresu](virtual-network-public-ip-address.md) přiřazené na síťové rozhraní umožňuje příchozí komunikaci k virtuálnímu počítači z Internetu. Adresa taky umožňuje odchozí komunikaci z virtuálního počítače k Internetu pomocí předvídatelné IP adresy. Podrobnosti najdete v tématu [Principy odchozích připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pokud potřebujete vytvořit, změnit, nebo odstranit síťové rozhraní, přečtěte si [Správa síťového rozhraní](virtual-network-network-interface.md) článku. Pokud je potřeba přidat síťová rozhraní k nebo odebrání síťových rozhraní virtuálního počítače, přečtěte si [přidání nebo odebrání síťových rozhraní](virtual-network-network-interface-vm.md) článku.

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v jakékoli části tohoto článku, proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud používáte příkazy prostředí PowerShell k dokončení úkolů v tomto článku, buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje použití Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.

Účet přihlásit nebo připojit k Azure, musíte být přiřazeni k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [sítě rozhraní oprávnění](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Přidejte IP adresy

Můžete přidat tolik [privátní](#private) a [veřejné](#public) [IPv4](#ipv4) adresy tak, aby síťové rozhraní, omezeními uvedenými v [omezeníchAzure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) článku. Přidat adresu IPv6 na existující síťové rozhraní (v případě, že na portálu můžete použít při vytváření rozhraní sítě přidáte k síťovému rozhraní s privátní IPv6 adresou) nelze použít na portálu. Můžete použít PowerShell nebo rozhraní příkazového řádku s privátní IPv6 adresou přidat do jednoho [sekundární konfigurace IP adresy](#secondary) (za předpokladu, nejsou žádné stávající sekundární konfigurace IP) pro síťové rozhraní, který není připojen k virtuálnímu počítači. Chcete-li přidat veřejnou IPv6 adresu k síťovému rozhraní nelze použít libovolný nástroj. Zobrazit [IPv6](#ipv6) podrobné informace o použití IPv6 adresy.

1. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *síťová rozhraní*. Když **síťová rozhraní** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte síťové rozhraní, které chcete přidat adresu IPv4 pro ze seznamu.
3. V části **nastavení**vyberte **konfigurací protokolu IP**.
4. V části **konfigurací protokolu IP**vyberte **+ přidat**.
5. Zadejte následující a potom vyberte **OK**:

    |Nastavení|Povinné?|Podrobnosti|
    |---|---|---|
    |Název|Ano|Musí být jedinečný pro síťové rozhraní|
    |Typ|Ano|Protože přidáváte do stávající síťové rozhraní konfigurace protokolu IP a musí mít každé síťové rozhraní [primární](#primary) je vaší jedinou možností konfigurace protokolu IP, **sekundární**.|
    |Metoda přiřazení privátní IP adresy|Ano|[**Dynamické**](#dynamic): Azure přiřadí další dostupnou adresu pro síťové rozhraní je nasazena v rozsahu adres podsítě. [**Statické**](#static): přiřadit nevyužité adresu pro síťové rozhraní je nasazena v rozsahu adres podsítě.|
    |Veřejná IP adresa|Ne|**Zakázáno:** žádný prostředek veřejné IP adresy je aktuálně přidružená ke konfiguraci IP adresy. **Povoleno:** vyberte stávající veřejnou IP adresu IPv4 adresu, nebo vytvořte novou. Zjistěte, jak vytvořit veřejnou IP adresu, přečtěte si [veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address) článku.|
6. Ručně přidat sekundární privátní IP adresy v operačním systému virtuálního počítače pomocí pokynů [přiřadit několik IP adres pro virtuální počítač operační systémy](virtual-network-multiple-ip-addresses-portal.md#os-config) článku. Zobrazit [privátní](#private) IP adres obsahuje důležité informace před ručním přidání IP adres do operačního systému virtuálního počítače. Nepřidávejte žádné veřejné IP adresy do operačního systému virtuálního počítače.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Změnit nastavení IP adresy

Můžete třeba změnit metodu přiřazování adresy IPv4 změnit statickou IPv4 adresu, nebo změna veřejné IP adresy přiřazené k síťovému rozhraní. Pokud chcete změnit privátní IPv4 adresu sekundární konfigurace IP adresy přidružené k sekundární síťové rozhraní ve virtuálním počítači (Další informace o [primární a sekundární síťová rozhraní](virtual-network-network-interface-vm.md)), umístěte virtuální počítač do zastaveného (uvolněného) stavu před provedením následujících kroků:

1. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *síťová rozhraní*. Když **síťová rozhraní** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte síťové rozhraní, které chcete zobrazit nebo změnit nastavení IP adresy pro ze seznamu.
3. V části **nastavení**vyberte **konfigurací protokolu IP**.
4. Vyberte konfigurace IP adresy, kterou chcete upravit ze seznamu.
5. Změňte nastavení podle potřeby, pomocí informací o nastavení v kroku 5 části [Přidat konfiguraci IP](#add-ip-addresses).
6. Vyberte **Uložit**.

>[!NOTE]
>Pokud se primární síťové rozhraní má více konfigurací protokolu IP a změnit privátní IP adresu primární konfigurace protokolu IP, musí ručně změnit přiřazení primárních a sekundárních IP adresy k síťovému rozhraní ve Windows (není vyžadované pro Linux) . Chcete-li přiřadit ručně IP adresy k síťovému rozhraní v rámci operačního systému, přečtěte si téma [přiřadit několik IP adres k virtuálním počítačům](virtual-network-multiple-ip-addresses-portal.md#os-config). Zvláštní upozornění před ručním přidání IP adres do operačního systému virtuálního počítače, naleznete v tématu [privátní](#private) IP adresy. Nepřidávejte žádné veřejné IP adresy do operačního systému virtuálního počítače.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ network nic ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Odebrání IP adres

Můžete odebrat [privátní](#private) a [veřejné](#public) IP adresy k síťovému rozhraní, ale síťové rozhraní musí mít vždy alespoň jeden privátní IPv4 adresu přiřazenou k němu.

1. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *síťová rozhraní*. Když **síťová rozhraní** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte síťové rozhraní, které chcete odebrat ze seznamu IP adres.
3. V části **nastavení**vyberte **konfigurací protokolu IP**.
4. Stisknutém pravém tlačítku vyberte [sekundární](#secondary) konfigurace IP adresy (nejde odstranit [primární](#primary) konfigurace), který chcete odstranit, vyberte **odstranit**a pak vyberte  **Ano**, potvrďte odstranění. Pokud prostředek veřejné IP adresy přidružené k jeho konfiguraci, prostředek je oddělen od konfigurace protokolu IP, ale neodstraní prostředek.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ network nic ip-config delete](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Konfigurace protokolu IP

[Privátní](#private) a (volitelně) [veřejné](#public) IP adresy jsou přiřazeny k jedné nebo víc konfigurací protokolu IP, které jsou přiřazené k síťovému rozhraní. Existují dva typy konfigurací protokolu IP:

### <a name="primary"></a>Primární

Každé síťové rozhraní se přiřadí jednu primární konfiguraci protokolu IP. Primární konfiguraci protokolu IP:

- Má [privátní](#private) [IPv4](#ipv4) přiřazenou adresu. Nelze přiřadit privátní [IPv6](#ipv6) primární konfigurace IP adresy.
- Mohou mít i [veřejné](#public) IPv4 adresu přiřazenou k němu. Nelze přiřadit veřejnou IPv6 adresu ke primární nebo sekundární konfiguraci IP adresy. Ale můžete přiřadit veřejnou IPv6 adresu služby Azure load balancer, která můžete vyrovnávat zatížení provozu na virtuální počítač privátní IPv6 adresou. Další informace najdete v tématu [podrobnosti a omezení pro protokol IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Sekundární

Kromě primární konfiguraci protokolu IP síťového rozhraní může mít nula nebo více sekundární konfigurace IP přiřazené. Sekundární konfigurace IP adresy:

- Musí mít privátní adresa IPv4 nebo IPv6 přiřazené k němu. Pokud je adresa protokolu IPv6, síťové rozhraní může mít pouze jednu sekundární konfigurace IP adresy. Pokud je adresa protokolu IPv4, může mít síťové rozhraní více sekundární konfigurace IP přiřazené. Další informace o kolik privátních a veřejných IPv4 adres je přiřadit k síťovému rozhraní, najdete v článku [omezeních Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) článku.
- Může také mít veřejnou IPv4 adresu přiřazenou Pokud je privátní IP adresu IPv4. Pokud privátní IP adresu protokolu IPv6, nejde přiřadit veřejnou adresu IPv4 nebo IPv6 ke konfiguraci IP adresy. Přiřazení více IP adres síťového rozhraní je užitečné v situacích, jako:
    - Hostovat několik webů nebo služeb s různými IP adresami a certifikáty SSL na jednom serveru.
    - Virtuální počítač, který slouží jako síťové virtuální zařízení, třeba brány firewall nebo službu Vyrovnávání zatížení.
    - Možnost a přidejte některý z privátních adres IPv4 pro některý z síťová rozhraní back endového fondu nástroje pro vyrovnávání zatížení Azure. V minulosti jenom primární IPv4 adresu primárního síťového rozhraní může být přidán do fondu back-end. Další informace o tom, jak vyrovnávat zatížení více konfigurací protokolu IPv4, najdete v článku [více konfigurací protokolu IP pro vyrovnávání zatížení](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článku. 
    - Vyrovnávání zatížení jedna adresa IPv6 přiřazené k síťovému rozhraní. Další informace o tom, jak pro privátní IPv6 adresou Vyrovnávání zatížení, najdete v článku [adresy IPv6 Vyrovnávání zatížení](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článku.

## <a name="address-types"></a>Typy adres

Následující typy IP adresy, které můžete přiřadit [konfigurace IP adresy](#ip-configurations):

### <a name="private"></a>Private

Privátní [IPv4](#ipv4) adresy povolit virtuálnímu počítači komunikovat s ostatními prostředky ve virtuální síti nebo jiných připojených sítích. Virtuální počítač nelze předávají příchozí ani můžete virtuální počítač odchozí komunikaci s privátní [IPv6](#ipv6) adresu s jednou výjimkou. Virtuální počítač může komunikovat s nástrojem Azure load balancer pomocí adresy IPv6. Další informace najdete v tématu [podrobnosti a omezení pro protokol IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

Ve výchozím nastavení, servery Azure DHCP přiřadit privátní adresu IPv4 pro [primární konfigurace IP adresy](#primary) Azure síťového rozhraní k síťovému rozhraní v operačním systému virtuálního počítače. Pokud není nezbytné, měli byste nastavit ručně nikdy adresu IP síťového rozhraní v rámci operačního systému virtuálního počítače.

> [!WARNING]
> Pokud adresu IPv4 nastavit jako primární IP adresa síťového rozhraní v rámci operačního systému virtuálního počítače je někdy liší od privátní IPv4 adresu přiřazenou k primární konfigurace IP primárního síťového rozhraní připojené k virtuálnímu počítači v rámci Azure můžete ztratit připojení k virtuálnímu počítači.

Existují scénáře, kdy je nutné ručně nastavit adresu IP síťového rozhraní v rámci operačního systému virtuálního počítače. Při přidávání více IP adres pro virtuální počítač Azure například musí nastavit ručně IP adresy primárního a sekundárního operačního systému Windows. Pro virtuální počítač s Linuxem může pouze musíte ručně nastavit sekundárních IP adres. Zobrazit [přidat IP adresy na operační systém virtuálního počítače](virtual-network-multiple-ip-addresses-portal.md#os-config) podrobnosti. Pokud byste někdy potřebovali změnit přiřazenou konfiguraci IP adresu, doporučujeme vám:

1. Ujistěte se, že virtuální počítač je přijetí adresy ze serverů Azure DHCP. Jakmile budete mít, změnit přiřazení IP adresy zpět na DHCP v operačním systému a restartujte virtuální počítač.
2. Zastavení (uvolnění) virtuálního počítače.
3. Změna IP adresy pro konfiguraci protokolu IP v rámci Azure.
4. Umožňuje spustit virtuální počítač.
5. [Ruční konfigurace](virtual-network-multiple-ip-addresses-portal.md#os-config) sekundárních IP adres v rámci operačního systému (a také primární IP adresu v rámci Windows) tak, aby odpovídaly nastavení v rámci Azure.

Podle předchozích kroků, privátní IP adresy přiřazené k síťovému rozhraní v rámci Azure a v rámci operačního systému virtuálního počítače, zůstávají stejné. Chcete-li udržovat přehled o které virtuální počítače v rámci vašeho předplatného, který jste ručně nastavili IP adresy v rámci operačního systému pro, zvažte přidání Azure [značka](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags) k virtuálním počítačům. Můžete použít "přiřazení IP adresy: statická", např. Díky tomu můžete snadno vyhledat virtuální počítače v rámci vašeho předplatného, který jste ručně nastavili IP adresu v rámci operačního systému.

Kromě povolení virtuální počítač, aby komunikovat s ostatními prostředky v rámci stejné nebo připojených virtuálních sítí, privátní IP adresu také umožňuje virtuálním počítačům pro odchozí komunikaci s Internetem. Odchozí připojení se zdrojovou adresu sítě přeložit pomocí Azure na nepředvídatelné veřejnou IP adresu. Další informace o Azure odchozí připojení k Internetu, přečtěte si [Azure odchozí připojení k Internetu](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článku. Nemůžete komunikovat příchozí privátní IP adresu virtuálního počítače z Internetu. Pokud vaše odchozí připojení vyžadují předvídatelné veřejnou IP adresu, přidružte prostředek veřejné IP adresy k síťovému rozhraní.

### <a name="public"></a>Public

Veřejné IP adresy přiřazené prostřednictvím prostředek veřejné IP adresy povolit příchozí připojení k virtuálnímu počítači z Internetu. Odchozí připojení k Internetu, použít předvídatelná IP adresu. Zobrazit [Principy odchozích připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) podrobnosti. Může přiřadit veřejnou IP adresu ke konfiguraci IP, ale nejsou potřeba. Pokud není přiřadíte veřejnou IP adresu virtuálního počítače tím, že přidružíte prostředek veřejné IP adresy, virtuálního počítače můžete stále odchozí komunikaci s Internetem. V tomto případě privátní IP adresa je adresa síťové zdroje přeložit v Azure k nepředvídatelným veřejnou IP adresu. Další informace o prostředky veřejné IP adresy najdete v tématu [prostředek veřejné IP adresy](virtual-network-public-ip-address.md).

Existují omezení pro počet privátních a veřejných IP adres přiřazené k síťovému rozhraní. Podrobnosti najdete v článku [omezeních Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) článku.

> [!NOTE]
> Azure se přeloží virtuální počítač privátní IP adresu na veřejnou IP adresu. V důsledku toho je operačního systému virtuálního počítače neví, všechny veřejné IP adresy přiřazené, a proto není nutné někdy ručně přiřadit veřejnou IP adresu v rámci operačního systému.

## <a name="assignment-methods"></a>Metodu přidělování

Veřejných a privátních IP adres se přiřazuje pomocí jedné z následujících metod přiřazení:

### <a name="dynamic"></a>Dynamická

Dynamické privátních IPv4 a IPv6 (volitelně) adresy přiřazené ve výchozím nastavení.

- **Pouze veřejné**: Azure přiřadí adresu z rozsahu jedinečná pro každou oblast Azure. Další informace, které rozsahy jsou přiřazeny do každé oblasti, naleznete v tématu [rozsahy IP adres Datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adresu můžete změnit, když virtuální počítač je zastaveno (přidělení zrušeno), potom ho spustit znovu. Konfigurace IP pomocí některé z metod přiřazení nelze přiřadit veřejnou IPv6 adresu.
- **Jenom privátní**: Azure si vyhrazuje první čtyři adresy v rozsahu adres každé podsítě a tyto adresy nepřiřazuje. Azure přiřadí prostředku další dostupnou adresu z rozsahu adres podsítě. Pokud je například rozsah adres podsítě 10.0.0.0/16 a adresy 10.0.0.0.4–10.0.0.14 už jsou přiřazené (.0–.3 jsou vyhrazené), Azure prostředku přiřadí adresu 10.0.0.15. Dynamická metoda přidělování je výchozí metoda. Jakmile jsou dynamické IP adresy přiřazené, uvolní se pouze v případě odstranění síťového rozhraní, jeho přiřazení k jiné podsíti ve stejné virtuální síti nebo změně metody přidělování na statickou a zadání jiné IP adresy. Když změníte metodu přidělování z dynamické na statickou, Azure ve výchozím nastavení jako statickou IP adresu přiřadí dříve dynamicky přiřazenou adresu. Můžete přiřadit pouze pomocí metody dynamického přidělování privátní IPv6 adresou.

### <a name="static"></a>Statická

(Volitelně) můžete přiřadit veřejné nebo privátní statickou IPv4 adresu ke konfiguraci IP. Statická adresa IPv6 veřejných nebo privátních nelze přiřadit ke konfiguraci IP. Další informace o přiřazování statických veřejných IPv4 adres v Azure najdete v tématu [veřejné IP adresy](virtual-network-public-ip-address.md).

- **Pouze veřejné**: Azure přiřadí adresu z rozsahu jedinečná pro každou oblast Azure. Můžete si stáhnout seznam rozsahů (předpon) pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064). Adresa nezmění, dokud prostředek veřejné IP adresy, které je přiřazen k odstranění nebo změně metody přiřazení na dynamický. Pokud je prostředek veřejné IP adresy přidružené ke konfiguraci IP, musíte oddělit od konfigurace protokolu IP, než změníte jeho metodu přiřazení.
- **Jenom privátní**: vyberete a přiřadíte adresu z rozsahu adres podsítě. Adresa, kterou přiřadíte, může být jakákoli adresa v rozsahu adres podsítě kromě prvních čtyř adres, která aktuálně není přiřazená k žádnému jinému prostředku v této podsíti. Statické adresy se uvolní pouze v případě odstranění síťového rozhraní. Pokud změníte metodu přidělování na statickou, Azure jako dynamickou adresu dynamicky přiřadí dříve přiřazenou statickou IP adresu, a to i v případě, že tato adresa není další dostupnou adresou v rozsahu adres podsítě. Adresa se změní také v případě přiřazení síťového rozhraní k jiné podsíti ve stejné virtuální síti. Pokud však chcete síťové rozhraní přiřadit k jiné podsíti, musíte nejprve změnit metodu přidělování ze statické na dynamickou. Jakmile přiřadíte síťové rozhraní k jiné podsíti, můžete metodu přidělování změnit zpět na statickou a přiřadit IP adresu z rozsahu adres nové podsítě.

## <a name="ip-address-versions"></a>Verze IP adres

Při přiřazování adresy můžete zadat následující verze:

### <a name="ipv4"></a>IPv4

Každé síťové rozhraní musí mít jeden [primární](#primary) konfigurace IP adresy s přiřazeným [privátní](#private) [IPv4](#ipv4) adresu. Můžete přidat jednu nebo více [sekundární](#secondary) konfigurací protokolu IP, že mají privátní IPv4 a (volitelně) IPv4 [veřejné](#public) IP adresu.

### <a name="ipv6"></a>IPv6

Můžete přiřadit žádnou nebo jednu privátní [IPv6](#ipv6) adres na jednu sekundární konfigurace IP adresy síťového rozhraní. Síťové rozhraní nemůže mít žádné stávající sekundární konfigurace IP. Nelze přidat konfiguraci IP adresou IPv6 pomocí portálu. Přidat konfiguraci IP s privátní IPv6 adresou do existující rozhraní sítě pomocí Powershellu nebo rozhraní příkazového řádku. Síťové rozhraní nelze připojit k existujícímu virtuálnímu počítači.

> [!NOTE]
> I když můžete vytvořit síťové rozhraní s adresou IPv6 pomocí portálu, nelze přidat stávající síťové rozhraní do nového nebo existujícího virtuálního počítače, na portálu. Vytvořte síťové rozhraní s privátní IPv6 adresou pomocí Powershellu nebo rozhraní příkazového řádku Azure a pak připojit síťové rozhraní při vytváření virtuálního počítače. Nelze připojit síťové rozhraní s privátní adresou IPv6 přiřazená k existujícímu virtuálnímu počítači. Privátní IPv6 adresou nelze přidat do konfigurace IP pro každé síťové rozhraní připojené k virtuálnímu počítači pomocí všech nástrojů (portal, Powershellu nebo rozhraní příkazového řádku).

Nelze přiřadit veřejnou IPv6 adresu ke primární nebo sekundární konfiguraci IP adresy.

## <a name="skus"></a>Skladové položky

Veřejná IP adresa se vytvoří s SKU basic nebo standard. Další informace o rozdílech SKU najdete v části [spravovat veřejné IP adresy](virtual-network-public-ip-address.md).

> [!NOTE]
> Při přiřazování veřejné IP adresy standardní SKU k síťovému rozhraní virtuálního počítače je potřeba explicitně povolit plánovaný provoz pomocí [skupiny zabezpečení sítě](security-overview.md#network-security-groups). Komunikace s prostředkem nebude možná, dokud nevytvoříte a nepřiřadíte skupinu zabezpečení sítě a explicitně nepovolíte požadovaný provoz.

## <a name="next-steps"></a>Další postup
K vytvoření virtuálního počítače s různými konfiguracemi IP, přečtěte si následující články:

|Úkol|Nástroj|
|---|---|
|Vytvoření virtuálního počítače s několika síťovými rozhraními|[Rozhraní příkazového řádku](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [prostředí PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Vytvořit jeden virtuální počítač síťovou kartu s více adresami IPv4|[Rozhraní příkazového řádku](virtual-network-multiple-ip-addresses-cli.md), [prostředí PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Vytvořte jeden virtuální počítač síťovou kartu s privátní IPv6 adresou (za služby Azure Load Balancer)|[Rozhraní příkazového řádku](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [šablony Azure Resource Manageru](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
