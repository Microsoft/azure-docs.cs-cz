---
title: Omezení spolupráce Azure Active Directory s B2B | Microsoft Docs
description: Aktuální omezení pro spolupráci Azure Active Directory s B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 562076e9529ffeac4cb0f99c1ffd4d4866d0bd1a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Omezení spolupráce Azure AD B2B
Spolupráce Azure Active Directory (Azure AD) s B2B aktuálně podléhá omezení popsaná v tomto článku.

## <a name="possible-double-multi-factor-authentication"></a>Možné double aplikace Multi-Factor authentication
S B2B Azure AD můžete vynutit ověřování Multi-Factor authentication v organizaci prostředků (pozváním organizace). Důvody tohoto přístupu jsou podrobně popsané na [podmíněného přístupu pro uživatele spolupráce B2B](conditional-access.md). Pokud partnera již obsahuje službu Multi-Factor authentication nastavit a vynutit, uživatelé pravděpodobně k provedení ověření jednou v jejich domovskou organizaci a potom znovu za vaše.

## <a name="instant-on"></a>Okamžitého
Toky spolupráce B2B nemůžeme přidat uživatele do adresáře a dynamicky aktualizovat během uplatnění pozvánku, aplikace přiřazení a tak dále. Aktualizace a zápisy normálně dojít v jednom adresáři instance a musí být replikována napříč všemi instancemi. Jakmile jsou aktualizovány všechny instance po dokončení replikace. V některých případech, pokud objekt se zapisují nebo aktualizuje v jedné instance a volání k načtení tohoto objektu je k jiné instanci, může dojít latenci replikace. Pokud k tomu dojde, aktualizovat nebo opakujte pomohou. Pokud píšete mobilní aplikace pomocí našem rozhraní API, opakování s některé back vypnout je dobré, Obranným postupem se tím tento problém.

## <a name="next-steps"></a>Další postup

Na spolupráci Azure AD B2B najdete v následujících článcích:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Delegovat pozvánek B2bB spolupráce](delegate-invitations.md)

