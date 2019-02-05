---
title: Vytvoření, změna nebo odstranění rozhraní sítě Azure
titlesuffix: Azure Virtual Network
description: Zjistěte, co je síťové rozhraní je a jak vytvořit, změnit nastavení a toku nějaký tok odstranit.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 93534cf80a007dbb848a515ec4ec165c67e3b456
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55730692"
---
# <a name="create-change-or-delete-a-network-interface"></a>Vytvoření, změna nebo odstranění síťového rozhraní

Zjistěte, jak vytvořit, změnit nastavení a odstranit síťové rozhraní. Síťové rozhraní umožňuje virtuálním počítači Azure ke komunikaci s Internetem, Azure a místním prostředkům. Při vytváření virtuálního počítače pomocí webu Azure portal, portál vytvoří jedno síťové rozhraní s výchozím nastavením za vás. Místo toho můžete k vytvoření síťových rozhraní s vlastním nastavením a přidejte jeden nebo víc síťových rozhraní k virtuálnímu počítači po jeho vytvoření. Můžete také změnit výchozí nastavení síťového rozhraní pro existující síťové rozhraní. Tento článek vysvětluje, jak vytvořit síťové rozhraní s vlastním nastavením, stávající nastavení, jako je například přiřazení sítě filtru (skupiny zabezpečení sítě), přiřazení podsítě, nastavení serveru DNS a předávání IP, změnit a odstranit síťové rozhraní.

