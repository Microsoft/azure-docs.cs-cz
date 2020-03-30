---
title: Problém s ukládáním přihlašovacích údajů správce při konfiguraci aplikace Galerie Azure AD
description: Jak řešit běžné problémy, kterým čelí při konfiguraci zřizování uživatelů k aplikaci, která je již uvedena v Galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ff80224037001e41daf49cd6fc21439b2cc5cff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522862"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Při konfiguraci zřizování uživatelů do aplikace Azure Active Directory Gallery došlo k potížím s ukládáním přihlašovacích údajů správce 

Při použití portálu Azure ke konfiguraci [automatického zřizování uživatelů](user-provisioning.md) pro podnikovou aplikaci se může narazíte na situaci, kdy:

* **Pověření správce zadaná** pro aplikaci jsou platná a tlačítko **Testovat připojení** funguje. Pověření však nelze uložit a portál Azure vrátí obecnou chybovou zprávu.

Pokud saml založené jednotné přihlašování je také nakonfigurován pro stejnou aplikaci, nejpravděpodobnější příčinou chyby je, že Azure AD interní, na základě limitu úložiště pro certifikáty a pověření byla překročena.

Azure AD má aktuálně maximální kapacitu úložiště 1024 bajtů pro všechny certifikáty, tajné tokeny, přihlašovací údaje a související konfigurační data spojená s jedinou instancí aplikace (označované také jako záznam hlavního uživatele služby ve službě Azure AD).

Při konfiguraci jednotného přihlašování založenéna SAML je zde uložen certifikát použitý k podepisování tokenů SAML a často spotřebovává více než 50 % % místa.

Všechny tajné tokeny, identifikátory URI, e-mailové adresy oznámení, uživatelská jména a hesla, které se zadávají během instalace zřizování uživatelů, mohou způsobit překročení limitu úložiště.

## <a name="how-to-work-around-this-issue"></a>Jak tento problém vyřešit 

Existují dva možné způsoby, jak tento problém vyřešit dnes:

1. **Použijte dvě instance aplikace galerie, jednu pro jednotné přihlašování a jednu pro zřizování uživatelů** – [Jako](../saas-apps/linkedinelevate-tutorial.md) příklad můžete přidat LinkedIn Elevate z galerie a nakonfigurovat ji pro jednotné přihlašování. Pro zřizování přidejte další instanci LinkedIn Elevate z galerie aplikací Azure AD a pojmenujte ji "LinkedIn Elevate (Zřizování)." Pro tuto druhou instanci nakonfigurujte [zřizování](../saas-apps/linkedinelevate-provisioning-tutorial.md), ale ne jednotné přihlašování. Při použití tohoto zástupu je třeba [přiřadit](../manage-apps/assign-user-or-group-access-portal.md) stejné uživatele a skupiny oběma aplikacím. 

2. **Snižte množství uložených konfiguračních dat** – všechna data [zadaná](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) v části Pověření správce na kartě zřizování jsou uložena na stejném místě jako certifikát SAML. I když nemusí být možné zkrátit délku všech těchto dat, některá volitelná konfigurační pole, jako je **e-mail s oznámením,** mohou být odebrána.

## <a name="next-steps"></a>Další kroky
[Konfigurace zřizování uživatelů a zrušení zřizování aplikací SaaS](user-provisioning.md)
