---
title: Zjistěte, kdy bude mít konkrétní uživatel přístup k aplikaci.
description: Jak zjistit, kdy uživatel s kritickou důležitosti bude mít přístup k aplikaci, kterou jste nakonfigurovali pro zřizování uživatelů pomocí Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 890c4a8a5aec3b15b150908c64bb114bd85a61a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256758"
---
# <a name="check-the-status-of-user-provisioning"></a>Ověřit stav zřizování uživatelů

Služba zřizování Azure AD spouští počáteční cyklus zřizování proti zdrojovému systému a cílovému systému, následovaným pravidelnými přírůstkovým cyklem. Při konfiguraci zřizování pro aplikaci můžete zjistit aktuální stav služby zřizování a zjistit, kdy uživatel bude moci získat přístup k aplikaci.

## <a name="view-the-provisioning-progress-bar"></a>Zobrazit indikátor průběhu zřizování

 Na stránce **zřizování** pro aplikaci můžete zobrazit stav služby Azure AD Provisioning. V části **aktuální stav** v dolní části stránky se zobrazuje, zda zřizovací cyklus zahájil zřizování uživatelských účtů. Průběh cyklu můžete sledovat, zjistit, kolik uživatelů a skupin se zřídilo, a zjistit, kolik rolí se vytvořilo.

