---
title: Postup konfigurace ověřování Google pro vaši aplikaci aplikační služby
description: Informace o konfiguraci ověřování Google pro vaši aplikaci aplikační služby.
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
ms.openlocfilehash: 1a174913446c0a1d5e3e3b01123db8b40bfd172c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Postup konfigurace aplikace služby App Service pomocí Google přihlášení
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Toto téma ukazuje, jak nakonfigurovat služby Azure App Service, pokud chcete použít jako zprostředkovatel ověřování Google.

Dokončete postup v tomto tématu, musí mít účet Google s ověřenou e-mailovou adresu. Nový účet Google si můžete vytvořit na stránce [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrace aplikace pomocí služby Google
1. Přihlaste se na [portál Azure]a přejděte k vaší aplikaci. Kopie vašeho **URL**, které můžete použít ke konfiguraci vaší aplikace Google později.
2. Přejděte na [rozhraní Google API](http://go.microsoft.com/fwlink/p/?LinkId=268303) klikněte na web, přihlaste se pomocí svých přihlašovacích údajů účtu Google **vytvoření projektu**, poskytovat **název projektu**, pak klikněte na tlačítko  **Vytvoření**.
3. Po vytvoření projektu, vyberte ho. Na řídicím panelu projektu klikněte na tlačítko **přejít na přehled rozhraní API**.
4. Vyberte **povolit rozhraní API a služby**. Vyhledejte **rozhraní API Google +** a vyberte ho. Pak klikněte na tlačítko **povolit**.
6. V levém navigačním **pověření** > **obrazovky souhlas OAuth**, pak vyberte vaše **e-mailová adresa**, zadejte **název produktu**a klikněte na tlačítko **Uložit**.
7. V **pověření** , klikněte na **vytvořit přihlašovací údaje** > **ID klienta OAuth**. Klikněte na tlačítko **konfigurovat souhlasu obrazovky**, poskytovat **název produktu**. Pak klikněte na tlačítko **uložit**
8. Na obrazovce "Vytvořit ID klienta" vyberte **webové aplikace**.
9. Vložte App Service **URL** jste zkopírovali dříve do **oprávnění zdroje JavaScript**, vložte vaší přesměrování URI do **oprávnění identifikátor URI pro přesměrování**. Přesměrování identifikátor URI je adresa URL aplikace připojí s cestou, */.auth/login/google/callback*. Například, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Ujistěte se, že používáte schéma HTTPS. Poté klikněte na **Vytvořit**.
10. Poznamenejte si hodnoty ID klienta a tajný klíč klienta na další obrazovce.

    > [!IMPORTANT]
    > Tajný klíč klienta je důležitým bezpečnostním pověřením. S kýmkoli sdílet tento tajný klíč nebo distribuovat v rámci klientské aplikace.


## <a name="secrets"> </a>Přidání Google informace do aplikace
1. Zpět v [portál Azure], přejděte k vaší aplikaci. Klikněte na tlačítko **nastavení**a potom **ověřování / autorizace**.
2. Pokud ověřování / autorizace funkce není povolena, zapněte přepínač k **na**.
3. Klikněte na tlačítko **Google**. Vložte hodnoty ID aplikace a tajný klíč aplikace, které jste získali dříve a volitelně povolte všechny obory, které vaše aplikace vyžaduje. Pak klikněte na **OK**.
   
   ![][1]
   
   Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. Je nutné autorizovat uživatele v kódu aplikace.
4. (Volitelné) Chcete-li omezit přístup k webu jenom na uživatele ověřeného službou Google, nastavte **akci provést, když požadavek nebude ověřený** k **Google**. To vyžaduje, že všechny žádosti o ověření a všechny neověřené požadavky se přesměrují do Google pro ověřování.
5. Klikněte na **Uložit**.

Nyní jste připraveni pro ověřování ve vaší aplikaci pomocí služby Google.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[portál Azure]: https://portal.azure.com/

