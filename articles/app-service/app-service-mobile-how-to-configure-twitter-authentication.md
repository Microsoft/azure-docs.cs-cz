---
title: Postup konfigurace ověřování pomocí Twitteru pro aplikaci App Services
description: Zjistěte, jak konfigurovat Twitter ověřování pro aplikaci App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 42d857fe712658ca3c17d824d061742cf7195e39
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967347"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Konfigurace aplikace App Service pro použití přihlášení k Twitteru
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Toto téma ukazuje, jak nakonfigurovat služby Azure App Service pro použití Twitteru jako zprostředkovatele ověřování.

K dokončení postupu v tomto tématu, musíte mít účtu sítě Twitter, která má ověřený e-mailovou adresu a telefonní číslo. Pokud chcete vytvořit nový účet Twitteru, přejděte na <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registrace aplikace pomocí Twitteru
1. Přihlaste se k [Azure Portal]a přejděte k vaší aplikaci. Kopii vaší **URL**. Které použijete k konfigurace aplikace pro Twitter.
2. Přejděte [Vývojáři na twitteru] webu, přihlaste se pomocí přihlašovacích údajů k účtu Twitteru a klikněte na tlačítko **vytvořit novou aplikaci**.
3. Zadejte **název** a **popis** pro novou aplikaci. Vložit ve vaší aplikaci **URL** pro **webu** hodnotu. Potom **adresu URL zpětného volání**, vložte **adresu URL zpětného volání** jste si zkopírovali dříve. Jde o vaše mobilní aplikace s cestu, */.auth/login/twitter/callback*. Například, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Ujistěte se, že budou používat schéma HTTPS.
4. V dolní části stránky přečtěte si a přijměte podmínky. Pak klikněte na tlačítko **vytvoření aplikace Twitter**. Registruje tato aplikace zobrazí podrobnosti o aplikaci.
5. Klikněte na tlačítko **nastavení** kartě **povolit této aplikace se použije k přihlášení pomocí Twitteru**, pak klikněte na tlačítko **aktualizace nastavení**.
6. Vyberte **klíče a přístupové tokeny** kartu. Poznamenejte si hodnoty **uživatelský klíč (klíč rozhraní API)** a **uživatelský tajný klíč (tajný klíč rozhraní API)**.
   
   > [!NOTE]
   > Uživatelský tajný klíč je důležitým bezpečnostním pověřením. S kýmkoli sdílet tento tajný kód nebo distribuovat s vaší aplikací.
   > 
   > 

## <a name="secrets"> </a>Přidání informací Twitter do aplikace
1. Zpátky [Azure Portal], přejděte k vaší aplikaci. Klikněte na tlačítko **nastavení**a potom **ověřování / autorizace**.
2. Pokud ověřování / autorizace funkce není povolená, přepněte přepínač **na**.
3. Klikněte na tlačítko **Twitter**. Vložte ID aplikace a tajný kód aplikace hodnoty, které jste získali dříve. Pak klikněte na **OK**.
   
   ![][1]
   
   Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. V kódu vaší aplikace musí uživateli uživatele autorizovat.
4. (Volitelné) Chcete-li omezit přístup k webu pouze uživatele ověřené služba Twitter, nastavte **akce má být provedena, když požadavek nebude ověřený** k **Twitter**. K tomu je potřeba ověřit, že všechny požadavky, a všechny neověřené požadavky jsou přesměrovány na Twitter pro ověřování.
5. Klikněte na **Uložit**.

Nyní jste připraveni pro účely ověření v aplikaci pro Twitter.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Vývojáři na twitteru]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
