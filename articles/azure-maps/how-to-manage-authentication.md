---
title: Spravovat ověřování ve službě Azure Maps | Dokumentace Microsoftu
description: Na webu Azure portal můžete použít ke správě ověřování ve službě Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: db0450ab8b765ec158201c6db149a7de7d135b98
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56143752"
---
# <a name="manage-authentication-in-azure-maps"></a>Spravovat ověřování ve službě Azure Maps

Po vytvoření účtu Azure Maps se vytvoří tři identifikátory GUID k podpoře Azure Active Directory (Azure AD) nebo ověřování pomocí sdíleného klíče.

Zjistíte tak, že přejdete do vašich ověřovacích údajů **ověřování** stránky **nastavení** na webu Azure Portal. Přejděte do svého účtu. Potom vyberte **ověřování** z nabídky.


## <a name="view-authentication-details"></a>Zobrazit podrobnosti o ověřování

Zobrazte si podrobnosti o ověřování, přejděte **ověřování** v nabídce nastavení.

![Ověření zobrazení](./media/how-to-manage-authentication/how-to-view-auth.png)

 Další informace o ověřování a Azure Maps, najdete v článku [ověřování pomocí služby Azure Maps](https://aka.ms/amauth).


## <a name="configure-azure-ad-app-registration"></a>Konfigurace registrace aplikace Azure AD

Po vytvoření účtu Azure mapa vyžádáním propojení mezi vaším tenantem Azure AD a prostředků Azure Maps Azure. 

1. Přejděte do okna AAD a vytvořit registrace aplikace s názvem a přihlašovací adresa URL jako domovská stránka webové aplikace a rozhraní API, jako "https://localhost/". Pokud už máte registrovaná aplikace, pokračujte ke kroku 2.

    ![Registrace aplikace](./media/how-to-manage-authentication/app-registration.png)

    ![Registrace aplikace](./media/how-to-manage-authentication/app-create.png)

2. Přiřadit delegovaná oprávnění k rozhraní API pro mapy Azure tak, že přejdete do aplikace v rámci registrace aplikací a klikněte na **nastavení**.  Vyberte **požadovaná oprávnění** a vyberte **přidat**. Vyhledat a pak vyberte v části Azure Maps **vyberte rozhraní API** a klikněte na tlačítko **vyberte**.

    ![Oprávnění aplikace api](./media/how-to-manage-authentication/app-permissions.png)

3. Nakonec v části oprávnění Select vyberte přístup k Azure Maps a klikněte na vybrat.

    ![Vyberte rozhraní api oprávnění aplikace](./media/how-to-manage-authentication/select-app-permissions.png)

4. Postupujte podle kroku nebo b, v závislosti na provádění ověřování.

    1. Pokud aplikace hodlá pomocí naší sady SDK služby Azure Maps webové ověřování pomocí tokenu uživatele, musíte povolit `oauthEnableImplicitFlow` tak, že nastavíte na hodnotu true v manifestu části na stránce podrobností registrace vaší aplikace. 
    
       ![Manifest aplikace](./media/how-to-manage-authentication/app-manifest.png)

    2. Pokud vaše aplikace používá server/aplikace ověřování Přejít **klíče** okně registrace aplikací a buď vytvořit heslo nebo nahrát certifikát veřejného klíče pro registraci aplikace. Pokud vytvoříte heslo, jakmile **Uložit**, zkopírujte pro pozdější, heslo a bezpečně uložte, se pomocí získat tokeny ze služby AAD. 

       ![Klíče aplikace](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Udělení RBAC pro Azure Maps

Jakmile účet Azure Maps je přidružen k tenantovi Azure AD, řízení přístupu může udělit přiřazením uživatelů nebo aplikaci k dispozici role řízení přístupu Azure Maps.

1. Přejděte do **řízení přístupu** , kliknutím na **přiřazení rolí**, a **přidat přiřazení role**.

    ![Udělení RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. V přiřazení Role pop si okno, vyberte Azure Maps data Reader (Preview) **Role**, **přiřadit přístup k**: Uživatele Azure AD, skupinu nebo instanční objekt, **vyberte** uživatele nebo aplikaci z rozevíracího seznamu, a **Uložit**.

    ![Přidat přiřazení role](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Zobrazit dostupné role Azure RBAC mapy

Chcete-li zobrazit role řízení přístupu k dispozici na základě rolí pro mapy Azure, který můžete udělit přístup, přejděte na **řízení přístupu (IAM)** možnost, klikněte na **role**, a vyhledejte role počínaje **Azure Maps**.

![Zobrazení dostupných rolí](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>Zobrazit Azure Maps řízení přístupu na základě role (RBAC)

Azure AD umožňuje podrobné řízení přístupu prostřednictvím řízení přístupu na základě role (RBAC). 

Pokud chcete zobrazit uživatele nebo aplikace, kterým byl udělen RBAC pro Azure Maps, přejděte na **řízení přístupu (IAM)** možnosti, vyberte **přiřazení rolí**a filtrovat podle **Azure Maps**. Všechny dostupné role se zobrazí níže.

![View RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Požádat o tokeny pro mapy Azure

Jakmile vaše aplikace je zaregistrované a související s Azure Maps, můžete si teď můžou vyžádat přístupové tokeny.

* Pokud aplikace je hodláte používat ověřování pomocí tokenu uživatele s naší Azure Maps Web SDK (Web), budete muset nakonfigurovat stránku html s ID klienta Azure Maps a ID aplikace Azure AD


* U aplikací s použitím ověřování serveru/aplikace požádat o token z koncového bodu Azure AD přihlášení https://login.microsoftonline.com s ID klienta Azure Maps, ID aplikace Azure AD a Azure AD App registrace heslo nebo certifikát.

Další informace o vyžádání přístupových tokenů z Azure AD pro uživatele a instančních objektů najdete v tématu [scénáře ověřování pro službu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Další postup

* Další informace o ověřování Azure AD a Azure Maps Web SDK najdete v tématu [Azure AD a Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).