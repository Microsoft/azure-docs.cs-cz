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
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995607"
---
Pokud chcete, aby se uživatelé před udělením přístupu zobrazovali k druhému faktoru ověřování, můžete nakonfigurovat Azure AD Multi-Factor Authentication (MFA). Vícefaktorové ověřování můžete nakonfigurovat na základě jednotlivých uživatelů nebo můžete využít MFA prostřednictvím [podmíněného přístupu](../articles/active-directory/conditional-access/overview.md).

* Vícefaktorové ověřování na uživatele se dá povolit bez dalších poplatků. Při povolování MFA na uživatele se uživateli zobrazí výzva k zadání druhého faktoru ověřování proti všem aplikacím, které jsou vázané na tenanta Azure AD. Postup najdete v části s [možností 1](#peruser) .
* Podmíněný přístup umožňuje přesnější kontrolu nad tím, jak by měl být povýšen druhý faktor. Může umožňovat přiřazení MFA jenom k síti VPN a vyloučení dalších aplikací, které jsou vázané na tenanta Azure AD. Postup najdete v části [možnost 2](#conditional) .