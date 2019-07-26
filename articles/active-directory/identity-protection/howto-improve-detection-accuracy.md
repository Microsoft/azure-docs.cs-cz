---
title: Jak zlepšit přesnost detekce v Azure Active Directory Identity Protection (Aktualizováno) | Microsoft Docs
description: Jak zlepšit přesnost detekce v Azure Active Directory Identity Protection (Aktualizováno).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32bb8de7970fc167a6a95e9d9c3c71e4e1dc0150
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333953"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Jak: Zlepšit přesnost detekce 

Identity Protection poskytuje mechanizmy pro poskytování zpětné vazby do Azure AD při detekcích rizik ve vašem prostředí. Chcete-li poskytnout zpětnou vazbu, můžete potvrdit stav zjištěného rizikového uživatele nebo události přihlášení. Uživatelé Microsoftu tuto zpětnou vazbu provedou na základě současného zjišťování rizik a zlepšují přesnost budoucích zjišťování. 

## <a name="what-is-detection"></a>Co je detekce?

Zjišťování je proces identifikace podezřelých aktivit ve spojení s vašimi uživatelskými účty. Podezřelé aktivity, které Azure AD dokáže detekovat, se nazývají [rizikové události](../reports-monitoring/concept-risk-events.md). Proces zjišťování vychází z adaptivních algoritmů strojového učení a heuristiky k detekci rizikových událostí pro uživatele.

Výsledky detekce slouží k určení, zda jsou uživatelé a přihlášení ohroženi. 

## <a name="how-can-i-improve-the-detection-accuracy"></a>Jak můžu zlepšit přesnost detekce?

Vzhledem k tomu, že detekce je automatizovaný proces, je možné, že služba Azure AD hlásí falešně pozitivní výsledky. Přesnost detekce můžete zlepšit poskytnutím zpětné vazby do Azure AD ohledně výsledků zjišťování.

Existují tři způsoby, jak vylepšit přesnost detekce: potvrďte nahlášené přihlášení, potvrďte zabezpečené přihlášení a odsuňte riziko uživatele. Můžete to udělat z následujících sestav:

- **Sestava rizikových přihlášení –** V sestavě riziková přihlášení si můžete ověřit, že přihlášení jsou bezpečná nebo ohrožená.
- **Sestava rizikových uživatelů-** V sestavě rizikové uživatele můžete zrušit riziko uživatele. 

Vaše zpětná vazba se zpracovává službou Azure AD za účelem zlepšení přesnosti výsledků zjišťování. Vaše zpětná vazba se většinou poskytuje jako součást rizika uživatele nebo šetření rizik při přihlašování. Další informace najdete v tématu [jak prozkoumat rizikové uživatele a přihlášení](howto-investigate-risky-users-signins.md).

## <a name="confirm-compromised"></a>Potvrdit napadení

Potvrzení přihlašovací události jako ohrožených signálů do služby Azure AD, že přihlášení neautorizované vlastníkem identity Když vyberete potvrdit ohrožení, Azure AD bude

- Zvyšte riziko uživatele ovlivněného uživatelem na vysoké úrovni.
- Pomáhat s optimalizací strojového učení, které detekuje rizikové události
- Proveďte další opatření pro další ochranu vaší organizace.

Potvrzení napadeného přihlášení:

- **Sestava rizikových přihlášení** – Tato možnost umožňuje potvrdit napadené přihlášení pro jednu nebo více přihlašovacích událostí.

   ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/07.png)

- **Zobrazení podrobností sestavy rizikových přihlášení** – Tato možnost umožňuje potvrdit napadený účet pro vybranou událost přihlášení v sestavě rizikových přihlášení. 

   ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/04.png)
 
## <a name="confirm-safe"></a>Potvrdit bezpečnost

Potvrzení přihlašovací události jako bezpečných signálů službě Azure AD, že přihlášení schválil příslušný vlastník identity  . Když vyberete potvrdit zabezpečení, Azure AD bude:

- Vrátí příspěvek rizika uživatele pro vybraná přihlášení.
- Zavřít základní rizikové události
- Pomáhat s optimalizací strojového učení, které detekuje rizikové události
- Proveďte další opatření pro další ochranu vaší organizace.
 
Ověření bezpečného přihlášení v nástroji:

- **Sestava rizikových přihlášení** – Tato možnost umožňuje potvrdit bezpečné přihlášení pro jednu nebo více přihlašovacích událostí.

   ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/08.png)

- **Zobrazení podrobností sestavy rizikových přihlášení** – Tato možnost umožňuje potvrdit bezpečné přihlášení k vybrané události přihlášení v sestavě rizikových přihlášení. 

   ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/05.png)

## <a name="dismiss-user-risk"></a>Skrýt riziko uživatele

Pokud jste již provedli nápravné akce pro rizikové uživatele nebo se domníváte, že byly falešně označeny jako rizikové, můžete odstranit riziko uživatele. Po chybějícím riziku uživatele se uživatel vrátí do nerizikového stavu. Všechna poslední riziková přihlášení a rizikové události pro vybraného uživatele budou zrušena.

Hlášené riziko uživatele můžete zrušit v:

- **Sestava rizikové uživatele** – Tato možnost umožňuje přeskočit riziko uživatele pro jednoho nebo více vybraných uživatelů.

   ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/02.png)

- **Zobrazení podrobností** – Tato možnost umožňuje přeskočit riziko uživatele pro vybraného uživatele v sestavě rizik uživatele. 

   ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/01.png)

**Co byste měli znát:**

- Tuto akci nelze vrátit zpět.
- Dokončení této akce může trvat několik minut, což je důvod, proč byste svou žádost neměli znovu odeslat.
- Tuto akci můžete provést jenom v případě, že služba AD spravuje přihlašovací údaje uživatele. 

## <a name="best-practices"></a>Osvědčené postupy

Chybějící riziko uživatele je jedním ze způsobů, jak je odblokovat, pokud byly zablokovány zásadami rizika uživatele a nelze je opravit, protože není povoleno resetování hesla nebo ověřování MFA. V této situaci je nejvhodnější zajistit, aby uživatel potom registroval resetování hesla a MFA, aby mohl provádět samočinnou nápravu všech budoucích rizikových událostí.

## <a name="next-steps"></a>Další postup

Přehled Azure AD Identity Protection najdete v tématu [Azure AD Identity Protection Overview](overview-v2.md).
