---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP webové aplikace automatizace – Configuration Management
description: FedRAMP webové aplikace automatizace – Configuration Management.
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 5b953c0d-236f-4b61-b2c5-df2199490c73
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 356eccac6af2780c02d1cd935d41891b5f89f1a2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31421443"
---
# <a name="configuration-management-cm"></a>Správa konfigurací (CM)

> [!NOTE]
> Tyto ovládací prvky jsou definovány NIST a USA Ministerstvo obchodu jako součást speciální publikace NIST 800-53 revize 4. Naleznete NIST 800-53 Rev. 4 informace o testování postupy a pokyny pro každý ovládací prvek.

## <a name="nist-800-53-control-cm-1"></a>Ovládací prvek NIST 800-53 CM-1

#### <a name="configuration-management-policy-and-procedures"></a>Konfigurace zásad správy a postupy

**CM-1** organizace sama vyvinula, dokumenty a šíří do [přiřazení: organizace definované pracovníky nebo rolí] zásadu správy konfigurace, které řeší účel, oboru, role, odpovědnosti, závazků správy spolupráce mezi organizační entity a dodržování předpisů; a postupy pro usnadnění provádění zásady správy konfigurace a ovládací prvky přidružené konfigurace správy; kontroluje a aktualizuje aktuální zásady správy konfigurace [přiřazení: organizace definované frekvence]; a postupy správy konfigurace [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady správy konfigurace na úrovni celého podniku a postupy zákazníka může být dostatečná k vyřešení tohoto ovládacího prvku. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-2"></a>Ovládací prvek NIST 800-53 CM-2

#### <a name="baseline-configuration"></a>Standardních hodnot konfigurace

**CM 2** organizace sama vyvinula, dokumenty a udržuje pod řízení konfigurace, standardních hodnot konfigurace aktuální informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Šablony Azure Resource Manager a doprovodné prostředky, které tvoří tento plán, podle kterého představují směrný plán "konfigurace jako kód" nasazené architektuře. Řešení je k dispozici, když Githubu, které lze použít pro řízení konfigurace. Řešení obsahuje směrný plán konfigurace požadovaného stavu (DSC) pro každý virtuální počítač nasazený. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-2-1a"></a>((1) .a CM 2 NIST 800-53 ovládací prvek

#### <a name="baseline-configuration--reviews-and-updates"></a>Standardních hodnot konfigurace | Recenze a aktualizace

**CM-2 (1) .a** organizace kontroluje a aktualizuje standardních hodnot konfigurace systému informace [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za kontrola a aktualizace standardních hodnot konfigurace nasazených zákazníka prostředků (třeba aplikace, operační systémy, databází a softwaru). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-2-1b"></a>((1) .b CM 2 NIST 800-53 ovládací prvek

#### <a name="baseline-configuration--reviews-and-updates"></a>Standardních hodnot konfigurace | Recenze a aktualizace

**CM-2 (1) .b** organizace kontroluje a aktualizuje standardních hodnot konfigurace systému informace o vyžádání kvůli [přiřazení definované organizace okolností].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za kontrola a aktualizace standardních hodnot konfigurace nasazených zákazníka prostředků vyžádání. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-2-1c"></a>((1) .c CM 2 NIST 800-53 ovládací prvek

#### <a name="baseline-configuration--reviews-and-updates"></a>Standardních hodnot konfigurace | Recenze a aktualizace

**CM-2 (1) .c** organizace kontroluje a aktualizuje standardních hodnot konfigurace systému informace jako nedílné součásti informace systému součást instalace a upgrady.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za kontrola a aktualizace standardních hodnot konfigurace nasazených zákazníka prostředků vyžádání. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-2-2"></a>NIST 800-53 řízení CM-2 (2)

#### <a name="baseline-configuration--automation-support-for-accuracy--currency"></a>Standardních hodnot konfigurace | Podpora automatizace přesnost nebo měny

**CM-2 (2)** organizace využívá automatizované mechanismy pro údržbu aktuální, úplné, přesný a snadno dostupné standardních hodnot konfigurace systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Šablony Azure Resource Manager a doprovodné prostředky, které tvoří tento plán, podle kterého představují směrný plán "konfigurace jako kód" nasazené architektuře. Řešení je k dispozici, když Githubu, které lze použít pro řízení konfigurace. Na portálu Azure skriptu pro automatizaci je k dispozici pro všechny nasazené prostředky a poskytuje vždy aktuální reprezentace těchto prostředků.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-2-3"></a>NIST 800-53 řízení CM-2 (3)

#### <a name="baseline-configuration--retention-of-previous-configurations"></a>Standardních hodnot konfigurace | Uchování předchozích konfigurací

**CM-2 (3)** organizace zachová [přiřazení: organizace definované předchozích verzích standardních hodnot konfigurace systému informace] pro podporu vrácení zpět.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za zachovat předchozí verze směrného plánu konfigurace pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-2-7a"></a>NIST 800-53 řízení .a CM 2 (7)

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Standardních hodnot konfigurace | Konfigurace systémů, komponenty nebo zařízení s vysokým rizikem oblastí

**CM-2 (7) .a** problémy organizace [přiřazení: systémy informace definované organizace, součásti systému nebo zařízení] s [přiřazení: Konfigurace organizace definované] jednotlivcům cestě do umístění, organizace za to mít významné riziko.

**Odpovědnosti:** `Not Applicable`

|||
|---|---|
| **Zákazníka** | Nejsou řízené zákazníka fyzického zařízení v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Obsah zákazníka Microsoft Azure se nikdy ukládá mimo Microsoft Azure, který je fyzicky nacházejí v rámci kontinentální Spojených státech amerických. S zařízení obsažené v Microsoft Azure inventář, který není cestují pracovníky Microsoft Azure. Tento ovládací prvek jako takový není použitelná pro Microsoft Azure. |


 ### <a name="nist-800-53-control-cm-2-7b"></a>NIST 800-53 řízení .b CM 2 (7)

#### <a name="baseline-configuration--configure-systems-components-or-devices-for-high-risk-areas"></a>Standardních hodnot konfigurace | Konfigurace systémů, komponenty nebo zařízení s vysokým rizikem oblastí

**.B CM-2 (7)** organizace platí [přiřazení: ochrana definované organizace zabezpečení] do zařízení, když vrátíte jednotlivce.

**Odpovědnosti:** `Not Applicable`

|||
|---|---|
| **Zákazníka** | Nejsou řízené zákazníka fyzického zařízení v rámci oboru systémy, které jsou nasazené na platformě Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Obsah zákazníka Microsoft Azure se nikdy uložený mimo Microsoft Azure a pracovníky Microsoft Azure s zařízení obsažené v Microsoft Azure inventář, který není cestují, proto tento ovládací prvek není použitelný. |


 ## <a name="nist-800-53-control-cm-3a"></a>Ovládací prvek NIST 800-53 CM-3.a

#### <a name="configuration-change-control"></a>Řízení změn konfigurace

**CM 3.a** organizace určuje typy změny systému informace, které jsou řízené konfigurací.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za určení toho, jaké typy změn k nasazení zákazníka prostředkům (třeba aplikace, operační systémy, databází a softwaru) se řídí konfigurace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-3b"></a>Ovládací prvek NIST 800-53 CM-3.b

#### <a name="configuration-change-control"></a>Řízení změn konfigurace

**CM 3.b** organizace zkontroluje navrhované změny konfigurace řídí systému informace a schválí nebo disapproves tyto změny s explicitní aspekt analýzy dopadu zabezpečení.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za kontrola navrhované změny konfigurace řídí na zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-3c"></a>Ovládací prvek NIST 800-53 CM-3.c

#### <a name="configuration-change-control"></a>Řízení změn konfigurace

**CM 3.c** dokumenty konfigurace organizace změnit rozhodnutí, které jsou přidružené informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za dokumentaci změny konfigurace řídí přidružené zákazníka nasazené prostředky (viz CM 03.b). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-3d"></a>Ovládací prvek NIST 800-53 CM-3.d

#### <a name="configuration-change-control"></a>Řízení změn konfigurace

**CM 3.d** organizace implementuje schválené změny konfigurace řídí informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za implementaci změny konfigurace řídí schválit v CM 03.b. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-3e"></a>Ovládací prvek NIST 800-53 CM-3.e

#### <a name="configuration-change-control"></a>Řízení změn konfigurace

**CM-3.e** Organizace zachová záznamy řízené konfigurací změny systému informace [přiřazení: organizace definované časové období].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za zachování záznam řízené konfigurací změny zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-3f"></a>Ovládací prvek NIST 800-53 CM-3.f

#### <a name="configuration-change-control"></a>Řízení změn konfigurace

**CM 3.f** organizace audity a zkontroluje aktivity spojené s konfigurace řídí změnami informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za auditování a posouzení změn konfigurace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-3g"></a>Ovládací prvek NIST 800-53 CM-3.g

#### <a name="configuration-change-control"></a>Řízení změn konfigurace

**CM 3.g** organizace koordinuje a poskytuje dohledu pro aktivity řízení změn konfigurace prostřednictvím [přiřazení: elementu ovládacího prvku změn konfigurace definované organizace (například výbor, panel)], convenes [výběr ( jeden nebo více): [přiřazení: organizace definované frekvence]; [Přiřazení: Konfigurace organizace definované podmínky změnu]].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za koordinace a zajištění dohledu aktivity řízení změn konfigurace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-3-1a"></a>((1) .a CM 3 NIST 800-53 ovládací prvek

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Řízení změn konfigurace | Automatizované dokumentu nebo oznámení nebo zákaz změn

**CM-3 (1) .a** organizace využívá automatizované mechanismy pro dokumentu navrhované změny do systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání automatizované mechanismy pro dokumentu navrhované změny (viz CM 03.b). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-3-1b"></a>((1) .b CM 3 NIST 800-53 ovládací prvek

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Řízení změn konfigurace | Automatizované dokumentu nebo oznámení nebo zákaz změn

**CM-3 (1) .b** organizace využívá automatizované mechanismy pro upozornění [přiřazení: autority uspořádané definované schválení] navrhované změny informací o systému a žádosti o změnu schválení.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání automatizovaný mechanismus pro trasy a žádosti o schválení pro navrhované změny zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-3-1c"></a>((1) .c CM 3 NIST 800-53 ovládací prvek

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Řízení změn konfigurace | Automatizované dokumentu nebo oznámení nebo zákaz změn

**CM-3 (1) .c** organizace využívá automatizované mechanismy, abyste měli na očích informace systému, který dosud neschválených podle nebo schválení navrhované změny [přiřazení: organizace definované časové období].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání automatizovaný mechanismus pro zvýrazněte nerevidované změnu návrhy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-3-1d"></a>((1) .d CM 3 NIST 800-53 ovládací prvek

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Řízení změn konfigurace | Automatizované dokumentu nebo oznámení nebo zákaz změn

**CM-3 (1) .d** organizace využívá automatizované mechanismy zakázat změny systému informace, dokud jsou přijaty určené schválení.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání automatizovaný mechanismus pro zakázat implementace neschválených změny zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-3-1e"></a>((1) .e CM 3 NIST 800-53 ovládací prvek

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Řízení změn konfigurace | Automatizované dokumentu nebo oznámení nebo zákaz změn

**.E CM-3 (1)** Organizace používá automatizované mechanismy pro všechny změny do systému informace o dokumentu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání automatizovaný mechanismus pro dokumentu všechny implementovaná změny zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-3-1f"></a>((1) .f CM 3 NIST 800-53 ovládací prvek

#### <a name="configuration-change-control--automated-document--notification--prohibition-of-changes"></a>Řízení změn konfigurace | Automatizované dokumentu nebo oznámení nebo zákaz změn

**CM-3 (1) .f** organizace využívá automatizované mechanismy pro upozornění [přiřazení: definované organizace pracovníky] při dokončení schválené změny systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání automatizovaný mechanismus k oznámení, když jsou dokončeny schválené změny zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-3-2"></a>NIST 800-53 řízení CM-3 (2)

#### <a name="configuration-change-control--test--validate--document-changes"></a>Řízení změn konfigurace | Test / ověření / změny dokumentu

**CM-3 (2)** organizace testuje, ověří a dokumenty změny systému informace před implementací změny na operační systém.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za testování, ověřování a dokumentů změn zákazníka nasazené prostředky před implementací. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-3-4"></a>NIST 800-53 řízení CM-3 (4)

#### <a name="configuration-change-control--security-representative"></a>Řízení změn konfigurace | Zástupce zabezpečení

**CM-3 (4)** organizace vyžaduje zástupce informace o zabezpečení, být členem skupiny [přiřazení: elementu ovládacího prvku změn konfigurace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za přiřazení zabezpečení zástupce informace jako člena elementu ovládacího prvku změnu definované v CM 03.g. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-3-6"></a>NIST 800-53 řízení CM-3 (6)

#### <a name="configuration-change-control--cryptography-management"></a>Řízení změn konfigurace | Správa šifrování

**CM-3 (6)** organizace zajistí, že kryptografických mechanismů použitý k poskytnutí [přiřazení: ochrana definované organizace zabezpečení] jsou v části Správa konfigurace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za zajistíte, že kryptografických mechanismů pod správu konfigurace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-4"></a>Ovládací prvek NIST 800-53 CM – 4

#### <a name="security-impact-analysis"></a>Analýza dopadu zabezpečení

**CM 4** organizace analyzuje změny systému informace k určení potenciální dopadů zabezpečení před implementací změnu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za analýza navrhované změny zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-4-1"></a>NIST 800-53 řízení CM-4 (1)

#### <a name="security-impact-analysis--separate-test-environments"></a>Analýza dopadu zabezpečení | Samostatné testovací prostředí

**CM-4 (1)** organizace analyzuje změny systému informace v samostatných testovacím prostředí před implementací v provozním prostředí, hledá zabezpečení ovlivňuje kvůli nedostatky, slabá místa, nekompatibilita nebo úmyslně zlomyslných.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za analýza navrhované změny zákazníka nasazené prostředky v testovacím prostředí před implementací v provozním prostředí. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-5"></a>Ovládací prvek NIST 800-53 CM-5

#### <a name="access-restrictions-for-change"></a>Omezení přístupu pro změnu

**CM 5** organizace definuje, dokumentů, schválí a vynucuje omezení přístupu fyzické a logické přidružené změny systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Azure Active Directory účet, který oprávnění jsou implementované pomocí řízení přístupu na základě rolí přiřazování uživatelů do rolí poskytuje striktní určit, přes které uživatelé mohou zobrazit a řízení nasazené prostředky. Oprávnění k účtu Active Directory jsou implementovány pomocí řízení přístupu na základě rolí přiřazování uživatelů do skupiny zabezpečení. Tyto skupiny zabezpečení řídit akce, které mohou uživatelé provádět s ohledem na konfigurace operačního systému. Tato schémata na základě rolí lze rozšířit zákazník zvláště potřebám. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-5-1"></a>NIST 800-53 řízení CM-5 (1)

#### <a name="access-restrictions-for-change--automated-access-enforcement--auditing"></a>Přístup k omezení pro změnu | Automatizované přístup vynucení / auditování

**CM-5 (1)** informace systému vynucuje omezení přístupu a podporuje auditování vynucení akce.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Azure Active Directory účet, který oprávnění jsou implementované pomocí řízení přístupu na základě rolí přiřazování uživatelů do rolí poskytuje striktní určit, přes které uživatelé mohou zobrazit a řízení nasazené prostředky. Oprávnění k účtu Active Directory jsou implementovány pomocí řízení přístupu na základě rolí přiřazování uživatelů do skupiny zabezpečení. Tyto skupiny zabezpečení řídit akce, které mohou uživatelé provádět s ohledem na konfigurace operačního systému. Všechny přístupy a pokusů o přístup se auditují. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-5-2"></a>NIST 800-53 řízení CM-5 (2)

#### <a name="access-restrictions-for-change--review-system-changes"></a>Přístup k omezení pro změnu | Zkontrolujte změny v systému

**CM-5 (2)** organizace kontroluje změny systému informace [přiřazení: organizace definované frekvence] a [přiřazení: organizace definované okolností] k určení, zda došlo k neoprávněné změny.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za kontrola změny zákazníka nasazené prostředky k určení, zda došlo k neoprávněné změny. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-5-3"></a>NIST 800-53 řízení CM-5 (3)

#### <a name="access-restrictions-for-change--signed-components"></a>Přístup k omezení pro změnu | Podepsaný součásti

**CM-5 (3)** informace brání instalaci [přiřazení: organizace definované software a firmware součásti] bez ověření, že součást byla digitálně podepsané používá certifikát, který se rozpozná a Schválit organizace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán, podle kterého implementovat AppLocker Windows k určení uživatelů, kteří můžete nainstalovat nebo spustit konkrétní aplikací. Další, všechny systémy Windows, které jsou digitálně singed aktualizací operačního systému. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-5-5a"></a>NIST 800-53 řízení .a CM 5 (5)

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Přístup k omezení pro změnu | Omezit produkční / provozní oprávnění

**CM-5 (5) .a** organizace omezuje oprávnění pro změnu komponent systému informace a informace týkající se systému v produkčním nebo provozní prostředí.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za omezení oprávnění k provedení změn v produkčním nasazení zákazníka nebo provozní prostředí. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-5-5b"></a>NIST 800-53 řízení .b CM 5 (5)

#### <a name="access-restrictions-for-change--limit-production--operational-privileges"></a>Přístup k omezení pro změnu | Omezit produkční / provozní oprávnění

**CM-5 (5) .b** organizace zkontroluje a reevaluates oprávnění [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za kontrolu a reevaluating oprávnění definované v .a CM-05 (05). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-6a"></a>Ovládací prvek NIST 800-53 CM-6.a

#### <a name="configuration-settings"></a>Nastavení konfigurace

**CM 6.a** organizace vytváří a dokumenty nastavení konfigurace pro produkty informačních technologií pracujících v rámci informace systém pomocí [přiřazení: konfigurace zabezpečení organizace definované kontrolní seznamy] které odrážejí nejvíc omezující režim konzistentní s provozní požadavky.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán zahrnuje směrný plán konfigurace požadovaného stavu (DSC) pro každý virtuální počítač nasazený. Tyto deklarativní skripty prostředí PowerShell definovat a nakonfigurovat prostředky, na které se použijí. Zákazník podle potřeb zvláště lze rozšířit do směrného plánu DSC zahrnuté pro prostředky nasazené v tomhle řešení. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-6b"></a>Ovládací prvek NIST 800-53 CM-6.b

#### <a name="configuration-settings"></a>Nastavení konfigurace

**CM 6.b** organizace implementuje nastavení konfigurace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán zahrnuje směrný plán konfigurace požadovaného stavu (DSC) pro každý virtuální počítač nasazený. Směrné plány je automaticky použita pro virtuální počítače během nasazení pomocí rozšíření vlastních skriptů virtuálního počítače. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-6c"></a>Ovládací prvek NIST 800-53 CM-6.c

#### <a name="configuration-settings"></a>Nastavení konfigurace

**CM 6.c** organizace identifikuje, dokumenty a schválí všechny odchylky od zavedených konfigurační nastavení pro [přiřazení: komponent systému informace definované organizace] na základě [přiřazení: definované organizace provozní požadavky].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za identifikaci, dokumentace a schválení všech odchylky od zavedených konfigurační nastavení pro prostředky nasazené zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-6d"></a>Ovládací prvek NIST 800-53 CM-6.d

#### <a name="configuration-settings"></a>Nastavení konfigurace

**CM 6.d** organizace monitorování a ovládací prvky změny nastavení konfigurace v souladu se zásadami a postupy.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí Automation DSC. Automatizace DSC zarovnává konfigurace počítačů s konkrétní konfigurací definované organizace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-6-1"></a>NIST 800-53 řízení CM – 6 (1)

#### <a name="configuration-settings--automated-central-management--application--verification"></a>Nastavení konfigurace | Automatizované Centrální správa / aplikace / ověření

**CM-6 (1)** organizace využívá automatizované mechanismy centrálně spravovat, používá a ověření konfiguračních nastavení pro [přiřazení: komponent systému informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí Azure Automation DSC. Automatizace DSC zarovnává konfigurace počítačů s konkrétní konfigurací definované organizace a průběžně sleduje změny. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-6-2"></a>NIST 800-53 řízení CM – 6 (2)

#### <a name="configuration-settings--respond-to-unauthorized-changes"></a>Nastavení konfigurace | Reakce na neoprávněné změny

**CM-6 (2)** organizace využívá [přiřazení: ochrana definované organizace zabezpečení] reagovat na neoprávněné změny [přiřazení: nastavení konfigurace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí Azure Automation DSC. Automatizace DSC lze nakonfigurovat pro generování výstrahy, nebo chcete-li opravit nesprávné konfigurace při zjištění. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-7a"></a>Ovládací prvek NIST 800-53 CM-7a

#### <a name="least-functionality"></a>Minimálně funkce

**CM 7a** organizace nakonfiguruje systému informace poskytují pouze základní funkce.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Prostředky nasadit pomocí tento plán, podle kterého jsou nakonfigurované na poskytování funkci minimálně pro jejich zamýšlený účel. Směrný plán konfigurace požadovaného stavu (DSC) je zahrnuté pro všechny nasazené virtuální počítače. Tyto deklarativní skripty prostředí PowerShell definovat a nakonfigurovat prostředky, na které se použijí. Základní DSC zahrnuté pro prostředky nasazené v tomhle řešení můžete rozšířit pomocí zákazník dále omezit tak jeho funkčnost zvláště potřebám. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-7b"></a>CM-7.b a NIST 800-53 ovládací prvek

#### <a name="least-functionality"></a>Minimálně funkce

**CM 7.b** organizace zakáže nebo omezí používání následující funkce, porty, protokoly a služby: [přiřazení: organizace definované funkce zakázané nebo s omezeným přístupem, porty, protokoly nebo služby].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí Azure Application Gateway a sítě skupiny zabezpečení omezit použití porty a protokoly pouze ty, které jsou nezbytné. Aplikační bránu, skupiny zabezpečení sítě a směrných plánů DSC pro virtuální počítače lze dále konfigurovat zákazník omezit použití funkcí, porty, protokoly a služby pouze funkce, které jsou určené. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-7-1a"></a>((1) .a CM 7 NIST 800-53 ovládací prvek

#### <a name="least-functionality--periodic-review"></a>Minimálně funkce | Pravidelné kontroly

**CM-7 (1) .a** organizace zkontroluje informace o systému [přiřazení: organizace definované frekvence] k identifikaci nepotřebné nebo nezabezpečené funkcí, porty, protokoly a služby.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za kontrola zákazníka nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru) k identifikaci nepotřebné nebo nezabezpečená funkcí, porty, protokoly a služby. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-7-1b"></a>((1) .b CM 7 NIST 800-53 ovládací prvek

#### <a name="least-functionality--periodic-review"></a>Minimálně funkce | Pravidelné kontroly

**CM-7 (1) .b** zakáže organizace [přiřazení: organizace definované funkce, porty, protokoly a služby v rámci systému informace považuje za nepotřebné nebo nezabezpečené].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za zakázání funkce, porty, protokoly a službách, které mají byla považována za zbytečných nebo nezabezpečená. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-7-2"></a>NIST 800-53 řízení CM-7 (2)

#### <a name="least-functionality--prevent-program-execution"></a>Minimálně funkce | Zabránit spuštění programu

**CM-7 (2)** systému informací zabraňuje spuštění programu v souladu s [výběr (jeden nebo více): [přiřazení: organizace definované zásady týkající se softwaru programu využití a omezení]; pravidla autorizace podmínky a podmínky použití programu software].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za brání spuštění programu v souladu s definovanou zákazníkem softwaru zásady používání programu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-7-5a"></a>NIST 800-53 řízení .a CM 7 (5)

#### <a name="least-functionality--authorized-software--whitelisting"></a>Minimálně funkce | Oprávnění softwaru / vytvoření seznamu povolených

**CM-7 (5) .a** identifikuje organizace [přiřazení: programy definované organizace softwaru pověření ke spouštění v systému informace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za identifikaci autorizovaný softwarové programy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-7-5b"></a>NIST 800-53 řízení .b CM 7 (5)

#### <a name="least-functionality--authorized-software--whitelisting"></a>Minimálně funkce | Oprávnění softwaru / vytvoření seznamu povolených

**CM-7 (5) .b** organizace využívá zásadu odepřít all, povolení výjimka, která umožní provádění autorizovaný softwarových programů na informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání zásadu odepřít all, povolení výjimka, která umožní provádění autorizovaný softwarových programů na zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-7-5c"></a>NIST 800-53 řízení .c CM 7 (5)

#### <a name="least-functionality--authorized-software--whitelisting"></a>Minimálně funkce | Oprávnění softwaru / vytvoření seznamu povolených

**CM-7 (5) .c** organizace kontroluje a aktualizuje seznam autorizovaných softwarových programů [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za kontrola a aktualizace seznamu autorizovaných softwarové programy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-8a"></a>Ovládací prvek NIST 800-53 CM-8.a

#### <a name="information-system-component-inventory"></a>Informace o systému součást inventáře

**CM 8.a** organizace vyvíjí a dokumenty inventáře informace o systémových součástí, které přesně odráží aktuální informace o systému zahrnuje všechny součásti v rámci hranice autorizace systému informace; je v úrovně podrobností považované za nezbytné pro sledování a vytváření sestav; a zahrnuje [přiřazení: informace definované organizace považované za nezbytné k dosažení odpovědnosti za součást systému informace o platnosti].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí všechny prostředky do skupiny prostředků Azure Resource Manager. Azure Resource Manager poskytuje vždy aktuální seznam nasazené prostředky a můžete přizpůsobit tak, aby značky a skupiny prostředků pro správu inventáře. Toto řešení nasazené prostředky jsou uvedeny konkrétní prostředek značku, která může být přidružen hranice systému. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-8b"></a>Ovládací prvek NIST 800-53 CM-8.b

#### <a name="information-system-component-inventory"></a>Informace o systému součást inventáře

**CM 8.b** organizace kontroluje a aktualizuje inventář součásti systému informace [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí všechny prostředky do skupiny prostředků Azure Resource Manager. Azure Resource Manager poskytuje vždy aktuální seznam nasazené prostředky, které jsou k dispozici ke kontrole na portálu Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-8-1"></a>NIST 800-53 řízení CM-8 (1)

#### <a name="information-system-component-inventory--updates-during-installations--removals"></a>Informace o systému součást inventáře | Během instalace aktualizací nebo odstraňování

**CM-8 (1)** organizace aktualizace inventáře komponenty system informace jako součást instalace součástí, odstraňování a aktualizace informací o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí všechny prostředky do skupiny prostředků Azure Resource Manager. V okně prostředků na portálu Azure jsou uvedeny všechny nasazené prostředky, zadáním vždy aktuální inventář, jako jsou nasazené a odebrat prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-8-2"></a>NIST 800-53 řízení CM-8 (2)

#### <a name="information-system-component-inventory--automated-maintenance"></a>Informace o systému součást inventáře | Automatické údržby

**CM-8 (2)** organizace využívá automatizované mechanismy, které pomáhají udržovat inventář aktuální, úplné, přesný a snadno dostupné informace o systémových součástí.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí všechny prostředky do skupiny prostředků Azure Resource Manager. V okně prostředků na portálu Azure jsou uvedeny všechny nasazené prostředky, zadáním vždy aktuální inventář, jako jsou nasazené a odebrat prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-8-3a"></a>NIST 800-53 řízení (3) .a CM-8

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Informace o systému součást inventáře | Automatické zjišťování neoprávněným součásti

**CM-8 (3) .a** organizace využívá automatizované mechanismy [přiřazení: definované organizace frekvence] k detekci přítomnosti neoprávněným hardware, software a firmware součásti v rámci systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za využívání automatizované mechanismy pro zjištění přítomnosti neoprávněného softwaru v rámci zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-8-3b"></a>NIST 800-53 řízení (3) .b CM-8

#### <a name="information-system-component-inventory--automated-unauthorized-component-detection"></a>Informace o systému součást inventáře | Automatické zjišťování neoprávněným součásti

**CM-8 (3) .b** organizace provede následující akce při zjištění neoprávněným součásti: [výběr (jeden nebo více): Zakáže přístup k síti tím, že tyto součásti; izoluje komponenty; upozorní [přiřazení: definované organizace "pracovníky nebo rolí]].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za provedením akce, když je zjištěna neoprávněného softwaru. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-8-4"></a>NIST 800-53 řízení CM-8 (4)

#### <a name="information-system-component-inventory--accountability-information"></a>Informace o systému součást inventáře | Informace o odpovědnosti za

**CM-8 (4)** organizace obsahuje součást inventáře informace Systémové informace, způsob pro definování [výběr (jeden nebo více): název; pozice role], jednotlivce zodpovědná/odpovědní za správu těchto součástí .

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí všechny prostředky do skupiny prostředků Azure Resource Manager. Značky prostředku Azure jsou klíč / hodnota páry, které je možné použít ke kategorizaci prostředky pro účely odpovědnosti za nebo management. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-8-5"></a>NIST 800-53 řízení CM-8 (5)

#### <a name="information-system-component-inventory--no-duplicate-accounting-of-components"></a>Informace o systému součást inventáře | Žádné duplicitní monitorování účtů součástí

**CM-8 (5)** organizace ověřuje, že všechny součásti v rámci hranice autorizace systému informace se neduplikují v jiné informace o systému součást inventáře.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí všechny prostředky do skupiny prostředků Azure Resource Manager. Azure Resource Manager poskytuje vždy aktuální seznam nasazené prostředky. Toto řešení nasazené prostředky jsou uvedeny konkrétní prostředek značku, která může být přidružen hranice systému. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-9a"></a>Ovládací prvek NIST 800-53 CM-9.a

#### <a name="configuration-management-plan"></a>Konfigurace plánu

**CM 9.a** organizace sama vyvinula, dokumenty a implementuje plán správy konfigurace pro informace systému, která řeší rolí, odpovědnosti a konfigurace správy procesy a postupy.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za vývoj, dokumentace a implementace plánu správy konfigurace pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-9b"></a>Ovládací prvek NIST 800-53 CM-9.b

#### <a name="configuration-management-plan"></a>Konfigurace plánu

**CM 9.b** organizace sama vyvinula, dokumenty a implementuje plán správy konfigurace pro informace o systému, který stanoví proces identifikace položky konfigurace po celou dobu životnosti vývoj systému cyklus a pro Správa konfigurace položek konfigurace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za vývoj, dokumentace a implementace plánu správy konfigurace pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-9c"></a>Ovládací prvek NIST 800-53 CM-9.c

#### <a name="configuration-management-plan"></a>Konfigurace plánu

**CM 9.c** organizace sama vyvinula, dokumenty a implementuje plán správy konfigurace pro informace o systému, která definuje konfiguraci položek pro systém informace a umístí položek konfigurace v konfiguraci Správa.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za vývoj, dokumentace a implementace plánu správy konfigurace pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-9d"></a>Ovládací prvek NIST 800-53 CM-9.d

#### <a name="configuration-management-plan"></a>Konfigurace plánu

**CM 9.d** organizace sama vyvinula, dokumenty a implementuje plán správy konfigurace pro informace o systému, který chrání před neoprávněným sdílením a úpravy do konfigurace plánu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za vývoj, dokumentace a implementace plánu správy konfigurace pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-10a"></a>Ovládací prvek NIST 800-53 CM-10.a

#### <a name="software-usage-restrictions"></a>Omezení využití softwaru

**CM 10.a** organizace používá software a související dokumentace v souladu s kontrakt smlouvy a zákony o autorských právech.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Licence Windows a SQL Server jsou zahrnuté pro prostředky nasazené pomocí tento plán. Toto je integrované funkce Azure. Organizace s existující licenční smlouvy k softwaru zvážit při nasazování modelů alternativní licence. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-10b"></a>Ovládací prvek NIST 800-53 CM-10.b

#### <a name="software-usage-restrictions"></a>Omezení využití softwaru

**CM 10.b** organizace sleduje použití softwaru a související dokumentaci chráněn množství licencí pro řízení kopírování a distribuci.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Licence Windows a SQL Server jsou zahrnuté pro prostředky nasazené pomocí tento plán. Uživatel není potřeba samostatně sledovat využití licencí. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-10c"></a>Ovládací prvek NIST 800-53 CM-10.c

#### <a name="software-usage-restrictions"></a>Omezení využití softwaru

**CM 10.c** organizace ovládací prvky a dokumenty použití technologie zajistíte, že tato funkce není použité pro neoprávněné distribuční zobrazení, výkon nebo kopírování autorským dílem pro sdílení souborů peer-to-peer.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Neexistuje žádný soubor peer-to-peer, sdílení schopnost nasadit pomocí tento plán. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-10-1"></a>NIST 800-53 řízení CM-10 (1)

#### <a name="software-usage-restrictions--open-source-software"></a>Omezení použití softwaru | Software s otevřeným zdrojem

**CM-10 (1)** organizace vytváří následující omezení použití software s otevřeným zdrojem: [přiřazení: omezení definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady správy konfigurace na úrovni organizace zákazníka může adres omezení pro použití software s otevřeným zdrojem. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-11a"></a>Ovládací prvek NIST 800-53 CM-11.a

#### <a name="user-installed-software"></a>Uživatel nainstaloval softwaru

**CM 11.a** organizace vytváří [přiřazení: organizace definované zásady] pro instalaci softwaru uživatelé.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za vytvoření zásady, kterými se řídí instalace softwaru na zákazníka nasazené prostředky uživatelé. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-11b"></a>Ovládací prvek NIST 800-53 CM-11.b

#### <a name="user-installed-software"></a>Uživatel nainstaloval softwaru

**CM 11.b** organizace vynucuje zásady instalace softwaru prostřednictvím [přiřazení: metody definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za vynucování zásad instalace softwaru. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-cm-11c"></a>Ovládací prvek NIST 800-53 CM-11.c

#### <a name="user-installed-software"></a>Uživatel nainstaloval softwaru

**CM 11.c** monitoruje zásady dodržování předpisů v organizaci [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za monitorování dodržování předpisů nasazených zákazníka prostředky se zásadami v CM 11.a identifikovat. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-cm-11-1"></a>NIST 800-53 řízení CM-11 (1)

#### <a name="user-installed-software--alerts-for-unauthorized-installations"></a>Uživatel nainstaloval Software | Výstrahy pro neoprávněné instalace

**CM-11 (1)** výstrahy systémové informace [přiřazení: organizace definované pracovníky nebo rolí] když je zjištěna neoprávněným instalace softwaru.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za poskytování výstrahy, když je zjištěna neoprávněným instalaci softwaru. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |

