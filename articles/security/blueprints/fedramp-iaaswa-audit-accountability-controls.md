---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP webové aplikace automatizace - Audit a odpovědnosti za
description: FedRAMP webové aplikace automatizace - Audit a odpovědnosti za
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: acb0677d48084108a9a5219f92dead10948228c8
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206831"
---
# <a name="audit-and-accountability-au"></a>Audit a odpovědnosti za (AU)

> [!NOTE]
> Tyto ovládací prvky jsou definovány NIST a USA Ministerstvo obchodu jako součást speciální publikace NIST 800-53 revize 4. Naleznete NIST 800-53 Rev. 4 informace o testování postupy a pokyny pro každý ovládací prvek.

## <a name="nist-800-53-control-au-1"></a>Ovládací prvek NIST 800-53 AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>Audit a odpovědnosti za zásady a postupy

**AU-1** organizace sama vyvinula, dokumenty a šíří do [přiřazení: organizace definované pracovníky nebo rolí] zásadu auditu a odpovědnosti za, která řeší účel, oboru, role, odpovědnosti, závazků správy spolupráce mezi organizační entity a dodržování předpisů; a postupy pro usnadnění provádění auditu a odpovědnosti za zásady a přidružené auditu a ovládací prvky odpovědnosti za; kontroluje a aktualizuje aktuální zásady auditu a odpovědnosti za [přiřazení: organizace definované frekvence]; a audit a odpovědnosti za postupy [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady auditu a odpovědnosti za úrovni celého podniku a postupy zákazníka může být dostatečná k vyřešení tohoto ovládacího prvku. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-2a"></a>Ovládací prvek NIST 800-53 AU-2.a

#### <a name="audit-events"></a>Události auditu

**AU 2.a** organizace zjistí, že informace systém schopný auditování následující události: [přiřazení: kontrolovatelný událostí definovaných organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Funkce auditu pro tento plán, podle kterého je poskytovaný Azure monitorování a služba analýzy protokolů. Monitorování Azure poskytuje podrobné auditování protokoly o činnosti spojené s nasazené prostředky. Tyto protokoly na úrovni operačního systému jsou shromažďovány analýzy protokolů a uložené v pracovním prostoru analýzy protokolů. Analýzy protokolů korelaci dat auditu mezi prostředky nasazené v tomhle řešení a je možné rozšířit na zákazníka nasazené webové aplikace. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-2b"></a>Ovládací prvek NIST 800-53 AU-2.b

#### <a name="audit-events"></a>Události auditu

**AU 2.b** organizace koordinuje funkce auditu zabezpečení s dalšími organizační entitami nutnosti auditu související informace k vylepšení vzájemné podpory a pomoci výběr kontrolovatelný události.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může spoléhají na zavedených procesu podnikové úrovni, který určuje kontrolovatelný události. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-2c"></a>Ovládací prvek NIST 800-53 AU-2.c

#### <a name="audit-events"></a>Události auditu

**AU 2.c** organizace poskytuje odůvodnění pro proč kontrolovatelný události jsou považovány za vhodné jako vyšetřování po fakt bezpečnostních incidentů.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Události Audituje tento plán, podle kterého zahrnují informace o dostatečná k určení, kdy dojde k událostem, zdroj události, výsledek události a další podrobné informace, které podporuje šetření bezpečnostních incidentů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-2d"></a>Ovládací prvek NIST 800-53 AU-2.d

#### <a name="audit-events"></a>Události auditu

**AU 2.d** organizace určuje, zda jsou tyto události k auditování v rámci systému informace: [přiřazení: organizace definované auditované události (podmnožinu kontrolovatelný událostí definovaných v a. AU-2) společně s frekvence (nebo situation requiring) auditování pro všechny zjištěné události].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Události Audituje tento plán, podle kterého zahrnují ty Audituje protokoly Azure aktivity pro nasazené prostředky, protokoly na úrovni operačního systému, služby Active Directory protokoly a protokoly systému SQL Server. Zákazníci mohou vyberte další události, které mají být auditovány zvláště potřebám. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-2-3"></a>NIST 800-53 řízení AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>Události auditu | Recenze a aktualizace

**AU – 2 (3)** organizace kontroluje a aktualizuje auditované události [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může využívají zavedených pravidelné kontroly úrovni celého podniku a aktualizujte proces pro definovanou sadu auditované události. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-3"></a>Ovládací prvek NIST 800-53 AU-3

#### <a name="content-of-audit-records"></a>Obsah záznamů auditu

**AU – 3** generuje systém informace záznamy auditu obsahující informace, které určuje, jaký typ události došlo k chybě, při výskytu události, kde k události došlo, zdroj události, výsledek události a všechny identity jednotlivcům nebo témata přidružená k události.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého se spoléhá na funkce integrované auditu Azure, Windows Server a SQL Server. Tyto auditní záznamy auditu zachycení řešení s dostatečně podrobně, aby splňují požadavky tohoto ovládacího prvku. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-3-1"></a>NIST 800-53 řízení AU-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>Obsahu záznamů auditu | Informace o dalších auditování

**AU – 3 (1)** generuje systém informace záznamy auditu obsahující následující doplňkové informace: [přiřazení: organizace definované další, podrobnější informace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Azure aktivity protokolu události použijte podrobné schéma, které obsahuje pole pro více než 20 typy informace o auditování. Kromě protokol aktivit tento plán, podle kterého nasadí řešení analýzy protokolů, které podporuje různého typu zdroje dat, včetně protokoly systému Windows, Linux protokoly, Azure Diagnostics protokoly a protokoly zákazníka.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-3-2"></a>NIST 800-53 řízení AU-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Obsahu záznamů auditu | Centralizovaná správa záznamů obsahu plánované auditu

**AU-3 (2)** systému informací nabízí centralizovanou správu a konfiguraci obsahu možné je zachytit v vygenerované záznamy auditu [přiřazení: komponent systému informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Všechny virtuální počítače nasazené pomocí tento plán, podle kterého jsou připojené k nasazené doméně služby Active Directory. Všechny virtuální počítače připojené k doméně implementovat zásady skupiny, které lze nakonfigurovat, aby centrálně spravovat konfiguraci operačního systému na úrovni auditování systému. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-4"></a>Ovládací prvek NIST 800-53 AU-4

#### <a name="audit-storage-capacity"></a>Kapacita úložiště auditu

**AU – 4** organizace přidělí kapacita záznamů úložiště auditu v souladu s [přiřazení: požadavky na úložiště záznamů auditu definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého přiděluje dostatečná kapacita úložiště pro záznamy auditu uchovávány po dobu jednoho roku. Všechny záznamy auditu jsou shromážděny prostřednictvím analýzy protokolů, který je nakonfigurovaný pro uchování jeden rok. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-5a"></a>Ovládací prvek NIST 800-53 AU-5.a

#### <a name="response-to-audit-processing-failures"></a>Odpověď na Audit selhání zpracování

**AU 5.a** výstrahy systémové informace [přiřazení: organizace definované pracovníky nebo rolí] v případě auditu chyba zpracování.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Stav služby pro Azure monitorování a analýzy protokolů je k dispozici na webu Azure stav a v okně stavu služby v portálu Azure. Výstrahy můžete nakonfigurovat přes Log Analytics k poskytování oznámení jiné typy auditu selhání zpracování. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-5b"></a>Ovládací prvek NIST 800-53 AU-5.b

#### <a name="response-to-audit-processing-failures"></a>Odpověď na Audit selhání zpracování

**AU 5.b** informace systému provede následující další akce: [přiřazení: organizace definované akce (například vypnout informace systému, přepisovat nejstarší záznamy auditu, zastavit generování záznamů auditu)].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Všechny záznamy auditu generované prostředky nasadit pomocí tento plán, podle kterého jsou shromažďovány analýzy protokolů a uchovávány po dobu jednoho roku. Přidělení úložiště pro toto úložiště záznamů auditu se přidělí dynamicky zajistit dostatečnou kapacitu je k dispozici. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-5-1"></a>NIST 800-53 řízení AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Odpověď na Audit selhání zpracování | Kapacita úložiště auditu

**AU-5 (1)** informace systém poskytuje upozornění a na [přiřazení: organizace definované pracovníky, role nebo umístění] v [přiřazení: organizace definované časové období] když svazek záznamů úložiště přidělené auditu dosáhne [Přiřazení: organizace definované procento] maximální úložiště auditu záznamů úložnou kapacitu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Všechny záznamy auditu generované prostředky nasadit pomocí tento plán, podle kterého jsou shromažďovány analýzy protokolů a uchovávány po dobu jednoho roku. Přidělení úložiště pro toto úložiště záznamů auditu se přidělí dynamicky zajistit dostatečnou kapacitu je k dispozici. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-5-2"></a>NIST 800-53 řízení AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Odpověď na Audit selhání zpracování | Výstrahy v reálném čase

**AU-5 (2)** informace systém poskytuje výstrahu v [přiřazení: organizace definované v reálném čase období] na [přiřazení: definované organizace pracovníky, role nebo umístění] Pokud k následujícím událostem auditu selhání: [přiřazení: organizace definované selhání událostí auditu nutnosti v reálném čase výstrahy].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Stav služby pro Azure je k dispozici v okně služby stavu na portálu Azure. Výstrahy můžete nakonfigurovat přes Log Analytics k poskytování oznámení jiné typy auditu selhání zpracování. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-6a"></a>Ovládací prvek NIST 800-53 AU-6.a

#### <a name="audit-review-analysis-and-reporting"></a>Zkontrolujte auditu, analýzu a vytváření sestav

**AU 6.a** organizace zkontroluje a analyzuje záznamů auditu systému informace [přiřazení: organizace definované frekvence] k označení [přiřazení: organizace definované nevhodný nebo neobvyklé aktivity].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za kontrolu a analýza záznamy auditu zákazníka nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-6b"></a>Ovládací prvek NIST 800-53 AU-6.b

#### <a name="audit-review-analysis-and-reporting"></a>Zkontrolujte auditu, analýzu a vytváření sestav

**AU 6.b** organizace sestavy nálezy [přiřazení: organizace definované pracovníky nebo rolí].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za generování sestav výsledků nepotřebnými nebo neobvyklé aktivity (definovanou v AU 06.a) na zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-6-1"></a>NIST 800-53 řízení AU-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>Audit kontrolní, analýzu a vytváření sestav | Proces integrace

**AU – 6 (1)** organizace využívá automatizované mechanismy pro integraci zkontrolujte auditu, analýzu a vytváření sestav procesy, které podporují organizační procesy pro zkoumání a reakci na podezřelé aktivity.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může spoléhají na podnikové úrovni centralizované auditu kontrolní, analýzu a vykazovací funkci. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-6-3"></a>NIST 800-53 řízení AU-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Audit kontrolní, analýzu a vytváření sestav | Korelovat úložiště auditu

**AU – 6 (3)** analyzuje organizace a korelaci auditování záznamy mezi různé úložiště k získání situational sledování celé organizace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řešení analýzy protokolů a centralizovat data auditu napříč nasazené prostředky, podpora situational sledování celé organizace. Zákazníci mohou pokusit k další integraci s jinými systémy analýzy protokolů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-6-4"></a>NIST 800-53 řízení AU-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Audit kontrolní, analýzu a vytváření sestav | Centrální zkontrolujte a analýzy

**AU – 6 (4)** informace systém poskytuje možnost centrálně zkontrolujte a analyzovat záznamy auditu z několika součástí v systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řešení analýzy protokolů a centralizovat data auditu napříč nasazené prostředky, podpůrné centralizované kontrolní, analýzu a vytváření sestav. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-6-5"></a>NIST 800-53 řízení AU-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Audit kontrolní, analýzu a vytváření sestav | Integrace / kontrolu a možnosti monitorování

**AU – 6 (5)** organizace se službou analysis záznamů auditu analýzu [výběr (jeden nebo více): ohrožení zabezpečení kontrolu informace; údaje o výkonu systému informace o monitorování informace; [Přiřazení: definované organizace data nebo informace shromažďovány z jiných zdrojů]] Chcete-li dále zvýšit schopnost určit nevhodný nebo neobvyklé aktivity.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řešení zabezpečení a auditování. Toto řešení poskytuje komplexní pohled postavení zabezpečení. Řídicí panel zabezpečení a Audit poskytuje podrobný přehled o stavu zabezpečení nasazené prostředky pomocí dat, které jsou k dispozici v nasazené řešení pro analýzu protokolu, integraci dat protokolu a ohrožení zabezpečení dat ze směrného plánu a oprava hodnocení. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-6-6"></a>NIST 800-53 řízení AU-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Audit kontrolní, analýzu a vytváření sestav | Korelace s fyzické monitorování

**AU – 6 (6)** informace z auditu záznamů s informacemi získanými z monitorování fyzického přístupu k další vylepšení umožňuje identifikovat podezřelé, nevhodný, neobvyklé nebo tvaru aktivita koreluje organizace.

**Odpovědnosti:** `Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Tým Microsoft Azure SIM používá související fyzické data sledování a korelaci se záznamy auditu k určení, pokud se všechny přidružené logické porušení nebo podezřelé chování při zjištění fyzických incidenty. |


 ### <a name="nist-800-53-control-au-6-7"></a>NIST 800-53 řízení AU-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Audit kontrolní, analýzu a vytváření sestav | Povolené akce

**AU – 6 (7)** organizace určuje povolené akce pro každou [výběr (jeden nebo více): proces systému informace; role uživatele] přidružené kontrola, analýzu a vytváření sestav auditní informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače s Windows, které jsou nasazeny tento plán, podle kterého implementovat oprávnění na úrovni operačního systému, které omezují akce, které může uživatel provést s ohledem informace o auditování. V rámci Azure uživatelé nebo skupiny uživatelů můžete přiřadit do rolí (například vlastník, Přispěvatel, čtečky nebo vlastní role) omezit akce, které jsou k dispozici s ohledem na všechny prostředky, nebo nasazení řešení, včetně analýzy protokolů.  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-6-10"></a>NIST 800-53 řízení AU-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Audit kontrolní, analýzu a vytváření sestav | Úpravy na úrovni auditu

**AU – 6 (10)** organizace upraví úroveň zkontrolujte auditu, analýzu a vytváření sestav v rámci systému informace, když dojde ke změně v riziko podle informace vynucení zákonem, informací o prostředcích nebo dalších důvěryhodných zdrojů informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za úpravě úroveň zkontrolujte auditu, analýzu a vytváření sestav pro zákazníka nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru) po změně v riziko podle informací uvedených zákonem vynucení, intelligence nebo jiných důvěryhodných zdrojů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-7a"></a>Ovládací prvek NIST 800-53 AU-7a

#### <a name="audit-reduction-and-report-generation"></a>Snížení auditu a generování sestav

**AU 7a** poskytuje informace o systému služby audit snížení a funkci generování sestav, která podporuje na vyžádání audit kontrolní, analýzu a vytváření sestav požadavky a vyšetřování po fakt bezpečnostních incidentů.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řešení analýzy protokolů. Analýzy protokolů pro zajišťuje služby monitorování Azure ve shromažďování dat ze spravované prostředky do centrální úložiště. Po získání jsou data dostupná pro výstrahy, analýzu a export. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-7b"></a>AU – 7.b a NIST 800-53 ovládací prvek

#### <a name="audit-reduction-and-report-generation"></a>Snížení auditu a generování sestav

**AU 7.b** informace systému poskytuje auditu snížení a sestava generování funkci, která nezmění původní obsah nebo čas řazení záznamů auditu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řešení analýzy protokolů. Analýzy protokolů pro zajišťuje služby monitorování Azure ve shromažďování dat ze spravované prostředky do centrální úložiště. Když shromažďují analýzy protokolů nejsou změnit obsah a čas řazení záznamů auditu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-7-1"></a>NIST 800-53 řízení AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Snížení auditu a generování sestav | Automatické zpracování

**AU – 7 (1)** systému informací nabízí funkci ke zpracování záznamů auditu události na základě [přiřazení: pole definované organizace auditu v rámci záznamy auditu].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje řešení analýzy protokolů. Analýzy protokolů pro zajišťuje služby monitorování Azure ve shromažďování dat ze spravované prostředky do centrální úložiště. Po získání jsou data dostupná pro výstrahy, analýzu a export. Log Analytics zahrnuje účinný dotazovací jazyk k extrakci dat uložených v úložišti. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-8a"></a>Ovládací prvek NIST 800-53 AU-8.a

#### <a name="time-stamps"></a>Časová razítka

**AU 8.a** informace systému používá ke generování časová razítka pro záznamy auditu interní systémové hodiny.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Nasazené pomocí tento plán, podle kterého prostředky pomocí interní systémové hodiny vygenerujte časová razítka pro záznamy auditu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-8b"></a>Ovládací prvek NIST 800-53 AU-8.b

#### <a name="time-stamps"></a>Časová razítka

**AU 8.b** systém informace zaznamenává časová razítka na záznamy auditu, které lze mapovat na koordinovaný světový čas (UTC) nebo greenwichský střední čas (GMT) a splňuje [přiřazení: organizace definované členitost měření času].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Nasazené pomocí tento plán, podle kterého prostředky pomocí interní systémové hodiny vygenerujte časová razítka pro záznamy auditu. Časová razítka se zaznamenávají v čase UTC. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-8-1a"></a>((1) .a AU 8 NIST 800-53 ovládací prvek

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Časová razítka | Synchronizace s autoritativní zdroj času

**.A AU-8 (1)** systém informace porovná systémové hodiny interní informace [přiřazení: organizace definované frekvence] s [přiřazení: organizace definované autoritativní zdroj času].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Nasazené pomocí tento plán, podle kterého prostředky pomocí interní systémové hodiny vygenerujte časová razítka pro záznamy auditu. Interní systémové hodiny jsou nakonfigurovány pro synchronizaci s zdrojem autoritativní času. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-8-1b"></a>((1) .b AU 8 NIST 800-53 ovládací prvek

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Časová razítka | Synchronizace s autoritativní zdroj času

**AU – 8 (1) .b** informace systém synchronizuje čas vnitřního systému, aby autoritativní zdroj času při časový rozdíl je větší než [přiřazení: organizace definované časové období].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Nasazené pomocí tento plán, podle kterého prostředky pomocí interní systémové hodiny vygenerujte časová razítka pro záznamy auditu. Interní systémové hodiny jsou nakonfigurovány pro synchronizaci s zdrojem autoritativní času. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-9"></a>Ovládací prvek NIST 800-53 AU-9

#### <a name="protection-of-audit-information"></a>Ochrana informací auditu

**AU – 9** informace systému chrání před neoprávněným přístupem, úpravu a mazání nástroje auditu informace a auditování.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Ovládací prvky logického přístupu slouží k ochraně auditu informacím a nástrojům v rámci tento plán, podle kterého před neoprávněným přístupem, úpravy a odstranění. Azure Active Directory vynucuje schválené logické přístup pomocí členství ve skupinách na základě rolí. Umožňuje zobrazit informace o auditování a používat nástroje auditování může být omezen na uživatele, kteří tato oprávnění vyžadují. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-9-2"></a>NIST 800-53 řízení AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Ochrana informací auditu | Audit zálohování na samostatné fyzické systémy nebo součásti

**AU – 9 (2)** informace systému zálohuje záznamy auditu [přiřazení: organizace definované frekvence] na fyzicky jiného systému nebo součást systému než systému nebo součást se auditují.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje službu analýzy protokolů. Nasadit virtuální počítače a Azure diagnostics účty úložiště jsou připojené zdroje k analýze protokolů a zachovali samostatně z jejich původu. Shromažďuje data, analýzy protokolů v téměř v reálném čase. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-9-3"></a>NIST 800-53 řízení AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>Ochrana informací auditu | Kryptografické ochrany

**AU – 9 (3)** systém informace implementuje kryptografických mechanismů chránit integritu nástroje auditu informace a auditování.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje službu analýzy protokolů. Analýzy protokolů zajišťuje, že příchozích dat z důvěryhodného zdroje pomocí ověřování certifikátů a integrity dat pomocí ověřování Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-9-4"></a>NIST 800-53 řízení AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Ochrana informací auditu | Přístup podmnožině privilegovaných uživatelů

**AU – 9 (4)** organizace povolí přístup ke správě funkce auditu jenom [přiřazení: podmnožinu definované organizace mohou uživatelé s oprávněním].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Ovládací prvky logického přístupu slouží k ochraně auditu informacím a nástrojům v rámci tento plán, podle kterého před neoprávněným přístupem, úpravy a odstranění. Azure Active Directory vynucuje schválené logické přístup pomocí členství ve skupinách na základě rolí. Umožňuje zobrazit informace o auditování a používat nástroje auditování může být omezen na uživatele, kteří tato oprávnění vyžadují.
 |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-10"></a>Ovládací prvek NIST 800-53 AU-10

#### <a name="non-repudiation"></a>Zrušení odmítnutí

**AU 10** informace systému chrání před jednotlivce (nebo proces funguje jménem konkrétního) nesprávně odepření s provést [přiřazení: organizace definované akce, které mají být pokryté komponentami nepopiratelnosti].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Funkce auditu pro tento plán, podle kterého je poskytovaný Azure monitorování a služba analýzy protokolů. Monitorování Azure poskytuje podrobné auditování protokoly o činnosti spojené s nasazené prostředky. Tyto protokoly na úrovni operačního systému jsou shromažďovány analýzy protokolů a uložené v pracovním prostoru analýzy protokolů. Tyto protokoly obsahoval záznamy podrobné informace o systémových událostí a může pomoct chránit proti nepopiratelnosti. Navíc přístup k protokolování dat je omezen pomocí řízení přístupu na základě rolí, aby se zabránilo unauthored úpravy nebo odstranění dat protokolu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-11"></a>Ovládací prvek NIST 800-53 AU-11

#### <a name="audit-record-retention"></a>Uchování záznamu pro audit

**AU – 11** organizace zachová záznamy auditu pro [přiřazení: organizace definované časové období, které jsou konzistentní s zásady uchovávání záznamů] kvůli zajištění podpory pro vyšetřování za fakt bezpečnostních incidentů a pro splnění zákonných a požadavky na uchování organizační údaje.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje službu analýzy protokolů. Analýzy protokolů pro zajišťuje služby monitorování Azure ve shromažďování dat ze spravované prostředky do centrální úložiště. Jakmile se shromažďují, data uchovávat jeden rok za konfigurace analýzy protokolů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-12a"></a>Ovládací prvek NIST 800-53 AU-12a

#### <a name="audit-generation"></a>Generování auditování

**AU 12a** informace systém poskytuje funkci generování záznamů auditu pro kontrolovatelný událostí definovaných v AU-2. v [přiřazení: komponent systému informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Události Audituje tento plán, podle kterého zahrnují ty Audituje protokoly Azure aktivity pro nasazené prostředky, protokoly na úrovni operačního systému, služby Active Directory protokoly a protokoly systému SQL Server. Zákazníci mohou vyberte další události, které mají být auditovány zvláště potřebám. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-12b"></a>Ovládací prvek NIST 800-53 AU-12.b

#### <a name="audit-generation"></a>Generování auditování

**AU 12.b** umožňuje systému informace [přiřazení: organizace definované pracovníky nebo rolí] vyberte kontrolovatelný události, které jsou ověřovat konkrétní součásti systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Přístup k provádění kontroly funkce je omezen pomocí řízení přístupu na základě role v rámci Azure a na úrovni operačního systému virtuálního počítače. Konfigurace událostí vybraná ověřovat prostředky nasadit pomocí tento plán, podle kterého se dá nakonfigurovat uživatelé s příslušnou autorizace na základě rolí. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-au-12c"></a>Ovládací prvek NIST 800-53 AU-12.c

#### <a name="audit-generation"></a>Generování auditování

**AU 12.c** záznamy auditu pro událostí definovaných v AU 2.d generuje systém informace. s obsahem definované v AU-3.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Události Audituje tento plán, podle kterého zahrnují ty Audituje protokoly Azure aktivity pro nasazené prostředky, protokoly na úrovni operačního systému, služby Active Directory protokoly a protokoly systému SQL Server. Zákazníci mohou vyberte další události, které mají být auditovány zvláště potřebám. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-12-1"></a>NIST 800-53 řízení AU-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Audit generování | Systémové / korelační čas záznamu pro Audit

**AU – 12 (1)** záznamy auditu z zkompiluje informace o systému [přiřazení: komponent systému informace definované organizace] do záznam pro audit systémové (logický nebo fyzický), který je čas korelována v rámci [přiřazení: organizace definované úroveň tolerance pro vztah mezi časová razítka jednotlivých záznamů v záznamu pro audit].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého implementuje službu analýzy protokolů. Analýzy protokolů pro zajišťuje služby monitorování Azure ve shromažďování dat ze spravované prostředky do centrální úložiště. Audit záznamů časová razítka nejsou změněna, proto záznam pro audit je korelační čas. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-au-12-3"></a>NIST 800-53 řízení AU-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>Audit generování | Změny podle autorizované jednotlivce

**AU-12 (3)** systému informací nabízí funkci pro [přiřazení: organizace definované jednotlivce nebo role] změna auditování provést v [přiřazení: komponent systému informace definované organizace] podle [Přiřazení: kritéria definované organizace volitelný události] v [přiřazení: organizace definovaný čas prahové hodnoty].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Přístup k provádění kontroly funkce je omezen pomocí řízení přístupu na základě role v rámci Azure a na úrovni operačního systému virtuálního počítače. Konfigurace událostí vybraná ověřovat prostředky nasadit pomocí tento plán, podle kterého se dá nakonfigurovat uživatelé s příslušnou autorizace na základě rolí. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |
