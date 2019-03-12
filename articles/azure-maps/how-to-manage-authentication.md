---
title: Spravovat ověřování ve službě Azure Maps | Dokumentace Microsoftu
description: Na webu Azure portal můžete použít ke správě ověřování ve službě Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: fe0e325543d245e4415fea925fcdade32eb00614
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570500"
---
# <a name="manage-authentication-in-azure-maps"></a>Spravovat ověřování ve službě Azure Maps

Po vytvoření účtu Azure Maps ID klienta a klíče jsou vytvořeny pro podporu služby Azure Active Directory (Azure AD) nebo ověřování pomocí sdíleného klíče.

## <a name="view-authentication-details"></a>Zobrazit podrobnosti o ověřování

Vašich ověřovacích údajů můžete zobrazit na portálu Azure portal. Přejděte na svůj účet a vyberte **ověřování** na **nastavení** nabídky.

![Podrobnosti o ověřování](./media/how-to-manage-authentication/how-to-view-auth.png)

 Další informace najdete v tématu [ověřování pomocí služby Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Nastavení registrace aplikace Azure AD

Po vytvoření účtu Azure Maps, budete muset vytvořit propojení mezi vaším tenantem Azure AD a prostředků Azure Maps.

1. Přejděte do okna Azure AD a vytvořit registrace aplikace. Zadejte název pro registraci. V **přihlašovací adresa URL** zadejte Domovská stránka webové aplikace a rozhraní API (například https://localhost/). Pokud už máte registrovaná aplikace, přejděte ke kroku 2.

    ![Registrace aplikace](./media/how-to-manage-authentication/app-registration.png)

    ![Podrobnosti registrace aplikace](./media/how-to-manage-authentication/app-create.png)

2. Pokud chcete přiřadit delegovaná oprávnění k rozhraní API pro mapy Azure, přejděte na aplikace v rámci **registrace aplikací**a pak vyberte **nastavení**.  Vyberte **požadovaná oprávnění**a pak vyberte **přidat**. Vyhledejte a vyberte **Azure Maps** pod **vyberte rozhraní API**a pak vyberte **vyberte** tlačítko.

    ![Oprávnění aplikace API](./media/how-to-manage-authentication/app-permissions.png)

3. V části **vyberte oprávnění**vyberte **přístup k Azure Maps**a pak vyberte **vyberte** tlačítko.

    ![Vyberte oprávnění aplikace API](./media/how-to-manage-authentication/select-app-permissions.png)

4. Dokončení kroku nebo b, v závislosti na vaší metodě ověřování.

    1. Pokud vaše aplikace používá token uživatele ověřování s využitím Azure Maps Web SDK, povolte `oauthEnableImplicitFlow` tak, že nastavíte na hodnotu true v manifestu části na stránce podrobností registrace vaší aplikace.
    
       ![Manifest aplikace](./media/how-to-manage-authentication/app-manifest.png)

    2. Pokud vaše aplikace používá ověřování serveru nebo aplikace, přejděte **klíče** okna portálu registrace aplikace a vytvořte heslo nebo nahrání certifikátu veřejného klíče pro registraci aplikace. Pokud vytvoříte hesla, po výběru **Uložit**, zkopírujte pro pozdější hesla a bezpečně uložit. Toto heslo budete používat k získání tokenů z Azure AD.

       ![Klíče aplikace](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Udělení RBAC pro Azure Maps

Poté, co přidružíte účet Azure Maps v tenantu Azure AD, můžete udělit řízení přístupu podle přiřazení uživatelů nebo aplikací na jeden nebo více role řízení přístupu Azure Maps.

1. Přejděte na **řízení přístupu (IAM)** vyberte **přiřazení rolí**a pak vyberte **přidat přiřazení role**.

    ![Udělení RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. V **přidat přiřazení role** okně v části **Role**vyberte **Azure Maps data Reader (Preview)**. V části **přiřadit přístup k**vyberte **uživatele, skupinu nebo instanční objekt služby Azure AD**. V části **vyberte**, vyberte uživatele nebo aplikace. Vyberte **Uložit**.

    ![Přidat přiřazení role](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Zobrazit dostupné role Azure RBAC mapy

Chcete-li zobrazit role řízení přístupu na základě rolí, které jsou k dispozici pro mapy Azure, přejděte na **řízení přístupu (IAM)** vyberte **role**, a pak vyhledejte role počínaje **Azure Maps**. Toto jsou role, které může udělit přístup k.

![Zobrazení dostupných rolí](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Zobrazení mapy Azure RBAC

RBAC nabízí podrobné řízení přístupu.

Chcete-li zobrazit seznam uživatelů a aplikací, kterým byl udělen RBAC pro Azure Maps, přejděte na **řízení přístupu (IAM)** vyberte **přiřazení rolí**a potom vyfiltrovat **Azure Maps**.

![Zobrazit uživatele a aplikace udělena RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Požádat o tokeny pro mapy Azure

Po registraci vaší aplikace a související s Azure Maps, můžete požádat o přístupové tokeny.

* Pokud vaše aplikace používá token uživatele ověřování s využitím Azure Maps Web SDK, budete muset nakonfigurovat stránku HTML s ID klienta Azure Maps a ID aplikace Azure AD.

* Pokud vaše aplikace používá server/aplikace ověřování, budete muset požádat o token z koncového bodu Azure AD přihlášení `https://login.microsoftonline.com` s ID prostředku Azure AD `https://atlas.microsoft.com/`, ID klienta Azure Maps, ID aplikace Azure AD a registrace aplikace Azure AD heslo nebo certifikát.

Další informace o vyžádání přístupových tokenů z Azure AD pro uživatele a instančních objektů najdete v tématu [scénáře ověřování pro službu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Další postup

* Další informace o ověřování Azure AD a Azure Maps Web SDK najdete v tématu [Azure AD a Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).