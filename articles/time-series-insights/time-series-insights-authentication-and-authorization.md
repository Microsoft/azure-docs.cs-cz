---
title: Jak ověřování a autorizace pomocí rozhraní API ve službě Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat ověřování a autorizace pro vlastní aplikaci, která volá rozhraní API Azure čas Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 5fb2802bfe9cc0a4d3297e6fa749e5b94008c616
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472632"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Ověřování a autorizace pro rozhraní API služby Azure čas Series Insights

Tento článek vysvětluje postup konfigurace ověřování a autorizaci použít ve vlastní aplikaci, která volá rozhraní API Azure čas Series Insights.

> [!TIP]
> Přečtěte si informace o [udělení přístupu k datům](./time-series-insights-data-access.md) do prostředí Time Series Insights v Azure Active Directory.

## <a name="service-principal"></a>Instanční objekt

Toto a následující části popisují, jak nakonfigurovat aplikaci pro přístup k rozhraní API čas Series Insights jménem aplikace. Aplikace můžete potom dotazů nebo publikovat referenčních dat v prostředí Time Series Insights pomocí přihlašovacích údajů aplikace místo uživatelských přihlašovacích údajů.

## <a name="best-practices"></a>Osvědčené postupy

Pokud máte aplikaci, která musí aplikace access Time Series Insights:

1. Nastavení aplikace Azure Active Directory.
1. [Přiřadit zásady přístupu k datům](./time-series-insights-data-access.md) v prostředí Time Series Insights.

Použití aplikací, nikoli vaše přihlašovací údaje uživatele je žádoucí od:

* Můžete přiřadit oprávnění pro aplikace identitu, která se liší od své vlastní oprávnění. Tato oprávnění jsou obvykle omezené na pouze co aplikace vyžaduje. Například můžete umožnit aplikaci jenom číst data v konkrétním prostředí Time Series Insights.
* Nemusíte měnit aplikace přihlašovacích údajů, pokud se změní vaše odpovědnosti.
* K automatizaci ověřování pomocí bezobslužného skriptu, můžete použít certifikát nebo klíč aplikace.

Následující části ukazují, jak provést tyto kroky na webu Azure portal. Tento článek se zaměřuje na jednoho tenanta aplikaci, ve kterém je aplikace určena pro spuštění v pouze jedna organizace. Aplikace pro jednoho tenanta budete obvykle používat pro-obchodní aplikace, které ve vaší organizaci.

## <a name="set-up-summary"></a>Souhrn nastavení

Nastavení toku se skládá ze tří kroků:

1. Vytvoření aplikace v Azure Active Directory.
1. Povolíte této aplikaci přístup k prostředí Time Series Insights.
1. Pomocí ID aplikace a klíč k získání tokenu z `https://api.timeseries.azure.com/`. Token, který potom slouží k volání rozhraní API čas Series Insights.

## <a name="detailed-setup"></a>Podrobné nastavení

1. Na webu Azure Portal, vyberte **Azure Active Directory** > **registrace aplikací** > **registrace nové aplikace**.

   [![Registrace nové aplikace v Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Pojmenujte aplikaci, vyberte typ, který má být **webovou aplikaci nebo API**, vyberte libovolný platný identifikátor URI pro **přihlašovací adresa URL**a klikněte na tlačítko **vytvořit**.

   [![Vytvoření aplikace v Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Vyberte svoji nově vytvořenou aplikaci a zkopírujte jeho ID aplikace na svém oblíbeném textovém editoru.

   [![Zkopírujte ID aplikace](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Vyberte **klíče**, zadejte název klíče, vyberte vypršení platnosti a klikněte na tlačítko **Uložit**.

   [![Vyberte klíče aplikace](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Zadejte název klíče a vypršení platnosti a klikněte na tlačítko Uložit](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Zkopírujte klíč na vašem oblíbeném textovém editoru.

   [![Zkopírujte klíč aplikace](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Pro prostředí Time Series Insights, vyberte **zásady přístupu k datům** a klikněte na tlačítko **přidat**.

   [![Přidat nové zásady přístupu dat do prostředí Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. V **vybrat uživatele** dialogové okno vložte název aplikace (z **kroku 2**) nebo ID aplikace (z **kroku 3**).

   [![Vyhledání aplikace v dialogovém okně vyberte uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Vyberte roli (**čtečky** pro dotazování na data, **Přispěvatel** pro dotazování na data a změně referenčních dat) a klikněte na tlačítko **OK**.

   [![Vyberte v dialogovém okně vybrat roli přispěvatele nebo čtenáře](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Uložte zásadu kliknutím **OK**.

1. Pomocí ID aplikace (z **kroku 3**) a klíč aplikace (z **kroku 5**) k získání tokenu jménem aplikace. Token, který je pak možné předat v `Authorization` záhlaví, když aplikace volá rozhraní API čas Series Insights.

    Pokud používáte C#, můžete použít následující kód k získání tokenu jménem aplikace. Úplnou ukázku najdete v tématu [dotazování dat pomocí jazyka C#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

Použití **ID aplikace** a **klíč** ve vaší aplikaci k ověřování pomocí Azure Time Series Insights.

## <a name="next-steps"></a>Další postup

- Ukázkový kód, který volá rozhraní API čas Series Insights, naleznete v tématu [dotazování dat pomocí jazyka C#](time-series-insights-query-data-csharp.md).

- Referenční informace rozhraní API najdete v tématu [referenční dokumentace rozhraní API pro dotazy](/rest/api/time-series-insights/ga-query-api).

- Zjistěte, jak [vytvoření instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md).
