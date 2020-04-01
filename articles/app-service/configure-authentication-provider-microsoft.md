---
title: Konfigurace ověřování společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat ověřování účtu Microsoft jako poskytovatele identity pro vaši aplikaci App Service nebo Azure Functions.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: ea745aa00f8990a2d1232a19780fdc70e2f78996
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437967"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Konfigurace aplikace App Service nebo aplikace Azure Functions tak, aby používala přihlášení k účtu Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Toto téma ukazuje, jak nakonfigurovat Azure App Service nebo Azure Functions pro použití AAD pro podporu osobních přihlášení k účtu Microsoft.

> [!NOTE]
> Osobní účty Microsoft i účty organizace používají zprostředkovatele identity AAD. V současné době není možné nakonfigurovat tohoto zprostředkovatele identity tak, aby podporoval oba typy přihlášení.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registrace aplikace pomocí účtu Microsoft

1. Přejděte na [**registrace aplikací**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) na webu Azure Portal. V případě potřeby se přihlaste pomocí svého účtu Microsoft.
1. Vyberte **Nová registrace**a zadejte název aplikace.
1. V části **Podporované typy účtů**vyberte Účty v **libovolném organizačním adresáři (libovolný adresář Azure AD – víceklientů) a osobní účty Microsoft (například Skype, Xbox).**
1. V **příkazu Přesměrování identifikátorů URI** `https://<app-domain-name>/.auth/login/aad/callback`vyberte možnost **Web**a zadejte . Nahraďte * \<název domény aplikace>* názvem domény aplikace.  Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Nezapomeňte použít schéma HTTPS v adrese URL.

1. Vyberte **Zaregistrovat**.
1. Zkopírujte **ID aplikace (klienta).** Budete ho potřebovat později.
1. V levém podokně vyberte **možnost Certifikáty & tajných kódů** > **Nový tajný klíč klienta**. Zadejte popis, vyberte dobu platnosti a vyberte **Přidat**.
1. Zkopírujte hodnotu, která se zobrazí na stránce **Tajné klíče certifikáty certifikáty &.** Po opuštění stránky se znovu nezobrazí.

    > [!IMPORTANT]
    > Hodnota tajného klíče klienta (heslo) je důležité pověření zabezpečení. Nesdílejte heslo s nikým ani jej nedistribuujte v rámci klientské aplikace.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Přidání informací o účtu Microsoft do aplikace App Service

1. Přejděte do vaší aplikace na [webu Azure Portal].
1. Vyberte **Nastavení** > **ověřování / autorizace**a ujistěte se, že je **zapnuté ověřování služby App Service** . **On**
1. V části **Zprostředkovatelé ověřování**vyberte **službu Azure Active Directory**. V **režimu správy**vyberte **Upřesnit** . Vložte do ID aplikace (klienta) a tajný klíč klienta, který jste získali dříve. Používá **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** se pro pole **Adresa URL vystavitče.**
1. Vyberte **OK**.

   Služba App Service poskytuje ověřování, ale neomezuje oprávněný přístup k obsahu webu a souborům API. V kódu aplikace musíte autorizovat uživatele.

1. (Nepovinné) Chcete-li omezit přístup k uživatelům účtu Microsoft, nastavte **akci, která má být v případě, že požadavek není ověřen, přihlaste** se **pomocí služby Azure Active Directory**. Když nastavíte tuto funkci, vaše aplikace vyžaduje, aby byly ověřeny všechny požadavky. Také přesměruje všechny neověřené požadavky na použití aad pro ověřování. Všimněte si, že vzhledem k tomu, že jste nakonfigurovali **adresu URL vystavitče** tak, aby používala klienta účtu Microsoft, bude úspěšně ověřen pouze osobní účet.

   > [!CAUTION]
   > Omezení přístupu tímto způsobem platí pro všechna volání do vaší aplikace, což nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, jako v mnoha jednostránkových aplikacích. Pro takové aplikace **povolit anonymní požadavky (žádná akce)** může být upřednostňována tak, aby aplikace ručně spustí ověřování sám. Další informace naleznete v [tématu Ověřování toku](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Uložit**.

Nyní jste připraveni používat účet Microsoft pro ověřování ve vaší aplikaci.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[portál Azure]: https://portal.azure.com/
