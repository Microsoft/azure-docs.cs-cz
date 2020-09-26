---
title: Připojení ExpressRoute k bráně virtuální sítě
description: Postup připojení ExpressRoute k bráně virtuální sítě.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: cc17de4ad9c16100a75d5ca2527b8cb0e7eef628
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254771"
---
<!-- Used in avs-deployment.md and tutorial-configure-networking.md -->

1. Přejděte do privátního cloudu, který jste vytvořili v předchozím kurzu, a v části **Spravovat**vyberte **připojení** . Vyberte kartu **ExpressRoute** .

1. Zkopírujte autorizační klíč. Pokud není k dispozici autorizační klíč, musíte ho vytvořit, vybrat **+ požádat o autorizační klíč**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Zkopírujte autorizační klíč. Pokud není k dispozici autorizační klíč, musíte ho vytvořit, vybrat + požádat o autorizační klíč." border="true":::

1. Přejděte do Virtual Network brány, kterou jste vytvořili v předchozím kroku, a v části **Nastavení**vyberte **připojení**. Na stránce **připojení** vyberte **+ Přidat**.

1. Na stránce **Přidat připojení** zadejte hodnoty pro pole a vyberte **OK**. 

   | Pole | Hodnota |
   | --- | --- |
   | **Název**  | Zadejte název tohoto připojení.  |
   | **Typ připojení**:  | Vyberte **ExpressRoute**.  |
   | **Uplatnit autorizaci**  | Ujistěte se, že je toto políčko zaškrtnuté.  |
   | **Brána virtuální sítě** | Brána Virtual Network, kterou jste předtím vytvořili.  |
   | **Autorizační klíč**  | Zkopírujte autorizační klíč a vložte ho z karty ExpressRoute pro vaši skupinu prostředků. |
   | **Identifikátor URI rovnocenného okruhu**  | Zkopírujte a vložte ID ExpressRoute z karty ExpressRoute pro vaši skupinu prostředků.  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Na stránce Přidat připojení zadejte hodnoty pro pole a vyberte OK." border="true":::

Vytvoří se připojení mezi okruhem ExpressRoute a vaším Virtual Network.