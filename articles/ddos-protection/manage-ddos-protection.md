---
title: Správa Azure DDoS Protection Standard pomocí Azure Portal
description: Naučte se používat Standard Azure DDoS Protection ke zmírnění útoku.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: b38f2831136b0ccec4aa241135f2fd342c939845
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936882"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Rychlý Start: vytvoření a konfigurace Azure DDoS Protection Standard

Začněte s Azure DDoS Protection Standard pomocí Azure Portal. 

Plán DDoS Protection definuje sadu virtuálních sítí, které mají povolený Standard DDoS Protection v rámci předplatných. Můžete nakonfigurovat jeden plán DDoS Protection pro vaši organizaci a propojit virtuální sítě s několika předplatnými do stejného plánu. 

V tomto rychlém startu vytvoříte plán DDoS Protection a propojíte ho s virtuální sítí. 

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com. Ujistěte se, že je váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) , nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v průvodci postupy pro [oprávnění](manage-permissions.md).

## <a name="create-a-ddos-protection-plan"></a>Vytvoření plánu DDoS Protection

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** .
2. Vyhledejte pojem *DDoS*. Pokud se ve výsledcích hledání zobrazí **plán DDoS Protection** , vyberte ho.
3. Vyberte **Vytvořit**.
4. Zadejte nebo vyberte následující hodnoty a pak vyberte **vytvořit**:

    |Nastavení        |Hodnota                                              |
    |---------      |---------                                          |
    |Název           | Zadejte _MyDdosProtectionPlan_.                     |
    |Předplatné   | Vyberte své předplatné.                         |
    |Skupina prostředků | Vyberte **vytvořit nové** a zadejte _MyResourceGroup_.|
    |Umístění       | Zadejte _východní USA_.                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Povolení ochrany DDoS pro virtuální síť

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Povolit DDoS Protection pro novou virtuální síť

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** .
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. Zadejte nebo vyberte následující hodnoty, přijměte zbývající výchozí hodnoty a pak vyberte **vytvořit**:

    | Nastavení         | Hodnota                                           |
    | ---------       | ---------                                       |
    | Název            | Zadejte _MyVnet_.                                 |
    | Předplatné    | Vyberte své předplatné.                                    |
    | Skupina prostředků  | Vyberte **použít existující** a pak vyberte **MyResourceGroup** . |
    | Umístění        | Zadejte _východní USA_                                                    |
    | DDoS Protection úrovně Standard | Vyberte **Povolit**. Vybraný plán může být ve stejném nebo jiném předplatném, než je virtuální síť, ale oba odběry musí být přidružené ke stejnému Azure Active Directory tenantovi.|

Virtuální síť nejde přesunout do jiné skupiny prostředků nebo předplatného, pokud je DDoS standard pro virtuální síť povolený. Pokud potřebujete přesunout virtuální síť se zapnutou DDoS standardem, zakažte nejprve DDoS Standard, přesuňte virtuální síť a pak povolte DDoS Standard. Po přesunu se automaticky vyladěné prahové hodnoty zásad pro všechny chráněné veřejné IP adresy ve virtuální síti resetují.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Povolit ochranu DDoS pro existující virtuální síť

1. Vytvořte plán DDoS Protection tak, že vyplníte kroky v tématu [Vytvoření plánu DDoS Protection](#create-a-ddos-protection-plan), pokud nemáte existující plán DDoS Protection.
2. Do **pole Hledat prostředky, služby a dokumenty** v horní části Azure Portal zadejte název virtuální sítě, pro kterou chcete povolit DDoS Protection Standard. Pokud se ve výsledcích hledání zobrazí název virtuální sítě, vyberte ji.
3. V části **Nastavení** vyberte **DDoS Protection**.
4. Vyberte **Standard**. V části **plán ochrany DDoS** vyberte existující plán DDoS Protection nebo plán, který jste vytvořili v kroku 1, a pak vyberte **Uložit**. Vybraný plán může být ve stejném nebo jiném předplatném, než je virtuální síť, ale oba odběry musí být přidružené ke stejnému Azure Active Directory tenantovi.

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>Povolit DDoS Protection pro všechny virtuální sítě

Tato [zásada](https://aka.ms/ddosvnetpolicy) vyhledá všechny virtuální sítě v definovaném oboru, u kterých není povolená DDoS Protection Standard, a pak volitelně vytvoří úlohu nápravy, která vytvoří přidružení pro ochranu virtuální sítě. Podrobné pokyny, jak nasadit tuto zásadu, najdete v tématu https://aka.ms/ddosvnetpolicy-techcommunity .

## <a name="validate-and-test"></a>Ověřit a otestovat

Nejdřív si Projděte podrobnosti svého plánu DDoS Protection:

1. V levé horní části portálu vyberte **Všechny služby**.
2. Do pole **filtru** zadejte *DDoS* . Pokud se ve výsledcích zobrazí **plány ochrany DDoS Protection** , vyberte ji.
3. Ze seznamu vyberte svůj plán DDoS Protection.

_MyVnet_ virtuální síť by měla být uvedena v seznamu. 

### <a name="view-protected-resources"></a>Zobrazení chráněných prostředků
V části **chráněné prostředky** můžete zobrazit chráněné virtuální sítě a veřejné IP adresy nebo do svého plánu DDoS Protection přidat další virtuální sítě:

![Zobrazení chráněných prostředků](./media/manage-ddos-protection/ddos-protected-resources.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Své prostředky můžete uchovávat pro další kurz. Pokud už je nepotřebujete, odstraňte skupinu prostředků _MyResourceGroup_ . Když odstraníte skupinu prostředků, odstraní se i plán DDoS Protection a všechny související prostředky. Pokud nemáte v úmyslu použít tento plán DDoS Protection, měli byste odebrat prostředky, abyste se vyhnuli zbytečným poplatkům.

   >[!WARNING]
   >Tato akce je irreversable.

1. V Azure Portal vyhledejte a vyberte **skupiny prostředků** nebo v nabídce Azure Portal vyberte **skupiny prostředků** .

2. Vyfiltrujte nebo přejděte dolů a najděte skupinu prostředků _MyResourceGroup_ .

3. Vyberte skupinu prostředků a pak vyberte **Odstranit skupinu prostředků**.

4. Zadejte název skupiny prostředků, který chcete ověřit, a pak vyberte **Odstranit**.

Zakázání ochrany DDoS pro virtuální síť: 

1. Do **pole Hledat prostředky, služby a dokumenty** v horní části portálu zadejte název virtuální sítě, pro kterou chcete zakázat DDoS Protection Standard. Pokud se ve výsledcích hledání zobrazí název virtuální sítě, vyberte ji.
2. V části **DDoS Protection Standard** vyberte **Zakázat**.

Pokud chcete odstranit plán DDoS Protection, musíte nejdřív z něj oddělit všechny virtuální sítě. 

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak zobrazit a nakonfigurovat telemetrii pro svůj plán DDoS Protection, přejděte k kurzům.

> [!div class="nextstepaction"]
> [Zobrazení a konfigurace telemetrie ochrany před útoky DDoS](telemetry.md)
