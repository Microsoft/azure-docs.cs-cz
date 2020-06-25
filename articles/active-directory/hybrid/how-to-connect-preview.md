---
title: 'Azure AD Connect: funkce ve verzi Preview | Microsoft Docs'
description: V tomto tématu najdete další informace o funkcích, které jsou ve verzi Preview v Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bb7305fe8ee96697c5c8b2172e32a2a97e5bd6c
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85358425"
---
# <a name="more-details-about-features-in-preview"></a>Další podrobnosti o funkcích ve verzi Preview
Toto téma popisuje, jak používat funkce, které jsou aktuálně ve verzi Preview.

## <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Rozhraní API koncového bodu služby Azure AD Connect Sync v2 (Public Preview) 

Nasadili jsme nový koncový bod (API) pro Azure AD Connect, který vylepšuje výkon operací synchronizační služby Azure Active Directory. Díky použití nového koncového bodu v2 budete mít při exportu a importu do Azure AD patrné zvýšení výkonu. Tento nový koncový bod podporuje i synchronizaci skupin s až 250 tisíc členy. Použití tohoto koncového bodu vám taky umožní zapisovat do vaší místní služby Active Directory zpátky sjednocené skupiny O365 bez maximálního počtu členství, pokud je povolen zpětný zápis skupin.   Další informace najdete v tématu [Azure AD Connect Sync v2 Endpoint API (Public Preview)](how-to-connect-sync-endpoint-api-v2.md).

## <a name="user-writeback"></a>Zpětný zápis uživatele
> [!IMPORTANT]
> Funkce pro náhled zpětného zápisu uživatelů byla v aktualizaci srpna 2015 pro Azure AD Connect odebrána. Pokud jste ho povolili, měli byste tuto funkci zakázat.
>
>

## <a name="next-steps"></a>Další kroky
Pokračujte v [vlastní instalaci Azure AD Connect](how-to-connect-install-custom.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
