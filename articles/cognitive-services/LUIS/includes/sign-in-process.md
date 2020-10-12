---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.openlocfilehash: fda9df6c7e9651bbd3b0b70ad9d47f23c0c19d01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541423"
---
## <a name="sign-in-to-luis-portal"></a>Přihlášení k portálu LUIS

Nový uživatel, který má LUIS, musí provést tento postup:

1. Přihlaste se k [portálu Luis](https://www.luis.ai), vyberte svou zemi/oblast a vyjádřete souhlas s podmínkami použití. Pokud se místo toho zobrazí **Moje aplikace** , prostředek Luis už existuje a před vytvořením aplikace byste ho měli přeskočit. Podporované oblasti najdete v [oblastech pro vytváření a publikování a v přidružených klíčích](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

1. Vyberte **vytvořit prostředek Azure** a pak vyberte **vytvořit prostředek pro vytváření obsahu, do kterého chcete své aplikace migrovat.**

    ![Zvolit typ prostředku pro vytváření Language Understanding](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Vyplňte podrobnosti prostředku.

    ![Snímek obrazovky se zobrazí v podokně vytvořit nový prostředek pro vytváření obsahu.](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Při **vytváření nového prostředku pro vytváření obsahu**zadejte následující informace:

    * **Název prostředku** – vlastní název, který zvolíte, se používá jako součást adresy URL pro dotazy na koncový bod vytváření a předpovědi.
    * **Tenant** – tenant, ke kterému je předplatné Azure přidružené.
    * **Název předplatného** – předplatné, které se bude fakturovat za prostředek.
    * **Skupina prostředků** – název vlastní skupiny prostředků, který zvolíte nebo vytvoříte. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.
    * **Umístění** – Volba umístění je založená na výběru **skupiny prostředků** .
    * **Cenová úroveň** – cenová úroveň určuje maximální transakci za sekundu a měsíc.

1. Zobrazí se souhrn prostředku, který má být vytvořen. Vyberte **Další**.

    ![Snímek obrazovky s možností odkazu na účet Azure se zobrazí na úvodní stránce.](../media/sign-in/sign-in-confirm-key-selection.png)

1. Potvrďte výběr možnosti **pokračovat**.

    ![Snímek obrazovky s úvodní stránkou se zobrazí po propojení s účtem Azure.](../media/sign-in/sign-in-confirm-continue.png)
