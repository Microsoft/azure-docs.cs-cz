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
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7def733a80aea1be77825bb9069217f5f43e003
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376038"
---
# <a name="more-details-about-features-in-preview"></a>Další podrobnosti o funkcích ve verzi Preview
Toto téma popisuje, jak používat funkce, které jsou aktuálně ve verzi Preview.

## <a name="group-writeback"></a>Zpětný zápis skupin
Možnost pro zpětný zápis skupin v volitelných funkcích umožňuje zpětný zápis **skupin Office 365** do doménové struktury s nainstalovaným systémem Exchange. Toto je skupina, která je v cloudu vždycky spravovaná. Pokud máte místní Exchange, můžete tyto skupiny do místní schránky zapsat zpátky, aby uživatelé s místní poštovní schránkou Exchange mohli odesílat a přijímat e-maily z těchto skupin.

Další informace o skupinách Office 365 a jejich použití najdete [tady](https://aka.ms/O365g).

Skupina Office 365 se reprezentuje jako distribuční skupina v místních služba AD DS. Místní Exchange Server musí být na Exchangi 2013 kumulativní aktualizace 8 (vydané v březnu 2015) nebo Exchange 2016 pro rozpoznání tohoto nového typu skupiny.

**Poznámky během verze Preview**

* Atribut adresáře není aktuálně naplněný ve verzi Preview. Bez tohoto atributu není skupina viditelná v globálním seznamu. Nejjednodušší způsob, jak tento atribut naplnit, je použití rutiny prostředí Exchange PowerShell `update-recipient`.
* Platnými cíli pro skupiny jsou pouze doménové struktury se schématem Exchange. Pokud se nezjistil žádný Exchange, není možné povolit zpětný zápis skupin.
* V tuto chvíli se podporují jenom nasazení organizace Exchange s jednou doménovou strukturou. Pokud máte místní organizaci Exchange, budete potřebovat místní řešení GALSync, aby se tyto skupiny zobrazovaly v jiných doménových strukturách.
* Funkce zpětného zápisu skupiny nezpracovává skupiny zabezpečení nebo distribuční skupiny.

> [!NOTE]
> Pro zpětný zápis skupin se vyžaduje předplatné Azure AD Premium.
> 
>

## <a name="user-writeback"></a>Zpětný zápis uživatele
> [!IMPORTANT]
> Funkce pro náhled zpětného zápisu uživatelů byla v aktualizaci srpna 2015 pro Azure AD Connect odebrána. Pokud jste ho povolili, měli byste tuto funkci zakázat.
>
>

## <a name="next-steps"></a>Další kroky
Pokračujte v [vlastní instalaci Azure AD Connect](how-to-connect-install-custom.md).

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
