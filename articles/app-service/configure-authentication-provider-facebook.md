---
title: Konfigurace ověřování na Facebooku
description: Přečtěte si, jak nakonfigurovat ověřování na Facebooku jako poskytovatele identity pro vaši aplikaci App Service nebo Azure Functions.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9a2d390a5647ed90284730e9186e981b8e699d10
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438017"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Konfigurace aplikace App Service nebo aplikace Azure Functions tak, aby používala facebookové přihlašovací údaje

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Tento článek ukazuje, jak nakonfigurovat Azure App Service nebo Azure Functions pro použití Facebooku jako poskytovatele ověřování.

Chcete-li dokončit postup v tomto článku, potřebujete účet Na Facebooku, který má ověřenou e-mailovou adresu a číslo mobilního telefonu. Pokud chcete vytvořit nový Facebook účet, přejděte na [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Zaregistrujte svou přihlášku na Facebooku

1. Přejděte na web [Facebook Developers] a přihlaste se pomocí přihlašovacích údajů k účtu na Facebooku.

   Pokud nemáte účet Facebook pro vývojáře, vyberte **Začínáme** a postupujte podle pokynů pro registraci.
1. Vyberte **Moje aplikace** > **Přidat novou aplikaci**.
1. V poli **Zobrazovaný název:**
   1. Zadejte jedinečný název aplikace.
   1. Zadejte **svůj kontaktní e-mail**.
   1. Vyberte **Vytvořit ID aplikace**.
   1. Dokončete bezpečnostní kontrolu.

   Otevře se řídicí panel pro vývojáře pro novou aplikaci Facebook.
1. Vyberte Možnost**Facebook Přihlásit** > **se** > na **řídicí panel** > Nastavit**web**.
1. V levém navigačním panelu pod **facebookovou položkou Přihlášení**vyberte **Nastavení**.
1. Do pole **Platné přesměrování identifikátorů URI oAuth** zadejte `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`. Nezapomeňte nahradit `<app-name>` názvem aplikace Azure App Service.
1. Vyberte **Uložit změny**.
1. V levém podokně vyberte **Nastavení** > **Základní**. 
1. V poli **Tajný soubor aplikací** vyberte **Zobrazit**. Zkopírujte hodnoty **ID aplikace** a **tajného klíče aplikace**. Později je použijete ke konfiguraci aplikace App Service v Azure.

   > [!IMPORTANT]
   > Tajný klíč aplikace je důležité pověření zabezpečení. Nesdílejte tento tajný klíč s nikým ani jej nedistribuujte v rámci klientské aplikace.
   >

1. Facebook účet, který jste použili k registraci aplikace je správceaplikace. V tomto okamžiku se k této aplikaci mohou přihlásit pouze správci.

   Pokud chcete ověřit další facebookové účty, vyberte **Revize aplikace** a povolte ** \<povolit, aby> název aplikace veřejný,** aby se k aplikaci mohla mít přístup k aplikaci pomocí ověřování na Facebooku.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Přidání informací o Facebooku do aplikace

1. Přihlaste se na [portál Azure] a přejděte do aplikace App Service.
1. Vyberte **Nastavení** > **ověřování / autorizace**a ujistěte se, že je **zapnuté ověřování služby App Service** . **On**
1. Vyberte **Facebook**a vložte do ID aplikace a tajných hodnot aplikace, které jste získali dříve. Povolte všechny obory potřebné pro vaši aplikaci.
1. Vyberte **OK**.

   ![Snímek obrazovky s nastavením mobilní aplikace Facebook][0]

    Ve výchozím nastavení služba App Service poskytuje ověřování, ale neomezuje oprávněný přístup k obsahu webu a souborům API. V kódu aplikace je potřeba autorizovat uživatele.
1. (Nepovinné) Chcete-li omezit přístup pouze na uživatele ověřené Facebookem, nastavte **akci, která má být přijata v případě, že žádost není ověřena** na **Facebooku**. Když nastavíte tuto funkci, vaše aplikace vyžaduje, aby byly ověřeny všechny požadavky. Také přesměruje všechny neověřené požadavky na Facebook pro ověřování.

   > [!CAUTION]
   > Omezení přístupu tímto způsobem platí pro všechna volání do vaší aplikace, což nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, jako v mnoha jednostránkových aplikacích. Pro takové aplikace **povolit anonymní požadavky (žádná akce)** může být upřednostňována tak, aby aplikace ručně spustí ověřování sám. Další informace naleznete v [tématu Ověřování toku](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Uložit**.

Teď jste připraveni používat Facebook k ověřování ve své aplikaci.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Vývojáři Facebooku]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[portál Azure]: https://portal.azure.com/