Při první konfiguraci automatického zřizování se v části **aktuální stav** v dolní části stránky zobrazuje stav počátečního cyklu zřizování. Tato část se aktualizuje pokaždé, když se spustí přírůstkový cyklus. Zobrazí se následující podrobnosti:
- Typ cyklu zřizování (počáteční nebo přírůstkové), který aktuálně běží nebo byl naposledy dokončen.
- Indikátor **průběhu** znázorňující procento dokončeného cyklu zřizování. Procento odráží počet zřízených stránek. Všimněte si, že každá stránka může obsahovat více uživatelů nebo skupin, takže procento přímo nekoreluje s počtem uživatelů, skupin nebo zřízených rolí.
- Tlačítko pro **obnovení** , které můžete použít k udržení aktualizovaného zobrazení.
- Počet **uživatelů** a **skupin** v úložišti dat konektoru. Počet se zvyšuje, kdykoli se do oboru zřizování přidá objekt. Pokud je uživatel bez potíží nebo odstraněn z úložiště dat konektoru, počet se neukončí. Počet se přepočítá při první synchronizaci po [resetování](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta) disků CD. 
- Odkaz **Zobrazit protokoly auditu** , ve kterém se otevře protokol zřizování Azure AD, který obsahuje podrobnosti o všech operacích spuštěných službou zřizování uživatelů, včetně stavu zřizování pro jednotlivé uživatele (viz část [použití protokolů zřizování](#use-provisioning-logs-to-check-a-users-provisioning-status) níže).

Po dokončení cyklu zřizování se v části **Statistika k datu** zobrazuje kumulativní počet uživatelů a skupin, které byly zřízeny do data, spolu s datem dokončení a dobou trvání posledního cyklu. **ID aktivity** jednoznačně identifikuje nejnovější cyklus zřizování. **ID úlohy** je jedinečný identifikátor pro úlohu zřizování a je specifický pro aplikaci ve vašem tenantovi.

Průběh zřizování můžete zobrazit v Azure Portal na kartě **Azure Active Directory &gt; podnikové aplikace &gt; \[ název aplikace \] &gt;** .

![Indikátor průběhu zřizování stránky](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Pomocí protokolů zřizování ověřte stav zřizování uživatele.

Pokud chcete zobrazit stav zřizování pro vybraného uživatele, přečtěte si téma [zřizování protokolů (Preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) ve službě Azure AD. Všechny operace spouštěné službou zřizování uživatelů se zaznamenávají v protokolech zřizování služby Azure AD. To zahrnuje všechny operace čtení a zápisu provedené ve zdrojovém a cílovém systému a uživatelská data, která byla během každé operace načtena nebo zapsána.

K protokolům zřizování v Azure Portal můžete získat přístup tak, že  &gt; v části aktivita vyberete Azure Active Directory protokoly zřizování **podnikových aplikací** &gt; **(Preview)** .  Data zřizování můžete vyhledat na základě jména uživatele nebo identifikátoru v systému zdrojového systému nebo v cílovém systému. Podrobnosti najdete v tématu [zřizování protokolů (Preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Protokoly zřizování zaznamenávají všechny operace prováděné službou zřizování, včetně:

* Dotazování Azure AD na přiřazené uživatele, kteří jsou v oboru pro zřizování
* Dotazování cílové aplikace na existenci těchto uživatelů
* Porovnání objektů uživatele mezi systémem
* Přidání, aktualizace nebo zakázání uživatelského účtu v cílovém systému na základě porovnání

Další informace o tom, jak číst protokoly zřizování v Azure Portal, najdete v [příručce pro vytváření sestav](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Jak dlouho bude trvat zřizování uživatelů?
Při použití automatického zřizování uživatelů s aplikací Azure AD automaticky zřídí a aktualizuje uživatelské účty v aplikaci na základě toho, co je třeba [přiřazení uživatelů a skupin](../manage-apps/assign-user-or-group-access-portal.md) , v pravidelných plánovaných intervalech, obvykle každých 40 minut.

Čas potřebný k zřízení daného uživatele závisí hlavně na tom, jestli vaše úloha zřizování spouští počáteční cyklus nebo přírůstkový cyklus.

- Pro **počáteční cyklus** je doba úlohy závislá na mnoha faktorech, včetně počtu uživatelů a skupin v oboru pro zřizování a celkového počtu uživatelů a skupin ve zdrojovém systému. První synchronizace mezi službou Azure AD a aplikací může trvat až 20 minut až několik hodin, a to v závislosti na velikosti adresáře služby Azure AD a počtu uživatelů v oboru pro zřizování. Úplný seznam faktorů, které ovlivňují výkon počátečního cyklu, jsou shrnuty dále v této části.

- Pro **přírůstkové cykly** po počátečním cyklu úlohy mají někdy rychlejší dobu (například během 10 minut), protože služba zřizování ukládá meze, které reprezentují stav obou systémů po počátečním cyklu, což zlepšuje výkon následných synchronizací. Čas úlohy závisí na počtu změn zjištěných v rámci tohoto cyklu zřizování. Pokud je k dispozici méně než 5 000 změn členství uživatele nebo skupiny, může být úloha dokončena v rámci jednoho přírůstkového zřizovacího cyklu. 

Následující tabulka shrnuje časy synchronizace pro běžné scénáře zřizování. V těchto scénářích je zdrojový systém Azure AD a cílový systém je aplikace SaaS. Časy synchronizace se odvozují ze statistické analýzy úloh synchronizace pro aplikace SaaS ServiceNow, pracoviště, Salesforce a G Suite.


| Konfigurace oboru | Uživatelé, skupiny a členové v oboru | Čas prvotního cyklu | Čas přírůstkového cyklu |
| -------- | -------- | -------- | -------- |
| Synchronizovat jenom přiřazené uživatele a skupiny |  < 1 000 |  < 30 minut | < 30 minut |
| Synchronizovat jenom přiřazené uživatele a skupiny |  1 000 – 10 000 | 142 – 708 minut | < 30 minut |
| Synchronizovat jenom přiřazené uživatele a skupiny |   10 000 – 100 000 | 1 170 – 2 340 minut | < 30 minut |
| Synchronizovat všechny uživatele a skupiny v Azure AD |  < 1 000 | < 30 minut  | < 30 minut |
| Synchronizovat všechny uživatele a skupiny v Azure AD |  1 000 – 10 000 | < 30-120 minut | < 30 minut |
| Synchronizovat všechny uživatele a skupiny v Azure AD |  10 000 – 100 000  | 713 – 1 425 minut | < 30 minut |
| Synchronizovat všechny uživatele v Azure AD|  < 1 000  | < 30 minut | < 30 minut |
| Synchronizovat všechny uživatele v Azure AD | 1 000 – 10 000  | 43 – 86 minut | < 30 minut |

U **přiřazených uživatelů a skupin konfigurace synchronizace** konfigurací můžete pomocí následujících vzorců určit přibližné minimální a maximální očekávané **počáteční časy cyklů** :

- Minimální počet minut = 0,01 x [počet přiřazených uživatelů, skupin a členů skupiny]
- Maximální počet minut = 0,08 x [počet přiřazených uživatelů, skupin a členů skupiny]

Souhrn faktorů, které mají vliv na dobu potřebnou k dokončení **počátečního cyklu**:

- Celkový počet uživatelů a skupin v oboru pro zřizování.

- Celkový počet uživatelů, skupin a členů skupiny, kteří jsou přítomni ve zdrojovém systému (Azure AD).

- Určuje, jestli uživatelé v oboru pro zřizování odpovídají existujícím uživatelům v cílové aplikaci, nebo je potřeba je vytvořit poprvé. Úlohy synchronizace, pro které jsou všichni uživatelé vytvořeni poprvé, trvají přibližně *dvakrát, pokud* úlohy synchronizace, pro které se všichni uživatelé shodují s existujícími uživateli.

- Počet chyb v [protokolech zřizování](check-status-user-account-provisioning.md). Výkon je pomalejší, pokud dojde k mnoha chybám a služba zřizování přešla do karantény. 

- Omezení rychlosti požadavků a omezování implementované cílovým systémem. Některé cílové systémy implementují omezení přenosů požadavků a omezování, což může mít vliv na výkon při rozsáhlých operacích synchronizace. V rámci těchto podmínek aplikace, která přijímá příliš mnoho požadavků, může být příliš rychlá a může zpomalit rychlost odezvy nebo ukončit připojení. Aby bylo možné zvýšit výkon, musí konektor upravit tím, že neposílá požadavky aplikace rychleji, než je aplikace dokáže zpracovat. Tuto úpravu vytváří konektory zřízené Microsoftem. 

- Počet a velikost přiřazených skupin. Synchronizace přiřazených skupin trvá déle než synchronizace uživatelů. Počet i velikost přiřazených skupin mají vliv na výkon. Pokud má aplikace [povolená mapování pro synchronizaci objektů skupin](customize-application-attributes.md#editing-group-attribute-mappings), jsou kromě uživatelů synchronizovány i vlastnosti skupiny, jako jsou názvy skupin a členství. Tyto další synchronizace budou trvat déle než jenom synchronizace uživatelských objektů.

- Pokud dojde k potížím s výkonem a pokoušíte se zřídit většinu uživatelů a skupin ve vašem tenantovi, použijte filtry oborů. Filtry oborů vám umožňují vyladit data, která služba zřizování z Azure AD extrahuje, filtrováním uživatelů na základě konkrétních hodnot atributů. Další informace o filtrech oborů najdete v tématu [zřizování aplikací na základě atributů s filtry oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md)