Pokud potřebujete přidat, změnit, nebo odebrání IP adres pro síťové rozhraní, naleznete v tématu [Správa IP adres](virtual-network-network-interface-addresses.md). Pokud je potřeba přidat síťová rozhraní nebo odebrání síťových rozhraní virtuálních počítačů, naleznete v tématu [přidání nebo odebrání síťových rozhraní](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Před zahájením

Před dokončením kroků v jakékoli části tohoto článku, proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud používáte příkazy prostředí PowerShell k dokončení úkolů v tomto článku, buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje použití Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.

Účet přihlásit nebo připojit k Azure, musíte být přiřazeni k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

## <a name="create-a-network-interface"></a>Vytvořte síťové rozhraní

Při vytváření virtuálního počítače pomocí webu Azure portal, na portálu se vytvoří síťové rozhraní s výchozím nastavením za vás. Pokud by místo toho zadat všechna nastavení síťového rozhraní, můžete vytvořit síťové rozhraní s vlastním nastavením a připojit síťové rozhraní k virtuálnímu počítači při vytváření virtuálního počítače (pomocí Powershellu nebo rozhraní příkazového řádku Azure CLI). Můžete také vytvořit síťové rozhraní a přidat do existujícího virtuálního počítače (pomocí Powershellu nebo rozhraní příkazového řádku Azure CLI). Informace o vytvoření virtuálního počítače s existující síťové rozhraní nebo přidat nebo odebrat síťová rozhraní z existujících virtuálních počítačů najdete v tématu [přidání nebo odebrání síťových rozhraní](virtual-network-network-interface-vm.md). Před vytvořením síťového rozhraní, musíte mít existující [virtuální sítě](manage-virtual-network.md) ve stejném umístění a předplatném můžete vytvořit síťové rozhraní v.

1. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *síťová rozhraní*. Když **síťová rozhraní** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte **+ přidat** pod **síťová rozhraní**.
3. Zadejte, nebo vyberte hodnoty pro následující nastavení a pak vyberte **vytvořit**:

    |Nastavení|Povinné?|Podrobnosti|
    |---|---|---|
    |Název|Ano|Název musí být jedinečný v rámci skupiny prostředků, kterou vyberete. V čase budete mít pravděpodobně několik síťových rozhraní ve vašem předplatném Azure. Návrhy při vytváření zásady vytváření názvů provést několik síťových rozhraní snadnější správu, naleznete v tématu [zásady vytváření názvů](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Název nelze změnit po vytvoření síťového rozhraní.|
    |Virtuální síť|Ano|Výběr virtuální sítě pro síťové rozhraní. Síťové rozhraní můžete přiřadit pouze k virtuální síti, který existuje ve stejném předplatném a umístění jako síťové rozhraní. Jakmile se vytvoří síťové rozhraní, nelze změnit virtuální síť, ke které je přiřazen. Virtuální počítač, který můžete přidat síťové rozhraní musí existovat také ve stejném umístění a předplatném jako síťové rozhraní.|
    |Podsíť|Ano|Vyberte podsíť ve virtuální síti, kterou jste vybrali. Můžete změnit podsíť, ve které je síťové rozhraní přiřazená po jeho vytvoření.|
    |Přidělení privátní IP adresy|Ano| V tomto nastavení, abyste dokázali vybrat metody přiřazení pro IPv4 adresu. Zvolte jednu z následujících metod přiřazení: **Dynamické:** Když vyberete tuto možnost, Azure automaticky přiřadí další dostupnou adresou z adresního prostoru podsítě, kterou jste vybrali. **Statické:** Když vyberete tuto možnost, musíte ručně přiřadit dostupnou IP adresu z v rámci adresního prostoru podsítě, kterou jste vybrali. Statické a dynamické adresy se nezmění, dokud je změna nebo odstranění síťového rozhraní. Metodu přiřazování můžete změnit po vytvoření síťového rozhraní. Server Azure DHCP přiřadí tuto adresu k síťovému rozhraní v operačním systému virtuálního počítače.|
    |Skupina zabezpečení sítě|Ne| Ponechejte nastavený na hodnotu **žádný**, vyberte existující [skupinu zabezpečení sítě](security-overview.md), nebo [vytvořte skupinu zabezpečení sítě](tutorial-filter-network-traffic.md). Skupiny zabezpečení sítě vám umožní filtrovat síťový provoz do a z síťové rozhraní. Můžete provést žádnou nebo jednu skupinu zabezpečení sítě k síťovému rozhraní. Žádnou nebo jednu skupinu zabezpečení sítě můžete také použít pro podsíť, ve které je přiřazeno síťové rozhraní. Při použití skupinu zabezpečení sítě u síťových rozhraní a podsítě, přiřazené k síťovému rozhraní, dojde k někdy neočekávané výsledky. Řešení potíží se skupinami zabezpečení sítě u síťových rozhraní a podsítí, najdete v článku [řešení potíží se skupinami zabezpečení sítě](diagnose-network-traffic-filter-problem.md).|
    |Předplatné|Ano|Vyberte jednu z Azure [předplatná](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Virtuální počítač připojit síťové rozhraní a propojte jej s virtuální síť musí existovat ve stejném předplatném.|
    |Privátní IP adresa (IPv6)|Ne| Pokud zaškrtnete toto políčko, adresa protokolu IPv6 je přiřazené k síťovému rozhraní, kromě IPv4 adresu přiřazenou k síťovému rozhraní. Zobrazit [IPv6](#IPv6) části tohoto článku důležité informace o použití protokolu IPv6 se síťovými rozhraními. Nelze vybrat metodu přiřazení pro adresu IPv6. Pokud se rozhodnete přiřadit adresu protokolu IPv6, je přiřazen s dynamickou metodu.
    |Název protokolu IPv6 (se zobrazí pouze tehdy, když **privátní IP adresa (IPv6)** je zaškrtnuté políčko) |Ano, pokud **privátní IP adresa (IPv6)** je zaškrtnuté políčko.| Tento název je přiřazen k sekundární konfigurace IP adresy pro síťové rozhraní. Další informace o konfigurací protokolu IP, naleznete v tématu [zobrazení nastavení síťového rozhraní](#view-network-interface-settings).|
    |Skupina prostředků|Ano|Vyberte existující [skupiny prostředků](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) nebo ji vytvořte. Síťové rozhraní může existovat ve skupině prostředků stejného nebo jiného než virtuální počítač, který připojí, nebo virtuální sítě, můžete připojit k.|
    |Umístění|Ano|Virtuální počítač připojit síťové rozhraní a připojte ho k virtuální síti musí existovat ve stejném [umístění](https://azure.microsoft.com/regions), která se také označují jako oblast.|

Na portálu nenabízí možnost přiřadit veřejné IP adresy k síťovému rozhraní, při vytváření, i když na portálu vytvořit veřejnou IP adresu a přiřadit ji k síťovému rozhraní při vytváření virtuálního počítače pomocí portálu. Další postup přidání veřejné IP adresy k síťovému rozhraní po jeho vytvoření najdete v tématu [Správa IP adres](virtual-network-network-interface-addresses.md). Pokud chcete vytvořit síťové rozhraní s veřejnou IP adresu, musíte použít PowerShell nebo rozhraní příkazového řádku k vytvoření síťového rozhraní.

Na portálu neposkytuje možnost pro síťové rozhraní přiřadit do skupin zabezpečení aplikací, při vytváření síťového rozhraní, ale rozhraní příkazového řádku Azure a Powershellu. Existující síťové rozhraní můžete přiřadit skupinu zabezpečení aplikace pomocí portálu, ale tak dlouho, dokud je síťové rozhraní připojené k virtuálnímu počítači. Informace o síťové rozhraní přiřadit skupinu zabezpečení aplikace, najdete v článku [přidat nebo odebrat ze skupiny zabezpečení aplikací](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Azure přiřadí adresu MAC síťového rozhraní pouze poté, co je síťové rozhraní připojené k virtuálnímu počítači a prvním spuštění virtuálního počítače. Nelze zadat adresu MAC, který Azure ho přiřadí k síťovému rozhraní. Adresa MAC zůstane přiřazené k síťovému rozhraní, dokud odstranění síťového rozhraní nebo privátní IP adresa přiřazená primární konfigurace IP primárního síťového rozhraní se změnilo. Další informace o IP adresách a konfigurace protokolu IP, naleznete v tématu [Správa IP adres](virtual-network-network-interface-addresses.md)

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)|

## <a name="view-network-interface-settings"></a>Zobrazení nastavení síťového rozhraní

Můžete zobrazit a změnit většinu nastavení síťového rozhraní po jeho vytvoření. Na portálu nezobrazuje DNS přípona nebo aplikaci členství ve skupině zabezpečení pro síťové rozhraní. Můžete použít PowerShell nebo rozhraní příkazového řádku Azure [příkazy](#view-settings-commands) zobrazíte DNS přípona a aplikace členství ve skupině zabezpečení.

1. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *síťová rozhraní*. Když **síťová rozhraní** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte síťové rozhraní, které chcete zobrazit nebo změnit nastavení pro ze seznamu.
3. Následující položky jsou uvedeny pro síťové rozhraní, které jste vybrali:
    - **Přehled:** Poskytuje informace o síťové rozhraní, jako je například IP adresy přiřazené, virtuální sítě a podsítě, přiřazené k síťovému rozhraní, a z virtuálního počítače, které je síťové rozhraní připojené k (Pokud je připojený k jednomu). Následující obrázek znázorňuje přehled nastavení pro síťové rozhraní s názvem **mywebserver256**: ![Přehled rozhraní sítě](./media/virtual-network-network-interface/nic-overview.png) síťové rozhraní můžete přesunout do jiné skupiny prostředků nebo předplatného tak, že vyberete (**změnit**) vedle položky **skupiny prostředků** nebo  **Název předplatného**. Pokud přesunete síťové rozhraní, je nutné přesunout všechny prostředky vztahující se k síťovému rozhraní s ním. Pokud je síťové rozhraní připojené k virtuálnímu počítači, například musíte také přesunout virtuální počítač a další prostředky týkající se virtuálních počítačů. Pokud chcete přesunout síťové rozhraní, naleznete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). Tento článek uvádí předpoklady a postup přesunutí prostředků pomocí webu Azure portal, PowerShell a rozhraní příkazového řádku Azure.
    - **Konfigurace protokolu IP:** Zde jsou uvedeny veřejných a privátních IPv4 a IPv6 adresy přiřazené konfigurace protokolu IP. Pokud ke konfiguraci IP není přiřazena adresa protokolu IPv6, se nezobrazí adresu. Další informace o konfiguracích IP a jak přidávat a odebírat z něj IP adresy najdete v tématu [konfigurace IP adresy pro rozhraní sítě Azure](virtual-network-network-interface-addresses.md). V této části jsou také nakonfigurované předávání IP a podsíti přiřazení. Další informace o těchto nastaveních najdete v tématu [povolení nebo zakázání předávání IP](#enable-or-disable-ip-forwarding) a [změnit přiřazení podsítě](#change-subnet-assignment).
    - **Servery DNS:** Můžete určit, který server DNS, servery Azure DHCP přiřadí síťového rozhraní. Síťové rozhraní můžete dědí nastavení z virtuální sítě, přiřazené k síťovému rozhraní, nebo vlastní nastavení, která přepíše nastavení pro virtuální síť, který je přiřazen k. Pokud chcete upravit obsah zobrazený, naleznete v tématu [servery DNS změnu](#change-dns-servers).
    - **Skupina zabezpečení sítě (NSG):** Zobrazí, které je skupina zabezpečení sítě přidružené k síťovému rozhraní (pokud existuje). Skupina zabezpečení sítě obsahuje pravidla pro příchozí a odchozí k filtrování provozu sítě pro síťové rozhraní. Pokud je skupina zabezpečení sítě přidružené k síťovému rozhraní, zobrazí se název přidružené skupiny zabezpečení sítě. Pokud chcete upravit obsah zobrazený, naleznete v tématu [přidružení nebo zrušit přidružení skupiny zabezpečení sítě](#associate-or-dissociate-a-network-security-group).
    - **Vlastnosti:** Zobrazuje nastavení klíče o síťové rozhraní, včetně jeho adresa MAC (prázdné, pokud není síťové rozhraní připojené k virtuálnímu počítači) a předplatné existuje ve službě.
    - **Platná pravidla zabezpečení:**  Pravidla zabezpečení jsou uvedeny, pokud je síťové rozhraní připojené k běžícímu virtuálnímu počítači a je skupina zabezpečení sítě přidružené k síťovému rozhraní a podsíť, ve které je přiřazen k. Další informace o co se zobrazí, naleznete v tématu [zobrazit platná pravidla zabezpečení](#view-effective-security-rules). Další informace o skupinách Nsg najdete v tématu [skupiny zabezpečení sítě](security-overview.md).
    - **Efektivní trasy:** Pokud je síťové rozhraní připojené k běžícímu virtuálnímu počítači jsou uvedeny trasy. Trasy se kombinace Azure výchozí trasy, všechny trasy definované uživatelem a všechny trasy protokolu BGP, které mohou existovat pro podsíť, ve které je přiřazeno síťové rozhraní. Další informace o co se zobrazí, naleznete v tématu [zobrazení efektivních tras](#view-effective-routes). Další informace o Azure výchozí trasy a trasy definované uživatelem, najdete v článku [Přehled směrování](virtual-networks-udr-overview.md).
    - **Obecná nastavení Azure Resource Manageru:**  Další informace o běžných nastavení Azure Resource Manageru najdete v tématu [protokolu aktivit](../azure-monitor/platform/activity-logs-overview.md), [řízení přístupu (IAM)](../role-based-access-control/overview.md), [značky](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [uzamkne](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)a [ Automatizační skript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

<a name="view-settings-commands"></a>**Příkazy**

Pokud k síťovému rozhraní není přiřazena adresa protokolu IPv6, na výstupu prostředí PowerShell vrátí skutečnost, že je přiřazena adresa, ale nevrací přiřazenou adresu. Podobně, rozhraní příkazového řádku vrátí skutečnost, že je přiřazena adresa, ale vrátí *null* v jeho výstupní adresy.

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ network nic seznamu](/cli/azure/network/nic) zobrazíte síťová rozhraní v rámci předplatného; [az network nic show](/cli/azure/network/nic) Chcete-li zobrazit nastavení pro síťové rozhraní|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) zobrazíte síťová rozhraní v předplatném nebo zobrazit nastavení pro síťové rozhraní|

## <a name="change-dns-servers"></a>Změnit servery DNS

DNS server je přiřazené serverem Azure DHCP k síťovému rozhraní v operačním systému virtuálního počítače. Server DNS, které jsou přiřazeny je cokoli, co nastavení serveru DNS pro síťové rozhraní. Další informace o nastavení pro nalezení název pro síťové rozhraní, naleznete v tématu [překlad názvů pro virtuální počítače](virtual-networks-name-resolution-for-vms-and-role-instances.md). Síťové rozhraní může dědit nastavení z virtuální sítě nebo použijte svůj vlastní jedinečný nastavení, která přepíše nastavení pro virtuální síť.

1. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *síťová rozhraní*. Když **síťová rozhraní** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte síťové rozhraní, které chcete změnit server DNS pro ze seznamu.
3. Vyberte **servery DNS** pod **nastavení**.
4. Vyberte buď:
    - **Zdědit z virtuální sítě**: Tato možnost zdědí nastavení serveru DNS definované pro virtuální síť přiřazené k síťovému rozhraní. Na všech úrovních virtuální sítě je definován vlastní server DNS nebo serveru DNS poskytnutých platformou Azure. Server DNS poskytnutých platformou Azure lze přeložit názvy hostitelů pro prostředky, které jsou přiřazeny ke stejné virtuální síti. Plně kvalifikovaný název domény musí být používá k překladu pro prostředky přiřazené k jiné virtuální sítě.
    - **Vlastní**: Můžete nakonfigurovat vlastní server DNS k překladu názvů mezi několika virtuálními sítěmi. Zadejte IP adresu serveru, který chcete použít jako DNS server. Adresa serveru DNS, kterou zadáte je přiřazena pouze k tomuto síťovému rozhraní a přepíše jakékoli nastavení DNS pro virtuální síť přiřazené k síťovému rozhraní.
5. Vyberte **Uložit**.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Povolení nebo zakázání předávání IP

Předávání IP umožňuje virtuální počítač síťového rozhraní je připojeno k:
- Přijímat síťový provoz, které nejsou určené pro jednu z IP adresy přiřazené k některému z konfigurace protokolu IP, které jsou přiřazené k síťovému rozhraní.
- Posílat síťový provoz s IP adresou jiného zdroje než ten, který přiřadí k jednomu z konfigurace protokolu IP síťového rozhraní.

Nastavení musí být povolena pro každé síťové rozhraní, který je připojen k virtuálnímu počítači, který přijímá provoz, který virtuální počítač je potřeba předávat. Virtuální počítač může směrovat provoz, zda má několik síťových rozhraní nebo jedno síťové rozhraní připojené k němu. Nastavení aplikace Azure při předávání IP virtuálního počítače musí také spuštění aplikace dokáže přesměrovat provoz, jako jsou brány firewall, optimalizace sítě WAN a aplikace pro vyrovnávání zatížení. Virtuální počítač je spuštěný síťové aplikace, virtuální počítač se často označuje jako síťové virtuální zařízení. Připraveno k nasazení síťových virtuálních zařízení v seznamu můžete zobrazit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Předávání IP se obvykle používá u trasy definované uživatelem. Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem](virtual-networks-udr-overview.md).

1. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *síťová rozhraní*. Když **síťová rozhraní** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte síťové rozhraní, které chcete povolit nebo zakázat pro předávání IP.
3. Vyberte **konfigurací protokolu IP** v **nastavení** oddílu.
4. Vyberte **povoleno** nebo **zakázané** (výchozí nastavení) Chcete-li změnit nastavení.
5. Vyberte **Uložit**.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="change-subnet-assignment"></a>Změnit přidružení podsítě

Můžete změnit podsíť, ale ne virtuální síti, přiřazené k síťovému rozhraní.

1. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *síťová rozhraní*. Když **síťová rozhraní** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte, kterou chcete změnit přiřazení podsíť pro síťové rozhraní.
3. Vyberte **konfigurací protokolu IP** pod **nastavení**. Pokud všechny privátní IP adresy pro všechny konfigurace protokolu IP uveden **(statické)** vedle sebe, je nutné změnit způsob přiřazení IP adresy na dynamické provedením následujících kroků. Všechny privátní IP adresy musí přiřazované pomocí metody dynamického přidělování změnit přiřazení podsíť pro síťové rozhraní. Pokud adresy se přiřazují s dynamickou metodu, pokračujte krokem 5. Pokud žádné adresy protokolu IPv4 jsou přiřazované pomocí metody statického přidělování, změňte metodu přiřazení na dynamické následujících kroků:
    - Vyberte konfigurace IP adresy, kterou chcete změnit způsob přiřazování adresy protokolu IPv4 pro ze seznamu konfigurací protokolu IP.
    - Vyberte **dynamické** pro privátní IP adresu **přiřazení** metody. Nejde přiřadit adresu protokolu IPv6 s metodou statického přidělování.
    - Vyberte **Uložit**.
4. Vyberte podsíť, které chcete přesunout síťové rozhraní z **podsítě** rozevíracího seznamu.
5. Vyberte **Uložit**. Nové dynamické adresy jsou přiřazené, z rozsahu adres podsítě pro nové podsítě. Po přiřazení síťového rozhraní na novou podsíť, můžete přiřadit statickou IPv4 adresu z rozsahu adres nové podsítě, pokud se rozhodnete. Další informace o přidání, změna a odebírání adres IP pro síťové rozhraní, naleznete v tématu [Správa IP adres](virtual-network-network-interface-addresses.md).

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Přidat nebo odebrat ze skupiny zabezpečení aplikací

Pouze můžete přidat síťové rozhraní nebo odebrat ze skupiny zabezpečení aplikací pomocí portálu, pokud je síťové rozhraní připojené k virtuálnímu počítači síťového rozhraní. Můžete přidat síťové rozhraní pomocí Powershellu nebo rozhraní příkazového řádku Azure nebo síťové rozhraní odebrat skupinu zabezpečení aplikace, zda síťové rozhraní je připojené k virtuálnímu počítači, nebo ne. Další informace o [skupiny zabezpečení aplikací](security-overview.md#application-security-groups) a jak [vytvořit skupinu zabezpečení aplikace](manage-network-security-group.md).

1. V *hledat prostředky, služby a dokumenty* pole v horní části portálu, začněte psát název virtuálního počítače, který má síťové rozhraní, které chcete přidat nebo odebrat ze skupiny zabezpečení aplikace. Když se ve výsledcích hledání zobrazí název vašeho virtuálního počítače, vyberte ji.
2. V části **NASTAVENÍ** vyberte **Sítě**.  Vyberte **konfigurace skupin zabezpečení aplikací**vyberte skupiny zabezpečení aplikací, které chcete přidat síťové rozhraní, nebo zrušte výběr skupiny zabezpečení aplikací, které chcete odstranit síťové rozhraní, a pak vyberte **Uložit**. Jenom síťová rozhraní, které existují ve stejné virtuální síti je přidat do stejné skupiny zabezpečení aplikace. Skupiny zabezpečení aplikací musí existovat ve stejném umístění jako síťové rozhraní.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Přidružení nebo zrušit přidružení skupiny zabezpečení sítě

1. Do vyhledávacího pole v horní části portálu zadejte *síťová rozhraní* do vyhledávacího pole. Když **síťová rozhraní** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte v seznamu a přidružte skupinu zabezpečení sítě pro síťové rozhraní, nebo zrušit přidružení skupiny zabezpečení sítě z.
3. Vyberte **skupinu zabezpečení sítě** pod **nastavení**.
4. Vyberte **Upravit**.
5. Vyberte **skupinu zabezpečení sítě** a pak vyberte skupinu zabezpečení sítě, kterou chcete přidružit k síťovému rozhraní, nebo vyberte **žádný**, zrušení přidružení skupiny zabezpečení sítě.
6. Vyberte **Uložit**.

**Příkazy**

- Azure CLI: [az network nic update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)

## <a name="delete-a-network-interface"></a>Odstranit síťové rozhraní

Síťové rozhraní můžete odstranit tak dlouho, dokud není připojen k virtuálnímu počítači. Pokud je síťové rozhraní připojené k virtuálnímu počítači, je nutné nejprve umístit virtuální počítač ve stavu Zastaveno (přidělení zrušeno), potom Odpojit síťové rozhraní z virtuálního počítače. Chcete-li odpojit síťové rozhraní z virtuálního počítače, proveďte kroky v [Odpojit síťové rozhraní z virtuálního počítače](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Pokud se jedná pouze síťové rozhraní připojené k virtuálnímu počítači, ale není možné odpojit síťové rozhraní z virtuálního počítače. Virtuální počítač musí mít vždy alespoň jedno síťové rozhraní k němu připojená. Odstranění virtuálního počítače odpojí všechna síťová rozhraní připojená k ní, ale nedojde k odstranění síťových rozhraní.

1. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *síťová rozhraní*. Když **síťová rozhraní** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte **...**  na pravé straně síťového rozhraní, které chcete odstranit ze seznamu síťových rozhraní.
3. Vyberte **Odstranit**.
4. Vyberte **Ano** k potvrzení odstranění síťového rozhraní.

Při odstranění síťového rozhraní se vydávají jakékoli MAC nebo IP adresy přiřazené k němu.

**Příkazy**

|Nástroj|Příkaz|
|---|---|
|Rozhraní příkazového řádku|[AZ network nic delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Vyřešte problémy s připojením

Pokud nemůžete komunikovat na nebo z virtuálního počítače, pravidel zabezpečení skupiny zabezpečení sítě nebo trasy s platností pro síťové rozhraní, může být příčinou problému. Máte následující možnosti pro řešení těchto potíží:

### <a name="view-effective-security-rules"></a>Zobrazit platná pravidla zabezpečení

Platná pravidla zabezpečení pro každé síťové rozhraní připojené k virtuálnímu počítači jsou kombinací pravidla, které jste vytvořili skupinu zabezpečení sítě a [výchozích pravidlech zabezpečení](security-overview.md#default-security-rules). Principy platná pravidla zabezpečení pro síťové rozhraní můžete zjistit, proč nemůžete pro komunikaci na nebo z virtuálního počítače. Můžete zobrazit platná pravidla pro síťové rozhraní, který je připojený k běžícímu virtuálnímu počítači.

1. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, které chcete zobrazit platná pravidla zabezpečení pro. Pokud si nejste jisti názvem virtuálního počítače, zadejte *virtuálních počítačů* do vyhledávacího pole. Když **virtuálních počítačů** zobrazí ve výsledcích hledání, vyberte ji a potom vyberte virtuální počítač ze seznamu.
2. Vyberte **sítě** pod **nastavení**.
3. Vyberte název síťového rozhraní.
4. Vyberte **platná pravidla zabezpečení** pod **podpora a řešení potíží**.
5. Projděte si seznam platná pravidla zabezpečení a určit, pokud existují správné pravidla pro požadovanou příchozí a odchozí komunikaci. Další informace o co se zobrazí v seznamu v [přehled skupin zabezpečení sítě](security-overview.md).

Ověření toku protokolu IP, že funkce služby Azure Network Watcher také vám pomohou určit, pokud jsou pravidla zabezpečení znemožňuje komunikaci mezi virtuálním počítačem a koncový bod. Další informace najdete v tématu [ověření toku protokolu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Příkazy**

- Azure CLI: [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup) 

### <a name="view-effective-routes"></a>Zobrazení efektivních tras

Efektivní trasy pro síťová rozhraní připojená k virtuálnímu počítači jsou kombinací výchozí trasy, všechny trasy, které jste vytvořili a všechny trasy rozšířena z místní sítě přes protokol BGP prostřednictvím brány virtuální sítě Azure. Principy efektivní trasy pro síťové rozhraní můžete zjistit, proč nemůžete pro komunikaci na nebo z virtuálního počítače. Můžete zobrazit efektivní trasy pro každé síťové rozhraní, který je připojený k běžícímu virtuálnímu počítači.

1. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, které chcete zobrazit platná pravidla zabezpečení pro. Pokud si nejste jisti názvem virtuálního počítače, zadejte *virtuálních počítačů* do vyhledávacího pole. Když **virtuálních počítačů** zobrazí ve výsledcích hledání, vyberte ji a potom vyberte virtuální počítač ze seznamu.
2. Vyberte **sítě** pod **nastavení**.
3. Vyberte název síťového rozhraní.
4. Vyberte **efektivní trasy** pod **podpora a řešení potíží**.
5. Projděte si seznam efektivní trasy a určete, zda existují správné trasy pro požadovanou příchozí a odchozí komunikaci. Další informace o co se zobrazí v seznamu v [Přehled směrování](virtual-networks-udr-overview.md).

Funkce dalšího segmentu směrování služby Azure Network Watcher můžete také vám pomohou určit Pokud trasy brání komunikaci mezi virtuálním počítačem a koncový bod. Další informace najdete v tématu [směrování](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Příkazy**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable)

## <a name="permissions"></a>Oprávnění

K provádění úloh na síťová rozhraní, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena odpovídající oprávnění uvedená v následující tabulce:

| Akce                                                                     | Název                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Získání síťového rozhraní                                     |
| Microsoft.Network/networkInterfaces/write                                  | Vytvořit nebo aktualizovat síťové rozhraní                        |
| Microsoft.Network/networkInterfaces/join/action                            | Připojit síťové rozhraní k virtuálnímu počítači           |
| Microsoft.Network/networkInterfaces/delete                                 | Odstranit síťové rozhraní                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Připojte se k prostředku k síťovému rozhraní prostřednictvím aktualizace servi...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Získejte aktuálně platná směrovací tabulka síťových rozhraní               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Získat skupiny efektivní zabezpečení rozhraní sítě           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Získejte nástroje pro vyrovnávání zatížení síťové rozhraní                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Získejte přidružení služby                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Vytvořit nebo aktualizovat přidružení služby                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Odstraňte přidružení služby                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Ověření služby přidružení                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Získat konfiguraci protokolu IP síťového rozhraní                    |

## <a name="next-steps"></a>Další postup

- Vytvoření Virtuálního počítače s více síťových adaptérů pomocí funkce [rozhraní příkazového řádku Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [prostředí PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Vytvořit samostatný virtuální počítač síťovou kartu s více IPv4 adresy, které používají [rozhraní příkazového řádku Azure](virtual-network-multiple-ip-addresses-cli.md) nebo [prostředí PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Vytvořit jeden virtuální počítač síťovou kartu s privátní IPv6 adresu (za služby Azure Load Balancer) using [rozhraní příkazového řádku Azure](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), nebo [šablony Azure Resource Manageru](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Vytvoření síťových rozhraní pomocí [PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablony Resource Manageru](template-samples.md)
- Vytvoření a použití [Azure policy](policy-samples.md) pro virtuální sítě
