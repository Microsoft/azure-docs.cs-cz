---
title: Jak nakonfigurovat Microsoft Account ověřování pro aplikaci aplikační služby
description: Zjistěte, jak nakonfigurovat Microsoft Account ověřování pro aplikaci aplikační služby.
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
ms.openlocfilehash: 4fb5bdf30502dbca3eba961165a1ab643427abd6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Postup konfigurace aplikace služby App Service k používání Microsoft Account přihlášení
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Toto téma ukazuje, jak nakonfigurovat služby Azure App Service pro použití Microsoft Account jako zprostředkovatel ověřování. 

## <a name="register-microsoft-account"> </a>Svou aplikaci zaregistrovat pomocí účtu Microsoft
1. Přihlaste se na [portál Azure]a přejděte k vaší aplikaci. Kopie vašeho **URL**, které později můžete použít ke konfiguraci vaší aplikace s Account Microsoft.
2. Přejděte na [Moje aplikace] stránky na webu Microsoft Developer Center Account a přihlaste se pomocí účtu Microsoft, v případě potřeby.
3. Klikněte na tlačítko **přidat aplikaci**, pak zadejte název aplikace a klikněte na **vytvořit**.
4. Poznamenejte si **ID aplikace**, protože jej budete potřebovat později. 
5. V části "Platformy", klikněte na **přidejte platformu** a vyberte možnost "Web".
6. V části "Identifikátory URI přesměrování" zadat koncový bod pro vaši aplikaci a pak klikněte na **Uložit**. 
   
   > [!NOTE]
   > Vaše přesměrování identifikátor URI je adresa URL aplikace připojí s cestou, */.auth/login/microsoftaccount/callback*. Například, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Ujistěte se, že používáte schéma HTTPS.
   
7. V části "Aplikace tajné údaje", klikněte na **generovat nové heslo**. Poznamenejte si hodnotu, která se zobrazí. Jakmile stránku opustit, nebudou zobrazeny znovu.

    > [!IMPORTANT]
    > Heslo je důležitým bezpečnostním pověřením. S kýmkoli sdílet heslo nebo distribuovat v rámci klientské aplikace.

## <a name="secrets"> </a>Přidání informací Account Microsoft do aplikace služby App Service
1. Zpět v [portál Azure], přejděte k aplikaci, klikněte na **nastavení** > **ověřování / autorizace**.
2. Pokud ověřování / autorizace funkce není povolena, je přepínač **na**.
3. Klikněte na tlačítko **účtu Microsoft**. Vložte hodnoty ID aplikace a heslo, které jste získali dříve a volitelně povolte všechny obory, které vaše aplikace vyžaduje. Pak klikněte na **OK**.
   
    ![][1]
   
    Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. Je nutné autorizovat uživatele v kódu aplikace.
4. (Volitelné) Chcete-li omezit přístup k webu jenom na uživatele ověřeného službou účet Microsoft, nastavte **akci provést, když požadavek nebude ověřený** k **Account Microsoft**. To vyžaduje, že všechny žádosti o ověření a všechny neověřené požadavky se přesměrují do účtu Microsoft pro ověřování.
5. Klikněte na **Uložit**.

Nyní jste připraveni použít Microsoft Account pro ověřování v aplikaci.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Moje aplikace]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portál Azure]: https://portal.azure.com/
