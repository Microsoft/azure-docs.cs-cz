---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 09/02/2018
ms.author: diberry
ms.openlocfilehash: 00eb6f40e77cbdb64249600e35ca8531f1a744a8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258155"
---
## <a name="sign-in-to-luis-portal"></a>Přihlášení k portálu LUIS

Nový uživatel, který má LUIS, musí provést tento postup:

1. Přihlaste se k [portálu Luis](https://www.luis.ai), vyberte svou zemi a vyjádřete souhlas s podmínkami použití.
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

    ![Vytvořit prostředek pro vytváření](./media/sign-in-confirm-key-selection.png)

1. Krok 3 je potvrzení. Výběrem možnosti **pokračovat**potvrďte svůj výběr. 

    ![Vytvořit prostředek pro vytváření](./media/sign-in-confirm-continue.png)