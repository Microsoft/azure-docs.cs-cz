---
title: Konfigurace ověřování Account Microsoft - službě Azure App Service
description: Zjistěte, jak nakonfigurovat Microsoft Account ověřování pro aplikaci App Services.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e3da856efd7d44f15f9de27c9e38375d40dc211d
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410958"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Konfigurace aplikace App Service použít Microsoft Account login
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Toto téma ukazuje, jak nakonfigurovat služby Azure App Service pro použití Microsoft Account jako zprostředkovatel ověřování. 

## <a name="register-microsoft-account"> </a>Zaregistrujte svoji aplikaci pomocí účtu Microsoft
1. Přihlaste se k [Azure Portal]a přejděte k vaší aplikaci. Kopii vaší **URL**, který později můžete použít ke konfiguraci vaší aplikace s Account Microsoft.
2. Přejděte [Moje aplikace] stránce na webu Microsoft Account Developer Center a přihlaste se svým účtem Microsoft se v případě potřeby.
3. Klikněte na tlačítko **přidat aplikaci**, pak zadejte název aplikace a klikněte na tlačítko **vytvořit**.
4. Poznamenejte si, **ID aplikace**, protože ho budete potřebovat později. 
5. V části "Platformy", klikněte na **přidat platformy** a vyberte "Web".
6. V části "Identifikátory URI přesměrování" zadat koncový bod pro vaši aplikaci a pak klikněte na **Uložit**. 
   
   > [!NOTE]
   > Vaše přesměrování identifikátoru URI je adresa URL vaší aplikace s cestu, */.auth/login/microsoftaccount/callback*. Například, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Ujistěte se, že budou používat schéma HTTPS.
   
7. V části "Tajné kódy aplikace," klikněte na **generovat nové heslo**. Poznamenejte si hodnotu, která se zobrazí. Jakmile se stránku opustit, se znovu nezobrazí.

    > [!IMPORTANT]
    > Heslo je důležitým bezpečnostním pověřením. S kýmkoli sdílet hesla nebo distribuovat v rámci klientské aplikace.
    
8. Klikněte na **Uložit**.

## <a name="secrets"> </a>Přidat informace o Microsoft Account do svojí aplikace služby App Service
1. Zpátky [Azure Portal], přejděte k vaší aplikaci, klikněte na tlačítko **nastavení** > **ověřování / autorizace**.
2. Pokud ověřování / autorizace funkce není povolená, přepnout **na**.
3. Klikněte na tlačítko **účtu Microsoft**. Vložením hodnoty ID aplikace a heslo, které jste získali dříve a volitelně povolit všechny obory, které vaše aplikace vyžaduje. Pak klikněte na **OK**.
   
    ![][1]
   
    Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. V kódu vaší aplikace musí uživateli uživatele autorizovat.
4. (Volitelné) Chcete-li omezit přístup k webu pouze uživatele ověřené pomocí účtu Microsoft, nastavte **akce má být provedena, když požadavek nebude ověřený** k **Account Microsoft**. K tomu je potřeba ověřit, že všechny požadavky, a všechny neověřené požadavky jsou přesměrovány na účet Microsoft pro ověřování.
5. Klikněte na **Uložit**.

Nyní jste připraveni použít Microsoft Account pro ověřování ve vaší aplikaci.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Moje aplikace]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
