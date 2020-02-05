---
title: Spravovat ověřování | Mapy Microsoft Azure
description: Pomocí Azure Portal můžete spravovat ověřování v Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: f856aebe5e3acaca142e460d18ec8c6498b18787
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989186"
---
# <a name="manage-authentication-in-azure-maps"></a>Správa ověřování v Azure Maps

Po vytvoření účtu Azure Maps se vytvoří ID klienta a klíče pro podporu Azure Active Directory (Azure AD) a ověřování pomocí sdíleného klíče.

## <a name="view-authentication-details"></a>Zobrazit podrobnosti o ověřování

Po vytvoření účtu Azure Maps se vygenerují primární a sekundární klíče. Při volání Azure Maps s použitím [ověřování pomocí sdíleného klíče](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)se doporučuje použít primární klíč jako klíč předplatného. Sekundární klíč lze použít ve scénářích, jako je například vracení klíčových změn. Další informace najdete v tématu [ověřování pomocí Azure Maps](https://aka.ms/amauth).

Podrobnosti o ověřování můžete zobrazit na Azure Portal. Přejděte na svůj účet a v nabídce **Nastavení** vyberte **ověřování** .

![Podrobnosti ověřování](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="configure-azure-ad-app-registration"></a>Konfigurace registrace Aplikace Azure AD

1. V Azure Portal vyberte **Azure Active Directory** ze seznamu služeb Azure.  Vyberte **Registrace aplikací** a klikněte na **Nová registrace**.  Generace. Zadejte **název**, vyberte **typ účtu podpory**a klikněte na **zaregistrovat**.  Pokud už máte registrovanou aplikaci, pokračujte krokem 2. 

    ![Registrace aplikací](./media/how-to-manage-authentication/app-registration.png)

    ![Podrobnosti registrace aplikace](./media/how-to-manage-authentication/app-create.png)

2. Pokud chcete přiřadit Azure Maps oprávnění k delegovanému rozhraní API, v části **Registrace aplikací**klikněte na aplikace. Pak vyberte **oprávnění rozhraní API**a pak vyberte **Přidat oprávnění**. Vyhledejte a vyberte **Azure Maps** v části **rozhraní API moje organizace používá**.

    ![Oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/app-permissions.png)

3. Zkontrolujte **přístup Azure Maps** a pak klikněte na **Přidat oprávnění**.

    ![Výběr oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/select-app-permissions.png)

4. V závislosti na metodě ověřování proveďte krok a nebo b. 

    1. Pokud vaše aplikace používá ověřování pomocí tokenu uživatele s Azure Maps Web SDK, povolte `oauth2AllowImplicitFlow`. Pokud chcete povolit `oauth2AllowImplicitFlow`, nastavte ji na hodnotu true v části manifest registrace vaší aplikace. 
    
       ![Manifest aplikace](./media/how-to-manage-authentication/app-manifest.png)

    2. Pokud vaše aplikace používá ověřování typu server/aplikace, v okně registrace aplikace otevřete okno **certifikáty & tajných** kódů a buď vytvořte heslo kliknutím na **nový tajný klíč klienta** , nebo nahrajte certifikát veřejného klíče do registrace aplikace. Pokud vytvoříte heslo, po kliknutí na tlačítko **Přidat**zkopírujte heslo pro pozdější uložení a bezpečně ho uložte. Toto heslo použijete k získání tokenů z Azure AD.

       ![Klíče aplikace](./media/how-to-manage-authentication/app-keys.png)

       ![Přidat klíč](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>Udělení řízení přístupu na základě role (RBAC) pro Azure Maps

Po přidružení účtu Azure Maps k vašemu tenantovi služby Azure AD můžete udělit řízení přístupu. Řízení přístupu udělujete přiřazením uživatele, skupiny nebo aplikace k jedné nebo více rolím řízení přístupu Azure Maps.

1. Přejít na **účet Azure Maps**. Vyberte **řízení přístupu (IAM)** a pak vyberte **přiřazení role**.

    ![Udělit RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. V okně **přiřazení role** vyberte v části **role**položku **Azure Maps data Reader (Preview)** . V části **přiřadit přístup** vyberte možnost **uživatel, skupina nebo Princip služby Azure AD**. Vyberte uživatele nebo aplikaci. Vyberte **Uložit**.

    ![Přidat přiřazení role](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Zobrazit dostupné Azure Maps rolí RBAC

Chcete-li zobrazit role řízení přístupu na základě role (RBAC), které jsou k dispozici pro Azure Maps, přejděte na **řízení přístupu (IAM)** , vyberte **role**a potom vyhledejte role začínající **Azure Maps**. Tyto role jsou role, kterým můžete udělit přístup.

![Zobrazit dostupné role](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Zobrazit Azure Maps RBAC

RBAC poskytuje podrobné řízení přístupu.

Chcete-li zobrazit uživatele a aplikace, kterým byla udělená RBAC pro Azure Maps, vyberte možnost **Access Control (IAM)** , vyberte **přiřazení rolí**a pak filtrujte podle **Azure Maps**.

![Zobrazit uživatele a aplikace, kterým bylo uděleno RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Žádosti o tokeny pro Azure Maps

Po registraci aplikace a jejím přidružení k Azure Maps můžete požádat o přístupové tokeny.

* Pokud vaše aplikace používá ověřování pomocí tokenu uživatele s Azure Maps Web SDK, je nutné nakonfigurovat stránku HTML s ID Azure Maps klienta a ID aplikace Azure AD.

* Pokud vaše aplikace používá ověřování typu server/aplikace, musíte požádat o token z koncového bodu tokenu Azure AD `https://login.microsoftonline.com` s ID prostředku Azure AD `https://atlas.microsoft.com/`Azure Maps, ID klienta služby Azure AD, ID aplikace Azure AD a registrační heslo aplikace Azure AD nebo certifikát.

| Prostředí Azure   | Koncový bod tokenu Azure AD | ID prostředku Azure |
| --------------------|-------------------------|-------------------|
| Veřejné Azure        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Další informace o tom, jak získat přístupové tokeny ze služby Azure AD, pro uživatele a instanční objekty, najdete v tématu [scénáře ověřování pro Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Další kroky

Další informace o ověřování Azure AD a Azure Maps webové sadě SDK najdete v tématu [Azure AD a Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Přečtěte si, jak zobrazit metriky využití rozhraní API pro účet Azure Maps:
> [!div class="nextstepaction"] 
> [Zobrazit metriky využití](how-to-view-api-usage.md)

Seznam ukázek, které ukazují, jak integrovat Azure Active Directory (AAD) s Azure Maps, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Ukázky ověřování Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
