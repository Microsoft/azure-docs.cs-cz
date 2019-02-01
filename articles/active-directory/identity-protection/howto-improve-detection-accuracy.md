---
title: Jak zlepšit přesnost detekce ve službě Azure Active Directory Identity Protection (Aktualizovat) | Dokumentace Microsoftu
description: Jak zlepšit přesnost detekce ve službě Azure Active Directory Identity Protection (Aktualizovat).
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: d2b477c31898884761f260e9520913c67c7c6f83
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488913"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Jak: Zlepšovat přesnost detekce 

Identity Protection poskytuje mechanismus poskytněte zpětnou vazbu ke službě Azure AD na rizika zjištěními ve vašem prostředí. Chcete-li poskytnout zpětnou vazbu, lze potvrdit tento stav zjištěné rizikový uživatel nebo události přihlášení. Uživatelé Microsoft tuto zpětnou vazbu reagovat na aktuální detekce rizik a zlepšili přesnost detekce budoucí. 


## <a name="what-is-detection"></a>Co je detekce?

Zjišťování je proces identifikace podezřelých aktivit ve spojení s vašimi uživatelskými účty. Podezřelé aktivity služby Azure AD může zjistit, se nazývají [riziková událost](../reports-monitoring/concept-risk-events.md). Proces zjišťování je založená na adaptivní algoritmy strojového učení a heuristik ke zjištění rizikové události pro uživatele.

Výsledky vyhledávání se používají k určení, zda jsou ohrožených uživatelů a přihlášení. 


## <a name="how-can-i-improve-the-detection-accuracy"></a>Jak můžete zlepšit přesnost detekce?

Protože zjišťování je automatizovaný proces je možné, že Azure AD hlásí počet falešně pozitivních výsledků. Zlepšovat přesnost detekce tím, že poskytuje zpětnou vazbu ke službě Azure AD, pokud jde o výsledky vyhledávání.

Existují tři způsoby, jak zlepšit přesnost detekce: potvrďte ohrožení zabezpečení přihlášení, comfirm bezpečného přihlášení a zavřít uživatelského rizika. Můžete tak učinit z následujících sestav:

- **Sestavy rizikových přihlášení –** v sestavě rizikových přihlášení můžete potvrďte, že přihlášení jsou bezpečné nebo ohrožení zabezpečení

- **Sestava rizikových uživatelů –** v sestavě rizikových uživatelů, můžete zavřít uživatelského rizika 

Vaše zpětná vazba je zpracován Azure AD, aby se zlepšila přesnost detekce výsledky. Obvykle můžete poskytnout zpětnou vazbu jako součást uživatelského rizika nebo šetření rizika přihlášení. Další informace najdete v tématu [zkoumání rizikoví uživatelé a přihlašování](howto-investigate-risky-users-signins.md).


## <a name="confirm-compromised"></a>Potvrdit ohrožení zabezpečení

Potvrzení události přihlášení, protože dojde k ohrožení bezpečnosti signalizuje do služby Azure AD, že přihlášení nebyl autorizován vlastníkem identity. Když si vyberete, "Potvrdit dojde k ohrožení bezpečnosti", bude Azure AD

- Zvýšení rizika uživatelů ovlivněných uživatelů na vysoká.

- Nápověda pro optimalizaci strojového učení, který zjistí rizikových událostí
 
- Proveďte další opatření ke zvýšení ochrany vaší organizace



Potvrďte ohrožení zabezpečení přihlášení:

- **Sestavy rizikových přihlášení** – tato možnost umožňuje, abyste potvrdili, že ohrožení zabezpečení přihlášení pro jeden nebo více události přihlášení.

    ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/07.png)

- **Zobrazení podrobností o sestavě rizikových přihlášení** – tato možnost povolí, budete vyzváni k potvrzení ohrožení bezpečnosti účtu pro vybrané přihlášení události v sestavě rizikových přihlášení. 

    ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>Potvrdit bezpečnost


Potvrzení události přihlášení jako bezpečné signály do služby Azure AD, která přihlášení **byl** oprávnění vlastníkem příslušné identity. Když si vyberete, "Potvrdit bezpečné", bude Azure AD:

- Vrátí podíl vybrané přihlášení rizika uživatele

- Zavřete základních rizikových událostí

- Nápověda pro optimalizaci strojového učení, který zjistí rizikových událostí

- Proveďte další opatření ke zvýšení ochrany vaší organizace
 

Potvrďte, že bezpečného přihlášení v:

- **Sestavy rizikových přihlášení** – tato možnost povolí, budete vyzváni k potvrzení bezpečné přihlašování k nejméně jedné události přihlášení.

    ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/08.png)

- **Zobrazení podrobností o sestavě rizikových přihlášení** – tato možnost povolí, budete vyzváni k potvrzení bezpečného přihlášení na vybrané události přihlášení v sestavě rizikových přihlášení. 

    ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>Skrýt riziko uživatele

Pokud už udělali nápravné akce pro riziko uživatele nebo přesvědčeni, že falešně byly označeny jako rizikovou, že můžete zavřít uživatelského rizika. Zavření rizika uživatele obnoví uživatele – rizikové stavu. Všechny minulé rizika a rizikových přihlášení se zruší události pro vybraného uživatele.


Můžete zavřít ohlášené uživatelského rizika v:

- **Sestava rizikových uživatelů** – tato možnost dovoluje zrušit uživatelského rizika pro jeden nebo více vybraných uživatelů.

    ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/02.png)

- **Zobrazení podrobností o** – tato možnost dovoluje zrušit riziko uživatele pro vybrané uživatele v sestavě rizika uživatele. 

    ![Skrýt riziko uživatele](./media/howto-improve-detection-accuracy/01.png)


**Co byste měli vědět:**

- Tuto akci nejde vrátit zpět.

- Může trvat několik minut, než tuto akci dokončit, což je důvod, proč by neměl znovu odeslat žádost o.

- Tuto akci lze provést pouze v případě AD spravuje přihlašovací údaje uživatele. 



## <a name="best-practices"></a>Osvědčené postupy

Zavření rizika uživatele je jedním ze způsobů je odblokování, pokud byla blokována zásady rizik uživatelů a nejde vlastní opravit kvůli nemají resetování hesla nebo povolené ověřování MFA. V takovém případě je nejvhodnější zajistit uživatele a zaregistruje se pro resetování hesla a vícefaktorové ověřování, takže budou moct sami napravit všechny budoucí rizikové události.


## <a name="next-steps"></a>Další postup

Získejte přehled o Azure AD Identity Protection, najdete v článku [Přehled služby Azure AD Identity Protection](overview-v2.md).


