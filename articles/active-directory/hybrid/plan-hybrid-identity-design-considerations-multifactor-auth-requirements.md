---
title: Hybridní identita návrh – požadavky na vícefaktorové ověřování Azure | Dokumentace Microsoftu
description: Pomocí řízení podmíněného přístupu Azure Active Directory ověří zvláštní podmínky, kterou vyberete při ověřování uživatele a před povolením přístupu k aplikaci. Po splnění těchto podmínek, uživatel ověří a povolí se přístup k aplikaci.
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
ms.openlocfilehash: 3dabb381c16aa107e41c1d556e61e020b8c6a6c3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170217"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Určení požadavků na ověření službou Multi-Factor Authentication pro vaše řešení hybridní identity
V tomto světě nastavení mobilních zařízení s uživateli přístup k datům a aplikacím v cloudu a z jakéhokoli zařízení a zabezpečení těchto informací se stal prvořadá.  Každý den se nový nadpis o porušení zabezpečení.  I když není zaručeno před takové porušením, ověřování službou Multi-Factor Authentication poskytuje další úroveň zabezpečení, které pomáhají zabránit těchto porušení.
Začněte tím, že vaše rozhodnutí vyzkoušet požadavky organizace na ověřování službou Multi-Factor Authentication. To znamená co je organizace pokoušíte zabezpečit.  Toto testování je důležité definovat technické požadavky pro nastavení a povolení uživatelé organizace pro ověřování službou Multi-Factor Authentication.

Ujistěte se, že odpovědět následující:

* Je vaší společnosti pokoušíte zabezpečit aplikace od Microsoftu? 
* Jak tyto aplikace jsou publikovány?
* Poskytuje společnosti vzdálený přístup k zaměstnancům přístup k místním aplikacím?

Pokud ano, jaký typ vzdáleného přístupu? Také musíte vyhodnotit, kde budou umístěné uživatele, kteří přistupují k těmto aplikacím. Toto hodnocení se jiný důležitým krokem k definování strategie pro správné ověřování službou Multi-Factor Authentication. Ujistěte se, že odpovědět na následující otázky:

* Pokud budou uživatelé budou umístěné?
* Můžou se být umístěná kdekoli?
* Vaše společnost chce vytvořit omezení podle umístění uživatele?

Jakmile pochopíte tyto požadavky je potřeba také měli zvážit požadavky na uživatele Multi-Factor Authentication. Toto testování je důležité, protože ho budou definovat požadavky na použití služby Multi-Factor authentication. Ujistěte se, že odpovědět na následující otázky:

* Jsou uživatelé obeznámeni s ověřováním Multi-Factor Authentication?
* Využijete, se bude vyžadovat další ověřování?  
  * Pokud ano, vždy, když pocházející z externí sítě nebo přístup k určité aplikace, nebo v jiných podmínkách?
* Uživatelé budou vyžadovat školení o tom, jak nastavit a zaveďte vícefaktorové ověřování?
* Jaké jsou klíčové scénáře, které chce vaše společnost povolení služby Multi-Factor authentication pro uživatele?

Po zvolení odpovědi na otázky předchozí, bude moci porozumět tomu při ověřování službou Multi-Factor Authentication již implementováno místní. Toto testování je důležité definovat technické požadavky pro nastavení a povolení uživatelé organizace pro ověřování službou Multi-Factor Authentication. Ujistěte se, že odpovědět na následující otázky:

* Potřebuje vaše společnost pro ochranu privilegovaných účtů s MFA?
* Potřebuje vaše společnost jak zapnout MFA pro určité aplikace kvůli dodržování předpisů?
* Potřebuje vaše společnost povolte MFA pro všichni oprávnění uživatelé tyto aplikace, nebo jenom správci?
* Třeba máte vždy povolená služba MFA nebo pouze při přihlášení uživatele mimo vaši firemní síť?

## <a name="next-steps"></a>Další postup
[Definování strategie přijetí hybridní identity](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Další informace najdete v tématech
[Přehled aspektů návrhu](plan-hybrid-identity-design-considerations-overview.md)

