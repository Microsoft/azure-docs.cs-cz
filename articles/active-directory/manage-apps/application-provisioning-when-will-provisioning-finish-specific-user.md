---
title: Zjistěte, kdy bude mít konkrétní uživatel přístup k aplikaci | Microsoft Docs
description: Jak zjistit, kdy uživatel s kritickou důležitosti bude mít přístup k aplikaci, kterou jste nakonfigurovali pro zřizování uživatelů pomocí Azure AD
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
ms.openlocfilehash: bf6190ae35a573ab4b9a37f5cd1f2bd75f06ca46
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667158"
---
# <a name="check-the-status-of-user-provisioning"></a>Ověřit stav zřizování uživatelů

Služba zřizování Azure AD spouští počáteční cyklus zřizování proti zdrojovému systému a cílovému systému, následovaným pravidelnými přírůstkovým cyklem. Při konfiguraci zřizování pro aplikaci můžete zjistit aktuální stav služby zřizování a zjistit, kdy uživatel bude moci získat přístup k aplikaci.

## <a name="view-the-provisioning-progress-bar"></a>Zobrazit indikátor průběhu zřizování

 Na stránce **zřizování** pro aplikaci můžete zobrazit stav služby Azure AD Provisioning. V části **aktuální stav** v dolní části stránky se zobrazuje, zda zřizovací cyklus zahájil zřizování uživatelských účtů. Průběh cyklu můžete sledovat, zjistit, kolik uživatelů a skupin se zřídilo, a zjistit, kolik rolí se vytvořilo.

