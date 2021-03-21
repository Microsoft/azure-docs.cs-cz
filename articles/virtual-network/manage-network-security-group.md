---
title: Vytvoření, změna nebo odstranění skupiny zabezpečení sítě Azure
titlesuffix: Azure Virtual Network
description: Zjistěte, kde najdete informace o pravidlech zabezpečení a o tom, jak vytvořit, změnit nebo odstranit skupinu zabezpečení sítě.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 5de909d0d57ae212fa562eb31551e2271d307d47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694253"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Vytvoření, změna nebo odstranění skupiny zabezpečení sítě

Pravidla zabezpečení ve skupinách zabezpečení sítě umožňují filtrovat typ síťového provozu, který může přecházet do podsítí a síťových rozhraní virtuální sítě. Další informace o skupinách zabezpečení sítě najdete v tématu [Přehled skupin zabezpečení sítě](./network-security-groups-overview.md). Potom dokončete kurz [vyfiltrování provozu sítě](tutorial-filter-network-traffic.md) , abyste získali nějaké zkušenosti se skupinami zabezpečení sítě.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud ho nemáte, nastavte účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Před zahájením zbývající části tohoto článku dokončete jednu z těchto úloh:

- **Uživatelé portálu**: Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

- **Uživatelé PowerShellu**: buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo z počítače spusťte PowerShell. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Na kartě Azure Cloud Shell prohlížeč Najděte rozevírací seznam **Vybrat prostředí** a pak vyberte **PowerShell** , pokud už není vybraný.

    Pokud používáte PowerShell místně, použijte Azure PowerShell modul verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az.Network`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure.

- **Uživatelé rozhraní příkazového řádku Azure (CLI)**: buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spusťte CLI z počítače. Pokud používáte Azure CLI místně, použijte Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Spuštěním příkazu `az login` vytvořte připojení k Azure.

Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k [roli Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v [oprávněních](#permissions).

## <a name="work-with-network-security-groups"></a>Práce se skupinami zabezpečení sítě

Můžete vytvořit, [Zobrazit všechny](#view-all-network-security-groups), [Zobrazit podrobnosti o](#view-details-of-a-network-security-group), [změnit](#change-a-network-security-group)a [Odstranit](#delete-a-network-security-group) skupinu zabezpečení sítě. Můžete taky [přidružit nebo oddělit](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) skupinu zabezpečení sítě od síťového rozhraní nebo podsítě.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Existuje omezení, kolik skupin zabezpečení sítě, které můžete vytvořit pro každé umístění a předplatné Azure. Další informace najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V nabídce webu [Azure Portal](https://portal.azure.com) nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

2. Vyberte **sítě** a pak vyberte **Skupina zabezpečení sítě**.

3. Na stránce **vytvořit skupinu zabezpečení sítě** na kartě **základy** nastavte hodnoty pro následující nastavení:

    | Nastavení | Akce |
    | --- | --- |
    | **Předplatné** | Zvolte vaše předplatné. |
    | **Skupina prostředků** | Zvolte existující skupinu prostředků nebo vyberte **vytvořit novou** a vytvořte novou skupinu prostředků. |
    | **Název** | Zadejte jedinečný textový řetězec v rámci skupiny prostředků. |
    | **Oblast** | Vyberte umístění, které chcete. |

4. Vyberte **Zkontrolovat a vytvořit**.

5. Po zobrazení zprávy s **potvrzením** vyberte **vytvořit**.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Zobrazit všechny skupiny zabezpečení sítě

Chcete-li zobrazit skupiny zabezpečení sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **skupiny zabezpečení sítě**. Zobrazí se seznam skupin zabezpečení sítě pro vaše předplatné.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network NSG list](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Zobrazit podrobnosti skupiny zabezpečení sítě

1. Chcete-li zobrazit skupiny zabezpečení sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název vaší skupiny zabezpečení sítě.

V panelu nabídek skupiny zabezpečení sítě v části **Nastavení** můžete zobrazit **příchozí pravidla zabezpečení**, **odchozí pravidla zabezpečení**, **Síťová rozhraní** a **podsítě** , ke kterým je přidružená skupina zabezpečení sítě.

V části **monitorování** můžete povolit nebo zakázat **nastavení diagnostiky**. V části **Podpora a řešení potíží** můžete zobrazit **platná pravidla zabezpečení**. Další informace najdete v tématu [protokolování diagnostiky pro skupinu zabezpečení sítě](virtual-network-nsg-manage-log.md) a [diagnostiku problému s filtrem provozu sítě virtuálních počítačů](diagnose-network-traffic-filter-problem.md).

Další informace o běžných nastaveních Azure uvedených v následujících článcích:

- [Protokol aktivit](../azure-monitor/essentials/platform-logs-overview.md)
- [Řízení přístupu (IAM)](../role-based-access-control/overview.md)
- [Značky](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zámky](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automatizační skript](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Změna skupiny zabezpečení sítě

1. Chcete-li zobrazit skupiny zabezpečení sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě, kterou chcete změnit.

Nejběžnějšími změnami je [Přidání pravidla zabezpečení](#create-a-security-rule), [Odebrání pravidla](#delete-a-security-rule)a [přidružení nebo zrušení přidružení skupiny zabezpečení sítě k podsíti nebo síťovému rozhraní](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network NSG Update](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Přidružení nebo zrušení přidružení skupiny zabezpečení sítě k podsíti nebo síťovému rozhraní

Pokud chcete přidružit skupinu zabezpečení sítě k nebo oddělit skupinu zabezpečení sítě od síťového rozhraní, přečtěte si téma [přidružení skupiny zabezpečení sítě k nebo odrušení skupiny zabezpečení sítě ze síťového rozhraní](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Chcete-li přidružit skupinu zabezpečení sítě k nebo oddělit skupinu zabezpečení sítě z podsítě, přečtěte si téma [Změna nastavení podsítě](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Odstraní skupinu zabezpečení sítě.

Pokud je skupina zabezpečení sítě přidružená k žádným podsítím nebo síťovým rozhraním, nelze ji odstranit. Před pokusem o odstranění zrušte přidružení skupiny zabezpečení sítě ze všech podsítí a síťových rozhraní.

1. Chcete-li zobrazit skupiny zabezpečení sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě, kterou chcete odstranit.

3. Na panelu nástrojů skupiny zabezpečení sítě vyberte **Odstranit**. Potom v potvrzovacím dialogovém okně vyberte **Ano** .

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network NSG DELETE](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Práce s pravidly zabezpečení

Skupina zabezpečení sítě obsahuje nula nebo více pravidel zabezpečení. Můžete vytvořit, [Zobrazit všechny](#view-all-security-rules), [Zobrazit podrobnosti o](#view-details-of-a-security-rule), [změnit](#change-a-security-rule)a [Odstranit](#delete-a-security-rule) pravidlo zabezpečení.

### <a name="create-a-security-rule"></a>Vytvořit pravidlo zabezpečení

U každého umístění Azure a předplatného můžete vytvořit omezení počtu pravidel na skupinu zabezpečení sítě. Další informace najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Chcete-li zobrazit skupiny zabezpečení sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě, do které chcete přidat pravidlo zabezpečení.

3. V řádku nabídek skupiny zabezpečení sítě vyberte **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení**.

    Seznam obsahuje několik stávajících pravidel, včetně některých, které jste pravděpodobně nepřidali. Při vytváření skupiny zabezpečení sítě se v ní vytvoří několik výchozích pravidel zabezpečení. Další informace najdete v tématu [výchozí pravidla zabezpečení](./network-security-groups-overview.md#default-security-rules).  Výchozí pravidla zabezpečení nemůžete odstranit, ale můžete je přepsat pravidly, která mají vyšší prioritu.

4. <a name="security-rule-settings"></a>Vyberte **Přidat**. Vyberte nebo přidejte hodnoty pro následující nastavení a pak vyberte **OK**:

    | Nastavení | Hodnota | Podrobnosti |
    | ------- | ----- | ------- |
    | **Zdroj** | Jedna z těchto:<ul><li>**Jakýkoli**</li><li>**IP adresy**</li><li>**Značka služby** (příchozí pravidlo zabezpečení) nebo **VirtualNetwork** (odchozí pravidlo zabezpečení)</li><li>**&nbsp;Skupina zabezpečení &nbsp; aplikace**</li></ul> | <p>Pokud zvolíte **IP adresy**, musíte zadat taky **zdrojové IP adresy/rozsahy CIDR**.</p><p>Pokud zvolíte **značku služby**, můžete také vybrat **značku zdrojové služby**.</p><p>Pokud zvolíte **skupinu zabezpečení aplikace**, musíte také vybrat existující skupinu zabezpečení aplikace. Pokud zvolíte **skupinu zabezpečení aplikace** pro **zdroj** i **cíl**, síťová rozhraní v obou skupinách zabezpečení aplikace musí být ve stejné virtuální síti.</p> |
    | **Zdrojové IP adresy/rozsahy CIDR** | Seznam IP adres oddělených čárkami a rozsahy směrování mezi doménami (CIDR) | <p>Toto nastavení se zobrazí, pokud změníte **zdroj** na **IP adresy**. Je nutné zadat jednu nebo čárkou oddělený seznam více hodnot. Příkladem více hodnot je `10.0.0.0/16, 192.188.1.1` . Existují omezení počtu hodnot, které můžete zadat. Další podrobnosti najdete v tématu [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Pokud je zadaná IP adresa přiřazená k virtuálnímu počítači Azure, zadejte její privátní IP adresu, nikoli její veřejnou IP adresu. Azure zpracovává pravidla zabezpečení po překládání veřejné IP adresy na privátní IP adresu pro příchozí pravidla zabezpečení, ale předtím, než přeloží privátní IP adresu na veřejnou IP adresu pro odchozí pravidla. Další informace o veřejných a privátních IP adresách v Azure najdete v tématu [typy IP adres](./public-ip-addresses.md).</p> |
    | **Značka zdrojové služby** | Značka služby z rozevíracího seznamu | Toto volitelné nastavení se zobrazí, pokud nastavíte **značku** **zdroj** -služba pro příchozí pravidlo zabezpečení. Označení služby je předdefinovaný identifikátor pro kategorii IP adres. Další informace o dostupných značkách služby a o tom, co jednotlivé značky představují, najdete v tématu [značky služeb](./network-security-groups-overview.md#service-tags). |
    | **Skupina zabezpečení zdrojové aplikace** | Existující skupina zabezpečení aplikace | Toto nastavení se zobrazí, pokud nastavíte možnost **zdroj** na **skupinu zabezpečení aplikace**. Vyberte skupinu zabezpečení aplikace, která existuje ve stejné oblasti jako síťové rozhraní. Naučte se [vytvořit skupinu zabezpečení aplikace](#create-an-application-security-group). |
    | **Rozsahy zdrojových portů** | Jedna z těchto:<ul><li>Jeden port, například `80`</li><li>Rozsah portů, například `1024-65535`</li><li>Čárkami oddělený seznam jednotlivých portů nebo rozsahů portů, například `80, 1024-65535`</li><li>Hvězdička ( `*` ) pro povolení provozu na jakémkoli portu</li></ul> | Toto nastavení určuje porty, na kterých pravidlo povoluje nebo zakazuje provoz. Existují omezení počtu portů, které můžete zadat. Další podrobnosti najdete v tématu [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Cíl** | Jedna z těchto:<ul><li>**Jakýkoli**</li><li>**IP adresy**</li><li>**Značka služby** (pravidlo odchozího zabezpečení) nebo **VirtualNetwork** (příchozí pravidlo zabezpečení)</li><li>**&nbsp;Skupina zabezpečení &nbsp; aplikace**</li></ul> | <p>Pokud vyberete možnost **IP adresy**, zadejte také **cílové IP adresy/rozsahy CIDR**.</p><p>Zvolíte-li možnost **VirtualNetwork**, bude provoz povolen pro všechny IP adresy v adresním prostoru virtuální sítě. **VirtualNetwork** je značka služby.</p><p>Pokud vyberete **skupinu zabezpečení aplikace**, musíte vybrat existující skupinu zabezpečení aplikace. Naučte se [vytvořit skupinu zabezpečení aplikace](#create-an-application-security-group).</p> |
    | **Cílové IP adresy/rozsahy CIDR** | Seznam IP adres a rozsahů CIDR oddělených čárkami | <p>Toto nastavení se zobrazí, pokud změníte **cíl** na **IP adresy**. Podobně jako **zdrojové** a **zdrojové IP adresy/rozsahy CIDR** můžete zadat jednu nebo více adres nebo rozsahů. K dispozici jsou omezení pro číslo, které můžete zadat. Další podrobnosti najdete v tématu [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Pokud se IP adresa, kterou zadáte, přiřadí virtuálnímu počítači Azure, ujistěte se, že zadáváte jeho privátní IP adresu, nikoli její veřejnou IP adresu. Azure zpracovává pravidla zabezpečení po překládání veřejné IP adresy na privátní IP adresu pro příchozí pravidla zabezpečení, ale před tím, než Azure převede privátní IP adresu na veřejnou IP adresu pro odchozí pravidla. Další informace o veřejných a privátních IP adresách v Azure najdete v tématu [typy IP adres](./public-ip-addresses.md).</p> |
    | **Značka cílové služby** | Značka služby z rozevíracího seznamu | Toto volitelné nastavení se zobrazí, pokud změníte **cíl** na **tag Service** pro pravidlo odchozího zabezpečení. Označení služby je předdefinovaný identifikátor pro kategorii IP adres. Další informace o dostupných značkách služby a o tom, co jednotlivé značky představují, najdete v tématu [značky služeb](./network-security-groups-overview.md#service-tags). |
    | **Cílová skupina zabezpečení aplikace** | Existující skupina zabezpečení aplikace | Toto nastavení se zobrazí, pokud nastavíte **cíl** na **skupinu zabezpečení aplikace**. Vyberte skupinu zabezpečení aplikace, která existuje ve stejné oblasti jako síťové rozhraní. Naučte se [vytvořit skupinu zabezpečení aplikace](#create-an-application-security-group). |
    | **Rozsahy cílových portů** | Jedna z těchto:<ul><li>Jeden port, například `80`</li><li>Rozsah portů, například `1024-65535`</li><li>Čárkami oddělený seznam jednotlivých portů nebo rozsahů portů, například `80, 1024-65535`</li><li>Hvězdička ( `*` ) pro povolení provozu na jakémkoli portu</li></ul> | Stejně jako u **rozsahů zdrojového portu** můžete zadat jeden nebo několik portů a rozsahů. K dispozici jsou omezení pro číslo, které můžete zadat. Další podrobnosti najdete v tématu [omezení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protokol** | **Libovolný**, **TCP**, **UDP** nebo **ICMP** | Můžete omezit pravidlo protokolu TCP (Transmission Control Protocol), protokolu UDP (User Datagram Protocol) nebo protokolu ICMP (Internet Control Message Protocol). Ve výchozím nastavení platí, že pravidlo platí pro všechny protokoly. |
    | **Akce** | **Povolit** nebo **zamítnout** | Toto nastavení určuje, zda toto pravidlo povolí nebo odepře přístup pro zadanou konfiguraci zdroje a cíle. |
    | **Priorita** | Hodnota mezi 100 a 4096, která je jedinečná pro všechna pravidla zabezpečení v rámci skupiny zabezpečení sítě | Azure zpracovává pravidla zabezpečení v pořadí podle priority. Čím nižší číslo, tím vyšší Priorita. Při vytváření pravidel, jako je například 100, 200 a 300, doporučujeme ponechat mezi čísly priority mezeru. Ponechání mezer usnadňuje přidávání pravidel do budoucna, aby bylo možné jim zvýšit nebo snížit prioritu než stávající pravidla. |
    | **Název** | Jedinečný název pro pravidlo v rámci skupiny zabezpečení sítě | Název může mít až 80 znaků. Musí začínat písmenem nebo číslicí a musí končit písmenem, číslicí nebo podtržítkem. Název může obsahovat jenom písmena, číslice, podtržítka, tečky nebo spojovníky. |
    | **Popis** | Textový popis | Volitelně můžete zadat textový popis pravidla zabezpečení. Popis nemůže být delší než 140 znaků. |

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Zobrazit všechna pravidla zabezpečení

Skupina zabezpečení sítě obsahuje nula nebo více pravidel. Další informace o informacích uvedených při zobrazení pravidel najdete v tématu [Přehled skupin zabezpečení sítě](./network-security-groups-overview.md).

1. Chcete-li zobrazit pravidla skupiny zabezpečení sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě, pro kterou chcete zobrazit pravidla.

3. V řádku nabídek skupiny zabezpečení sítě vyberte **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení**.

Seznam obsahuje všechna vámi vytvořená pravidla a [výchozí pravidla zabezpečení](./network-security-groups-overview.md#default-security-rules)skupiny zabezpečení sítě.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Zobrazení podrobností pravidla zabezpečení

1. Chcete-li zobrazit pravidla skupiny zabezpečení sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě, pro kterou chcete zobrazit podrobnosti pravidla.

3. V řádku nabídek skupiny zabezpečení sítě vyberte **příchozí pravidla zabezpečení** nebo **odchozí pravidla zabezpečení**.

4. Vyberte pravidlo, pro které chcete zobrazit podrobnosti. Vysvětlení všech nastavení najdete v tématu [Nastavení pravidel zabezpečení](#security-rule-settings).

    > [!NOTE]
    > Tento postup se týká pouze vlastního pravidla zabezpečení. Nefunguje, pokud zvolíte výchozí pravidlo zabezpečení.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network NSG Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Změna pravidla zabezpečení

1. Proveďte kroky v části [zobrazení podrobností pravidla zabezpečení](#view-details-of-a-security-rule).

2. Podle potřeby změňte nastavení a pak vyberte **Uložit**. Vysvětlení všech nastavení najdete v tématu [Nastavení pravidel zabezpečení](#security-rule-settings).

    > [!NOTE]
    > Tento postup se týká pouze vlastního pravidla zabezpečení. Nemůžete změnit výchozí pravidlo zabezpečení.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Odstraní pravidlo zabezpečení.

1. Proveďte kroky v části [zobrazení podrobností pravidla zabezpečení](#view-details-of-a-security-rule).

2. Vyberte **Odstranit** a potom vyberte **Ano**.

    > [!NOTE]
    > Tento postup se týká pouze vlastního pravidla zabezpečení. Nepovoluje se odstranit výchozí pravidlo zabezpečení.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network NSG Rule DELETE](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Práce se skupinami zabezpečení aplikace

Skupina zabezpečení aplikace obsahuje nula nebo více síťových rozhraní. Další informace najdete v tématu [skupiny zabezpečení aplikací](./network-security-groups-overview.md#application-security-groups). Všechna síťová rozhraní ve skupině zabezpečení aplikace musí existovat ve stejné virtuální síti. Informace o tom, jak přidat síťové rozhraní do skupiny zabezpečení aplikace, najdete v tématu [Přidání síťového rozhraní do skupiny zabezpečení aplikace](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Vytvořit skupinu zabezpečení aplikace

1. V nabídce webu [Azure Portal](https://portal.azure.com) nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

2. Do vyhledávacího pole zadejte *skupinu zabezpečení aplikace*.

3. Na stránce **Skupina zabezpečení aplikace** vyberte **vytvořit**.

4. Na stránce **vytvořit skupinu zabezpečení aplikace** na kartě **základy** nastavte hodnoty pro následující nastavení:

    | Nastavení | Akce |
    | --- | --- |
    | **Předplatné** | Zvolte vaše předplatné. |
    | **Skupina prostředků** | Zvolte existující skupinu prostředků nebo vyberte **vytvořit novou** a vytvořte novou skupinu prostředků. |
    | **Název** | Zadejte jedinečný textový řetězec v rámci skupiny prostředků. |
    | **Oblast** | Vyberte umístění, které chcete. |

5. Vyberte **Zkontrolovat a vytvořit**.

6. Na kartě **Revize + vytvořit** klikněte po zobrazení zprávy s **potvrzením** na možnost **vytvořit**.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network ASG Create](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Zobrazit všechny skupiny zabezpečení aplikací

Pokud chcete zobrazit skupiny zabezpečení vaší aplikace, přejít na [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **skupiny zabezpečení aplikace**. Azure Portal zobrazí seznam skupin zabezpečení vaší aplikace.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network ASG list](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Zobrazit podrobnosti konkrétní skupiny zabezpečení aplikace

1. Chcete-li zobrazit skupinu zabezpečení aplikace, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **skupiny zabezpečení aplikace**.

2. Vyberte název skupiny zabezpečení aplikace, pro kterou chcete zobrazit podrobnosti.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network ASG show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Změna skupiny zabezpečení aplikace

1. Chcete-li zobrazit skupinu zabezpečení aplikace, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **skupiny zabezpečení aplikace**.

2. Vyberte název skupiny zabezpečení aplikace, kterou chcete změnit.

3. Vyberte možnost **změnit** vedle nastavení, které chcete upravit. Můžete například přidat nebo odebrat **značky** nebo můžete změnit **skupinu prostředků** nebo **předplatné**.

    > [!NOTE]
    > Nemůžete změnit umístění.

    V řádku nabídek můžete také vybrat **řízení přístupu (IAM)**. Na stránce **řízení přístupu (IAM)** můžete přiřadit nebo odebrat oprávnění skupině zabezpečení aplikace.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network ASG Update](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Žádná rutina PowerShellu |

### <a name="delete-an-application-security-group"></a>Odstraní skupinu zabezpečení aplikace.

Skupinu zabezpečení aplikace nemůžete odstranit, pokud obsahuje jakákoli síťová rozhraní. Pokud chcete ze skupiny zabezpečení aplikace odebrat všechna síťová rozhraní, změňte nastavení síťového rozhraní nebo odstraňte síťová rozhraní. Další informace najdete v tématu [Přidání nebo odebrání ze skupin zabezpečení aplikace](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) nebo [Odstranění síťového rozhraní](virtual-network-network-interface.md#delete-a-network-interface).

1. Pokud chcete spravovat skupiny zabezpečení aplikací, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **skupiny zabezpečení aplikace**.

2. Vyberte název skupiny zabezpečení aplikace, kterou chcete odstranit.

3. Vyberte **Odstranit** a pak vyberte **Ano** , pokud chcete odstranit skupinu zabezpečení aplikace.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network ASG DELETE](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Oprávnění

Aby bylo možné provádět úlohy se skupinami zabezpečení sítě, pravidly zabezpečení a skupinami zabezpečení aplikací, musí být váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která má přiřazená příslušná oprávnění, jak je uvedeno v následujících tabulkách:

### <a name="network-security-group"></a>Skupina zabezpečení sítě

| Akce                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/Read                  |   Získat skupinu zabezpečení sítě                                          |
| Microsoft. Network/networkSecurityGroups/Write                 |   Vytvořit nebo aktualizovat skupinu zabezpečení sítě                             |
| Microsoft. Network/networkSecurityGroups/DELETE                |   Odstranit skupinu zabezpečení sítě                                       |
| Microsoft. Network/networkSecurityGroups/JOIN/Action           |   Přidružení skupiny zabezpečení sítě k podsíti nebo síťovému rozhraní 


>[!NOTE]
> Aby bylo možné provádět `write` operace ve skupině zabezpečení sítě, musí mít účet předplatného alespoň `read` oprávnění pro skupinu prostředků spolu s `Microsoft.Network/networkSecurityGroups/write` oprávněním.


### <a name="network-security-group-rule"></a>Pravidlo skupiny zabezpečení sítě

| Akce                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft. Network/networkSecurityGroups/securityRules/Read            |   Získat pravidlo                                                            |
| Microsoft. Network/networkSecurityGroups/securityRules/Write           |   Vytvořit nebo aktualizovat pravidlo                                               |
| Microsoft. Network/networkSecurityGroups/securityRules/DELETE          |   Odstranění pravidla                                                         |

### <a name="application-security-group"></a>Skupina zabezpečení aplikace

| Akce                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/Action     | Připojení konfigurace protokolu IP ke skupině zabezpečení aplikace|
| Microsoft. Network/applicationSecurityGroups/joinNetworkSecurityRule/Action | Připojit pravidlo zabezpečení ke skupině zabezpečení aplikace    |
| Microsoft. Network/applicationSecurityGroups/Read                           | Získat skupinu zabezpečení aplikace                        |
| Microsoft. Network/applicationSecurityGroups/Write                          | Vytvoří nebo aktualizuje skupinu zabezpečení aplikace.           |
| Microsoft. Network/applicationSecurityGroups/DELETE                         | Odstraní skupinu zabezpečení aplikace.                     |

## <a name="next-steps"></a>Další kroky

- Vytvoření skupiny zabezpečení sítě nebo aplikace pomocí [PowerShellu](powershell-samples.md) nebo ukázkových skriptů [Azure CLI](cli-samples.md) nebo [šablon Azure správce prostředků](template-samples.md)
- Vytvoření a přiřazení [definic Azure Policy](./policy-reference.md) pro virtuální sítě
