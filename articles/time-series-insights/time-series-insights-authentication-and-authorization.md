---
title: Jak k ověřování a autorizaci rozhraním API v Azure časové řady přehledy
description: Tento článek popisuje postup konfigurace ověřování a autorizace pro vlastní aplikaci, která volá rozhraní API služby Azure časové řady statistiky.
ms.service: time-series-insights
services: time-series-insights
author: dmdenmsft
ms.author: dmden
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: a18caf03dbc5377f0adc540c8550129ef3bdb9e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652429"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Ověřování a autorizace pro rozhraní API pro Azure časové řady přehledy

Tento článek vysvětluje postup konfigurace ověřování a autorizace, které jsou používány vlastní aplikaci, která volá rozhraní API služby Azure časové řady statistiky.

## <a name="service-principal"></a>Instanční objekt

Tato část vysvětluje postup konfigurace aplikace pro přístup k rozhraní API pro přehledy časové řady jménem aplikace. Aplikace můžete potom dotaz na data nebo publikování referenční data v prostředí časové řady Statistika aplikace pověření než pověření uživatele.

Až budete mít aplikaci, která musí doba přístupu k řady Insights, musíte nastavit aplikaci Azure Active Directory a přiřadit zásady přístupu k datům v prostředí Statistika časové řady. Tento postup je vhodnější spuštění aplikace vlastní oprávnění, protože:

* Můžete přiřadit oprávnění k identitě aplikace, která se liší od vlastní oprávnění. Tato oprávnění jsou obvykle omezené na vyžaduje pouze, co aplikace. Můžete například povolit aplikaci pouze číst data v prostředí s konkrétní Statistika časové řady.
* Nemáte ke změně pověření aplikace, pokud vaše odpovědnosti změnit.
* Certifikát nebo klíč aplikace můžete použít k automatizaci ověřování, když používáte bezobslužného skriptu.

Tento článek ukazuje, jak provést tyto kroky prostřednictvím portálu Azure. Zaměřuje se na jednoho klienta aplikace, kde je záměrem aplikaci spustit v pouze jedné organizaci. Jednoho klienta aplikace se obvykle používají pro-obchodní aplikace, které běží ve vaší organizaci.

Nastavení toku se skládá z tři hlavní kroky:

1. Vytvoření aplikace v Azure Active Directory.
2. Autorizovat tuto aplikaci pro přístup k prostředí Statistika časové řady.
3. Pomůže získat token pro ID aplikace a klíč `"https://api.timeseries.azure.com/"` cílové skupiny nebo prostředku. Token poté slouží k volání rozhraní API pro přehledy časové řady.

Tady jsou podrobné kroky:

1. Na portálu Azure vyberte **Azure Active Directory** > **registrace aplikace** > **nové registrace aplikace**.

   ![Nové registrace aplikace v Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Zadejte název aplikace, vyberte typ, který má být **webovou aplikaci nebo API**, vyberte libovolný platný identifikátor URI pro **přihlašovací adresa URL**a klikněte na tlačítko **vytvořit**.

   ![Vytvoření aplikace v Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Vyberte nově vytvořené aplikace a zkopírujte jeho ID aplikace na svém oblíbeném textovém editoru.

   ![Zkopírujte ID aplikace](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Vyberte **klíče**, zadejte název klíče, vyberte vypršení platnosti a klikněte na tlačítko **Uložit**.

   ![Výběr aplikace klíče](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Zadejte název klíče a vypršení platnosti a klikněte na tlačítko Uložit](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Zkopírujte klíč na svém oblíbeném textovém editoru.

   ![Zkopírovat klíč aplikace](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Pro časové řady Statistika prostředí, vyberte **zásady přístupu k datům** a klikněte na tlačítko **přidat**.

   ![Přidat nové zásady přístupu data do prostředí Statistika časové řady](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. V **vyberte uživatele** dialogové okno, vložte název aplikace (z kroku 2) nebo ID aplikace (z kroku 3).

   ![Vyhledání aplikace v dialogovém okně vyberte uživatele](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Vyberte roli (**čtečky** pro dotazování na data, **Přispěvatel** pro dotazování dat a změna referenční data) a klikněte na tlačítko **Ok**.

   ![Vyberte čtečky nebo Přispěvatel v dialogovém okně vybrat roli](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Uložit zásadu kliknutím **Ok**.

10. Pomocí ID aplikace (z kroku 3) a klíč aplikace (z kroku 5) k získání tokenu jménem aplikace. Token je pak možné předat v `Authorization` záhlaví při volání rozhraní API pro přehledy časové řady.

    Pokud používáte C#, můžete použít následující kód k získání tokenu jménem aplikace. Kompletní příklad, najdete v části [dotaz na data pomocí jazyka C#](time-series-insights-query-data-csharp.md).

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
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

ID aplikace a klíč v aplikaci použijte k ověření pomocí Azure časové řady přehledy. 

## <a name="next-steps"></a>Další postup
- Ukázkový kód, který volá rozhraní API pro přehledy časové řady, najdete v části [dotaz na data pomocí jazyka C#](time-series-insights-query-data-csharp.md).
- Rozhraní API referenční informace najdete v tématu [referenční dokumentace rozhraní API dotazu](/rest/api/time-series-insights/time-series-insights-reference-queryapi).

> [!div class="nextstepaction"]
> [Vytvoření instančního objektu služby](../azure-resource-manager/resource-group-create-service-principal-portal.md)
