---
title: Nejčastější dotazy a známé problémy se službou Identity Protection (aktualizované) v Azure Active Directory | Microsoft Docs
description: Nejčastější dotazy a známé problémy se službou Identity Protection (aktualizované) v Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe7125174129752e6d6dbe0e00d01d4f32755333
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126097"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Nejčastější dotazy a známé problémy se službou Identity Protection (aktualizované) v Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Zavřít známé problémy s rizikem uživatele

**Zavřít riziko uživatele** v rámci klasické Identity Protection nastaví objekt actor v historii rizik uživatele v části Identita Protection (Aktualizováno) do **Azure AD**.

**Zavřít riziko uživatele** v aplikaci Identity Protection (Aktualizováno) nastaví objekt actor v historii rizik uživatele v aplikaci Identity Protection (Aktualizováno) na  **\<jméno správce pomocí hypertextového odkazu, který odkazuje na okno\>uživatele**.

Existuje aktuální známý problém, který způsobuje latence v toku odchodu rizika uživatele. Pokud máte zásady rizik uživatelů, tato zásada se přestane používat pro uživatele v řádu minut po kliknutí na Zavřít riziko uživatele. Existují však známé prodlevy při aktualizaci uživatelského prostředí pro neúspěšné uživatele. Alternativním řešením je aktualizovat stránku na úrovni prohlížeče, aby se zobrazil nejnovější stav "rizika" uživatele.

## <a name="risky-users-report-known-issues"></a>Rizikové uživatele hlásí známé problémy

Dotaz na pole **username** rozlišuje velká a malá písmena, zatímco dotazy v poli **název** jsou Case-nezávislá.

Přepnutí **zobrazení dat jako** SKRYJE sloupec **Poslední aktualizace rizika** . Pro čtení sloupce klikněte v horní části okna rizikové uživatele na **sloupce** .

**Zavřít všechny události** v klasickém zabezpečení identity nastaví stav detekce rizik na **Uzavřeno (vyřešeno)** .

## <a name="risky-sign-ins-report-known-issues"></a>Zpráva o rizikových přihlášeních sestav známých problémů

**Řešení** při detekci rizik nastaví stav na **uživatele, kteří prošli MFA řízenými zásadami na základě rizika**.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Proč nejde nastavit vlastní úrovně rizika pro každé zjišťování rizik?

Úrovně rizika v ochraně identity jsou založené na přesnosti detekce a využívají se v našem strojovém učení. Pokud chcete přizpůsobit, jaké zkušenosti uživatelé prezentují, může správce zahrnout nebo vyloučit určité uživatele nebo skupiny z rizika uživatele a rizikových zásad přihlašování.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Proč se umístění přihlášení neshoduje s tím, kde se uživatel skutečně přihlásil?

Mapování geografického umístění IP je v celém oboru. Pokud se domníváte, že umístění uvedené v sestavě přihlášení se neshoduje se skutečným umístěním, obraťte se na podporu. 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Jak funguje mechanismus zpětné vazby v ochraně identit?

**Potvrďte ohrožení** (při přihlášení) – informuje Azure AD Identity Protection, že se přihlášení neuskutečnil vlastník identity a označuje ohrožení zabezpečení.

- Po přijetí této zpětné vazby přesuneme stav přihlášení a rizika uživatele na **potvrzené ohrožení** a úroveň rizika na **vysokou**.

- Kromě toho poskytujeme informace pro naše systémy strojového učení pro budoucí vylepšení posuzování rizik.

    > [!NOTE]
    > Pokud je uživatel už opravený, neklepejte na **Potvrdit napadení** , protože přesouvá stav přihlášení a rizika uživatele na potvrzené napadení a úroveň rizika na **Vysoká**.

**Potvrzení zabezpečení** (při přihlášení) – informuje Azure AD Identity Protection, že se přihlášení provádělo vlastníkem identity a neindikuje ohrožení.

- Po přijetí této zpětné vazby přesuneme nerizikový stav přihlášení (nikoli uživatele) na **potvrzené zabezpečení** a úroveň rizika pro **-** .

- Kromě toho poskytujeme informace pro naše systémy strojového učení pro budoucí vylepšení posuzování rizik.

    > [!NOTE]
    > Pokud se domníváte, že uživatel není ohrožen, použijte na úrovni uživatele **Zrušit riziko pro uživatele** , místo použití **potvrzení** na úrovni přihlášení na úrovni zabezpečení. Zrušením **rizika uživatele** na úrovni uživatele se zavřou riziko uživatele a veškerá dřívější riziková přihlášení a detekce rizik.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Proč se mi zobrazuje uživatel s nízkým (nebo výše) rizikovým skórem, i když se v rámci Identity Protection nezobrazuje žádné rizikové přihlašování nebo zjišťování rizik?

Vzhledem k kumulativnímu riziku uživatele je souhrnná povaha a nevyprší platnost, uživatel může mít riziko nízké nebo vyšší, a to i v případě, že nejsou k dispozici žádná nedávný riziková přihlášení nebo detekce rizik zobrazená v nástroji Identity Protection. K tomu může dojít v případě, že dojde k překročení časového rámce, pro který uživatel ukládá podrobnosti o rizikových přihlášeních a detekcích rizik. Nevypršení rizika uživatele nekončí, protože v prostředí zákazníků byly známy chybné aktéry před 140 dny za napadenou identitou před tím, než dojde k jejich útokům. Zákazníci si můžou prohlédnout časovou osu uživatelů a pochopit, proč hrozí riziko uživatele tím, že se vrátí:`Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Proč má přihlášení "riziko při přihlašování" (agregované) "vysoké skóre", pokud jsou k němu přidružená zjištění nízká nebo střední rizikovost?

Vysoké agregované riziko by mohlo být založené na dalších funkcích přihlášení nebo na skutečnost, že pro toto přihlášení bylo vyvoláno více zjišťování. A naopak, přihlášení může mít riziko přihlašování (agregované) na střední úrovni, i když zjišťování spojená s přihlašováním jsou vysoké riziko. 
