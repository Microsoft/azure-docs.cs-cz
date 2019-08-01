---
title: Konfigurace ověřování účtu Microsoft – Azure App Service
description: Přečtěte si, jak nakonfigurovat ověřování účtu Microsoft pro vaši aplikaci App Services.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 10b661f0c4b7dc45284b907e83df3c0372f97cab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561544"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Jak nakonfigurovat aplikaci App Service, aby používala přihlášení k účtu Microsoft
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat Azure App Service pro použití účtu Microsoft jako poskytovatele ověřování. 

## <a name="register-microsoft-account"> </a>Registrace aplikace pomocí účtu Microsoft
1. Přihlaste se k [Azure Portal]a přejděte do aplikace. Zkopírujte **adresu URL**, která později použijete ke konfiguraci aplikace pomocí účtu Microsoft.
2. Pokud je to požadováno, přejděte na [**Registrace aplikací**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)a přihlaste se pomocí účet Microsoft.
3. Klikněte na **Přidat aplikaci**, zadejte název aplikace a klikněte na **vytvořit**.
4. Poznamenejte si **ID aplikace**, jak ho budete potřebovat později. 
5. V části platformy klikněte na **Přidat platformu** a vyberte web.
6. V části "identifikátory URI pro přesměrování" Zadejte koncový bod pro vaši aplikaci a pak klikněte na **Uložit**. 
   
   > [!NOTE]
   > Identifikátor URI pro přesměrování je adresa URL vaší aplikace připojené s cestou, */.auth/Login/MicrosoftAccount/callback*. Například, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Ujistěte se, že používáte schéma HTTPS.
   
7. V části tajné kódy aplikací klikněte na **Generovat nové heslo**. Poznamenejte si hodnotu, která se zobrazí. Po opuštění stránky se znovu nezobrazí.

    > [!IMPORTANT]
    > Heslo je důležité bezpečnostní pověření. Nesdílejte heslo s kýmkoli ani ho distribuujte v klientské aplikaci.
    
8. Klikněte na **Uložit**.

## <a name="secrets"> </a>Přidání informací o účtu Microsoft do aplikace App Service
1. Zpátky v [Azure Portal]přejděte do aplikace, klikněte na **Nastavení** > **ověřování/autorizace**.
2. Pokud není povolená funkce ověřování/autorizace, přepněte ji **na**.
3. Klikněte na **účet Microsoft**. Vložte do hodnot ID aplikace a hesla, které jste získali dříve, a volitelně povolte libovolné rozsahy, které vaše aplikace vyžaduje. Pak klikněte na **OK**.
   
    ![][1]
   
    Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.
4. Volitelné Pokud chcete omezit přístup k webu jenom na uživatele ověřené pomocí účet Microsoft, nastavte **akci, která se má provést, když se žádost neověřuje** u **účtu Microsoft**. To vyžaduje, aby všechny požadavky byly ověřené a všechny neověřené požadavky byly přesměrovány na účet Microsoft pro ověřování.
5. Klikněte na **Uložit**.

Nyní jste připraveni použít účet Microsoft k ověřování ve vaší aplikaci.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
