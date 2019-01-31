---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 14678a143e1b14b9b0b89435f356ac5df98ef40c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478677"
---
Přístup ke koncovému bodu předpovědí umožňuje klíč koncového bodu. Pro účely tohoto rychlého startu použijte bezplatný počáteční klíč přidružený k vašemu účtu LUIS. 
 
1. Přihlaste se pomocí účtu LUIS. 

    [![Snímek obrazovky se seznamem aplikací LUIS](media/cognitive-services-luis/app-list.png "Snímek obrazovky se seznamem aplikací LUIS")](media/cognitive-services-luis/app-list.png)

2. V pravé horní nabídce vyberte své jméno a potom vyberte **Nastavení**.

    ![Přístup do nabídky nastavení uživatelů služby LUIS](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. Zkopírujte hodnotu **klíče pro vytváření obsahu**. Později ho ještě použijete. 

    [![Snímek obrazovky s nastavením uživatele služby LUIS](media/cognitive-services-luis/get-user-authoring-key.png "Snímek obrazovky s nastavením uživatele služby LUIS")](media/cognitive-services-luis/get-user-authoring-key.png)

    Díky klíči pro vytváření obsahu získáte pro všechny aplikace LUIS neomezený počet bezplatných požadavků na rozhraní API pro vytváření a až 1000 dotazů na rozhraní API koncového bodu předpovědí měsíčně. <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
