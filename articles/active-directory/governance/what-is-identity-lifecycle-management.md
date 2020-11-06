---
title: Co je Správa životního cyklu identit pomocí Azure Active Directory? | Dokumentace Microsoftu
description: Popisuje Přehled správy životního cyklu identit.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d54e1127207f0593f8d345b266d7db6e92b5c0d
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335396"
---
# <a name="what-is-identity-lifecycle-management"></a>Co je správa životního cyklu identit?

Řízení identit pomáhá organizacím dosáhnout rovnováhy mezi produktivitou – jak rychle může osoba získat přístup k potřebným prostředkům, například když se připojí k mojí organizaci? A zabezpečení – jak se má v průběhu času měnit přístup, například kvůli změnám stavu zaměstnanosti této osoby?

**Správa životního cyklu identit** je základem pro řízení identit a efektivní řízení škálování vyžaduje modernizaci infrastrukturu správy životního cyklu identit pro aplikace. Správa životního cyklu identit se zaměřuje na automatizaci a správu celého procesu životního cyklu digitální identity. 

![zřizování cloudu](media/what-is-provisioning/cloud-1.png)

## <a name="what-is-a-digital-identity"></a>Co je digitální identita?

Digitální identita je informace o entitě, kterou používá jeden nebo více výpočetních prostředků, jako jsou například operační systémy nebo aplikace. Tyto entity můžou představovat lidi, organizace, aplikace nebo zařízení.  Identita je obvykle popsána pomocí atributů, které jsou k ní přidruženy, například názvu, identifikátorů a vlastností, jako jsou například role používané pro správu přístupu.  Tyto atributy usnadňují systémům určování, kdo mají přístup k tomu, co a kdo smí používat tento nebo tento systém.  

## <a name="managing-the-lifecycle-of-digital-identities"></a>Správa životního cyklu digitálních identit

Správa digitálních identit je složitá úloha, zejména v případě, že souvisí s korelacemi reálných objektů, jako je osoba a jejich vztah k organizaci, jako je zaměstnanec organizace, s digitální reprezentací.    V malých organizacích může být digitální reprezentace jednotlivců, kteří potřebují identitu, schopná ruční proces – když je někdo najatý nebo dodavatel dostane, IT specialista může pro ně vytvořit účet v adresáři a přiřadit jim přístup, který potřebují.  Ve střední velikosti a velkých organizacích ale může automatizace umožňovat efektivnější škálování organizace a zajištění správných identit.

Typický proces pro zajištění správy životního cyklu identit v organizaci se řídí těmito kroky:

1. Určete, zda již existují systémy záznamů: zdroje dat, které organizace považuje za autoritativní.  Organizace může mít například pracovní den systému HR a tento systém je autoritativní pro poskytování aktuálního seznamu zaměstnanců a některé z jejich vlastností, jako jsou jméno nebo oddělení zaměstnance.  Nebo e-mailový systém, jako je Exchange Online, může být autoritativní pro e-mailovou adresu zaměstnance.

2. Připojte tyto systémy záznamů s jedním nebo více adresáři a databázemi, které používají aplikace, a vyřešte veškeré nekonzistence mezi adresáři a systémy záznamu. Adresář může například obsahovat zastaralá data, jako je například účet bývalého zaměstnance, který již není potřeba. 

3. Určete, jaké procesy lze použít k poskytnutí autoritativních informací při absenci systému záznamu.  Pokud například existují digitální identity, ale Návštěvníci, ale organizace nemá žádné databáze pro návštěvníky, může být nutné najít alternativní způsob, jak určit, kdy už není potřeba Digitální identita pro návštěvníky.

4. Nakonfigurujte změny provedené ze systému záznamů nebo jiných procesů do každého adresáře nebo databáze, které vyžadují aktualizaci.

## <a name="identity-lifecycle-management-for-representing-employees-and-other-individuals-with-an-organizational-relationship"></a>Správa životního cyklu identit pro reprezentaci zaměstnanců a dalších jednotlivců pomocí organizačního vztahu

