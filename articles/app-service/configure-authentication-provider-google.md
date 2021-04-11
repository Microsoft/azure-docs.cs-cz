---
title: Konfigurace ověřování Google
description: Naučte se nakonfigurovat ověřování Google jako zprostředkovatele identity pro App Service nebo Azure Functions aplikaci.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: f6bec32fa928e840569ed95c35a056db91ea9737
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077988"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Konfigurace App Service nebo Azure Functions aplikace pro používání přihlášení Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat Azure App Service nebo Azure Functions používat Google jako poskytovatele ověřování.

Chcete-li dokončit postup v tomto tématu, musíte mít účet Google, který má ověřenou e-mailovou adresu. Nový účet Google si můžete vytvořit na stránce [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registrace aplikace pomocí Google

1. Pokud chcete vytvořit ID klienta a tajný klíč klienta, postupujte podle dokumentace Google na webu [google Sign-In pro aplikace na straně serveru](https://developers.google.com/identity/sign-in/web/server-side-flow) . Není nutné dělat žádné změny kódu. Stačí použít následující informace:
    - U **autorizovaných zdrojů JavaScriptu** použijte `https://<app-name>.azurewebsites.net` s názvem vaší aplikace v *\<app-name>* .
    - Pro **autorizovaný identifikátor URI přesměrování** použijte `https://<app-name>.azurewebsites.net/.auth/login/google/callback` .
1. Zkopírujte ID aplikace a hodnoty tajného klíče aplikace.

    > [!IMPORTANT]
    > Tajný klíč aplikace je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte v klientské aplikaci.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Přidání informací Google do aplikace

1. Přihlaste se k [Azure Portal] a přejděte do aplikace.
1. V nabídce na levé straně vyberte **ověřování** . Klikněte na **Přidat zprostředkovatele identity**.
1. V rozevíracím seznamu Zprostředkovatel identity vyberte **Google** . Vložte ID aplikace a hodnoty tajného klíče aplikace, které jste získali dříve.

    Tajný kód bude uložen jako [nastavení aplikace](./configure-common.md#configure-app-settings) typu "slot – vždy" s názvem `GOOGLE_PROVIDER_AUTHENTICATION_SECRET` . Toto nastavení můžete později aktualizovat, aby se používaly [Key Vault odkazy](./app-service-key-vault-references.md) , pokud chcete tajný klíč spravovat v Azure Key Vault.

1. Pokud se jedná o prvního zprostředkovatele identity nakonfigurovaného pro aplikaci, zobrazí se také výzva s oddílem **nastavení ověřování App Service** . V opačném případě se můžete přesunout k dalšímu kroku.
    
    Tyto možnosti určují, jak vaše aplikace reaguje na neověřené požadavky, a výchozí výběry přesměrují všechny požadavky na přihlášení pomocí tohoto nového poskytovatele. Přizpůsobením tohoto chování teď můžete změnit nebo upravit toto nastavení později z hlavní obrazovky pro **ověřování** výběrem možnosti **Upravit** vedle **nastavení ověřování**. Další informace o těchto možnostech najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Volitelné Klikněte na **Další: obory** a přidejte libovolné obory, které aplikace potřebuje. Budou požadovány v době přihlášení pro toky založené na prohlížeči.
1. Klikněte na **Přidat**.

Teď jste připravení použít Google pro ověřování ve vaší aplikaci. Poskytovatel bude uveden na obrazovce **ověřování** . Odtud můžete tuto konfiguraci poskytovatele upravit nebo odstranit.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

