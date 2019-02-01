---
title: 'Azure AD Connect: Funkce ve verzi preview | Dokumentace Microsoftu'
description: Toto téma popisuje v části Další podrobnosti o funkce, které jsou ve verzi preview ve službě Azure AD Connect.
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
ms.openlocfilehash: afbd48b8793e8b16597efb1e3996058b7450e03e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497090"
---
# <a name="more-details-about-features-in-preview"></a>Další podrobnosti o funkce ve verzi preview
Toto téma popisuje, jak používat funkce aktuálně ve verzi preview.

## <a name="group-writeback"></a>Zpětný zápis skupin
Možnost pro zpětný zápis skupin v volitelné funkce vám umožní zpětný zápis **skupiny Office 365** do doménové struktury se serverem Exchange nainstalovaný. Toto je skupina, která je vždy spravovaná v cloudu. Pokud máte v místním systému Exchange, můžete napsat zpět tyto skupiny do místního uživatele s poštovní schránku v místním Exchangi můžete odesílat a přijímat e-mailů z těchto skupin.

Další informace o skupinách Office 365 a jejich použití najdete [tady](https://aka.ms/O365g).

Skupiny Office 365 je vyjádřena jako distribuční skupinu v místní službě AD DS. Místní Exchange serveru musí být na serveru Exchange 2013 kumulativní aktualizaci 8 (vydané spolu. března 2015) nebo Exchange 2016 rozpoznat tento nový typ skupiny.

**Poznámky k verzi preview**

* Atribut knihy adresy není naplněn aktuálně ve verzi preview. Bez tohoto atributu skupiny není zobrazená v globálním seznamu adres. Nejjednodušší způsob, jak vyplnit tento atribut je použít rutinu Powershellu v Exchangi `update-recipient`.
* Platné cíle pro skupiny jsou pouze doménové struktury se schématem systému Exchange. Pokud byla zjištěna žádná Exchange, pak zpětný zápis skupin není možné povolit.
* Aktuálně jsou podporovány pouze jednou doménovou strukturou nasazení organizaci Exchange. Pokud máte více než jedna organizace v místním systému Exchange, potřebujete místní GALSync řešení pro tyto skupiny se zobrazí v vaše jiných doménových strukturách.
* Funkce zpětný zápis skupin nezpracovává skupiny zabezpečení nebo distribuční skupiny.

> [!NOTE]
> Předplatné služby Azure AD Premium je vyžadován pro zpětný zápis skupin.
> 
>

## <a name="user-writeback"></a>Zpětný zápis uživatelů
> [!IMPORTANT]
> Funkce ve verzi preview zpětný zápis uživatelů byla odebrána v aktualizace ze srpna 2015 na Azure AD Connect. Pokud povolíte, měli byste zakázat tuto funkci.
>
>

## <a name="next-steps"></a>Další postup
Pokračovat vaše [vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
