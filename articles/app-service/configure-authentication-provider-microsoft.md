---
title: Konfigurace ověřování společnosti Microsoft
description: Naučte se nakonfigurovat ověřování účtu Microsoft jako zprostředkovatele identity pro App Service nebo Azure Functions aplikaci.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 9ec9c102680496407106a3bf9b7683890c7a63ee
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043242"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-microsoft-account-login"></a>Konfigurace App Service nebo Azure Functions aplikace pro použití přihlášení k účtu Microsoft

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto tématu se dozvíte, jak nakonfigurovat Azure App Service nebo Azure Functions, aby používaly AAD pro podporu přihlašovacích údajů pro osobní účet Microsoft.

> [!NOTE]
> Poskytovatel identity AAD používají osobní účty Microsoft i účty organizace. V tuto chvíli není možné nakonfigurovat tohoto zprostředkovatele identity tak, aby podporoval oba typy protokolů.

## <a name="register-your-app-with-microsoft-account"></a><a name="register-microsoft-account"> </a>Registrace aplikace pomocí účtu Microsoft

1. V Azure Portal přejít na [**Registrace aplikací**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) . V případě potřeby se přihlaste pomocí účet Microsoft.
1. Vyberte **Nová registrace** a pak zadejte název aplikace.
1. V části **podporované typy účtů** vyberte **účty v jakémkoli adresáři organizace (libovolný adresář Azure AD – víceklientské) a osobní účty Microsoft (např. Skype, Xbox)** .
1. V v **identifikátorech URI pro přesměrování** vyberte **Web** a potom zadejte `https://<app-domain-name>/.auth/login/aad/callback` . Nahraďte *\<app-domain-name>* názvem domény vaší aplikace.  Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Nezapomeňte použít schéma HTTPS v adrese URL.

1. Vyberte **Zaregistrovat** .
1. Zkopírujte **ID aplikace (klienta)** . Budete ho potřebovat později.
1. V levém podokně vyberte **certifikáty & tajných klíčů**  >  **nový tajný klíč klienta** . Zadejte popis, vyberte dobu platnosti a vyberte **Přidat** .
1. Zkopírujte hodnotu, která se zobrazí na stránce **certifikáty & tajných** kódů. Po opuštění stránky se znovu nezobrazí.

    > [!IMPORTANT]
    > Hodnota tajného klíče klienta (heslo) je důležité bezpečnostní pověření. Nesdílejte heslo s kýmkoli ani ho distribuujte v klientské aplikaci.

## <a name="add-microsoft-account-information-to-your-app-service-application"></a><a name="secrets"> </a>Přidání informací o účtu Microsoft do aplikace App Service

1. V [Azure Portal]přejdete do své aplikace.
1. Vyberte **Nastavení**  >  **ověřování/autorizace** a ujistěte se, že je **zapnuté** **ověřování App Service** .
1. V části **Zprostředkovatelé ověřování** vyberte **Azure Active Directory** . V části **režim správy** vyberte **Upřesnit** . Vložte do ID aplikace (klienta) a tajného klíče klienta, který jste získali dříve. Použijte **`https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0`** pro pole **Adresa URL vystavitele** .
1. Vyberte **OK** .

   App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.

1. Volitelné Pokud chcete omezit přístup k účet Microsoft uživatelům, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se **přihlásila pomocí Azure Active Directory** . Když nastavíte tuto funkci, aplikace vyžaduje ověření všech požadavků. Také přesměruje všechny neověřené požadavky na použití AAD pro ověřování. Všimněte si, že vzhledem k tomu, že jste nakonfigurovali **adresu URL vystavitele** na používání tenanta účtu Microsoft, úspěšně se ověří jenom osobní účty.

   > [!CAUTION]
   > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. Pro takové aplikace může být vhodnější použití **anonymních požadavků (žádná akce)** , aby aplikace ručně spouštěla ověřování. Další informace najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Vyberte **Uložit** .

Nyní jste připraveni použít účet Microsoft k ověřování ve vaší aplikaci.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
