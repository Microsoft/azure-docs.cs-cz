---
title: Nejčastější dotazy k Identity Protection v Azure Active Directory
description: Nejčastější dotazy Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 10/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42b1c0ef42454b085c5d4dbcdc14162b2c829c6a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074810"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Nejčastější dotazy – ochrana identity v Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Zavřít známé problémy s rizikem uživatele

Zrušení **rizika uživatele** v rámci klasické Identity Protection nastaví objekt actor v historii rizik uživatele v části Identity Protection na **službu Azure AD** .

Zrušení **rizika uživatele** v ochraně identity nastaví objekt actor v historii rizik uživatele v ochraně identity na **\<Admin’s name with a hyperlink pointing to user’s blade\>** .

Existuje aktuální známý problém, který způsobil latenci v toku neúspěšných rizik uživatele. Pokud máte zásady rizik uživatelů, tato zásada se přestane používat pro uživatele v řádu minut po kliknutí na Zavřít riziko uživatele. Existují však známé prodlevy při aktualizaci uživatelského prostředí pro neúspěšné uživatele. Alternativním řešením je aktualizovat stránku na úrovni prohlížeče, aby se zobrazil nejnovější stav "rizika" uživatele.

## <a name="risky-users-report-known-issues"></a>Rizikové uživatele hlásí známé problémy

Dotaz na pole **username** rozlišuje velká a malá písmena, zatímco dotazy v poli **název** jsou Case-nezávislá.

Přepnutí **zobrazení dat jako** SKRYJE sloupec **Poslední aktualizace rizika** . Pro čtení sloupce klikněte v horní části okna rizikové uživatele na **sloupce** .

**Zavřít všechny události** v klasickém zabezpečení identity nastaví stav detekce rizik na **Uzavřeno (vyřešeno)** .


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-is-a-user-is-at-risk"></a>Proč je uživatel ohrožen?

