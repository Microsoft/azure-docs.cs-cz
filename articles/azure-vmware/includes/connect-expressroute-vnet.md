---
title: Připojení ExpressRoute k bráně virtuální sítě
description: Postup připojení ExpressRoute k bráně virtuální sítě.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 6e2e3748dbfd8d69b53dcc4c3a09809756ac48dc
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494344"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Přejděte do privátního cloudu, který jste vytvořili v kurzu [nasazení clusteru vSphere v Azure](../tutorial-create-private-cloud.md) . V části **Spravovat** vyberte **připojení** vyberte kartu **ExpressRoute** .

1. Zkopírujte autorizační klíč. Pokud není k dispozici autorizační klíč, musíte ho vytvořit, vybrat **+ požádat o autorizační klíč**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="Zkopírujte autorizační klíč. Pokud není k dispozici autorizační klíč, musíte ho vytvořit, vybrat + požádat o autorizační klíč." border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. Přejděte do Virtual Network brány, kterou jste vytvořili v předchozím kroku, a v části **Nastavení** vyberte **připojení**. Na stránce **připojení** vyberte **+ Přidat**.

1. Na stránce **Přidat připojení** zadejte hodnoty pro pole a vyberte **OK**. 

   | Pole | Hodnota |
   | --- | --- |
   | **Název**  | Zadejte název tohoto připojení.  |
   | **Typ připojení**:  | Vyberte **ExpressRoute**.  |
   | **Uplatnit autorizaci**  | Ujistěte se, že je toto políčko zaškrtnuté.  |
   | **Brána virtuální sítě** | Brána Virtual Network, kterou jste předtím vytvořili.  |
   | **Autorizační klíč**  | Zkopírujte autorizační klíč a vložte ho z karty ExpressRoute pro vaši skupinu prostředků. |
   | **Identifikátor URI rovnocenného okruhu**  | Zkopírujte a vložte ID ExpressRoute z karty ExpressRoute pro vaši skupinu prostředků.  |

   :::image type="content" source="../media/expressroute-global-reach/expressroute-add-connection.png" alt-text="Snímek obrazovky se stránkou přidat připojení pro připojení ExpressRoute k bráně virtuální sítě":::

Vytvoří se připojení mezi okruhem ExpressRoute a vaším Virtual Network.

:::image type="content" source="../media/expressroute-global-reach/virtual-network-gateway-connections.png" alt-text="Snímek obrazovky s připojením brány virtuální sítě":::