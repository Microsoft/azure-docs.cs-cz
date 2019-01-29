---
title: Přehled uživatelských účtů v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o uživatelských účtech v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a901072875d6810711cb0b23d17555d9492c240a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157971"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Přehled uživatelských účtů v Azure Active Directory B2C

V Azure Active Directory (Azure AD) B2C můžete použít různé typy účtů. Typy uživatelských účtů, které lze použít sdílené složky Azure Active Directory, Azure Active Directory s B2B a Azure Active Directory B2C.

K dispozici jsou následující typy účtů:

- **Pracovní účet** – pracovní účet přístup k prostředkům v tenantovi, a pomocí Správce rolí, můžete spravovat klienty.
- **Účet Guest** – pomocí účtu guest může být jenom účet Microsoft nebo Azure Active Directory uživatele, který slouží k přístupu k aplikacím nebo správě tenantů. 
- **Uživatelský účet** – uživatelský účet je vytvořen prostřednictvím tok registrace uživatele v aplikaci Azure AD B2C, nebo pomocí Azure AD Graph API a používá se uživatelé aplikace, které jsou registrované v Azure AD B2C. 

## <a name="work-account"></a>Pracovní účet

Pracovní účet se vytvoří stejným způsobem jako pro všechny klienty založené na službě Azure AD. Chcete-li vytvořit pracovní účet, můžete použít informace v [rychlý start: Přidání nových uživatelů do služby Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Pracovní účet je vytvořený pomocí **nového uživatele** choice na webu Azure Portal.

Když přidáte nový pracovní účet, je potřeba zvážit následující nastavení konfigurace:

- **Název** a **uživatelské jméno** – **název** obsahuje vlastnost daný jména a příjmení uživatele. **Uživatelské jméno** je identifikátor, který uživatel zadá pro přihlášení. Uživatelské jméno obsahuje úplnou doménu. Část názvu domény, uživatelského jména musí být buď počáteční výchozí název domény *Vaše_Doména.onmicrosoft.com*, nebo ověřený, jiné než federované [vlastní doménu](../active-directory/fundamentals/add-custom-domain.md) název například  *contoso.com*.
- **Profil** – účet je nastavený s profilem uživatelská data. Máte možnost zadat křestní jméno, příjmení, pozice a oddělení název. Profil, který můžete upravit po vytvoření účtu.
- **Skupiny** -použít skupinu k provádění úloh správy, jako je například přiřazení licencí nebo oprávnění počtu uživatelů nebo zařízení najednou. Nový účet můžete umístit do stávající [skupiny](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) ve vašem tenantovi. 
- **Role adresáře** – je potřeba zadat úroveň přístupu, uživatelský účet má k prostředkům ve vašem tenantovi. K dispozici jsou následující úrovně oprávnění:

    - **Uživatel** – uživatelé mají přístup k přiřazeným prostředkům ale nemůže spravovat většinu prostředků tenanta.
    - **Globální správce** – globální Správci mají plnou kontrolu nad všechny prostředky tenanta.
    - **Správce s omezením** – vyberte roli správce nebo role pro uživatele. Další informace o rolích, které lze vybrat, najdete v části [přiřazení rolí správce v Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md). 

### <a name="create-a-work-account"></a>Vytvořit pracovní účet

Tyto informace slouží k vytvoření nové pracovní účet:

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_post_users)

### <a name="update-a-user-profile"></a>Aktualizovat profil uživatele.

Aktualizovat profil uživatele můžete použít následující informace:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_update)

### <a name="reset-a-password-for-a-user"></a>Resetování hesla pro uživatele

Resetovat heslo uživatele, můžete použít následující informace: 

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_update)

## <a name="guest-user"></a>Uživatel typu host

Externí uživatelé do svého tenanta můžete pozvat jako uživatele typu Host. Typický scénář pro pozvání uživatelů typu Host do tenanta Azure AD B2C je ke sdílení správy odpovědnosti. Příklad použití účtu guest, naleznete v tématu [vlastnosti uživatele spolupráce Azure Active Directory s B2B](../active-directory/b2b/user-properties.md).

Pozvat uživatele typu Host do tenanta, zadáte e-mailová adresa příjemce a zprávu s popisem e-mailové pozvánce. Odkaz žádosti přejde stránka pro odsouhlasení podmínek kde **Začínáme** výběru tlačítka a přijmout kontrolu oprávnění. Pokud doručené pošty není připojena k e-mailovou adresu, můžete uživatele tak, že přejdete na stránku Microsoft pomocí pozvaného přihlašovacích údajů přejděte na stránka pro odsouhlasení podmínek. Uživatel pak musí pro uplatnění pozvání stejným způsobem jako při kliknutí na odkaz v e-mailu. Například: `https://myapps.microsoft.com/B2CTENANTNAME`.

Můžete také použít [Microsoft Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/invitation_post) pozvat uživatele typu Host.

## <a name="consumer-user"></a>Příjemce uživatele

Příjemce uživatel může přihlásit k aplikacím, které jsou zabezpečené pomocí Azure AD B2C, ale nemají přístup k prostředkům Azure, jako je například na webu Azure portal.  Uživatel příjemce může použít místní účet nebo účty federované, jako je Facebook nebo Twitter. Uživatelský účet je vytvořen pomocí [tok registrace / přihlášení uživatele](../active-directory-b2c/active-directory-b2c-reference-policies.md).

Můžete zadat data, která shromažďuje, když uživatelský účet uživatele je vytvořená pomocí vlastní uživatelské atributy. Další informace najdete v tématu [definovat vlastní atributy v Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md).

Můžete použít informace v **vytváření uživatelských účtů uživatelů** část [pomocí Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) vytvoření účtu služby Azure AD B2C příjemce. Můžete také použít informace v **aktualizovat příjemce uživatelské účty** oddílu ve stejném článku spravovat vlastnosti účtu.

### <a name="migrate-consumer-user-accounts"></a>Migrace uživatelů uživatelské účty

Bude pravděpodobně potřeba migrovat existující uživatelské účty uživatelů ze všech poskytovatelů identit Azure AD B2C. Další informace najdete v tématu [migraci uživatelů](active-directory-b2c-user-migration.md) nebo [migrace uživatelů se sociálními identitami](active-directory-b2c-social-migration.md).
