---
title: Konfigurace ověřování na Twitteru
description: Naučte se nakonfigurovat ověřování na Twitter jako zprostředkovatele identity pro vaši aplikaci App Service.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: seodec18
ms.openlocfilehash: 794f671b36b5aeb9f19cf5d80e488500cedb1098
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207131"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Konfigurace aplikace pro App Service, aby používala přihlášení k Twitteru

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Tento článek ukazuje, jak nakonfigurovat Azure App Service pro použití služby Twitter jako poskytovatele ověřování.

K dokončení postupu v tomto článku potřebujete účet na Twitteru s ověřenou e-mailovou adresou a telefonním číslem. Pokud chcete vytvořit nový účet na Twitteru, navštivte [Twitter.com].

## <a name="register"> </a>Registrace aplikace na Twitteru

1. Přihlaste se k [Azure Portal] a pokračujte do své aplikace. Zkopírujte **adresu URL**. Použijete ho ke konfiguraci aplikace Twitter.
1. Přejít na web pro [Vývojáři na Twitteru] , přihlaste se pomocí přihlašovacích údajů k účtu Twitteru a vyberte **vytvořit aplikaci**.
1. Zadejte **název aplikace** a **Popis aplikace** pro novou aplikaci. Vložte **adresu URL** vaší aplikace do pole **Adresa URL webu** . V části **adresy URL zpětného volání** zadejte adresu URL https vaší aplikace App Service a přidejte cestu `/.auth/login/twitter/callback`. například `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. V dolní části stránky zadejte aspoň 100 znaků v části **řekněte nám, jak se tato aplikace bude používat**, a pak vyberte **vytvořit**. V automaticky otevíraném okně klikněte na **vytvořit** znovu. Zobrazí se podrobnosti o aplikaci.
1. Vyberte kartu **klíče a přístupové tokeny** .

   Poznamenejte si tyto hodnoty:
   - Klíč rozhraní API
   - Tajný klíč rozhraní API

   > [!NOTE]
   > Tajný klíč rozhraní API je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte s vaší aplikací.

## <a name="secrets"> </a>Přidání informací o Twitteru do aplikace

1. V [Azure Portal]přejdete do své aplikace.
1. Vyberte **nastavení** > **ověřování/autorizace**a ujistěte se, že je **zapnuté** **ověřování App Service** .
1. Vyberte **Twitter**.
1. Vložte do `API key` a `API secret key` hodnoty, které jste získali dříve.
1. Vyberte **OK**.

   ![Snímek obrazovky s nastavením Twitteru mobilní aplikace][1]

   Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.

1. Volitelné Pokud chcete omezit přístup k vašemu webu jenom na uživatele ověřené Twitterem, nastavte **akci, která se má provést, když se žádost neověřuje** na **Twitteru**. Když nastavíte tuto funkci, aplikace vyžaduje ověření všech požadavků. Také přesměruje všechny neověřené požadavky na Twitter pro ověřování.

   > [!CAUTION]
   > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. Pro takové aplikace může být vhodnější použití **anonymních požadavků (žádná akce)** , aby aplikace ručně spouštěla ověřování. Další informace najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Save** (Uložit).

Nyní jste připraveni použít Twitter pro ověřování ve vaší aplikaci.

## <a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Vývojáři na Twitteru]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
