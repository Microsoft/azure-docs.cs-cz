---
title: Konfigurace ověřování na Facebooku
description: Naučte se nakonfigurovat ověřování na Facebooku jako zprostředkovatele identity pro App Service nebo Azure Functions aplikaci.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078005"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Konfigurace App Service nebo Azure Functions aplikace pro použití přihlašovacích údajů na Facebooku

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Tento článek popisuje, jak nakonfigurovat Azure App Service nebo Azure Functions pro použití Facebooku jako poskytovatele ověřování.

K dokončení postupu v tomto článku potřebujete účet Facebook s ověřenou e-mailovou adresou a mobilním telefonním číslem. Pokud chcete vytvořit nový účet Facebook, použijte [Facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registrace aplikace pomocí Facebooku

1. Navštivte web pro [vývojáře na Facebooku] a přihlaste se pomocí přihlašovacích údajů k účtu Facebook.

   Pokud nemáte účet Facebooku pro vývojáře **, vyberte Začínáme** a postupujte podle pokynů k registraci.
1. Vyberte **Moje aplikace**  >  **Přidat novou aplikaci**.
1. Do pole **Zobrazovaný název** :
   1. Zadejte jedinečný název vaší aplikace.
   1. Zadejte svůj **kontaktní e-mail**.
   1. Vyberte **vytvořit ID aplikace**.
   1. Dokončete kontrolu zabezpečení.

   Otevře se řídicí panel vývojář pro novou facebookovou aplikaci.
1. Vyberte možnost **řídicí panel**–  >  **přihlášení** k  >    >  **webu** Facebook.
1. V levém navigačním panelu v části **přihlášení na Facebooku** vyberte **Nastavení**.
1. Do pole **platné identifikátory URI pro přesměrování OAuth** zadejte `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . Nezapomeňte nahradit `<app-name>` názvem vaší aplikace Azure App Service.
1. Vyberte **Uložit změny**.
1. V levém podokně vyberte **Nastavení**  >  **základní**. 
1. V poli **tajný kód aplikace** vyberte **Zobrazit**. Zkopírujte hodnoty **ID aplikace** a **tajný klíč aplikace**. Později je můžete použít ke konfiguraci aplikace App Service v Azure.

   > [!IMPORTANT]
   > Tajný klíč aplikace je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte v klientské aplikaci.
   >

1. Účet Facebook, který jste použili k registraci aplikace, je správce aplikace. V tuto chvíli se k této aplikaci můžou přihlásit jenom správci.

   Pokud chcete ověřit jiné účty Facebook, vyberte možnost **Kontrola aplikace** a umožněte **\<your-app-name> veřejnému** veřejnému přístupu pro přístup k aplikaci pomocí ověřování na Facebooku.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Přidání informací o Facebooku do aplikace

1. Přihlaste se k [Azure Portal] a přejděte do aplikace.
1. V nabídce na levé straně vyberte **ověřování** . Klikněte na **Přidat zprostředkovatele identity**.
1. V rozevíracím seznamu Zprostředkovatel identity vyberte **Facebook** . Vložte ID aplikace a hodnoty tajného klíče aplikace, které jste získali dříve.

    Tajný kód bude uložen jako [nastavení aplikace](./configure-common.md#configure-app-settings) typu "slot – vždy" s názvem `FACEBOOK_PROVIDER_AUTHENTICATION_SECRET` . Toto nastavení můžete později aktualizovat, aby se používaly [Key Vault odkazy](./app-service-key-vault-references.md) , pokud chcete tajný klíč spravovat v Azure Key Vault.

1. Pokud se jedná o prvního zprostředkovatele identity nakonfigurovaného pro aplikaci, zobrazí se také výzva s oddílem **nastavení ověřování App Service** . V opačném případě se můžete přesunout k dalšímu kroku.
    
    Tyto možnosti určují, jak vaše aplikace reaguje na neověřené požadavky, a výchozí výběry přesměrují všechny požadavky na přihlášení pomocí tohoto nového poskytovatele. Přizpůsobením tohoto chování teď můžete změnit nebo upravit toto nastavení později z hlavní obrazovky pro **ověřování** výběrem možnosti **Upravit** vedle **nastavení ověřování**. Další informace o těchto možnostech najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Volitelné Klikněte na **Další: obory** a přidejte libovolné obory, které aplikace potřebuje. Budou požadovány v době přihlášení pro toky založené na prohlížeči.
1. Klikněte na **Přidat**.

Nyní jste připraveni použít Facebook pro ověřování ve vaší aplikaci. Poskytovatel bude uveden na obrazovce **ověřování** . Odtud můžete tuto konfiguraci poskytovatele upravit nebo odstranit.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Vývojáři Facebooku]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
