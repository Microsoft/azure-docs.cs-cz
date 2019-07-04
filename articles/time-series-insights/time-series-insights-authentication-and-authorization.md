---
title: Ověřování a autorizaci s použitím rozhraní API v Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat ověřování a autorizace pro vlastní aplikaci, která volá rozhraní API Azure čas Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543905"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Ověřování a autorizace pro rozhraní API služby Azure čas Series Insights

Tento dokument popisuje, jak zaregistrovat aplikaci v Azure Active Directory pomocí nové okno Azure Active Directory. Aplikace v Azure Active Directory umožní uživatelům ověřit ale být autorizované na používání času řady přehled rozhraní API pro Azure přidružené k prostředí Time Series Insights.

## <a name="service-principal"></a>Instanční objekt služby

Následující části popisují, jak nakonfigurovat aplikaci pro přístup k rozhraní API čas Series Insights jménem aplikace. Aplikace může potom zadat dotaz nebo publikovat referenčních dat v prostředí Time Series Insights pomocí vlastních přihlašovacích údajů aplikaci Azure Active Directory.

## <a name="summary-and-best-practices"></a>Summary a osvědčené postupy

Tok registrace aplikace Azure Active Directory zahrnuje tři hlavní kroky.

1. [Registrace aplikace](#azure-active-directory-app-registration) ve službě Azure Active Directory.
1. Autorizovat aplikaci mít [přístup k datům prostředí Time Series Insights](#granting-data-access).
1. Použití **ID aplikace** a **tajný kód klienta** k získání tokenu z `https://api.timeseries.azure.com/` ve vašich [klientskou aplikaci](#client-app-initialization). Token, který potom slouží k volání rozhraní API čas Series Insights.

Za **kroku 3**, oddělení vaší aplikace a své přihlašovací údaje pro vám umožní:

* Přiřadíte oprávnění pro aplikace identitu, která se liší od své vlastní oprávnění. Tato oprávnění jsou obvykle omezené na pouze co aplikace vyžaduje. Například můžete umožnit aplikaci číst data jenom z konkrétního prostředí Time Series Insights.
* Izolace zabezpečení aplikace, od vytváření pověření ověřování s využitím **tajný kód klienta** nebo certifikát zabezpečení. Přihlašovací údaje vaší aplikace v důsledku toho nejsou závislé na pověření konkrétního uživatele. Pokud se změní na roli uživatele, aplikace nemusí nutně vyžadovat nové přihlašovací údaje nebo další konfiguraci. Pokud uživatel změní heslo, nevyžaduje, aby veškerý přístup k aplikaci nové přihlašovacím údajům nebo klíčům.
* Spusťte pomocí skriptu bezobslužné instalace **tajný kód klienta** nebo zabezpečení certifikátu místo přihlašovacích údajů konkrétního uživatele (museli být k dispozici).
* Použijte certifikát zabezpečení, ne heslo k zabezpečení přístupu k rozhraní API Azure čas Series Insights.

> [!IMPORTANT]
> Postupujte podle principu **oddělení se týká** (popsaných výše v tomto scénáři) při konfiguraci zásad zabezpečení Azure Time Series Insights.

> [!NOTE]
> * Tento článek se zaměřuje na jednoho tenanta aplikaci, ve kterém je aplikace určena pro spuštění v pouze jedna organizace.
> * Aplikace pro jednoho tenanta budete obvykle používat pro-obchodní aplikace, které ve vaší organizaci.

## <a name="detailed-setup"></a>Podrobné nastavení

### <a name="azure-active-directory-app-registration"></a>Registrace aplikace Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Udělení přístupu k datům

1. Pro prostředí Time Series Insights, vyberte **zásady přístupu k datům** a vyberte **přidat**.

   [![Přidat nové zásady přístupu dat do prostředí Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. V **vybrat uživatele** dialogové okno vložte buď **název_aplikace** nebo **ID aplikace** z části registrace aplikace Azure Active Directory.

   [![Vyhledání aplikace v dialogovém okně vyberte uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Vyberte roli. Vyberte **čtečky** provádět dotazy na data nebo **Přispěvatel** změn referenčních dat a dotazování na data. Vyberte **OK**.

   [![V dialogovém okně vybrat roli uživatele vybrat přispěvatele nebo čtenáře](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Uložte zásadu tak, že vyberete **OK**.

   > [!TIP]
   > Přečtěte si informace o [udělení přístupu k datům](./time-series-insights-data-access.md) do prostředí Time Series Insights v Azure Active Directory.

### <a name="client-app-initialization"></a>Inicializace klienta aplikace

1. Použití **ID aplikace** a **tajný kód klienta** (klíč aplikace) z části registrace aplikace Azure Active Directory k získání tokenu jménem aplikace.

    V C#, následující kód můžete získat token jménem aplikace. Úplnou ukázku najdete v tématu [dotazování dat pomocí jazyka C#](time-series-insights-query-data-csharp.md).

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

1. Token, který je pak možné předat v `Authorization` záhlaví, když aplikace volá rozhraní API čas Series Insights.

## <a name="next-steps"></a>Další postup

- Ukázkový kód, který volá rozhraní API GA čas Series Insights, naleznete v tématu [dotazování dat pomocí C# ](./time-series-insights-query-data-csharp.md).

- Ukázky kódu rozhraní API ve verzi Preview čas Series Insights najdete v tématu [data dotazu ve verzi Preview s využitím C# ](./time-series-insights-update-query-data-csharp.md).

- Referenční informace rozhraní API najdete v tématu [referenční dokumentace rozhraní API pro dotazy](/rest/api/time-series-insights/ga-query-api).

- Zjistěte, jak [vytvoření instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md).