Při plánování správy životního cyklu identit zaměstnanců nebo jiných jednotlivců s organizačním vztahem, jako je dodavatel nebo student, mnoho organizací modeluje "připojení, přesun a opuštění".  Jsou to:
    
   - Spojení – pokud jednotlivec přichází do rozsahu vyžadování přístupu, musí tyto aplikace vyžadovat identitu, takže je možné, že je potřeba vytvořit novou digitální identitu, pokud už jedna není dostupná.
   - Přesunout – když se jednotlivec přesune mezi hranicemi, které vyžadují přidání nebo odebrání dalších autorizací přístupu do své digitální identity
   - Opustit – pokud jednotlivec opustí rozsah vyžadování přístupu, může být nutné odebrat přístup a následně nemusí identitu už vyžadovat jiné aplikace než pro účely auditu nebo forenzní.

Pokud se například nový zaměstnanec připojí k vaší organizaci, který se nikdy nepřidal do vaší organizace, bude tento zaměstnanec vyžadovat novou digitální identitu reprezentovanou jako uživatelský účet ve službě Azure AD.  Vytváření tohoto účtu by mohlo být "připojujícím" proces, který by mohl být automatizovaný, pokud existoval systém záznamů, jako je třeba pracovní den, který může indikovat, že nový zaměstnanec začne pracovat.  Později, pokud má vaše organizace pohyb zaměstnanců od řekněme, že prodej do marketingu, patří do procesu "stěhovacího".  To by vyžadovalo Odebrání přístupových práv, která měla v organizaci pro prodej, kterou už nepotřebují, a udělení oprávnění v organizaci pro marketing, kterou noví noví potřebují.

## <a name="identity-lifecycle-management-for-guests"></a>Správa životního cyklu identit pro hosty

Podobné procesy jsou také potřeba pro hosty a jiné uživatele.  Správa nároků Azure AD využívá Azure AD Business-to-Business (B2B) a poskytuje řízení životního cyklu, které je potřeba pro spolupráci s lidmi mimo vaši organizaci, kteří potřebují přístup k prostředkům vaší organizace. Pomocí Azure AD B2B se externí uživatelé ověřují do svého domovského adresáře, ale mají v adresáři reprezentace. Reprezentace v adresáři umožňuje uživateli přiřadit přístup k vašim prostředkům.  Správa nároků umožňuje jednotlivcům mimo vaši organizaci požádat o přístup a vytvořit pro ně digitální identitu podle potřeby. Tyto digitální identity se automaticky odeberou, když uživatel ztratí přístup.  

## <a name="how-does-azure-ad-automate-identity-lifecycle-management"></a>Jak Azure AD automatizuje správu životního cyklu identit?

Služba Azure AD v současné době poskytuje tyto funkce:

* Uživatelé, kteří představují zaměstnance, se dají automaticky vytvořit a aktualizovat ve službě Azure AD a Active Directory s využitím [zřizování na základě lidských zdrojů](what-is-hr-driven-provisioning.md) .
* Uživatele, kteří už jsou ve službě Active Directory, se dají automaticky vytvořit a spravovat ve službě Azure AD pomocí [zřizování mezi adresáři](what-is-inter-directory-provisioning.md) .
* Uživatelé můžou být automaticky přiřazeni ke skupinám na základě jejich vlastností, pomocí [dynamických skupin](../external-identities/use-dynamic-groups.md#what-are-dynamic-groups) a můžou na vyžádání být přiřazeni skupinám, týmům, rolím Azure AD, rolím prostředků Azure a webům SharePointu Online pomocí [správy nároků](entitlement-management-scenarios.md) a [Privileged Identity Management](../privileged-identity-management/pim-configure.md) .
* Aktualizace uživatelů se dají automaticky posílat do dalších aplikací s využitím [zřizování aplikací](what-is-app-provisioning.md) .

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Řízení přístupu pro externí uživatele ve správě nároků Azure AD](/azure/active-directory/governance/entitlement-management-external-users)
- [Co je zřizování na základě lidských zdrojů?](what-is-hr-driven-provisioning.md)
- [Co je zřizování aplikací?](what-is-app-provisioning.md)
- [Co je zřizování mezi adresářovými systémy?](what-is-inter-directory-provisioning.md)
