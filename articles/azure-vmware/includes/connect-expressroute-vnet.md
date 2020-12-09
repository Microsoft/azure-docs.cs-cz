---
title: Připojení ExpressRoute k bráně virtuální sítě
description: Postup připojení ExpressRoute k bráně virtuální sítě.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 5f9a565a7662041dbd85e61388129496fa376962
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861509"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Přejděte do privátního cloudu, který jste vytvořili v kurzu [nasazení clusteru vSphere v Azure](../tutorial-create-private-cloud.md) . V části **Spravovat** vyberte **připojení** vyberte kartu **ExpressRoute** .

1. Zkopírujte autorizační klíč. Pokud není k dispozici autorizační klíč, musíte ho vytvořit, vybrat **+ požádat o autorizační klíč**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Zkopírujte autorizační klíč. Pokud není k dispozici autorizační klíč, musíte ho vytvořit, vybrat + požádat o autorizační klíč." border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::

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

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Na stránce Přidat připojení zadejte hodnoty pro pole a vyberte OK." border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

Vytvoří se připojení mezi okruhem ExpressRoute a vaším Virtual Network.