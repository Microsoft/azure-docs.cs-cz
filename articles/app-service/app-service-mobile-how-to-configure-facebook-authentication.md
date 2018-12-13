---
title: Konfigurace ověřování Facebook - službě Azure App Service
description: Zjistěte, jak nakonfigurovat ověřování sítě Facebook pro aplikaci App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: cc10c9be5bab3b84c8773d8a930473267db353ab
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256770"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Konfigurace aplikace App Service pro použití přihlášení k Facebooku
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Toto téma ukazuje, jak nakonfigurovat služby Azure App Service pro použití jako zprostředkovatele ověřování sítě Facebook.

K dokončení postupu v tomto tématu, musíte mít Facebookový účet, který má ověřený e-mailovou adresu a číslo mobilního telefonu. Pokud chcete vytvořit nový účet Facebook, přejděte na [facebook.com].

## <a name="register"> </a>Registrace vaší aplikace se službou Facebook.
1. Přihlaste se k [Azure Portal]a přejděte k vaší aplikaci. Kopii vaší **URL**. Bude použijte ji ke konfiguraci aplikace Facebook.
2. V jiném okně prohlížeče přejděte [Vývojáři Facebooku] web a přihlaste se pomocí vaší sítě Facebook přihlašovací údaje účtu.
3. (Volitelné) Pokud jste ještě nezaregistrovali, klikněte na tlačítko **aplikace** > **zaregistrujte se jako vývojář**, přijměte zásady a postupujte podle kroků registrace.
4. Klikněte na tlačítko **Moje aplikace** > **přidejte novou aplikaci**.
5. V **zobrazovaný název**, zadejte jedinečný název pro vaši aplikaci. Také poskytnout vaše **E-mail kontaktu**a potom klikněte na tlačítko **vytvoření ID aplikace** a dokončit kontrolu zabezpečení. Tím přejdete na řídicí panel pro vývojáře pro novou aplikaci Facebook.
6. V části **přihlášení k Facebooku**, klikněte na tlačítko **nastavení**a klikněte na tlačítko **nastavení** v levém navigačním panelu v části **přihlášení k Facebooku**.
7. Přidejte svoji aplikaci **identifikátor URI pro přesměrování** k **identifikátory URI pro přesměrování OAuth platný**, pak klikněte na tlačítko **uložit změny**.
   
   > [!NOTE]
   > Vaše přesměrování identifikátoru URI je adresa URL vaší aplikace s cestu, */.auth/login/facebook/callback*. Například, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Ujistěte se, že budou používat schéma HTTPS.
   > 
   > 
8. V levém navigačním panelu klikněte na tlačítko **nastavení** > **základní**. Na **tajný kód aplikace** pole, klikněte na tlačítko **zobrazit**, zadejte heslo, pokud požadovaný, poznamenejte si hodnoty **ID aplikace** a **tajný kód aplikace** . Můžete použít později ke konfiguraci vaší aplikace v Azure.
   
   > [!IMPORTANT]
   > Tajný kód aplikace je důležitým bezpečnostním pověřením. S kýmkoli sdílet tento tajný kód nebo distribuovat v rámci klientské aplikace.
   > 
   > 
9. Facebookový účet, který se použije k registraci aplikace je správce aplikace. V tomto okamžiku pouze správci se můžete přihlásit do této aplikace. K ověřování ostatních účtů pro Facebook, klikněte na tlačítko **revizi aplikace** a povolit **veřejná značka < your-app-name >** k povolení obecné veřejného přístupu ověřováním služby Facebook.

## <a name="secrets"> </a>Přidejte informace o službě Facebook pro vaši aplikaci
1. Zpátky [Azure Portal], přejděte k vaší aplikaci. Klikněte na tlačítko **nastavení** > **ověřování / autorizace**a ujistěte se, že **ověřování pomocí služby App Service** je **na**.
2. Klikněte na tlačítko **Facebook**, vložením hodnoty ID aplikace a tajný kód aplikace, které jste získali dříve, volitelně povolit všechny obory, které vaše aplikace vyžaduje a potom klikněte na tlačítko **OK**.
   
    ![][0]
   
    Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. V kódu vaší aplikace musí uživateli uživatele autorizovat.
3. (Volitelné) Chcete-li omezit přístup k webu pouze uživatelům ověřit ve službě Facebook, nastavte **akce má být provedena, když požadavek nebude ověřený** k **Facebook**. K tomu je potřeba ověřit, že všechny požadavky, a všechny neověřené požadavky jsou přesměrovány na Facebook pro ověřování.
4. Po dokončení konfigurace ověřování, klikněte na tlačítko **Uložit**.

Nyní jste připraveni používat sítě Facebook pro ověřování ve vaší aplikaci.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Vývojáři Facebooku]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
