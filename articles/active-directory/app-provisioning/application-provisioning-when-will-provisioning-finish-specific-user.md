---
title: Zjistěte, kdy bude mít konkrétní uživatel přístup k aplikaci.
description: Jak zjistit, kdy má kriticky důležitý uživatel přístup k aplikaci, kterou jste nakonfigurovali pro zřizování uživatelů pomocí Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48370e2806b70d550bce95ceff3857a79561f247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264138"
---
# <a name="check-the-status-of-user-provisioning"></a>Kontrola stavu zřizování uživatelů

Zřizovací služba Azure AD spouští počáteční cyklus zřizování proti zdrojovému systému a cílovému systému, následovaný periodickými přírůstkovými cykly. Když nakonfigurujete zřizování aplikace, můžete zkontrolovat aktuální stav zřizovací služby a zjistit, kdy bude mít uživatel přístup k aplikaci.

## <a name="view-the-provisioning-progress-bar"></a>Zobrazení indikátoru průběhu zřizování

 Na stránce **Zřizování** pro aplikaci můžete zobrazit stav služby zřizování Azure AD. Aktuální **stav** části v dolní části stránky ukazuje, zda cyklus zřizování začala zřizování uživatelských účtů. Můžete sledovat průběh cyklu, zjistit, kolik uživatelů a skupin bylo zřízeno, a zjistit, kolik rolí se vytvoří.

