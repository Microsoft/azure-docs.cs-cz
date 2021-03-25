---
title: Přehled uživatelských účtů v Azure Active Directory B2C
description: Přečtěte si o typech uživatelských účtů, které se dají použít v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: f2473955db5439911280360b169f469b8c93eb93
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043593"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Přehled uživatelských účtů v Azure Active Directory B2C

V Azure Active Directory B2C (Azure AD B2C) existuje několik typů účtů, které lze vytvořit. Azure Active Directory, služba Active Directory B2B a Active Directory B2C sdílet v typech uživatelských účtů, které se dají použít.

K dispozici jsou tyto typy účtů:

- **Pracovní účet** – pracovní účet má přístup k prostředkům v tenantovi a s rolí správce může spravovat klienty.
- **Účet Guest** – účet hosta může být jenom účet Microsoft nebo uživatel Azure Active Directory, který se dá použít pro přístup k aplikacím nebo správě tenantů.
- **Účet příjemce** – účet příjemce se používá uživatelem aplikací, které jste zaregistrovali v Azure AD B2C. Uživatelské účty je možné vytvořit pomocí:
  - Uživatel prochází uživatelským tokem registrace v aplikaci Azure AD B2C.
  - Používání rozhraní Microsoft Graph API
  - Použití webu Azure Portal

## <a name="work-account"></a>Pracovní účet

Pracovní účet se vytvoří stejným způsobem pro všechny klienty založené na Azure AD. Chcete-li vytvořit pracovní účet, můžete použít informace v [rychlém startu: přidání nových uživatelů do Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Pracovní účet se vytvoří pomocí volby **Nový uživatel** v Azure Portal.

Když přidáte nový pracovní účet, je nutné vzít v úvahu následující nastavení konfigurace:

- **Jméno** a **uživatelské jméno** – vlastnost **název** obsahuje dané a příjmení uživatele. **Uživatelské jméno** je identifikátor, který uživatel zadá pro přihlášení. Uživatelské jméno zahrnuje úplnou doménu. Část názvu domény uživatelského jména musí být buď počáteční výchozí název domény *Your-Domain.onmicrosoft.com*, nebo ověřený nefederovaný [vlastní název domény](../active-directory/fundamentals/add-custom-domain.md) , například *contoso.com*. 
- **E-mail** – nový uživatel se také může přihlásit pomocí e-mailové adresy. V e-mailech nepodporujeme speciální znaky ani vícebajtové znaky, například japonské znaky.
- **Profil** – účet je nastavený s profilem uživatelských dat. Máte možnost zadat křestní jméno, příjmení, název úlohy a název oddělení. Po vytvoření účtu můžete profil upravit.
- **Skupiny** – pomocí skupin můžete provádět úlohy správy, jako je přiřazení licencí nebo oprávnění mnoha uživatelům nebo zařízením najednou. Nový účet můžete umístit do existující [skupiny](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) ve vašem tenantovi.
- **Role adresáře** – je třeba zadat úroveň přístupu, který uživatelský účet potřebuje k prostředkům ve vašem tenantovi. K dispozici jsou následující úrovně oprávnění:

    - **Uživatel** – uživatelé mají přístup k přiřazeným prostředkům, ale nemůžou spravovat většinu prostředků tenanta.
    - **Globální správce** – globální správci mají plnou kontrolu nad všemi prostředky tenanta.
    - **Omezený správce** – vyberte roli pro správu nebo role pro uživatele. Další informace o rolích, které je možné vybrat, najdete v tématu [přiřazení rolí správce v Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="create-a-work-account"></a>Vytvořit pracovní účet

K vytvoření nového pracovního účtu můžete použít následující informace:

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>Aktualizace profilu uživatele

K aktualizaci profilu uživatele můžete použít následující informace:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>Resetování hesla pro uživatele

K resetování hesla uživatele můžete použít následující informace:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>Uživatel typu host

Externí uživatele můžete pozvat do svého tenanta jako uživatel typu Host. Typický scénář pro pozvání uživatele typu Host ke klientovi Azure AD B2C je sdílet odpovědnosti správy. Příklad použití účtu hosta najdete v tématu [vlastnosti Azure Active Directory uživatele spolupráce B2B](../active-directory/external-identities/user-properties.md).

Při pozvání uživatele typu Host k vašemu tenantovi zadáte e-mailovou adresu příjemce a zprávu popisující pozvánku. Odkaz na pozvánku převezme uživatele na stránku souhlasu. Pokud Doručená pošta není připojená k e-mailové adrese, může uživatel přejít na stránku s souhlasem na stránce Microsoftu pomocí pozvaných přihlašovacích údajů. Uživatel pak nuceně uplatňuje pozvánku stejným způsobem jako při kliknutí na odkaz v e-mailu. Příklad: `https://myapps.microsoft.com/B2CTENANTNAME`.

K pozvání uživatele typu Host můžete použít taky [rozhraní Microsoft Graph API](/graph/api/invitation-post?view=graph-rest-beta) .

## <a name="consumer-user"></a>Uživatel příjemce

Uživatel se může přihlásit k aplikacím zabezpečeným pomocí Azure AD B2C, ale nemůže získat přístup k prostředkům Azure, jako je Azure Portal. Uživatel s uživatelským účtem může používat místní účet nebo federované účty, jako je Facebook nebo Twitter. Účet příjemce se vytvoří pomocí [uživatelského toku registrace nebo přihlašování](user-flow-overview.md), pomocí rozhraní Microsoft Graph API nebo pomocí Azure Portal.

Můžete zadat data, která se shromažďují při vytváření uživatelského účtu uživatele. Další informace najdete v tématech [Přidání uživatelských atributů a přizpůsobení uživatelského vstupu](configure-user-input.md).

Další informace o správě uživatelských účtů najdete v tématu [správa Azure AD B2C uživatelských účtů pomocí Microsoft Graph](./microsoft-graph-operations.md).

### <a name="migrate-consumer-user-accounts"></a>Migrace uživatelských účtů uživatelů

Možná budete muset migrovat existující uživatelské účty uživatelů od libovolného poskytovatele identity a Azure AD B2C. Další informace najdete v tématu [Migrace uživatelů do Azure AD B2C](user-migration.md).
