---
title: Prozkoumat Azure Active Directory Identity Protection rizika
description: Naučte se prozkoumat rizikové uživatele, detekce a přihlášení v Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: be72c2152bdb8e1155d2dd29547f93ba3605d462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95251089"
---
# <a name="how-to-investigate-risk"></a>Postup: Šetření rizik

Identity Protection poskytuje organizacím se třemi sestavami, které mohou používat k prozkoumání rizik identity ve svém prostředí. Tyto sestavy jsou **rizikové uživatele**, **rizikové přihlášení** a **detekce rizik**. Zkoumání událostí je klíč pro lepší porozumění a identifikaci slabých bodů ve vaší strategii zabezpečení.

Všechny tři sestavy umožňují stažení událostí v nástroji. Formát CSV pro další analýzu mimo Azure Portal. Sestavy rizikových uživatelů a rizikových přihlášení umožňují stahovat nejnovější položky 2500, zatímco sestava detekce rizik umožňuje stáhnout nejnovější záznamy 5000.

Organizace můžou využít výhod Microsoft Graph integrace rozhraní API k agregaci dat s jinými zdroji, ke kterým můžou mít přístup jako organizace.

Tři sestavy se nacházejí v zabezpečení **Azure Portal**  >  **Azure Active Directory**  >  .

## <a name="navigating-the-reports"></a>Navigace v sestavách

Každá sestava se spustí se seznamem všech zjišťování pro období zobrazené v horní části sestavy. Každá sestava umožňuje přidání nebo odebrání sloupců na základě předvolby správce. Správci si mohou stáhnout data v nástroji. Sdílený svazek clusteru nebo. Formát JSON. Sestavy lze filtrovat pomocí filtrů v horní části sestavy.

Výběr jednotlivých položek může v horní části sestavy povolit další položky, jako je například možnost potvrdit přihlašování jako napadené nebo bezpečné, potvrdit uživatele jako napadený nebo odstranit riziko uživatele.

Výběr jednotlivých položek rozbalí okno podrobností pod detekci. Zobrazení podrobností umožňuje správcům prozkoumat a provádět akce při každém zjišťování. 

![Ukázková sestava Identity Protection ukazující rizikové přihlašovací údaje a podrobnosti](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Rizikoví uživatelé

Pomocí informací uvedených v sestavě rizikové uživatele můžou správci najít:

- Kteří uživatelé jsou ohroženi, došlo k nápravě rizik nebo byly vyrušeny riziko?
- Podrobnosti o detekcích
- Historie všech rizikových přihlášení
- Historie rizik
 
Správci se pak mohou rozhodnout, že budou provádět akce s těmito událostmi. Správci se můžou rozhodnout:

- Resetování hesla uživatele
- Potvrdit ohrožení uživatele
- Zavřít riziko uživatele
- Zablokovat uživatelům přihlášení
- Prozkoumat dál pomocí Azure ATP

## <a name="risky-sign-ins"></a>Riziková přihlášení

Sestava rizikových přihlášení obsahuje data, která lze filtrovat až v posledních 30 dnech (1 měsíc).

Na základě informací poskytnutých sestavou rizikových přihlášení můžou správci najít:

- Která označení jsou klasifikována jako ohrožená, potvrzená ohrožení, potvrzená v bezpečí, zrušena nebo opravena.
- Úrovně rizik v reálném čase a agregované úrovně rizika spojené s pokusy o přihlášení.
- Aktivované typy detekce
- Aplikovány zásady podmíněného přístupu
- Podrobnosti MFA
- Informace o zařízení
- Informace o aplikaci
- Informace o poloze

Správci se pak mohou rozhodnout, že budou provádět akce s těmito událostmi. Správci se můžou rozhodnout:

- Potvrdit ohrožení při přihlašování
- Potvrzení bezpečného přihlášení

> [!NOTE] 
> Identity Protection vyhodnocuje riziko pro všechny toky ověřování, bez ohledu na to, jestli je interaktivní nebo neinteraktivní. Sestava přihlášení ale zobrazuje jenom interaktivní přihlášení. Můžou se zobrazit riziková přihlášení, ke kterým došlo v neinteraktivním přihlášení, ale přihlášení se nezobrazí v sestavě přihlášení Azure AD.

## <a name="risk-detections"></a>Detekce rizik

Sestava zjišťování rizik obsahuje data, která lze filtrovat až do posledních 90 dní (3 měsíce).

Pomocí informací poskytnutých sestavou zjišťování rizik můžou správci najít:

- Informace o každém zjišťování rizik včetně typu.
- Další rizika aktivované ve stejnou dobu
- Umístění pokusu o přihlášení
- Odkaz na Další informace z Microsoft Cloud App Security (MCAS).

Správci se pak mohou rozhodnout, že se vrátí do sestavy rizika nebo přihlášení uživatele, aby mohli provádět akce založené na shromažďovaných informacích.

> [!NOTE] 
> Náš systém může zjistit, že riziková událost, která přispěla k rizikovým skóre rizikového uživatele, byla falešně pozitivní nebo riziko uživatele opravené pomocí vynucení zásad, jako je například dokončení výzvy MFA nebo zabezpečená Změna hesla. Proto náš systém zruší stav rizika a zobrazí se podrobnosti o riziku "AI potvrzující bezpečnost", takže už nebude přispívat k riziku uživatele. 


## <a name="next-steps"></a>Další kroky

- [Dostupné zásady pro zmírnění rizik](concept-identity-protection-policies.md)

- [Povolit přihlašování a zásady rizik uživatelů](howto-identity-protection-configure-risk-policies.md)
