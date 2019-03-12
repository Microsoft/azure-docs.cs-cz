---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554798"
---
Zde jsou uvedena omezení využití a další omezení služby pro službu Azure Active Directory (Azure AD).

| Kategorie | Omezení |
| --- | --- |
| Adresáře | Jeden uživatel může patřit do maximálně 500 adresářů služby Azure AD jako člen nebo Host.<br/>Jenom jednoho konkrétního uživatele můžete vytvořit maximálně 20 adresářů. |
| Domény | Můžete přidat více než 900 spravovaných názvech domén. Pokud jste nastavili všech domén pro federaci s místní služby Active Directory, můžete přidat více než 450 názvy domén v každém adresáři. |
| Objekty |<ul><li>Maximálně 500 000 objektů lze vytvořit v jednom adresáři uživatelé edice Free služby Azure Active Directory.</li><li>Uživatelé, kteří nejsou správci, můžou vytvořit maximálně 250 objektů. Aktivní objekty a odstraněných objektů, které jsou k dispozici pro obnovení počítají této kvóty. Pouze objekty, které byly odstraněny. odstraněna před méně než 30 dny jsou k dispozici pro obnovení. Odstraněné objekty, které už nejsou k dispozici pro obnovení počítají tato kvóta na hodnotě jedna čtvrtina po dobu 30 dnů. Možná [přiřazení role správce](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) uživatelům bez oprávnění správce, kteří jsou opakovaně může potenciálně překročit tuto kvótu při pravidelných těchto úkolů.</li></ul> |
| Rozšíření schématu |<ul><li>Rozšíření řetězcového typu může mít maximálně 256 znaků. </li><li>Binární typ rozšíření jsou omezena 256 bajty.</li><li>Pouze 100 hodnot rozšíření, napříč *všechny* typy a *všechny* aplikací, je možné zapisovat na libovolný objekt.</li><li>Pouze entity uživatele, skupiny, TenantDetail, zařízení, Application a ServicePrincipal je možné rozšířit pomocí typu řetězec nebo binární typ jednohodnotové atributy.</li><li>Rozšíření schématu jsou k dispozici pouze v rozhraní Graph API verze 1.21 preview. Pro registraci rozšíření musí aplikace mít přístup k zápisu.</li></ul> |
| Aplikace |Maximálně 100 uživatelů může být vlastníkem jedné aplikace. |
| Skupiny |<ul><li>Maximálně 100 uživatelů může být vlastníkem jedné skupiny.</li><li>Libovolný počet objektů, které může být členem jedné skupiny.</li><li>Uživatel může být členem skupiny libovolný počet skupin.</li><li>Počet členů ve skupině, které můžete synchronizovat z místní Active Directory do Azure Active Directory pomocí služby Azure AD Connect je omezen na 50 000.</li></ul> |
| Přístupový panel |<ul><li>Neexistuje žádné omezení počtu aplikací, které lze zobrazit na přístupovém panelu na uživatele. To platí pro uživatele s přiřazenými licencemi pro Azure AD Premium nebo Enterprise Mobility Suite.</li><li>Maximálně 10 dlaždic aplikací uvidí na přístupovém panelu pro každého uživatele. Toto omezení platí pro uživatele, kteří jsou přiřazení licence zdarma nebo edice Azure AD Basic služby Azure Active Directory. Příklady dlaždic aplikací: Box, Salesforce nebo Dropbox. Toto omezení neplatí pro účty správců.</li></ul> |
| Reports | V jednotlivých sestavách je možné zobrazit nebo stáhnout maximálně 1 000 řádků. Veškerá další data se oříznou. |
| Jednotky pro správu | Objekt může být členem maximálně 30 jednotek pro správu. |
| Správce rolí a oprávnění | <li>Skupinu nelze přidat jako vlastníka.<li>Skupinu nelze přiřadit k roli.<li>Ve výchozím nastavení uživatele, není možné změnit oprávnění, s výjimkou tenanta přepínače, které je určené nastavení uživatele ve službě Azure AD. |
