---
title: Omezení spolupráce Azure Active Directory s B2B | Dokumentace Microsoftu
description: Aktuální omezení pro spolupráci Azure Active Directory s B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: c32e26ca0cea0ae571514bca9e4b0cd62208af09
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078317"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Omezení spolupráce B2B ve službě Azure AD
Spolupráce Azure Active Directory (Azure AD) B2B je aktuálně v souladu s omezeními popsaných v tomto článku.

## <a name="possible-double-multi-factor-authentication"></a>Je to možné double ověřování službou Multi-Factor Authentication
S Azure AD s B2B můžete vynutit ověřování Multi-Factor Authentication v organizaci poskytující prostředky (zvoucí organizaci). Důvody tohoto přístupu jsou podrobně popsány v [podmíněného přístupu pro uživatele spolupráce B2B](conditional-access.md). Pokud je partnerem už ověřování službou Multi-Factor Authentication nastavit a vynucovat, jejich uživatelé pravděpodobně k provedení ověření jednou v jejich domovskou organizaci a potom znovu za vás.

## <a name="instant-on"></a>Instant-on
Toky B2B spolupráce můžeme přidat uživatele do adresáře a dynamicky je aktualizaci spouštěné během uplatnění pozvání, přiřazení aplikace a tak dále. Aktualizace a zápisy obvykle dochází v jednom adresáři instance a musí být replikována do všech instancí. Jakmile se všechny instance se aktualizují po dokončení replikace. Někdy při objektu je zapsána nebo aktualizovat v jedné instanci a volání pro získání tohoto objektu je do jiné instance, latence replikace může dojít. Pokud k tomu dojde, aktualizujte nebo znovu pomůže. Pokud vytváříte aplikace pomocí rozhraní API, opakování pomocí některé regresní je dobré, obranné postup ke zmírnění tohoto problému.

## <a name="azure-ad-directories"></a>Adresáře Azure AD
Azure AD B2B je v souladu s Azure AD directory omezení služby. Další informace o počtu adresářů, které uživatel může vytvořit a počet adresářů které uživatele nebo uživatele typu Host může patřit, najdete v článku [limity a omezení služby Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících článcích:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Delegování pozvánek spolupráce B2B](delegate-invitations.md)

