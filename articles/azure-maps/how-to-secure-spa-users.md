---
title: Postup zabezpečení jednostránkové aplikace pomocí přihlášení uživatele
titleSuffix: Azure Maps
description: Jak nakonfigurovat jedinou stránku aplikace, která podporuje jednotné přihlašování Azure AD pomocí Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 0ba2e23e8121a76ec281b5e411819ee7d450cbe0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319686"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>Zabezpečení jednostránkové aplikace pomocí přihlášení uživatele

Následující příručka se vztahuje na aplikaci, která je hostována na serveru obsahu nebo má minimální závislosti webového serveru. Aplikace poskytuje chráněné prostředky zabezpečené pouze uživatelům Azure AD. Cílem scénáře je povolit webové aplikaci ověřování ve službě Azure AD a volat Azure Maps rozhraní REST API jménem uživatele.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Vytvoření registrace aplikace v Azure AD

Vytvořte webovou aplikaci ve službě Azure AD, aby se uživatelé mohli přihlásit. Webová aplikace deleguje přístup uživatelů k Azure Maps rozhraní REST API.

1. V Azure Portal v seznamu služeb Azure vyberte **Azure Active Directory**  >  **Registrace aplikací**  >  **Nová registrace**.  

    > [!div class="mx-imgBorder"]
    > ![Registrace aplikace](./media/how-to-manage-authentication/app-registration.png)

2. Zadejte **název**, vyberte **typ účtu podpory**, zadejte identifikátor URI pro přesměrování, který bude představovat adresu URL, kterou služba Azure AD vydá token, a je adresa URL, kde je ovládací prvek mapy hostovaný. Podrobný příklad najdete v tématu [Azure Maps ukázek Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant). Pak vyberte **Register** (Registrovat).  

3. Pokud chcete přiřadit Azure Maps oprávnění k delegovanému rozhraní API, Projděte si aplikaci. Pak v části **Registrace aplikací**vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**. V části **rozhraní API moje organizace používá**, vyhledejte a vyberte **Azure Maps**.

    > [!div class="mx-imgBorder"]
    > ![Přidat oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/app-permissions.png)

4. Zaškrtněte políčko vedle pole přístup k **Azure Maps**a pak vyberte **Přidat oprávnění**.

    > [!div class="mx-imgBorder"]
    > ![Výběr oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/select-app-permissions.png)

5. Povolit `oauth2AllowImplicitFlow` . Pokud ho chcete povolit, v části **manifest** registrace vaší aplikace nastavte `oauth2AllowImplicitFlow` na `true` .

6. Zkopírujte ID aplikace Azure AD a ID tenanta Azure AD z registrace aplikace, která se má použít v sadě web SDK. Přidejte podrobnosti o registraci aplikace Azure AD a `x-ms-client-id` z účtu mapy Azure do webové sady SDK.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. Nakonfigurujte řízení přístupu na základě role Azure pro uživatele nebo skupiny. Pokud [chcete povolit RBAC](#grant-role-based-access-for-users-to-azure-maps), přečtěte si následující části.
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Další kroky

Další porozumění scénáři jedné stránky aplikace:
> [!div class="nextstepaction"]
> [Jednostránková aplikace](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview)

Najděte metriky využití API pro váš účet Azure Maps:
> [!div class="nextstepaction"]
> [Zobrazení metrik využití](how-to-view-api-usage.md)

Prozkoumejte ukázky, které ukazují, jak integrovat Azure AD s Azure Maps:
> [!div class="nextstepaction"]
> [Ukázky Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)
