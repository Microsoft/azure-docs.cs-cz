---
title: 'Azure AD Connect: Funkce ve verzi Preview | Dokumenty společnosti Microsoft'
description: Toto téma popisuje podrobněji funkce, které jsou ve verzi Preview v Azure AD Connect.
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
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7def733a80aea1be77825bb9069217f5f43e003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261278"
---
# <a name="more-details-about-features-in-preview"></a>Další podrobnosti o funkcích ve verzi Preview
Toto téma popisuje, jak používat funkce aktuálně ve verzi Preview.

## <a name="group-writeback"></a>Zpětný zápis skupin
Možnost zpětného zápisu skupiny ve volitelných funkcích umožňuje odepsat **skupiny Office 365** do doménové struktury s nainstalovaným Exchangem. Jedná se o skupinu, která je vždy zvládnutá v cloudu. Pokud máte Exchange místně, pak můžete odepsat tyto skupiny do místního, aby uživatelé s místní poštovní schránkou Exchange můžete odesílat a přijímat e-maily z těchto skupin.

Další informace o skupinách Office 365 a jejich použití najdete [zde](https://aka.ms/O365g).

Skupina Office 365 je reprezentována jako distribuční skupina v místním ad ds. Místní Exchange server musí být na Exchange 2013 kumulativní aktualizace 8 (vydáno v březnu 2015) nebo Exchange 2016 rozpoznat tento nový typ skupiny.

**Poznámky během náhledu**

* Atribut adresáře není aktuálně vyplněn v náhledu. Bez tohoto atributu není skupina viditelná v globálním seznamu adres. Nejjednodušší způsob, jak naplnit tento atribut, je použít `update-recipient`rutinu prostředí Exchange PowerShell .
* Platné cíle pro skupiny jsou pouze doménové struktury se schématem serveru Exchange. Pokud nebyla zjištěna žádná výměna, není možné povolit zpětný zápis skupiny.
* V současné době jsou podporována pouze nasazení organizace Exchange s jednou doménovou struktuře. Pokud máte více než jednu organizaci Exchange v místním prostředí, budete potřebovat místní řešení GALSync pro tyto skupiny se zobrazí v jiných doménových strukturách.
* Funkce zpětného zápisu skupiny nezpracovává skupiny zabezpečení ani distribuční skupiny.

> [!NOTE]
> Předplatné Azure AD Premium je vyžadováno pro skupinový zpětný zápis.
> 
>

## <a name="user-writeback"></a>Zpětný zápis uživatele
> [!IMPORTANT]
> Funkce náhledu zpětného zápisu uživatele byla odebrána v aktualizaci azure aslužby Azure AD Connect v srpnu 2015. Pokud jste ji povolili, měli byste tuto funkci zakázat.
>
>

## <a name="next-steps"></a>Další kroky
Pokračujte v [vlastní instalaci služby Azure AD Connect](how-to-connect-install-custom.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
