---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: ef7208596ead8f7d3903bb614ccb980df782e581
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837892"
---
## <a name="sign-in-to-luis-portal"></a>Přihlášení k portálu LUIS

Nový uživatel, který má LUIS, musí provést tento postup:

1. Přihlaste se k [portálu Luis](https://www.luis.ai), vyberte svou zemi/oblast a vyjádřete souhlas s podmínkami použití. Pokud se místo toho zobrazí **Moje aplikace** , prostředek Luis už existuje a před vytvořením aplikace byste ho měli přeskočit. Podporované oblasti najdete v [oblastech pro vytváření a publikování a v přidružených klíčích](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

1. Vyberte **vytvořit prostředek Azure** a pak vyberte **vytvořit prostředek pro vytváření obsahu, do kterého chcete své aplikace migrovat.**

    ![Zvolit typ prostředku pro vytváření Language Understanding](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Vyplňte podrobnosti prostředku.

    ![Vytvořit prostředek pro vytváření](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Při **vytváření nového prostředku pro vytváření obsahu**zadejte následující informace:

    * **Název prostředku** – vlastní název, který zvolíte, se používá jako součást adresy URL pro dotazy na koncový bod vytváření a předpovědi.
    * **Tenant** – tenant, ke kterému je předplatné Azure přidružené.
    * **Název předplatného** – předplatné, které se bude fakturovat za prostředek.
    * **Skupina prostředků** – název vlastní skupiny prostředků, který zvolíte nebo vytvoříte. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.
    * **Umístění** – Volba umístění je založená na výběru **skupiny prostředků** .
    * **Cenová úroveň** – cenová úroveň určuje maximální transakci za sekundu a měsíc.

1. Zobrazí se souhrn prostředku, který má být vytvořen. Vyberte **Další**.

    ![Vytvořit prostředek pro vytváření](../media/sign-in/sign-in-confirm-key-selection.png)

1. Potvrďte výběr možnosti **pokračovat**.

    ![Vytvořit prostředek pro vytváření](../media/sign-in/sign-in-confirm-continue.png)
