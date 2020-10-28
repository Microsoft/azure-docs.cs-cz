---
title: Postup zabezpečení vstupního omezeného zařízení pomocí služby Azure AD a Azure Maps rozhraní REST API
titleSuffix: Azure Maps
description: Jak nakonfigurovat aplikaci bez prohlížeče, která podporuje přihlášení k Azure AD a volá Azure Maps rozhraní REST API.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3833cbfd0802f334e482203d269984eb0e299797
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895626"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Zabezpečení vstupního omezeného zařízení pomocí služby Azure AD a Azure Maps rozhraní REST API

Tato příručka popisuje, jak zabezpečit veřejné aplikace nebo zařízení, která nemůžou bezpečně ukládat tajné klíče nebo přijímat vstupy prohlížeče. Tyto typy aplikací spadají do kategorie IoT nebo Internet věcí. Mezi příklady těchto aplikací patří: zařízení inteligentního televizního vysílání nebo data senzorů pro vysílání aplikací. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Vytvoření registrace aplikace v Azure AD

> [!NOTE]
> * **Požadavky na čtení:** [scénář: desktopová aplikace, která volá webová rozhraní API](../active-directory/develop/scenario-desktop-overview.md)
> * Následující scénář používá tok kódu zařízení, který nezahrnuje webový prohlížeč pro získání tokenu.

Vytvořte aplikaci založenou na zařízeních ve službě Azure AD a povolte přihlášení k Azure AD. Této aplikaci bude udělen přístup k rozhraním REST API Azure Maps.

1. V Azure Portal v seznamu služeb Azure vyberte **Azure Active Directory**  >  **Registrace aplikací**  >  **Nová registrace** .  

    > [!div class="mx-imgBorder"]
    > ![Registrace aplikace](./media/how-to-manage-authentication/app-registration.png)

2. Zadejte **název** , **v tomto adresáři organizace vyberte účty jenom** jako **podporovaný typ účtu** . V části **identifikátory URI pro přesměrování** zadejte **veřejný klient/nativní (mobilní & Desktop)** a pak přidejte `https://login.microsoftonline.com/common/oauth2/nativeclient` k hodnotě. Další podrobnosti najdete v tématu aplikace Azure AD [Desktop, která volá webová rozhraní API: registrace aplikace](../active-directory/develop/scenario-desktop-app-registration.md). Pak aplikaci **Zaregistrujte** .

    > [!div class="mx-imgBorder"]
    > ![Přidat podrobnosti registrace aplikace pro název a identifikátor URI pro přesměrování](./media/azure-maps-authentication/devicecode-app-registration.png)

3. Přejděte k **ověřování** a povolte **považovat aplikaci za veřejného klienta** . Tím se povolí ověřování kódu zařízení pomocí služby Azure AD.
    
    > [!div class="mx-imgBorder"]
    > ![Povolit registraci aplikace jako veřejného klienta](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Pokud chcete přiřadit Azure Maps oprávnění k delegovanému rozhraní API, Projděte si aplikaci. Pak vyberte **oprávnění API**  >  **Přidat oprávnění** . V části **rozhraní API moje organizace používá** , vyhledejte a vyberte **Azure Maps** .

    > [!div class="mx-imgBorder"]
    > ![Přidat oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/app-permissions.png)

5. Zaškrtněte políčko vedle pole přístup k **Azure Maps** a pak vyberte **Přidat oprávnění** .

    > [!div class="mx-imgBorder"]
    > ![Výběr oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/select-app-permissions.png)

6. Nakonfigurujte řízení přístupu na základě role v Azure (Azure RBAC) pro uživatele nebo skupiny. Přečtěte si téma [udělení přístupu na základě role uživatelům Azure Maps](#grant-role-based-access-for-users-to-azure-maps).

7. Přidejte kód pro získání toku tokenů do aplikace. Podrobnosti o implementaci najdete v části [tok kódu zařízení](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow). Při získávání tokenů odkazuje na obor: `user_impersonation` který byl vybrán v předchozích krocích.

> [!Tip]
> K získání přístupových tokenů použijte Microsoft Authentication Library (MSAL). Zobrazit doporučení pro [desktopovou aplikaci, která volá webová rozhraní API: Konfigurace kódu](../active-directory/develop/scenario-desktop-app-configuration.md)

8. Vytvořte požadavek HTTP pomocí získaného tokenu z Azure AD a odešlete žádost s platným klientem HTTP.

### <a name="sample-request"></a>Ukázková žádost
Zde je ukázkový text žádosti o nahrání jednoduché geografické ploty reprezentované jako KRUHOVÁ geometrie pomocí středového bodu a poloměru.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 Text ukázkové žádosti je uvedený níže v rámečku JSON:
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>Ukázková odpověď:

Záhlaví
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

Těles
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Další kroky

Najděte metriky využití API pro váš účet Azure Maps:
> [!div class="nextstepaction"]
> [Zobrazení metrik využití](how-to-view-api-usage.md)