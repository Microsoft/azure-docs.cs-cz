---
title: Uživatelé, skupiny, licencování a Přehled role – Azure Active Directory | Dokumentace Microsoftu
description: Vztah mezi uživateli a přiřazenými licencemi, role správců a členství ve skupinách v Azure Active Directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: vincesm
ms.date: 01/28/2019
ms.topic: overview
ms.service: active-directory
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.openlocfilehash: d578794a258faa9ad0222207f73e108e11f3281f
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770448"
---
# <a name="users-groups-licensing-and-roles-for-large-organizations"></a>Uživatelé, skupiny, licencování a role pro velké organizace

Tento článek představuje správce Azure Active Directory ve vztahu k hlavním úkolům [správy identit](/azure/active-directory/fundamentals/identity-fundamentals?context=azure/active-directory/users-groups-roles/context/ugr-context) pro uživatele z hlediska jejich skupin, licencí, nasazených podnikových aplikací a rolí správců. Když vaše organizace roste, můžete skupiny Azure Active Directory a role správců používat k následujícímu:

* přiřazení licencí ke skupinám místo k jednotlivcům,
* delegování oprávnění pro distribuci práce při správě Azure Active Directory na méně privilegované role,
* přiřazení přístupu k podnikovým aplikacím pro skupiny.

## <a name="assign-users-to-groups"></a>Přiřazení uživatelů ke skupinám

Skupiny v Azure Active Directory můžete použít k přiřazení licencí k velkému počtu uživatelů nebo k přiřazení uživatelského přístup k nasazeným podnikovým aplikacím. Skupiny můžete použít k přiřazení rolí správce v Azure Active Directory, nebo můžete udělit přístup k prostředkům, které jsou externí, například aplikace SaaS nebo sharepointové weby.

Pokud chcete zvýšit flexibilitu a omezit práci spojenou se správou členství ve skupině, můžete v Azure Active Directory použít [dynamické skupiny](groups-create-rule.md), které budou členství ve skupině rozšiřovat a zmenšovat automaticky. Pro každého jedinečného uživatele, který je členem jedné nebo více dynamických skupin, budete potřebovat licenci Azure AD Premium P1.

## <a name="assign-licenses-to-groups"></a>Přiřazení licencí ke skupinám

Přiřazování a odebírání licencí jednotlivým uživatelům může být náročné na čas i na pozornost. Pokud místo toho [přiřadíte licence ke skupinám](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), můžete si správu licencí ve velkém měřítku značně zjednodušit.

Když se uživatelé v Azure Active Directory připojí k licencované skupině, budou jim automaticky přiřazeny příslušné licence. Když uživatelé skupinu opustí, Azure Active Directory jim přiřazené licence odebere. Bez skupin Azure Active Directory budete muset napsat powershellový skript nebo použít rozhraní Graph API, abyste mohli hromadně přidat nebo odebrat uživatelské licence uživatelům, které do organizace přicházejí, nebo z ní odcházejí.

Pokud nemáte k dispozici dostatek licencí nebo když dojde k potížím, například s plány služeb, které nelze přiřadit ve stejnou dobu, zobrazí se stav jakéhokoli problému s licencováním pro skupiny na webu Azure Portal.

>[!NOTE]
>Funkce licencování podle skupin je v tuto chvíli ve veřejné verzi Preview. V rámci verze Preview je tato funkce dostupná s každým placeným licenčním plánem Azure Active Directory (Azure AD) i v bezplatné zkušební verzi.

## <a name="delegate-administrator-roles"></a>Delegování rolí správců

Mnoho velkých organizací chce takové možnosti pro své uživatele, aby mohli získávat dostatečná oprávnění pro své pracovní úkoly bez přiřazení výkonné role globálního správce, například uživatelé, kteří se musejí registrovat aplikace. Tady je příklad nové role správce v Azure Active Directory, která pomáhá s podrobnějším rozdělením práce při správě aplikací:

 Název role | Souhrn oprávnění
 --------- | -------------------
 **Správce aplikace** | Může přidávat a spravovat podnikové aplikace a registrace aplikací a může konfigurovat nastavení aplikačního proxy serveru. Správci aplikace můžou zobrazit zásady podmíněného přístupu a zařízení, ale nemůžou je spravovat.
 **Správce cloudové aplikace** | Může přidávat, spravovat a registrovat podnikové aplikace. Tato role má všechna oprávnění správce aplikace, nemůže však spravovat nastavení aplikačního proxy serveru.
**Vývojář aplikace** | Může přidávat a aktualizovat registrace aplikací, ale nemůže spravovat podnikové aplikace ani konfigurovat nastavení aplikačního proxy serveru.

Nové role správce Azure Active Directory neustále přidáváme. Aktuální dostupné role naleznete na webu Azure Portal nebo na [odkazu s oprávněními role správce](directory-assign-admin-roles.md).

## <a name="assign-app-access"></a>Přiřazení přístup k aplikacím

Azure Active Directory můžete k přiřazení skupinového přístupu k [podnikovým aplikacím, které jsou nasazené ve vašem klientovi Azure AD](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups#assign-group?context=azure/active-directory/users-groups-roles/context/ugr-context). Pokud zkombinujete dynamické skupiny s přiřazením skupin k aplikacím, můžete zautomatizovat přiřazování uživatelského přístupu k aplikacím v souladu s růstem vaší organizace. K přiřazení přístupu k podnikovým aplikacím budete potřebovat licenci Azure Active Directory Premium P1 nebo Premium P2.

Azure AD nabízí také podrobné řízení dat, která proudí mezi aplikací a skupinami, kterým přiřadíte přístup. V možnosti [Podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) otevřete aplikaci a vyberte **Zřizování**, poté můžete provést následující:

* nastavit automatické zřizování pro aplikace, které ho podporují,
* zadat přihlašovací údaje pro připojení k rozhraní API pro správu uživatelů aplikace,
* nastavit mapování, která řídí atributy uživatelů, které proudí mezi Azure AD a aplikací, když jsou uživatelské účty zřízeny nebo aktualizovány,
* spustit a zastavit službu zřizování Azure AD pro aplikaci, vymazat mezipaměť zřizování nebo restartovat službu,
* zobrazit **sestavu aktivit zřizování**, která poskytuje protokol všech uživatelů a skupin vytvořených, aktualizovaných a odebraných mezi Azure AD a aplikací, a **sestavu o chybách zřizování**, která poskytuje podrobnější chybové zprávy.

## <a name="next-steps"></a>Další postup

Pokud jste začínající správce Azure AD, přečtěte si základní informací v článku [Základní dokumentace ke službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/index).

Popřípadě můžete začít [vytvářet skupiny](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context), [přiřazovat licence](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), [přiřazovat přístup k aplikaci](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups#assign-group?context=azure/active-directory/users-groups-roles/context/ugr-context) nebo [přiřazovat role správce](directory-assign-admin-roles.md).
