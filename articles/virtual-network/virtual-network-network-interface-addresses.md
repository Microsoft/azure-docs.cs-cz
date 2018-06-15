---
title: Konfigurace IP adres pro rozhraní sítě Azure | Microsoft Docs
description: Naučte se přidávat, měnit a odebírat privátní a veřejné IP adresy pro síťové rozhraní.
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
ms.openlocfilehash: 018ca5d0510ef37c58a6d841ac17d2920817e216
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895349"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Přidat, změnit nebo odebrat IP adresy pro rozhraní sítě Azure

Naučte se přidávat, měnit a odebírat veřejné a privátní IP adresy pro síťové rozhraní. Povolit privátní IP adresy přiřazené k síťovému rozhraní virtuálního počítače ke komunikaci s další prostředky ve virtuální sítě Azure a připojených sítí. Privátní IP adresy, taky umožňuje odchozí komunikaci k Internetu pomocí nepředvídatelným IP adresy. A [veřejnou IP adresu](virtual-network-public-ip-address.md) přiřazené na síťové rozhraní povolí příchozí komunikaci na virtuální počítač z Internetu. Adresu taky umožňuje odchozí komunikaci z virtuálního počítače k Internetu pomocí předvídatelný IP adresy. Podrobnosti najdete v tématu [pochopení odchozí připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pokud třeba chcete vytvořit, změnit nebo odstranit síťové rozhraní, přečtěte si [spravovat síťové rozhraní](virtual-network-network-interface.md) článku. Pokud potřebujete síťová rozhraní pro přidání nebo odebrání síťová rozhraní z virtuálního počítače, přečtěte si [přidat nebo odebrat síťových rozhraní](virtual-network-network-interface-vm.md) článku.

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v žádné části tohoto článku dokončete následující úlohy:

- Pokud nemáte účet Azure, si zaregistrovat [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí účtu Azure.
- Pokud pomocí příkazů prostředí PowerShell k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení s Azure.

Účet přihlásit nebo připojit k Azure, musí být přiřazená k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role nebo [vlastní role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [sítě rozhraní oprávnění](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Přidání IP adres

Můžete přidat jako mnoho [privátní](#private) a [veřejné](#public) [IPv4](#ipv4) adres podle potřeby k síťovému rozhraní, v rámci omezení uvedené v [Azure omezuje](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) článku. Přidat adresu IPv6 do existujícího síťového rozhraní (i když na portálu můžete přidat k síťovému rozhraní s privátní adresou IPv6, při vytváření rozhraní sítě) nelze použít na portálu. Můžete použít PowerShell nebo rozhraní příkazového řádku přidání s privátní adresou IPv6 do jedné [sekundární konfiguraci IP adresy](#secondary) (za předpokladu, nejsou žádné existující sekundární konfigurace IP) pro síťové rozhraní, který není připojen k virtuálnímu počítači. Jakýkoli nástroj nelze použít pro přidání veřejnou adresu IPv6 k síťovému rozhraní. V tématu [IPv6](#ipv6) podrobnosti o použití adresy IPv6.

1. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *síťových rozhraní*. Když **síťových rozhraní** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte síťové rozhraní, které chcete přidat adresu IPv4 pro ze seznamu.
3. V části **nastavení**, vyberte **konfigurace protokolu IP**.
4. V části **konfigurace protokolu IP**, vyberte **+ přidat**.
5. Zadejte následující a pak vyberte **OK**:

    |Nastavení|Povinné?|Podrobnosti|
    |---|---|---|
    |Název|Ano|Musí být jedinečný pro síťové rozhraní|
    |Typ|Ano|Vzhledem k tomu, že přidáváte konfiguraci IP adres do existujícího síťového rozhraní, a musí mít každé síťové rozhraní [primární](#primary) je jedinou možností konfigurace protokolu IP, **sekundární**.|
    |Způsob přiřazení privátní IP adresy|Ano|[**Dynamické**](#dynamic): Azure přiřadí další dostupnou adresu pro síťové rozhraní je nasazena v rozsah adres podsítě. [**Statické**](#static): přiřaďte nepoužívané adresy pro síťové rozhraní je nasazena v rozsah adres podsítě.|
    |Veřejná IP adresa|Ne|**Zakázáno:** žádný prostředek veřejné IP adresy je aktuálně přidružené ke konfiguraci IP adresy. **Povoleno:** vyberte existující veřejnou IP adresu IPv4 adresu, nebo vytvořte novou. Naučte se vytvořit veřejnou IP adresu, přečtěte si téma [veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address) článku.|
6. Ručně pomocí pokynů v přidat sekundární soukromé IP adresy do operačního systému virtuálního počítače [přiřadit více IP adres pro virtuální počítač operační systémy](virtual-network-multiple-ip-addresses-portal.md#os-config) článku. V tématu [privátní](#private) IP adresy pro zvláštní situace před ruční přidání IP adres do operačního systému virtuálního počítače. Všechny veřejné IP adresy, nepřidávejte do operačního systému virtuálního počítače.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[Seskupování síťových az ip-config vytvořit](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Změňte nastavení IP adresy

Můžete třeba změnit metodu přiřazení adresy IPv4 změňte statickou IPv4 adresu, nebo změna veřejné IP adresy přiřazené k síťovému rozhraní. Pokud chcete změnit privátní IPv4 adresu sekundární konfiguraci IP adresy přidružené k sekundární síťové rozhraní ve virtuálním počítači (Další informace o [primární a sekundární síťová rozhraní](virtual-network-network-interface-vm.md)), umístěte virtuální počítač do stavu ukončeno (deallocated) před dokončením následujících kroků:

1. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *síťových rozhraní*. Když **síťových rozhraní** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte síťové rozhraní, které chcete zobrazit nebo změnit nastavení IP adresy pro v seznamu.
3. V části **nastavení**, vyberte **konfigurace protokolu IP**.
4. Vyberte konfiguraci IP, kterou chcete upravit ze seznamu.
5. Změňte nastavení podle potřeby, pomocí informací o nastavení v kroku 5 tohoto [Přidat konfiguraci IP adres](#create-ip-config).
6. Vyberte **Uložit**.

>[!NOTE]
>Pokud primární síťové rozhraní má víc konfigurací IP adres a změníte privátní IP adresa primární konfiguraci IP adresy, je třeba přiřadit ručně IP adresy primární a sekundární rozhraní sítě v systému Windows (není potřeba pro Linux) . Chcete-li přiřadit ručně IP adresy síťového rozhraní v rámci operačního systému, přečtěte si téma [přiřadit více IP adres k virtuálním počítačům](virtual-network-multiple-ip-addresses-portal.md#os-config). Zvláštní upozornění před ruční přidání IP adres do operačního systému virtuálního počítače, naleznete v části [privátní](#private) IP adresy. Všechny veřejné IP adresy, nepřidávejte do operačního systému virtuálního počítače.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[aktualizace ip-config seskupování az sítě](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Odebírat IP adresy

Můžete odebrat [privátní](#private) a [veřejné](#public) IP adres ze síťového rozhraní, ale síťové rozhraní musí být vždy alespoň jeden privátní IPv4 adresu přiřazen.

1. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *síťových rozhraní*. Když **síťových rozhraní** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte síťové rozhraní, které chcete odebrat ze seznamu IP adres.
3. V části **nastavení**, vyberte **konfigurace protokolu IP**.
4. Vyberte práva [sekundární](#secondary) konfigurace protokolu IP (nelze odstranit, [primární](#primary) konfigurace), kterou chcete odstranit, vyberte **odstranit**, pak vyberte  **Ano**, potvrďte odstranění. Konfigurace měli prostředek veřejné IP adresy přidružené k němu, prostředek je oddělen od konfiguraci protokolu IP, ale prostředek se neodstraní.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ síť seskupování ip-config odstranit](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Konfigurace protokolu IP

[Privátní](#private) a (volitelně) [veřejné](#public) IP adresy přiřazené k jedné nebo více konfigurací IP adres, které jsou přiřazené k síťovému rozhraní. Existují dva typy konfigurace protokolu IP:

### <a name="primary"></a>Primární

Každé síťové rozhraní je přiřazen jednu primární konfiguraci IP adresy. Primární konfigurace IP adresy:

- Má [privátní](#private) [IPv4](#ipv4) adresu přiřazenou k němu. Nelze přiřadit privátního [IPv6](#ipv6) adresu primární konfiguraci IP adresy.
- Mohou mít i [veřejné](#public) přiřazenu adresu IPv4. Nelze přiřadit veřejnou adresu IPv6 na primární nebo sekundární konfiguraci IP. Ale můžete přiřadit veřejnou adresu IPv6 pro vyrovnávání zatížení Azure, které můžete načíst vyrovnávání přenosů virtuálního počítače privátního IPv6 adresu. Další informace najdete v tématu [podrobnosti a omezení pro protokol IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Sekundární

Kromě primární konfiguraci IP adresy rozhraní sítě může obsahovat nula nebo více sekundární IP konfigurace přiřazen. Sekundární konfiguraci IP adresy:

- Musí mít privátní adresa IPv4 nebo IPv6 přiřazená k němu. Pokud je adresa IPv6, rozhraní sítě může mít pouze jednu sekundární konfiguraci IP adresy. Pokud je adresa IPv4, rozhraní sítě může mít několik sekundární konfigurace IP přiřazen. Další informace o tom, kolik privátní a veřejné IPv4 adresy lze přiřadit k síťovému rozhraní, najdete v článku [Azure omezuje](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) článku.
- Může také veřejnou adresu IPv4 přiřadili, pokud je privátní IP adresa IPv4. Pokud je privátní IP adresu IPv6, nelze přiřadit veřejnou adresu IPv4 nebo IPv6 ke konfiguraci IP adresy. Přiřazení více IP adres k síťovému rozhraní je užitečné v situacích, jako:
    - Hostovat několik webů nebo služeb s různými IP adresami a certifikáty SSL na jednom serveru.
    - Virtuální počítač, který slouží jako virtuální zařízení sítě, jako je například brány firewall nebo službu Vyrovnávání zatížení.
    - Možnost přidat kterýkoliv privátní adresy IPv4 v žádném z rozhraní sítě na fond back-end pro vyrovnávání zatížení Azure. V minulosti pouze primární adresu IPv4 pro primární síťové rozhraní nebylo možné přidat do fondu back-end. Další informace o tom, jak Vyrovnávání zatížení konfigurací s více IPv4, najdete v článku [víc konfigurací IP adres Vyrovnávání zatížení](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článku. 
    - Umožňuje načíst vyrovnávat jedna adresa IPv6, které jsou přiřazené k síťovému rozhraní. Další informace o tom, jak pro privátní adresa IPv6 Vyrovnávání zatížení, najdete v článku [IPv6 adresy Vyrovnávání zatížení](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článku.

## <a name="address-types"></a>Typy adres

Následující typy IP adresy, které můžete přiřadit [konfigurace protokolu IP](#ip-configurations):

### <a name="private"></a>Private

Privátní [IPv4](#ipv4) adresy povolit virtuální počítač ke komunikaci s další prostředky ve virtuální síti nebo jiné připojených sítích. Virtuální počítač nelze oznamovat příchozí k ani může virtuální počítač komunikovat s privátního odchozí [IPv6](#ipv6) adresu s jednou výjimkou. Virtuální počítač může komunikovat s nástrojem pro vyrovnávání zatížení Azure pomocí adresy IPv6. Další informace najdete v tématu [podrobnosti a omezení pro protokol IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

Ve výchozím nastavení, servery Azure DHCP přiřadit privátní adresa IPv4 pro [primární konfiguraci IP adresy](#primary) Azure síťového rozhraní na rozhraní sítě v rámci operačního systému virtuálního počítače. Pokud není nezbytné, měli byste nastavit nikdy ručně IP adresu síťového rozhraní v rámci operačního systému virtuálního počítače.

> [!WARNING]
> Pokud adresu IPv4 nastavit jako primární IP adresa síťového rozhraní v rámci operačního systému virtuálního počítače je někdy liší od privátní adresa IPv4, které jsou přiřazené k primární konfiguraci IP primární síťové rozhraní připojen k virtuálnímu počítači v rámci Azure ztratíte připojení k virtuálnímu počítači.

Existují scénáře, kdy je nutné ručně nastavit IP adresu síťového rozhraní v rámci operačního systému virtuálního počítače. Při přidávání více IP adres pro virtuální počítač Azure například musí nastavit ručně primární a sekundární IP adresy operačního systému Windows. Pro virtuální počítač s Linuxem jenom musíte ručně nastavit sekundární IP adresy. V tématu [přidat IP adresy na operační systém virtuálního počítače](virtual-network-multiple-ip-addresses-portal.md#os-config) podrobnosti. Pokud byste někdy potřebovali změnit přiřazené ke konfiguraci IP adresu, se doporučuje, můžete:

1. Ujistěte se, že virtuální počítač je přijetí adresy ze serverů Azure DHCP. Když máte, můžete změnit přiřazení IP adresy zpět na serveru DHCP v operačním systému a restartování virtuálního počítače.
2. Zastavit (zrušit přidělení) virtuálního počítače.
3. Změna IP adresy pro konfiguraci protokolu IP v rámci Azure.
4. Umožňuje spustit virtuální počítač.
5. [Ruční konfigurace](virtual-network-multiple-ip-addresses-portal.md#os-config) sekundární IP adresy v rámci operačního systému (a také primární IP adresu v systému Windows) tak, aby odpovídaly nastavení v rámci Azure.

Podle předchozího postupu, přiřazené k síťovému rozhraní v rámci Azure a v rámci operačního systému virtuálního počítače, privátní IP adresy zůstávají stejné. Ke sledování virtuálních počítačů v rámci vašeho předplatného, které jste nastavili ručně IP adresy v rámci operačního systému pro, zvažte přidání Azure [značky](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags) k virtuálním počítačům. Můžete použít "přiřazení IP adresy: statické", např. Tímto způsobem, budete moci snadno najít virtuální počítače v rámci vašeho předplatného, které jste ručně nastavení IP adresy v operačním systému.

Kromě povolení komunikovat s jiným prostředkům v rámci stejné, nebo připojené virtuální sítě virtuálního počítače, privátní IP adresu taky umožňuje virtuálnímu počítači komunikovat odchozí k Internetu. Odchozí připojení se zdrojovou adresu sítě přeložit v Azure nepředvídatelným veřejné IP adresy. Další informace o Azure odchozí připojení k Internetu, přečtěte si [Azure odchozí připojení k Internetu](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) článku. Nemůžete komunikovat příchozí privátní IP adresu virtuálního počítače z Internetu. Pokud váš odchozí připojení vyžadují předvídatelnou veřejnou IP adresu, přidružte prostředek veřejné IP adresy pro síťové rozhraní.

### <a name="public"></a>Public

Veřejné IP adresy přiřazené prostřednictvím prostředek veřejné IP adresy povolit příchozí připojení k virtuálnímu počítači z Internetu. Odchozí připojení k Internetu používat předvídatelný IP adresu. V tématu [pochopení odchozí připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) podrobnosti. Může přiřadit konfiguraci IP adres veřejnou IP adresu, ale nejsou potřeba. Pokud nepřiřadíte veřejnou IP adresu do virtuálního počítače tím, že přidružíte prostředek veřejné IP adresy, virtuálního počítače může komunikovat stále odchozí k Internetu. V takovém případě privátní IP adresa je adresa sítě zdroj přeložit v Azure nepředvídatelným veřejné IP adresy. Další informace o veřejné IP adresy prostředky, najdete v části [prostředek veřejné IP adresy](virtual-network-public-ip-address.md).

Existují omezení počtu privátní a veřejné IP adresy, které můžete přiřadit k síťovému rozhraní. Informace najdete [Azure omezuje](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) článku.

> [!NOTE]
> Azure znamená, že virtuální počítač privátní IP adresu na veřejnou IP adresu. V důsledku toho je operačního systému virtuálního počítače neví, všechny veřejné IP adresy přiřazené, a proto není nutné někdy ručně přiřadit veřejnou IP adresu v operačním systému.

## <a name="assignment-methods"></a>Přiřazení metody

Veřejné a privátní IP adresy se přiřazují pomocí jedné z následujících metod přiřazení:

### <a name="dynamic"></a>Dynamická

Dynamické privátní IPv4 a IPv6 (volitelně) adresy přiřazené ve výchozím nastavení.

- **Pouze veřejné**: Azure přiřadí adresu z rozsahu jedinečný pro každé oblasti Azure. Další informace, které rozsahy jsou přiřazeny pro každou oblast, najdete v části [rozsahy IP Datacentra Azure Microsoft](https://www.microsoft.com/download/details.aspx?id=41653). Pokud je virtuální počítač zastaven (deallocated), pak spustit znovu, můžete změnit adresu. Pro konfiguraci IP adres pomocí metody přiřazení nelze přiřadit veřejnou adresu IPv6.
- **Privátní pouze**: Azure si vyhrazuje první čtyři adres v každé rozsah adres podsítě a není přiřadit adresy. Azure přiřadí prostředku další dostupnou adresu z rozsahu adres podsítě. Pokud je například rozsah adres podsítě 10.0.0.0/16 a adresy 10.0.0.0.4–10.0.0.14 už jsou přiřazené (.0–.3 jsou vyhrazené), Azure prostředku přiřadí adresu 10.0.0.15. Dynamická metoda přidělování je výchozí metoda. Jakmile jsou dynamické IP adresy přiřazené, uvolní se pouze v případě odstranění síťového rozhraní, jeho přiřazení k jiné podsíti ve stejné virtuální síti nebo změně metody přidělování na statickou a zadání jiné IP adresy. Když změníte metodu přidělování z dynamické na statickou, Azure ve výchozím nastavení jako statickou IP adresu přiřadí dříve dynamicky přiřazenou adresu. Lze přiřadit pouze s privátní adresou IPv6 pomocí metody dynamického přiřazení.

### <a name="static"></a>Statická

Veřejné nebo soukromé statickou IPv4 adresu (volitelně) můžete přiřadit konfiguraci IP adres. Nelze přiřadit statická adresa IPv6 veřejných nebo privátních pro konfiguraci IP adres. Další informace o tom, jak Azure přiřadí statické veřejné adresy IPv4, najdete v článku [veřejnou IP adresu](virtual-network-public-ip-address.md) článku.

- **Pouze veřejné**: Azure přiřadí adresu z rozsahu jedinečný pro každé oblasti Azure. Další informace, které rozsahy jsou přiřazeny pro každou oblast, najdete v části [rozsahy IP Datacentra Azure Microsoft](https://www.microsoft.com/download/details.aspx?id=41653). Adresu nemění příliš dlouho, dokud prostředek veřejné adresy IP, které je přiřazena k odstranění nebo metodu přiřazení se změní na dynamický. Pokud prostředek veřejné IP adresy je přidružena ke konfiguraci IP adres, musí být oddělit z konfigurace IP před změnou metoda jeho přiřazení.
- **Privátní pouze**: vyberte a přiřadit adresu z rozsahu adres podsíti. Adresa, kterou přiřadíte, může být jakákoli adresa v rozsahu adres podsítě kromě prvních čtyř adres, která aktuálně není přiřazená k žádnému jinému prostředku v této podsíti. Statické adresy se uvolní pouze v případě odstranění síťového rozhraní. Pokud změníte metodu přidělování na statickou, Azure jako dynamickou adresu dynamicky přiřadí dříve přiřazenou statickou IP adresu, a to i v případě, že tato adresa není další dostupnou adresou v rozsahu adres podsítě. Adresa se změní také v případě přiřazení síťového rozhraní k jiné podsíti ve stejné virtuální síti. Pokud však chcete síťové rozhraní přiřadit k jiné podsíti, musíte nejprve změnit metodu přidělování ze statické na dynamickou. Jakmile přiřadíte síťové rozhraní k jiné podsíti, můžete metodu přidělování změnit zpět na statickou a přiřadit IP adresu z rozsahu adres nové podsítě.

## <a name="ip-address-versions"></a>Verze IP adres

Při přiřazování adresy můžete zadat následující verze:

### <a name="ipv4"></a>IPv4

Každé síťové rozhraní musí mít jeden [primární](#primary) konfigurace protokolu IP s přiřazeným [privátní](#private) [IPv4](#ipv4) adresu. Můžete přidat jeden nebo víc [sekundární](#secondary) konfigurace protokolu IP nichž každá má privátní IPv4 a (volitelně) IPv4 [veřejné](#public) IP adresu.

### <a name="ipv6"></a>IPv6

Můžete přiřadit žádnou nebo jednu privátní [IPv6](#ipv6) adresu jednu sekundární konfiguraci IP adresy síťového rozhraní. Síťové rozhraní nemůže mít všechny existující sekundární konfigurace IP. Nelze přidat konfiguraci IP adres s IPv6 adresu pomocí portálu. Pomocí prostředí PowerShell nebo rozhraní příkazového řádku přidejte konfiguraci IP adres s privátní adresou IPv6 do existujícího síťového rozhraní. Síťové rozhraní nelze připojit k existující virtuální počítač.

> [!NOTE]
> Když vytvoříte síťového rozhraní IPv6 adresu pomocí portálu nelze přidat existující rozhraní sítě do nového nebo existujícího virtuálního počítače pomocí portálu. Pomocí prostředí PowerShell nebo 2.0 rozhraní příkazového řádku Azure k vytvoření síťového rozhraní s privátní adresou IPv6 a potom připojit síťové rozhraní, při vytváření virtuálního počítače. Nelze připojit síťové rozhraní s privátní adresou IPv6 přiřazená k existující virtuální počítač. Pro konfiguraci IP adres pro všechny síťové rozhraní, který je připojen k virtuálnímu počítači pomocí libovolné nástroje (portál, rozhraní příkazového řádku nebo prostředí PowerShell) nelze přidat s privátní adresou IPv6.

Nelze přiřadit veřejnou adresu IPv6 na primární nebo sekundární konfiguraci IP.

## <a name="skus"></a>Skladové položky

Veřejná IP adresa je vytvořen s SKU basic nebo standard. Další informace o rozdílech SKU najdete v tématu [spravovat veřejné IP adresy](virtual-network-public-ip-address.md).

> [!NOTE]
> Při přiřazování veřejné IP adresy standardní SKU k síťovému rozhraní virtuálního počítače je potřeba explicitně povolit plánovaný provoz pomocí [skupiny zabezpečení sítě](security-overview.md#network-security-groups). Komunikace s prostředkem nebude možná, dokud nevytvoříte a nepřiřadíte skupinu zabezpečení sítě a explicitně nepovolíte požadovaný provoz.

## <a name="next-steps"></a>Další postup
Pokud chcete vytvořit virtuální počítač s různými konfiguracemi protokolu IP, přečtěte si v následujících článcích:

|Úkol|Nástroj|
|---|---|
|Vytvoření virtuálního počítače s několika síťovými rozhraními|[Rozhraní příkazového řádku](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [prostředí PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Vytvoření jednoho virtuálního počítače síťový adaptér s více adresami IPv4|[Rozhraní příkazového řádku](virtual-network-multiple-ip-addresses-cli.md), [prostředí PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Vytvoření jednoho virtuálního počítače síťový adaptér s privátní adresou IPv6 (za pro vyrovnávání zatížení Azure)|[Rozhraní příkazového řádku](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [prostředí PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [šablony Azure Resource Manageru](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
