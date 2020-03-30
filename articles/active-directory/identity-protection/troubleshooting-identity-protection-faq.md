---
title: Časté otázky pro ochranu identit ve službě Azure Active Directory
description: Nejčastější dotazy Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443578"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Nejčastější dotazy K ochraně identity ve službě Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Zrušení známých problémů s uživatelským rizikem

**Odmítnout riziko uživatele** v klasické identity protection nastaví objekt actor v historii rizik uživatele v identity protection azure **ad**.

**Zavřít riziko uživatele** v identity protection nastaví objekt u uživatele v historii rizik uživatele v identity protection na ** \<jméno správce s hypertextovým odkazem směřujícím do okna\>uživatele**.

Je aktuální známý problém způsobuje latence v toku rizika uživatele propuštění. Pokud máte "Zásady rizika uživatele", tato zásada se přestane uplatňovat na propuštěné uživatele během několika minut po kliknutí na "Odmítnout riziko uživatele". Existují však známá zpoždění s uživatelským uživatelským uživatelským uživatelským, který obnovuje "rizikový stav" propuštěných uživatelů. Jako řešení aktualizujte stránku na úrovni prohlížeče, abyste viděli nejnovějšího uživatele "Stav rizika".

## <a name="risky-users-report-known-issues"></a>Rizikoví uživatelé hlásí známé problémy

Dotazy na pole **uživatelského jména** rozlišují malá a velká písmena, zatímco dotazy v poli **Název** jsou bez ohledu na případ.

Přepínání **Zobrazit data jako** skryje sloupec POSLEDNÍ AKTUALIZACE **RIZIKA.** Chcete-li si sloupec přečíst, klikněte na **sloupce** v horní části okna Rizikových uživatelů.

**Zavřít všechny události** v klasické identity protection nastaví stav detekce rizik **a uzavřenou (vyřešeno).**

## <a name="risky-sign-ins-report-known-issues"></a>Rizikové přihlášení hlásí známé problémy

**Vyřešit** na detekci rizik nastaví stav **uživatelé prošli vícefaktorové řízení založené na zásadách založených na rizicích**.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-is-a-user-is-at-risk"></a>Proč je uživatel ohrožen?

