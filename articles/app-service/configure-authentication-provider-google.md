---
title: Konfigurace ověřování Google - službě Azure App Service
description: Zjistěte, jak nakonfigurovat ověřování Google pro aplikaci App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 50905b86924e0f564eaf4867c2906ad8740ddbaf
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410952"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Konfigurace aplikace App Service pro použití přihlášení k Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat služby Azure App Service, který se použije jako zprostředkovatel ověřování Google.

K dokončení postupu v tomto tématu, musíte mít účet Google s ověřenou e-mailovou adresou. Nový účet Google si můžete vytvořit na stránce [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrace vaší aplikace pomocí Googlu
1. Přihlaste se k [Azure Portal]a přejděte k vaší aplikaci. Kopii vaší **URL**, které použijete později ke konfiguraci vaší aplikace Google.
2. Přejděte na [rozhraní Google API](https://go.microsoft.com/fwlink/p/?LinkId=268303) klikněte na webu, přihlaste se pomocí svých přihlašovacích údajů účtu Google **vytvořit projekt**, zadejte **název projektu**, klikněte na  **Vytvoření**.
3. Jakmile se vytvoří projekt, vyberte ji. Na řídicím panelu projektu klikněte na tlačítko **přejít na přehled rozhraní API**.
4. Vyberte **povolit rozhraní API a služby**. Vyhledejte **rozhraní API Google +** a vyberte ji. Pak klikněte na tlačítko **povolit**.
5. V levém navigačním panelu **pověření** > **obrazovku se souhlasem OAuth**a pak vyberte vaši **e-mailová adresa**, zadejte **název produktu**a klikněte na tlačítko **Uložit**.
6. V **pověření** klikněte na tlačítko **Vytvořte přihlašovací údaje** > **ID klienta OAuth**.
7. Na obrazovce "Vytvořit ID klienta" vyberte **webovou aplikaci**.
8. Vložte služby App Service **URL** jste dříve zkopírovali do **oprávnění zdroje jazyka JavaScript**, vložte váš přesměrování URI do **oprávnění identifikátor URI pro přesměrování**. Přesměrování identifikátoru URI je adresa URL vaší aplikace s cestu, */.auth/login/google/callback*. Například, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Ujistěte se, že budou používat schéma HTTPS. Poté klikněte na **Vytvořit**.
9. Poznamenejte si hodnoty ID klienta a tajný kód klienta na další obrazovce.

    > [!IMPORTANT]
    > Tajný kód klienta je důležitým bezpečnostním pověřením. S kýmkoli sdílet tento tajný kód nebo distribuovat v rámci klientské aplikace.


## <a name="secrets"> </a>Přidat do vaší aplikace Google informace
1. Zpátky [Azure Portal], přejděte k vaší aplikaci. Klikněte na tlačítko **nastavení**a potom **ověřování / autorizace**.
2. Pokud ověřování / autorizace funkce není povolená, přepněte přepínač **na**.
3. Klikněte na tlačítko **Google**. Vložením hodnoty ID aplikace a tajný kód aplikace, které jste získali dříve a volitelně povolit všechny obory, které vaše aplikace vyžaduje. Pak klikněte na **OK**.
   
   ![][1]
   
   Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. V kódu vaší aplikace musí uživateli uživatele autorizovat.
4. (Volitelné) Chcete-li omezit přístup k webu pouze uživatele ověřené Google, nastavte **akce má být provedena, když požadavek nebude ověřený** k **Google**. K tomu je potřeba ověřit, že všechny požadavky, a všechny neověřené požadavky jsou přesměrovány do Googlu pro ověřování.
5. Klikněte na **Uložit**.

Nyní jste připraveni používat Google pro ověřování ve vaší aplikaci.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

