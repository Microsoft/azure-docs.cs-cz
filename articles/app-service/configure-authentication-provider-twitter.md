---
title: Konfigurace ověřování na Twitteru
description: Naučte se nakonfigurovat ověřování na Twitter jako zprostředkovatele identity pro vaši aplikaci App Service.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 04/19/2018
ms.custom: seodec18
ms.openlocfilehash: b5ec17c18cec8053f0732366c0cc5d0c5003e4de
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670803"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Konfigurace aplikace pro App Service, aby používala přihlášení k Twitteru

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Tento článek ukazuje, jak nakonfigurovat Azure App Service pro použití služby Twitter jako poskytovatele ověřování.

K dokončení postupu v tomto článku potřebujete účet na Twitteru s ověřenou e-mailovou adresou a telefonním číslem. Pokud chcete vytvořit nový účet na Twitteru, navštivte [Twitter.com].

## <a name="register"> </a>Registrace aplikace na Twitteru

1. Přihlaste se k [Azure Portal] a pokračujte do své aplikace. Zkopírujte **adresu URL**. Použijete ho ke konfiguraci aplikace Twitter.
1. Přejít na web pro [Vývojáři na Twitteru] , přihlaste se pomocí přihlašovacích údajů k účtu Twitteru a vyberte **vytvořit novou aplikaci**.
1. Zadejte **název** a **Popis** nové aplikace. Vložte **adresu URL** vaší aplikace do pole **Web** . Do pole **Adresa URL zpětného volání** zadejte adresu URL vaší aplikace App Service a přidejte cestu `/.auth/login/aad/callback`. Například, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Ujistěte se, že používáte schéma HTTPS.
1. V dolní části stránky si přečtěte a přijměte podmínky. Vyberte **vytvořit aplikaci Twitter**. Zobrazí se podrobnosti o aplikaci.
1. Vyberte kartu **Nastavení** , zaškrtněte políčko **povoluje, aby se tato aplikace použila k přihlášení přes Twitter**, a pak vyberte **aktualizovat nastavení**.
1. Vyberte kartu **klíče a přístupové tokeny** .

   Poznamenejte si tyto hodnoty:
   - Klíč příjemce (klíč rozhraní API)
   - Tajný klíč uživatele (tajný kód rozhraní API)

   > [!NOTE]
   > Tajný klíč příjemce je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte s vaší aplikací.

## <a name="secrets"> </a>Přidání informací o Twitteru do aplikace

1. V [Azure Portal]přejdete do své aplikace.
1. Vyberte **nastavení** > **ověřování/autorizace**a ujistěte se, že je **zapnuté** **ověřování App Service** .
1. Vyberte **Twitter**.
1. Vložte do `API Key` a `API Secret` hodnoty, které jste získali dříve.
1. Vyberte **OK**.

   ![Snímek obrazovky s nastavením Twitteru mobilní aplikace][1]

   Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.

1. Volitelné Pokud chcete omezit přístup k vašemu webu jenom na uživatele ověřené Twitterem, nastavte **akci, která se má provést, když se žádost neověřuje** na **Twitteru**. Když nastavíte tuto funkci, aplikace vyžaduje ověření všech požadavků. Také přesměruje všechny neověřené požadavky na Twitter pro ověřování.

   > [!CAUTION]
   > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. Pro takové aplikace může být vhodnější použití **anonymních požadavků (žádná akce)** , aby aplikace ručně spouštěla ověřování. Další informace najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Save** (Uložit).

Nyní jste připraveni použít Twitter pro ověřování ve vaší aplikaci.

## <a name="related-content"></a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Vývojáři na Twitteru]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
