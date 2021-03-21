---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b32ff0926aa2b797bdb8dfcbb01cf845080a0ce9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93376169"
---
1. Přejděte na [Google Cloud Console](https://console.developers.google.com/cloud-resource-manager) a přihlaste se pomocí svých přihlašovacích údajů k účtu Google. 
2. Na panelu nástrojů vyberte **Vytvořit projekt**. 
   
    ![Vytvoření nového projektu](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. Do pole **Project name** (Název projektu) zadejte název vašeho produktu a klikněte na **Create** (Vytvořit).
4. Vyberte na panelu nástrojů tlačítko **upozornění** a v seznamu vyberte váš projekt. Zobrazí se řídicí panel pro váš projekt. Můžete také přejít přímo na řídicí panel pomocí adresy URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

    ![Výběr projektu v upozorněních](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Poznamenejte si údaj **Project number** (Číslo projektu), který se nachází v řídicím panelu na dlaždici **Project info** (Informace o projektu). 

    ![ID projektu](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. V řídicím panelu na dlaždici **APIs** (Rozhraní API) vyberte **Go to APIs overview** (Přejít na přehled rozhraní API). 

    ![Odkaz na přehled rozhraní API](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. Na stránce rozhraní **API** vyberte **ENABLE APIS AND SERVICES** (Povolit rozhraní API a služby). 

    ![Tlačítko pro povolení rozhraní API a služeb](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. Vyhledejte a vyberte službu **Google Cloud Messaging**. 

    ![Vyhledání a výběr služby Google Cloud Messaging](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. Pokud chcete pro projekt povolit službu Google Cloud Messaging, vyberte **ENABLE** (Povolit).

    ![Povolení služby GCM (Google Cloud Messaging)](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Na panelu nástrojů vyberte **Create credentials** (Vytvořit přihlašovací údaje). 

    ![Tlačítko pro vytvoření přihlašovacích údajů](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. Na stránce **Add credentials to your project** (Přidání přihlašovacích údajů k vašemu projektu) vyberte odkaz **API key** (Klíč rozhraní API). 

    ![Přidat přihlašovací údaje](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. Na stránce **API key** (Klíč rozhraní API) vyberte **Create/Save** (Vytvořit nebo Uložit). V následujícím příkladu je vybraná možnost **IP addresses** (IP adresy) a pro povolené IP adresy je zadaná hodnota **0.0.0.0/0**. Klíč rozhraní API byste měli vhodným způsobem omezit. 

    ![Klíč rozhraní API – tlačítko pro vytvoření](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. Zkopírujte **API key** (Klíč rozhraní API) do schránky a uložte si ho někam. 

    ![Zkopírování klíče rozhraní API](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Pomocí této hodnoty klíče API potom povolíte službě Azure ověření na serveru GCM a odesílání nabízených oznámení jménem vaší aplikace. K návratu na řídicí panel projektu použijte adresu URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

