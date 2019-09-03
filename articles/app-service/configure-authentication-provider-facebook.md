---
title: Konfigurace ověřování na Facebooku – Azure App Service
description: Přečtěte si, jak nakonfigurovat ověřování na Facebooku pro vaši aplikaci App Service.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 410d769d0d9abe3a0a0f9c45e3cf67bb94ec9f4d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232080"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Konfigurace aplikace App Service pro použití přihlášení k Facebooku
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat Azure App Service pro použití Facebooku jako poskytovatele ověřování.

Chcete-li dokončit postup v tomto tématu, musíte mít účet Facebook s ověřenou e-mailovou adresou a mobilním telefonním číslem. Pokud chcete vytvořit nový účet Facebook, použijte [Facebook.com].

## <a name="register"> </a>Registrace aplikace pomocí Facebooku
1. Přejděte na web pro [Vývojáři Facebooku] a přihlaste se pomocí přihlašovacích údajů k účtu Facebook.
3. Volitelné Pokud nemáte účet Facebook pro vývojáře, klikněte na tlačítko Začínámea postupujte podle pokynů k registraci.
4. Klikněte na **Moje aplikace** > **Přidat novou aplikaci**.
5. Do **zobrazeného názvu**zadejte jedinečný název vaší aplikace. Zadejte také **kontaktní e-mail**a potom klikněte na **vytvořit ID aplikace** a dokončete kontrolu zabezpečení. Otevře se řídicí panel vývojář pro vaši novou facebookovou aplikaci.
6. Klikněte na **řídicí panel** > **Facebook přihlášení** > **nastavit** > **Web**.
1. V levém navigačním panelu v části **přihlášení na Facebooku**klikněte na **Nastavení**.
1. V poli **platné identifikátory URI přesměrování OAuth**zadejte `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` a nahraďte  *\<název aplikace >* názvem vaší aplikace Azure App Service. Klikněte na tlačítko **uložit změny**.
8. V levém navigačním panelu klikněte na **Nastavení** > **základní**. V poli **tajný klíč aplikace** klikněte na tlačítko **Zobrazit**. Zkopírujte hodnoty **ID aplikace** a **tajný klíč aplikace**. Později je můžete použít ke konfiguraci aplikace App Service v Azure.
   
   > [!IMPORTANT]
   > Tajný klíč aplikace je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte v klientské aplikaci.
   > 
   > 
9. Účet Facebook, který jste použili k registraci aplikace, je správce aplikace. V tuto chvíli se k této aplikaci můžou přihlásit jenom správci. Pokud chcete ověřit jiné účty Facebooku, klikněte na **Revize aplikace** a povolit možnost **nastavit \<> veřejné jméno aplikace jako veřejný** , aby se povolil obecný veřejný přístup s použitím ověřování na Facebooku.

## <a name="secrets"> </a>Přidání informací o Facebooku do aplikace
1. Přihlaste se k [Azure Portal] a přejděte do aplikace App Service. Klikněte na **Nastavení** > **ověřování/autorizace**a ujistěte se, že je **zapnuté** **ověřování App Service** .
2. Klikněte na **Facebook**, vložte ID aplikace a hodnoty tajného klíče aplikace, které jste získali dříve, případně povolte libovolné obory, které vaše aplikace potřebuje, a pak klikněte na **OK**.
   
    ![][0]
   
    Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.
3. Volitelné Pokud chcete omezit přístup k webu jenom na uživatele ověřené přes Facebook, nastavte **akci, která se má provést, když se žádost neověřuje** na **Facebooku**. To vyžaduje, aby všechny požadavky byly ověřené a všechny neověřené požadavky se přesměrovaly na Facebook pro ověřování.
 
> [!CAUTION]
> Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. U takových aplikací může být upřednostňována možnost **povolení anonymních požadavků (bez akce)** , pokud se aplikace ručně spouští samotné přihlášení, jak je popsáno [zde](overview-authentication-authorization.md#authentication-flow).

4. Po dokončení konfigurace ověřování klikněte na **Uložit**.

Nyní jste připraveni použít Facebook pro ověřování ve vaší aplikaci.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Vývojáři Facebooku]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