Pokud jste zákazníkazure ad identity protection, přejděte na [zobrazení rizikových uživatelů](howto-identity-protection-investigate-risk.md#risky-users) a klikněte na ohroženého uživatele. V zásuvce v dolní části se zobrazí karta Historie rizik a zobrazí se všechny události, které vedly ke změně rizika uživatele. Chcete-li zobrazit všechna riziková přihlášení pro uživatele, klikněte na "Riskantní přihlášení uživatele". Chcete-li zobrazit všechny detekce rizik pro tohoto uživatele, klikněte na "Detekce rizik uživatele".

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Jak mohu získat zprávu o detekci určitého typu?

Přejděte do zobrazení detekce rizik a filtrujte podle typu zjišťování. Tuto sestavu si pak můžete stáhnout v aplikaci . CSV nebo . formátu JSON pomocí tlačítka **Stáhnout** v horní části. Další informace naleznete v článku [Jak: Prozkoumat riziko](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Proč nemohu nastavit vlastní úrovně rizika pro každou detekci rizik?

Úrovně rizika v ochraně identity jsou založeny na přesnosti detekce a jsou poháněny naším dohlíženým strojovým učením. Chcete-li přizpůsobit, jaké prostředí uživatelé jsou prezentovány, správce může zahrnout nebo vyloučit určité uživatele / skupiny z uživatelského rizika a přihlášení rizikové zásady.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Proč umístění přihlášení neodpovídá místu, odkud se uživatel skutečně přihlásil?

Mapování geolokace IP je výzvou pro celé odvětví. Pokud máte pocit, že umístění uvedené v sestavě přihlášení neodpovídá skutečné poloze, ozvěte se podpoře společnosti Microsoft. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Jak lze uzavřít detekce specifických rizik, jako jsem to udělal ve starém ui?

Můžete poskytnout zpětnou vazbu k detekci rizik potvrzením propojeného přihlášení jako ohroženého nebo bezpečného. Zpětná vazba uvedená na přihlášení se stéká na všechny detekce provedené na tomto přihlášení. Pokud chcete zavřít zjišťování, které nejsou propojeny s přihlášením, můžete poskytnout tuto zpětnou vazbu na úrovni uživatele. Další informace najdete v článku [How to: Give risk feedback in Azure AD Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Jak daleko se mohu vrátit v čase, abych pochopil, co se děje s mým uživatelem?

- [Zobrazení rizikových uživatelů](howto-identity-protection-investigate-risk.md#risky-users) zobrazuje stav rizika uživatele na základě všech minulých přihlášení. 
- [Zobrazení rizikového přihlášení](howto-identity-protection-investigate-risk.md#risky-sign-ins) zobrazuje rizikové příznaky za posledních 30 dní. 
- Zobrazení [detekce rizik](howto-identity-protection-investigate-risk.md#risk-detections) ukazuje detekci rizik provedenou za posledních 90 dní.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Jak se mohu dozvědět více o konkrétní detekci?

Všechny detekce rizik jsou popsány v článku [Co je riziko](concept-identity-protection-risks.md#risk-types-and-detection). Můžete najet na symbol (i) vedle detekce na webu Azure Portal další informace o zjišťování.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Jak fungují mechanismy zpětné vazby v ochraně identity?

**Potvrdit ohrožena** (při přihlášení) – informuje Azure AD Identity Protection, že přihlášení nebylo provedeno vlastníkem identity a označuje kompromis.

- Po obdržení této zpětné vazby přesuneme stav rizika přihlášení a uživatele na **Potvrzeno ohroženo** a úroveň rizika na **vysokou**.

- Kromě toho poskytujeme informace našim systémům strojového učení pro budoucí zlepšení v hodnocení rizik.

    > [!NOTE]
    > Pokud je uživatel již opraven, neklikejte na **Potvrdit ohrožení zabezpečení,** protože přesune stav rizika přihlášení a uživatele na **Potvrzeno ohroženo** a úroveň rizika na **Vysoká**.

**Potvrďte trezor** (při přihlášení) – informuje Azure AD Identity Protection, že přihlášení bylo provedeno vlastníkem identity a neznamená kompromis.

- Po obdržení této zpětné vazby přesuneme stav rizika přihlášení (nikoli uživatele) **-** do seznamu Potvrzeno **bezpečné** a úroveň rizika na .

- Kromě toho poskytujeme informace našim systémům strojového učení pro budoucí zlepšení v hodnocení rizik.

    > [!NOTE]
    > Pokud se domníváte, že uživatel není ohrožen, použijte **Odmítnout riziko uživatele** na úrovni uživatele namísto použití **Potvrzeno bezpečné** na úrovni přihlášení. A **Odmítnout uživatelské riziko** na úrovni uživatele zavře riziko uživatele a všechny minulé rizikové přihlášení a detekce rizik.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Proč se uživateli s nízkým (nebo vyšším) skóre rizika zobrazuje, i když se v ochraně identity nezobrazují žádná riziková přihlášení ani detekce rizik?

Vzhledem k tomu, že riziko uživatele má kumulativní povahu a nevyprší jeho platnost, může mít uživatel riziko nízké nebo vyšší, i když v ochraně identity nejsou zobrazena žádná riziková přihlášení nebo detekce rizik. K této situaci může dojít, pokud se pouze škodlivá aktivita na uživatele došlo mimo časový rámec, pro který ukládáme podrobnosti o rizikové přihlášení a detekce rizik. Nevyprší riziko pro uživatele, protože je známo, že špatní herci zůstávají v prostředí zákazníků více než 140 dní za narušenou identitou před zvýšením jejich útoku. Zákazníci si mohou prohlédnout časovou osu rizika uživatele a pochopit, proč je uživatel ohrožen, a to takto:`Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Proč má přihlášení skóre "riziko přihlášení (agregace)" vysoké, pokud jsou detekce s ním spojené nízké nebo střední riziko?

Skóre vysokého celkového rizika může být založeno na dalších funkcích přihlášení nebo na skutečnosti, že pro toto přihlášení bylo aktivováno více než jedna detekce. A naopak přihlášení může mít riziko přihlášení (agregace) Medium i v případě, že detekce spojené s přihlášením jsou vysoké riziko. 
