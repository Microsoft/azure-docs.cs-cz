---
title: Vytvořit, změnit nebo odstranit skupinu zabezpečení sítě Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit, změnit nebo odstranit skupinu zabezpečení sítě.
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
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: 774d429b960e56e998e9d964b9de1e6461816734
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901693"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Vytvořit, změnit nebo odstranit skupinu zabezpečení sítě

Pravidla zabezpečení ve skupinách zabezpečení sítě umožňují filtrovat typ síťového provozu, který může téct do a z podsítě virtuální sítě a síťová rozhraní. Pokud nejste obeznámeni s použitím skupin zabezpečení sítě, přečtěte si téma [přehled skupin zabezpečení sítě](security-overview.md) Další informace o nich a dokončení [filtrování síťového provozu](tutorial-filter-network-traffic.md) kurzu získáte nějaké zkušenosti s síť skupiny zabezpečení.

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v jakékoli části tohoto článku, proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud používáte příkazy prostředí PowerShell k dokončení úkolů v tomto článku, buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje použití Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.

Musí mít účet přihlásit nebo připojit k Azure s přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

## <a name="work-with-network-security-groups"></a>Práce se skupinami zabezpečení sítě

Můžete vytvořit, [zobrazit všechny](#view-all-network-security-groups), [zobrazit podrobnosti o](#view-details-of-a-network-security-group), [změnit](#change-a-network-security-group), a [odstranit](#delete-a-network-security-group) skupinu zabezpečení sítě. Můžete také [přidružení nebo zrušit přidružení](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) skupinu zabezpečení sítě síťového rozhraní nebo podsítě.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Platí omezení na tom, kolik síťové skupiny zabezpečení, které můžete vytvořit na umístění a předplatném Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V levém horním rohu portálu, vyberte **+ vytvořit prostředek**.
2. Vyberte **sítě**a pak vyberte **skupinu zabezpečení sítě**.
3. Zadejte **název** pro skupinu zabezpečení sítě, vyberte vaše **předplatné**, vytvořte nový **skupiny prostředků**, nebo vyberte existující skupinu prostředků, vyberte **Umístění**a pak vyberte **vytvořit**.

**Příkazy**

- Azure CLI: [vytvořit az network nsg](/cli/azure/network/nsg#az-network-nsg-create)
- Prostředí PowerShell: [nové AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Zobrazení všech skupin zabezpečení sítě

Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě*. Když **skupiny zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ji. Skupiny zabezpečení sítě, které existují ve vašem předplatném jsou uvedeny.

**Příkazy**

- Azure CLI: [az network nsg seznamu](/cli/azure/network/nsg#az-network-nsg-list)
- Prostředí PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Zobrazení podrobností o skupinu zabezpečení sítě

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě*. Když **skupiny zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte skupinu zabezpečení sítě v seznamu, který chcete zobrazit podrobnosti. V části **nastavení** můžete zobrazit **příchozí pravidla zabezpečení** a **odchozí pravidla zabezpečení**, **síťová rozhraní** a  **Podsítě** je skupina zabezpečení sítě přidružená k. Můžete také povolit nebo zakázat **diagnostické protokoly** a zobrazení **platná pravidla zabezpečení**. Další informace najdete v tématu [diagnostické protokoly](virtual-network-nsg-manage-log.md) a [zobrazit platná pravidla zabezpečení](diagnose-network-traffic-filter-problem.md).
3. Další informace o běžných nastavení Azure uvedené, naleznete v následujících článcích:
    *   [Protokol aktivit](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Řízení přístupu (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Značky](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Zámky](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automatizační skript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Příkazy**

- Azure CLI: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- Prostředí PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Změnit skupinu zabezpečení sítě

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě* do vyhledávacího pole. Když **skupiny zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte skupinu zabezpečení sítě, kterou chcete změnit. Výčet nejběžnějších změn jsou [přidání](#create-a-security-rule) nebo [odebrání](#delete-a-security-rule) pravidla zabezpečení a [Associating nebo ruší se přidružení skupiny zabezpečení sítě na nebo z podsíti nebo síťovému rozhraní](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Příkazy**

- Azure CLI: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- Prostředí PowerShell: [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Přidružení nebo zrušit přidružení skupiny zabezpečení sítě na nebo z podsíti nebo síťovému rozhraní

Přidružení skupiny zabezpečení sítě k nebo zrušit přidružení skupiny zabezpečení sítě k síťovému rozhraní, naleznete v tématu [přidružení skupiny zabezpečení sítě k nebo zrušit přidružení skupiny zabezpečení sítě k síťovému rozhraní](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Přidružení skupiny zabezpečení sítě k nebo zrušit přidružení skupiny zabezpečení sítě z podsítě, najdete v článku [změnit nastavení podsítě](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Odstranit skupinu zabezpečení sítě

Pokud je skupina zabezpečení sítě přidružené k podsítím nebo síťová rozhraní, nelze odstranit. [Zrušit přidružení](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) ze všech podsítí a síťových rozhraní než se pokusíte odstranit skupinu zabezpečení sítě.

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě* do vyhledávacího pole. Když **skupiny zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte skupinu zabezpečení sítě, kterou chcete odstranit ze seznamu.
3. Vyberte **odstranit**a pak vyberte **Ano**.

**Příkazy**

- Azure CLI: [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- Prostředí PowerShell: [Remove-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroupp) 

## <a name="work-with-security-rules"></a>Práce s pravidly zabezpečení

Skupina zabezpečení sítě obsahuje nula nebo více pravidel zabezpečení. Můžete vytvořit, [zobrazit všechny](#view-all-security-rules), [zobrazit podrobnosti o](#view-details-of-a-security-rule), [změnit](#change-a-security-rule), a [odstranit](#delete-a-security-rule) pravidlo zabezpečení.

### <a name="create-a-security-rule"></a>Vytvořit pravidlo zabezpečení

Platí omezení na tom, kolik pravidel na skupiny zabezpečení sítě můžete vytvořit pro jedno umístění a předplatném Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě* do vyhledávacího pole. Když **skupiny zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte ze seznamu, který chcete přidat pravidlo zabezpečení pro skupinu zabezpečení sítě.
3. Vyberte **příchozí pravidla zabezpečení** pod **nastavení**. Několik existujících pravidel jsou uvedeny. Některé z pravidel, kterou jste možná přidali. Po vytvoření skupiny zabezpečení sítě se v něm vytvoří několik výchozích pravidel zabezpečení. Další informace najdete v tématu [výchozích pravidlech zabezpečení](security-overview.md#default-security-rules).  Nelze odstranit výchozí pravidla zabezpečení, ale je můžete přepsat pomocí pravidel, která mají vyšší prioritu.
4. <a name = "security-rule-settings"></a>Vyberte **+ přidat**.  Vyberte a přidejte hodnoty pro následující nastavení a pak vyberte **OK**:
    
    |Nastavení  |Hodnota  |Podrobnosti  |
    |---------|---------|---------|
    |Zdroj     | Vyberte **jakékoli**, **skupiny zabezpečení aplikací**, **IP adresy**, nebo **značka služby** pro pravidla zabezpečení příchozích dat. Pokud vytváříte odchozí pravidlo zabezpečení, možnosti jsou stejné jako u možnosti **cílové**.       | Pokud vyberete **skupiny zabezpečení aplikací**, vyberte jednu nebo více stávajícím aplikace zabezpečení skupiny, které existují ve stejné oblasti jako síťové rozhraní. Zjistěte, jak [vytvořit skupinu zabezpečení aplikace](#create-an-application-security-group). Pokud vyberete **skupiny zabezpečení aplikací** pro obě **zdroj** a **cílové**, síťová rozhraní v obou skupinách zabezpečení aplikací musí být ve stejném virtuální sítě. Pokud vyberete **IP adresy**, pak zadejte **zdrojové IP adresy/rozsahy CIDR**. Můžete určit jednu hodnotu nebo seznam oddělený čárkami obsahující více hodnot. Příklad více hodnot je 10.0.0.0/16, 192.188.1.1. Existují omezení počtu hodnot, které určíte. Zobrazit [omezeních Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) podrobnosti. Pokud vyberete **značka služby**, vyberte jednu značku služby. Značka služby je předdefinovaný identifikátor pro určité kategorie IP adres. Další informace o dostupnou službu značky a co jednotlivé značky představuje, najdete v článku [značky služeb](security-overview.md#service-tags). Pokud IP adresa, kterou zadáte je přiřazený k virtuálnímu počítači Azure, ujistěte se, že zadáte privátní IP adresa, ne přiřazenou veřejnou IP adresu virtuálnímu počítači. Po Azure se přeloží veřejnou IP adresu privátní IP adresu pro pravidla zabezpečení příchozích dat a předtím, než Azure přeloží privátní IP adresu na veřejnou IP adresu pro odchozí pravidla se zpracovávají pravidla zabezpečení. Další informace o veřejných a privátních IP adresách v Azure najdete v tématu [typy IP adres](virtual-network-ip-addresses-overview-arm.md).        |
    |Rozsahy zdrojových portů     | Zadejte jeden port, třeba 80, rozsah portů, třeba 1024 – 65535, nebo seznam čárkami oddělených samostatných portů nebo rozsahů portů, třeba 80, 1024 – 65535. Zadejte hvězdičku a povolit přenosy přes libovolný port. | Porty a rozsahy adres zadejte, jaký provoz portů je povolen nebo odepřen pravidlem. Existují omezení počtu porty, které určíte. Zobrazit [omezeních Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) podrobnosti.  |
    |Cíl     | Vyberte **jakékoli**, **skupiny zabezpečení aplikací**, **IP adresy**, nebo **virtuální sítě** pro pravidla zabezpečení příchozích dat. Pokud vytváříte odchozí pravidlo zabezpečení, možnosti jsou stejné jako u možnosti **zdroj**.        | Pokud vyberete **skupiny zabezpečení aplikací** pak musíte vybrat minimálně jeden existující skupiny zabezpečení aplikací, které existují ve stejné oblasti jako síťové rozhraní. Zjistěte, jak [vytvořit skupinu zabezpečení aplikace](#create-an-application-security-group). Pokud vyberete **skupiny zabezpečení aplikací**, vyberte jeden existující skupiny zabezpečení aplikací, který existuje ve stejné oblasti jako síťové rozhraní. Pokud vyberete **IP adresy**, pak zadejte **cílové IP adresy/rozsahy CIDR**. Podobně jako **zdroj** a **zdrojové IP adresy/rozsahy CIDR**, můžete určit jeden nebo více adres nebo rozsahy a existují omezení počtu můžete zadat. Výběr **virtuální síť**, což je značka služby, znamená, že provoz je povolený pro všechny IP adresy v rámci adresního prostoru virtuální sítě. Pokud IP adresa, kterou zadáte je přiřazený k virtuálnímu počítači Azure, ujistěte se, že zadáte privátní IP adresa, ne přiřazenou veřejnou IP adresu virtuálnímu počítači. Po Azure se přeloží veřejnou IP adresu privátní IP adresu pro pravidla zabezpečení příchozích dat a předtím, než Azure přeloží privátní IP adresu na veřejnou IP adresu pro odchozí pravidla se zpracovávají pravidla zabezpečení. Další informace o veřejných a privátních IP adresách v Azure najdete v tématu [typy IP adres](virtual-network-ip-addresses-overview-arm.md).        |
    |Rozsahy cílových portů     | Zadejte jednu hodnotu, nebo čárkami oddělený seznam hodnot. | Podobně jako **zdrojové rozsahy portů**, můžete určit jeden nebo více porty a rozsahy a existují omezení počtu můžete zadat. |
    |Protocol (Protokol)     | Vyberte **jakékoli**, **TCP**, nebo **UDP**.        |         |
    |Akce     | Vyberte **povolit** nebo **Odepřít**.        |         |
    |Priorita     | Zadejte hodnotu mezi 100-4096, který je jedinečný pro všechna pravidla zabezpečení v rámci skupiny zabezpečení sítě. |Pravidla se zpracovávají v pořadí podle priority. Čím nižší je číslo, tím vyšší je priorita. Doporučujeme ponechat mezeru mezi čísel priority při vytváření pravidel, jako jsou 100, 200, 300. Opuštění mezery díky tomu snadněji k přidání pravidel v budoucnu, které budete muset provést vyšší nebo nižší, než mají existující pravidla.         |
    |Název     | Jedinečný název pravidla v rámci skupiny zabezpečení sítě.        |  Název může obsahovat maximálně 80 znaků. Musí začínat písmenem nebo číslicí, končit písmenem, číslicí nebo podtržítkem a může obsahovat jenom písmena, čísla, podtržítka, tečky nebo spojovníky.       |
    |Popis     | Volitelný popis.        |         |

**Příkazy**

- Azure CLI: [az network nsg pravidlo vytváření](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- Prostředí PowerShell: [nové AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Zobrazit všechna pravidla zabezpečení

Skupina zabezpečení sítě obsahuje nula nebo více pravidel. Další informace o informace při zobrazení pravidel, naleznete v tématu [přehled skupin zabezpečení sítě](security-overview.md).

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě*. Když **skupiny zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte ze seznamu, který chcete zobrazit pravidla pro skupiny zabezpečení sítě.
3. Vyberte **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení** pod **nastavení**.

Seznam obsahuje všechna pravidla, které jste vytvořili a skupinu zabezpečení sítě [výchozích pravidlech zabezpečení](security-overview.md#default-security-rules).

**Příkazy**

- Azure CLI: [az network nsg pravidlo seznamu](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- Prostředí PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Zobrazení podrobností pravidla zabezpečení

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě*. Když **skupiny zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte skupinu zabezpečení sítě, které chcete zobrazit podrobnosti pro pravidlo zabezpečení.
3. Vyberte **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení** pod **nastavení**.
4. Vyberte pravidlo, které chcete zobrazit podrobnosti. Podrobné vysvětlení všech nastavení najdete v tématu [nastavení pravidla zabezpečení](#security-rule-settings).

**Příkazy**

- Azure CLI: [az network nsg pravidlo show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- Prostředí PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Změna pravidla zabezpečení

1. Proveďte kroky v [zobrazit podrobnosti o pravidlo zabezpečení](#view-details-of-a-security-rule).
2. Změňte nastavení podle potřeby a potom vyberte **Uložit**. Podrobné vysvětlení všech nastavení najdete v tématu [nastavení pravidla zabezpečení](#security-rule-settings).

**Příkazy**

- Azure CLI: [az network nsg pravidlo aktualizace](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- Prostředí PowerShell: [Set AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Odstranit pravidlo zabezpečení

1. Proveďte kroky v [zobrazit podrobnosti o pravidlo zabezpečení](#view-details-of-a-security-rule).
2. Vyberte **odstranit**a pak vyberte **Ano**.

**Příkazy**

- Azure CLI: [az network nsg pravidlo odstranění](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- Prostředí PowerShell: [AzureRmSecurityRuleConfig odebrat](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Práce se skupinami zabezpečení aplikací

Skupinu zabezpečení aplikace obsahuje nula nebo více síťových rozhraní. Další informace najdete v tématu [skupiny zabezpečení aplikací](security-overview.md#application-security-groups). Všechna síťová rozhraní skupinu zabezpečení aplikace musí existovat ve stejné virtuální síti. Zjistěte, jak přidat síťové rozhraní do skupiny zabezpečení aplikací, najdete v článku [přidat síťové rozhraní do skupiny zabezpečení aplikací](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Vytvoření skupiny zabezpečení aplikací

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. V **Hledat na Marketplace** zadejte *skupiny zabezpečení aplikací*. Při **skupiny zabezpečení aplikací** se zobrazí ve výsledcích hledání vyberte ho vyberte **skupiny zabezpečení aplikací** znovu pod **všechno, co**a pak vyberte **Vytvořit**.
3. Zadejte nebo vyberte následující informace a pak vyberte **Vytvořit**:

    | Nastavení        | Hodnota                                                   |
    | ---            | ---                                                     |
    | Název           | Název musí být jedinečný v rámci skupiny prostředků.        |
    | Předplatné   | Vyberte své předplatné.                               |
    | Skupina prostředků | Vyberte existující skupinu prostředků nebo vytvořte novou. |
    | Umístění       | Vyberte umístění                                       |

**Příkazy**

- Azure CLI: [vytvořit asg az sítě](/cli/azure/network/asg#az-network-asg-create)
- Prostředí PowerShell: [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Zobrazení všech skupin zabezpečení aplikací

1. Vyberte **všechny služby** v pravém horním levém horním rohu webu Azure portal.
2. Zadejte *skupiny zabezpečení aplikací* v **všechny služby filtr** a potom vyberte **skupiny zabezpečení aplikací** když se zobrazí ve výsledcích hledání.

**Příkazy**

- Azure CLI: [az sítě asg seznamu](/cli/azure/network/asg#az-network-asg-list)
- Prostředí PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Zobrazit podrobnosti o skupině zabezpečení konkrétní aplikaci

1. Vyberte **všechny služby** v pravém horním levém horním rohu webu Azure portal.
2. Zadejte *skupiny zabezpečení aplikací* v **všechny služby filtr** a potom vyberte **skupiny zabezpečení aplikací** když se zobrazí ve výsledcích hledání.
3. Vyberte skupiny zabezpečení aplikací, které chcete zobrazit podrobnosti o.

**Příkazy**

- Azure CLI: [az sítě asg show](/cli/azure/network/asg#az-network-asg-show)
- Prostředí PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Změnit skupinu zabezpečení aplikace

1. Vyberte **všechny služby** v pravém horním levém horním rohu webu Azure portal.
2. Zadejte *skupiny zabezpečení aplikací* v **všechny služby filtr** a potom vyberte **skupiny zabezpečení aplikací** když se zobrazí ve výsledcích hledání.
3. Vyberte skupiny zabezpečení aplikací, kterou chcete změnit nastavení. Můžete přidat nebo odebrat značky, nebo přiřadit nebo odebrat oprávnění ke skupině zabezpečení aplikace.

- Azure CLI: [az sítě asg aktualizace](/cli/azure/network/asg#az-network-asg-update)
- Prostředí PowerShell: Rutiny žádná prostředí PowerShell.

### <a name="delete-an-application-security-group"></a>Odstranit skupinu zabezpečení aplikace

Nelze odstranit skupinu zabezpečení aplikace, pokud má všech síťových rozhraní v ní. Odeberte všechna síťová rozhraní ze skupiny zabezpečení aplikace buď změnou nastavení síťového rozhraní nebo odstranění síťových rozhraní. Podrobnosti najdete v tématu [přidat nebo odebrat síťové rozhraní ze skupin zabezpečení aplikací](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) nebo [odstranit síťové rozhraní](virtual-network-network-interface.md#delete-a-network-interface).

1. Vyberte **všechny služby** v pravém horním levém horním rohu webu Azure portal.
2. Zadejte *skupiny zabezpečení aplikací* v **všechny služby filtr** a potom vyberte **skupiny zabezpečení aplikací** když se zobrazí ve výsledcích hledání.
3. Vyberte skupiny zabezpečení aplikací, které chcete odstranit.
4. Vyberte **odstranit**a pak vyberte **Ano** se odstranit skupinu zabezpečení aplikace.

**Příkazy**

- Azure CLI: [odstranit asg az sítě](/cli/azure/network/asg#az-network-asg-delete)
- Prostředí PowerShell: [Remove-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>Oprávnění

K provádění úloh na skupiny zabezpečení sítě, pravidly zabezpečení a skupiny zabezpečení aplikací, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušná oprávnění uvedená v následujících tabulkách:

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

| Akce                                                        |   Název                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/ruleTables/read                             |   Načíst skupinu zabezpečení sítě                                          |
| Microsoft.Network/ruleTables/write                            |   Vytvořit nebo aktualizovat skupinu zabezpečení sítě                             |
| Microsoft.Network/ruleTables/delete                           |   Odstranit skupinu zabezpečení sítě                                       |
| Microsoft.Network/ruleTables/join/action                      |   Přidružení skupiny zabezpečení sítě k podsíti nebo síťovému rozhraní |
| Microsoft.Network/ruleTables/rules/read                       |   Získat pravidlo                                                            |
| Microsoft.Network/ruleTables/rules/write                      |   Vytvořit nebo aktualizovat pravidlo                                               |
| Microsoft.Network/ruleTables/rules/delete                     |   Odstranit pravidlo                                                         |
| Microsoft.Network/networkInterfaces/effectiveruleTable/action |   Načíst síťové rozhraní efektivní skupinu zabezpečení sítě              |
| Microsoft.Network/networkWatchers/nextHop/action              |   Získá další segment směrování z virtuálního počítače                                         |

### <a name="application-security-groups"></a>Skupiny zabezpečení aplikací

| Akce                                                                     | Název                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Připojte se k konfigurace protokolu IP pro skupinu zabezpečení aplikace|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Připojte se k pravidlo zabezpečení pro skupinu zabezpečení aplikace    |
| Microsoft.Network/applicationSecurityGroups/read                           | Získat skupinu zabezpečení aplikace                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Vytvořit nebo aktualizovat skupinu zabezpečení aplikace           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Odstranit skupinu zabezpečení aplikace                     |

## <a name="next-steps"></a>Další postup

- Vytvoření sítě nebo pomocí skupiny zabezpečení aplikací [PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablon Resource Manageru](template-samples.md)
- Vytvoření a použití [Azure policy](policy-samples.md) pro virtuální sítě
