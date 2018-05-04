---
title: Postup konfigurace ověřování Facebook pro aplikaci aplikační služby
description: Informace o konfiguraci ověřování Facebook pro aplikaci aplikační služby.
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
ms.openlocfilehash: 1d2b294fc0663770f9a699e300672695225dfdfd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Konfigurace aplikace App Service pro použití přihlášení k Facebooku
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Toto téma ukazuje, jak nakonfigurovat služby Azure App Service použít k ověřování sítě Facebook.

Dokončete postup v tomto tématu, musí mít účet Facebook, který má ověřenou e-mailovou adresu a číslo mobilního telefonu. Chcete-li vytvořit nový účet Facebook, přejděte na [facebook.com].

## <a name="register"> </a>Registrace vaší aplikace se službou Facebook.
1. Přihlaste se na [portál Azure]a přejděte k vaší aplikaci. Kopie vašeho **URL**. To bude používat ke konfiguraci aplikace Facebook.
2. V jiném okně prohlížeče, přejděte na [Facebook vývojáři] webu a přihlaste se pomocí vaší sítě Facebook přihlašovací údaje účtu.
3. (Volitelné) Pokud jste ještě nezaregistrovali, klikněte na tlačítko **aplikace** > **zaregistrujte se jako vývojář**, přijměte zásady a postupujte podle kroků registrace.
4. Klikněte na tlačítko **Moje aplikace** > **přidejte novou aplikaci**.
5. V **zobrazovaný název**, zadejte jedinečný název pro vaši aplikaci. Také poskytují vaší **e-mailu kontaktujte**a potom klikněte na **vytvoření ID aplikace** a dokončit kontrolu zabezpečení. Tím přejdete na řídicí panel pro vývojáře pro novou aplikaci Facebook.
7. V části **Facebook přihlášení**, klikněte na tlačítko **nastavit**a potom zvolte **nastavení** v levém navigačním panelu v části **Facebook přihlášení**.
8. Přidejte svoji aplikaci **identifikátor URI pro přesměrování** k **identifikátory URI přesměrování platný OAuth**, pak klikněte na tlačítko **uložit změny**.
   
   > [!NOTE]
   > Vaše přesměrování identifikátor URI je adresa URL aplikace připojí s cestou, */.auth/login/facebook/callback*. Například, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Ujistěte se, že používáte schéma HTTPS.
   > 
   > 
7. V levém navigačním panelu, klikněte na tlačítko **nastavení** > **základní**. Na **tajný klíč aplikace** pole, klikněte na tlačítko **zobrazit**, zadejte heslo, pokud požadovaný, poznamenejte si hodnoty **ID aplikace** a **tajný klíč aplikace**. Můžete použít později ke konfiguraci vaší aplikace v Azure.
   
   > [!IMPORTANT]
   > Tajný klíč aplikace je důležitým bezpečnostním pověřením. S kýmkoli sdílet tento tajný klíč nebo distribuovat v rámci klientské aplikace.
   > 
   > 
8. Účet Facebook, který se používá k registraci aplikace je správce aplikace. V tomto okamžiku se jenom správci moct přihlásit do této aplikace. K ověření jiné účty Facebook, klikněte na tlačítko **revize aplikace** a povolte **zkontrolujte <-název_aplikace > veřejné** a povolte obecné veřejný přístup pomocí ověřování Facebook.

## <a name="secrets"> </a>Přidání Facebook informace do aplikace
1. Zpět v [portál Azure], přejděte k vaší aplikaci. Klikněte na tlačítko **nastavení** > **ověřování / autorizace**a ujistěte se, že **ověřování služby aplikace** je **na**.
2. Klikněte na tlačítko **Facebook**, vložte hodnoty ID aplikace a tajný klíč aplikace, které jste získali dříve, můžete také povolit všechny obory, které vyžaduje vaše aplikace a pak klikněte na **OK**.
   
    ![][0]
   
    Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. Je nutné autorizovat uživatele v kódu aplikace.
3. (Volitelné) Chcete-li omezit přístup k webu jenom na uživatele ověřeného službou Facebook, nastavte **akci provést, když požadavek nebude ověřený** k **Facebook**. To vyžaduje, že všechny žádosti o ověření a všechny neověřené požadavky se přesměrují do sítě Facebook pro ověření.
4. Po dokončení konfigurace ověřování, klikněte na tlačítko **Uložit**.

Nyní jste připraveni používat sítě Facebook pro ověření ve vaší aplikaci.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook vývojáři]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[portál Azure]: https://portal.azure.com/
