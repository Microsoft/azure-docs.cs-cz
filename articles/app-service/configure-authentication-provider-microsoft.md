---
title: Konfigurace ověřování účtu Microsoft – Azure App Service
description: Přečtěte si, jak nakonfigurovat ověřování účtu Microsoft pro vaši aplikaci App Service.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/08/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 70af534e6bcd0039dbc602a5ebc3fc35fb145e79
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176935"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>Konfigurace aplikace App Service pro používání přihlášení k účtu Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat Azure App Service pro použití účtu Microsoft jako poskytovatele ověřování. 

## <a name="register-microsoft-account"> </a>Registrace aplikace pomocí účtu Microsoft

1. V Azure Portal přejít na [**Registrace aplikací**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) . V případě potřeby se přihlaste pomocí účet Microsoft.
1. Vyberte **Nová registrace**a pak zadejte název aplikace.
1. V rozevíracích **identifikátorech URI pro přesměrování**vyberte **Web**a potom zadejte `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`. Nahraďte *\<app-Domain-name >* názvem domény vaší aplikace.  Například, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Nezapomeňte použít schéma HTTPS v adrese URL.

1. Vyberte **Zaregistrovat**.
1. Zkopírujte **ID aplikace (klienta)** . Budete ho potřebovat později.
1. V levém podokně vyberte **certifikáty & tajných**kódů  > **nový tajný klíč klienta**. Zadejte popis, vyberte dobu platnosti a vyberte **Přidat**.
1. Zkopírujte hodnotu, která se zobrazí na stránce **certifikáty & tajných** kódů. Po opuštění stránky se znovu nezobrazí.

    > [!IMPORTANT]
    > Heslo je důležité bezpečnostní pověření. Nesdílejte heslo s kýmkoli ani ho distribuujte v klientské aplikaci.

## <a name="secrets"> </a>Přidání informací o účtu Microsoft do aplikace App Service

1. V [Azure Portal]přejdete do své aplikace.
1. Vyberte **nastavení** > **ověřování/autorizace**a ujistěte se, že je **zapnuté** **ověřování App Service** .
1. V části **Zprostředkovatelé ověřování**vyberte **účet Microsoft**. Vložte do ID aplikace (klienta) a tajného klíče klienta, který jste získali dříve. Povolte všechny rozsahy, které vaše aplikace vyžaduje.
1. Vyberte **OK**.

   App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.

1. Volitelné Pokud chcete omezit přístup k účet Microsoft uživatelům, nastavte **akci, která se má provést, když se žádost neověřuje** pro **přihlášení pomocí účtu Microsoft**. Když nastavíte tuto funkci, aplikace vyžaduje ověření všech požadavků. Také přesměruje všechny neověřené požadavky na účet Microsoft pro ověřování.

   > [!CAUTION]
   > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. Pro takové aplikace může být vhodnější použití **anonymních požadavků (žádná akce)** , aby aplikace ručně spouštěla ověřování. Další informace najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Save** (Uložit).

Nyní jste připraveni použít účet Microsoft k ověřování ve vaší aplikaci.

## <a name="related-content"></a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
