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
ms.openlocfilehash: 0832c1e5f10cdb8e1d7a2edbb88162230ab13401
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233082"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Jak nakonfigurovat aplikaci App Service, aby používala přihlášení k účtu Microsoft
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat Azure App Service pro použití účtu Microsoft jako poskytovatele ověřování. 

## <a name="register-microsoft-account"> </a>Registrace aplikace pomocí účtu Microsoft
1. Přihlaste se k [Azure Portal]a přejděte do aplikace. 

<!-- Copy your **URL**, which you will use later to configure your app with Microsoft Account. -->
1. Pokud je to požadováno, přejděte na [**Registrace aplikací**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)a přihlaste se pomocí účet Microsoft.

1. Klikněte na **Nová registrace**a pak zadejte název aplikace.

1. V v **identifikátorech URI pro přesměrování**vyberte **Web**a `https://<app-domain-name>/.auth/login/microsoftaccount/callback supply the endpoint for your application`potom zadejte. *\<> App-Domain-Name* nahraďte názvem domény vaší aplikace.  Například, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. 

   > [!NOTE]
   > Použijte schéma HTTPS v adrese URL.

1. Vyberte **Registrovat**. 

1. Zkopírujte **ID aplikace (klienta)** . Budete ho potřebovat později. 
   
7. V levém navigačním panelu registrace nové aplikace vyberte **certifikáty & tajných klíčů** > **nový tajný klíč klienta**. Zadejte popis, vyberte dobu platnosti a vyberte **Přidat**.

1. Zkopírujte hodnotu, která se zobrazí na stránce **certifikáty & tajných** kódů. Po opuštění stránky se znovu nezobrazí.

    > [!IMPORTANT]
    > Heslo je důležité bezpečnostní pověření. Nesdílejte heslo s kýmkoli ani ho distribuujte v klientské aplikaci.

## <a name="secrets"> </a>Přidání informací o účtu Microsoft do aplikace App Service
1. V [Azure Portal]přejděte do aplikace. V levém navigačním panelu klikněte na **ověřování/autorizace**.

2. Pokud není povolená funkce ověřování/autorizace, vyberte **zapnuto**.

3. V části **Zprostředkovatelé ověřování**vyberte **účet Microsoft**. Vložte do ID aplikace (klienta) a tajného klíče klienta, který jste získali dříve, a volitelně povolte libovolné rozsahy, které vaše aplikace vyžaduje. Pak klikněte na **OK**.

    Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.

4. Volitelné Pokud chcete omezit přístup k účet Microsoft uživatelům, nastavte **akci, která se má provést, když se žádost neověřuje** pro **přihlášení pomocí účtu Microsoft**. To vyžaduje, aby všechny požadavky byly ověřené a všechny neověřené požadavky byly přesměrovány na účet Microsoft pro ověřování.

> [!NOTE]
> Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. U takových aplikací může být upřednostňována možnost **povolení anonymních požadavků (bez akce)** , pokud se aplikace ručně spouští samotné přihlášení, jak je popsáno [zde](overview-authentication-authorization.md#authentication-flow).

5. Klikněte na **Uložit**.

Nyní jste připraveni použít účet Microsoft k ověřování ve vaší aplikaci.

## <a name="related-content"> </a>Související obsah
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
