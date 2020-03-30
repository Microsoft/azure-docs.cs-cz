---
title: Konfigurace ověřování Google
description: Přečtěte si, jak nakonfigurovat ověřování Google jako poskytovatele identity pro aplikaci App Service.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: seodec18
ms.openlocfilehash: 81ce3e393d308323c8d5a3d688c16c9b45e7be9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74670816"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Konfigurace aplikace App Service tak, aby používala přihlášení Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Toto téma ukazuje, jak nakonfigurovat službu Azure App Service tak, aby používala Google jako poskytovatele ověřování.

Chcete-li dokončit postup v tomto tématu, musíte mít účet Google s ověřenou e-mailovou adresou. Nový účet Google si můžete vytvořit na stránce [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Zaregistrujte svou aplikaci u Googlu

1. Chcete-li vytvořit id klienta a tajný klíč klienta, vytvořte dokumentaci Google na webu [Google Sign-In pro aplikace na straně serveru.](https://developers.google.com/identity/sign-in/web/server-side-flow) Není třeba provádět žádné změny kódu. Stačí použít následující informace:
    - U **autorizovaných počátků JavaScriptu**použijte `https://<app-name>.azurewebsites.net` s názvem aplikace v * \<>názvu aplikace *.
    - Pro **identifikátor URI autorizovaného přesměrování**použijte `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Zkopírujte ID aplikace a tajné hodnoty aplikace.

    > [!IMPORTANT]
    > Tajný klíč aplikace je důležité pověření zabezpečení. Nesdílejte tento tajný klíč s nikým ani jej nedistribuujte v rámci klientské aplikace.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Přidání informací Google do aplikace

1. Na [webu Azure Portal]přejděte do aplikace App Service.
1. Vyberte **Nastavení** > **ověřování / autorizace**a ujistěte se, že je **zapnuté ověřování služby App Service** . **On**
1. Vyberte **Google**a vložte do ID aplikace a tajných hodnot aplikace, které jste získali dříve. Povolte všechny obory potřebné pro vaši aplikaci.
1. Vyberte **OK**.

   Služba App Service poskytuje ověřování, ale neomezuje oprávněný přístup k obsahu webu a souborům API. Další informace naleznete v [tématu Autorizace nebo odepření uživatelům](app-service-authentication-how-to.md#authorize-or-deny-users).

1. (Nepovinné) Chcete-li omezit přístup k webu pouze na uživatele ověřené společností Google, nastavte **akci, která má být přijata v případě, že žádost není ověřena** společností **Google**. Když nastavíte tuto funkci, vaše aplikace vyžaduje, aby byly ověřeny všechny požadavky. Také přesměruje všechny neověřené požadavky na Google pro ověřování.

    > [!CAUTION]
    > Omezení přístupu tímto způsobem platí pro všechna volání do vaší aplikace, což nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, jako v mnoha jednostránkových aplikacích. Pro takové aplikace **povolit anonymní požadavky (žádná akce)** může být upřednostňována tak, aby aplikace ručně spustí ověřování sám. Další informace naleznete v [tématu Ověřování toku](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Uložit**.

Nyní jste připraveni používat Google pro ověřování ve vaší aplikaci.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Portál Azure]: https://portal.azure.com/

