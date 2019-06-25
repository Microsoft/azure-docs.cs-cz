---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: e6e1014ac20ef70f21344ec6ece5627eccb7ba66
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148407"
---
Tady jsou omezení využití a další omezení služby Azure Active Directory (Azure AD).

| Category | Omezení |
| --- | --- |
| Adresáře | Jeden uživatel může jako člen nebo host patřit do maximálně 500 adresářů služby Azure AD.<br/>Jeden uživatel může vytvořit maximálně 20 adresářů. |
| Domény | Nemůžete přidat víc než 900 názvů spravovaných domén. Pokud pro všechny domény nastavíte federaci s využitím místní služby Active Directory, můžete v každém adresáři přidat maximálně 450 názvů domén. |
| Objekty |<ul><li>Uživatelé edice Free služby Azure Active Directory mohou ve výchozím nastavení v jednom adresáři vytvořit maximálně 50 000 objektů. Pokud máte alespoň jednu ověřenou doménu, výchozí kvóta adresářové služby v Azure AD se zvyšuje na 300 000 objektů. </li><li>Uživatelé, kteří nejsou správci, můžou vytvořit maximálně 250 objektů. Do této kvóty se započítávají aktivní objekty i odstraněné objekty, které jsou k dispozici pro obnovení. Pro obnovení jsou k dispozici jenom objekty odstraněné před méně než 30 dny. Odstraněné objekty, které už nejsou k dispozici pro obnovení, se do této kvóty po dobu 30 dnů započítávají jednou čtvrtinou. Možná bude vhodné [přiřadit roli správce](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) uživatelům, kteří nejsou správci a kteří budou pravděpodobně v rámci svých běžných povinností tuto kvótu překračovat.</li></ul> |
| Rozšíření schématu |<ul><li>Rozšíření řetězcového typu (String) mohou mít maximálně 256 znaků. </li><li>Rozšíření binárního typu (Binary) jsou omezena na 256 bajtů.</li><li>K libovolnému objektu je možné zapsat jenom 100 hodnot rozšíření (v rámci *všech* typů a *všech* aplikací).</li><li>Rozšířit pomocí atributů typu String nebo Binary s jednou hodnotou se dají jenom entity User, Group, TenantDetail, Device, Application a ServicePrincipal.</li><li>Rozšíření schématu jsou k dispozici jenom v rozhraní Graph API verze 1.21 Preview. Pro registraci rozšíření musí aplikace mít přístup k zápisu.</li></ul> |
| Aplikace |Vlastníky jedné aplikace může být maximálně 100 uživatelů. |
| Skupiny |<ul><li>Vlastníky jedné skupiny může být maximálně 100 uživatelů.</li><li>Členy jedné skupiny může být libovolný počet objektů.</li><li>Uživatel může být členem libovolného počtu skupin.</li><li>Počet členů ve skupině, které můžete synchronizovat z místní služby Active Directory do Azure Active Directory pomocí služby Azure AD Connect, je omezený na 50 000.</li></ul> |
| Proxy aplikací | <ul><li>Maximálně 500 transakcí za sekundu na aplikaci Proxy aplikací</li><li>Maximálně 750 transakcí za sekundu pro příslušného tenanta</li></ul><br/>Transakce je definován jako jeden http požadavku a odpovědi pro prostředek jedinečný. Když omezené, klienti obdrží odpověď 429 (příliš mnoho požadavků). |
| Přístupový panel |<ul><li>Počet aplikací, které se zobrazí na přístupovém panelu pro jednoho uživatele, není nijak omezený. To platí pro uživatele s přiřazenými licencemi pro Azure AD Premium nebo Enterprise Mobility Suite.</li><li>Jednotliví uživatelé mohou na přístupovém panelu zobrazit maximálně 10 dlaždic aplikací. Tento limit platí pro uživatele s přiřazenými licencemi pro edice Free nebo Azure AD Basic služby Azure Active Directory. Příklady dlaždic aplikací zahrnují Box, Salesforce nebo Dropbox. Toto omezení se nevztahuje na účty správců.</li></ul> |
| Sestavy | V jednotlivých sestavách je možné zobrazit nebo stáhnout maximálně 1 000 řádků. Veškerá další data se oříznou. |
| Jednotky pro správu | Objekt může být členem maximálně 30 jednotek pro správu. |
| Oprávnění a role správce | <ul><li>Skupinu nelze přidat jako [vlastníka](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>Nelze přiřadit skupinu [role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>Uživatelé budou moci číst informace adresáře jiných uživatelů není možné omezit mimo přepínač celého tenanta můžete zakázat přístup všichni uživatelé bez oprávnění správce na všech informací adresáře (nedoporučuje se). Další informace o výchozích oprávnění [tady](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Může trvat až 15 minut nebo podepisování out/přihlášení před správce přidání členství role a zrušení projeví.</li></ul> |
