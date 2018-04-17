---
title: Vytvoření, změnit nebo odstranit skupinu zabezpečení sítě Azure | Microsoft Docs
description: Zjistěte, jak vytvářet, měnit nebo odstranit skupinu zabezpečení sítě.
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
ms.openlocfilehash: ba7c0400e59d8c747553479eb5474978a629dfdf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-change-or-delete-a-network-security-group"></a>Vytvoření, změnit nebo odstranit skupinu zabezpečení sítě

Pravidla zabezpečení v skupin zabezpečení sítě umožňují filtrovat typ síťového provozu, který může obtékat směřující podsítě virtuální sítě a síťových rozhraní. Pokud si nejste obeznámeni s skupin zabezpečení sítě, najdete v části [přehled skupiny zabezpečení sítě](security-overview.md) Další informace o nich a dokončete [filtrování provozu sítě přenosů](tutorial-filter-network-traffic.md) kurzu k získání nějaké zkušenosti s sítí skupiny zabezpečení.

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v žádné části tohoto článku dokončete následující úlohy:

- Pokud nemáte účet Azure, si zaregistrovat [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí účtu Azure.
- Pokud pomocí příkazů prostředí PowerShell k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení s Azure.

## <a name="work-with-network-security-groups"></a>Práce se skupinami zabezpečení sítě

Můžete vytvořit, [zobrazit všechny](#view-all-network-security-groups), [zobrazit podrobnosti o](#view-details-of-a-network-security-group), [změnit](#change-a-network-security-group), a [odstranit](#delete-a-network-security-group) skupinu zabezpečení sítě. Můžete také [přidružit nebo zrušit přidružení](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) skupinu zabezpečení sítě z rozhraní sítě nebo podsítě.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Je omezena na tom, kolik skupin zabezpečení, které můžete vytvořit na umístění Azure a předplatné sítě. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V levém horním rohu portálu, vyberte **+ vytvořit prostředek**.
2. Vyberte **sítě**, pak vyberte **skupinu zabezpečení sítě**.
3. Zadejte **název** pro skupinu zabezpečení sítě, vyberte vaše **předplatné**, vytvořte novou **skupiny prostředků**, nebo vyberte existující skupinu prostředků, vyberte **Umístění**a potom vyberte **vytvořit**. 

**Příkazy**

- Azure CLI: [vytvořit az sítě nsg](/cli/azure/network/nsg#az-network-nsg-create)
- Prostředí PowerShell: [nové AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Zobrazení všech skupin zabezpečení sítě

Do vyhledávacího pole v horní části portálu, zadejte *skupin zabezpečení sítě*. Když **skupin zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ho. Jsou uvedeny skupiny zabezpečení sítě, které existují v rámci vašeho předplatného.

**Příkazy**

- Azure CLI: [seznam nsg az sítě](/cli/azure/network/nsg#az-network-nsg-list)
- Prostředí PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Zobrazení podrobností skupiny zabezpečení sítě

1. Do vyhledávacího pole v horní části portálu, zadejte *skupin zabezpečení sítě*. Když **skupin zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. V seznamu, který chcete zobrazit podrobnosti, vyberte skupinu zabezpečení sítě. V části **nastavení** můžete zobrazit **příchozí pravidla zabezpečení** a **odchozí pravidla zabezpečení**, **síťových rozhraní** a  **Podsítě** skupinu zabezpečení sítě je přidružen k. Můžete také povolit nebo zakázat **diagnostické protokoly** a zobrazení **pravidla efektivní zabezpečení**. Další informace najdete v tématu [diagnostické protokoly](virtual-network-nsg-manage-log.md) a [zobrazit efektivní zabezpečení pravidla](virtual-network-nsg-troubleshoot-portal.md).
3. Další informace o běžných nastavení Azure uvedené, naleznete v následujících článcích:
    *   [Protokol aktivit](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Řízení přístupu (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Značky](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
    *   [Zámky.](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Skriptu pro automatizaci](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Příkazy**

- Azure CLI: [az sítě nsg zobrazit](/cli/azure/network/nsg#az-network-nsg-show)
- Prostředí PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Změnit skupinu zabezpečení sítě

1. Do vyhledávacího pole v horní části portálu, zadejte *skupin zabezpečení sítě* do vyhledávacího pole. Když **skupin zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte skupinu zabezpečení sítě, kterou chcete změnit. Nejběžnější změny jsou [přidání](#create-a-security-rule) nebo [odebrání](#delete-a-security-rule) pravidla zabezpečení a [Associating nebo zrušení přidružení skupiny zabezpečení sítě do nebo z podsítě nebo sítě rozhraní](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource).

**Příkazy**

- Azure CLI: [aktualizace nsg az sítě](/cli/azure/network/nsg#az-network-nsg-update)
- Prostředí PowerShell: [AzureRmNetworkSecurityGroup sady](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Přidružení nebo zrušit přidružení skupiny zabezpečení sítě do nebo z podsítě nebo sítě rozhraní

Přidružte skupinu zabezpečení sítě pro nebo zrušit přidružení skupiny zabezpečení sítě ze síťového rozhraní, najdete v části [přidružení skupiny zabezpečení sítě pro nebo zrušit přidružení skupiny zabezpečení sítě k síťovému rozhraní](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Přidružte skupinu zabezpečení sítě pro nebo zrušit přidružení skupiny zabezpečení sítě z podsítě, najdete v části [změnit nastavení podsítě](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Odstranit skupinu zabezpečení sítě

Pokud skupina zabezpečení sítě je přidružen k žádné podsítě nebo síťová rozhraní, nelze jej odstranit. [Zrušit přidružení](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) skupinu zabezpečení sítě ze všech podsítí a síťových rozhraní před pokusem o jeho odstranění.

1. Do vyhledávacího pole v horní části portálu, zadejte *skupin zabezpečení sítě* do vyhledávacího pole. Když **skupin zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte skupinu zabezpečení sítě, které chcete odstranit ze seznamu.
3. Vyberte **odstranit**a potom vyberte **Ano**.

**Příkazy**

- Azure CLI: [odstranit az sítě nsg](/cli/azure/network/nsg#az-network-nsg-delete)
- Prostředí PowerShell: [AzureRmNetworkSecurityGroup odebrat](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroupp) 

## <a name="work-with-security-rules"></a>Práce s pravidla zabezpečení

Skupina zabezpečení sítě obsahuje nula nebo více pravidel zabezpečení. Můžete vytvořit, [zobrazit všechny](#view-all-security-rules), [zobrazit podrobnosti o](#view-details-of-a-security-rule), [změnit](#change-a-security-rule), a [odstranit](#delete-a-security-rule) pravidlo zabezpečení.

### <a name="create-a-security-rule"></a>Vytvořit pravidlo zabezpečení

Je omezena na tom, kolik pravidel na skupinu zabezpečení sítě můžete vytvořit na umístění Azure a předplatné. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Do vyhledávacího pole v horní části portálu, zadejte *skupin zabezpečení sítě* do vyhledávacího pole. Když **skupin zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte skupinu zabezpečení sítě v seznamu, který chcete přidat pravidlo zabezpečení.
3. Vyberte **příchozí pravidla zabezpečení** pod **nastavení**. Několik existujících pravidel jsou uvedené. Některá pravidla, které nemusí jste přidali. Při vytvoření skupiny zabezpečení sítě se vytvoří několik výchozích pravidel zabezpečení v ho. Další informace najdete v tématu [výchozí pravidla zabezpečení](security-overview.md#default-security-rules).  Nelze odstranit výchozí pravidla zabezpečení, ale je možné přepsat pravidla, která mají vyšší prioritu.
4. <a name = "security-rule-settings"></a>Vyberte **+ přidat**.  Vyberte nebo přidejte hodnoty pro následující nastavení a pak vyberte **OK**:
    
    |Nastavení  |Hodnota  |Podrobnosti  |
    |---------|---------|---------|
    |Zdroj     | Vyberte **žádné**, **IP adresy**, nebo **služby značky**.        | Pokud vyberete **IP adresy**, pak musíte zadat **zdrojové IP adresy nebo CIDR rozsahy**. Můžete zadat jednu hodnotu nebo seznam oddělený čárkami obsahující více hodnot. Příkladem víc hodnot je 10.0.0.0/16, 192.188.1.1. Existují omezení počtu hodnot, které můžete zadat. V tématu [Azure omezuje](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) podrobnosti. Pokud vyberete **výrobní číslo**, pak je nutné vybrat jeden výrobní číslo. Výrobní číslo je identifikátor předdefinované kategorii IP adres. Další informace o dostupných služeb značky a co jednotlivé značky představuje, najdete v části [služby značky](security-overview.md#service-tags)        |
    |Rozsahy portů zdroje     | Zadejte jediný port, jako třeba 80, rozsah portů, jako třeba 1024-65535, nebo seznamu jednoho porty a rozsahy portů, jako je například 80, 1024 až 65535. Zadejte hvězdičku umožnit provoz z jakéhokoli portu. | Porty a rozsahy zadejte, jaký provoz porty povolený nebo zakázaný pravidlem. Existují omezení počtu portů, které můžete zadat. V tématu [Azure omezuje](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) podrobnosti.  |
    |Cíl     | Vyberte **žádné**, **IP adresy**, nebo **virtuální sítě**.        | Pokud vyberete **IP adresy**, pak musíte zadat **rozsahy cílové IP adresy nebo CIDR**. Podobně jako **zdroj** a **zdrojové IP adresy nebo CIDR rozsahy**, můžete zadat jeden nebo více adres nebo rozsahy a existují omezení počtu můžete zadat. Výběr **virtuální síť**, což je služba značku, znamená to, že provoz je povoleno pro všechny IP adresy v adresním prostoru virtuální sítě.        |
    |Cílový port rozsahů     | Zadejte jednu hodnotu, nebo seznam hodnot oddělených čárkami. | Podobně jako **zdroje rozsahy portů**, můžete zadat jeden nebo více porty a rozsahy a existují omezení počtu můžete zadat. |
    |Protocol (Protokol)     | Vyberte **žádné**, **TCP**, nebo **UDP**.        |         |
    |Akce     | Vyberte **povolit** nebo **Odepřít**.        |         |
    |Priorita     | Zadejte hodnotu mezi 100-4096 jedinečný pro všechna pravidla zabezpečení v rámci skupinu zabezpečení sítě. |Pravidla se zpracovávají v pořadí podle priority. Čím nižší je číslo, tím vyšší je priorita. Doporučujeme ponechat mezera mezi čísel priority při vytváření pravidel, například 100, 200, 300. Nechat mezery umožňuje snadno přidávat v budoucnu pravidla, které byste mohli potřebovat zajistit vyšší nebo nižší než existující pravidla.         |
    |Název     | Jedinečný název pro pravidlo v rámci skupinu zabezpečení sítě.        |  Název může obsahovat až 80 znaků. Musí začínat písmenem nebo číslicí, končit písmenem, číslicí nebo podtržítkem a může obsahovat pouze písmena, číslice, podtržítka, tečky a pomlčky.       |
    |Popis     | Volitelný popis.        |         |

    Nelze zadat [skupiny zabezpečení aplikací](#work-with-application-security-groups) pro **zdroj** nebo **cílové** nastavení pomocí portálu. Můžete ale pomocí příkazového řádku Azure CLI nebo Powershellu. Nastavení pro **odchozí pravidla zabezpečení** jsou podobné, takže není zaručeno samostatně.

**Příkazy**

- Azure CLI: [vytvořit pravidla nsg az sítě](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- Prostředí PowerShell: [nové AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Zobrazit všechna pravidla zabezpečení

Skupina zabezpečení sítě obsahuje nula nebo více pravidel. Další informace o informace uvedené při zobrazení pravidel najdete v tématu [přehled skupiny zabezpečení sítě](security-overview.md).

1. Do vyhledávacího pole v horní části portálu, zadejte *skupin zabezpečení sítě*. Když **skupin zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte ze seznamu, který chcete zobrazit pravidla pro skupinu zabezpečení sítě.
3. Vyberte **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení** pod **nastavení**.

Seznam obsahuje všechna pravidla, které jste vytvořili a skupinu zabezpečení sítě [výchozí pravidla zabezpečení](security-overview.md#default-security-rules).

**Příkazy**

- Azure CLI: [seznam pravidel nsg az sítě](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- Prostředí PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Zobrazení podrobností pravidla zabezpečení

1. Do vyhledávacího pole v horní části portálu, zadejte *skupin zabezpečení sítě*. Když **skupin zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte skupinu zabezpečení sítě, které chcete zobrazit podrobnosti o pravidlo zabezpečení.
3. Vyberte **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení** pod **nastavení**.
4. Vyberte pravidlo, které chcete zobrazit podrobnosti. Podrobné vysvětlení všech nastavení najdete v tématu [nastavení pravidla zabezpečení](#security-rule-settings).

**Příkazy**

- Azure CLI: [zobrazit pravidla nsg sítě az](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- Prostředí PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Změna pravidla zabezpečení

1. Proveďte kroky v [zobrazit podrobnosti o pravidlo zabezpečení](#view-details-of-a-security-rule).
2. Změňte nastavení podle potřeby a potom vyberte **Uložit**. Podrobné vysvětlení všech nastavení najdete v tématu [nastavení pravidla zabezpečení](#security-rule-settings).

**Příkazy**

- Azure CLI: [aktualizace pravidla nsg az sítě](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- Prostředí PowerShell: [AzureRmSecurityRuleConfig sady](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Odstranit pravidlo zabezpečení

1. Proveďte kroky v [zobrazit podrobnosti o pravidlo zabezpečení](#view-details-of-a-security-rule).
2. Vyberte **odstranit**a potom vyberte **Ano**.

**Příkazy**

- Azure CLI: [az síť nsg pravidlo odstranit](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- Prostředí PowerShell: [AzureRmSecurityRuleConfig odebrat](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)


## <a name="work-with-application-security-groups"></a>Práce se skupinami zabezpečení aplikací

Skupina zabezpečení aplikace obsahuje nula nebo víc síťových rozhraní. Další informace najdete v tématu [skupin zabezpečení aplikací](security-overview.md#application-security-groups). Nemůže pracovat se skupinami zabezpečení aplikací na portálu, ale můžete použít PowerShell nebo rozhraní příkazového řádku Azure. Všech síťových rozhraní ve skupině zabezpečení aplikací, musí existovat ve stejné virtuální síti. První síťové rozhraní přidat do skupiny zabezpečení aplikace určuje, všechny následné síťová rozhraní musí být ve virtuální sítě. Informace o postupu přidání síťové rozhraní do skupiny zabezpečení aplikací, najdete v tématu [rozhraní sítě přidat do skupiny zabezpečení aplikace](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Vytvořte skupinu zabezpečení aplikací

- Azure CLI: [vytvořit asg az sítě](/cli/azure/network/asg#az-network-asg-create)
- Prostředí PowerShell: [nové AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Zobrazení všech skupin zabezpečení aplikací

- Azure CLI: [seznam asg az sítě](/cli/azure/network/asg#az-network-asg-list)
- Prostředí PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Zobrazení podrobností skupiny zabezpečení konkrétní aplikace

- Azure CLI: [az sítě asg zobrazit](/cli/azure/network/asg#az-network-asg-show)
- Prostředí PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Změnit skupinu zabezpečení aplikací

Když chcete změnit některá nastavení, jako je například značky a oprávnění pro existující skupiny zabezpečení aplikací, nelze změnit jeho název nebo umístění.

- Azure CLI: [aktualizace asg az sítě](/cli/azure/network/asg#az-network-asg-update)
- Prostředí PowerShell: Rutiny žádné prostředí PowerShell.

### <a name="delete-an-application-security-group"></a>Odstranit skupinu zabezpečení aplikací

Skupina zabezpečení aplikace nelze odstranit, pokud má všech síťových rozhraní v ní. Změna nastavení síťového rozhraní, nebo odstranění rozhraní sítě, je nutné odebrat ze skupiny zabezpečení aplikace všech síťových rozhraní. Podrobnosti najdete v tématu [přidat nebo odebrat síťové rozhraní ze skupin zabezpečení aplikací](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) nebo [odstranit síťové rozhraní](virtual-network-network-interface.md#delete-a-network-interface).

**Příkazy**

- Azure CLI: [az sítě asg odstranit](/cli/azure/network/asg#az-network-asg-delete)
- Prostředí PowerShell: [AzureRmApplicationSecurityGroup odebrat](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>Oprávnění

K provádění úloh na skupiny zabezpečení sítě, pravidla zabezpečení a skupiny zabezpečení aplikací, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušná oprávnění uvedených v následující tabulce:

|Operace                                                       |   Název operace                               |
|--------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/ruleTables/read                              |   Načíst skupinu zabezpečení sítě                              |
|Microsoft.Network/ruleTables/write                             |   Vytvořit nebo aktualizovat skupinu zabezpečení sítě                 |
|Microsoft.Network/ruleTables/delete                            |   Odstranit skupinu zabezpečení sítě                           |
|Microsoft.Network/ruleTables/join/action                       |   Připojit skupinu zabezpečení sítě                             |
|Microsoft.Network/ruleTables/rules/read                       |   Získat pravidlo                                    |
|Microsoft.Network/ruleTables/rules/write                      |   Vytvořit nebo aktualizovat pravidlo                       |
|Microsoft.Network/ruleTables/rules/delete                     |   Odstranit pravidlo                                 |
|Microsoft.Network/networkInterfaces/effectiveruleTable/action  |   Načíst skupinu zabezpečení sítě rozhraní efektivní sítě  | 
|Microsoft.Network/networkWatchers/nextHop/action                |   Získá další segment z virtuálního počítače                  |

*Spojit skupinu zabezpečení sítě* operaci je nutný k přidružení skupiny zabezpečení sítě k podsíti.