---
title: Zabezpečení Azure a řízení přístupu plán, podle kterého - FedRAMP webové aplikace automatizace – dodržování předpisů
description: FedRAMP webové aplikace automatizace – řízení přístupu
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: f7e6cd8f-b2df-4db6-8332-de97d86c5281
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 3d30f889c4d7ed0dafcf8559e8987090c03f4d5e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206834"
---
# <a name="access-control-ac"></a>Řízení přístupu (AC)

> [!NOTE]
> Tyto ovládací prvky jsou definovány NIST a USA Ministerstvo obchodu jako součást speciální publikace NIST 800-53 revize 4. Naleznete NIST 800-53 Rev. 4 informace o testování postupy a pokyny pro každý ovládací prvek.

## <a name="nist-800-53-control-ac-1"></a>Ovládací prvek NIST 800-53 AC-1

#### <a name="access-control-policy-and-procedures"></a>Zásada řízení přístupu a postupy

**AC-1** organizace sama vyvinula, dokumenty a šíří do [přiřazení: organizace definované pracovníky nebo rolí] zásad řízení přístupu, která řeší účel, oboru, role, odpovědnosti, správu závazků, spolupráce mezi organizační entity a dodržování předpisů; a postupy pro usnadnění provádění zásad řízení přístupu a řízení přístupu přidružené; kontroluje a aktualizuje aktuální zásady řízení přístupu [přiřazení: organizace definované frekvence]; a procedury řízení přístupu [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může stačit zásad řízení přístupu na úrovni celého podniku a postupy zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2a"></a>Ovládací prvek NIST 800-53 AC-2.a

#### <a name="account-management"></a>Správa účtů

**AC 2.a** organizace identifikuje a vybere následující typy informace účty systému pro podporu organizační poslání nebo podnikové funkce: [přiřazení: typy účtů systému informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého využívá a implementuje následující typy účtů systému: uživatelů Azure Active Directory (používá se k nasazení řešení a spravovat přístup k prostředkům Azure), uživatelé operačního systému Windows (spravované pomocí služby Active Directory), účet služby SQL Server. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2b"></a>Ovládací prvek NIST 800-53 AC-2.b

#### <a name="account-management"></a>Správa účtů

**AC 2.b** organizace přiřadí účet správce pro informace o systémové účty.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za přiřazení správců k účtů identifikovat v AC 02.a. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2c"></a>Ovládací prvek NIST 800-53 AC-2.c

#### <a name="account-management"></a>Správa účtů

**AC 2.c** organizace vytváří podmínky pro členství skupiny a role.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za vytvoření role a skupiny kritéria členství pro typy účtů zákazníků řízené (viz AC 02.a). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2d"></a>Ovládací prvek NIST 800-53 AC-2.d

#### <a name="account-management"></a>Správa účtů

**AC 2.d** organizace určuje oprávněným uživatelům informace o systému, skupiny a role členství a přístup k povolení (tj. oprávnění) a další atributy (podle potřeby) pro každý účet.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může spoléhají na zavedených účtu podnikové úrovni autorizačního procesu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2e"></a>Ovládací prvek NIST 800-53 AC-2.e

#### <a name="account-management"></a>Správa účtů

**AC-2.e** Organizace vyžaduje schválení podle [přiřazení: organizace definované pracovníky nebo rolí] pro požadavky na vytvoření informace systémové účty.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může spoléhají na zavedených účtu podnikové úrovni autorizačního procesu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2f"></a>Ovládací prvek NIST 800-53 AC-2.f

#### <a name="account-management"></a>Správa účtů

**AC 2.f** organizace vytvoří, povolí, upraví, zakáže a odebere účty systému informace v souladu s [přiřazení: organizace definované postupy nebo podmínky].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník využít procesu správy zavedených účtů podnikové úrovni. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2g"></a>Ovládací prvek NIST 800-53 AC-2.g

#### <a name="account-management"></a>Správa účtů

**AC 2.g** organizace monitoruje použití účtů, informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řídicí panel řešení zabezpečení a Audit přístupu a identit. Tento řídicí panel umožňuje správcům účet monitorování použití účtů, informace o systému. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2h"></a>Ovládací prvek NIST 800-53 AC-2.h

#### <a name="account-management"></a>Správa účtů

**AC 2.h** organizace upozorní správce účtu při účty se už nevyžadují; při ukončen nebo přenést; uživatelů a při používání systému jednotlivé informace nebo potřeba znát změny.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Procedury řízení přístupu na úrovni organizace zákazníka může vytvořit proces pro upozornit odpovídající účet správce, pokud účet již není potřeba. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2i"></a>Ovládací prvek NIST 800-53 AC-2.i

#### <a name="account-management"></a>Správa účtů

**AC 2.i** organizace povolí přístup k systému informace na základě platný přístup autorizace; určený systému využití; a další atributy podle požadavků organizace nebo přidružené poslání nebo podnikovým funkcím.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Procedury řízení přístupu na úrovni organizace zákazníka může vytvořit autorizačního procesu přístup. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2j"></a>Ovládací prvek NIST 800-53 AC-2.j

#### <a name="account-management"></a>Správa účtů

**AC 2.j** organizace zkontroluje účty kompatibilitu s požadavky na správu účtu [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za prostudovali řízenou zákaznické účty na požadovanou četnost k určení, zda jsou účty splňovat všechny požadavky organizace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-2k"></a>Ovládací prvek NIST 800-53 AC-2.k

#### <a name="account-management"></a>Správa účtů

**AC 2.k** organizace vytváří proces pro opětovné vystavení přihlašovací údaje účtu sdílené nebo skupiny (Pokud je nasazený) při jednotlivce se odeberou ze skupiny.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Procedury řízení přístupu na úrovni organizace zákazníka může vytvořit proces pro správu přihlašovací údaje účtu skupiny. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-1"></a>NIST 800-53 řízení AC-2 (1)

#### <a name="account-management--automated-system-account-management"></a>Správa účtů | Automatizované správy účtů systému

**AC-2 (1)** organizace využívá automatizované mechanismy pro podporu správy informace systémové účty.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řídicí panel řešení zabezpečení a Audit přístupu a identit. Tento řídicí panel umožňuje správcům účet monitorování použití účtů, informace o systému. Můžete nastavit pro odesílání upozornění, pokud podezření netypických aktivity nebo jiné předdefinované události. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-2"></a>NIST 800-53 řízení AC-2 (2)

#### <a name="account-management--removal-of-temporary--emergency-accounts"></a>Správa účtů | Odebrání dočasné / nouzový účtů

**AC-2 (2)** informace systému automaticky [výběr: Odebere; zakáže] dočasné a nouzový účty po [přiřazení: organizace definované časové období pro každý typ účtu].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého není nasazen dočasné nebo nouzový účty. Pokud není ručně zakázané, řadiče domény nasazené automaticky zakáže všechny neaktivní účty po 35 dní. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-3"></a>NIST 800-53 řízení AC-2 (3)

#### <a name="account-management--disable-inactive-accounts"></a>Správa účtů | Zakázat neaktivní účty

**AC-2 (3)** informace systému automaticky zakáže neaktivní účty po [přiřazení: organizace definované časové období].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Řadič domény nasadit pomocí tento plán, podle kterého je nakonfigurována na zakažte všechny uživatelské účty po 35 dní nečinnosti. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-4"></a>NIST 800-53 řízení AC-2 (4)

#### <a name="account-management--automated-audit-actions"></a>Správa účtů | Akce automatické auditování

**AC-2 (4)** informace systému automaticky audity akce vytváření, úpravy, povolení, zasílání zpráv o zakázání a odebrání účtu a upozorní, [přiřazení: organizace definované pracovníky nebo rolí].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje následující typy účtů systému: uživatelů Azure Active Directory, uživatelé operačního systému Windows, účet služby SQL Server. Akce správy účtu služby Azure Active Directory generovat událost v protokolu aktivit Azure; Úroveň operačního systému účtu akce správy vygenerují událost do systémového protokolu. Tyto protokoly se shromažďují analýzy protokolů a uložené v pracovním prostoru analýzy protokolů. Analýzy protokolů můžete nakonfigurovat na odesílání výstrah při výskytu události v předdefinované.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-5"></a>NIST 800-53 řízení AC-2 (5)

#### <a name="account-management--inactivity-logout"></a>Správa účtů | Odhlášení nečinnosti

**AC-2 (5)** organizace vyžaduje, aby se při přihlašování uživatelů [přiřazení: organizace definované doby nečinnosti očekávané nebo popis při odhlášení].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady řízení přístupu k podnikové úrovni zákazníka může vytvořit zásadu, která odhlášení uživatele, když jejich předvídat do neaktivního stavu pro určitou dobu (nebo jinými faktory). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-7a"></a>NIST 800-53 řízení .a AC-2 (7)

#### <a name="account-management--role-based-schemes"></a>Správa účtů | Schémata na základě rolí

**AC-2 (7) .a** organizace vytváří a spravuje privilegované uživatelské účty v souladu s schéma přístupu podle rolí, které slouží k uspořádání povolené informace systému přístup a oprávnění, do role.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje následující typy účtů systému: uživatelů Azure Active Directory, uživatelé operačního systému Windows, účet služby SQL Server. Oprávnění k účtu Azure Active Directory jsou implementované pomocí řízení přístupu na základě rolí přiřazování uživatelů do rolí; Oprávnění k účtu Active Directory jsou implementovány pomocí řízení přístupu na základě rolí přiřazování uživatelů do skupiny zabezpečení. Tato schémata na základě rolí lze rozšířit zákazník zvláště potřebám. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-7b"></a>NIST 800-53 řízení .b AC-2 (7)

#### <a name="account-management--role-based-schemes"></a>Správa účtů | Schémata na základě rolí

**AC-2 (7) .b** organizace monitoruje přiřazení privilegované role.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého se implementuje zabezpečení a Audit řešení identit a přístupu řídicího panelu. Tento řídicí panel umožňuje správcům účet monitorování použití účtů, informace o systému. Toto řešení můžete položit dotaz k hlášení přiřazení privilegované role. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-7c"></a>NIST 800-53 řízení .c AC-2 (7)

#### <a name="account-management--role-based-schemes"></a>Správa účtů | Schémata na základě rolí

**AC-2 (7) .c** organizace trvá [přiřazení: akce definované organizace] při přiřazení privilegované role již nejsou vhodné.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za provádět akce u zákazníka řízené účty při přiřazení privilegované role již nejsou vhodné. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-9"></a>NIST 800-53 řízení AC-2 (9)

#### <a name="account-management--restrictions-on-use-of-shared--group-accounts"></a>Správa účtů | Omezení použití sdílené / skupiny účtů

**AC-2 (9)** organizace pouze povolí použití sdíleného nebo skupinové účty, které splňují [přiřazení: organizace definované podmínky pro vytvoření sdílených nebo skupinové účty].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | U prostředků nasadit pomocí tento plán, podle kterého jsou povolené žádné sdílené nebo skupinové účty. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-10"></a>NIST 800-53 řízení AC-2 (10)

#### <a name="account-management--shared--group-account-credential-termination"></a>Správa účtů | Ukončení přihlašovací údaje účtu sdílené / skupiny

**AC-2 (10)** informace systému ukončit sdílené nebo skupiny pověření při členy nechte skupinu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | U prostředků nasadit pomocí tento plán, podle kterého jsou povolené žádné sdílené nebo skupinové účty. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-11"></a>NIST 800-53 řízení AC-2 (11)

#### <a name="account-management--usage-conditions"></a>Správa účtů | Podmínky použití

**AC-2 (11)** vynucuje systémové informace [přiřazení: organizace definované okolností nebo podmínky použití] pro [přiřazení: účty systému informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řadič domény, ke kterému jsou připojené všechny nasazené virtuální počítače. Zásady skupiny se dá zřídit ve službě Active Directory a konfigurovat pro implementaci omezení hodiny nebo jinými podmínkami použití účtu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-12a"></a>NIST 800-53 řízení .a AC-2 (12)

#### <a name="account-management--account-monitoring--atypical-usage"></a>Správa účtů | Účet monitorování / Netypických využití

**AC-2 (12) .a** organizace sleduje informace o systémových účtů pro [přiřazení: organizace definované netypické využití].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řídicí panel řešení zabezpečení a Audit přístupu a identit. Tento řídicí panel umožňuje správcům účet monitorování přístupu pokusy o přihlášení nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-12b"></a>NIST 800-53 řízení .b AC-2 (12)

#### <a name="account-management--account-monitoring--atypical-usage"></a>Správa účtů | Účet monitorování / Netypických využití

**AC-2 (12) .b** organizace sestavy netypických využití účty systému informace [přiřazení: definované organizace pracovníky nebo rolí].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řídicí panel řešení zabezpečení a Audit přístupu a identit. Tento řídicí panel umožňuje správcům účet monitorování přístupu pokusy o přihlášení nasazené prostředky. Toto řešení můžete nakonfigurovat k odesílání upozornění, pokud podezření netypických aktivity nebo jiné předdefinované události. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-2-13"></a>NIST 800-53 řízení AC-2 (13)

#### <a name="account-management--disable-accounts-for-high-risk-individuals"></a>Správa účtů | Zakázat účty pro jednotlivce s vysokým rizikem

**AC-2 (13)** zakáže účty uživatelů autority významné riziko v rámci organizace [přiřazení: organizace definované časové období] zjišťování riziko.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Podmínky pro zakázání účtů pro uživatele, která představuje významné riziko pro organizaci může vytvořit zásady řízení přístupu k podnikové úrovni zákazníka a postupy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-3"></a>Ovládací prvek NIST 800-53 AC-3

#### <a name="access-enforcement"></a>Přístup k vynucení

**AC 3** vynucuje schválené autorizací pro logického přístupu k informace a systémové prostředky v souladu s zásady řízení přístupu na příslušné informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého vynucuje autorizací logického přístupu pomocí řízení přístupu na základě rolí vynucuje přiřadit uživatele k role, služby Active Directory přiřadit uživatele do skupiny zabezpečení Azure Active Directory a ovládací prvky Windows úrovni operačního systému. Azure Active Directory role přiřazené k uživatelům nebo skupinám řídit logického přístupu k prostředkům v rámci Azure na úrovni prostředku, skupiny nebo předplatné. Skupiny zabezpečení Active Directory řídit logického přístupu k prostředkům na úrovni operačního systému a funkcí. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-4"></a>Ovládací prvek NIST 800-53 AC – 4

#### <a name="information-flow-enforcement"></a>Informace o toku vynucení

**AC 4** vynucuje schválené oprávnění pro řízení toku informací v rámci systému a mezi propojenými systémy založené na systému informace [přiřazení: zásady řízení toku informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého vynucuje omezení toku informací prostřednictvím skupin zabezpečení sítě použít na podsítě, ve kterých jsou nasazené prostředky, aplikační bránu a nástroj pro vyrovnávání zatížení. Skupiny zabezpečení sítě zkontrolujte, že je řízený tok informací mezi prostředky na základě pravidel, schválené. Aplikace brány a zatížení nástroje pro vyrovnávání dynamické směrovat provoz ke konkrétním prostředkům na základě schválené rolí. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-4-8"></a>NIST 800-53 řízení AC-4 (8)

#### <a name="information-flow-enforcement--security-policy-filters"></a>Informace o toku vynucení | Filtry zásad zabezpečení

**AC-4 (8)** informace systému vynucuje informace o toku řízení pomocí [přiřazení: filtry zásad zabezpečení organizace definované] jako základ pro rozhodnutí o toku řízení pro [přiřazení: toky informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za vynucení řízení toku informací v rámci zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-4-21"></a>NIST 800-53 řízení AC-4 (21)

#### <a name="information-flow-enforcement--physical--logical-separation-of-information-flows"></a>Informace o toku vynucení | Fyzické / logického oddělení toků informace

**AC-4 (21)** informace systému odděluje informace toky logicky nebo fyzicky pomocí [přiřazení: mechanismy definované organizace nebo techniky] provedete [přiřazení: organizace definované požadované separace typy informace o].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za oddělení informace toků v rámci zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-5a"></a>Ovládací prvek NIST 800-53 AC-5.a

#### <a name="separation-of-duties"></a>Oddělení povinností

**AC 5.a** organizace odděluje [přiřazení: organizace definované povinností jednotlivců].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za oddělení povinností mezi účty řízenou zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-5b"></a>Ovládací prvek NIST 800-53 AC-5.b

#### <a name="separation-of-duties"></a>Oddělení povinností

**AC 5.b** organizace dokumenty oddělení povinností jednotlivce.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za dokumentaci oddělení povinností mezi účty řízenou zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-5c"></a>Ovládací prvek NIST 800-53 AC-5.c

#### <a name="separation-of-duties"></a>Oddělení povinností

**AC 5.c** organizace definuje informace systému přístup autorizací pro podporu rozdělení povinností.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řízení přístupu na základě role, které lze nakonfigurovat k oddělení povinností podle požadavků organizace. Oprávnění k účtu Azure Active Directory jsou implementované pomocí řízení přístupu na základě rolí přiřazování uživatelů do rolí; Oprávnění k účtu Active Directory jsou implementovány pomocí řízení přístupu na základě rolí přiřazování uživatelů do skupiny zabezpečení. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-6"></a>Ovládací prvek NIST 800-53 AC-6

#### <a name="least-privilege"></a>Nejnižší oprávnění

**AC 6** organizace využívá princip nejnižších nutných oprávnění, umožňuje pouze oprávněných přístupů pro uživatele (nebo procesy, které jednají jménem uživatelů), které jsou nezbytné k provádění úloh přiřazené v souladu s organizační poslání a podnikovým funkcím.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řízení přístupu na základě rolí k omezení uživatelů na pouze oprávnění explicitně přiřazeny. Oprávnění k účtu Azure Active Directory jsou implementované pomocí řízení přístupu na základě rolí přiřazování uživatelů do rolí; Oprávnění k účtu Active Directory jsou implementovány pomocí řízení přístupu na základě rolí přiřazování uživatelů do skupiny zabezpečení.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-6-1"></a>NIST 800-53 řízení AC – 6 (1)

#### <a name="least-privilege--authorize-access-to-security-functions"></a>Nejnižší oprávnění | Autorizace přístupu k funkcím zabezpečení

**AC – 6 (1)** organizace výslovně povoluje přístup k [přiřazení: zabezpečení organizace definované funkce (nasazený v hardware, software a firmware) a zabezpečení relevantní informace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Procedury řízení přístupu na úrovni organizace zákazníka může vytvořit proces autorizace přístupu, která zahrnuje přístup k funkcím zabezpečení. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-6-2"></a>NIST 800-53 řízení AC – 6 (2)

#### <a name="least-privilege--non-privileged-access-for-nonsecurity-functions"></a>Nejnižší oprávnění | Bez oprávnění přístupu pro funkce netýká se zabezpečení

**AC – 6 (2)** organizace vyžaduje, aby uživatelé informace systému účtů nebo role, přístup k [přiřazení: funkce definované organizace zabezpečení nebo zabezpečení relevantní informace], použít účty bez oprávnění nebo role, při přístup k funkce netýká se zabezpečení.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady řízení přístupu k podnikové úrovni zákazníka může vyžadovat uživatelům používat účty bez oprávnění pro přístup k funkce netýká se zabezpečení. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-6-3"></a>NIST 800-53 řízení AC – 6 (3)

#### <a name="least-privilege--network-access-to-privileged-commands"></a>Nejnižší oprávnění | Přístup k síti privilegované příkazy

**AC – 6 (3)** organizace povolí přístup k síti na [přiřazení: organizace definovaný privilegovaný příkazy] pouze pro [přiřazení: organizace definované přesvědčivé provozní potřeby] a dokumenty důvody tohoto přístupu v plánování zabezpečení systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady řízení přístupu k podnikové úrovni zákazníka může definovat privilegované příkazy, které mohly být přístupné přes síť. Poznámka: Zákazníci mít žádný fyzický přístup k infrastruktury Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-6-5"></a>NIST 800-53 řízení AC – 6 (5)

#### <a name="least-privilege--privileged-accounts"></a>Nejnižší oprávnění | Privilegované účty

**AC – 6 (5)** organizace omezuje privilegovaných účtů v systému informace [přiřazení: organizace definované pracovníky nebo rolí].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady řízení přístupu k podnikové úrovni zákazníka může definovat omezení pro použití privilegovaných účtů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-6-7a"></a>NIST 800-53 řízení .a AC 6 (7)

#### <a name="least-privilege--review-of-user-privileges"></a>Nejnižší oprávnění | Zkontrolujte oprávnění uživatele

**.A AC – 6 (7)** recenze organizace [přiřazení: organizace definované frekvence] oprávnění přiřazená [přiřazení: organizace definované role nebo třídy uživatelů] k ověření potřeba taková oprávnění.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za kontrola uživatelská oprávnění řídí zákazníka účtů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-6-7b"></a>NIST 800-53 řízení .b AC 6 (7)

#### <a name="least-privilege--review-of-user-privileges"></a>Nejnižší oprávnění | Zkontrolujte oprávnění uživatele

**AC – 6 (7) .b** organizace pouze Změna přiřazení nebo odebere oprávnění, pokud je to nezbytné, aby se správně odrážela organizační zvláště nebo obchodní potřeby.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za nové přiřazení nebo odebrání oprávnění pro účty řízenou zákazníka v případě nutnosti. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-6-8"></a>NIST 800-53 řízení AC – 6 (8)

#### <a name="least-privilege--privilege-levels-for-code-execution"></a>Nejnižší oprávnění | Úrovně oprávnění pro provádění kódu

**AC – 6 (8)** informace brání [přiřazení: softwaru definované organizace] spuštění na vyšší úrovni oprávnění než uživatelé provádění softwaru.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řízení přístupu na základě rolí k omezení uživatelů na pouze oprávnění explicitně přiřazeny. Virtuální počítač OS úroveň ochrany neumožňují softwaru provést na vyšší úrovni oprávnění než uživatelé provádění softwaru. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-6-9"></a>NIST 800-53 řízení AC – 6 (9)

#### <a name="least-privilege--auditing-use-of-privileged-functions"></a>Nejnižší oprávnění | Auditování použití privilegovaných funkce

**AC – 6 (9)** informace systému audity provádění privilegovaných funkce.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje službu analýzy protokolů. Nasadit virtuální počítače a Azure diagnostics účty úložiště jsou připojené zdroje pro analýzy protokolů zajistíte, že je auditováno provádění privilegovaných funkcí. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-6-10"></a>NIST 800-53 řízení AC – 6 (10)

#### <a name="least-privilege--prohibit-non-privileged-users-from-executing-privileged-functions"></a>Nejnižší oprávnění | Provádění privilegovaných funkce zakázat uživatelům bez oprávnění

**AC – 6 (10)** systému informací zabraňuje uživatelům bez oprávnění v provádění privilegovaných funkce: zákaz, obcházení, nebo změnu implementována zabezpečení bezpečnostní opatření nebo protiopatření.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řízení přístupu na základě rolí k omezení uživatelů na pouze oprávnění explicitně přiřazeny.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-7a"></a>Ovládací prvek NIST 800-53 AC-7a

#### <a name="unsuccessful-logon-attempts"></a>Pokusy o neúspěšných přihlášení

**AC 7a** informace systému se vynucuje maximální počet [přiřazení: organizace definované číslo] po sobě jdoucích neplatné pokusy o přihlášení uživatelem během [přiřazení: organizace definované časové období].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Portálu Azure limity po sobě jdoucích neplatné pokusy o přihlášení uživatelů. Zásady skupiny se použije na úrovni operačního systému pro všechny virtuální počítače nasazené pomocí tento plán. Zásady omezení po sobě jdoucích neplatných pokusů o přihlášení uživatelé na více než tři v období 15 minut. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-7b"></a>Ovládací prvek NIST 800-53 AC-7.b

#### <a name="unsuccessful-logon-attempts"></a>Pokusy o neúspěšných přihlášení

**AC 7.b** informace systému automaticky [výběr: zamkne účet nebo uzel pro [přiřazení: organizace definované časové období]; zamkne účet nebo uzel, dokud vydání správcem; zpozdí další výzva k přihlášení podle [Přiřazení: algoritmus definované organizace zpoždění]] při překročení maximální počet neúspěšných pokusů.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Portál Azure zamkne účty po po sobě jdoucích neplatné pokusy o přihlášení uživatelů. Zásady skupiny se použije na úrovni operačního systému pro všechny virtuální počítače nasazené pomocí tento plán. Zásady zamkne účty pro tři hodiny po tři po sobě jdoucích neplatných pokusů o přihlášení uživatelů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-7-2"></a>NIST 800-53 řízení AC-7 (2)

#### <a name="unsuccessful-logon-attempts--purge--wipe-mobile-device"></a>Pokusy o neúspěšných přihlášení | Vyprázdnění / vymazat mobilní zařízení

**AC-7 (2)** informace systému vyprazdňovat nebo vymazání informace z [přiřazení: organizace definované mobilní zařízení] na základě [přiřazení: organizace definované vyprazdňování nebo vymazání požadavky nebo techniky] po [přiřazení: pokusy o přihlášení po sobě jdoucích neúspěšných zařízení organizace definované číslo].

**Odpovědnosti:** `Not Applicable`

|||
|---|---|
| **Zákazníka** | Mobilní zařízení nejsou v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nepovoluje mobilní zařízení v rámci Azure hranice. Tento ovládací prvek jako takový není použitelná pro Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-8a"></a>Ovládací prvek NIST 800-53 AC-8.a

#### <a name="system-use-notification"></a>Oznámení pomocí systému

**AC 8.a** zobrazí systém informace pro uživatele [přiřazení: systém definované organizace používat oznámení nebo informační zpráva] před udělením přístupu k systému, který poskytuje ochranu osobních údajů a zabezpečení oznámení, které jsou konzistentní s použít federální právní Executive objednávky, direktivy, zásady, standardy a pokyny a stavy, uživatelé přistupují USA Informace o systému Government; využití systémové informace může být monitorovaná, zaznamenané a auditování; neoprávněné použití systému informace je zakázané a souladu trestního a občanského práva; a použití systému informace označuje souhlasu sledování a záznamu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řadič domény, ke kterému jsou připojené všechny nasazené virtuální počítače. Zásady skupiny implementuje oznámení pomocí systému, které se zobrazí uživatelům před přihlášení. Poznámka: Tento plán, podle kterého implementuje e příklad systému použijte oznámení. Zákazník musí upravit tento text ke splnění organizace nebo regulačních textu požadavky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-8b"></a>Ovládací prvek NIST 800-53 AC-8.b

#### <a name="system-use-notification"></a>Oznámení pomocí systému

**AC 8.b** informace systému zachová oznámení nebo informační zpráva na obrazovce, dokud uživatelé vědomí podmínky použití a explicitní kroky k přihlášení k počítači nebo Další informace o systému přístup.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řadič domény, ke kterému jsou připojené všechny nasazené virtuální počítače. Zásady skupiny implementuje oznámení pomocí systému, které se zobrazí uživatelům před přihlášení. Uživatel musí potvrdit oznámení tak, aby přihlásit. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-8c"></a>Ovládací prvek NIST 800-53 AC-8.c

#### <a name="system-use-notification"></a>Oznámení pomocí systému

**AC 8.c** systému informace veřejně přístupná systémů zobrazí informace o použití systému [přiřazení: organizace definované podmínky], před udělením přístupu Další; zobrazí odkazy, pokud existuje, sledování, záznamu, nebo auditování, které jsou konzistentní s přizpůsobení ochrany osobních údajů pro tyto systémy, které obvykle zakázat tyto aktivity; a obsahuje popis povolená použití systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Při zobrazování oznámení pomocí systému na všechny veřejně dostupné prostředky nasazené zákazníka zodpovídá zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-10"></a>Ovládací prvek NIST 800-53 AC-10

#### <a name="concurrent-session-control"></a>Ovládací prvek souběžných relací

**AC 10** informace systému omezuje počet souběžných relací pro každou [přiřazení: organizace definovaný účet nebo typ účtu] na [přiřazení: organizace definované číslo].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Pro virtuální počítače nasazené pomocí tento plán, podle kterého jsou implementovány zásady operačního systému. Zásady implementuje omezení souběžných relací (dvě relace). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-11a"></a>Ovládací prvek NIST 800-53 AC-11.a

#### <a name="session-lock"></a>Uzamčení relace

**AC 11.a** informace brání další přístupu k systému pomocí inicializace relace uzamčení po [přiřazení: organizace definované časové období] nečinnosti nebo po přijetí žádosti od uživatele.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řadič domény, ke kterému jsou připojené všechny nasazené virtuální počítače. Zásady skupiny implementuje zámek nečinnosti relace protokolu RDP. Uživatelé mohou ručně zahájit zámek. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-11b"></a>Ovládací prvek NIST 800-53 AC-11.b

#### <a name="session-lock"></a>Uzamčení relace

**AC 11.b** informace systému zachová zámek relace, dokud uživatel obnoví přístup pomocí stanovené postupy identifikace a ověření.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řadič domény, ke kterému jsou připojené všechny nasazené virtuální počítače. Zásady skupiny implementuje zámek nečinnosti relace protokolu RDP. Uživatelé musí novému ověření, k odemčení relace.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-11-1"></a>NIST 800-53 řízení AC-11 (1)

#### <a name="session-lock--pattern-hiding-displays"></a>Uzamčení relace | Zobrazí vzor skrytí

**AC-11 (1)** informace systému ukrývá prostřednictvím relace zámek informace dříve viditelné na displeji s veřejně viditelný obrázek.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řadič domény, ke kterému jsou připojené všechny nasazené virtuální počítače. Zásady skupiny implementuje zámek nečinnosti relace protokolu RDP. Zámek relace ukrývá informace dříve viditelné. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-12"></a>Ovládací prvek NIST 800-53 AC-12

#### <a name="session-termination"></a>Ukončení relace

**AC – 12** informace systém automaticky ukončí relace uživatele po [přiřazení: organizace definované podmínky nebo aktivační události vyžadující relace odpojení].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Konfigurace hostitele relací vzdálené plochy pro virtuální počítače s Windows nasadit pomocí tento plán, podle kterého se dá nakonfigurovat k ukončení relace požadavkům organizace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-12-1a"></a>((1) .a AC – 12 NIST 800-53 ovládací prvek

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Ukončení relace | Uživatel spustil Logouts / zpráva zobrazí

**AC – 12 (1) .a** systému informace poskytuje funkci odhlášení pro komunikaci uživatel spustil relace vždy, když je ověřování slouží k získání přístupu k [přiřazení: prostředky informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Azure portal a virtuální počítač operační systémy nasadit pomocí tento plán, podle kterého povolit používá k zahájení odhlášení. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-12-1b"></a>((1) .b AC – 12 NIST 800-53 ovládací prvek

#### <a name="session-termination--user-initiated-logouts--message-displays"></a>Ukončení relace | Uživatel spustil Logouts / zpráva zobrazí

**AC – 12 (1) .b** systému informace zobrazí zprávu explicitní odhlášení uživatelům označující spolehlivé ukončení relací ověřené komunikaci.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Azure portal a virtuální počítač operační systémy nasadit pomocí tento plán, podle kterého povolit používá k zahájení odhlášení. Proces odhlášení indikaci uživatelům, aby byla relace ukončena. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-14a"></a>Ovládací prvek NIST 800-53 AC-14.a

#### <a name="permitted-actions-without-identification-or-authentication"></a>Povolených akcí bez identifikace a ověřování

**AC 14.a** identifikuje organizace [přiřazení: akcí uživatele organizaci definované], lze provést v systému informace bez identifikace a ověřování, které jsou konzistentní s organizační poslání nebo podnikovým funkcím.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za identifikaci akce, které lze provést na zákazníka nasazené prostředkům bez identifikace a ověřování (např. například zobrazení veřejně přístupná webové stránky). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-14b"></a>Ovládací prvek NIST 800-53 AC-14.b

#### <a name="permitted-actions-without-identification-or-authentication"></a>Povolených akcí bez identifikace a ověřování

**AC 14.b** organizace dokumenty a poskytuje podpůrné odůvodnění plánu zabezpečení informace systému, akcí uživatele, které nevyžadují identifikace nebo ověřování.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za poskytování dokumentace pro uživatele akce, které nevyžadují identifikace nebo ověřování na zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-17a"></a>Ovládací prvek NIST 800-53 AC-17.a

#### <a name="remote-access"></a>Vzdálený přístup

**AC 17.a** organizace vytváří a dokumenty omezení použití, požadavky konfigurace nebo připojení a implementace pokyny pro každý typ vzdáleného přístupu povolena.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady řízení přístupu k podnikové úrovni zákazníka může definovat omezení použití vzdáleného přístupu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-17b"></a>Ovládací prvek NIST 800-53 AC-17.b

#### <a name="remote-access"></a>Vzdálený přístup

**AC 17.b** organizace autorizuje vzdálený přístup k systému informace před povolením takové připojení.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Procedury řízení přístupu na úrovni organizace zákazníka může vytvořit proces autorizace vzdáleného přístupu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-17-1"></a>NIST 800-53 řízení AC-17 (1)

#### <a name="remote-access--automated-monitoring--control"></a>Vzdálený přístup | Automatizované monitorování / ovládací

**AC-17 (1)** informace systému sleduje a řídí metody vzdáleného přístupu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého poskytuje vzdálený přístup k systému informace prostřednictvím portálu Azure prostřednictvím připojení ke vzdálené ploše prostřednictvím jumpbox a prostřednictvím implementované zákazníka webové aplikace. Přistupuje prostřednictvím portálu Azure a relací vzdálené plochy se auditují a je možné monitorovat prostřednictvím analýzy protokolů. Zákazník musí implementovat ovládací prvky vzdáleného přístupu, jako nezbytné, aby webové aplikace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-17-2"></a>NIST 800-53 řízení AC-17 (2)

#### <a name="remote-access--protection-of-confidentiality--integrity-using-encryption"></a>Vzdálený přístup | Ochranu důvěrnost / Integrity pomocí šifrování

**AC-17 (2)** systém informace implementuje kryptografických mechanismů chránit důvěrnost a integritu relací vzdáleného přístupu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Vzdálený přístup k prostředkům nasadit pomocí tento plán, včetně portálu Azure, připojení ke vzdálené ploše a webové aplikace brány, jsou zabezpečené pomocí protokolu TLS. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-17-3"></a>NIST 800-53 řízení AC-17 (3)

#### <a name="remote-access--managed-access-control-points"></a>Vzdálený přístup | Spravované body řízení přístupu

**AC-17 (3)** informace systému směruje všechny vzdálené přistupuje prostřednictvím [přiřazení: organizace definované číslo] spravované řízení přístupových bodů sítě.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Vzdálený přístup k fiktivní webové aplikace nasadit pomocí tento plán, podle kterého je prostřednictvím služby application gateway. Vzdálený přístup k jiným prostředkům, je prostřednictvím jumpbox. Neexistují žádné veřejně přístupná koncové body. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-17-4a"></a>NIST 800-53 řízení .a AC 17 (4)

#### <a name="remote-access--privileged-commands--access"></a>Vzdálený přístup | Privilegovaný příkazy / přístup

**AC-17 (4) .a** organizace autorizuje provádění privilegovaných příkazů a přístup k zabezpečení relevantní informace prostřednictvím vzdáleného přístupu pouze pro [přiřazení: organizace definované vyžaduje].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady řízení přístupu k podnikové úrovni zákazníka může definovat privilegované příkazy, které může vzdálený přístup a zahrnout odůvodnění. Poznámka: Zákazníci mít žádný přímý přístup k síti na infrastrukturu Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-17-4b"></a>NIST 800-53 řízení (4) .b AC-17

#### <a name="remote-access--privileged-commands--access"></a>Vzdálený přístup | Privilegovaný příkazy / přístup

**AC-17 (4) .b** organizace dokumenty důvody tohoto přístupu v plánu zabezpečení pro systém informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady řízení přístupu k podnikové úrovni zákazníka může definovat privilegované příkazy, které může vzdálený přístup a zahrnout odůvodnění. Poznámka: Zákazníci mít žádný přímý přístup k síti na infrastrukturu Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-17-9"></a>NIST 800-53 řízení AC-17 (9)

#### <a name="remote-access--disconnect--disable-access"></a>Vzdálený přístup | Odpojit / zakázat přístup

**AC-17 (9)** poskytuje možnost rychle odpojit nebo zakázat vzdálený přístup k systému informace v rámci organizace [přiřazení: organizace definované časové období].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého poskytuje vzdálený přístup k systému informace prostřednictvím portálu Azure, prostřednictvím připojení ke vzdálené ploše prostřednictvím jumpbox a ve webové aplikaci. Pokud účet služby Azure Active Directory je vypnuto nebo odstraněno, Azure přístup k portálu je odpojen okamžitě. Podobně pokud účet úrovni operačního systému virtuálního počítače je vypnuto nebo odstraněno, přístup ke vzdálené ploše prostřednictvím jumpbox je odpojen okamžitě. Zákazníci musí implementovat vzdáleného přístupu odpojit pro webové aplikace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-18a"></a>Ovládací prvek NIST 800-53 AC-18.a

#### <a name="wireless-access"></a>Bezdrátový přístup

**AC 18.a** organizace vytváří omezení použití, požadavky konfigurace nebo připojení a implementace pokyny pro bezdrátový přístup.

**Odpovědnosti:** `Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici bezdrátový přístup v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure vytvoří omezení použití, požadavky konfigurace nebo připojení a implementace pokyny pro bezdrátový přístup přes standardní zabezpečení sítě, které explicitně zakáže použití bezdrátové v prostředí Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-18b"></a>Ovládací prvek NIST 800-53 AC-18.b

#### <a name="wireless-access"></a>Bezdrátový přístup

**AC 18.b** organizace autorizuje bezdrátový přístup k systému informace před povolením takové připojení.

**Odpovědnosti:** `Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici bezdrátový přístup v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nepovoluje bezdrátový přístup v rámci datová centra služby Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-1"></a>NIST 800-53 řízení AC až 18 (1)

#### <a name="wireless-access--authentication-and-encryption"></a>Bezdrátový přístup | Ověřování a šifrování

**AC 18 (1)** informace systému chrání bezdrátový přístup k systému prostřednictvím ověřování [výběr (jeden nebo více): uživatelé; zařízení] a šifrování.

**Odpovědnosti:** `Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici bezdrátový přístup v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nepovoluje bezdrátový přístup v rámci prostředí Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-3"></a>NIST 800-53 řízení AC až 18 (3)

#### <a name="wireless-access--disable-wireless-networking"></a>Bezdrátový přístup | Zakázat bezdrátové sítě

**AC 18 (3)** zakáže organizace, pokud není určena pro použití, bezdrátové sítě možnosti interně vloženým do komponent systému informace před vystavování a nasazení.

**Odpovědnosti:** `Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici bezdrátový přístup v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nepovoluje bezdrátový přístup v rámci prostředí Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-4"></a>NIST 800-53 řízení AC až 18 (4)

#### <a name="wireless-access--restrict-configurations-by-users"></a>Bezdrátový přístup | Omezit konfigurace uživatelé

**AC 18 (4)** organizace identifikuje a explicitně autorizuje uživatele, které mohou nezávisle konfigurovat bezdrátové síťové služby.

**Odpovědnosti:** `Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici bezdrátový přístup v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nepovoluje bezdrátový přístup v rámci prostředí Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-18-5"></a>NIST 800-53 řízení AC až 18 (5)

#### <a name="wireless-access--antennas--transmission-power-levels"></a>Bezdrátový přístup | Antény / úrovně Power přenosu

**AC 18 (5)** organizace vybere antény přepínačů a kalibruje přenosu úrovní snížit pravděpodobnost, že použitelné signál lze zachytit mimo organizaci řízené hranice.

**Odpovědnosti:** `Azure Only`

|||
|---|---|
| **Zákazníka** | Není k dispozici bezdrátový přístup v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nepovoluje bezdrátový přístup v rámci prostředí Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19a"></a>Ovládací prvek NIST 800-53 AC-19.a

#### <a name="access-control-for-mobile-devices"></a>Řízení přístupu pro mobilní zařízení

**AC 19.a** organizace vytváří omezení použití, požadavky na konfiguraci, požadavky na připojení a implementace pokyny pro organizaci řízené mobilní zařízení.

**Odpovědnosti:** `Not Applicable`

|||
|---|---|
| **Zákazníka** | Nejsou řízené zákazníka mobilních zařízení v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nepovoluje mobilní zařízení v rámci Azure hranice. Tento ovládací prvek jako takový není použitelná pro Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-19b"></a>Ovládací prvek NIST 800-53 AC-19.b

#### <a name="access-control-for-mobile-devices"></a>Řízení přístupu pro mobilní zařízení

**AC 19.b** organizace povolí připojení mobilních zařízení a systémy organizační údaje.

**Odpovědnosti:** `Not Applicable`

|||
|---|---|
| **Zákazníka** | Nejsou řízené zákazníka mobilních zařízení v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nepovoluje mobilní zařízení v rámci Azure hranice. Tento ovládací prvek jako takový není použitelná pro Microsoft Azure. |


 ### <a name="nist-800-53-control-ac-19-5"></a>NIST 800-53 řízení AC-19 (5)

#### <a name="access-control-for-mobile-devices--full-device--container-based--encryption"></a>Řízení přístupu pro mobilní zařízení | Úplné zařízení / kontejneru na základě šifrování

**AC-19 (5)** organizace využívá [výběr: šifrování kontejneru; šifrování zařízení úplné] chránit důvěrnost a integritu informace na [přiřazení: organizace definované mobilní zařízení].

**Odpovědnosti:** `Not Applicable`

|||
|---|---|
| **Zákazníka** | Nejsou řízené zákazníka mobilních zařízení v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure nepovoluje mobilní zařízení v rámci Azure hranice. Tento ovládací prvek jako takový není použitelná pro Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-20a"></a>Ovládací prvek NIST 800-53 AC-20.a

#### <a name="use-of-external-information-systems"></a>Pomocí externích informačních systémů

**AC 20.a** organizace vytváří podmínky a ujednání, které jsou konzistentní s žádný vztah důvěry s dalšími organizacemi, který je vlastníkem, provozování, nebo udržování systémů externí informace, což autorizované jednotlivce pro přístup k systému informace z externích informačních systémů.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady řízení přístupu k podnikové úrovni zákazníka může zahrnovat zřídit na použití cloudové nabídek služeb v rámci FedRAMP. Azure má uděleno dočasné povolení k provozu (P-ATO) pomocí FedRAMP Smíšeného autorizace Tabule (JAB) povolíte pořízení používání cloudové služby Azure podle organizace státní správy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-20b"></a>Ovládací prvek NIST 800-53 AC-20.b

#### <a name="use-of-external-information-systems"></a>Pomocí externích informačních systémů

**AC 20.b** organizace vytváří podmínky a ujednání, které jsou konzistentní s žádný vztah důvěry s dalšími organizacemi, který je vlastníkem, provozování, nebo udržování systémů externí informace, což autorizované jednotlivce ke zpracování, ukládání nebo přenášet informace řízenou organizace pomocí externích informačních systémů.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady řízení přístupu k podnikové úrovni zákazníka může zahrnovat zřídit na použití cloudové nabídek služeb v rámci FedRAMP. Azure má uděleno dočasné povolení k provozu (P-ATO) pomocí FedRAMP Smíšeného autorizace Tabule (JAB) povolíte pořízení používání cloudové služby Azure podle organizace státní správy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-20-1"></a>NIST 800-53 řízení AC-20 (1)

#### <a name="use-of-external-information-systems--limits-on-authorized-use"></a>Použití systémů externí informace | Omezení pro oprávněné použití

**AC-20 (1)** organizace umožňuje autorizované jednotlivce používat systém externí informace pro přístup k systému informace nebo ke zpracování, ukládání nebo přenášet řízenou organizace informace jenom v případě, že ověřuje organizace implementace ovládacích prvků požadované bezpečnostní v externím systému, jak je uvedeno v zásadách zabezpečení organizace informace a plánu zabezpečení; nebo uchovává schválené informace systému připojení nebo zpracování smluv s organizační entity hostování externí informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Skupina technologie informace na podnikové úrovni zákazníka může ověření shody poskytovatele cloudové služby s požadavky organizace informace o zabezpečení a udělení souhlasu celopodnikové používat nabídky služeb přidružený cloud. Azure má uděleno dočasné povolení k provozu (P-ATO) pomocí FedRAMP Smíšeného autorizace Tabule (JAB). Azure bude posouzeno organizací assessment schválení FedRAMP třetích stran (3PAO) k ověření shody s FedRAMP řízení zabezpečení a další požadavky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ac-20-2"></a>NIST 800-53 řízení AC-20 (2)

#### <a name="use-of-external-information-systems--portable-storage-devices"></a>Použití systémů externí informace | Přenosná úložná zařízení

**AC-20 (2)** organizace [výběr: omezuje; znemožňuje] použití řízené organizace přenosné úložné zařízení podle autorizované jednotlivce na externí informace o systémech.

**Odpovědnosti:** `Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft neumožňuje řízenou zákazníka přenosné úložné zařízení v rámci prostředí Microsoft Azure. |


 ## <a name="nist-800-53-control-ac-21a"></a>Ovládací prvek NIST 800-53 AC-21.a

#### <a name="information-sharing"></a>Sdílení informací

**AC 21.a** organizace usnadňuje tím, že umožňuje autorizovaným uživatelům zjistil, zda povolení přístupu přiřazené sdílení partnera shodují omezení přístupu na informace o sdílení informací [přiřazení: organizace informace definované sdílení případech, kdy je potřeba uživatel v případě potřeby].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady řízení přístupu k podnikové úrovni zákazníka mohou obsahovat ustanovení týkající se sdílení informací. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-21b"></a>Ovládací prvek NIST 800-53 AC-21.b

#### <a name="information-sharing"></a>Sdílení informací

**AC 21.b** organizace využívá [přiřazení: organizace definované automatizovaných mechanismy nebo manuální procesy] uživatelům pomoct rozhodnutími sdílení nebo spolupráce informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může spoléhají na podnikové úrovni informace sdílení schopností podporu rozhodnutí. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-22a"></a>Ovládací prvek NIST 800-53 AC-22.a

#### <a name="publicly-accessible-content"></a>Veřejně přístupná obsahu

**AC 22.a** organizace označí jednotlivce oprávnění ke zveřejnění informace veřejně dostupné informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Procedury řízení přístupu na úrovni organizace zákazníka může určit jednotlivce oprávnění ke zveřejnění veřejně dostupné informace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-22b"></a>Ovládací prvek NIST 800-53 AC-22.b

#### <a name="publicly-accessible-content"></a>Veřejně přístupná obsahu

**AC 22.b** organizace nastaví autorizované jednotlivce zajistit, že veřejně přístupné informace neobsahuje neveřejní informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může spoléhají na podnikové úrovni školení pro jednotlivce oprávnění ke zveřejnění veřejně dostupné informace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-22c"></a>Ovládací prvek NIST 800-53 AC-22.c

#### <a name="publicly-accessible-content"></a>Veřejně přístupná obsahu

**AC 22.c** organizace zkontroluje navrhované obsah informace před příspěvků na systém veřejně přístupné informace tak, aby neveřejní informace dosud nejsou zahrnuty.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Procedury řízení přístupu na úrovni organizace zákazníka může vytvořit proces kontroly obsahu navržená k odeslat systému veřejně přístupná. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ac-22d"></a>Ovládací prvek NIST 800-53 AC-22.d

#### <a name="publicly-accessible-content"></a>Veřejně přístupná obsahu

**AC 22.d** organizace zkontroluje obsah na veřejně přístupný informace systému neveřejní informace [přiřazení: organizace definované frekvence] a odebere takové informace, pokud vyhledat.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Procedury řízení přístupu na úrovni organizace zákazníka může vytvořit proces pro pravidelné kontroly obsahu odeslány na veřejně přístupný systémy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |

