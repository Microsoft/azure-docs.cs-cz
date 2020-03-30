---
title: Správa ověřování | Mapy Microsoft Azure
description: Ke správě ověřování v Mapách Microsoft Azure použijte portál Azure.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335530"
---
# <a name="manage-authentication-in-azure-maps"></a>Správa ověřování v Mapách Azure

Po vytvoření účtu Azure Maps se vytvoří ID klienta a klíče pro podporu ověřování Azure Active Directory (Azure AD) a ověřování pomocí sdíleného klíče.

## <a name="view-authentication-details"></a>Zobrazit podrobnosti o ověřování

Po vytvoření účtu Azure Maps se vygenerují primární a sekundární klíče. Doporučujeme použít primární klíč jako klíč předplatného při [použití ověřování pomocí sdíleného klíče k volání Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Sekundární klíč můžete použít ve scénářích, jako jsou změny svádkových klíčů. Další informace najdete [v tématu Ověřování v Mapách Azure](https://aka.ms/amauth).

Podrobnosti o ověřování si můžete zobrazit na webu Azure Portal. V nabídce **Nastavení** vyberte v nabídce **Nastavení možnost Ověřování**.

![Podrobnosti o ověřování](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Registrace a konfigurace aplikace Azure AD

1. Na webu Azure Portal vyberte v seznamu služeb Azure**registrace** > aplikací **Azure Active Directory** > **Nová registrace**.  

    ![Registrace aplikací](./media/how-to-manage-authentication/app-registration.png)

1. Pokud jste aplikaci už zaregistrovali, pokračujte dalším krokem. Pokud jste aplikaci nezaregistrovali, zadejte **název**, zvolte **typ účtu podpory**a pak vyberte **Registrovat**.  

    ![Podrobnosti o registraci aplikace](./media/how-to-manage-authentication/app-create.png)

1. Pokud chcete k Mapám Azure přiřadit delegovaná oprávnění rozhraní API, přejděte do aplikace. Potom v části **Registrace aplikací**vyberte **oprávnění** > rozhraní API**Přidat oprávnění**. V části **ROZHRANÍ API, která moje organizace používá**, vyhledejte a vyberte Azure **Maps**.

    ![Přidání oprávnění rozhraní API aplikace](./media/how-to-manage-authentication/app-permissions.png)

1. Zaškrtněte políčko vedle **accessu Azure Maps**a pak vyberte **Přidat oprávnění**.

    ![Výběr oprávnění rozhraní API aplikace](./media/how-to-manage-authentication/select-app-permissions.png)

1. V závislosti na metodě ověřování proveďte jeden z následujících kroků. 

    * Pokud vaše aplikace používá ověřování uživatelských tokenů pomocí sady `oauth2AllowImplicitFlow`Azure Maps Web SDK, povolte . Chcete-li ji **Manifest** povolit, nastavte `oauth2AllowImplicitFlow` v části Manifest registrace aplikace na hodnotu true. 
    
       ![Manifest aplikace](./media/how-to-manage-authentication/app-manifest.png)

    * Pokud vaše aplikace používá ověřování serveru nebo aplikace, přejděte na stránce registrace aplikace na **certifikáty & tajných kódů**. Potom buď nahrajte certifikát veřejného klíče nebo vytvořte heslo výběrem **nového tajného klíče klienta**. 
    
       ![Vytvoření tajného klíče klienta](./media/how-to-manage-authentication/app-keys.png)

        Pokud vytvoříte heslo, pak po výběru **přidat**, zkopírujte heslo a bezpečně jej uložte. Toto heslo použijete k získání tokenů z Azure AD.

       ![Přidání tajného klíče klienta](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Udělení řízení přístupu na základě rolí do Azure Maps

Po přidružení účtu Azure Maps k tenantovi Azure AD můžete udělit řízení přístupu. Udělit *řízení přístupu na základě rolí* (RBAC) přiřazením uživatele, skupiny nebo aplikace na jednu nebo více rolí řízení přístupu Azure Maps. 

1. Přejděte na svůj **účet Azure Maps**. Vyberte**přiřazení role** **Řízení přístupu (IAM).** > 

    ![Grant RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Na kartě **Přiřazení rolí** vyberte v části **Role** **položku Azure Maps Date Reader (Preview).** V části **Přiřadit přístup k možnosti**vyberte **uživatele, skupinu nebo instanční objekt Služby Azure AD**. Vyberte uživatele nebo aplikaci. Potom vyberte **Uložit**.

    ![Přidat přiřazení role](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Zobrazit dostupné role RBAC map Azure

Chcete-li zobrazit role RBAC, které jsou k dispozici pro Azure Maps, přejděte na **řízení přístupu (IAM)**. Vyberte **Role**a vyhledejte role, které začínají na *Azure Maps*. Tyto role Azure Maps jsou role, které můžete udělit přístup.

![Zobrazit dostupné role](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Zobrazit Azure Maps RBAC

RBAC poskytuje podrobné řízení přístupu.

Chcete-li zobrazit uživatele a aplikace, kterým byl udělen RBAC pro Azure Maps, přejděte na **přístup k řízení přístupu (IAM).** Tady vyberte **Přiřazení rolí**a pak filtrujte podle **Azure Maps**.

![Zobrazit uživatele a aplikace, kterým byl udělen RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Žádost o žetony pro Azure Maps

Po registraci aplikace a přidružit ji k Azure Maps, můžete požádat o přístupové tokeny.

Pokud vaše aplikace používá ověřování uživatelských tokenů s webovou sadou Azure Maps Web SDK, nakonfigurujte stránku HTML pomocí ID klienta Azure Maps a ID aplikace Azure AD.

Pokud vaše aplikace používá ověřování serveru nebo aplikace, potom požádejte `https://login.microsoftonline.com`o token z koncového bodu tokenu Azure AD . V žádosti použijte následující údaje: 

* ID prostředku Azure AD`https://atlas.microsoft.com/`
* ID klienta Azure Maps
* ID aplikace Azure AD
* Heslo nebo certifikát registrace aplikace Azure AD

| Prostředí Azure   | Koncový bod tokenu Azure AD | ID prostředku Azure |
| --------------------|-------------------------|-------------------|
| Veřejný cloud Azure        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Cloud Azure Government   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Další informace o vyžádání přístupových tokenů z Azure AD pro uživatele a instanční objekty, najdete v [tématu scénáře ověřování pro Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Další kroky

Další informace najdete [v tématu Azure AD a Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Najděte metriky využití rozhraní API pro svůj účet Azure Maps:
> [!div class="nextstepaction"] 
> [Zobrazení metrik využití](how-to-view-api-usage.md)

Prozkoumejte ukázky, které ukazují, jak integrovat Azure AD s Azure Maps:

> [!div class="nextstepaction"]
> [Ukázky ověřování Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
