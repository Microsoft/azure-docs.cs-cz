---
title: Konfigurace ověřování na Twitteru
description: Přečtěte si, jak nakonfigurovat ověřování na Twitteru jako poskytovatele identity pro vaši aplikaci App Service.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: seodec18
ms.openlocfilehash: 794f671b36b5aeb9f19cf5d80e488500cedb1098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207131"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Konfigurace aplikace App Service tak, aby používala přihlášení na Twitteru

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Tento článek ukazuje, jak nakonfigurovat službu Azure App Service tak, aby používala Twitter jako poskytovatele ověřování.

Chcete-li dokončit postup v tomto článku, potřebujete účet Twitter, který má ověřenou e-mailovou adresu a telefonní číslo. Chcete-li vytvořit nový účet twitter, přejděte na [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Zaregistrujte svou žádost s Twitter

1. Přihlaste se k [portálu Azure] a přejděte do své aplikace. Zkopírujte **adresu URL**. Budete ji používat ke konfiguraci aplikace Twitter.
1. Přejděte na web [Vývojářů Twitteru,] přihlaste se pomocí přihlašovacích údajů k účtu na Twitteru a vyberte **Vytvořit aplikaci**.
1. Zadejte **název aplikace** a popis **aplikace** pro novou aplikaci. Vložte adresu **URL** aplikace do pole **URL webu.** V části **Adresy URL zpětného volání** zadejte adresu URL HTTPS aplikace `/.auth/login/twitter/callback`App Service a přidejte cestu . Například, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. V dolní části stránky zadejte alespoň 100 znaků **Řekněte nám, jak bude tato aplikace použita**, a pak vyberte **Vytvořit**. V automaticky otevírané majestátné části klikněte na **Vytvořit** znovu. Zobrazí se podrobnosti o aplikaci.
1. Vyberte kartu **Klíče a přístupové tokeny.**

   Poznamenejte si tyto hodnoty:
   - Klíč rozhraní API
   - Tajný klíč rozhraní API

   > [!NOTE]
   > Tajný klíč rozhraní API je důležité pověření zabezpečení. Nesdílejte tento tajný klíč s nikým ani jej nedistribuujte s aplikací.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Přidání informací o Twitteru do aplikace

1. Přejděte do vaší aplikace na [webu Azure Portal].
1. Vyberte **Nastavení** > **ověřování / autorizace**a ujistěte se, že je **zapnuté ověřování služby App Service** . **On**
1. Vyberte **Možnost Twitter**.
1. Vložte `API key` hodnoty `API secret key` a, které jste získali dříve.
1. Vyberte **OK**.

   ![Snímek obrazovky s nastavením Mobilní aplikace Twitter][1]

   Ve výchozím nastavení služba App Service poskytuje ověřování, ale neomezuje oprávněný přístup k obsahu webu a souborům API. V kódu aplikace musíte autorizovat uživatele.

1. (Nepovinné) Chcete-li omezit přístup k webu pouze na uživatele ověřené twitterem, nastavte **akci, která má být přijata v případě, že žádost není ověřena** na **Twitteru**. Když nastavíte tuto funkci, vaše aplikace vyžaduje, aby byly ověřeny všechny požadavky. Také přesměruje všechny neověřené požadavky na Twitter pro ověřování.

   > [!CAUTION]
   > Omezení přístupu tímto způsobem platí pro všechna volání do vaší aplikace, což nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, jako v mnoha jednostránkových aplikacích. Pro takové aplikace **povolit anonymní požadavky (žádná akce)** může být upřednostňována tak, aby aplikace ručně spustí ověřování sám. Další informace naleznete v [tématu Ověřování toku](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Uložit**.

Nyní jste připraveni používat Twitter pro ověřování ve vaší aplikaci.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter Vývojáři]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Portál Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
