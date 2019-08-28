---
title: Konfigurace ověřování Google – Azure App Service
description: Naučte se konfigurovat ověřování Google pro vaši aplikaci App Services.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 4e28f4e330fa24476b717334dfc6d3265640c62a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088215"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Jak nakonfigurovat aplikaci App Service, aby používala přihlášení Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat Azure App Service pro použití Google jako poskytovatele ověřování.

Chcete-li dokončit postup v tomto tématu, musíte mít účet Google, který má ověřenou e-mailovou adresu. Nový účet Google si můžete vytvořit na stránce [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrace aplikace pomocí Google
1. Přihlaste se k [Azure Portal]a přejděte do aplikace. Zkopírujte **adresu URL**, kterou použijete později ke konfiguraci aplikace Google.
2. Přejděte na web [Google API](https://go.microsoft.com/fwlink/p/?LinkId=268303) , přihlaste se pomocí přihlašovacích údajů k účtu Google, klikněte na **vytvořit projekt**, zadejte **název projektu**a pak klikněte na **vytvořit**.
3. Po vytvoření projektu ho vyberte. V řídicím panelu Projekt klikněte na možnost **Přejít na rozhraní API přehled**.
4. Vyberte možnost **Povolit rozhraní API a služby**. Vyhledejte **Google + API**a vyberte ji. Pak klikněte na **Povolit**.
5. V levém navigačním panelu klikněte na**obrazovku pro vyjádření souhlasu OAuth**s **přihlašovacími údaji** > a pak vyberte svou **e-mailovou adresu**, zadejte **název produktu**a klikněte na **Uložit**.
6. Na kartě **přihlašovací údaje** klikněte na **vytvořit přihlašovací údaje** > **ID klienta OAuth**.
7. Na obrazovce vytvořit ID klienta vyberte možnost **Webová aplikace**.
8. Vložte **adresu URL** App Service, kterou jste zkopírovali dříve do **autorizovaných zdrojů JavaScriptu**, a pak vložte identifikátor URI přesměrování do **autorizovaného identifikátoru URI přesměrování**. Identifikátor URI přesměrování je adresa URL vaší aplikace připojené s cestou, */.auth/Login/Google/callback*. Například, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Ujistěte se, že používáte schéma HTTPS. Poté klikněte na **Vytvořit**.
9. Na další obrazovce si poznamenejte hodnoty ID klienta a tajný klíč klienta.

    > [!IMPORTANT]
    > Tajný kód klienta je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte v klientské aplikaci.


## <a name="secrets"> </a>Přidání informací Google do aplikace
1. Zpátky v [Azure Portal]přejděte do aplikace. Klikněte na **Nastavení**a pak na **ověřování nebo autorizaci**.
2. Pokud není povolená funkce ověřování/autorizace, zapněte přepínač na **zapnuto**.
3. Klikněte na **Google**. Vložte do ID aplikace a hodnoty tajného klíče aplikace, které jste získali dříve, a volitelně povolte libovolné rozsahy, které vaše aplikace vyžaduje. Pak klikněte na **OK**.
   
   ![][1]
   
   Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.
4. Volitelné Pokud chcete omezit přístup k vašemu webu jenom na uživatele ověřené Google, nastavte **akci, která se má provést, když se žádost neověřuje** na **Google**. To vyžaduje, aby všechny požadavky byly ověřené a všechny neověřené požadavky se přesměrovaly na Google pro ověřování.

> [!CAUTION]
> Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. U takových aplikací může být upřednostňována možnost **povolení anonymních požadavků (bez akce)** , pokud se aplikace ručně spouští samotné přihlášení, jak je popsáno [zde](overview-authentication-authorization.md#authentication-flow).

5. Klikněte na **Uložit**.

Teď jste připravení použít Google pro ověřování ve vaší aplikaci.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

