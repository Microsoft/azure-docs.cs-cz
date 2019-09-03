---
title: Konfigurace ověřování Google – Azure App Service
description: Přečtěte si, jak nakonfigurovat ověřování Google pro vaši aplikaci App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232146"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Jak nakonfigurovat aplikaci App Service, aby používala přihlášení Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat Azure App Service pro použití Google jako poskytovatele ověřování.

Chcete-li dokončit postup v tomto tématu, musíte mít účet Google, který má ověřenou e-mailovou adresu. Nový účet Google si můžete vytvořit na stránce [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrace aplikace pomocí Google
1. Pokud chcete vytvořit ID klienta a tajný klíč klienta, postupujte podle dokumentace Google na webu [Google přihlášení pro aplikace na straně serveru](https://developers.google.com/identity/sign-in/web/server-side-flow) (není nutné provádět žádné změny kódu):
    - U **autorizovaných zdrojů JavaScriptu**použijte `https://<app-name>.azurewebsites.net` s názvem vaší aplikace v  *\<> App-Name*.
    - Pro **autorizovaný identifikátor URI přesměrování**použijte `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Jakmile se vytvoří ID klienta a tajné klíče klienta, zkopírujte jejich hodnoty.

    > [!IMPORTANT]
    > Tajný kód klienta je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte v klientské aplikaci.


## <a name="secrets"> </a>Přidání informací Google do aplikace
1. V [Azure Portal]přejděte do aplikace App Service. V nabídce vlevo vyberte **ověřování/autorizace**.
2. Pokud není povolená funkce ověřování/autorizace, zapněte přepínač na **zapnuto**.
3. Klikněte na **Google**. Vložte ID aplikace a hodnoty tajného klíče aplikace, které jste získali dříve, a volitelně povolte libovolné rozsahy, které vaše aplikace vyžaduje. Pak klikněte na **OK**.

   App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Další informace najdete v tématu [autorizace a zamítnutí uživatelů](app-service-authentication-how-to.md#authorize-or-deny-users).
4. Volitelné Pokud chcete omezit přístup k vašemu webu jenom na uživatele ověřené Google, nastavte **akci, která se má provést, když se žádost neověřuje** na **Google**. To vyžaduje, aby všechny požadavky byly ověřené a všechny neověřené požadavky se přesměrovaly na Google pro ověřování.

    > [!NOTE]
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

