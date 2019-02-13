---
title: Zkoumání rizikových uživatelů a přihlášení ve službě Azure Active Directory identity protection (Aktualizovat) | Dokumentace Microsoftu
description: Zjistěte, jak prozkoumat rizikových uživatelů a přihlášení ve službě Azure Active Directory identity protection (Aktualizovat).
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.author: markvi
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.date: 01/25/2019
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 546d9f6771ea75c2601630850f4e9ef082fd5623
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210981"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>Jak: Prozkoumávání rizikových uživatelů a přihlášení 


Pomocí sestavy rizikových přihlášení a rizikový uživatel můžete prozkoumat a získat přehled o rizika ve vašem prostředí. Díky možnosti filtrování a řazení rizikových přihlášení a uživatelů můžete lépe pochopili potenciální neoprávněného vniknutí ve vaší organizaci. 


## <a name="risky-users-report"></a>Sestava o rizikových uživatelích

Pomocí informací uvedených v sestavě rizikových uživatelů můžete najít odpovědi na otázky jako:

- Kteří uživatelé jsou vysoce rizikových?
- Kteří uživatelé mají stav rizika opravit?



Váš první vstupní bod do této sestavy je **prošetření** části na stránce zabezpečení.

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/01.png)


Sestava rizikových uživatelů má výchozí zobrazení, který ukazuje:

- Název

- Stav rizika

- Úroveň rizika

- Podrobnosti o riziku

- Poslední aktualizace rizika

- Type

- Status
 

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/03.png)


Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/04.png)

Dialogové okno sloupce můžete zobrazit další pole nebo odeberte pole, která jsou už zobrazená.

Kliknutím na položku v zobrazení seznamu k ní zobrazíte všechny dostupné podrobnosti ve vodorovném zobrazení.

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/05.png)


Zobrazení podrobností ukazuje:

- Základní informace

- Poslední riziková přihlášení

- Rizikové události, které nesouvisejí s přihlášením

- Historie rizika



Kromě toho můžete:

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/08.png)

- Zobrazte všechny místní přihlášení k zobrazení sestavy přihlášení pro daného uživatele.

- Zobrazte všechny rizikových přihlášení k zobrazení všech přihlášení pro daného uživatele, které byly označeny jako rizikovou.

- Resetovat heslo uživatele, pokud se domníváte, že se k ohrožení identity uživatele.

- Riziko uživatele zrušit, pokud se domníváte, že aktivní rizikové události uživatele jsou počet falešně pozitivních výsledků. Další informace najdete v tématu [jak zlepšit přesnost detekce](howto-improve-detection-accuracy.md).



### <a name="filter-risky-users"></a>Filtrovat rizikoví uživatelé

Abyste omezili zobrazovaná data na úroveň, která vám vyhovuje, můžete filtrovat rizikový uživatel data s využitím následujících výchozích polí:

- Název

- Uživatelské jméno

- Stav rizika

- Úroveň rizika

- Type

- Status

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/06.png)



**Název** filtr umožňuje určit jméno nebo hlavní název uživatele (UPN) uživatele, které vás zajímají.


**Rizika stavu** filtr umožňuje vybrat:

- Ohrožené
- Opraveno
- Zamítnout


**Úroveň rizika** filtr umožňuje vybrat:

- Vysoký
- Střednědobé používání
- Nízká


**Typ** filtr umožňuje vybrat:

- Host
- Člen

**Stav** filtr umožňuje vybrat:

- Odstraněno
- Aktivní


### <a name="download-risky-users-data"></a>Stáhnout data rizikoví uživatelé

Pokud chcete pracovat s ním mimo na webu Azure portal si můžete stáhnout data rizikový uživatel. Kliknutím na stáhnout vytvoří soubor CSV posledních 5 tisíc záznamů. 

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/07.png)


V panelu nástrojů klikněte na sloupce můžete přizpůsobit zobrazení seznamu.
 
