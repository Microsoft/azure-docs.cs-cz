---
title: Registrace aplikace veřejným klientem v Azure Active Directory – rozhraní API služby Azure pro FHIR
description: Tento článek vysvětluje postup při registraci aplikace veřejným klientem v Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 69504bc9ba0420b47a26519a0b112ff102171254
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824016"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Registrace aplikace veřejným klientem v Azure Active Directory

V tomto článku se dozvíte, jak zaregistrovat veřejné aplikace v Azure Active Directory. Registrace aplikace klienta jsou služby Azure Active Directory reprezentace aplikace, které můžete ověřit a požádat o oprávnění k rozhraní API jménem uživatele. Veřejní klienti jsou aplikace, jako je mobilní aplikace a jednostránkové aplikace jazyka javascript, které nelze uchovávat tajné kódy důvěrné. Postup je podobný [registrace důvěrnému klientovi](register-confidential-azure-ad-client-app.md), ale protože veřejní klienti nemůže být důvěryhodný pro uložení tajný klíč aplikace, není nutné pro přidání jednoho.

## <a name="app-registrations-in-azure-portal"></a>Registrace aplikací na webu Azure portal

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na levém navigačním panelu na **Azure Active Directory**.

2. V **Azure Active Directory** okna, klikněte na tlačítko **registrace aplikací (Preview)**:

    ![Azure portal. Registrace nové aplikace.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klikněte na tlačítko **registrace nové**.

## <a name="application-registration-overview"></a>Registrace aplikace – přehled

1. Dejte aplikaci se zobrazovaným názvem.

2. Zadejte adresu URL odpovědi. Adresa URL odpovědi je, kde ověřovací kódy vrátí do klientské aplikace. Můžete přidat další adresy URL odpovědí a upravovat stávající později.

    ![Azure portal. Veřejná registrace nové aplikace.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>Oprávnění rozhraní API

Podobně jako [důvěrnému klientovi aplikace](register-confidential-azure-ad-client-app.md), budete muset vybrat, jaká rozhraní API oprávnění této aplikace by měl mít možnost požádat o jménem uživatelů:

1. Otevřít **oprávnění k rozhraní API** a vyberte váš [registrace aplikace prostředku rozhraní API FHIR](register-resource-azure-ad-client-app.md):

    ![Azure portal. Nová veřejná oprávnění rozhraní API.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-API-PERMISSIONS.png)

2. Vyberte obory, které chcete mít možnost požádat o aplikaci.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak zaregistrovat veřejné klientské aplikace v Azure Active Directory. V dalším kroku nasaďte rozhraní API FHIR v Azure.
 
>[!div class="nextstepaction"]
>[Nasazení Open FHIR zdrojového serveru](fhir-oss-powershell-quickstart.md)
