---
title: "Vytvoření, jejich změny nebo odstranění rozhraní sítě Azure | Microsoft Docs"
description: "Další síťové rozhraní je a jak vytvořit, změňte nastavení pro a odstraňte jednu."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 6c3fb08a43bce6193aa0094992616a290129bac8
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="create-change-or-delete-a-network-interface"></a>Vytvoření, změnit nebo odstranit síťové rozhraní

Naučte se vytvářet, měnit nastavení pro a odstranit síťové rozhraní. Síťové rozhraní umožňuje virtuální počítač Azure ke komunikaci s Internetem, Azure a místních prostředků. Při vytváření virtuálního počítače pomocí portálu Azure, portál vytvoří jedno síťové rozhraní s výchozím nastavením pro vás. Místo toho můžete vytvořit síťových rozhraní s vlastním nastavením a přidejte jeden nebo víc síťových rozhraní k virtuálnímu počítači při jeho vytvoření. Můžete také změnit výchozí nastavení síťového rozhraní pro existující rozhraní sítě. Tento článek vysvětluje, jak vytvořit pomocí vlastních nastavení síťového rozhraní, změňte existující nastavení, jako je například přiřazení sítě filtru (skupina zabezpečení sítě), přiřazení podsítě, nastavení serveru DNS a předávání IP a odstranit síťové rozhraní.

Pokud potřebujete přidat, změnit nebo odebrat IP adresy pro síťové rozhraní, přečtěte si [Správa IP adres](virtual-network-network-interface-addresses.md) článku. Pokud potřebujete síťová rozhraní pro přidání nebo odebrání síťová rozhraní z virtuálních počítačů, přečtěte si [přidat nebo odebrat síťových rozhraní](virtual-network-network-interface-vm.md) článku.


## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v žádné části tohoto článku dokončete následující úlohy:

