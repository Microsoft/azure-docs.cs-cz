---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471497"
---
Pokud chcete, aby se uživatelům před udělením přístupu zobrazila výzva k zadání druhého faktoru ověřování, můžete nakonfigurovat azure multifaktorové ověřování (MFA). Vícefaktorové ověřování můžete konfigurovat pro jednotlivé uživatele nebo můžete využít vícefaktorové ověřování pomocí [podmíněného přístupu](../articles/active-directory/conditional-access/overview.md).

* MFA na uživatele lze povolit bez dodatečných nákladů. Při povolení MFA na uživatele, uživatel bude vyzván k ověření druhého faktoru proti všechny aplikace vázané na tenanta Azure AD. Postup najdete [v tématu Možnost 1.](#peruser)
* Podmíněný přístup umožňuje podrobnější kontrolu nad tím, jak by měl být podporován druhý faktor. Může povolit přiřazení Vícefaktorové ověřování pouze na síť VPN a vyloučit další aplikace vázané na klienta Azure AD. Postup najdete [v tématu Možnost 2.](#conditional)