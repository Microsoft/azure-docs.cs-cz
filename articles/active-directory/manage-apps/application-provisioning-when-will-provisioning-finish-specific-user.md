---
title: Podívejte se, kdy konkrétní uživatel bude mít přístup k aplikaci | Dokumentace Microsoftu
description: Jak zjistit, kdy kriticky důležité uživatel moct získat přístup k aplikaci, kterou jste nakonfigurovali pro zřizování uživatelů pomocí Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd6b70e7a4542a4cad2ee95fa280ddf8fbe6553
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310032"
---
# <a name="check-the-status-of-user-provisioning"></a>Zkontrolujte stav zřizování uživatelů

Služba zřizování Azure AD spustí počáteční zřizování cyklus systému zdrojového a cílového systému, za nímž následuje pravidelné přírůstkové cykly. Při konfiguraci zřizování pro aplikace, můžete zkontrolovat aktuální stav služby zřizování a zobrazit, kdy uživatel bude mít přístup k aplikaci.

## <a name="view-the-provisioning-progress-bar-preview"></a>Zobrazení přidělení přístupových práv průběh panelu (preview)

 Na **zřizování** stránky pro aplikaci, můžete zobrazit stav zřizování služby Azure AD. **Aktuální stav** oblast v dolní části stránky části ukazuje, zda bylo zahájeno zřizování cyklus zřizování uživatelských účtů. Můžete sledovat průběh cyklu, zobrazit, kolik uživatelů a skupin se zřizují a zjistit, kolik rolí vytvářejí.

