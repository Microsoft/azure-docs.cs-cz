---
title: Přehled uživatelských účtů ve službě Azure Active Directory B2C
description: Seznamte se s typy uživatelských účtů, které lze použít ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9630e1f23b6595ca690ecafcf0c4b9bfff603f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185654"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Přehled uživatelských účtů ve službě Azure Active Directory B2C

Ve službě Azure Active Directory B2C (Azure AD B2C) existuje několik typů účtů, které lze vytvořit. Azure Active Directory, Active Directory B2B a Active Directory B2C sdílejí typy uživatelských účtů, které lze použít.

K dispozici jsou následující typy účtů:

- **Pracovní účet** – pracovní účet může přistupovat k prostředkům v tenantovi a s rolí správce může spravovat klienty.
- **Účet guest** – účet hosta může být jenom účet Microsoft nebo uživatel Služby Azure Active Directory, který lze použít pro přístup k aplikacím nebo ke správě klientů.
- **Spotřebitelský účet** – spotřebitelský účet používá uživatel aplikací, které jste zaregistrovali u Azure AD B2C. Spotřebitelské účty lze vytvořit pomocí:
  - Uživatel prochází tok uživatele registrace v aplikaci Azure AD B2C
  - Použití rozhraní Microsoft Graph API
  - Použití webu Azure Portal

## <a name="work-account"></a>Pracovní účet

Pracovní účet se vytvoří stejným způsobem pro všechny klienty založené na Azure AD. Chcete-li vytvořit pracovní účet, můžete použít informace na [úvodním panelu: Přidání nových uživatelů do služby Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Pracovní účet se vytvoří pomocí volby **Nový uživatel** na webu Azure Portal.

Při přidání nového pracovního účtu je třeba zvážit následující nastavení konfigurace:

- **Jméno** a **uživatelské jméno** - Vlastnost **Name** obsahuje dané a příjmení uživatele. **Uživatelské jméno** je identifikátor, který uživatel zadá k přihlášení. Uživatelské jméno obsahuje úplnou doménu. Část uživatelského jména s názvem názvu domény musí být buď výchozí výchozí název domény *your-domain.onmicrosoft.com*, nebo ověřený, nefederovaný vlastní název [domény,](../active-directory/fundamentals/add-custom-domain.md) například *contoso.com*.
- **Profil** - Účet je nastaven s profilem uživatelských dat. Máte možnost zadat křestní jméno, příjmení, název úlohy a název oddělení. Profil můžete upravit po vytvoření účtu.
- **Skupiny** – Použití skupiny k provádění úloh správy, jako je přiřazování licencí nebo oprávnění několika uživatelům nebo zařízením najednou. Nový účet můžete umístit do existující [skupiny](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) ve vašem tenantovi.
- **Role adresáře** – je třeba zadat úroveň přístupu, který má uživatelský účet k prostředkům ve vašem tenantovi. K dispozici jsou následující úrovně oprávnění:

    - **Uživatel** – uživatelé mohou přistupovat k přiřazeným prostředkům, ale nemohou spravovat většinu prostředků klienta.
    - **Globální správce** – globální správci mají plnou kontrolu nad všemi prostředky klienta.
    - **Omezený správce** – vyberte roli správce nebo role pro uživatele. Další informace o rolích, které lze vybrat, najdete [v tématu Přiřazení rolí správce ve službě Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="create-a-work-account"></a>Vytvoření pracovního účtu

K vytvoření nového pracovního účtu můžete použít následující informace:

- [Portál Azure](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Aktualizace profilu uživatele

K aktualizaci profilu uživatele můžete použít následující informace:

- [Portál Azure](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Resetování hesla pro uživatele

K resetování hesla uživatele můžete použít následující informace:

- [Portál Azure](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Uživatel typu host

Externí uživatele můžete pozvat do svého tenanta jako uživatele typu Host. Typický scénář pro pozvání uživatele typu Host do vašeho klienta Azure AD B2C je sdílení odpovědnosti za správu. Příklad použití účtu guest najdete [v tématu Vlastnosti uživatele spolupráce služby Azure Active Directory B2B](../active-directory/b2b/user-properties.md).

Když do tenanta pozvete uživatele typu Host, zadáte e-mailovou adresu příjemce a zprávu popisující pozvánku. Odkaz na pozvánku přenese uživatele na stránku souhlasu, kde je vybráno tlačítko **Začínáme** a je přijata kontrola oprávnění. Pokud k e-mailové adrese není připojena doručená pošta, může uživatel přejít na stránku souhlasu tak, že přejde na stránku microsoftu pomocí pozvaných přihlašovacích údajů. Uživatel je pak nucen uplatnit pozvánku stejným způsobem jako kliknutí na odkaz v e-mailu. Například: `https://myapps.microsoft.com/B2CTENANTNAME`.

Pomocí [rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) můžete také pozvat uživatele typu Host.

## <a name="consumer-user"></a>Uživatel spotřebitele

Uživatel příjemce se může přihlásit k aplikacím zabezpečeným službou Azure AD B2C, ale nemůže přistupovat k prostředkům Azure, jako je například portál Azure. Uživatel může používat místní účet nebo federované účty, například Facebook nebo Twitter. Spotřebitelský účet se vytvoří pomocí [toku registrace nebo přihlášení uživatele](user-flow-overview.md), pomocí rozhraní Microsoft Graph API nebo pomocí portálu Azure.

Můžete určit data, která jsou shromažďována při vytvoření uživatelského účtu příjemce pomocí vlastních uživatelských atributů. Další informace najdete [v tématu Definování vlastních atributů ve službě Azure Active Directory B2C](user-flow-custom-attributes.md).

Další informace o správě spotřebitelských účtů najdete v [tématu Správa uživatelských účtů Azure AD B2C pomocí Microsoft Graphu](manage-user-accounts-graph-api.md).

### <a name="migrate-consumer-user-accounts"></a>Migrace uživatelských účtů spotřebitelů

Je možné, že budete muset migrovat existující uživatelské účty příjemce z libovolného poskytovatele identity do Azure AD B2C. Další informace najdete v tématu [Migrace uživatelů do Azure AD B2C](user-migration.md).