Při první konfiguraci automatického zřizování se v části **aktuální stav** v dolní části stránky zobrazuje stav počátečního cyklu zřizování. Tato část se aktualizuje pokaždé, když se spustí přírůstkový cyklus. Zobrazí se následující podrobnosti:
- Typ cyklu zřizování (počáteční nebo přírůstkové), který aktuálně běží nebo byl naposledy dokončen.
- Indikátor **průběhu** znázorňující procento dokončeného cyklu zřizování. Procento odráží počet zřízených stránek. Všimněte si, že každá stránka může obsahovat více uživatelů nebo skupin, takže procento přímo nekoreluje s počtem uživatelů, skupin nebo zřízených rolí.
- Tlačítko pro **obnovení** , které můžete použít k udržení aktualizovaného zobrazení.
- Počet zřízených **uživatelů** a **skupin** a počet vytvořených rolí. Během počátečního cyklu se číslo **uživatelů** při vytvoření nebo aktualizaci uživatele počítá podle 1 a při odstranění uživatele se vypočítává podle 1. Během přírůstkového cyklu nemají aktualizace uživatelů vliv na počet **uživatelů** ; Tento počet se změní pouze v případě, že jsou uživatelé vytvořeni nebo smazáni.
- Odkaz **Zobrazit protokoly auditu** , který otevře protokoly auditu Azure AD pro podrobnosti o všech operacích spuštěných službou zřizování uživatelů, včetně stavu zřizování pro jednotlivé uživatele (viz část [použití protokolů auditu](#use-audit-logs-to-check-a-users-provisioning-status) níže).

Po dokončení cyklu zřizování se v části **Statistika k datu** zobrazuje kumulativní počet uživatelů a skupin, které byly zřízeny do data, spolu s datem dokončení a dobou trvání posledního cyklu. **ID aktivity** jednoznačně identifikuje nejnovější cyklus zřizování. **ID úlohy** je jedinečný identifikátor pro úlohu zřizování a je specifický pro aplikaci ve vašem tenantovi.

Průběh zřizování můžete zobrazit v Azure Portal na kartě **Azure Active Directory &gt; podnikové\] aplikace &gt; \[ &gt; název aplikace** .

![Indikátor průběhu zřizování stránky](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>Kontrola stavu zřizování uživatele pomocí protokolů auditu

Pokud chcete zobrazit stav zřizování pro vybraného uživatele, Projděte si protokoly auditu v Azure AD. Všechny operace spouštěné službou zřizování uživatelů se zaznamenávají v protokolech auditu Azure AD. To zahrnuje všechny operace čtení a zápisu provedené ve zdrojovém a cílovém systému a uživatelská data, která byla během každé operace načtena nebo zapsána.

K protokolům auditu zřizování se dá v Azure Portal přihlašovat na kartě  **&gt; Azure Active Directory podnikové aplikace\] &gt; \[ &gt; název aplikace** . Vyfiltrujte protokoly v kategorii **zřizování účtů** , aby se zobrazily jenom události zřizování pro tuto aplikaci. Uživatele můžete vyhledat na základě "odpovídajícího ID", které bylo pro ně nakonfigurováno v mapování atributů. 

Pokud například nakonfigurujete "hlavní název uživatele" nebo "e-mailovou adresu" jako atribut pro porovnání na straně Azure AD a uživatel, který není zřizováním, bude mít hodnotuaudrey@contoso.com"", vyhledejte v protokolech auditu "audrey@contoso.com" a zkontrolujte položky. vrátil.

Protokoly auditu zřizování zaznamenávají všechny operace prováděné službou zřizování, včetně:

* Dotazování Azure AD na přiřazené uživatele, kteří jsou v oboru pro zřizování
* Dotazování cílové aplikace na existenci těchto uživatelů
* Porovnání objektů uživatele mezi systémem
* Přidání, aktualizace nebo zakázání uživatelského účtu v cílovém systému na základě porovnání

Další informace o tom, jak číst protokoly auditu v Azure Portal, najdete v [průvodci zřizováním sestav](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Jak dlouho bude trvat zřizování uživatelů?
Při použití automatického zřizování uživatelů s aplikací Azure AD automaticky zřídí a aktualizuje uživatelské účty v aplikaci na základě toho, co je třeba [přiřazení uživatelů a skupin](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) , v pravidelných plánovaných časových intervalech, obvykle každých 10 minut.

Čas potřebný k zřízení daného uživatele závisí hlavně na tom, jestli vaše úloha zřizování spouští počáteční synchronizaci nebo přírůstkovou synchronizaci.

- Pro **počáteční synchronizaci**je doba úlohy závislá na mnoha faktorech, včetně počtu uživatelů a skupin v oboru pro zřizování a celkového počtu uživatelů a skupin ve zdrojovém systému. První synchronizace mezi službou Azure AD a aplikací může trvat až 20 minut až několik hodin, a to v závislosti na velikosti adresáře služby Azure AD a počtu uživatelů v oboru pro zřizování. Podrobný seznam faktorů, které ovlivňují výkon počáteční synchronizace, jsou shrnuty dále v této části.

- V případě přírůstkových synchronizací po počáteční synchronizaci je doba úlohy obvykle rychlejší (například během 10 minut), protože služba zřizování ukládá meze, které reprezentují stav obou systémů po počáteční synchronizaci a zvyšuje výkon následných synchronizací. Čas úlohy závisí na počtu změn zjištěných v rámci tohoto cyklu zřizování. Pokud je k dispozici méně než 5 000 změn členství uživatele nebo skupiny, může být úloha dokončena v rámci jednoho přírůstkového zřizovacího cyklu. 

Následující tabulka shrnuje časy synchronizace pro běžné scénáře zřizování. V těchto scénářích je zdrojový systém Azure AD a cílový systém je aplikace SaaS. Časy synchronizace se odvozují ze statistické analýzy úloh synchronizace pro aplikace SaaS ServiceNow, pracoviště, Salesforce a G Suite.


| Konfigurace oboru | Uživatelé, skupiny a členové v oboru | Čas prvotní synchronizace | Čas přírůstkové synchronizace |
| -------- | -------- | -------- | -------- |
| Synchronizovat jenom přiřazené uživatele a skupiny |  < 1 000 |  < 30 minut | < 30 minut |
| Synchronizovat jenom přiřazené uživatele a skupiny |  1,000 - 10,000 | 142 – 708 minut | < 30 minut |
| Synchronizovat jenom přiřazené uživatele a skupiny |   10 000 – 100 000 | 1 170 – 2 340 minut | < 30 minut |
| Synchronizovat všechny uživatele a skupiny v Azure AD |  < 1 000 | < 30 minut  | < 30 minut |
| Synchronizovat všechny uživatele a skupiny v Azure AD |  1,000 - 10,000 | < 30-120 minut | < 30 minut |
| Synchronizovat všechny uživatele a skupiny v Azure AD |  10 000 – 100 000  | 713 – 1 425 minut | < 30 minut |
| Synchronizovat všechny uživatele v Azure AD|  < 1 000  | < 30 minut | < 30 minut |
| Synchronizovat všechny uživatele v Azure AD | 1,000 - 10,000  | 43 – 86 minut | < 30 minut |


U přiřazených **uživatelů a skupin konfigurace synchronizace**konfigurací můžete pomocí následujících vzorců určit přibližné minimální a maximální doby **počáteční synchronizace** :

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Souhrn faktorů, které mají vliv na dobu potřebnou k dokončení **počáteční synchronizace**:

- Celkový počet uživatelů a skupin v oboru pro zřizování.

- Celkový počet uživatelů, skupin a členů skupiny, kteří jsou přítomni ve zdrojovém systému (Azure AD).

- Určuje, jestli uživatelé v oboru pro zřizování odpovídají existujícím uživatelům v cílové aplikaci, nebo je potřeba je vytvořit poprvé. Úlohy synchronizace, pro které jsou všichni uživatelé vytvořeni poprvé, trvají přibližně *dvakrát, pokud* úlohy synchronizace, pro které se všichni uživatelé shodují s existujícími uživateli.

- Počet chyb v protokolech [auditu](check-status-user-account-provisioning.md). Výkon je pomalejší, pokud dojde k mnoha chybám a služba zřizování přešla do karantény.    

- Omezení rychlosti požadavků a omezování implementované cílovým systémem. Některé cílové systémy implementují omezení přenosů požadavků a omezování, což může mít vliv na výkon při rozsáhlých operacích synchronizace. V rámci těchto podmínek aplikace, která přijímá příliš mnoho požadavků, může být příliš rychlá a může zpomalit rychlost odezvy nebo ukončit připojení. Aby bylo možné zvýšit výkon, musí konektor upravit tím, že neposílá požadavky aplikace rychleji, než je aplikace dokáže zpracovat. Tuto úpravu vytváří konektory zřízené Microsoftem. 

- Počet a velikost přiřazených skupin. Synchronizace přiřazených skupin trvá déle než synchronizace uživatelů. Počet i velikost přiřazených skupin mají vliv na výkon. Pokud má aplikace [povolená mapování pro synchronizaci objektů skupin](customize-application-attributes.md#editing-group-attribute-mappings), jsou kromě uživatelů synchronizovány i vlastnosti skupiny, jako jsou názvy skupin a členství. Tyto další synchronizace budou trvat déle než jenom synchronizace uživatelských objektů.

## <a name="next-steps"></a>Další postup
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