Při první konfiguraci automatického zřizování **aktuální stav** části v dolní části stránky se zobrazí stav počáteční cyklů zřizování. Tato část aktualizuje pokaždé, když běží přírůstkové cyklu. Jsou zobrazeny následující údaje:
- Typ zřizování cyklu (počáteční nebo přírůstkového), který je spuštěn nebo byl poslední dokončené.
- A **indikátor průběhu** zobrazuje procento zřizování cyklu, které bylo dokončeno. Procento zobrazuje počet stránek, které jsou zřízené. Všimněte si, že každá stránka může obsahovat víc uživatelů nebo skupin, abyste procento nebude korelovat přímo na počet uživatelů, skupiny nebo role zřízený.
- A **aktualizovat** tlačítko, které vám umožní zachovat zobrazení aktualizovat.
- Počet **uživatelé** a **skupiny** zřízené a počet role vytvořené. Během počáteční cyklu **uživatelé** číslo přičítá nahoru o 1 při vytvoření nebo aktualizaci uživatele a při odstranění uživatele odpočítává o 1. Během přírůstkového cyklu, nemají vliv na aktualizace uživatele **uživatelé** počet; mění pouze při vytvoření nebo odstranění uživatelů.
- A **zobrazit protokoly auditu** odkaz, který otevře auditu Azure AD protokoly pro podrobnosti o všech operacích provozované služby zřizování uživatelů, včetně zřizování stav pro jednotlivé uživatele (najdete v článku [protokoly auditu použití](#use-audit-logs-to-check-a-users-provisioning-status)níže v části).

Po dokončení zřizování cyklu **statistiku k datu** část zobrazuje kumulativní počet uživatelů a skupin, které se zřizují k datu, datum dokončení a doba trvání posledního cyklu. **ID aktivity** jednoznačně identifikuje poslední cyklus zřizování. **ID úlohy** je jedinečný identifikátor pro úlohy zřizování a je specifické pro aplikaci ve vašem tenantovi.

Probíhá zřizování můžete zobrazit na webu Azure Portal, v **Azure Active Directory &gt; podnikové aplikace &gt; \[název_aplikace\] &gt; zřizování** kartu.

![Indikátor průběhu zřizování stránky](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Protokoly auditu použijte ke kontrole stavu zřizování uživatele

Zobrazíte stav zřizování pro vybraného uživatele najdete v protokolech auditování ve službě Azure AD. Všechny operace spuštění uživatelem služby zřizování se zaznamenávají ve službě Azure AD protokoly auditu. To zahrnuje všechny operace provedené zdrojové a cílové systémy a uživatelská data, která byla čteným nebo zapsaným během každé operace čtení a zápisu.

Zřizování protokolů auditu je možný na webu Azure Portal, **Azure Active Directory &gt; podnikové aplikace &gt; \[název aplikace\] &gt; protokoly auditu** kartu. Filtrovat protokoly **zřizování účtů** kategorie zobrazíte jen zřizování události pro tuto aplikaci. Můžete hledat uživatele na základě "Odpovídající ID", která byla konfigurována pro ně mapování atributů. 

Například, pokud jste nakonfigurovali "hlavní název uživatele" nebo "e-mailovou adresu" jako odpovídající atribut na straně služby Azure AD a uživatel není zřizování má hodnotu "audrey@contoso.com", potom hledání v protokolech auditu "audrey@contoso.com" a poté zkontrolovat položky Vrátí.

Zřizování protokoly auditu zaznamenejte všechny operace prováděné zřizovací služba, včetně:

* Dotazování služby Azure AD pro přiřazené uživatele, kteří jsou v oboru pro zřizování
* Dotazování na cílové aplikace existenci těchto uživatelů
* Porovnávání objektů uživatelů mezi systémem
* Přidání, aktualizace nebo zakázání uživatelský účet v cílovém systému založené na porovnání

Další informace o tom, jak načíst protokoly auditu na portálu Azure portal najdete v tématu [zřizování průvodce vytvářením sestav](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Jak dlouho bude trvat nezajistíte zřízení uživatelů?
Používáte automatické zřizování uživatelů pomocí aplikace Azure AD automaticky zřídí a aktualizuje uživatelské účty v aplikaci založenou na věci, jako je [přiřazení uživatelů a skupin](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) při pravidelném plánovaném časový interval, obvykle každých 10 minut.

Čas potřebný pro daného uživatele, které se mají zřídit závisí hlavně na tom, jestli vaše zřizování úloha běží počáteční synchronizace nebo přírůstková synchronizace.

- Pro **počáteční synchronizace**, čas úlohy závisí na mnoha faktorech, včetně počtu uživatelů a skupin v oboru pro zřizování a celkový počet uživatelů a skupin ve zdrojovém systému. První synchronizace mezi službami Azure AD a aplikace může trvat 20 minut i několik hodin, v závislosti na velikosti adresáře služby Azure AD a počet uživatelů v oboru pro zřizování. Úplný seznam faktorů, které ovlivňují výkon počáteční replikace jsou shrnuté později v této části.

- Pro **přírůstkové synchronizace** po počáteční synchronizaci časů úloh jsou obvykle rychlejší (například do 10 minut), jako zřizovací služba ukládá vodoznaky, představující stav obou systémů po počáteční synchronizaci, zvýšení výkonu z následných se synchronizuje. Čas úlohy závisí na počtu změn v tento cyklus zřizování. Pokud méně než 5 000 uživatelů nebo změny členství ve skupině, můžete dokončit úlohy v rámci jednoho cyklu přírůstkové zřizování. 

Následující tabulka shrnuje časů synchronizace pro běžné scénáře zřizování. V těchto scénářích zdrojového systému je Azure AD a cílovém systému je aplikace SaaS. Doba synchronizace jsou odvozeny z statistické analýzy úloh synchronizace pro aplikace SaaS, ServiceNow, síti na pracovišti, G Suite a Salesforce.


| Konfigurace oboru | Uživatelé, skupiny a členové v oboru | Čas počáteční synchronizace | Přírůstková synchronizace |
| -------- | -------- | -------- | -------- |
| Přiřazení uživatelé a skupiny pouze synchronizovat |  < 1,000 |  < 30 minut | < 30 minut |
| Přiřazení uživatelé a skupiny pouze synchronizovat |  1,000 - 10,000 | 142 - minut 708 | < 30 minut |
| Přiřazení uživatelé a skupiny pouze synchronizovat |   10,000 - 100,000 | 1,170 - 2,340 minut | < 30 minut |
| Synchronizovat všechny uživatele a skupiny ve službě Azure AD |  < 1,000 | < 30 minut  | < 30 minut |
| Synchronizovat všechny uživatele a skupiny ve službě Azure AD |  1,000 - 10,000 | < 30-120 minut | < 30 minut |
| Synchronizovat všechny uživatele a skupiny ve službě Azure AD |  10,000 - 100,000  | 713 - 1,425 minut | < 30 minut |
| Synchronizovat všechny uživatele ve službě Azure AD|  < 1,000  | < 30 minut | < 30 minut |
| Synchronizovat všechny uživatele ve službě Azure AD | 1,000 - 10,000  | 43 - 86 minut | < 30 minut |


Pro konfiguraci **synchronizace přiřazené uživatele a skupiny pouze**, těchto vzorců můžete použít k určení přibližné minimální a maximální očekávané **počáteční synchronizace** časy:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Seznam faktorů, které ovlivňují čas potřebný k dokončení **počáteční synchronizace**:

- Celkový počet uživatelů a skupin v oboru pro zřizování.

- Celkový počet uživatelů, skupin a členů skupiny, které jsou k dispozici ve zdrojovém systému (Azure AD).

- Určuje, zda uživatelé v oboru pro zřizování budou odpovídat existujícím uživatelům v cílové aplikaci nebo nutné vytvořit poprvé. Úlohy synchronizace pro které všichni uživatelé jsou vytvořeni poprvé trvat přibližně *dvakrát tak dlouho,* tak, jak synchronizovat úlohy, pro které všichni uživatelé budou odpovídat existujícím uživatelům.

- Počet chyb v [protokoly auditu](check-status-user-account-provisioning.md). Pokud je mnoho chyb a služby zřizování přešel do stavu karantény je pomalejší výkon.    

- Požádat o šířku pásma a omezení šířky pásma implementovány cílovým systémem. Některé cílové systémy implementovat omezení frekvence požadavků a omezení, které můžou ovlivnit výkon během operací velký synchronizace. Aplikace, která přijímá příliš mnoho požadavků příliš rychle za těchto podmínek může zpomalit jeho míry odezvy nebo uzavřením připojení. Kvůli zvýšení výkonu se konektoru je potřeba upravit odesláním žádosti o aplikace rychleji, než dokáže zpracovat aplikace. Zřizování konektory vytvořené microsoftem provést tuto úpravu. 

- Počet a velikost přiřazených skupin. Přiřazené skupiny synchronizace trvá déle než synchronizaci uživatelů. Počet a velikost přiřazených skupin dopad na výkon. Pokud má aplikace [mapování povolena pro skupinu synchronizace objektů](customize-application-attributes.md#editing-group-attribute-mappings)vlastnosti skupiny jako například názvy skupin a členství ve skupinách se synchronizují kromě uživatelů. Tyto další synchronizace bude trvat déle než pouze synchronizaci uživatelských objektů.

## <a name="next-steps"></a>Další postup
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
