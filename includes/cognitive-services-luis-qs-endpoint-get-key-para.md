---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 25a70d314ecb690a34e89a3dff7d2c4fad8b7a00
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42820117"
---
Přístup ke koncovému bodu předpovědi je součástí klíče rozhraní koncového bodu. Pro účely tohoto rychlého startu použijte bezplatnou úvodní klíč spojené s vaším účtem služby LUIS. 
 
1. Přihlaste se pomocí svého účtu služby LUIS. 

    [![Seznam aplikací – snímek obrazovky z Language Understanding (LUIS)](media/cognitive-services-luis/app-list.png "seznamu aplikací – snímek obrazovky z Language Understanding (LUIS)")](media/cognitive-services-luis/app-list.png)

2. V pravé horní nabídce vyberte své jméno a pak vyberte **nastavení**.

    ![Přístup do nabídky nastavení uživatelské služby LUIS](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Zkopírujte hodnotu **klíč pro tvorbu**. Použijete ji později v tomto rychlém startu. 

    [![Snímek obrazovky z Language Understanding (LUIS) uživatelská nastavení](media/cognitive-services-luis/get-user-authoring-key.png "uživatelská nastavení snímku obrazovky z Language Understanding (LUIS)")](media/cognitive-services-luis/get-user-authoring-key.png)

    Vytváření klíč umožňuje aplikaci zdarma neomezený počet požadavků rozhraní API pro vytváření obsahu a až 1000 dotazů k předpovědi koncový bod rozhraní API za měsíc pro všechny vaše aplikace LUIS. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->