Pokud jste zákazník Azure AD Identity Protection, přejděte do zobrazení [rizikové uživatelé](howto-identity-protection-investigate-risk.md#risky-users) a klikněte na uživatele s riziky. V zásuvce v dolní části se na kartě Historie rizik zobrazí všechny události, které vedly ke změně rizika uživatele. Pokud chcete zobrazit všechna riziková přihlášení pro uživatele, klikněte na rizikové přihlašování uživatele. Chcete-li zobrazit všechna zjištění rizik pro tohoto uživatele, klikněte na možnost detekce rizik uživatele.

## <a name="why-was-my-sign-in-blocked-but-identity-protection-didnt-generate-a-risk-detection"></a>Proč bylo moje přihlášení blokované, ale ochrana identity negenerovala detekci rizika?
Přihlášení mohou být blokována z několika důvodů. Je důležité si uvědomit, že ochrana identity generuje jenom detekci rizik, pokud se v žádosti o ověření používají správné přihlašovací údaje. Pokud uživatel používá nesprávné přihlašovací údaje, nebude označen ochranou identity, protože nehrozí riziko ohrožení zabezpečení přihlašovacích údajů, pokud nesprávný objekt actor nepoužívá správné přihlašovací údaje. Některé důvody, proč může být uživatel zablokován z podepisování, které negeneruje detekci Identity Protection, zahrnují:
* **IP adresa může být zablokovaná** z důvodu škodlivé aktivity z IP adresy. Zpráva blokovaná protokolem IP nerozlišuje, zda byly pověření správné nebo nikoli. Pokud je IP adresa blokovaná a správné přihlašovací údaje se nepoužijí, vygeneruje se detekce Identity Protection.
* **[Inteligentní uzamčení](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)** může účtu zabránit v přihlášení po několika neúspěšných pokusech.
* Je možné vyhovět **zásadám podmíněného přístupu** , které používají jiné podmínky než úroveň rizika k blokování žádosti o ověření.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Jak získám sestavu zjištění určitého typu?

Přejít na zobrazení detekce rizik a filtrovat podle typu detekce. Tuto sestavu pak můžete stáhnout v. Sdílený svazek clusteru nebo. Formátu JSON pomocí tlačítka **Stáhnout** v horní části. Další informace najdete v článku [Postupy: šetření rizik](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Proč nejde nastavit vlastní úrovně rizika pro každé zjišťování rizik?

Úrovně rizika v ochraně identity jsou založené na přesnosti detekce a využívají se v našem strojovém učení. Aby bylo možné přizpůsobit možnosti uživatelům, může správce zahrnout nebo vyloučit určité uživatele nebo skupiny z rizik uživatelů a rizikových zásad Sign-In.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Proč se umístění přihlášení neshoduje s tím, kde se uživatel skutečně přihlásil?

Mapování geografického umístění IP je v celém oboru. Pokud se domníváte, že umístění uvedené v sestavě přihlášení se neshoduje se skutečným umístěním, můžete se obrátit na podporu Microsoftu. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Jak můžu uzavřít konkrétní detekci rizik, jako jsem ve starém uživatelském rozhraní?

Zpětnou vazbu na detekci rizik můžete získat potvrzením propojeného přihlášení jako ohroženého nebo bezpečného. Zpětná vazba uvedená v trickles přihlašování ke všem detekcím provedeným na přihlášení. Pokud chcete ukončit detekce, které nejsou propojené s přihlášením, můžete tuto zpětnou vazbu poskytnout na úrovni uživatele. Další informace najdete v článku [Postupy: poskytnutí zpětné vazby o riziku v Azure AD Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Jak daleko se můžu vrátit v čase, abyste pochopili, co se na svém uživateli zaměří?

- Zobrazení [rizikové uživatele](howto-identity-protection-investigate-risk.md#risky-users) zobrazuje riziko rizika uživatele na základě všech minulých přihlášení. 
- Zobrazení [rizikových přihlášení](howto-identity-protection-investigate-risk.md#risky-sign-ins) zobrazuje v posledních 30 dnech znaménka rizika. 
- Zobrazení [detekce rizik](howto-identity-protection-investigate-risk.md#risk-detections) zobrazuje zjišťování rizik provedených během posledních 90 dnů.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Jak se mohu dozvědět více o konkrétní detekci?

Všechna zjišťování rizik jsou popsána v článku [co je to riziko](concept-identity-protection-risks.md#risk-types-and-detection). Pokud se chcete dozvědět víc o detekci, můžete ukazatel myši umístit na symbol (i) vedle detekce na Azure Portal.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Jak funguje mechanismus zpětné vazby v ochraně identit?

**Potvrďte ohrožení zabezpečení** (při přihlášení) – informuje Azure AD Identity Protection, že přihlášení neudělal vlastník identity a označuje ohrožení zabezpečení.

- Po přijetí této zpětné vazby přesuneme stav přihlášení a rizika uživatele na **potvrzené ohrožení** a úroveň rizika na **vysokou** .

- Kromě toho poskytujeme informace pro naše systémy strojového učení pro budoucí vylepšení posuzování rizik.

    > [!NOTE]
    > Pokud je uživatel už opravený, neklepejte na **Potvrdit napadení** , protože přesouvá stav přihlášení a rizika uživatele na **potvrzené napadení** a úroveň rizika na **Vysoká** .

**Potvrzení zabezpečení** (při přihlášení) – informuje Azure AD Identity Protection, že se přihlásilo od vlastníka identity a neindikuje kompromis.

- Po přijetí této zpětné vazby přesuneme nerizikový stav přihlášení (nikoli uživatele) na **potvrzené zabezpečení** a úroveň rizika pro **-** .

- Kromě toho poskytujeme informace pro naše systémy strojového učení pro budoucí vylepšení posuzování rizik.

    > [!NOTE]
    > Pokud se domníváte, že uživatel není ohrožen, použijte na úrovni uživatele **Zrušit riziko pro uživatele** , místo použití **potvrzení** na úrovni přihlášení na úrovni zabezpečení. Zrušením **rizika uživatele** na úrovni uživatele se zavřou riziko uživatele a veškerá dřívější riziková přihlášení a detekce rizik.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Proč se mi zobrazuje uživatel s nízkým (nebo výše) rizikovým skórem, i když se v rámci Identity Protection nezobrazuje žádné rizikové přihlašování nebo zjišťování rizik?

Vzhledem k kumulativnímu riziku uživatele je souhrnná povaha a nevyprší platnost, uživatel může mít riziko nízké nebo vyšší, a to i v případě, že nejsou k dispozici žádná nedávný riziková přihlášení nebo detekce rizik zobrazená v nástroji Identity Protection. K této situaci může dojít, pokud uživatel nastavil jedinou škodlivou aktivitu uživatele nad rámec, pro který ukládáme podrobné údaje o rizikových přihlášeních a detekcích rizik. Nevypršení rizika uživatele nekončí, protože v prostředí zákazníků byly známy chybné aktéry před 140 dny za napadenou identitou před tím, než dojde k jejich útokům. Zákazníci si můžou prohlédnout časovou osu uživatelů a pochopit, proč hrozí riziko uživatele tím, že se vrátí: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Proč má přihlášení "riziko při přihlašování" (agregované) "vysoké skóre", pokud jsou k němu přidružená zjištění nízká nebo střední rizikovost?

Vysoké agregované riziko by mohlo být založené na dalších funkcích přihlášení nebo na skutečnost, že pro toto přihlášení bylo vyvoláno více zjišťování. A naopak, přihlášení může mít riziko přihlašování (agregované) na střední úrovni, i když zjišťování spojená s přihlašováním jsou vysoké riziko. 

### <a name="why-is-the-detection-which-is-linked-to-a-risky-sign-in-have-a-different-risk-level-than-the-sign-in-risk-level-real-time"></a>Proč je detekce, která je propojená s rizikovým přihlašováním, odlišná na úrovni rizika přihlašování (v reálném čase)? 

Nedávno jsme provedli vylepšení způsobu, jakým se počítá riziko přihlašování v reálném čase. Výsledkem těchto změn je neshoda zjištěná mezi úrovní zjišťování rizik a úrovní rizika přihlašování. Všimněte si, že riziko přihlášení v reálném čase je hodnota, která se používá při vynucení zásad. 
