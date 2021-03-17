---
title: Konfigurace ověřování na Twitteru
description: Naučte se nakonfigurovat ověřování na Twitter jako zprostředkovatele identity pro App Service nebo Azure Functions aplikaci.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 11c913b12b4dcb7d2a5ffa532064b347b82904ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80519909"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Konfigurace aplikace App Service nebo Azure Functions, aby používala přihlášení k Twitteru

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure App Service nebo Azure Functions používat Twitter jako poskytovatele ověřování.

K dokončení postupu v tomto článku potřebujete účet na Twitteru s ověřenou e-mailovou adresou a telefonním číslem. Pokud chcete vytvořit nový účet na Twitteru, navštivte [Twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Registrace aplikace na Twitteru

1. Přihlaste se k [Azure Portal] a pokračujte do své aplikace. Zkopírujte **adresu URL**. Použijete ho ke konfiguraci aplikace Twitter.
1. Přejít na web pro [vývojáře na Twitteru] , přihlaste se pomocí přihlašovacích údajů k účtu Twitteru a vyberte **vytvořit aplikaci**.
1. Zadejte **název aplikace** a **Popis aplikace** pro novou aplikaci. Vložte **adresu URL** vaší aplikace do pole **Adresa URL webu** . V části **adresy URL zpětného volání** zadejte adresu URL https vaší aplikace App Service a přidejte cestu `/.auth/login/twitter/callback` . Například, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. V dolní části stránky zadejte aspoň 100 znaků v části **řekněte nám, jak se tato aplikace bude používat**, a pak vyberte **vytvořit**. V automaticky otevíraném okně klikněte na **vytvořit** znovu. Zobrazí se podrobnosti o aplikaci.
1. Vyberte kartu **klíče a přístupové tokeny** .

   Poznamenejte si tyto hodnoty:
   - Klíč rozhraní API
   - Tajný klíč rozhraní API

   > [!NOTE]
   > Tajný klíč rozhraní API je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte s vaší aplikací.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Přidání informací o Twitteru do aplikace

1. V [Azure Portal]přejdete do své aplikace.
1. Vyberte **Nastavení**  >  **ověřování/autorizace**a ujistěte se, že je **zapnuté** **ověřování App Service** .
1. Vyberte **Twitter**.
1. Vložte `API key` `API secret key` hodnoty a, které jste získali dříve.
1. Vyberte **OK**.

   ![Snímek obrazovky s nastavením Twitteru mobilní aplikace][1]

   Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.

1. Volitelné Pokud chcete omezit přístup k vašemu webu jenom na uživatele ověřené Twitterem, nastavte **akci, která se má provést, když se žádost neověřuje** na **Twitteru**. Když nastavíte tuto funkci, aplikace vyžaduje ověření všech požadavků. Také přesměruje všechny neověřené požadavky na Twitter pro ověřování.

   > [!CAUTION]
   > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. Pro takové aplikace může být vhodnější použití **anonymních požadavků (žádná akce)** , aby aplikace ručně spouštěla ověřování. Další informace najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Uložit**.

Nyní jste připraveni použít Twitter pro ověřování ve vaší aplikaci.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Vývojáři na Twitteru]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
