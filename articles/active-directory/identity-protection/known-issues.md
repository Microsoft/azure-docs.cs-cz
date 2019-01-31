---
title: Nejčastější dotazy a známé problémy s identity protection (Aktualizovat) v Azure Active Directory | Dokumentace Microsoftu
description: Nejčastější dotazy a známé problémy s (Aktualizovat) v Azure Active Directory identity protection.
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
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 912326bb1e5420fd4c9051f5a71aa98e6cff80e5
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470378"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Nejčastější dotazy a známé problémy s (Aktualizovat) v Azure Active Directory identity protection


## <a name="dismiss-user-risk"></a>Skrýt riziko uživatele

**Zavřít uživatelského rizika** v modelu classic Identity Protection nastaví objekt actor v historii rizika uživatele ve službě Identity Protection (Aktualizovat) na **Azure AD**.


**Zavřít uživatelského rizika** v ochrana Identity (Aktualizovat) nastaví objekt actor v historii rizika uživatele ve službě Identity Protection (Aktualizovat) na **\<jména správce s hypertextovým odkazem přejdete na okno uživatele\>**.


## <a name="risky-users-report"></a>Sestava o rizikových uživatelích

Dotazy na **uživatelské jméno** pole jsou malá a velká písmena, při dotazech na **název** pole jsou nezávislá na případ.

Při přepínání **hierarchické zobrazení** skryje **poslední aktualizace rizika** sloupce. Chcete-li znovu přidat kliknutím na sloupec **sloupce** v horní části okna rizikový uživatel.

**Zavřít všechny události** v modelu classic Identity Protection nastaví stav rizikových událostí na **uzavřené (vyřešené)**.

Pokud se pokusíte o přístup k sestavě rizikových uživatelů kliknutím **Rizikoví uživatelé sestavy** záznamu přihlášení v sestavě rizikových přihlášení, mohou někdy zobrazit **došlo k chybě. Zkuste to prosím znovu**. Chcete-li to napravit, klikněte na tlačítko **použít** nebo **resetování** v horní části obrazovky a rizikových uživatelů se vyplní data.


## <a name="risky-sign-ins-report"></a>Sestava rizikových přihlášení

**Vyřešit** na riziko událostí, nastaví stav na **uživatele předaný MFA řízené zásadami na základě rizik**.

**Resetovat** v **rizikových přihlášení** sestavy nevymaže hodnotu **rizikových událostí typu**.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>Proč nelze nastavit vlastní úrovní rizik pro všechny rizikové události?

Úrovně rizik ve službě Identity Protection jsou založené na přesnost detekce a s využitím naší technik strojového učení. Chcete-li přizpůsobit, co uživatelé prostředí se zobrazí, Správce může zahrnout nebo vyloučit určité uživatele nebo skupiny z uživatelského rizika a zásady rizik přihlašování.


### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Proč umístění přihlásit se neshoduje s kde uživatel skutečně přihlásil z?

Informace o zeměpisné poloze mapování adresy IP, je globální výzva. Pokud se domníváte, že umístění uvedené v sestavě přihlášení neodpovídá skutečné umístění, kontaktujte prosím podporu. 


### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Jak fungují mechanismus zpětné vazby ve službě Identity Protection?

**Potvrďte dojde k ohrožení bezpečnosti** (na u přihlášení) – informuje Azure AD Identity Protection, která přihlášení nebyla prováděné identitu vlastníka a označuje ohrožení zabezpečení.

- Po přijetí této zpětné vazby, pustíme stav rizika přihlášení a uživatele pro **potvrzeno dojde k ohrožení bezpečnosti** a úroveň rizika **vysoké**.

- Kromě toho zajišťuje informace na našem strojového učení systémy pro budoucí vylepšení v posouzení rizik.

    > [!NOTE]
    > Pokud uživatel je již napravit, není klikněte na tlačítko **potvrzení dojde k ohrožení bezpečnosti** protože přesune stav rizika přihlášení a uživatele pro **potvrzeno dojde k ohrožení bezpečnosti** a úroveň rizika **vysoké**.

**Potvrďte bezpečné** (na u přihlášení) – informuje Azure AD Identity Protection, že přihlášení proběhlo vlastníkem identity a nenaznačuje, že ohrožení zabezpečení.

- Po přijetí této zpětné vazby, pustíme přihlášení (nikoli uživatele) rizika stavu **potvrzeno bezpečné** a úroveň rizika pro **-**.

- Kromě toho zajišťuje informace na našem strojového učení systémy pro budoucí vylepšení v posouzení rizik.

    > [!NOTE]
    > Pokud si myslíte, že nedošlo k ohrožení bezpečnosti uživatele, použijte **zavřít uživatelského rizika** na uživatelské úrovni namísto použití **potvrzeno bezpečné** na úrovni přihlášení. A **zavřít uživatelského rizika** na uživatele, zavře úroveň rizika uživatele a všechny za riziková přihlášení a rizikové události.



### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>Proč se zobrazila skóre rizika uživatele s nízkou úrovní (nebo vyšší), i v případě, že jsou zobrazeny žádné rizikových přihlášení nebo rizikových událostí ve službě Identity Protection?

Zadaný uživatel rizika je kumulativní ze své podstaty a, nemá prošlou platnost, uživatel může mít riziko uživatele s nízkými nebo nad i když neexistují žádné nedávné rizikových přihlášení nebo rizikových událostí podle Identity Protection. Může k tomu dojít, pokud došlo pouze škodlivé aktivity na uživatele nad rámec časový rámec, pro kterou můžeme ukládat podrobnosti o události rizika a rizikových přihlášení. Riziko uživatele jsme nevyprší platnost vzhledem k tomu, že bylo zjištěno útočníků, zůstat v prostředí zákazníků víc než 140 dní, za ohrožené identity než ramping nahoru útoku. Zákazníky můžete zkontrolovat časová osa rizika uživatele pochopit, proč je uživatel ohrožených tak, že přejdete na: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Proč u přihlášení má skóre "riziko přihlášení (agregace)" vysokou při zjištění s ním spojená se střední nebo nízké riziko?

Agregační vysoce rizikové skóre můžou vycházet další funkce přihlášení nebo skutečnost, že více než jeden detekce se aktivuje například pro tento přihlášení. A naopak, že přihlášení může mít riziko přihlášení (agregace) střední i v případě detekce přidružené k přihlášení je s vysokým rizikem. 
