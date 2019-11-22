---
title: Vytvoření, změna nebo odstranění skupiny zabezpečení sítě Azure
titlesuffix: Azure Virtual Network
description: Naučte se, jak vytvořit, změnit nebo odstranit skupinu zabezpečení sítě.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: afbe4aae0ac1296bfc4b2011069f9d81afed977f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273682"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Vytvoření, změna nebo odstranění skupiny zabezpečení sítě

Pravidla zabezpečení ve skupinách zabezpečení sítě umožňují filtrovat typ síťového provozu, který může přecházet do podsítí a síťových rozhraní virtuální sítě. Pokud nejste obeznámeni se skupinami zabezpečení sítě, přečtěte si článek [Přehled skupiny zabezpečení sítě](security-overview.md) , kde se dozvíte víc o těchto úlohách a o tom, jak získat zkušenosti se skupinami zabezpečení sítě v kurzu [filtrování síťového provozu](tutorial-filter-network-traffic.md) .

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku proveďte následující úlohy:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy prostředí PowerShell, buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento kurz vyžaduje Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, je také potřeba spustit `az login` a vytvořit připojení k Azure.

Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazena k příslušným akcím uvedeným v [oprávněních](#permissions).

## <a name="work-with-network-security-groups"></a>Práce se skupinami zabezpečení sítě

Můžete vytvořit, [Zobrazit všechny](#view-all-network-security-groups), [Zobrazit podrobnosti o](#view-details-of-a-network-security-group), [změnit](#change-a-network-security-group)a [Odstranit](#delete-a-network-security-group) skupinu zabezpečení sítě. Můžete taky [přidružit nebo oddělit](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) skupinu zabezpečení sítě od síťového rozhraní nebo podsítě.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Existuje omezení počtu skupin zabezpečení sítě, které můžete vytvořit pro každé umístění a předplatné Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.
2. Vyberte **sítě**a pak vyberte **Skupina zabezpečení sítě**.
3. Zadejte **název** skupiny zabezpečení sítě, vyberte své **předplatné**, vytvořte novou **skupinu prostředků**nebo vyberte existující skupinu prostředků, vyberte **umístění**a pak vyberte **vytvořit**.

**Příkaz**

- Azure CLI: [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Zobrazit všechny skupiny zabezpečení sítě

Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě*. Pokud se ve výsledcích hledání zobrazí **skupiny zabezpečení sítě** , vyberte ji. V seznamu jsou uvedené skupiny zabezpečení sítě, které existují ve vašem předplatném.

**Příkaz**

- Azure CLI: [AZ Network NSG list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Zobrazit podrobnosti skupiny zabezpečení sítě

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě*. Pokud se ve výsledcích hledání zobrazí **skupiny zabezpečení sítě** , vyberte ji.
2. V seznamu vyberte skupinu zabezpečení sítě, pro kterou chcete zobrazit podrobnosti. V části **Nastavení** můžete zobrazit **příchozí pravidla zabezpečení** a **odchozí pravidla zabezpečení**, k **síťovým rozhraním** a **podsítím** , ke kterým je skupina zabezpečení sítě přidružená. Můžete taky povolit nebo zakázat **diagnostické protokoly** a zobrazit **platná pravidla zabezpečení**. Další informace najdete v tématech [diagnostické protokoly](virtual-network-nsg-manage-log.md) a [zobrazení platných pravidel zabezpečení](diagnose-network-traffic-filter-problem.md).
3. Další informace o běžných nastaveních Azure uvedených v následujících článcích:
    *   [Protokol aktivit](../azure-monitor/platform/activity-logs-overview.md)
    *   [Řízení přístupu (IAM)](../role-based-access-control/overview.md)
    *   [Značky](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Počtu](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Skript Automation](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Příkaz**

- Azure CLI: [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Změna skupiny zabezpečení sítě

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě* do vyhledávacího pole. Pokud se ve výsledcích hledání zobrazí **skupiny zabezpečení sítě** , vyberte ji.
2. Vyberte skupinu zabezpečení sítě, kterou chcete změnit. Nejběžnější změny jsou [Přidání](#create-a-security-rule) nebo [Odebrání](#delete-a-security-rule) pravidel zabezpečení a [přidružení nebo ruší skupiny zabezpečení sítě k podsíti nebo síťovému rozhraní](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Příkaz**

- Azure CLI: [AZ Network NSG Update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Přidružení nebo zrušení přidružení skupiny zabezpečení sítě k podsíti nebo síťovému rozhraní

Pokud chcete přidružit skupinu zabezpečení sítě k nebo oddělit skupinu zabezpečení sítě od síťového rozhraní, přečtěte si téma [přidružení skupiny zabezpečení sítě k nebo odrušení skupiny zabezpečení sítě ze síťového rozhraní](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Chcete-li přidružit skupinu zabezpečení sítě k nebo oddělit skupinu zabezpečení sítě z podsítě, přečtěte si téma [Změna nastavení podsítě](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Odstraní skupinu zabezpečení sítě.

Pokud je skupina zabezpečení sítě přidružená k žádným podsítím nebo síťovým rozhraním, nelze ji odstranit. Před pokusem o odstranění zrušte přidružení skupiny zabezpečení sítě ze všech podsítí a síťových rozhraní.

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě* do vyhledávacího pole. Pokud se ve výsledcích hledání zobrazí **skupiny zabezpečení sítě** , vyberte ji.
2. V seznamu vyberte skupinu zabezpečení sítě, kterou chcete odstranit.
3. Vyberte **Odstranit**a pak vyberte **Ano**.

**Příkaz**

- Azure CLI: [AZ Network NSG Delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Práce s pravidly zabezpečení

Skupina zabezpečení sítě obsahuje nula nebo více pravidel zabezpečení. Můžete vytvořit, [Zobrazit všechny](#view-all-security-rules), [Zobrazit podrobnosti o](#view-details-of-a-security-rule), [změnit](#change-a-security-rule)a [Odstranit](#delete-a-security-rule) pravidlo zabezpečení.

### <a name="create-a-security-rule"></a>Vytvořit pravidlo zabezpečení

Existuje omezení, kolik pravidel na skupinu zabezpečení sítě může vytvořit v rámci umístění a předplatného Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě* do vyhledávacího pole. Pokud se ve výsledcích hledání zobrazí **skupiny zabezpečení sítě** , vyberte ji.
2. Vyberte skupinu zabezpečení sítě ze seznamu, do kterého chcete přidat pravidlo zabezpečení.
3. V části **Nastavení**vyberte **příchozí pravidla zabezpečení** . V seznamu je uvedeno několik stávajících pravidel. Některá z pravidel, která jste pravděpodobně nepřidali. Při vytvoření skupiny zabezpečení sítě se v ní vytvoří několik výchozích pravidel zabezpečení. Další informace najdete v tématu [výchozí pravidla zabezpečení](security-overview.md#default-security-rules).  Výchozí pravidla zabezpečení nemůžete odstranit, ale můžete je přepsat pravidly, která mají vyšší prioritu.
4. <a name = "security-rule-settings"></a>Vyberte **+ Přidat**.  Vyberte nebo přidejte hodnoty pro následující nastavení a pak vyberte **OK**:
    
    |Nastavení  |Hodnota  |Podrobnosti  |
    |---------|---------|---------|
    |Zdroj     | Pro příchozí pravidla zabezpečení vyberte **libovolnou** **skupinu zabezpečení aplikace**, **IP adresu**nebo **značku služby** . Pokud vytváříte odchozí pravidlo zabezpečení, budou tyto možnosti stejné jako možnosti uvedené pro **cíl**.       | Pokud vyberete možnost **Skupina zabezpečení aplikace**, vyberte jednu nebo více existujících skupin zabezpečení aplikace, které existují ve stejné oblasti jako síťové rozhraní. Naučte se [vytvořit skupinu zabezpečení aplikace](#create-an-application-security-group). Pokud vyberete **skupinu zabezpečení aplikace** pro **zdroj** i **cíl**, síťová rozhraní v obou skupinách zabezpečení aplikace musí být ve stejné virtuální síti. Pokud vyberete možnost **IP adresy**, zadejte **zdrojové IP adresy/rozsahy CIDR**. Můžete zadat jednu hodnotu nebo seznam oddělený čárkami s více hodnotami. Příkladem více hodnot je 10.0.0.0/16, 192.188.1.1. Existují omezení počtu hodnot, které můžete zadat. Podrobnosti najdete v tématu [omezení Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . Pokud vyberete možnost **značka služby**, pak vyberte jednu značku služby. Označení služby je předdefinovaný identifikátor pro kategorii IP adres. Další informace o dostupných značkách služby a o tom, co jednotlivé značky představují, najdete v tématu [značky služeb](security-overview.md#service-tags). Pokud je zadaná IP adresa přiřazená k virtuálnímu počítači Azure, ujistěte se, že zadáte privátní IP adresu, nikoli veřejnou IP adresu přiřazenou k virtuálnímu počítači. Pravidla zabezpečení se zpracovávají po Azure překládá veřejnou IP adresu na privátní IP adresu pro příchozí pravidla zabezpečení a předtím, než Azure převede privátní IP adresu na veřejnou IP adresu pro odchozí pravidla. Další informace o veřejných a privátních IP adresách v Azure najdete v tématu [typy IP adres](virtual-network-ip-addresses-overview-arm.md).        |
    |Rozsahy zdrojových portů     | Zadejte jeden port, například 80, rozsah portů, jako je například 1024-65535 nebo seznam oddělený čárkami a rozsahy portů, například 80, 1024-65535. Zadejte hvězdičku pro povolení provozu na jakémkoli portu. | Porty a rozsahy určují, které porty jsou povolené nebo zakázané tímto pravidlem. Existují omezení počtu portů, které můžete zadat. Podrobnosti najdete v tématu [omezení Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .  |
    |Cíl     | Pro odchozí pravidla zabezpečení vyberte **libovolnou** **skupinu zabezpečení aplikace**, **IP adresu**nebo **Virtual Network** . Pokud vytváříte příchozí pravidlo zabezpečení, jsou možnosti stejné jako možnosti uvedené pro **zdroj**.        | Pokud vyberete možnost **Skupina zabezpečení aplikace** , musíte vybrat jednu nebo více existujících skupin zabezpečení aplikace, které existují ve stejné oblasti jako síťové rozhraní. Naučte se [vytvořit skupinu zabezpečení aplikace](#create-an-application-security-group). Pokud vyberete možnost **Skupina zabezpečení aplikace**, vyberte jednu existující skupinu zabezpečení aplikace, která existuje ve stejné oblasti jako síťové rozhraní. Pokud vyberete možnost **IP adresy**, pak určete **cílové IP adresy/rozsahy CIDR**. Podobně jako **zdrojové** a **zdrojové IP adresy/rozsahy CIDR**můžete zadat jednu nebo několik adres nebo rozsahů a omezení pro číslo, které můžete zadat. Výběr **virtuální sítě**, což je značka služby, znamená, že provoz je povolený pro všechny IP adresy v adresním prostoru virtuální sítě. Pokud je zadaná IP adresa přiřazená k virtuálnímu počítači Azure, ujistěte se, že zadáte privátní IP adresu, nikoli veřejnou IP adresu přiřazenou k virtuálnímu počítači. Pravidla zabezpečení se zpracovávají po Azure překládá veřejnou IP adresu na privátní IP adresu pro příchozí pravidla zabezpečení a předtím, než Azure převede privátní IP adresu na veřejnou IP adresu pro odchozí pravidla. Další informace o veřejných a privátních IP adresách v Azure najdete v tématu [typy IP adres](virtual-network-ip-addresses-overview-arm.md).        |
    |Rozsahy cílových portů     | Zadejte jednu hodnotu nebo seznam hodnot oddělených čárkami. | Podobně jako u **rozsahů zdrojového portu**můžete zadat jeden nebo několik portů a rozsahů a omezení pro počet, který můžete zadat. |
    |Protocol (Protokol)     | Vyberte **libovolnou**položku, **TCP**, **UDP** nebo **ICMP**.        |         |
    |Akce     | Vyberte **Povolit** nebo **Odepřít**.        |         |
    |Priorita     | Zadejte hodnotu mezi 100-4096, která je jedinečná pro všechna pravidla zabezpečení v rámci skupiny zabezpečení sítě. |Pravidla se zpracovávají v pořadí podle priority. Čím nižší číslo, tím vyšší Priorita. Při vytváření pravidel se doporučuje opustit mezeru mezi čísly priority, například 100, 200, 300. Ponechání mezer usnadňuje přidání pravidel do budoucna, že možná budete muset udělat vyšší nebo nižší než stávající pravidla.         |
    |Název     | Jedinečný název pro pravidlo v rámci skupiny zabezpečení sítě.        |  Název může mít až 80 znaků. Musí začínat písmenem nebo číslicí, končit písmenem, číslicí nebo podtržítkem a může obsahovat jenom písmena, číslice, podtržítka, tečky nebo spojovníky.       |
    |Popis     | Volitelný popis.        |         |

**Příkaz**

- Azure CLI: [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Zobrazit všechna pravidla zabezpečení

Skupina zabezpečení sítě obsahuje nula nebo více pravidel. Další informace o informacích uvedených při zobrazení pravidel najdete v tématu [Přehled skupin zabezpečení sítě](security-overview.md).

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě*. Pokud se ve výsledcích hledání zobrazí **skupiny zabezpečení sítě** , vyberte ji.
2. V seznamu vyberte skupinu zabezpečení sítě, pro kterou chcete zobrazit pravidla.
3. V části **Nastavení**vyberte **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení** .

Seznam obsahuje všechna vámi vytvořená pravidla a [výchozí pravidla zabezpečení](security-overview.md#default-security-rules)skupiny zabezpečení sítě.

**Příkaz**

- Azure CLI: [AZ Network NSG Rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Zobrazení podrobností pravidla zabezpečení

1. Do vyhledávacího pole v horní části portálu zadejte *skupiny zabezpečení sítě*. Pokud se ve výsledcích hledání zobrazí **skupiny zabezpečení sítě** , vyberte ji.
2. Vyberte skupinu zabezpečení sítě, pro kterou chcete zobrazit podrobnosti pravidla zabezpečení pro.
3. V části **Nastavení**vyberte **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení** .
4. Vyberte pravidlo, pro které chcete zobrazit podrobnosti. Podrobné vysvětlení všech nastavení najdete v tématu [Nastavení pravidel zabezpečení](#security-rule-settings).

**Příkaz**

- Azure CLI: [AZ Network NSG Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Změna pravidla zabezpečení

1. Proveďte kroky v části [zobrazení podrobností pravidla zabezpečení](#view-details-of-a-security-rule).
2. Změňte nastavení podle potřeby a pak vyberte **Uložit**. Podrobné vysvětlení všech nastavení najdete v tématu [Nastavení pravidel zabezpečení](#security-rule-settings).

**Příkaz**

- Azure CLI: [AZ Network NSG Rule Update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Odstraní pravidlo zabezpečení.

1. Proveďte kroky v části [zobrazení podrobností pravidla zabezpečení](#view-details-of-a-security-rule).
2. Vyberte **Odstranit**a pak vyberte **Ano**.

**Příkaz**

- Azure CLI: [AZ Network NSG Rule Delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Práce se skupinami zabezpečení aplikace

Skupina zabezpečení aplikace obsahuje nula nebo více síťových rozhraní. Další informace najdete v tématu [skupiny zabezpečení aplikací](security-overview.md#application-security-groups). Všechna síťová rozhraní ve skupině zabezpečení aplikace musí existovat ve stejné virtuální síti. Informace o tom, jak přidat síťové rozhraní do skupiny zabezpečení aplikace, najdete v tématu [Přidání síťového rozhraní do skupiny zabezpečení aplikace](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Vytvořit skupinu zabezpečení aplikace

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Do pole **Hledat na Marketplace** zadejte *Skupina zabezpečení aplikace*. Jakmile se ve výsledcích hledání zobrazí **Skupina zabezpečení aplikace**, vyberte ji. V části **Všechno** vyberte **Skupina zabezpečení aplikace** a pak vyberte **Vytvořit**.
3. Zadejte nebo vyberte následující informace a pak vyberte **Vytvořit**:

    | Nastavení        | Hodnota                                                   |
    | ---            | ---                                                     |
    | Název           | Název musí být v rámci skupiny prostředků jedinečný.        |
    | Předplatné   | Vyberte své předplatné.                               |
    | Skupina prostředků | Vyberte existující skupinu prostředků nebo vytvořte novou. |
    | Umístění       | Vybrat umístění                                       |

**Příkaz**

- Azure CLI: [AZ Network ASG Create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Zobrazit všechny skupiny zabezpečení aplikací

1. V levém horním rohu Azure Portal vyberte **všechny služby** .
2. Do pole **Filtr všech služeb** zadejte *skupiny zabezpečení aplikace* a po zobrazení ve výsledcích hledání vyberte možnost **skupiny zabezpečení aplikace** .

**Příkaz**

- Azure CLI: [AZ Network ASG list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Zobrazit podrobnosti konkrétní skupiny zabezpečení aplikace

1. V levém horním rohu Azure Portal vyberte **všechny služby** .
2. Do pole **Filtr všech služeb** zadejte *skupiny zabezpečení aplikace* a po zobrazení ve výsledcích hledání vyberte možnost **skupiny zabezpečení aplikace** .
3. Vyberte skupinu zabezpečení aplikace, pro kterou chcete zobrazit podrobnosti.

**Příkaz**

- Azure CLI: [AZ Network ASG show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Změna skupiny zabezpečení aplikace

1. V levém horním rohu Azure Portal vyberte **všechny služby** .
2. Do pole **Filtr všech služeb** zadejte *skupiny zabezpečení aplikace* a po zobrazení ve výsledcích hledání vyberte možnost **skupiny zabezpečení aplikace** .
3. Vyberte skupinu zabezpečení aplikace, pro kterou chcete změnit nastavení. Můžete přidat nebo odebrat značky nebo přiřadit nebo odebrat oprávnění skupině zabezpečení aplikace.

- Azure CLI: [AZ Network ASG Update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: není k dispozici žádná rutina prostředí PowerShell.

### <a name="delete-an-application-security-group"></a>Odstraní skupinu zabezpečení aplikace.

Skupinu zabezpečení aplikace nemůžete odstranit, pokud obsahuje jakákoli síťová rozhraní. Odeberte všechna síťová rozhraní ze skupiny zabezpečení aplikace, a to buď změnou nastavení síťového rozhraní, nebo odstraněním síťových rozhraní. Podrobnosti najdete v tématu [Přidání nebo odebrání síťového rozhraní ze skupin zabezpečení aplikace](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) nebo [Odstranění síťového rozhraní](virtual-network-network-interface.md#delete-a-network-interface).

1. V levém horním rohu Azure Portal vyberte **všechny služby** .
2. Do pole **Filtr všech služeb** zadejte *skupiny zabezpečení aplikace* a po zobrazení ve výsledcích hledání vyberte možnost **skupiny zabezpečení aplikace** .
3. Vyberte skupinu zabezpečení aplikace, kterou chcete odstranit.
4. Vyberte **Odstranit**a pak vyberte **Ano** , pokud chcete odstranit skupinu zabezpečení aplikace.

**Příkaz**

- Azure CLI: [AZ Network ASG Delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Oprávnění

Aby bylo možné provádět úlohy se skupinami zabezpečení sítě, pravidly zabezpečení a skupinami zabezpečení aplikací, musí být váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která má přiřazená příslušná oprávnění uvedená v následujících tabulkách:

### <a name="network-security-group"></a>Skupina zabezpečení sítě

| Akce                                                        |   Název                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Získat skupinu zabezpečení sítě                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Vytvořit nebo aktualizovat skupinu zabezpečení sítě                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Odstranit skupinu zabezpečení sítě                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Přidružení skupiny zabezpečení sítě k podsíti nebo síťovému rozhraní 


### <a name="network-security-group-rule"></a>Pravidlo skupiny zabezpečení sítě

| Akce                                                        |   Název                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Získat pravidlo                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Vytvořit nebo aktualizovat pravidlo                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Odstranit pravidlo                                                         |

### <a name="application-security-group"></a>Skupina zabezpečení aplikace

| Akce                                                                     | Název                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Připojení konfigurace protokolu IP ke skupině zabezpečení aplikace|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Připojit pravidlo zabezpečení ke skupině zabezpečení aplikace    |
| Microsoft.Network/applicationSecurityGroups/read                           | Získat skupinu zabezpečení aplikace                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Vytvoří nebo aktualizuje skupinu zabezpečení aplikace.           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Odstraní skupinu zabezpečení aplikace.                     |

## <a name="next-steps"></a>Další kroky

- Vytvoření skupiny zabezpečení sítě nebo aplikace pomocí [PowerShellu](powershell-samples.md) nebo ukázkových skriptů [Azure CLI](cli-samples.md) nebo používání [šablon Azure správce prostředků](template-samples.md)
- Vytvoření a použití [zásad Azure](policy-samples.md) pro virtuální sítě
