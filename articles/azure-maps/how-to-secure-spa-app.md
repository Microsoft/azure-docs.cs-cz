---
title: Jak zabezpečit jednostránkové aplikaci pomocí neinteraktivního přihlašování
titleSuffix: Azure Maps
description: Postup konfigurace jednostránkové aplikace s neinteraktivním řízením přístupu na základě role Azure (Azure RBAC) a Azure Maps Web SDK
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 000f6a80a2cee14abc3d954de479dd87b1edf876
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090246"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Jak zabezpečit jednostránkové aplikaci pomocí neinteraktivního přihlašování

Následující příručka se týká aplikace, která používá Azure Active Directory (Azure AD) k poskytování přístupového tokenu pro Azure Maps aplikací, když se uživatel nemůže přihlásit ke službě Azure AD. Tento tok vyžaduje hostování webové služby, která musí být zabezpečená, aby k nim měl přistup jenom webová aplikace s jednou stránkou. Existuje několik implementací, které můžou provádět ověřování do Azure AD. Tato příručka využívá produkt, funkce Azure Functions k získání přístupových tokenů.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Služby Azure Maps můžou podporovat přístupové tokeny od uživatelských přihlášení a interaktivních toků. Interaktivní toky umožňují omezenější rozsah přístupu a správy tajných kódů.

## <a name="create-azure-function"></a>Vytvoření funkce Azure

Vytvořte zabezpečenou aplikaci webové služby, která je zodpovědná za ověřování do služby Azure AD. 

1. Vytvořte funkci v Azure Portal. Další informace najdete v tématu [Vytvoření funkce Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function).

2. Nakonfigurujte zásady CORS pro funkci Azure, aby byly přístupné pro webovou aplikaci s jednou stránkou. Tím se zabezpečí klienti prohlížeče na povolených zdrojích vaší webové aplikace. Viz [Přidání funkce CORS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-rest-api#add-cors-functionality).

3. [Přidejte identitu přiřazenou systémem](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) do funkce Azure, která umožňuje vytvoření instančního objektu pro ověření ve službě Azure AD.  

4. Udělte účtu Azure Maps přístup na základě rolí pro identitu přiřazenou systémem. Podrobnosti najdete v tématu [udělení přístupu na základě role](#grant-role-based-access) .

5. Napíšete kód pro funkci Azure Functions, který získá Azure Maps přístupové tokeny pomocí identity přiřazené systémem s jedním z podporovaných mechanismů nebo protokolu REST. Viz [získání tokenů pro prostředky Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) .

    Příklad ukázkového protokolu REST:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Ukázková odpověď:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Konfigurace zabezpečení pro Azure Function HttpTrigger

   * [Vytvoření přístupového klíče funkce](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys)
   * [Zabezpečený koncový bod HTTP](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production) pro funkci Azure v produkčním prostředí.
   
7. Konfigurace webové aplikace Azure Maps webové sady SDK. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Udělení přístupu na základě role

Přístup k *řízení přístupu na základě role Azure udělujete (Azure RBAC)* tím, že přiřadíte identitu přiřazenou systémem k jedné nebo více definicím rolí Azure. Pokud chcete zobrazit definice rolí Azure, které jsou k dispozici pro Azure Maps, přejděte na **řízení přístupu (IAM)**. Vyberte **role**a potom vyhledejte role, které začínají na *Azure Maps*.

1. Přejít na **účet Azure Maps**. Vyberte přiřazení role **řízení přístupu (IAM)**  >  **Role assignment**.

    > [!div class="mx-imgBorder"]
    > ![Udělení přístupu pomocí Azure RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na kartě **přiřazení rolí** v části **Role**vyberte předdefinované Azure Maps definice role, jako je **Azure Maps data Reader** nebo **Azure Maps Přispěvatel dat**. V části **přiřadit přístup k**vyberte **Function App**. Vyberte objekt zabezpečení podle názvu. Pak vyberte **Uložit**.

   * Podívejte se na podrobnosti o [Přidání nebo odebrání přiřazení rolí](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

> [!WARNING]
> Azure Maps předdefinované definice rolí poskytují velmi velký přístup k mnoha Azure Mapsám rozhraní REST API. Pokud chcete omezení přístupu k rozhraním API omezit na minimum, přečtěte si téma [Vytvoření vlastní definice role a přiřazení identity přiřazené systému](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) k definici vlastní role. Tím umožníte, aby aplikace měla k disAzure Maps nejnižší oprávnění, která jsou nezbytná pro přístup k.

## <a name="next-steps"></a>Další kroky

Další porozumění scénáři jedné stránky aplikace:
> [!div class="nextstepaction"]
> [Jednostránková aplikace](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Najděte metriky využití API pro váš účet Azure Maps:
> [!div class="nextstepaction"]
> [Zobrazení metrik využití](how-to-view-api-usage.md)

Prozkoumejte další ukázky, které ukazují, jak integrovat Azure AD s Azure Maps:
> [!div class="nextstepaction"]
> [Ukázky Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)
