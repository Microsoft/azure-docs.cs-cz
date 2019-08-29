---
title: Jak prozkoumat rizika v Azure Active Directory Identity Protection (Aktualizováno) | Microsoft Docs
description: Naučte se prozkoumat rizikové uživatele, detekce a přihlášení v Azure Active Directory Identity Protection (aktualizované).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e9715b63ccfbf1da3bdaedf947ea4cd65109b7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129715"
---
# <a name="how-to-investigate-risk"></a>Jak: Prozkoumat riziko

Pomocí sestav rizikových přihlášení, rizikových uživatelů a detekcí rizik můžete prozkoumat a získat přehled o rizikech ve vašem prostředí. Díky možnosti filtrovat a seřadit rizikové přihlašování, uživatele a detekce můžete lépe pochopit potenciální průnik ve vaší organizaci. 

## <a name="risky-users-report"></a>Sestava o rizikových uživatelích

Pomocí informací uvedených v sestavě rizikové uživatele můžete najít odpovědi na otázky, jako například:

- Kteří uživatelé mají vysoké riziko?
- Kteří uživatelé mají stav rizika opraveno?

První vstupní bod do této sestavy je oddíl **prozkoumat** na stránce zabezpečení.

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/01.png)

Sestava rizikové uživatele má výchozí zobrazení, které zobrazuje:

- Name
- Stav rizika
- Úroveň rizika
- Podrobnosti o riziku
- Poslední aktualizace rizika
- type
- Stav

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/03.png)

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/04.png)

Dialog sloupce umožňuje zobrazit další pole nebo odebrat pole, která jsou již zobrazena.

Kliknutím na položku v zobrazení seznamu k ní zobrazíte všechny dostupné podrobnosti ve vodorovném zobrazení.

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/05.png)

Zobrazení podrobností ukazuje:

- Základní informace
- Poslední riziková přihlášení
- Detekce rizik, která nejsou propojená s přihlášením
- Historie rizika

Kromě toho můžete:

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/08.png)

- Zobrazením všech zástupců přihlášení zobrazíte sestavu přihlášení pro daného uživatele.
- Zobrazit všechna riziková přihlášení k zobrazení všech přihlášení pro daného uživatele, která byla označena jako riziková
- Resetujte heslo uživatele, pokud se domníváte, že došlo k ohrožení identity uživatele.
- Pokud si myslíte, že aktivní detekce rizik uživatele je falešně pozitivní, odsuňte riziko uživatele. Další informace najdete v článku věnovaném [zpětné vazbě na detekci rizik v Azure AD Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-users"></a>Filtrovat rizikové uživatele

Chcete-li zúžit uvedená data na úroveň, která vám vyhovuje, můžete filtrovat data rizikových uživatelů pomocí následujících výchozích polí:

- Name
- Uživatelské jméno
- Stav rizika
- Úroveň rizika
- type
- Stav

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/06.png)

Filtr **názvů** vám umožní zadat jméno nebo hlavní název uživatele (UPN) uživatele, o kterého se zajímáte.

Filtr **stavu rizika** umožňuje vybrat:

- Ohrožené
- Opravené
- Zamítnout

Filtr **úrovně rizika** umožňuje vybrat:

- Vysoká
- Střední
- Nízká

Filtr **typu** umožňuje vybrat:

- Host
- Člen

Filtr **stavu** umožňuje vybrat:

- Odstraněné
- Aktivní

### <a name="download-risky-users-data"></a>Stáhnout data o rizikových uživatelích

Data rizikových uživatelů si můžete stáhnout, pokud chcete s ní pracovat mimo Azure Portal. Kliknutím na Stáhnout se vytvoří soubor CSV s nejnovějšími záznamy 2 500. 

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/07.png)

Zobrazení seznamu můžete přizpůsobit kliknutím na sloupce na panelu nástrojů.
 
To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.
 
Pokud se chcete dozvědět víc o rizikovém uživateli, rozbalte ho kliknutím na zásuvku podrobností.

## <a name="risky-sign-ins-report"></a>Sestava rizikových přihlášení

Na základě informací poskytnutých sestavou rizikových přihlášení můžete najít odpovědi na otázky, jako jsou:

- Kolik úspěšných přihlášení bylo v minulém týdnu pro detekci rizik anonymních IP adres?
- Kteří uživatelé byli v posledním měsíci napadeni ohroženi?
- Kteří uživatelé měli na portálu Office 365 rizikové přihlášení?

První vstupní bod do této sestavy je oddíl **prozkoumat** na stránce zabezpečení.

![Sestava rizikových přihlášení](./media/howto-investigate-risky-users-signins/02.png)

Sestava rizikových přihlášení má výchozí zobrazení, které zobrazuje:

- Date
- Uživatel
- Aplikace
- Stav přihlášení
- Stav rizika
- Úroveň rizika (agregace)
- Úroveň rizika (v reálném čase)
- Podmíněný přístup
- Vyžaduje se MFA  

![Sestava rizikových přihlášení](./media/howto-investigate-risky-users-signins/09.png)

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/11.png)

Dialog sloupce umožňuje zobrazit další pole nebo odebrat pole, která jsou již zobrazena.

Kliknutím na položku v zobrazení seznamu k ní zobrazíte všechny dostupné podrobnosti ve vodorovném zobrazení.

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/12.png)

Zobrazení podrobností ukazuje:

- Základní informace
- Informace o zařízení
- Informace o riziku
- Informace o MFA
- Podmíněný přístup

Kromě toho můžete:

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/13.png)

- Potvrdit napadení 
- Potvrdit bezpečnost

