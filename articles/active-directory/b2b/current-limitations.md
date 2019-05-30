---
title: Omezení spolupráce B2B – Azure Active Directory | Dokumentace Microsoftu
description: Aktuální omezení pro spolupráci Azure Active Directory s B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1466614dfeb8fa4d3d095bae070d01c6503c5bb
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357241"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Omezení spolupráce B2B ve službě Azure AD
Spolupráce Azure Active Directory (Azure AD) B2B je aktuálně v souladu s omezeními popsaných v tomto článku.

## <a name="possible-double-multi-factor-authentication"></a>Je to možné double ověřování službou Multi-Factor Authentication
S Azure AD s B2B můžete vynutit ověřování Multi-Factor Authentication v organizaci poskytující prostředky (zvoucí organizaci). Důvody tohoto přístupu jsou podrobně popsány v [podmíněného přístupu pro uživatele spolupráce B2B](conditional-access.md). Pokud je partnerem už ověřování službou Multi-Factor Authentication nastavit a vynucovat, jejich uživatelé pravděpodobně k provedení ověření jednou v jejich domovskou organizaci a potom znovu za vás.

## <a name="instant-on"></a>Instant-on
Toky B2B spolupráce můžeme přidat uživatele do adresáře a dynamicky je aktualizaci spouštěné během uplatnění pozvání, přiřazení aplikace a tak dále. Aktualizace a zápisy obvykle dochází v jednom adresáři instance a musí být replikována do všech instancí. Jakmile se všechny instance se aktualizují po dokončení replikace. Někdy při objektu je zapsána nebo aktualizovat v jedné instanci a volání pro získání tohoto objektu je do jiné instance, latence replikace může dojít. Pokud k tomu dojde, aktualizujte nebo znovu pomůže. Pokud vytváříte aplikace pomocí rozhraní API, opakování pomocí některé regresní je dobré, obranné postup ke zmírnění tohoto problému.

## <a name="azure-ad-directories"></a>Adresáře Azure AD
Azure AD B2B je v souladu s Azure AD directory omezení služby. Další informace o počtu adresářů, které uživatel může vytvořit a počet adresářů které uživatele nebo uživatele typu Host může patřit, najdete v článku [limity a omezení služby Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Národní cloudy
[Národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) jsou fyzicky izolované instance Azure. Spolupráce B2B ve službě nepodporuje přes hranice národních cloudů. Například pokud tenanta služby Azure je ve veřejné globální cloud, nemůžou zvát uživatele, jehož účet je v národních cloudů. Spolupracovat s uživatelem, požádat o jiné e-mailovou adresu nebo vytvořte účet uživatele člena pro ně ve vašem adresáři.

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících článcích:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Delegování pozvánek spolupráce B2B](delegate-invitations.md)

