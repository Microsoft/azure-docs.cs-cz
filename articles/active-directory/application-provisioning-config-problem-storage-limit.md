---
title: Problém ukládání přihlašovacích údajů správce při konfiguraci zřizování uživatelů k aplikaci Galerie Azure AD | Microsoft Docs
description: Postup řešení běžných problémů s potýkají při konfiguraci zřizování uživatelů na aplikaci již uveden v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 1146df364a08128b5cd191ed1120198ae31b763e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337790"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Chyba při ukládání přihlašovacích údajů správce při konfiguraci zřizování uživatelů k aplikaci Azure Active Directory Gallery 

Při použití portálu Azure ke konfiguraci [zřizování automatické uživatelů](active-directory-saas-app-provisioning.md) pro podniková aplikace, mohou nastat situace, kde:

* **Přihlašovací údaje správce** zadaných pro aplikace jsou platné a **Test připojení** tlačítko funguje. Ale přihlašovací údaje nelze uložit, a portálu Azure vrátí obecnou chybovou zprávu.

Pokud na základě SAML jednotné přihlašování je také nakonfigurovaný pro stejnou aplikaci, nejpravděpodobnější příčinou této chyby je limit úložiště interních, každou aplikaci této Azure AD pro certifikáty a pověření byla překročena.

Aktuálně Azure AD má maximální úložnou kapacitu jeden kB pro všechny certifikáty, tajný tokeny, přihlašovací údaje a související konfigurační data související s jednu instanci aplikace (také označované jako hlavní záznam služby ve službě Azure AD).

Pokud na základě SAML jednotné přihlašování je nakonfigurován, certifikát používaný k podepisování tokenů SAML je zde uloženy a často spotřebovává více než 50 procent % prostoru.

Všechny tajný tokeny, identifikátory URI, oznámení e-mailové adresy, uživatelská jména a hesla, které získat zadaný během nastavení zřizování uživatelů může způsobit překročení limitu úložiště.

## <a name="how-to-work-around-this-issue"></a>Jak chcete tento problém vyřešit 

Existují dva možné způsoby, jak tento problém obejít dnes:

1. **Použít dvě Galerie aplikace instancí, jeden pro jednotné přihlašování a jeden pro zřizování uživatelů** -trvá aplikace Galerie [zvýšení oprávnění LinkedIn](saas-apps/linkedinelevate-tutorial.md) jako příklad můžete přidat LinkedIn zvýšení oprávnění z galerie a nakonfigurovat je pro jednotné přihlašování. Pro zřizování, přidejte další instanci LinkedIn zvýšení oprávnění z galerii aplikací Azure AD a pojmenujte ji "LinkedIn zvýšení (zřizování)." Tato druhá instance konfigurace [zřizování](saas-apps/linkedinelevate-provisioning-tutorial.md), ale není jednotného přihlašování. Při použití tohoto řešení, stejné uživatele a skupiny, musí být [přiřazené](manage-apps/assign-user-or-group-access-portal.md) pro obě aplikace. 

2. **Snížení objemu uložených dat konfigurace** -všechna zadaná v data [přihlašovací údaje správce](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) oddíl kartě zřizování je uložený na stejném místě jako certifikát SAML. Nemusí být možné zkrátit délku všech těchto dat, některá pole volitelné konfigurace, jako **e-mailové oznámení** lze odebrat.

## <a name="next-steps"></a>Další postup
[Nakonfigurujte uživatele zřizování a jeho rušení pro aplikace SaaS](active-directory-saas-app-provisioning.md)
