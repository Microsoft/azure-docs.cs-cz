---
title: Ověřování a autorizace pomocí rozhraní API v Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat ověřování a autorizaci pro vlastní aplikaci, která volá rozhraní Azure Time Series Insights API.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 4fd68f770cbe48b15646ec41c0bf94be5e760a50
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990181"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Ověřování a autorizace pro rozhraní Azure Time Series Insights API

Tento dokument popisuje, jak zaregistrovat aplikaci v Azure Active Directory pomocí nového okna Azure Active Directory. Aplikace zaregistrované v Azure Active Directory umožňují uživatelům ověřování a autorizaci používat rozhraní API služby Azure Time Series Insights přidružené k prostředí Time Series Insights.

## <a name="service-principal"></a>Instanční objekt

Následující části popisují, jak nakonfigurovat aplikaci pro přístup k rozhraní Time Series Insights API jménem aplikace. Aplikace pak může dotazovat nebo publikovat referenční data v prostředí Time Series Insights pomocí vlastních přihlašovacích údajů aplikace prostřednictvím Azure Active Directory.

## <a name="summary-and-best-practices"></a>Souhrn a osvědčené postupy

Registrační tok aplikace Azure Active Directory zahrnuje tři hlavní kroky.

1. [Registrace aplikace](#azure-active-directory-app-registration) v Azure Active Directory.
1. Autorizujte aplikaci tak, aby měla [přístup k datům Time Series Insights prostředí](#granting-data-access).
1. Použijte **ID aplikace** a **tajný klíč klienta** k získání tokenu z `https://api.timeseries.azure.com/` v [klientské aplikaci](#client-app-initialization). Token se pak dá použít k volání rozhraní Time Series Insights API.

V rámci **kroku 3**oddělení aplikace a přihlašovací údaje uživatele vám umožní:

* Přiřaďte oprávnění identitě aplikace, která se liší od vašich vlastních oprávnění. Tato oprávnění se většinou omezují jenom na to, co aplikace vyžaduje. Aplikaci můžete například dovolit číst data pouze z konkrétního Time Series Insightsho prostředí.
* Izolujte zabezpečení aplikace od přihlašovacích údajů pro ověření uživatele pomocí **tajného klíče klienta** nebo certifikátu zabezpečení. V důsledku toho přihlašovací údaje aplikace nejsou závislé na přihlašovacích údajích konkrétního uživatele. Pokud se změní role uživatele, aplikace nemusí nutně vyžadovat nové přihlašovací údaje ani další konfiguraci. Pokud uživatel změní heslo, veškerý přístup k aplikaci nevyžaduje nové přihlašovací údaje ani klíče.
* Spusťte bezobslužný skript pomocí **tajného klíče klienta** nebo certifikátu zabezpečení, nikoli přihlašovací údaje konkrétního uživatele (vyžaduje, aby byly přítomny).
* Pro zabezpečení přístupu k rozhraní Azure Time Series Insights API použijte certifikát zabezpečení, nikoli heslo.

> [!IMPORTANT]
> Při konfiguraci zásad zabezpečení Azure Time Series Insights postupujte podle principu **oddělení** potíží (popsané v tomto scénáři výše).

> [!NOTE]
> * Článek se zaměřuje na aplikaci s jedním tenantů, kde má aplikace běžet jenom v jedné organizaci.
> * Pro obchodní aplikace, které běží ve vaší organizaci, obvykle používáte aplikace pro jednoho tenanta.

## <a name="detailed-setup"></a>Podrobné nastavení

### <a name="azure-active-directory-app-registration"></a>Registrace aplikace Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Udělení přístupu k datům

1. V prostředí Time Series Insights vyberte **zásady přístupu k datům** a vyberte **Přidat**.

   [![přidat nové zásady přístupu k datům do prostředí Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. V dialogovém okně **Vybrat uživatele** vložte **název aplikace** nebo **ID aplikace** z části registrace aplikace Azure Active Directory.

   [![najít aplikaci v dialogovém okně Vybrat uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Vyberte roli. Výběr **čtecího zařízení** pro dotazování dat nebo **přispěvatele** k dotazování na data a změně referenčních dat. Vyberte **OK**.

   [![výběru čtecího modulu nebo přispěvatele v dialogovém okně Vybrat roli uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Uložte zásadu výběrem **OK**.

   > [!TIP]
   > Přečtěte si informace o [udělení přístupu k datům](./time-series-insights-data-access.md) do prostředí Time Series Insights v Azure Active Directory.

### <a name="client-app-initialization"></a>Inicializace klientské aplikace

1. Použijte **ID aplikace** a **tajný klíč klienta** (aplikační klíč) z části registrace aplikace Azure Active Directory k získání tokenu jménem aplikace.

    V C#nástroji může následující kód získat token jménem aplikace. Úplnou ukázku najdete v tématu [dotazování na data C#pomocí ](time-series-insights-query-data-csharp.md).

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

1. Token se pak může předat v hlavičce `Authorization`, když aplikace volá rozhraní API Time Series Insights.

## <a name="common-headers-and-parameters"></a>Společné hlavičky a parametry

Tato část popisuje společné hlavičky a parametry požadavků protokolu HTTP, které slouží k vytváření dotazů proti Time Series Insights rozhraní API GA a Preview. Požadavky na konkrétní rozhraní API jsou podrobněji popsány v [referenční dokumentaci Time Series Insights REST API](https://docs.microsoft.com/rest/api/time-series-insights/).

### <a name="authentication"></a>Ověřování

Aby bylo možné provádět ověřené dotazy proti [Time Series Insights rozhraní REST API](https://docs.microsoft.com/rest/api/time-series-insights/), musí se v [autorizační hlavičce](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) předávat platný token OAuth 2,0 s použitím klienta REST podle vašeho výběru (post, JavaScript, C#). 

> [!IMPORTANT]
> Token se musí vystavit přesně pro prostředek `https://api.timeseries.azure.com/` (označuje se také jako "cílová skupina" tokenu).
> * Vaše [dodatečná](https://www.getpostman.com/) **AuthURLa** je proto vyhovující: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`

> [!TIP]
> Podívejte se na [ukázkovou vizualizaci sady SDK](https://tsiclientsample.azurewebsites.net/) hostovaného Azure Time Series Insights a zjistěte, jak pomocí rozhraní API [klienta pro JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) společně s grafy a grafy ověřit ověřování pomocí rozhraní API pro Time Series Insights prostřednictvím kódu programu.

### <a name="http-headers"></a>Hlavičky protokolu HTTP

Požadované hlavičky žádosti:

- `Authorization` pro ověřování a autorizaci musí být v autorizační hlavičce předán platný nosný token OAuth 2,0. Token se musí vystavit přesně pro prostředek `https://api.timeseries.azure.com/` (označuje se také jako "cílová skupina" tokenu).

Nepovinné hlavičky žádosti:

- je podporována pouze `Content-type` `application/json`.
- `x-ms-client-request-id` – ID žádosti klienta. Služba zaznamenává tuto hodnotu. Umožňuje službě sledovat operace napříč službami.
- `x-ms-client-session-id` – ID klientské relace. Služba zaznamenává tuto hodnotu. Umožňuje službě trasovat skupinu souvisejících operací napříč službami.
- `x-ms-client-application-name` – název aplikace, která vygenerovala tento požadavek. Služba zaznamenává tuto hodnotu.

Hlavičky odpovědi:

- je podporována pouze `Content-type` `application/json`.
- `x-ms-request-id` – ID žádosti generované serverem Dá se použít ke kontaktování žádosti Microsoftu o vyšetření žádosti.

### <a name="http-parameters"></a>Parametry HTTP

Požadované parametry řetězce dotazu adresy URL:

- `api-version=2016-12-12`
- `api-version=2018-11-01-preview`

Volitelné parametry řetězce dotazu adresy URL:

- `timeout=<timeout>` – časový limit pro provedení žádosti na straně serveru. Dá se použít jenom pro [události Get prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) a rozhraní API pro [agregace prostředí](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) . Hodnota časového limitu by měla být ve formátu doby trvání ISO 8601, například `"PT20S"` a měla by být v rozsahu `1-30 s`. Výchozí hodnota je `30 s`.

## <a name="next-steps"></a>Další kroky

- Vzorový kód, který volá rozhraní API GA Time Series Insights, najdete v tématu [dotazování dat pomocí C# ](./time-series-insights-query-data-csharp.md).

- Náhled ukázek kódu rozhraní API Time Series Insights najdete v tématu [dotazování dat C#ve verzi Preview pomocí ](./time-series-insights-update-query-data-csharp.md).

- Referenční informace k rozhraní API najdete v tématu [Reference k rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Naučte se [vytvořit instanční objekt](../active-directory/develop/howto-create-service-principal-portal.md).