Při první konfiguraci automatickézřivací stav, **aktuální stav** části v dolní části stránky zobrazuje stav počáteční zřizování cyklu. Tato část se aktualizuje při každém spuštění přírůstkového cyklu. Jsou zobrazeny následující podrobnosti:
- Typ cyklu zřizování (počáteční nebo přírůstkové), který je aktuálně spuštěnnebo byl naposledy dokončen.
- **Indikátor průběhu** zobrazující procento cyklu zřizování, který byl dokončen. Procento odráží počet zřízených stránek. Všimněte si, že každá stránka může obsahovat více uživatelů nebo skupin, takže procento přímo nekoreluje s počtem uživatelů, skupin nebo zřízených rolí.
- Tlačítko **Aktualizovat,** které můžete použít k aktualizaci zobrazení.
- Počet **uživatelů** a **skupin** v úložišti dat konektoru. Počet zvyšuje kdykoli objekt je přidán do oboru zřizování. Počet nepřejde dolů, pokud je uživatel obnovitelné nebo pevně odstraněn, protože to neodebere objekt z úložiště dat konektoru. Počet bude recaculated první synchronizace po [resetcd](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 
- A **Zobrazit protokoly auditu** odkaz, který otevře protokoly zřizování Azure AD pro podrobnosti o všech operacích spuštěných službou zřizování uživatelů, včetně stavu zřizování pro jednotlivé uživatele (viz část [Use provisioning protokoly](#use-provisioning-logs-to-check-a-users-provisioning-status) níže).

Po dokončení cyklu zřizování zobrazuje část **Statistika do data** kumulativní počet uživatelů a skupin, které byly zřízeny k dnešnímu dni, spolu s datem dokončení a dobou trvání posledního cyklu. **ID aktivity** jednoznačně identifikuje nejnovější cyklus zřizování. **ID úlohy** je jedinečný identifikátor pro úlohu zřizování a je specifické pro aplikaci ve vašem tenantovi.

Průběh zřizování lze zobrazit na webu Azure Portal, na kartě **Azure Active &gt; Directory Enterprise Apps &gt; \[název\] &gt; aplikace zřizování.**

![Panel průběhu stránky zřizování](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Použití protokolů zřizování ke kontrole stavu zřizování uživatele

Pokud chcete zobrazit stav zřizování pro vybraného uživatele, podívejte se do [protokolů zřizování (preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) ve službě Azure AD. Všechny operace spuštěné službou zřizování uživatelů se zaznamenávají do protokolů zřizování Azure AD. To zahrnuje všechny operace čtení a zápisu provedené do zdrojového a cílového systému a uživatelská data, která byla přečtena nebo zapsána během každé operace.

K protokolům zřizování na webu Azure Portal můžete přistupovat tak, že v části **Aktivita** **vyberete protokoly zřizování** aplikací **Azure Active Directory** &gt; **Enterprise Provisioning** &gt; (preview). Zřizování dat můžete prohledávat na základě jména uživatele nebo identifikátoru ve zdrojovém nebo cílovém systému. Podrobnosti naleznete v [tématu Zřizování protokolů (náhled)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Protokoly zřizování zaznamenávají všechny operace prováděné zřizovací službou, včetně:

* Dotazování Azure AD pro přiřazené uživatele, kteří jsou v oboru pro zřizování
* Dotazování cílové aplikace na existenci těchto uživatelů
* Porovnání uživatelských objektů mezi systémem
* Přidání, aktualizace nebo zakázání uživatelského účtu v cílovém systému na základě porovnání

Další informace o tom, jak číst protokoly zřizování na webu Azure Portal, najdete v [průvodci vytvářením přehledů](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Jak dlouho bude trvat, než se zřazují uživatelé?
Při použití automatického zřizování uživatelů s aplikací Azure AD automaticky zřazuje a aktualizuje uživatelské účty v aplikaci na základě věcí, jako je [přiřazení uživatelů a skupin](../manage-apps/assign-user-or-group-access-portal.md) v pravidelně naplánovaném časovém intervalu, obvykle každých 40 minut.

Čas potřebný pro daného uživatele, které mají být zřízeny závisí především na tom, zda úloha zřizování běží počáteční cyklus nebo přírůstkový cyklus.

- Pro **počáteční cyklus**závisí doba úlohy na mnoha faktorech, včetně počtu uživatelů a skupin v oboru pro zřizování a celkového počtu uživatelů a skupin ve zdrojovém systému. První synchronizace mezi Azure AD a aplikací může trvat od 20 minut do několika hodin, v závislosti na velikosti adresáře Azure AD a počtu uživatelů v oboru pro zřizování. Úplný seznam faktorů, které ovlivňují výkon počátečního cyklu, jsou shrnuty dále v této části.

- Pro **přírůstkové cykly** po počátečnícyklus doby úlohy mají tendenci být rychlejší (např. do 10 minut), jako zřizovací služba ukládá vodoznaky, které představují stav obou systémů po počáteční cyklu, zlepšení výkonu následné synchronizace. Doba úlohy závisí na počtu změn zjištěných v tomto cyklu zřizování. Pokud existuje méně než 5 000 změn členství uživatelů nebo skupin, úloha může být dokončena v rámci jednoho přírůstkového cyklu zřizování. 

Následující tabulka shrnuje časy synchronizace pro běžné scénáře zřizování. V těchto scénářích je zdrojový systém Azure AD a cílový systém je aplikace SaaS. Časy synchronizace jsou odvozeny ze statistické analýzy úloh synchronizace pro aplikace SaaS ServiceNow, Workplace, Salesforce a G Suite.


| Konfigurace oboru | Uživatelé, skupiny a členové v oboru | Počáteční doba cyklu | Přírůstková doba cyklu |
| -------- | -------- | -------- | -------- |
| Synchronizace pouze přiřazených uživatelů a skupin |  < 1 000 |  < 30 minut | < 30 minut |
| Synchronizace pouze přiřazených uživatelů a skupin |  1,000 - 10,000 | 142 - 708 minut | < 30 minut |
| Synchronizace pouze přiřazených uživatelů a skupin |   10,000 - 100,000 | 1 170 - 2 340 minut | < 30 minut |
| Synchronizace všech uživatelů a skupin ve službě Azure AD |  < 1 000 | < 30 minut  | < 30 minut |
| Synchronizace všech uživatelů a skupin ve službě Azure AD |  1,000 - 10,000 | < 30 - 120 minut | < 30 minut |
| Synchronizace všech uživatelů a skupin ve službě Azure AD |  10,000 - 100,000  | 713 - 1 425 minut | < 30 minut |
| Synchronizace všech uživatelů ve službě Azure AD|  < 1 000  | < 30 minut | < 30 minut |
| Synchronizace všech uživatelů ve službě Azure AD | 1,000 - 10,000  | 43 - 86 minut | < 30 minut |


Pouze pro konfiguraci **Synchronizovat přiřazeného uživatele a skupiny**můžete pomocí následujících vzorců určit přibližné minimální a maximální očekávané **počáteční doby cyklu:**

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Souhrn faktorů, které ovlivňují čas potřebný k dokončení **počátečního cyklu**:

- Celkový počet uživatelů a skupin v oboru pro zřizování.

- Celkový počet uživatelů, skupin a členů skupiny ve zdrojovém systému (Azure AD).

- Určuje, zda uživatelé v oboru pro zřizování jsou spárovány s existujícími uživateli v cílové aplikaci nebo je třeba vytvořit poprvé. Úlohy synchronizace, pro které jsou všichni uživatelé vytvořeni poprvé, trvat přibližně *dvakrát tak dlouho, než* úlohy synchronizace, pro které jsou všichni uživatelé spárováni s existujícími uživateli.

- Počet chyb v [protokolech zřizování](check-status-user-account-provisioning.md). Výkon je pomalejší, pokud existuje mnoho chyb a zřizovací služba přešla do stavu karantény. 

- Požadavky omezení rychlosti a omezení implementované cílovým systémem. Některé cílové systémy implementují omezení rychlosti požadavků a omezení, což může mít vliv na výkon během velkých synchronizačních operací. Za těchto podmínek aplikace, která přijímá příliš mnoho požadavků příliš rychle může zpomalit jeho rychlost odezvy nebo zavřete připojení. Chcete-li zvýšit výkon, konektor musí upravit tím, že neodesílá požadavky aplikace rychleji, než je aplikace může zpracovat. Zřizování konektory vytvořené společností Microsoft provést tuto úpravu. 

- Počet a velikost přiřazených skupin. Synchronizace přiřazených skupin trvá déle než synchronizace uživatelů. Počet i velikost přiřazených skupin mají vliv na výkon. Pokud má aplikace [povolená mapování pro synchronizaci objektů skupiny](customize-application-attributes.md#editing-group-attribute-mappings), budou kromě uživatelů synchronizovány také vlastnosti skupiny, jako jsou názvy skupin a členství. Tyto další synchronizace bude trvat déle než pouze synchronizace uživatelských objektů.

- Pokud se výkon stane problémem a pokoušíte se zřídit většinu uživatelů a skupin ve vašem tenantovi, použijte filtry oborů. Filtry oborů umožňují doladit data, která služba zřizování extrahuje z Azure AD filtrováním uživatelů na základě konkrétních hodnot atributů. Další informace o filtrech oborů naleznete v [tématu Zřizování aplikací založených na atributech pomocí filtrů oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Další kroky
[Automatizace zřizování uživatelů a deprovisioning u aplikací SaaS pomocí Azure Active Directory](user-provisioning.md)