- Pokud nemáte účet Azure, si zaregistrovat [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.com a přihlaste se pomocí účtu Azure.
- Pokud pomocí příkazů prostředí PowerShell k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.2.0 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` najít nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.26 nebo novější. Spustit `az --version` najít nainstalovanou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení s Azure.

## <a name="create-a-network-interface"></a>Vytvořit rozhraní sítě

Při vytváření virtuálního počítače pomocí portálu Azure, portál vytvoří rozhraní sítě s výchozím nastavením pro vás. Pokud byste místo zadat všechna nastavení síťového rozhraní, můžete vytvořit síťové rozhraní s vlastním nastavením a k virtuálnímu počítači připojit síťové rozhraní, při vytváření virtuálního počítače (pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure). Můžete také vytvořit rozhraní sítě a přidat jej do existujícího virtuálního počítače (pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure CLI). Zjistěte, jak vytvořit virtuální počítač s existující rozhraní sítě nebo přidejte nebo odeberte síťová rozhraní z existujících virtuálních počítačů, přečtěte si téma [přidat nebo odebrat síťových rozhraní](virtual-network-network-interface-vm.md) článku. Před vytvořením síťové rozhraní, musíte mít existující [virtuální sítě](manage-virtual-network.md#create-a-virtual-network) ve stejném umístění a předplatné vytvoříte síťové rozhraní na.

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je přiřazený (minimálně) oprávnění pro roli Přispěvatel sítě pro vaše předplatné. Pro čtení [předdefinované role pro řízení přístupu Azure na základě rolí](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) článku Další informace o přiřazování rolí a oprávnění k účtům.
2. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *síťových rozhraní*. Když **síťových rozhraní** se zobrazí ve výsledcích hledání klikněte na něj.
3. V **síťových rozhraní** okno, které se zobrazí, klikněte na tlačítko **+ přidat**.
4. V **vytvořit síťové rozhraní** okno, které se zobrazí, zadejte, nebo vyberte hodnoty pro následující nastavení a potom klikněte na tlačítko **vytvořit**:

    |Nastavení|Povinné?|Podrobnosti|
    |---|---|---|
    |Název|Ano|Název musí být jedinečný v rámci skupiny prostředků, kterou vyberete. V čase budete mít pravděpodobně několik síťových rozhraní ve vašem předplatném Azure. Pro čtení [konvence vytváření názvů](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) článek pro návrhy při vytváření zásady vytváření názvů, aby se správa několik síťových rozhraní jednodušší. Název nelze změnit po vytvoření síťové rozhraní.|
    |Virtuální síť|Ano|Vyberte virtuální síť pro síťové rozhraní. Síťové rozhraní lze přiřadit pouze k virtuální síti, která existuje ve stejném předplatném a umístění jako síťové rozhraní. Po vytvoření rozhraní sítě, nelze změnit virtuální sítě, které je přiřazen. Virtuální počítač, který přidáte síťové rozhraní musí existovat také ve stejném umístění a předplatné jako síťové rozhraní.|
    |Podsíť|Ano|Vyberte podsíť v rámci virtuální sítě, které jste vybrali. Můžete změnit na podsíť, kterou síťové rozhraní je přiřazena k po jejím vytvoření.|
    |Přidělení privátní IP adresy|Ano| V tomto nastavení zvolíte metodu přiřazení pro adresu IPv4. Vyberte z následujících metod přiřazení: **dynamické:** když vyberete tuto možnost, Azure automaticky přiřadí další dostupnou adresu z adresního prostoru podsítě, které jste vybrali. **Statické:** při výběru této možnosti je nutné ručně přiřadit dostupnou IP adresu z v rámci adresního prostoru podsítě, které jste vybrali. Statické a dynamické adresy se nezmění, dokud je změnit nebo je odstranit síťové rozhraní. Přiřazení metodu můžete změnit po vytvoření síťové rozhraní. Server Azure DHCP přiřadí tato adresa síťového rozhraní v operačním systému virtuálního počítače.|
    |Skupina zabezpečení sítě|Ne| Ponechejte nastavenou na **žádné**, vyberte existující [skupinu zabezpečení sítě](virtual-networks-nsg.md), nebo [vytvořit skupinu zabezpečení sítě](virtual-networks-create-nsg-arm-pportal.md). Skupiny zabezpečení sítě umožňují filtru síťový provoz směřující síťové rozhraní. Můžete použít žádnou nebo jednu skupinu zabezpečení sítě pro síťové rozhraní. Žádnou nebo jednu skupinu zabezpečení sítě můžete použít také k podsíti, který je přiřazen síťové rozhraní. Pokud skupina zabezpečení sítě se použije k rozhraní sítě a podsítě, který je přiřazen síťového rozhraní, dojít k někdy neočekávané výsledky. Odstraňování skupin zabezpečení sítě u síťových rozhraní a podsítě, přečtěte si téma [odstraňování skupin zabezpečení sítě](virtual-network-nsg-troubleshoot-portal.md#nsg) článku.|
    |Předplatné|Ano|Vyberte jednu z vaší Azure [odběry](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Virtuální počítač připojit síťové rozhraní a virtuální síť, ke kterým se připojujete, aby musí existovat ve stejném předplatném.|
    |Privátní IP adresa (IPv6)|Ne| Pokud zaškrtnete toto políčko, adresy IPv6 přiřazena síťového rozhraní, kromě adresu IPv4 přiřazené k síťovému rozhraní. Najdete v článku [IPv6](#IPv6) části tohoto článku důležité informace o použití protokolu IPv6 s síťových rozhraní. Nelze vybrat metodu přiřazení pro adresu IPv6. Pokud se rozhodnete přiřadit adresu IPv6, je přiřazen pomocí dynamické metody.
    |Název protokolu IPv6 (se zobrazí jenom tehdy, když **privátní IP adresa (IPv6)** je zaškrtnuté políčko) |Ano, pokud **privátní IP adresa (IPv6)** je zaškrtnuté políčko.| Tento název je přiřazený k sekundární konfiguraci IP adresy pro síťové rozhraní. Další informace o konfigurace protokolu IP v [zobrazení nastavení síťového rozhraní](#view-network-interface-settings) tohoto článku.|
    |Skupina prostředků|Ano|Vyberte existující [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) nebo ji vytvořte. Rozhraní sítě může existovat ve skupině prostředků stejný nebo jiný než virtuální počítač, který je je připojit, nebo virtuální sítě, můžete připojit k.|
    |Umístění|Ano|Virtuální počítač připojit síťové rozhraní a propojte jej s virtuální síť musí existovat ve stejné [umístění](https://azure.microsoft.com/regions), také nazývaný jako oblast.|

Na portálu neposkytuje možnost veřejnou IP adresu přiřadit síťové rozhraní, když vytvoříte, když na portál vytvoření veřejné IP adresy a přiřaďte ho k síťovému rozhraní při vytváření virtuálního počítače pomocí portálu. Informace o postupu přidání veřejnou IP adresu síťovému rozhraní po jeho vytvoření, přečtěte si téma [Správa IP adres](virtual-network-network-interface-addresses.md) článku. Pokud chcete vytvořit síťové rozhraní s veřejnou IP adresu, musíte použít rozhraní CLI nebo Powershellu k vytvoření síťového rozhraní.

>[!Note]
> Azure přiřadí adresu MAC síťového rozhraní až po síťové rozhraní je připojen k virtuálnímu počítači a při prvním spuštění virtuálního počítače. Nelze zadat adresu MAC, který přiřazuje Azure síťové rozhraní. Adresa MAC zůstane přiřazené k síťovému rozhraní, dokud síťové rozhraní je odstraněný, nebo privátní IP adresa přiřazené k primární konfiguraci IP primární síťové rozhraní se změní. Další informace o IP adresy a konfigurace protokolu IP, přečtěte si [Správa IP adres](virtual-network-network-interface-addresses.md) článku.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[Vytvoření az síťových adaptérů sítě](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-network-interface-settings"></a>Zobrazení nastavení síťového rozhraní

Můžete zobrazit a po jejím vytvoření změnit většinu nastavení pro síťové rozhraní.

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je přiřazený (minimálně) oprávnění pro roli Přispěvatel sítě pro vaše předplatné. Pro čtení [předdefinované role pro řízení přístupu Azure na základě rolí](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) článku Další informace o přiřazování rolí a oprávnění k účtům.
2. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *síťových rozhraní*. Když **síťových rozhraní** se zobrazí ve výsledcích hledání klikněte na něj.
3. V **síťových rozhraní** okno, které se zobrazí, klikněte na tlačítko chcete zobrazit nebo změnit nastavení pro síťové rozhraní.
4. Následující nastavení jsou uvedené v zobrazeném okně pro síťové rozhraní, které jste vybrali:
    - **Přehled:** poskytuje informace o síťového rozhraní, jako je například IP adresy přiřazené k jeho, virtuální sítě a podsítě síťové rozhraní je přiřazena k a síťové rozhraní je připojen k (Pokud je připojen k virtuálnímu počítači jedna). Následující obrázek ukazuje přehled nastavení pro síťové rozhraní s názvem **mywebserver256**: ![přehled rozhraní sítě](./media/virtual-network-network-interface/nic-overview.png) síťové rozhraní můžete přesunout do jiné skupině prostředků nebo předplatného kliknutím (**změnit**) vedle položky **skupiny prostředků** nebo **název odběru**. Pokud přesunete síťového rozhraní, musíte přesunout všechny prostředky související s síťové rozhraní s ním. Pokud síťové rozhraní je připojen k virtuálnímu počítači, například musíte také přesunout virtuální počítač a další zdroje související s virtuální počítač. Přesunout síťové rozhraní, přečtěte si téma [přesunutí prostředku do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal) článku. Článek uvádí předpoklady a postup přesunutí prostředků pomocí portálu Azure, prostředí PowerShell a rozhraní příkazového řádku Azure.
    - **Konfigurace protokolu IP:** jsou zde uvedeny veřejných a privátních IPv4 a IPv6 adresy přiřazené k konfigurace protokolu IP. Pokud adresu IPv6 je přiřazen k konfigurace protokolu IP, adresa se nezobrazí. Další informace o konfigurace protokolu IP a jak přidávat a odebírat IP adresy ve [konfigurace IP adresy pro síť Azure rozhraní](virtual-network-network-interface-addresses.md) článku. V této části jsou také nakonfigurované předávání IP a přiřazení podsítě. Další informace o těchto nastaveních, najdete [povolení nebo zakázání předávání IP](#enable-or-disable-ip-forwarding) a [změnit přiřazení podsítě](#change-subnet-assignment) části tohoto článku.
    - **Servery DNS:** můžete určit, který server DNS síťové rozhraní je přiřazena službou servery Azure DHCP. Síťové rozhraní můžete dědí nastavení z virtuální sítě, který je přiřazen síťového rozhraní nebo mají vlastní nastavení, které potlačí nastavení pro virtuální sítě, které je přiřazen. Pokud chcete upravit, co se zobrazí, proveďte kroky v [servery DNS změnu](#change-dns-servers) tohoto článku.
    - **Skupina zabezpečení sítě (NSG):** zobrazí, které skupina NSG je přidružena k síťovému rozhraní (pokud existuje). Skupina NSG obsahuje příchozí a odchozí pravidla k filtrování provozu sítě pro síťové rozhraní. Pokud se skupinu NSG k síťové rozhraní, zobrazí se název přidružené skupiny NSG. Pokud chcete upravit, co se zobrazí, proveďte kroky v [spravovat přidružení skupiny zabezpečení sítě](virtual-network-manage-nsg-arm-portal.md#manage-associations) článku.
    - **Vlastnosti:** zobrazí klíč nastavení o rozhraní sítě, včetně jeho adresy MAC (prázdný, pokud síťové rozhraní není připojen k virtuálnímu počítači) a předplatné v existuje.
    - **Pravidla efektivní zabezpečení:** pravidla zabezpečení jsou uvedeny, pokud síťové rozhraní je připojena k spuštěného virtuálního počítače a skupiny NSG je přidružena k rozhraní sítě, podsítě je přiřazena k nebo obojí. Další informace o co se zobrazí, přečtěte si [odstraňování skupin zabezpečení sítě](virtual-network-nsg-troubleshoot-portal.md#nsg) článku. Další informace o skupinách Nsg, najdete [skupin zabezpečení sítě](virtual-networks-nsg.md) článku.
    - **Efektivní trasy:** jsou uvedeny trasy, pokud síťové rozhraní je připojena k spuštěného virtuálního počítače. Trasy představují kombinaci Azure výchozích tras, všechny trasy definované uživatelem (UDR) a všechny trasy protokolu BGP, které mohou existovat podsítě, který je přiřazen síťové rozhraní. Další informace o co se zobrazí, přečtěte si [řešení potíží s trasy](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface) článku. Další informace o Azure výchozí a udr, najdete [trasy definované uživatelem](virtual-networks-udr-overview.md) článku.
    - **Obecná nastavení Azure Resource Manager:** Další informace o běžných nastavení Azure Resource Manager, přečtěte si [protokol aktivit](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [přístup k ovládacímu prvku (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [značky](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Zamkne](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a [skriptu pro automatizaci](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group) články.

**Příkazy**

Pokud adresu IPv6 je přiřazen k síťovému rozhraní, výstup prostředí PowerShell vrátí fakt, že je přiřazena adresa, ale nevrací přiřazenou adresu. Podobně, rozhraní příkazového řádku vrátí skutečnost, že adresa je přiřazena, ale vrátí *null* v jeho výstup pro adresu.

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[seznam seskupování sítě az](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_list) zobrazíte síťových rozhraní v rámci předplatného; [az sítě seskupování zobrazit](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_show) zobrazení nastavení síťového rozhraní|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) zobrazíte síťových rozhraní v odběru nebo zobrazení nastavení síťového rozhraní|

## <a name="change-dns-servers"></a>Změnit servery DNS

DNS server je přiřazena službou serveru Azure DHCP rozhraní sítě v rámci operačního systému virtuálního počítače. Server DNS přiřazené je, bez ohledu nastavení serveru DNS je pro síťové rozhraní. Další informace o nastavení překladu název pro rozhraní sítě najdete v tématu [překlad názvů pro virtuální počítače](virtual-networks-name-resolution-for-vms-and-role-instances.md). Síťové rozhraní můžete dědit nastavení z virtuální sítě, nebo použijte svůj vlastní jedinečný nastavení, která přepíše nastavení pro virtuální síť.

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je přiřazený (minimálně) oprávnění pro roli Přispěvatel sítě pro vaše předplatné. Pro čtení [předdefinované role pro řízení přístupu Azure na základě rolí](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) článku Další informace o přiřazování rolí a oprávnění k účtům.
2. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *síťových rozhraní*. Když **síťových rozhraní** se zobrazí ve výsledcích hledání klikněte na něj.
3. V **síťových rozhraní** okno, které se zobrazí, klikněte na tlačítko chcete zobrazit nebo změnit nastavení pro síťové rozhraní.
4. V okně pro síťové rozhraní, které jste vybrali, klikněte na tlačítko **servery DNS** pod **nastavení**.
5. Klikněte na možnost:
    - **Dědění z virtuální sítě (výchozí)**: tuto volbu pro dědění nastavení serveru DNS definované pro síťové rozhraní je přiřazena k virtuální síti. Na úrovni virtuální sítě je definována vlastního serveru DNS nebo serveru DNS poskytnutých platformou Azure. Server DNS poskytnutých platformou Azure lze přeložit názvy hostitelů pro prostředky, které jsou přiřazeny ke stejné virtuální síti. Plně kvalifikovaný název domény musí být používá k překladu pro prostředků přiřazených různých virtuálních sítích.
    - **Vlastní**: můžete nakonfigurovat vlastní server DNS k překladu názvů mezi více virtuálních sítí. Zadejte IP adresu serveru, který chcete použít jako DNS server. Adresa serveru DNS, který zadáte je přiřazena pouze pro toto síťové rozhraní a přepíše všechna nastavení DNS pro virtuální síť, ke které je přiřazen síťové rozhraní.
6. Klikněte na **Uložit**.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[aktualizace az sítě nic](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="enable-or-disable-ip-forwarding"></a>Povolení nebo zakázání předávání IP

Předávání IP umožňuje virtuálnímu počítači, který je síťové rozhraní je připojen k:
- Zobrazí síťový provoz, není určené pro jednu z IP adresy přiřazené k některé z konfigurace protokolu IP, které jsou přiřazené k síťovému rozhraní.
- Odesílat přenos v síti s jinou zdrojovou IP adresou než ten, který přiřadí k jednomu z konfigurace protokolu IP síťového rozhraní.

Nastavení musí být povolena pro každé síťové rozhraní, který je připojen k virtuálnímu počítači, který přijímá provoz, který virtuální počítač vyžaduje pro předávání. Virtuální počítač můžete předat dál provoz, ať už má více síťových rozhraní nebo k němu připojen jedno síťové rozhraní. Azure nastavení při předávání IP virtuálního počítače musí taky spustit aplikaci moct přenos dat, jako jsou brány firewall, optimalizace sítě WAN a aplikace pro vyrovnávání zatížení. Když virtuální počítač běží síťových aplikací, virtuální počítač se často označuje jako virtuální zařízení sítě. Můžete zobrazit seznam připraveny k nasazení virtuálních zařízení sítě v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Předávání IP se obvykle používá s trasy definované uživatelem. Další informace o trasách definovaných uživatelem najdete v článku [Trasy definované uživatelem](virtual-networks-udr-overview.md).

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je přiřazený (minimálně) oprávnění pro roli Přispěvatel sítě pro vaše předplatné. Pro čtení [předdefinované role pro řízení přístupu Azure na základě rolí](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) článku Další informace o přiřazování rolí a oprávnění k účtům.
2. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *síťových rozhraní*. Když **síťových rozhraní** se zobrazí ve výsledcích hledání klikněte na něj.
3. V **síťových rozhraní** okno, které se zobrazí, klikněte na síťové rozhraní, které chcete povolit nebo zakázat předávání protokolu IP pro.
4. V okně pro síťové rozhraní, které jste vybrali, klikněte na tlačítko **konfigurace protokolu IP** v **nastavení** části.
5. Klikněte na tlačítko **povoleno** nebo **zakázané** (výchozí nastavení) Chcete-li změnit nastavení.
6. Klikněte na **Uložit**.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[aktualizace az sítě nic](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet-assignment"></a>Změnit přiřazení podsítě

Podsíť, ale není virtuální síť, přiřazený síťové rozhraní, můžete změnit.

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je přiřazený (minimálně) oprávnění pro roli Přispěvatel sítě pro vaše předplatné. Pro čtení [předdefinované role pro řízení přístupu Azure na základě rolí](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) článku Další informace o přiřazování rolí a oprávnění k účtům.
2. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *síťových rozhraní*. Když **síťových rozhraní** se zobrazí ve výsledcích hledání klikněte na něj.
3. V **síťových rozhraní** okno, které se zobrazí, klikněte na tlačítko chcete zobrazit nebo změnit nastavení pro síťové rozhraní.
4. Klikněte na tlačítko **konfigurace protokolu IP** pod **nastavení** v okně pro síťové rozhraní, které jste vybrali. Pokud uvedené všechny privátní IP adresy pro všechny konfigurace IP **(statické)** vedle, musíte změnit způsob přiřazení IP adresy na dynamické provedením následujících kroků. Všechny privátní IP adresy musí být přiřazená s metodu dynamické přiřazení, chcete-li změnit přiřazení podsítě pro síťové rozhraní. Pokud adresy přiřazené pomocí dynamické metody, pokračujte krokem pět. Pokud žádné adresy IPv4 přiřazené s metodou statické přiřazení, proveďte následující kroky, chcete-li změnit přiřazení metoda na dynamický:
    - Klikněte na konfiguraci protokolu IP, kterou chcete změnit způsob přiřazení adresy IPv4 pro ze seznamu konfigurace protokolu IP.
    - V zobrazeném okně pro konfiguraci protokolu IP, klikněte na **dynamické** pro **přiřazení** metoda. Nelze přiřadit adresu IPv6 s metodou statické přiřazení.
    - Klikněte na **Uložit**.
5. Vyberte podsíť se chcete připojit síťové rozhraní z **podsíť** rozevíracího seznamu.
6. Klikněte na **Uložit**. Nové dynamické adresy přiřazené z rozsahu adres podsítě pro novou podsíť. Po přiřazení nové podsítě síťového rozhraní, můžete přiřadit se statickou adresou IPv4 z nový rozsah adres podsítě, pokud si zvolíte. Další informace o přidání, změně a odebrání IP adresy pro síťové rozhraní, přečtěte si téma [Správa IP adres](virtual-network-network-interface-addresses.md) článku.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[aktualizace ip-config seskupování az sítě](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-a-network-interface"></a>Odstranit síťové rozhraní

Síťové rozhraní můžete odstranit, dokud není připojen k virtuálnímu počítači. Pokud je připojen k virtuálnímu počítači, musíte nejprve umístit virtuální počítač v zastaveném stavu (deallocated), pak se odpojit síťové rozhraní z virtuálního počítače, než budete moct odstranit síťové rozhraní. Se odpojit síťové rozhraní z virtuálního počítače, proveďte kroky v [Odpojit síťové rozhraní z virtuálního počítače](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm) části [přidat nebo odebrat síťových rozhraní](virtual-network-network-interface-vm.md) článku. Odstranění virtuálního počítače umožňuje odpojit všech síťových rozhraní, které je připojený, ale neodstraní rozhraní sítě.

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je přiřazený (minimálně) oprávnění pro roli Přispěvatel sítě pro vaše předplatné. Pro čtení [předdefinované role pro řízení přístupu Azure na základě rolí](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) článku Další informace o přiřazování rolí a oprávnění k účtům.
2. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *síťových rozhraní*. Když **síťových rozhraní** se zobrazí ve výsledcích hledání klikněte na něj.
3. Klikněte pravým tlačítkem na síťové rozhraní, které chcete odstranit, klikněte na **odstranit**.
4. Klikněte na tlačítko **Ano** potvrďte odstranění síťového rozhraní.

Při odstranění rozhraní sítě, jsou vydávány žádné MAC nebo IP adresy přiřazené.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ síť seskupování odstranit](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Další postup
Vytvoření virtuálního počítače s více síťových rozhraní nebo IP adres, přečtěte si následující články:

**Příkazy**

|Úkol|Nástroj|
|---|---|
|Vytvoření virtuálního počítače s několika síťovými kartami|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Vytvoření jednoho virtuálního počítače síťový adaptér s více adresami IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Vytvoření jednoho virtuálního počítače síťový adaptér s privátní adresou IPv6 (za pro vyrovnávání zatížení Azure)|[Rozhraní příkazového řádku](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [prostředí PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [šablony Azure Resource Manageru](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
