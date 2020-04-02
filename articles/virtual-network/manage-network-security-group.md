---
title: Vytvoření, změna nebo odstranění skupiny zabezpečení sítě Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak vytvořit, změnit nebo odstranit skupinu zabezpečení sítě.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 9ed4ce6befda76069e965501a320dc110129a024
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521032"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Vytvoření, změna nebo odstranění skupiny zabezpečení sítě

Pravidla zabezpečení ve skupinách zabezpečení sítě umožňují filtrovat typ síťového provozu, který může proudit do a ze sítí virtuálních sítí a síťových rozhraní. Další informace o skupinách zabezpečení sítě naleznete v [tématu Přehled skupin zabezpečení sítě](security-overview.md). Dále dokončete kurz [filtrování síťového provozu,](tutorial-filter-network-traffic.md) abyste získali nějaké zkušenosti se skupinami zabezpečení sítě.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud ho nemáte, nastavte si účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Před zahájením zbývající části tohoto článku proveďte jeden z těchto úkolů:

- **Uživatelé portálu:** Přihlaste se na [portál Azure](https://portal.azure.com) pomocí svého účtu Azure.

- **Uživatelé PowerShellu:** Buď spusťte příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spusťte PowerShell z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Na kartě prohlížeče Azure Cloud Shell najděte rozevírací seznam **Select environment** a pak vyberte **PowerShell,** pokud ještě není vybraný.

    Pokud používáte PowerShell místně, použijte modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az.Network`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure.

- **Uživatelé rozhraní příkazového řádku Azure (CLI):** Buď spusťte příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash)nebo spusťte rozhraní příkazového řádku z vašeho počítače. Azure CLI verze 2.0.28 nebo novější, pokud používáte Azure CLI místně. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Spuštěním příkazu `az login` vytvořte připojení k Azure.

Účet, ke kterému se přihlásíte nebo se připojíte k Azure, musí být přiřazen k [roli přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v [části Oprávnění](#permissions).

## <a name="work-with-network-security-groups"></a>Práce se skupinami zabezpečení sítě

Můžete vytvořit, [zobrazit vše](#view-all-network-security-groups), zobrazit [podrobnosti ,](#view-details-of-a-network-security-group) [změnit](#change-a-network-security-group)a [odstranit](#delete-a-network-security-group) skupinu zabezpečení sítě. Skupinu zabezpečení sítě můžete také [přidružit nebo oddělit](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) od síťového rozhraní nebo podsítě.

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Existuje limit na to, kolik skupin zabezpečení sítě můžete vytvořit pro každé umístění Azure a předplatné. Další informace najdete v [tématu Limity předplatného azure a služby, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V nabídce [Portál Azure](https://portal.azure.com) nebo na **domovské** stránce vyberte **Vytvořit prostředek**.

2. Vyberte **síť ,** potom vyberte **skupinu zabezpečení sítě**.

3. Na stránce **Vytvořit skupinu zabezpečení sítě** nastavte na kartě **Základy** hodnoty pro následující nastavení:

    | Nastavení | Akce |
    | --- | --- |
    | **Předplatné** | Zvolte vaše předplatné. |
    | **Skupina prostředků** | Vyberte existující skupinu prostředků nebo vyberte **Vytvořit nový** a vytvořte novou skupinu prostředků. |
    | **Název** | Zadejte jedinečný textový řetězec v rámci skupiny prostředků. |
    | **Oblasti** | Zvolte požadované umístění. |

4. Vyberte **Zkontrolovat a vytvořit**.

5. Po zobrazení zprávy **Ověření předáno** vyberte **vytvořit**.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [Nová skupina zabezpečení sítě AzNetwork](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Zobrazit všechny skupiny zabezpečení sítě

Přejděte na [portál Azure](https://portal.azure.com) a prohlédněte si skupiny zabezpečení sítě. Vyhledejte a vyberte **skupiny zabezpečení sítě**. Seznam skupin zabezpečení sítě se zobrazí pro vaše předplatné.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síť nsg seznam](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Zobrazení podrobností o skupině zabezpečení sítě

1. Přejděte na [portál Azure](https://portal.azure.com) a prohlédněte si skupiny zabezpečení sítě. Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě.

Na řádku nabídek skupiny zabezpečení sítě můžete v části **Nastavení**zobrazit **pravidla zabezpečení Příchozí**, Odchozí pravidla **zabezpečení**, **Síťová rozhraní**a **Podsítě,** ke kterým je přidružena skupina zabezpečení sítě.

V části **Sledování**můžete povolit nebo zakázat **nastavení diagnostiky**. V části **Podpora + řešení potíží**můžete zobrazit platná pravidla **zabezpečení**. Další informace najdete [v tématu Diagnostické protokolování skupiny zabezpečení sítě](virtual-network-nsg-manage-log.md) a [Diagnostika problému s filtrem síťového provozu virtuálního připojení](diagnose-network-traffic-filter-problem.md).

Další informace o běžných nastaveních Azure uvedených najdete v následujících článcích:

- [Protokol aktivit](../azure-monitor/platform/platform-logs-overview.md)
- [Řízení přístupu (IAM)](../role-based-access-control/overview.md)
- [Značky](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zámky](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automatizační skript](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síť nsg show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Změna skupiny zabezpečení sítě

1. Přejděte na [portál Azure](https://portal.azure.com) a prohlédněte si skupiny zabezpečení sítě. Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě, kterou chcete změnit.

Nejběžnější změny jsou [přidání pravidla zabezpečení](#create-a-security-rule), [odebrání pravidla](#delete-a-security-rule)a přidružení nebo [oddělení skupiny zabezpečení sítě k podsíti nebo síťovému rozhraní nebo od ní](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síť nsg aktualizace](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Přidružení nebo oddělení skupiny zabezpečení sítě k podsíti nebo síťovému rozhraní nebo z ní

Informace o přidružení skupiny zabezpečení sítě k síťovému rozhraní nebo oddělení skupiny zabezpečení sítě od síťového rozhraní naleznete [v tématu Přidružení skupiny zabezpečení sítě k síťovému rozhraní nebo oddělení skupiny zabezpečení sítě .](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) Pokud chcete skupinu zabezpečení sítě přidružit nebo oddělit skupinu zabezpečení sítě od podsítě, přečtěte si informace o [změně nastavení podsítě](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Odstranění skupiny zabezpečení sítě

Pokud je skupina zabezpečení sítě přidružena k jakýmkoli podsítím nebo síťovým rozhraním, nelze ji odstranit. Před pokusem o její odstranění odkažte skupinu zabezpečení sítě od všech podsítí a síťových rozhraní.

1. Přejděte na [portál Azure](https://portal.azure.com) a prohlédněte si skupiny zabezpečení sítě. Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě, kterou chcete odstranit.

3. Na panelu nástrojů skupiny zabezpečení sítě vyberte **odstranit**. Pak v potvrzovacím dialogovém okně vyberte **Ano.**

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síť nsg odstranit](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Odebrat skupinu aznetworksecuritygroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Práce s pravidly zabezpečení

Skupina zabezpečení sítě obsahuje nulová nebo více pravidel zabezpečení. Můžete vytvořit, [zobrazit vše](#view-all-security-rules), zobrazit [podrobnosti ,](#view-details-of-a-security-rule) [změnit](#change-a-security-rule)a [odstranit](#delete-a-security-rule) pravidlo zabezpečení.

### <a name="create-a-security-rule"></a>Vytvoření pravidla zabezpečení

Existuje limit na to, kolik pravidel na skupinu zabezpečení sítě můžete vytvořit pro každé umístění Azure a předplatné. Další informace najdete v [tématu Limity předplatného azure a služby, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Přejděte na [portál Azure](https://portal.azure.com) a prohlédněte si skupiny zabezpečení sítě. Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě, do které chcete přidat pravidlo zabezpečení.

3. Na řádku nabídek skupiny zabezpečení sítě zvolte **Příchozí pravidla zabezpečení** nebo Odchozí pravidla **zabezpečení**.

    V seznamu je uvedeno několik stávajících pravidel, včetně některých, která jste možná nepřidali. Při vytváření skupiny zabezpečení sítě je v ní vytvořeno několik výchozích pravidel zabezpečení. Další informace naleznete [v tématu výchozí pravidla zabezpečení](security-overview.md#default-security-rules).  Výchozí pravidla zabezpečení nelze odstranit, ale můžete je přepsat pravidly, která mají vyšší prioritu.

4. <a name="security-rule-settings"></a>Vyberte **Přidat**. Vyberte nebo přidejte hodnoty pro následující nastavení a pak vyberte **OK**:

    | Nastavení | Hodnota | Podrobnosti |
    | ------- | ----- | ------- |
    | **Zdroj** | Jeden z:<ul><li>**Jakýkoli**</li><li>**IP adresy**</li><li>**Značka servisu** (pravidlo příchozího zabezpečení) nebo **Virtuální síť** (pravidlo odchozího zabezpečení)</li><li>**Skupina&nbsp;&nbsp;zabezpečení aplikace**</li></ul> | <p>Pokud zvolíte **adresy IP**, musíte také zadat **zdrojové adresy IP/rozsahy CIDR**.</p><p>Pokud zvolíte **výrobní číslo**, můžete také vybrat **zdrojovou značku .**</p><p>Pokud zvolíte **skupinu zabezpečení aplikace**, musíte také vybrat existující skupinu zabezpečení aplikace. Pokud zvolíte **skupinu zabezpečení aplikace** pro **zdroj** i **cíl**, musí být síťová rozhraní v rámci obou skupin zabezpečení aplikace ve stejné virtuální síti.</p> |
    | **Zdrojové IP adresy/rozsahy CIDR** | Seznam adres IP oddělených čárkami a rozsahy cidr (Classless Interdomain Routing) | <p>Toto nastavení se zobrazí, pokud změníte **zdroj** na **adresy IP**. Je nutné zadat jednu hodnotu nebo seznam více hodnot oddělených čárkami. Příkladem více hodnot `10.0.0.0/16, 192.188.1.1`je . Počet hodnot, které můžete zadat, je limitován. Další podrobnosti najdete v [tématu Limity Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Pokud je zadaná IP adresa přiřazena virtuálnímu počítači Azure, zadejte jeho privátní IP adresu, ne jeho veřejnou IP adresu. Azure zpracovává pravidla zabezpečení poté, co převede veřejnou IP adresu na privátní IP adresu pro příchozí pravidla zabezpečení, ale předtím, než převede privátní IP adresu na veřejnou IP adresu pro odchozí pravidla. Další informace o veřejných a privátních IP adresách v Azure najdete v tématu [Typy IP adres](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Zdrojová značka servisu** | Výrobní číslo z rozevíracího seznamu | Toto volitelné nastavení se zobrazí, pokud nastavíte **zdroj** na **výrobní číslo** pro příchozí pravidlo zabezpečení. Výrobní číslo je předdefinovaný identifikátor pro kategorii IP adres. Další informace o dostupných značkách služeb a o tom, co jednotlivé značky představují, najdete v [tématu Service tags](security-overview.md#service-tags). |
    | **Skupina zabezpečení zdrojové aplikace** | Existující skupina zabezpečení aplikace | Toto nastavení se zobrazí, pokud nastavíte **zdroj** na **skupinu zabezpečení aplikace**. Vyberte skupinu zabezpečení aplikace, která existuje ve stejné oblasti jako síťové rozhraní. Přečtěte si, jak [vytvořit skupinu zabezpečení aplikací](#create-an-application-security-group). |
    | **Rozsahy zdrojových portů** | Jeden z:<ul><li>Jeden port, například`80`</li><li>Řada portů, jako je`1024-65535`</li><li>Seznam jednotlivých portů a/nebo rozsahů portů oddělených čárkami, například`80, 1024-65535`</li><li>Hvězdička (`*`) umožňující provoz na libovolném portu</li></ul> | Toto nastavení určuje porty, na kterých pravidlo povoluje nebo zakazuje provoz. Počet portů, které můžete zadat, je limitován. Další podrobnosti najdete v [tématu Limity Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Cíl** | Jeden z:<ul><li>**Jakýkoli**</li><li>**IP adresy**</li><li>**Značka servisu** (pravidlo odchozího zabezpečení) nebo **Virtuální síť** (pravidlo příchozího zabezpečení)</li><li>**Skupina&nbsp;&nbsp;zabezpečení aplikace**</li></ul> | <p>Pokud zvolíte **IP adresy**, zadejte také **cílové IP adresy/rozsahy CIDR**.</p><p>Pokud zvolíte **VirtualNetwork**, přenosy jsou povoleny pro všechny IP adresy v adresním prostoru virtuální sítě. **VirtualNetwork** je výrobní číslo.</p><p>Pokud vyberete **skupinu zabezpečení aplikace**, musíte vybrat existující skupinu zabezpečení aplikace. Přečtěte si, jak [vytvořit skupinu zabezpečení aplikací](#create-an-application-security-group).</p> |
    | **Cílové IP adresy/rozsahy CIDR** | Seznam adres IP a rozsahů CIDR oddělených čárkami | <p>Toto nastavení se zobrazí, pokud změníte **cíl** na **adresy IP**. Podobně jako **zdrojové** a **zdrojové IP adresy/rozsahy CIDR**můžete zadat jednu nebo více adres nebo rozsahů. Číslo, které můžete zadat, má své omezení. Další podrobnosti najdete v [tématu Limity Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Pokud je zadaná IP adresa přiřazena virtuálnímu počítači Azure, ujistěte se, že zadáte jeho privátní IP adresu, ne jeho veřejnou IP adresu. Azure zpracovává pravidla zabezpečení poté, co převede veřejnou IP adresu na privátní IP adresu pro příchozí pravidla zabezpečení, ale předtím, než Azure převede privátní IP adresu na veřejnou IP adresu pro odchozí pravidla. Další informace o veřejných a privátních IP adresách v Azure najdete v tématu [Typy IP adres](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Značka cílové služby** | Výrobní číslo z rozevíracího seznamu | Toto volitelné nastavení se zobrazí, pokud změníte **cíl** na **výrobní číslo** pro pravidlo odchozího zabezpečení. Výrobní číslo je předdefinovaný identifikátor pro kategorii IP adres. Další informace o dostupných značkách služeb a o tom, co jednotlivé značky představují, najdete v [tématu Service tags](security-overview.md#service-tags). |
    | **Cílová skupina zabezpečení aplikace** | Existující skupina zabezpečení aplikace | Toto nastavení se zobrazí, pokud nastavíte **cíl** na **skupinu zabezpečení aplikace**. Vyberte skupinu zabezpečení aplikace, která existuje ve stejné oblasti jako síťové rozhraní. Přečtěte si, jak [vytvořit skupinu zabezpečení aplikací](#create-an-application-security-group). |
    | **Rozsahy cílových portů** | Jeden z:<ul><li>Jeden port, například`80`</li><li>Řada portů, jako je`1024-65535`</li><li>Seznam jednotlivých portů a/nebo rozsahů portů oddělených čárkami, například`80, 1024-65535`</li><li>Hvězdička (`*`) umožňující provoz na libovolném portu</li></ul> | Stejně jako u **oblastí zdrojových portů**můžete určit jeden nebo více portů a rozsahů. Číslo, které můžete zadat, má své omezení. Další podrobnosti najdete v [tématu Limity Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protokol** | **Jakékoli**, **TCP**, **UDP**nebo **ICMP** | Pravidlo můžete omezit na protokol TCP (TCP), protokol UDP (UDP) nebo protokol ICMP (Internet Control Message Protocol). Výchozí nastavení je pro pravidlo použít pro všechny protokoly. |
    | **Akce** | **Povolit** nebo **odepřít** | Toto nastavení určuje, zda toto pravidlo povolí nebo odepře přístup pro zadanou konfiguraci zdroje a cíle. |
    | **Priorita** | Hodnota mezi 100 a 4096, která je jedinečná pro všechna pravidla zabezpečení v rámci skupiny zabezpečení sítě | Azure zpracovává pravidla zabezpečení v pořadí podle priority. Čím nižší číslo, tím vyšší priorita. Při vytváření pravidel, například 100, 200 a 300, doporučujeme ponechat mezeru mezi čísly priorit. Ponechání mezer usnadňuje přidávání pravidel v budoucnu, takže jim můžete dát vyšší nebo nižší prioritu než stávající pravidla. |
    | **Název** | Jedinečný název pravidla v rámci skupiny zabezpečení sítě | Název může mít až 80 znaků. Musí začínat písmenem nebo číslem a musí končit písmenem, číslem nebo podtržítkem. Název může obsahovat pouze písmena, čísla, podtržítka, tečky nebo pomlčky. |
    | **Popis** | Textový popis | Volitelně můžete zadat textový popis pravidla zabezpečení. |

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Zobrazit všechna pravidla zabezpečení

Skupina zabezpečení sítě obsahuje nula nebo více pravidel. Další informace o uvedených informacích při prohlížení pravidel naleznete v [tématu Přehled skupiny zabezpečení sítě](security-overview.md).

1. Přejděte na [portál Azure](https://portal.azure.com) a zobrazte pravidla skupiny zabezpečení sítě. Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě, pro kterou chcete pravidla zobrazit.

3. Na řádku nabídek skupiny zabezpečení sítě zvolte **Příchozí pravidla zabezpečení** nebo Odchozí pravidla **zabezpečení**.

Seznam obsahuje všechna pravidla, která jste vytvořili, a [výchozí pravidla zabezpečení](security-overview.md#default-security-rules)sítě .

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityŘádConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Zobrazení podrobností pravidla zabezpečení

1. Přejděte na [portál Azure](https://portal.azure.com) a zobrazte pravidla skupiny zabezpečení sítě. Vyhledejte a vyberte **skupiny zabezpečení sítě**.

2. Vyberte název skupiny zabezpečení sítě, pro kterou chcete zobrazit podrobnosti pravidla.

3. Na řádku nabídek skupiny zabezpečení sítě zvolte **Příchozí pravidla zabezpečení** nebo Odchozí pravidla **zabezpečení**.

4. Vyberte pravidlo, pro které chcete zobrazit podrobnosti. Vysvětlení všech nastavení naleznete v tématu [Nastavení pravidel zabezpečení](#security-rule-settings).

    > [!NOTE]
    > Tento postup platí pouze pro vlastní pravidlo zabezpečení. Pokud zvolíte výchozí pravidlo zabezpečení, nefunguje to.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síť nsg pravidlo zobrazit](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityŘádConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Změna pravidla zabezpečení

1. Proveďte kroky v [části Zobrazení podrobností o pravidle zabezpečení](#view-details-of-a-security-rule).

2. Podle potřeby změňte nastavení a pak vyberte **Uložit**. Vysvětlení všech nastavení naleznete v tématu [Nastavení pravidel zabezpečení](#security-rule-settings).

    > [!NOTE]
    > Tento postup platí pouze pro vlastní pravidlo zabezpečení. Výchozí pravidlo zabezpečení nelze změnit.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Odstranění pravidla zabezpečení

1. Proveďte kroky v [části Zobrazení podrobností o pravidle zabezpečení](#view-details-of-a-security-rule).

2. Vyberte **Odstranit** a potom vyberte **Ano**.

    > [!NOTE]
    > Tento postup platí pouze pro vlastní pravidlo zabezpečení. Není povoleno odstranit výchozí pravidlo zabezpečení.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [Az síť nsg pravidlo odstranit](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Odebrat-AzNetworkSecuritySecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Práce se skupinami zabezpečení aplikace

Skupina zabezpečení aplikace obsahuje nula nebo více síťových rozhraní. Další informace naleznete v tématu [skupiny zabezpečení aplikací](security-overview.md#application-security-groups). Všechna síťová rozhraní ve skupině zabezpečení aplikace musí existovat ve stejné virtuální síti. Informace o přidání síťového rozhraní do skupiny zabezpečení aplikace naleznete [v tématu Přidání síťového rozhraní do skupiny zabezpečení aplikace](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Vytvoření skupiny zabezpečení aplikace

1. V nabídce [Portál Azure](https://portal.azure.com) nebo na **domovské** stránce vyberte **Vytvořit prostředek**.

2. Do vyhledávacího pole zadejte *skupinu zabezpečení aplikace*.

3. Na stránce **Skupina zabezpečení aplikace** vyberte **Vytvořit**.

4. Na stránce **Vytvořit skupinu zabezpečení aplikace** nastavte na kartě **Základy** hodnoty pro následující nastavení:

    | Nastavení | Akce |
    | --- | --- |
    | **Předplatné** | Zvolte vaše předplatné. |
    | **Skupina prostředků** | Vyberte existující skupinu prostředků nebo vyberte **Vytvořit nový** a vytvořte novou skupinu prostředků. |
    | **Název** | Zadejte jedinečný textový řetězec v rámci skupiny prostředků. |
    | **Oblasti** | Zvolte požadované umístění. |

5. Vyberte **Zkontrolovat a vytvořit**.

6. Na kartě **Revize + vytvořit** po zobrazení zprávy Ověření **předáno** vyberte **Vytvořit**.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az sítě asg vytvořit](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [Nová skupina zabezpečení aplikace AzApplication](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Zobrazit všechny skupiny zabezpečení aplikací

Přejděte na [portál Azure](https://portal.azure.com) a zobrazte skupiny zabezpečení aplikací. Vyhledejte a vyberte **skupiny zabezpečení aplikace**. Na portálu Azure se zobrazí seznam skupin zabezpečení aplikací.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az sítě asg seznam](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Zobrazení podrobností o konkrétní skupině zabezpečení aplikace

1. Přejděte na [portál Azure](https://portal.azure.com) a zobrazte skupinu zabezpečení aplikací. Vyhledejte a vyberte **skupiny zabezpečení aplikace**.

2. Vyberte název skupiny zabezpečení aplikace, ve které chcete zobrazit podrobnosti.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az sítě asg show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Změna skupiny zabezpečení aplikace

1. Přejděte na [portál Azure](https://portal.azure.com) a zobrazte skupinu zabezpečení aplikací. Vyhledejte a vyberte **skupiny zabezpečení aplikace**.

2. Vyberte název skupiny zabezpečení aplikace, kterou chcete změnit.

3. Vyberte **změnit** vedle nastavení, které chcete upravit. Můžete například přidat nebo odebrat **značky**nebo změnit **skupinu prostředků** nebo **odběr**.

    > [!NOTE]
    > Umístění nemůžete změnit.

    V řádku nabídek můžete také vybrat **řízení přístupu (IAM)**. Na stránce **Řízení přístupu (IAM)** můžete přiřadit nebo odebrat oprávnění skupině zabezpečení aplikace.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síť asg aktualizace](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Žádná rutina prostředí PowerShell |

### <a name="delete-an-application-security-group"></a>Odstranění skupiny zabezpečení aplikace

Skupinu zabezpečení aplikace nelze odstranit, pokud obsahuje síťová rozhraní. Chcete-li odebrat všechna síťová rozhraní ze skupiny zabezpečení aplikace, změňte nastavení síťového rozhraní nebo odstraňte síťová rozhraní. Další informace naleznete v [tématu Přidání do skupin zabezpečení aplikací nebo jejich odebrání ze skupin zabezpečení aplikací](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) nebo Odstranění [síťového rozhraní](virtual-network-network-interface.md#delete-a-network-interface).

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte skupiny zabezpečení aplikací. Vyhledejte a vyberte **skupiny zabezpečení aplikace**.

2. Vyberte název skupiny zabezpečení aplikace, kterou chcete odstranit.

3. Vyberte **Odstranit**a pak vyberte **Ano,** chcete-li odstranit skupinu zabezpečení aplikace.

#### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az sítě asg odstranit](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Odebrat azApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Oprávnění

Chcete-li provést úkoly se skupinami zabezpečení sítě, pravidly zabezpečení a skupinami zabezpečení aplikací, musí být váš účet přiřazen k roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazena příslušná oprávnění uvedená v následujících tabulkách:

### <a name="network-security-group"></a>Skupina zabezpečení sítě

| Akce                                                        |   Name (Název)                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Získat skupinu zabezpečení sítě                                          |
| Microsoft.Network/networkSecurityGroups/zápis                 |   Vytvoření nebo aktualizace skupiny zabezpečení sítě                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Odstranit skupinu zabezpečení sítě                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Přidružení skupiny zabezpečení sítě k podsíti nebo síťovému rozhraní 

### <a name="network-security-group-rule"></a>Pravidlo skupiny zabezpečení sítě

| Akce                                                        |   Name (Název)                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Získat pravidlo                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Vytvořit nebo aktualizovat pravidlo                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Odstranění pravidla                                                         |

### <a name="application-security-group"></a>Skupina zabezpečení aplikace

| Akce                                                                     | Name (Název)                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Připojení konfigurace PROTOKOLU IP ke skupině zabezpečení aplikace|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Připojení pravidla zabezpečení ke skupině zabezpečení aplikace    |
| Microsoft.Network/applicationSecurityGroups/read                           | Získání skupiny zabezpečení aplikace                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Vytvoření nebo aktualizace skupiny zabezpečení aplikace           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Odstranění skupiny zabezpečení aplikace                     |

## <a name="next-steps"></a>Další kroky

- Vytvoření skupiny zabezpečení sítě nebo aplikace pomocí ukázkových skriptů [PowerShellu](powershell-samples.md) nebo [Azure CLI](cli-samples.md) nebo šablon Azure [Resource Manageru](template-samples.md)
- Vytvoření a použití [zásad Azure](policy-samples.md) pro virtuální sítě
