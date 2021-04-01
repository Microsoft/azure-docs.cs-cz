---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e006f804b8ab6411f4949424147acf567dc2ed24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "97371303"
---
## <a name="sign-in-to-luis-portal"></a>Přihlášení k portálu LUIS

[!INCLUDE [Note about portal deprecation](luis-portal-note.md)]

Nový uživatel, který má LUIS, musí provést tento postup:

1. Přihlaste se k [portálu Luis](https://www.luis.ai), vyberte svou zemi nebo oblast a vyjádřete podmínky použití. Pokud se místo toho zobrazí **Moje aplikace** , prostředek Luis už existuje a před vytvořením aplikace byste ho měli přeskočit. Přihlásíte se dvě možnosti:

    * Použití prostředku Azure (doporučeno) – umožňuje propojit účet LUIS s novým nebo existujícím prostředkem pro vytváření obsahu Azure. To je ekvivalentní k registraci, která se už migruje. Nebudete už muset projít [procesem migrace](../luis-migration-authoring.md#what-is-migration) později.

    * Pomocí zkušebního klíče. To vám umožní přihlásit se k LUIS pomocí zkušebního prostředku, který nemusíte nastavovat. Pokud zvolíte tuto možnost, bude nakonec nutné [migrovat svůj účet](../luis-migration-authoring.md#migration-steps) a propojit aplikace s prostředky pro vytváření obsahu.

1. V zobrazeném okně **zvolit vytváření obsahu** Najděte své předplatné Azure a Luis Resource Authoring. Pokud nemáte prostředek, můžete vytvořit nový.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Vyberte typ Language Understanding prostředek pro vytváření obsahu.":::
    
    Při vytváření nového prostředku pro vytváření obsahu zadejte následující informace:
    * **Název tenanta** – tenant, ke kterému je předplatné Azure přidružené.
    * **Název předplatného Azure** – předplatné, které se bude fakturovat za prostředek.
    * **Název skupiny prostředků Azure** – název vlastní skupiny prostředků, kterou zvolíte nebo vytvoříte. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.
    * **Název prostředku Azure** – vlastní název, který zvolíte, se používá jako součást adresy URL pro dotazy na koncový bod vytváření a předpovědi.
    * **Cenová úroveň** – cenová úroveň určuje maximální transakci za sekundu a měsíc.


