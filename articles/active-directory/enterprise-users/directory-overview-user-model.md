---
title: Uživatelé, skupiny, licence a role v Azure Active Directory
description: Vztah mezi uživateli a přiřazenými licencemi, role správců a členství ve skupinách v Azure Active Directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: krbain
ms.date: 12/02/2020
ms.topic: overview
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce4330b72ef7e1440689e0ec4af9009f551553a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547266"
---
# <a name="what-is-enterprise-user-management"></a>Co je Správa uživatelů v podniku?

Tento článek představuje správce Azure Active Directory ve vztahu k hlavním úkolům [správy identit](../fundamentals/active-directory-whatis.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) pro uživatele z hlediska jejich skupin, licencí, nasazených podnikových aplikací a rolí správců. Když vaše organizace roste, můžete skupiny Azure Active Directory a role správců používat k následujícímu:

* přiřazení licencí ke skupinám místo k jednotlivcům,
* delegování oprávnění pro distribuci práce při správě Azure Active Directory na méně privilegované role,
* přiřazení přístupu k podnikovým aplikacím pro skupiny.

## <a name="assign-users-to-groups"></a>Přiřazení uživatelů ke skupinám

Skupiny v Azure Active Directory můžete použít k přiřazení licencí k velkému počtu uživatelů nebo k přiřazení uživatelského přístup k nasazeným podnikovým aplikacím. Skupiny můžete použít k přiřazení všech rolí správce kromě globálního správce ve službě Azure AD nebo můžete udělit přístup k prostředkům, které jsou externí, jako jsou například aplikace SaaS nebo weby služby SharePoint.

Pokud chcete zvýšit flexibilitu a omezit práci spojenou se správou členství ve skupině, můžete v Azure Active Directory použít [dynamické skupiny](groups-create-rule.md), které budou členství ve skupině rozšiřovat a zmenšovat automaticky. Pro každého jedinečného uživatele, který je členem jedné nebo více dynamických skupin, budete potřebovat licenci Azure AD Premium P1.

## <a name="assign-licenses-to-groups"></a>Přiřazení licencí ke skupinám

Přiřazování a odebírání licencí jednotlivým uživatelům může být náročné na čas i na pozornost. Pokud místo toho [přiřadíte licence ke skupinám](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), můžete si správu licencí ve velkém měřítku značně zjednodušit.

Když se uživatelé v Azure Active Directory připojí k licencované skupině, budou jim automaticky přiřazeny příslušné licence. Když uživatelé skupinu opustí, Azure Active Directory jim přiřazené licence odebere. Bez skupin Azure Active Directory budete muset napsat powershellový skript nebo použít rozhraní Graph API, abyste mohli hromadně přidat nebo odebrat uživatelské licence uživatelům, které do organizace přicházejí, nebo z ní odcházejí.

Pokud nemáte k dispozici dostatek licencí nebo když dojde k potížím, například s plány služeb, které nelze přiřadit ve stejnou dobu, zobrazí se stav jakéhokoli problému s licencováním pro skupiny na webu Azure Portal.

>[!NOTE]
>Funkce licencování podle skupin je v tuto chvíli ve veřejné verzi Preview. V rámci verze Preview je tato funkce dostupná s každým placeným licenčním plánem Azure Active Directory (Azure AD) i v bezplatné zkušební verzi.

## <a name="delegate-administrator-roles"></a>Delegování rolí správců

Mnoho velkých organizací chce takové možnosti pro své uživatele, aby mohli získávat dostatečná oprávnění pro své pracovní úkoly bez přiřazení výkonné role globálního správce, například uživatelé, kteří se musejí registrovat aplikace. Tady je příklad nové role správce v Azure Active Directory, která pomáhá s podrobnějším rozdělením práce při správě aplikací:

 Název role | Souhrn oprávnění
 --------- | -------------------
 **Správce aplikace** | Může přidávat a spravovat podnikové aplikace a registrace aplikací a může konfigurovat nastavení aplikačního proxy serveru. Správci aplikací můžou zobrazovat zásady podmíněného přístupu a zařízení, ale nemůžou je spravovat.
 **Správce cloudové aplikace** | Může přidávat, spravovat a registrovat podnikové aplikace. Tato role má všechna oprávnění správce aplikace, nemůže však spravovat nastavení aplikačního proxy serveru.
**Vývojář aplikace** | Může přidávat a aktualizovat registrace aplikací, ale nemůže spravovat podnikové aplikace ani konfigurovat nastavení aplikačního proxy serveru.

Nové role správce Azure Active Directory neustále přidáváme. Aktuální dostupné role naleznete na webu Azure Portal nebo na [odkazu s oprávněními role správce](../roles/permissions-reference.md).

## <a name="assign-app-access"></a>Přiřazení přístup k aplikacím

Pomocí Azure AD můžete přiřadit přístup skupin k [podnikovým aplikacím, které jsou nasazené ve vaší organizaci Azure AD](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context). Pokud zkombinujete dynamické skupiny s přiřazením skupin k aplikacím, můžete zautomatizovat přiřazování uživatelského přístupu k aplikacím v souladu s růstem vaší organizace. K přiřazení přístupu k podnikovým aplikacím budete potřebovat licenci Azure Active Directory Premium P1 nebo Premium P2.

Azure AD nabízí také podrobné řízení dat, která proudí mezi aplikací a skupinami, kterým přiřadíte přístup. V možnosti [Podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) otevřete aplikaci a vyberte **Zřizování**, poté můžete provést následující:

* nastavit automatické zřizování pro aplikace, které ho podporují,
* zadat přihlašovací údaje pro připojení k rozhraní API pro správu uživatelů aplikace,
* nastavit mapování, která řídí atributy uživatelů, které proudí mezi Azure AD a aplikací, když jsou uživatelské účty zřízeny nebo aktualizovány,
* spustit a zastavit službu zřizování Azure AD pro aplikaci, vymazat mezipaměť zřizování nebo restartovat službu,
* zobrazit **sestavu aktivit zřizování**, která poskytuje protokol všech uživatelů a skupin vytvořených, aktualizovaných a odebraných mezi Azure AD a aplikací, a **sestavu o chybách zřizování**, která poskytuje podrobnější chybové zprávy.

## <a name="next-steps"></a>Další kroky

Pokud jste začínající správce Azure AD, přečtěte si základní informací v článku [Základní dokumentace ke službě Azure Active Directory](../fundamentals/index.yml).

Popřípadě můžete začít [vytvářet skupiny](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [přiřazovat licence](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [přiřazovat přístup k aplikaci](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) nebo [přiřazovat role správce](../roles/permissions-reference.md).