Další informace najdete v článku věnovaném [zpětné vazbě na detekci rizik v Azure AD Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-sign-ins"></a>Filtrování rizikových přihlášení

Chcete-li zúžit uvedená data na úroveň, která vám vyhovuje, můžete filtrovat data rizikových uživatelů pomocí následujících výchozích polí:

- Uživatel
- Aplikace
- Stav přihlášení
- Stav rizika
- Úroveň rizika (agregace)
- Úroveň rizika (v reálném čase)
- Podmíněný přístup
- Date
- Typ úrovně rizika

![Sestava rizikových přihlášení](./media/howto-investigate-risky-users-signins/14.png)

Filtr **názvů** vám umožní zadat jméno nebo hlavní název uživatele (UPN) uživatele, o kterého se zajímáte.

Filtr **aplikace** umožňuje určit cloudovou aplikaci, se kterou se uživatel pokusil získat přístup.

Filtr **Stav přihlášení** umožňuje vybrat jednu z následujících možností:

- Vše
- Úspěch
- Chyba

Filtr **stavu rizika** umožňuje vybrat:

- Ohrožené
- Potvrzená napadení
- Potvrzené bezpečí
- Zamítnout
- Opravené

Filtr **úrovně rizika (agregovaný)** umožňuje vybrat:

- Vysoká
- Střední
- Nízká

Filtr **úrovně rizika (v reálném čase)** umožňuje vybrat:

- Vysoká
- Střední
- Nízká

Filtr **podmíněného přístupu** umožňuje vybrat:

- Vše
- Nepoužito
- Úspěch
- Chyba

Filtr **Datum** umožňuje definovat časový rámec pro vracená data.
Možné hodnoty jsou:

- Poslední 1 měsíc
- Posledních 7 dní
- Posledních 24 hodin
- Vlastní časový interval

### <a name="download-risky-sign-ins-data"></a>Stáhnout data o rizikových přihlášeních

Data rizikových přihlášení si můžete stáhnout, pokud chcete s ní pracovat mimo Azure Portal. Kliknutím na Stáhnout se vytvoří soubor CSV s nejnovějšími záznamy 2 500. 

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/15.png)


## <a name="risk-detections-report"></a>Sestava detekce rizik

Sestava zjišťování rizik vám poskytne přehled o všech detekcích rizik ochrany identity v tenantovi.


Sestava zjišťování rizik má výchozí zobrazení, které zobrazuje:

- Date

- Uživatel

- IP adresa

- Location

- Typ detekce

- Stav rizika

- Úroveň rizika

- ID požadavku
 

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů. Dialog sloupce umožňuje zobrazit další pole nebo odebrat pole, která jsou již zobrazena.

Kliknutím na položku v zobrazení seznamu k ní zobrazíte všechny dostupné podrobnosti ve vodorovném zobrazení.


V zobrazení podrobností se zobrazí další informace o detekci rizik.

Zobrazení podrobností také obsahuje odkazy na

- Zobrazit sestavu rizika uživatele
- Zobrazit přihlášení uživatele
- Zobrazení rizikových přihlášení uživatele
- Zobrazit propojené rizikové přihlášení
- Zobrazit zjišťování rizik uživatele


### <a name="filter-risk-detections"></a>Filtrovat detekci rizik

Chcete-li zúžit uvedená data na úroveň, která vám vyhovuje, můžete filtrovat data detekce rizik pomocí následujících výchozích polí:

- Čas detekce
- Typ detekce
- Stav rizika
- Úroveň rizika
- Uživatel
- Uživatelské jméno
- IP adresa
- Location
- Časování detekce
- Aktivita 
- Source
- Typ vystavitele tokenu
- ID požadavku
- ID korelace


Filtr **doby detekce** vám umožňuje definovat časový rámec pro vracená data. Tento filtr vám umožní vybrat:
- Posledních 90 dní
- Posledních 30 dní
- Posledních 7 dní
- Posledních 24 hodin
- Vlastní časový interval

Filtr **typu detekce** umožňuje zadat typ detekce rizika (například neznámé vlastnosti přihlášení).

Filtr **stavu rizika** umožňuje vybrat:

- Ohrožené
- Potvrzená napadení
- Potvrzené bezpečí
- Zamítnout
- Opravené

Filtr **úrovně rizika** umožňuje vybrat:

- Vysoká
- Střední
- Nízká

Filtr **uživatelů** umožňuje zadat jméno nebo ID objektu uživatele, o kterého se zajímáte.

Filtr **uživatelského jména** umožňuje zadat hlavní název uživatele (UPN) uživatele, o kterého se zajímáte.

Filtr **časování detekce** umožňuje určit, zda bylo zjišťování v reálném čase nebo offline. Poznámka: Můžete vyhlašovat přihlášení pomocí zjišťování v reálném čase pomocí zásad rizik přihlašování. 

Filtr **aktivity** umožňuje určit, zda bylo zjištěno propojení s přihlášením (například anonymní IP adresa) nebo uživatelem (např. nevrácená pověření).

**Zdrojový** filtr umožňuje zadat zdroj zjišťování rizik (například Azure AD nebo Microsoft Cloud App Security). 

Filtr **typu** vystavitele tokenu vám umožní určit, kam se token vystavil (například Azure AD nebo AD Federation Services).


### <a name="download-risk-detections-data"></a>Stáhnout data o detekci rizik

Data detekce rizik si můžete stáhnout, pokud chcete s ní pracovat mimo Azure Portal. Kliknutím na Stáhnout se vytvoří soubor CSV s nejnovějšími záznamy 5 000. 

## <a name="next-steps"></a>Další kroky

Přehled Azure AD Identity Protection najdete v tématu [Azure AD Identity Protection Overview](overview-v2.md).
