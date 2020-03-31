---
title: Prozkoumání rizika Azure Active Directory Identity Protection
description: Zjistěte, jak prozkoumat rizikové uživatele, detekce a přihlášení v Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253478"
---
# <a name="how-to-investigate-risk"></a>Postup: Prošetření rizika

Ochrana identity poskytuje organizacím tři sestavy, které mohou použít k prozkoumání rizik identity ve svém prostředí. Tyto zprávy jsou **riziková uživatelé**, **riskantní přihlášení**a **detekce rizik**. Vyšetřování událostí je klíčem k lepšímu pochopení a identifikaci slabých míst ve vaší bezpečnostní strategii.

Všechny tři sestavy umožňují stahování událostí v aplikaci . Formát CSV pro další analýzu mimo portál Azure. Rizikové uživatele a zprávy o rizikových přihlášeních umožňují stažení nejnovějších 2500 položek, zatímco sestava detekce rizik umožňuje stažení nejnovějších 5000 záznamů.

Organizace mohou využít integrace rozhraní Microsoft Graph API k agregaci dat s jinými zdroji, ke kterým mohou mít přístup jako organizace.

Tři sestavy se nacházejí na **webu Azure Portal** > **Azure Active Directory** > **Security**.

## <a name="navigating-the-reports"></a>Navigace v sestavách

Každá sestava se spustí se seznamem všech zjišťování pro období uvedené v horní části sestavy. Každá sestava umožňuje přidání nebo odebrání sloupců na základě předvoleb správce. Správci se mohou rozhodnout stáhnout data v aplikaci . CSV nebo . Formát JSON. Sestavy lze filtrovat pomocí filtrů v horní části sestavy.

Výběr jednotlivých položek může umožnit další položky v horní části sestavy, jako je například možnost potvrdit přihlášení jako ohrožené nebo bezpečné, potvrdit uživatele jako ohrožené nebo zrušit riziko uživatele.

Výběrem jednotlivých položek se rozbalí okno podrobností pod zjišťováním. Zobrazení podrobností umožňuje správcům prozkoumat a provádět akce při každém zjišťování. 

![Příklad sestavy ochrany identity zobrazující riskantní přihlášení a podrobnosti](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Rizikoví uživatelé

S informacemi poskytnutými zprávami rizikových uživatelů mohou správci najít:

- Kteří uživatelé jsou ohroženi, riskovali nápravu nebo byli propuštěni?
- Podrobnosti o detekcích
- Historie všech rizikových přihlášení
- Historie rizik
 
Správci se pak mohou rozhodnout, že u těchto událostí proberou akci. Správci se mohou rozhodnout:

- Resetování uživatelského hesla
- Potvrdit ohrožení zabezpečení uživatele
- Odmítnutí rizika uživatele
- Blokovat přihlášení uživatele
- Další zkoumání pomocí ochrany ATP v oblasti ochrany před službou Azure

## <a name="risky-sign-ins"></a>Riziková přihlášení

Sestava rizikových přihlášení obsahuje filtrovatelná data za posledních 30 dní (1 měsíc).

S informacemi poskytnutými v přehledu rizikových přihlášení mohou správci najít:

- Které přihlášení jsou klasifikovány jako ohrožené, potvrzené jako ohrožené, potvrzené bezpečné, zamítnuté nebo opravené.
- Úrovně rizika v reálném čase a souhrnné úrovně rizika spojené s pokusy o přihlášení.
- Spuštěné typy zjišťování
- Použité zásady podmíněného přístupu
- Podrobnosti vícefaktorové hospo-
- Informace o zařízení
- Informace o aplikaci
- Informace o poloze

Správci se pak mohou rozhodnout, že u těchto událostí proberou akci. Správci se mohou rozhodnout:

- Potvrzení kompromisu přihlášení
- Potvrzení bezpečného přihlášení

## <a name="risk-detections"></a>Detekce rizik

Sestava detekce rizik obsahuje filtrovatelná data za posledních 90 dní (3 měsíce).

S informacemi poskytnutými ve zprávě o detekci rizik mohou správci najít:

- Informace o každé detekci rizik včetně typu.
- Další rizika vyvolaná současně
- Umístění pokusu o přihlášení
- Odkaz na další podrobnosti z Microsoft Cloud App Security (MCAS).

Správci se pak mohou vrátit ke zprávě o riziku nebo přihlášení uživatele, aby mohli provést akce založené na shromážděných informacích.

## <a name="next-steps"></a>Další kroky

- [Dostupné zásady ke zmírnění rizik](concept-identity-protection-policies.md)

- [Povolení zásad přihlášení a rizik uživatelů](howto-identity-protection-configure-risk-policies.md)
