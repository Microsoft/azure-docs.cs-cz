---
title: Návrh hybridní identity – požadavky služby Multi-Factor Authentication na Azure | Microsoft Docs
description: Díky řízení podmíněného přístupu Azure AD ověřuje konkrétní podmínky, které vyberete při ověřování uživatele, a před povolením přístupu k aplikaci.
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
ms.openlocfilehash: 7d8ddf372e234bab242e4b28ba53dce7dd68cc89
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90976053"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků služby Multi-Factor Authentication pro vaše řešení hybridní identity
V tomto světě mobility s uživateli, kteří přistupují k datům a aplikacím v cloudu a z libovolného zařízení, se zabezpečení těchto informací stává prvořadým.  Každý den je k dispozici nový titulek týkající se porušení zabezpečení.  I když neexistuje žádná záruka na taková porušení, Multi-Factor Authentication poskytuje další úroveň zabezpečení, která jim může zabránit v těchto porušeních.
Začněte vyhodnocením požadavků organizace na službu Multi-Factor Authentication. To znamená, co je organizace, která se pokouší zabezpečit.  Toto vyhodnocení je důležité pro definování technických požadavků pro nastavení a povolení uživatelů organizace pro službu Multi-Factor Authentication.

Nezapomeňte odpovědět na následující:

* Snaží se vaše společnost zabezpečit aplikace Microsoftu? 
* Jak se tyto aplikace publikují?
* Poskytuje vaše společnost vzdálený přístup, aby si zaměstnanci mohli získat přístup k místním aplikacím?

Pokud ano, jaký typ vzdáleného přístupu? Je také nutné vyhodnotit, kde se nacházejí uživatelé, kteří k těmto aplikacím přistupují. Toto vyhodnocení je dalším důležitým krokem k definování správné strategie Multi-Factor Authentication. Nezapomeňte odpovědět na následující otázky:

* Kde se uživatelé nacházejí?
* Je možné je umístit kdekoli?
* Má vaše společnost stanovit omezení podle umístění uživatele?

Jakmile tyto požadavky porozumíte, je důležité také vyhodnotit požadavky uživatele na službu Multi-Factor Authentication. Toto vyhodnocení je důležité, protože definuje požadavky na zavedení služby Multi-Factor Authentication. Nezapomeňte odpovědět na následující otázky:

* Jsou uživatelé obeznámeni se službou Multi-Factor Authentication?
* Bude nutné, aby bylo k dispozici několik použití pro další ověřování?  
  * Pokud ano, pokaždé, když přichází z externích sítí, nebo k nim přistupují konkrétní aplikace nebo za jiných podmínek?
* Budou uživatelé potřebovat školení o tom, jak nastavit a implementovat službu Multi-Factor Authentication?
* Jaké jsou klíčové scénáře, které vaše společnost potřebuje k povolení služby Multi-Factor Authentication pro své uživatele?

Po zodpovězení předchozích otázek budete mít přehled o tom, jestli je už v místním prostředí implementované ověřování Multi-Factor Authentication. Toto vyhodnocení je důležité pro definování technických požadavků pro nastavení a povolení uživatelů organizace pro službu Multi-Factor Authentication. Nezapomeňte odpovědět na následující otázky:

* Potřebuje vaše společnost chránit privilegované účty pomocí MFA?
* Potřebuje vaše společnost povolit MFA pro určité aplikace z důvodu dodržování předpisů?
* Potřebuje vaše společnost povolit MFA pro všechny oprávněné uživatele této aplikace nebo jenom správce?
* Potřebujete mít MFA vždycky povolený nebo jenom v případě, že jsou uživatelé přihlášení mimo vaši firemní síť?

## <a name="next-steps"></a>Další kroky
[Definice strategie přijetí hybridní identity](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)

