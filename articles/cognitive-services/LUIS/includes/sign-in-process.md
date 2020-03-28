---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 155c88ec4766391f70701b17038b915c399d8b0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77372300"
---
## <a name="sign-in-to-luis-portal"></a>Přihlášení k portálu LUIS

Nový uživatel služby LUIS musí postupovat podle tohoto postupu:

1. Přihlaste se k [portálu LUIS (preview),](https://preview.luis.ai)vyberte svou zemi a odsouhlaste podmínky použití. Pokud se místo toho zobrazí **moje aplikace,** prostředek LUIS již existuje a měli byste přeskočit dopředu k vytvoření aplikace.

1. Vyberte **Vytvořit prostředek Azure** a pak vyberte Vytvořit **vývojový prostředek, do kterých chcete přenést aplikace.**

    ![Výběr prostředku vytváření language understanding](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Vyplňte podrobnosti o zdroji.

    ![Vytvoření vývojového prostředku](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Při **vytváření nového zdrojového zdroje**pro vytváření zadejte následující informace:

    * **Název prostředku** – vlastní název, který zvolíte, který se používá jako součást adresy URL pro dotazy koncového bodu vytváření a předpovědi.
    * **Tenant** – tenant, ke kterého je vaše předplatné Azure přidruženo.
    * **Název předplatného** - předplatné, které se bude účtovat pro prostředek.
    * **Skupina prostředků** – vlastní název skupiny prostředků, který zvolíte nebo vytvoříte. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.
    * **Umístění** - volba umístění je založena na výběru **skupiny prostředků.**
    * **Cenová úroveň** – cenová úroveň určuje maximální transakci za sekundu a měsíc.

1. Zobrazí se souhrn zdroje, který má být vytvořen. Vyberte **další**.

    ![Vytvoření vývojového prostředku](../media/sign-in/sign-in-confirm-key-selection.png)

1. Potvrďte výběrem možnosti **Pokračovat**.

    ![Vytvoření vývojového prostředku](../media/sign-in/sign-in-confirm-continue.png)