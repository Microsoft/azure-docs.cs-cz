---
title: Jak ověřování a autorizace pomocí rozhraní API ve službě Azure Time Series Insights
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
ms.date: 11/27/2017
ms.openlocfilehash: b8d298938d9b3ed0089eb52aed47c7086983a9ce
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47422928"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Ověřování a autorizace pro rozhraní API služby Azure čas Series Insights

Tento článek vysvětluje postup konfigurace ověřování a autorizaci použít ve vlastní aplikaci, která volá rozhraní API Azure čas Series Insights.

## <a name="service-principal"></a>Instanční objekt

Tato část vysvětluje postup konfigurace aplikace pro přístup k rozhraní API čas Series Insights jménem aplikace. Aplikace můžete potom dotazování na data nebo publikovat referenčních dat v prostředí Time Series Insights pomocí přihlašovacích údajů aplikace místo uživatelských přihlašovacích údajů.

Až budete mít aplikaci, která musí aplikace access Time Series Insights, musíte nastavit aplikaci Azure Active Directory a přiřadit zásady přístupu k datům v prostředí Time Series Insights. Tento přístup je vhodnější pro spuštění aplikace pod svými přihlašovacími údaji, protože:

* Můžete přiřadit oprávnění, která se liší od své vlastní oprávnění identitě aplikace. Tato oprávnění jsou obvykle omezené na pouze co aplikace vyžaduje. Například můžete umožnit aplikaci jenom číst data v konkrétním prostředí Time Series Insights.
* Nemusíte změnit přihlašovací údaje aplikace, pokud se změní vaše odpovědnosti.
* K automatizaci ověřování pomocí bezobslužného skriptu, můžete použít certifikát nebo klíč aplikace.

Tento článek popisuje, jak k provádění těchto kroků na webu Azure portal. Zaměřuje se na jednoho tenanta aplikaci, ve kterém je aplikace určena pro spuštění v pouze jedna organizace. Aplikace pro jednoho tenanta se obvykle používají pro-obchodní aplikace, které ve vaší organizaci.

Nastavení toku se skládá ze tří kroků:

1. Vytvoření aplikace v Azure Active Directory.
2. Povolíte této aplikaci přístup k prostředí Time Series Insights.
3. Pomocí ID aplikace a klíč k získání tokenu k `"https://api.timeseries.azure.com/"` cílové skupiny nebo prostředku. Token, který potom slouží k volání rozhraní API čas Series Insights.

Tady je podrobný postup:

1. Na webu Azure Portal, vyberte **Azure Active Directory** > **registrace aplikací** > **registrace nové aplikace**.

   ![Registrace nové aplikace v Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Pojmenujte aplikaci, vyberte typ, který má být **webovou aplikaci nebo API**, vyberte libovolný platný identifikátor URI pro **přihlašovací adresa URL**a klikněte na tlačítko **vytvořit**.

   ![Vytvoření aplikace v Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Vyberte svoji nově vytvořenou aplikaci a zkopírujte jeho ID aplikace na svém oblíbeném textovém editoru.

   ![Zkopírujte ID aplikace](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Vyberte **klíče**, zadejte název klíče, vyberte vypršení platnosti a klikněte na tlačítko **Uložit**.

   ![Vyberte klíče aplikace](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Zadejte název klíče a vypršení platnosti a klikněte na tlačítko Uložit](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Zkopírujte klíč na vašem oblíbeném textovém editoru.

   ![Zkopírujte klíč aplikace](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Pro prostředí Time Series Insights, vyberte **zásady přístupu k datům** a klikněte na tlačítko **přidat**.

   ![Přidat nové zásady přístupu dat do prostředí Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. V **vybrat uživatele** dialogové okno, vložte název aplikace (z kroku 2) nebo ID aplikace (z kroku 3).

   ![Vyhledání aplikace v dialogovém okně vyberte uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Vyberte roli (**čtečky** pro dotazování na data, **Přispěvatel** pro dotazování na data a změně referenčních dat) a klikněte na tlačítko **Ok**.

   ![Vyberte v dialogovém okně vybrat roli přispěvatele nebo čtenáře](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Uložte zásadu kliknutím **Ok**.

10. Získat token jménem aplikace pomocí ID aplikace (z kroku 3) a klíč aplikace (z kroku 5). Token, který je pak možné předat v `Authorization` záhlaví, když aplikace volá rozhraní API čas Series Insights.

    Pokud používáte C#, můžete použít následující kód k získání tokenu jménem aplikace. Úplnou ukázku najdete v tématu [dotazování dat pomocí jazyka C#](time-series-insights-query-data-csharp.md).

    ```csharp
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";

    var authenticationContext = new AuthenticationContext(
    $"https://login.microsoftonline.com/{tenant}",
    TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

Pomocí ID aplikace a klíč ve vaší aplikaci k ověřování pomocí Azure Time Series Insights. 

## <a name="next-steps"></a>Další postup
- Ukázkový kód, který volá rozhraní API čas Series Insights, naleznete v tématu [dotazování dat pomocí jazyka C#](time-series-insights-query-data-csharp.md).
- Referenční informace rozhraní API najdete v tématu [referenční dokumentace rozhraní API pro dotazy](/rest/api/time-series-insights/time-series-insights-reference-queryapi).

> [!div class="nextstepaction"]
> [Vytvoření instančního objektu služby](../azure-resource-manager/resource-group-create-service-principal-portal.md)
