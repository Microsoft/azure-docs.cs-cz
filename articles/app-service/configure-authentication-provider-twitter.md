---
title: Konfigurace ověřování na Twitteru
description: Naučte se nakonfigurovat ověřování na Twitter jako zprostředkovatele identity pro App Service nebo Azure Functions aplikaci.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077971"
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

   > [!IMPORTANT]
   > Tajný klíč rozhraní API je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte s vaší aplikací.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Přidání informací o Twitteru do aplikace

1. Přihlaste se k [Azure Portal] a přejděte do aplikace.
1. V nabídce na levé straně vyberte **ověřování** . Klikněte na **Přidat zprostředkovatele identity**.
1. V rozevíracím seznamu Zprostředkovatel identity vyberte **Twitter** . Vložte `API key` `API secret key` hodnoty a, které jste získali dříve.

    Tajný kód bude uložen jako [nastavení aplikace](./configure-common.md#configure-app-settings) typu "slot – vždy" s názvem `TWITTER_PROVIDER_AUTHENTICATION_SECRET` . Toto nastavení můžete později aktualizovat, aby se používaly [Key Vault odkazy](./app-service-key-vault-references.md) , pokud chcete tajný klíč spravovat v Azure Key Vault.

1. Pokud se jedná o prvního zprostředkovatele identity nakonfigurovaného pro aplikaci, zobrazí se také výzva s oddílem **nastavení ověřování App Service** . V opačném případě se můžete přesunout k dalšímu kroku.
    
    Tyto možnosti určují, jak vaše aplikace reaguje na neověřené požadavky, a výchozí výběry přesměrují všechny požadavky na přihlášení pomocí tohoto nového poskytovatele. Přizpůsobením tohoto chování teď můžete změnit nebo upravit toto nastavení později z hlavní obrazovky pro **ověřování** výběrem možnosti **Upravit** vedle **nastavení ověřování**. Další informace o těchto možnostech najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Klikněte na **Přidat**.

Teď jste připraveni použít Twitter pro ověřování ve vaší aplikaci. Poskytovatel bude uveden na obrazovce **ověřování** . Odtud můžete tuto konfiguraci poskytovatele upravit nebo odstranit.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Vývojáři na Twitteru]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
