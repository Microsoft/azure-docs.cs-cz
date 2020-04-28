---
title: Problém při ukládání přihlašovacích údajů správce konfigurace aplikace Galerie Azure AD
description: Řešení běžných problémů při konfiguraci zřizování uživatelů pro aplikaci, která je už uvedená v galerii aplikací Azure AD
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522862"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Při ukládání přihlašovacích údajů správce při konfiguraci zřizování uživatelů pro aplikaci Galerie Azure Active Directory došlo k potížím. 

Při použití Azure Portal ke konfiguraci [automatického zřizování uživatelů](user-provisioning.md) pro podnikovou aplikaci, může dojít k situaci, kdy:

* **Přihlašovací údaje správce** zadané pro aplikaci jsou platné a tlačítko **Test připojení** funguje. Pověření však nelze uložit a Azure Portal vrátí obecnou chybovou zprávu.

Pokud je pro stejnou aplikaci nakonfigurované jednotné přihlašování založené na SAML, je nejpravděpodobnější příčinou této chyby, že interní limit úložiště pro jednotlivé aplikace pro certifikáty a přihlašovací údaje se překročil.

Azure AD má v současné době maximální kapacitu úložiště 1024 bajtů pro všechny certifikáty, tajné tokeny, přihlašovací údaje a související konfigurační data přidružená k jedné instanci aplikace (označované také jako záznam instančního objektu ve službě Azure AD).

Pokud je nakonfigurováno jednotné přihlašování založené na SAML, je zde uložen certifikát použitý k podepsání tokenů SAML a často spotřebovává více než 50% procent místa.

U všech tajných tokenů, identifikátorů URI, e-mailových adres oznámení, uživatelských jmen a hesel, které se zadaly během nastavení zřizování uživatelů, může dojít k překročení limitu úložiště.

## <a name="how-to-work-around-this-issue"></a>Jak tento problém obejít 

Existují dva možné způsoby, jak tento problém vyřešit Dnes:

1. **Použijte dvě instance aplikace Galerie, jednu pro jednotné přihlašování a jednu pro zřizování uživatelů** – pořídit [linkedinový](../saas-apps/linkedinelevate-tutorial.md) přístup k aplikaci Galerie jako příklad můžete přidat zvýšení úrovně LinkedInu z galerie a nakonfigurovat ho pro jednotné přihlašování. V případě zřizování přidejte další instanci LinkedInu z Galerie aplikací Azure AD a pojmenujte ji "LinkedIn – zvýšení (zřizování)". Pro tuto druhou instanci nakonfigurujte [zřizování](../saas-apps/linkedinelevate-provisioning-tutorial.md), ale ne jednotné přihlašování. Při použití tohoto alternativního řešení je potřeba [přiřadit](../manage-apps/assign-user-or-group-access-portal.md) stejné uživatele a skupiny k oběma aplikacím. 

2. **Omezení množství uložených konfiguračních dat** – všechna data zadaná v části [přihlašovací údaje správce](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) na kartě zřizování se ukládají na stejné místo jako certifikát SAML. I když nemusí být možné zkrátit všechna tato data, některá volitelná konfigurační pole, jako je **e-mailová oznámení** , můžete odebrat.

## <a name="next-steps"></a>Další kroky
[Konfigurace zřizování uživatelů a zrušení zřizování pro aplikace SaaS](user-provisioning.md)
