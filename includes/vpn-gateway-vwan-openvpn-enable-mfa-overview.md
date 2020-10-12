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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471497"
---
Pokud chcete, aby se uživatelé před udělením přístupu zobrazovali pro druhý faktor ověřování, můžete nakonfigurovat Azure Multi-Factor Authentication (MFA). Vícefaktorové ověřování můžete nakonfigurovat na základě jednotlivých uživatelů nebo můžete využít MFA prostřednictvím [podmíněného přístupu](../articles/active-directory/conditional-access/overview.md).

* Vícefaktorové ověřování na uživatele se dá povolit bez dalších poplatků. Při povolování MFA na uživatele se uživateli zobrazí výzva k zadání druhého faktoru ověřování proti všem aplikacím, které jsou vázané na tenanta Azure AD. Postup najdete v části s [možností 1](#peruser) .
* Podmíněný přístup umožňuje přesnější kontrolu nad tím, jak by měl být povýšen druhý faktor. Může umožňovat přiřazení MFA jenom k síti VPN a vyloučení dalších aplikací, které jsou vázané na tenanta Azure AD. Postup najdete v části [možnost 2](#conditional) .