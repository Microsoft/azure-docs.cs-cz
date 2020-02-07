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
ms.openlocfilehash: 30eb637e9117818758ed4ab0e3adef9db29cc698
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057307"
---
# <a name="manage-authentication-in-azure-maps"></a>Správa ověřování v Azure Maps

Po vytvoření účtu Azure Maps se vytvoří ID klienta a klíče pro podporu ověřování Azure Active Directory (Azure AD) a ověřování pomocí sdíleného klíče.

## <a name="view-authentication-details"></a>Zobrazit podrobnosti o ověřování

Po vytvoření účtu Azure Maps se vygenerují primární a sekundární klíče. Pokud [ke volání Azure Maps použijete ověřování pomocí sdíleného klíče](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication), doporučujeme použít primární klíč jako klíč předplatného. Sekundární klíč můžete použít ve scénářích, jako je například vracení klíčových změn. Další informace najdete v tématu [ověřování v Azure Maps](https://aka.ms/amauth).

Podrobnosti o ověřování můžete zobrazit v Azure Portal. Ve svém účtu v nabídce **Nastavení** vyberte **ověřování**.

![Podrobnosti ověřování](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Registrace a konfigurace aplikace Azure AD

1. V Azure Portal v seznamu služeb Azure vyberte **Azure Active Directory** > **Registrace aplikací** > **Nová registrace**.  

    ![Registrace aplikací](./media/how-to-manage-authentication/app-registration.png)

1. Pokud jste svou aplikaci už zaregistrovali, přejděte k dalšímu kroku. Pokud jste svoji aplikaci nezaregistrovali, zadejte **název**, vyberte **typ účtu podpory**a pak vyberte **zaregistrovat**.  

    ![Podrobnosti registrace aplikace](./media/how-to-manage-authentication/app-create.png)

1. Pokud chcete přiřadit Azure Maps oprávnění k delegovanému rozhraní API, Projděte si aplikaci. Pak v části **Registrace aplikací**vyberte **oprávnění rozhraní API** > **Přidat oprávnění**. V části **rozhraní API moje organizace používá**, vyhledejte a vyberte **Azure Maps**.

    ![Přidat oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/app-permissions.png)

1. Zaškrtněte políčko vedle pole přístup k **Azure Maps**a pak vyberte **Přidat oprávnění**.

    ![Výběr oprávnění rozhraní API pro aplikace](./media/how-to-manage-authentication/select-app-permissions.png)

1. V závislosti na metodě ověřování proveďte jeden z následujících kroků. 

    * Pokud vaše aplikace používá ověřování pomocí tokenu uživatele s Azure Maps Web SDK, povolte `oauth2AllowImplicitFlow`. Pokud ho chcete povolit, v části **manifest** registrace vaší aplikace nastavte `oauth2AllowImplicitFlow` na hodnotu true. 
    
       ![Manifest aplikace](./media/how-to-manage-authentication/app-manifest.png)

    * Pokud vaše aplikace používá ověřování serveru nebo aplikace, přejít na registrační stránku aplikace na **certifikáty & tajných**kódů. Pak buď Nahrajte certifikát veřejného klíče, nebo vytvořte heslo tak, že vyberete **nový tajný klíč klienta**. 
    
       ![Vytvoření tajného klíče klienta](./media/how-to-manage-authentication/app-keys.png)

        Pokud vytvoříte heslo, potom po výběru **Přidat**zkopírujte heslo a bezpečně ho uložte. Toto heslo použijete k získání tokenů z Azure AD.

       ![Přidat tajný klíč klienta](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Udělení řízení přístupu na základě role Azure Maps

Po přidružení účtu Azure Maps k vašemu tenantovi služby Azure AD můžete udělit řízení přístupu. *Řízení přístupu na základě role* (RBAC) udělíte přiřazením uživatele, skupiny nebo aplikace k jedné nebo více Azure Maps rolím řízení přístupu. 

1. Přejít na **účet Azure Maps**. Vyberte možnost **řízení přístupu (IAM)**  > **přiřazení role**.

    ![Udělit RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Na kartě **přiřazení rolí** v části **Role**vyberte položku **Azure Maps data Reader (Preview)** . V části **přiřadit přístup k**vyberte možnost **uživatel, skupina nebo instanční objekt služby Azure AD**. Vyberte uživatele nebo aplikaci. Potom vyberte **Uložit**.

    ![Přidat přiřazení role](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Zobrazit dostupné Azure Maps rolí RBAC

Chcete-li zobrazit role RBAC, které jsou k dispozici pro Azure Maps, přejděte na **řízení přístupu (IAM)** . Vyberte **role**a potom vyhledejte role, které začínají na *Azure Maps*. Tyto role Azure Maps jsou role, kterým můžete udělit přístup.

![Zobrazit dostupné role](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Zobrazit Azure Maps RBAC

RBAC poskytuje podrobné řízení přístupu.

Pokud chcete zobrazit uživatele a aplikace, kterým byla udělená RBAC pro Azure Maps, jděte do **Access Control (IAM)** . Vyberte možnost **přiřazení rolí**a potom filtrovat podle **Azure Maps**.

![Zobrazit uživatele a aplikace, kterým byla udělena RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Žádosti o tokeny pro Azure Maps

Po registraci aplikace a jejím přidružení k Azure Maps můžete požádat o přístupové tokeny.

Pokud vaše aplikace používá ověřování pomocí tokenu uživatele s Azure Maps Web SDK, nakonfigurujte svoji stránku HTML pomocí ID klienta Azure Maps a ID aplikace Azure AD.

Pokud vaše aplikace používá ověřování serveru nebo aplikace, vyžádejte si od koncového bodu tokenu Azure AD token `https://login.microsoftonline.com`. V žádosti použijte následující podrobnosti: 

* ID prostředku Azure AD `https://atlas.microsoft.com/`
* ID klienta Azure Maps
* ID aplikace Azure AD
* Heslo k registraci aplikace Azure AD nebo certifikát

| Prostředí Azure   | Koncový bod tokenu Azure AD | ID prostředku Azure |
| --------------------|-------------------------|-------------------|
| Veřejný cloud Azure        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Cloud Azure Government   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Další informace o tom, jak získat přístupové tokeny ze služby Azure AD pro uživatele a instanční objekty, najdete v tématu [scénáře ověřování pro Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Azure AD a Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Najděte metriky využití API pro váš účet Azure Maps:
> [!div class="nextstepaction"] 
> [Zobrazit metriky využití](how-to-view-api-usage.md)

Prozkoumejte ukázky, které ukazují, jak integrovat Azure AD s Azure Maps:

> [!div class="nextstepaction"]
> [Ukázky ověřování Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
