---
title: Správa ověřování v Azure Maps | Microsoft Docs
description: Azure Portal můžete použít ke správě ověřování v Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 496edb4f3528daa5bd06193383f0277922e8a93a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73478785"
---
# <a name="manage-authentication-in-azure-maps"></a>Správa ověřování v Azure Maps

Po vytvoření účtu Azure Maps se vytvoří ID klienta a klíče pro podporu Azure Active Directory (Azure AD) nebo ověřování pomocí sdíleného klíče.

## <a name="view-authentication-details"></a>Zobrazit podrobnosti o ověřování

Podrobnosti o ověřování můžete zobrazit na Azure Portal. Přejděte na svůj účet a v nabídce **Nastavení** vyberte **ověřování** .

![Podrobnosti ověřování](./media/how-to-manage-authentication/how-to-view-auth.png)

 Další informace najdete v tématu [ověřování pomocí Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Nastavení registrace aplikace Azure AD

Po vytvoření účtu Azure Maps musíte vytvořit propojení mezi vaším klientem služby Azure AD a prostředkem Azure Maps.

1. Otevřete okno Azure AD a vytvořte registraci aplikace. Zadejte název pro registraci. V poli **Adresa URL pro přihlášení** zadejte domovskou stránku webové aplikace nebo rozhraní API (například https: \//localhost/). Pokud už máte registrovanou aplikaci, pokračujte na krok 2.

    ![Registrace aplikací](./media/how-to-manage-authentication/app-registration.png)

    ![Podrobnosti registrace aplikace](./media/how-to-manage-authentication/app-create.png)

2. Pokud chcete přiřadit Azure Maps oprávnění k delegovanému rozhraní API, v části **Registrace aplikací**klikněte na aplikace a pak vyberte **Nastavení**.  Vyberte **požadovaná oprávnění**a pak vyberte **Přidat**. Vyhledejte a vyberte **Azure Maps** v části **Vyberte rozhraní API**a pak vyberte tlačítko **Vybrat** .

    ![Oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/app-permissions.png)

3. V části **vybrat oprávnění**vyberte možnost **přístup Azure Maps**a pak vyberte tlačítko **Vybrat** .

    ![Výběr oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/select-app-permissions.png)

4. V závislosti na metodě ověřování proveďte krok a nebo b.

    1. Pokud vaše aplikace používá ověřování pomocí tokenu uživatele s Azure Maps Web SDK, povolte `oauthEnableImplicitFlow` nastavením na hodnotu true v části manifest na stránce s podrobnostmi o registraci aplikace.
    
       ![Manifest aplikace](./media/how-to-manage-authentication/app-manifest.png)

    2. Pokud vaše aplikace používá ověřování serveru nebo aplikace, v části registrace aplikace vyberte okno **klíče** a buď vytvořte heslo, nebo nahrajte certifikát veřejného klíče do registrace aplikace. Pokud vytvoříte heslo, po výběru **Uložit**zkopírujte heslo pro pozdější uložení a bezpečně ho uložte. Toto heslo použijete k získání tokenů z Azure AD.

       ![Klíče aplikace](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Udělení RBAC pro Azure Maps

Po přidružení účtu Azure Maps k vašemu tenantovi služby Azure AD můžete udělit řízení přístupu přiřazením uživatele, skupiny nebo aplikace k jedné nebo více rolím řízení přístupu Azure Maps.

1. Přejděte na **řízení přístupu (IAM)** , vyberte **přiřazení rolí**a pak vyberte **Přidat přiřazení role**.

    ![Udělit RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. V okně **Přidat přiřazení role** v části **role**vyberte položku **Azure Maps data Reader (Preview)** . V části **přiřadit přístup k**vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD**. V části **Vybrat**vyberte uživatele nebo aplikaci. Vyberte **Save** (Uložit).

    ![Přidat přiřazení role](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Zobrazit dostupné Azure Maps rolí RBAC

Chcete-li zobrazit role řízení přístupu na základě role (RBAC), které jsou k dispozici pro Azure Maps, přejděte na **řízení přístupu (IAM)** , vyberte **role**a potom vyhledejte role začínající **Azure Maps**. Jedná se o role, kterým můžete udělit přístup.

![Zobrazit dostupné role](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Zobrazit Azure Maps RBAC

RBAC poskytuje podrobné řízení přístupu.

Chcete-li zobrazit uživatele a aplikace, kterým byla udělená RBAC pro Azure Maps, vyberte možnost **Access Control (IAM)** , vyberte **přiřazení rolí**a pak filtrujte podle **Azure Maps**.

![Zobrazit uživatele a aplikace, kterým bylo uděleno RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Žádosti o tokeny pro Azure Maps

Po registraci aplikace a jejím přidružení k Azure Maps můžete požádat o přístupové tokeny.

* Pokud vaše aplikace používá ověřování pomocí tokenu uživatele s Azure Maps Web SDK, je nutné nakonfigurovat stránku HTML s ID Azure Maps klienta a ID aplikace Azure AD.

* Pokud vaše aplikace používá ověřování serveru nebo aplikace, musíte požádat o token z koncového bodu tokenu Azure AD `https://login.microsoftonline.com` s ID prostředku Azure AD `https://atlas.microsoft.com/`, ID klienta Azure Maps, ID aplikace Azure AD a registrační heslo aplikace Azure AD nebo certifikát.

| Prostředí Azure   | Koncový bod tokenu Azure AD | ID prostředku Azure |
| --------------------|-------------------------|-------------------|
| Veřejné Azure        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Další informace o tom, jak získat přístupové tokeny ze služby Azure AD pro uživatele a instanční objekty, najdete v tématu [scénáře ověřování pro Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Další kroky

Další informace o ověřování Azure AD a Azure Maps webové sadě SDK najdete v tématu [Azure AD a Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Přečtěte si, jak zobrazit metriky využití rozhraní API pro účet Azure Maps:
> [!div class="nextstepaction"] 
> [Zobrazit metriky využití](how-to-view-api-usage.md)

Seznam ukázek, které ukazují, jak integrovat Azure Active Directory (AAD) s Azure Maps, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Ukázky ověřování AAD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)