To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.
 
Další informace o rizikový uživatel, klikněte na panel podrobnosti a rozbalte ho

 



## <a name="risky-sign-ins-report"></a>Sestava rizikových přihlášení

Pomocí informací uvedených v sestavě rizikových přihlášení můžete najít odpovědi na otázky jako:

- Počet úspěšných přihlášeních tam byli, který měl anonymní IP adresy rizikové události za poslední týden?

- Uživatelé, které byly potvrzeny dojde k ohrožení bezpečnosti během posledního měsíce?

- Kteří uživatelé měli rizikových přihlášení na portál Office 365?




Váš první vstupní bod do této sestavy je **prošetření** části na stránce zabezpečení.

![Sestava rizikových přihlášení](./media/howto-investigate-risky-users-signins/02.png)

Sestavy rizikových přihlášení má výchozí zobrazení, které obsahuje:

- Datum

- Uživatel

- Aplikace

- Stav přihlášení

- Stav rizika

- Úroveň rizika (agregace)

- Úroveň rizika (v reálném čase)

- Podmíněný přístup

- Vyžaduje se MFA.  
 

![Sestava rizikových přihlášení](./media/howto-investigate-risky-users-signins/09.png)


Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/11.png)

Dialogové okno sloupce můžete zobrazit další pole nebo odeberte pole, která jsou už zobrazená.

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

- Potvrdit ohrožení zabezpečení 

- Potvrdit bezpečnost

Další informace najdete v tématu [jak zlepšit přesnost detekce](howto-improve-detection-accuracy.md).




### <a name="filter-risky-sign-ins"></a>Filtrovat riziková přihlášení

Abyste omezili zobrazovaná data na úroveň, která vám vyhovuje, můžete filtrovat rizikový uživatel data s využitím následujících výchozích polí:

- Uživatel
- Aplikace
- Stav přihlášení
- Stav rizika
- Úroveň rizika (agregace)
- Úroveň rizika (v reálném čase)
- Podmíněný přístup
- Datum
- Typ úrovně rizika

![Sestava rizikových přihlášení](./media/howto-investigate-risky-users-signins/14.png)



**Název** filtr umožňuje určit jméno nebo hlavní název uživatele (UPN) uživatele, které vás zajímají.

**Aplikace** filtr umožňuje určit cloudové aplikace, uživatel se pokusil o přístup.

Filtr **Stav přihlášení** umožňuje vybrat jednu z následujících možností:

- Vše
- Úspěch
- Selhání


**Rizika stavu** filtr umožňuje vybrat:

- Ohrožené
- Potvrzené ohrožení
- Potvrzené bezpečí
- Zamítnout
- Opraveno


**Úrovně (agregace) rizika** filtr umožňuje vybrat:

- Vysoký
- Střednědobé používání
- Nízká

**(V reálném čase) úroveň rizika** filtr umožňuje vybrat:

- Vysoký
- Střednědobé používání
- Nízká


**Podmíněného přístupu** filtr umožňuje vybrat:

- Vše
- Nevztahuje se.
- Úspěch
- Selhání


Filtr **Datum** umožňuje definovat časový rámec pro vracená data.
Možné hodnoty:

- Poslední 1 měsíc
- Posledních 7 dní
- Posledních 24 hodin
- Vlastní časový interval





### <a name="download-risky-sign-ins-data"></a>Stáhnout data riziková přihlášení

Pokud chcete pracovat s ním mimo na webu Azure portal si můžete stáhnout data rizikových přihlášení. Kliknutím na stáhnout vytvoří soubor CSV posledních 5 tisíc záznamů. 

![Sestava o rizikových uživatelích](./media/howto-investigate-risky-users-signins/15.png)



## <a name="next-steps"></a>Další postup

Získejte přehled o Azure AD Identity Protection, najdete v článku [Přehled služby Azure AD Identity Protection](overview-v2.md).
