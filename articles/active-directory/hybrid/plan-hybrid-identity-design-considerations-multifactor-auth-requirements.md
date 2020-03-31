---
title: Návrh hybridní identity – vícefaktorové požadavky na ověřování Azure | Dokumenty společnosti Microsoft
description: Pomocí řízení podmíněného přístupu služba Azure Active Directory zkontroluje konkrétní podmínky, které vyberete při ověřování uživatele a před povolením přístupu k aplikaci. Jakmile jsou tyto podmínky splněny, uživatel je ověřen a povolen přístup k aplikaci.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4743195fc79d43571ec79a13b8518edc7e81379b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109296"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Určení vícefaktorových požadavků na ověřování pro řešení hybridní identity
V tomto světě mobility, kdy uživatelé přistupují k datům a aplikacím v cloudu a z jakéhokoli zařízení, se zabezpečení těchto informací stalo prvořadým.  Každý den se píše nový titulek o narušení bezpečnosti.  Ačkoli neexistuje žádná záruka proti takovému porušení, vícefaktorové ověřování, poskytuje další vrstvu zabezpečení, která pomáhá předcházet těmto porušením.
Začněte vyhodnocením požadavků organizací na vícefaktorové ověřování. To znamená, co je organizace se snaží zajistit.  Toto hodnocení je důležité definovat technické požadavky pro nastavení a povolení organizací uživatelům pro vícefaktorové ověřování.

Ujistěte se, že jste odpověděli na následující:

* Snaží se vaše společnost zabezpečit aplikace Microsoft? 
* Jak jsou tyto aplikace publikovány?
* Poskytuje vaše společnost vzdálený přístup, který zaměstnancům umožňuje přístup k místním aplikacím?

Pokud ano, jaký typ vzdáleného přístupu? Musíte také vyhodnotit, kde budou umístěni uživatelé, kteří přistupují k těmto aplikacím. Toto hodnocení je dalším důležitým krokem k definování správné strategie vícefaktorového ověřování. Ujistěte se, že jste odpověděli na následující otázky:

* Kde budou uživatelé umístěni?
* Mohou být umístěny kdekoliv?
* Chce vaše společnost stanovit omezení podle polohy uživatele?

Jakmile porozumíte těmto požadavkům, je důležité také vyhodnotit požadavky uživatele na vícefaktorové ověřování. Toto hodnocení je důležité, protože bude definovat požadavky na zavedení vícefaktorového ověřování. Ujistěte se, že jste odpověděli na následující otázky:

* Jsou uživatelé obeznámeni s vícefaktorovým ověřováním?
* Budou k poskytnutí dalšího ověřování vyžadována některá použití?  
  * Pokud ano, po celou dobu, když přichází z externích sítí, nebo přístup k určitým aplikacím, nebo za jiných podmínek?
* Budou uživatelé vyžadovat školení o tom, jak nastavit a implementovat vícefaktorové ověřování?
* Jaké jsou klíčové scénáře, které vaše společnost chce povolit vícefaktorové ověřování pro své uživatele?

Po zodpovězení předchozích otázek budete moci pochopit, zda již existují vícefaktorové ověřování v místním prostředí. Toto hodnocení je důležité definovat technické požadavky pro nastavení a povolení organizací uživatelům pro vícefaktorové ověřování. Ujistěte se, že jste odpověděli na následující otázky:

* Potřebuje vaše společnost chránit privilegované účty pomocí vícefaktorové pomoci?
* Potřebuje vaše společnost povolit vícefaktorové povolení pro určité aplikace z důvodů dodržování předpisů?
* Potřebuje vaše společnost povolit vícefaktorové povolení pro všechny oprávněné uživatele těchto aplikací nebo pouze pro správce?
* Potřebujete mít mfa vždy povoleno, nebo pouze v případě, že uživatelé jsou přihlášeni mimo vaši podnikovou síť?

## <a name="next-steps"></a>Další kroky
[Definování strategie přechodu hybridní identity](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)

