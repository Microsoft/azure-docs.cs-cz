---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 06/20/2018
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 10b5fbe0a03d3ea712edc9df603bbcea5e188a02
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296528"
---
Zde jsou uvedena omezení využití a další omezení služby Azure Active Directory.

| Kategorie | Omezení |
| --- | --- |
| Adresáře |Jeden uživatel může být přidružen maximálně 20 adresáře služby Azure Active Directory.<br />Příklady možných kombinací: <ul> <li>Jeden uživatel vytvoří 20 adresářů.</li><li>Jeden uživatel je přidán k 20 adresářům jako člen.</li><li>Jeden uživatel vytvoří 10 adresářů a později je někým jiným přidán k 10 různým adresářům.</li></ul> |
| Domény | Můžete přidat více než 900 názvů spravované domény. Pokud při instalaci všech vašich domén pro federaci s místní služby Active Directory, můžete přidat více než 450 názvy domén v každý adresář. |
| Objekty |<ul><li>Lze vytvořit maximálně 500 000 objektů v jednom adresáři uživatelé bezplatná edice služby Azure Active Directory.</li><li>Uživatelé, kteří nejsou správci, můžou vytvořit maximálně 250 objektů.</li></ul> |
| Rozšíření schématu |<ul><li>Rozšíření řetězcového typu (String) můžou mít maximálně 256 znaků. </li><li>Rozšíření binárního typu (Binary) jsou omezena 256 bajty.</li><li>K libovolnému objektu je možné zapsat 100 hodnot rozšíření (v rámci VŠECH typů a VŠECH aplikací).</li><li>Pouze entity User, Group, TenantDetail, Device, Application a ServicePrincipal je možné rozšířit pomocí atributů typu String nebo Binary s jednou hodnotou.</li><li>Rozšíření schématu jsou k dispozici pouze v rozhraní Graph API verze 1.21 Preview. Pro registraci rozšíření musí aplikace mít přístup k zápisu.</li></ul> |
| Aplikace |Nesmí být delší než 100 uživatelů může být vlastníky jednu aplikaci. |
| Skupiny |<ul><li>Vlastníci jedné skupiny může být maximálně 100 uživatelů.</li><li>Členem jedné skupiny ve službě Azure Active Directory může být libovolný počet objektů.</li><li>Počet členů ve skupině, které můžete synchronizovat z místní služby Active Directory do Azure Active Directory pomocí synchronizace adresářů služby Azure Active Directory (DirSync), je omezen na 15 000.</li><li>Počet členů ve skupině, které můžete synchronizovat z místní služby Active Directory do Azure Active Directory pomocí služby Azure AD Connect, je omezen na 50 000.</li></ul> |
| Přístupový panel |<ul><li>Pro koncové uživatele a pro uživatele s přiřazenými licencemi služby Azure AD Premium nebo Enterprise Mobility Suite neexistuje žádné omezení počtu aplikací, které můžou zobrazit na přístupovém panelu.</li><li>Jednotliví koncoví uživatelé s přiřazenými licencemi edice Free nebo Basic služby Azure Active Directory můžou na přístupovém panelu zobrazit maximálně 10 dlaždic aplikací (například: Box, Salesforce nebo Dropbox). Toto omezení se nevztahuje na účty správců.</li></ul> |
| Reports | V jednotlivých sestavách je možné zobrazit nebo stáhnout maximálně 1 000 řádků. Veškerá další data se oříznou. |
| Jednotky pro správu | Objekt může být členem maximálně 30 jednotek pro správu. |
