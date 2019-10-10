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
ms.openlocfilehash: 917fa87a0cd0f7b0615a5139a7c15311f866739a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176970"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Konfigurace aplikace App Service pro používání přihlášení Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat Azure App Service pro použití Google jako poskytovatele ověřování.

Chcete-li dokončit postup v tomto tématu, musíte mít účet Google, který má ověřenou e-mailovou adresu. Nový účet Google si můžete vytvořit na stránce [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrace aplikace pomocí Google

1. Pokud chcete vytvořit ID klienta a tajný klíč klienta, postupujte podle dokumentace Google na webu [Google Signing pro aplikace na straně serveru](https://developers.google.com/identity/sign-in/web/server-side-flow) . Není nutné dělat žádné změny kódu. Stačí použít následující informace:
    - U **autorizovaných zdrojů JavaScriptu**použijte `https://<app-name>.azurewebsites.net` s názvem vaší aplikace v *\<app-Name >* .
    - Pro **autorizovaný identifikátor URI přesměrování**použijte `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Zkopírujte ID aplikace a hodnoty tajného klíče aplikace.

    > [!IMPORTANT]
    > Tajný klíč aplikace je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte v klientské aplikaci.

## <a name="secrets"> </a>Přidání informací Google do aplikace

1. V [Azure Portal]přejdete do aplikace App Service.
1. Vyberte **nastavení** > **ověřování/autorizace**a ujistěte se, že je **zapnuté** **ověřování App Service** .
1. Vyberte **Google**a potom vložte ID aplikace a hodnoty tajného klíče aplikace, které jste získali dříve. Povolte všechny rozsahy, které vaše aplikace vyžaduje.
1. Vyberte **OK**.

   App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Další informace najdete v tématu [autorizace a zamítnutí uživatelů](app-service-authentication-how-to.md#authorize-or-deny-users).

1. Volitelné Pokud chcete omezit přístup k webu jenom na uživatele ověřené Google, nastavte **akci, která se má provést, když se žádost neověřuje** na **Google**. Když nastavíte tuto funkci, aplikace vyžaduje, aby byly všechny požadavky ověřeny. Také přesměruje všechny neověřené žádosti na Google pro ověřování.

    > [!CAUTION]
    > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. Pro takové aplikace může být vhodnější použití **anonymních požadavků (žádná akce)** , aby aplikace ručně spouštěla ověřování. Další informace najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Save** (Uložit).

Teď jste připravení použít Google pro ověřování ve vaší aplikaci.

## <a name="related-content"></a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

