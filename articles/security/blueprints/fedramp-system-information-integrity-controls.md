---
title: Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP webové aplikace automatizace – systém a informace o Integrity
description: FedRAMP webové aplikace automatizace – systém a informace o Integrity
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 0eca3c82aea287f6582bd56574512dce5e8e86c7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31422160"
---
# <a name="system-and-information-integrity-si"></a>Systém a informace o Integrity (SI)

> [!NOTE]
> Tyto ovládací prvky jsou definovány NIST a USA Ministerstvo obchodu jako součást speciální publikace NIST 800-53 revize 4. Naleznete NIST 800-53 Rev. 4 informace o testování postupy a pokyny pro každý ovládací prvek.

## <a name="nist-800-53-control-si-1"></a>Ovládací prvek NIST 800-53 SI-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>Systém a zásady Integrity informace a postupy

**SI-1** organizace sama vyvinula, dokumenty a šíří do [přiřazení: organizace definované pracovníky nebo rolí] systém a informace o integrity zásadu, která řeší účel, oboru, role, odpovědnosti, závazků správy spolupráce mezi organizační entity a dodržování předpisů; a postupy pro usnadnění provádění systému a zásady integrity informace a přidružené systému a ovládací prvky integrity informace; kontroluje a aktualizuje aktuální zásady integrity systému a informace [přiřazení: organizace definované frekvence]; a postupy integrity systému a informace [přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může stačit zásady integrity systému a informace o úrovni celého podniku a postupy zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-2a"></a>Ovládací prvek NIST 800-53 ma 2.a

#### <a name="flaw-remediation"></a>Chyba nápravy

**Ma 2.a** organizace identifikuje, sestavy a opravuje nedostatky informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řešení automatizace a řízení sledovat stav aktualizací pro virtuální počítače Windows nasazené v této architektuře. Na řídicím panelu dlaždici správy aktualizací zobrazuje stav nápravy závadu pro všechny nasazené servery Windows. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-2b"></a>Ovládací prvek NIST 800-53 ma 2.b

#### <a name="flaw-remediation"></a>Chyba nápravy

**Ma 2.b** organizace testy software a firmware aktualizacemi souvisejícími se chyba nápravy efektivitu a potenciální vedlejší účinky před instalací.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za testování aktualizacemi souvisejícími se chyba nápravy efektivitu a potenciální vedlejší účinky před instalací na zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-2c"></a>Ovládací prvek NIST 800-53 ma 2.c

#### <a name="flaw-remediation"></a>Chyba nápravy

**Ma 2.c** organizace nainstaluje příslušné zabezpečení software a firmware aktualizace v rámci [přiřazení: organizace definované časové období] verze aktualizací.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače s Windows, které jsou nasazeny tento plán, podle kterého jsou nakonfigurované ve výchozím nastavení příjem automatických aktualizací ze služby Windows Update. Toto řešení taky nasadí automatizace a řízení řešení, pomocí kterého lze vytvořit nasazení aktualizací na servery Windows v případě potřeby nasadit opravy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-2d"></a>Ovládací prvek NIST 800-53 ma 2.d

#### <a name="flaw-remediation"></a>Chyba nápravy

**Ma 2.d** organizace zahrnuje závadu nápravy do procesu správy organizační konfigurace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za včetně závadu nápravy ve správě konfigurací. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-2-1"></a>NIST 800-53 řízení SI-2 (1)

#### <a name="flaw-remediation--central-management"></a>Chyba nápravy | Centrální správy

**SI-2 (1)** organizace centrálně spravovat proces nápravy chyba.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řešení automatizace a řízení sledovat stav aktualizací pro virtuální počítače Windows nasazené v této architektuře. Na řídicím panelu dlaždici správy aktualizací zobrazuje stav nápravy závadu pro všechny nasazené servery Windows. Nasazení aktualizací můžete vytvořit nasazení oprav na servery Windows v případě potřeby. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-2-2"></a>NIST 800-53 řízení SI-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Chyba nápravy | Stav automatizované závadu nápravy

**SI-2 (2)** organizace využívá automatizované mechanismy [přiřazení: organizace definované frekvence] k určení stavu komponent systému informace s ohledem na závadu nápravy.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řešení automatizace a řízení sledovat stav aktualizací pro virtuální počítače Windows nasazené v této architektuře. Pro každý spravovaný počítač s Windows se kontrola provádí dvakrát denně. Každých 15 minut se volá rozhraní Windows API pro zadání dotazu na čas poslední aktualizace, podle kterého zjistí, jestli se změnil stav, a pokud ano, zahájí se kontrola kompatibility. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-2-3a"></a>NIST 800-53 řízení .a SI-2 (3)

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Chyba nápravy | Čas k nápravě nedostatků / srovnávacích testů pro opravné akce

**.A SI-2 (3)** organizace měří čas mezi závadu identifikace a nápravy chyba.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za nekompatibilních nedostatky v rámci zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-2-3b"></a>NIST 800-53 řízení .b SI-2 (3)

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Chyba nápravy | Čas k nápravě nedostatků / srovnávacích testů pro opravné akce

**.B SI-2 (3)** organizace vytváří [přiřazení: organizace definované srovnávacích testů] pro případné nápravné akce.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník může spoléhají na podnikové úrovni srovnávacích testů pro závadu nápravy procesy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-3a"></a>Ovládací prvek NIST 800-53 ma 3.a

#### <a name="malicious-code-protection"></a>Škodlivý kód ochrany

**Ma 3.a** organizace využívá ochranu mechanismy škodlivý kód na informace o systému vstupní a výstupní místa ke zjišťování a eradikaci škodlivý kód.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí založené na hostiteli antimalwarové ochrany pro všechny nasazené virtuální počítače s Windows implementovaná pomocí rozšíření virtuálního počítače Antimalware od Microsoftu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-3b"></a>Ovládací prvek NIST 800-53 ma 3.b

#### <a name="malicious-code-protection"></a>Škodlivý kód ochrany

**Ma 3.b** organizace aktualizace mechanismy ochrany škodlivý kód vždy, když jsou k dispozici v souladu s organizační konfigurace zásad správy a postupy nové verze.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí založené na hostiteli antimalwarové ochrany pro všechny nasazené virtuální počítače s Windows implementovaná pomocí rozšíření virtuálního počítače Antimalware od Microsoftu. Toto rozšíření je nakonfigurován pro automatické aktualizace obou antimalwarový stroj a ochrany podpisů jako verzi k dispozici. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-3c"></a>Ovládací prvek NIST 800-53 ma 3.c

#### <a name="malicious-code-protection"></a>Škodlivý kód ochrany

**Ma 3.c** organizace nakonfiguruje mechanismy ochrany škodlivý kód do provádět pravidelné kontroly systému informace [přiřazení: organizace definované frekvence] a kontroly v reálném čase souborů z externích zdrojů na [výběr (jeden nebo informace); koncový bod; vstupní/výstupní body sítě] jsou stažené soubory, otevření nebo provést v souladu se zásadami zabezpečení organizace; a [výběr (jeden nebo více): blokovat škodlivý kód; umístit do karantény škodlivý kód; Odeslat výstrahu do Správce; [Přiřazení: akce definované organizace]] v reakci na detekce škodlivý kód.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí založené na hostiteli antimalwarové ochrany pro všechny nasazené virtuální počítače s Windows implementovaná pomocí rozšíření virtuálního počítače Antimalware od Microsoftu. Toto rozšíření je nakonfigurován, aby provádět v reálném čase a pravidelné kontroly (týdně), automaticky aktualizovat i antimalwarový stroj a ochrany podpisů a provádět automatické nápravné akce. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-3d"></a>Ovládací prvek NIST 800-53 ma 3.d

#### <a name="malicious-code-protection"></a>Škodlivý kód ochrany

**Ma 3.d** organizace řeší příjmu falešně pozitivních během likvidaci a škodlivý kód zjišťování a výsledný potenciální dopad na dostupnost informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za ochranu proti škodlivý kód. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-3-1"></a>NIST 800-53 řízení ma-3 (1)

#### <a name="malicious-code-protection--central-management"></a>Škodlivý kód ochrany | Centrální správy

**Ma-3 (1)** organizace centrálně spravuje mechanismy ochrany škodlivý kód.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí založené na hostiteli antimalwarové ochrany pro všechny nasazené virtuální počítače s Windows implementovaná pomocí rozšíření virtuálního počítače Antimalware od Microsoftu. Azure poskytuje centralizovanou schopnost zkontrolujte aktuální stav řešení proti malwaru. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-3-2"></a>NIST 800-53 řízení ma-3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>Škodlivý kód ochrany | Funkce Automatické aktualizace

**Ma-3 (2)** informace systému automaticky aktualizuje mechanismy ochrany škodlivý kód.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí založené na hostiteli antimalwarové ochrany pro všechny nasazené virtuální počítače s Windows implementovaná pomocí rozšíření virtuálního počítače Antimalware od Microsoftu. Toto rozšíření je nakonfigurován pro automatické aktualizace obou antimalwarový stroj a ochrany podpisů jako verzi k dispozici. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-3-7"></a>NIST 800-53 řízení ma-3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>Škodlivý kód ochrany | Na základě nonsignature detekce

**Ma-3 (7)** systém informace implementuje mechanismů zjišťování na základě nonsignature škodlivý kód.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí založené na hostiteli antimalwarové ochrany pro všechny nasazené virtuální počítače s Windows implementovaná pomocí rozšíření virtuálního počítače Antimalware od Microsoftu. Toto rozšíření je nakonfigurován k provedení Heuristická detekce. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-4a"></a>Ovládací prvek NIST 800-53 ma 4.a

#### <a name="information-system-monitoring"></a>Informace o sledování systému

**Ma 4.a** organizace sleduje informace o systému zjistit útoky a zjišťování indikátorů možných útoků, které v souladu s [přiřazení: organizace definované monitorování cílů]; a neoprávněným místní, síťové a vzdálené připojení.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí analýzy protokolů a řešení zabezpečení a auditování. Toto řešení poskytuje komplexní pohled postavení zabezpečení, útokům a indikátory možných útoků. Řídicí panel zabezpečení a Audit poskytuje podrobný přehled o stavu zabezpečení nasazené prostředky pomocí dat, které jsou k dispozici v řešení pro správu nasazené. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-4b"></a>Ovládací prvek NIST 800-53 ma 4.b

#### <a name="information-system-monitoring"></a>Informace o sledování systému

**Ma 4.b** organizace identifikuje neoprávněnému použití systému informace prostřednictvím [přiřazení: organizace definované technik a metody].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí řešení zabezpečení a auditování. Domény identifikace a přístup poskytuje přehled identity stavu informace systému, včetně počtu neúspěšných pokusů o přihlášení a aktuální počet účtů, které se protokolují v řídicím panelu. Informace k dispozici v tomto řídicím panelu může být užitečné při identifikaci potenciální podezřelých aktivit. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-4c"></a>Ovládací prvek NIST 800-53 ma 4.c

#### <a name="information-system-monitoring"></a>Informace o sledování systému

**Ma 4.c** organizace nasazuje monitorování zařízení strategicky systému informace ke shromažďování, organizaci určit základní informace; a na ad hoc místa v systému pro sledování konkrétní typy transakce zájmu v organizaci.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí analýzy protokolů a řešení zabezpečení a auditování. Řídicí panel zabezpečení a Audit poskytuje podrobný přehled o stavu zabezpečení nasazené prostředky pomocí dat, které jsou k dispozici v řešení pro správu nasazené, včetně aspekty dat monitorování virtuálních počítačů operačního systému. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-4d"></a>Ovládací prvek NIST 800-53 ma 4.d

#### <a name="information-system-monitoring"></a>Informace o sledování systému

**Ma 4.d** organizace chrání informacemi získanými z monitorování neoprávněných vniknutí nástroje před neoprávněným přístupem, úpravy a odstranění.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Ovládací prvky logického přístupu slouží k ochraně monitorování informace v rámci tento plán, podle kterého před neoprávněným přístupem, úpravu a odstranění. Azure Active Directory vynucuje schválené logické přístup pomocí členství ve skupinách na základě rolí. Možnost zobrazení informací o monitorování a použití nástrojů pro monitorování může být omezen na uživatele, kteří tato oprávnění vyžadují. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-4e"></a>Ovládací prvek NIST 800-53 ma 4e

#### <a name="information-system-monitoring"></a>Informace o sledování systému

**SI-4.e** Organizace zvýší úroveň monitorování aktivity vždy, když je to znamenat vyšší riziko pro organizační operace a prostředky, jednotlivce, jinými organizacemi nebo výhod založený na informacích vynucení zákonem, informace o systému informací o prostředcích, nebo dalších důvěryhodných zdrojů informací.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za monitorování zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-4f"></a>Ovládací prvek NIST 800-53 ma 4.f

#### <a name="information-system-monitoring"></a>Informace o sledování systému

**Ma 4.f** organizace získává právní stanovisko k monitorování aktivit v souladu s příslušných zákonů federal, objednávky vedení, direktivy, zásady a předpisy informace o systému.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za monitorování zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-4g"></a>Ovládací prvek NIST 800-53 ma 4.g

#### <a name="information-system-monitoring"></a>Informace o sledování systému

**Ma 4.g** poskytuje organizace [přiřazení: monitorování informace systému informace definované organizace] na [přiřazení: definované organizace pracovníky nebo rolí] [výběr (jeden nebo více): podle potřeby; [Přiřazení: organizace definované frekvence].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za monitorování zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-1"></a>NIST 800-53 řízení ma-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Informace o systému monitorování | Systém zjišťování neoprávněných vniknutí systémové

**Ma-4 (1)** organizace připojí a nakonfiguruje nástrojů pro zjišťování neoprávněných vniknutí jednotlivých do systému zjišťování neoprávněných vniknutí systémové informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za monitorování zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-2"></a>NIST 800-53 řízení ma-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Informace o systému monitorování | Automatizované nástroje pro analýzu v reálném čase

**Ma-4 (2)** organizace využívá automatizované nástroje k podpoře téměř v reálném čase analýzy událostí.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí analýzy protokolů a různá řešení správy, včetně řešení zabezpečení a auditování. Analýzy protokolů zajišťuje téměř v reálném čase analýzy událostí napříč nasazené prostředky. Řešení pro správu poskytuje komplexní pohled postavení zabezpečení napříč doménami řešení. Log Analytics poskytuje přehled o stavu zabezpečení nasazené prostředky pomocí dat, které jsou k dispozici v řešení pro správu nasazené. Analýzy protokolů může být nakonfigurováno pro generování výstrahy na základě definovaných kritérií. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-4"></a>NIST 800-53 řízení ma-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Informace o systému monitorování | Komunikace příchozí a odchozí provoz

**Ma-4 (4)** informace systému monitoruje provoz příchozí a odchozí komunikace [přiřazení: organizace definované frekvence] pro neobvyklou nebo neoprávněné aktivity nebo podmínky.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za monitorování zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-5"></a>NIST 800-53 řízení ma-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>Informace o systému monitorování | Výstrahy generované systémem

**Ma-4 (5)** výstrahy systémové informace [přiřazení: definované organizace pracovníky nebo rolí] Pokud dojde k ohrožení nebo potenciální ohrožení těchto údajů: [přiřazení: indikátory definované organizace ohrožení].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí různých řešení pro správu, včetně řešení zabezpečení a auditování. Analýzy protokolů zajišťuje téměř v reálném čase analýzy událostí napříč nasazené prostředky. Řešení pro správu poskytuje komplexní pohled postavení zabezpečení napříč doménami řešení. Analýzy protokolů může být nakonfigurováno pro generování výstrahy na základě definovaných kritérií. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-11"></a>NIST 800-53 řízení ma-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Informace o systému monitorování | Analýza provozu anomálií komunikace

**Ma-4 (11)** organizace analyzuje odchozí komunikace provoz na externí hranice systému informace a vybrali [přiřazení: organizace definované interior body v rámci systému (například podsítě, subsystémy)] do zjišťovat anomálie.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za Analýza provozu anomálií komunikace pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-14"></a>NIST 800-53 řízení ma-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Informace o systému monitorování | Zjišťování neoprávněných vniknutí bezdrátové

**Ma-4 (14)** zahrnuje organizace pokusí systému zjišťování neoprávněných vniknutí bezdrátové identifikovat podvodný bezdrátových zařízení a zjištění útoku a potenciální ohrožení/narušení informace o systému.

**Odpovědnosti:** `Azure Only`

|||
|---|---|
| **Zákazníka** | Žádný řízenou zákazníka hardware, včetně bezdrátových zařízení, je povolena v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure se pravidelně monitoruje bezdrátové signálů podvodný čtvrtletně, jak je popsáno v AC 18. <br /> Microsoft Azure implementuje tento ovládací prvek jménem zákazníky PaaS a IaaS. |


 ### <a name="nist-800-53-control-si-4-16"></a>NIST 800-53 řízení ma-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>Informace o systému monitorování | Korelovat informacemi o monitorování

**Ma-4 (16)** organizace korelaci informace z monitorování nástroje, které jsou použity v celém systému informace.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého nasadí analýzy protokolů a různá řešení správy, včetně řešení zabezpečení a auditování. Log Analytics poskytuje přehled o stavu zabezpečení nasazené prostředky pomocí dat, které jsou k dispozici v řešení pro správu nasazené. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-18"></a>NIST 800-53 řízení ma-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Informace o systému monitorování | Analýza provozu / tajná Exfiltration

**Ma-4 (18)** organizace analyzuje odchozí komunikace provoz na externí hranic informace o systému (tj. hraniční system) a v [přiřazení: organizace definované interior body v rámci systému (například subsystémy, podsítě)] rozpoznat převeďte exfiltration informací.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za Analýza provozu komunikace pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-19"></a>NIST 800-53 řízení ma-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Informace o systému monitorování | Jednotlivce autority větší riziko

**Ma-4 (19)** organizace implementuje [přiřazení: organizace definované další monitorování] osob, které byly identifikovány [přiřazení: zdroje definované organizace] jako autority na vyšší úroveň rizika.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za monitorování jednotlivcům, kteří představovat větší riziko. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-20"></a>NIST 800-53 řízení ma-4 (20)

#### <a name="information-system-monitoring--privileged-users"></a>Informace o systému monitorování | Privilegovaní uživatelé

**Ma-4 (20)** organizace implementuje [přiřazení: organizace definované další monitorování] privilegovaných uživatelů.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je odpovědná za monitorování mohou uživatelé s oprávněním. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-22"></a>NIST 800-53 řízení ma-4 (22)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>Informace o systému monitorování | Neoprávněné síťové služby

**Ma-4 (22)** informace systém rozpozná síťové služby, které nebyly oprávnění nebo schválený [přiřazení: organizace definované autorizace nebo schválení procesy] a [výběr (jeden nebo více): audity; výstrahy [přiřazení: "definované organizace pracovníky nebo rolí]].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za vyhledávání podpory neoprávněným síťové služby. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-23"></a>NIST 800-53 řízení ma-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>Informace o systému monitorování | Zařízení založené na hostiteli

**Ma-4 (23)** organizace implementuje [přiřazení: definované organizace založená na hostiteli monitorování mechanismy] na [přiřazení: komponent systému informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Tento plán, podle kterého shromažďuje data monitorování z nasazené prostředky, včetně dat z možnosti monitorování založené na hostiteli. Microsoft Monitoring Agent je nainstalována na všechny virtuální počítače s Windows ke shromažďování dat monitorování používané analýzy protokolů a dalších řešení pro správu. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-4-24"></a>NIST 800-53 řízení ma-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>Informace o systému monitorování | Ukazatele ohrožení zabezpečení

**Ma-4 (24)** informace systém zjistí, shromažďuje, distribuuje a používá ukazatele ohrožení zabezpečení.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za vyhledávání, shromažďování, distribuci a pomocí ukazatele ohrožení k prostředkům nasazené zákazníka. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-5a"></a>Ovládací prvek NIST 800-53 ma 5.a

#### <a name="security-alerts-advisories-and-directives"></a>Výstrahy zabezpečení, zpravodaje a direktivy

**Ma 5.a** organizace obdrží informace výstrahy zabezpečení systému, zpravodaje a direktivy z [přiřazení: organizace definované externími organizacemi] průběžně.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za správu výstrahy zabezpečení, zpravodaje a direktivy pro zákazníka nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-5b"></a>Ovládací prvek NIST 800-53 ma 5.b

#### <a name="security-alerts-advisories-and-directives"></a>Výstrahy zabezpečení, zpravodaje a direktivy

**Ma 5.b** organizace generuje výstrahy interní zabezpečení, zpravodaje a direktivy, které jsou nezbytné.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za správu výstrahy zabezpečení, zpravodaje a direktivy pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-5c"></a>Ovládací prvek NIST 800-53 ma 5.c

#### <a name="security-alerts-advisories-and-directives"></a>Výstrahy zabezpečení, zpravodaje a direktivy

**Ma 5.c** organizace šíří výstrahy zabezpečení, zpravodaje a direktivy pro: [výběr (jeden nebo více): [přiřazení: organizace definované pracovníky nebo rolí]; [Přiřazení: organizace definované elementů v rámci organizace]; [Přiřazení: organizace definované externími organizacemi]].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za správu výstrahy zabezpečení, zpravodaje a direktivy pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-5d"></a>Ovládací prvek NIST 800-53 ma 5

#### <a name="security-alerts-advisories-and-directives"></a>Výstrahy zabezpečení, zpravodaje a direktivy

**Ma 5** organizace implementuje direktivy zabezpečení v souladu s vytvořeným časových intervalů nebo upozorní vystavující organizace stupeň nesplňujících požadavky.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za správu výstrahy zabezpečení, zpravodaje a direktivy pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-5-1"></a>NIST 800-53 řízení ma-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Výstrahy zabezpečení, zpravodaje a direktivy | Automatizované upozornění a informační zpravodaje

**Ma-5 (1)** organizace využívá automatizované mechanismy, mají-li zabezpečení výstrah a Poradní informace, které jsou k dispozici v celé organizaci.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za správu výstrahy zabezpečení, zpravodaje a direktivy pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-6a"></a>Ovládací prvek NIST 800-53 ma 6.a

#### <a name="security-function-verification"></a>Funkce ověření zabezpečení

**Ma 6.a** systému informace ověřuje správné fungování [přiřazení: funkce zabezpečení organizace definované].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za ověření funkce zabezpečení pro zákazníka nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-6b"></a>Ovládací prvek NIST 800-53 ma 6.b

#### <a name="security-function-verification"></a>Funkce ověření zabezpečení

**Ma 6.b** informace systému, provede toto ověření [výběr (jeden nebo více): [přiřazení: přechodové stavy definované organizace systému]; po příkazu uživatelem s odpovídající oprávnění; [Přiřazení: organizace definované frekvence]].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za ověření funkce zabezpečení pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-6c"></a>Ovládací prvek NIST 800-53 ma 6.c

#### <a name="security-function-verification"></a>Funkce ověření zabezpečení

**Ma 6.c** systém informace oznámení [přiřazení: definované organizace pracovníky nebo rolí] testů pro ověření zabezpečení se nezdařilo.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za ověření funkce zabezpečení pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-6d"></a>Ovládací prvek NIST 800-53 ma 6.d

#### <a name="security-function-verification"></a>Funkce ověření zabezpečení

**Ma 6.d** systémové informace [výběr (jeden nebo více): vypnutí systému informace; restartování systému informace; [Přiřazení: organizace definované alternativní akce]] Při zjištění anomálií.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za ověření funkce zabezpečení pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-7"></a>Ovládací prvek NIST 800-53 ma-7

#### <a name="software-firmware-and-information-integrity"></a>Software, firmwaru a informace o integrita

**Ma 7** organizace využívá nástroje pro ověření integrity k detekci neoprávněných změn [přiřazení: softwaru definované organizace, firmwaru a informace o].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán spustit operační systémy Windows. Systém Windows poskytuje ověření integrity souborů v reálném čase, ochrany a obnovení souborů jádra systému, které jsou nainstalovány v rámci systému Windows nebo autorizovaný aktualizací systému Windows prostřednictvím možnosti ochrany WRP prostředků Windows (.). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-7-1"></a>NIST 800-53 řízení ma-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Software, firmwaru a integrita informace | Kontroly integrity

**Ma-7 (1)** systém informace provádí kontrolu integrity systému [přiřazení: softwaru definované organizace, firmwaru a informace o] [výběr (jeden nebo více): při spuštění; na [přiřazení: přechodové stavy definované organizace nebo zabezpečení relevantní události]; [Přiřazení: organizace definované frekvence]].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán spustit operační systémy Windows. Systém Windows poskytuje ověření integrity souborů v reálném čase, ochrany a obnovení souborů jádra systému, které jsou nainstalovány v rámci systému Windows nebo autorizovaný aktualizací systému Windows prostřednictvím možnosti ochrany WRP prostředků Windows (.). WRP. umožňuje kontrolu v reálném čase integrity. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-7-2"></a>NIST 800-53 řízení ma-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Software, firmwaru a integrita informace | Automatizované oznámení o porušení Integrity

**Ma-7 (2)** organizace využívá automatizované nástroje, které poskytují oznámení [přiřazení: organizace definované pracovníky nebo rolí] při zjišťování nesrovnalostí během ověření integrity.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán spustit operační systémy Windows. Systém Windows poskytuje ověření integrity souborů v reálném čase, ochrany a obnovení souborů jádra systému, které jsou nainstalovány v rámci systému Windows nebo autorizovaný aktualizací systému Windows prostřednictvím možnosti ochrany WRP prostředků Windows (.).  |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-7-5"></a>NIST 800-53 řízení ma-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Software, firmwaru a integrita informace | Automatické reakce na porušení Integrity

**Ma-7 (5)** informace systému automaticky [výběr (jeden nebo více): vypnutí systému informace; restartování systému informace; implementuje [přiřazení: ochrana definované organizace zabezpečení]] při porušení integrity jsou zjistit.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za automaticky odpověď k narušení integrity v rámci zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-7-7"></a>NIST 800-53 řízení ma-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Software, firmwaru a integrita informace | Integrace detekce a odpovědi

**Ma-7 (7)** organizace zahrnuje detekce neoprávněným [přiřazení: definované organizaci relevantní zabezpečení změny systému informace] do funkce organizační reakcí na incidenty.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za ochranu integrity informace o softwaru a pro zákazníka nasazené prostředky. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-7-14"></a>NIST 800-53 řízení ma-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Software, firmwaru a integrita informace | Binární nebo počítače spustitelného kódu

**Ma-7 (14)** organizace zakáže použití binary nebo počítač spustitelný kód z zdroje s omezeným nebo žádné záruky a bez poskytování zdrojového kódu; a poskytuje výjimky požadavek na zdroj kódu pouze pro poutavé zvláště / provozní požadavky a po schválení povolující oficiální.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazníka podnikové úrovni systému a informace o integrity postupů může vytvořit požadavky získat zdrojový kód binary nebo počítače spustitelný kód z některé zdroje. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-8a"></a>Ovládací prvek NIST 800-53 ma 8.a

#### <a name="spam-protection"></a>Ochrany proti spamu

**Ma 8.a** organizace zahrnuje nevyžádané pošty ochrany mechanismy na informace o systému vstupní a výstupní místa ke zjišťování a provést akci pro nevyžádaných zpráv.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Nejsou žádné e-mailu servery nasazené jako součást tento plán. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-8b"></a>Ovládací prvek NIST 800-53 ma 8.b

#### <a name="spam-protection"></a>Ochrany proti spamu

**Ma 8.b** organizace aktualizace nevyžádané pošty ochranný mechanismus když nové verze jsou k dispozici v souladu s organizační konfigurace zásad správy a postupy.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Nejsou žádné e-mailu servery nasazené jako součást tento plán. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-8-1"></a>NIST 800-53 řízení ma-8 (1)

#### <a name="spam-protection--central-management"></a>Ochrana proti nevyžádané poště | Centrální správy

**Ma-8 (1)** organizace centrálně spravuje mechanismy ochrany proti spamu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Nejsou žádné e-mailu servery nasazené jako součást tento plán. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-si-8-2"></a>NIST 800-53 řízení ma-8 (2)

#### <a name="spam-protection--automatic-updates"></a>Ochrana proti nevyžádané poště | Funkce Automatické aktualizace

**(2) ve-8** informace systému automaticky aktualizuje mechanismy ochrany proti spamu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Nejsou žádné e-mailu servery nasazené jako součást tento plán. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-10"></a>Ovládací prvek NIST 800-53 ma-10

#### <a name="information-input-validation"></a>Informace o ověření vstupu

**Ma 10** informace systému kontroluje platnost [přiřazení: vstupy informace definované organizace].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník zodpovídá za ověření vstupu informace pro zákazníka nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-11a"></a>Ovládací prvek NIST 800-53 ma 11.a

#### <a name="error-handling"></a>Zpracování chyb

**Ma 11.a** generuje systém informace chybové zprávy, které obsahují informace potřebné pro opravné akce, aniž by odhalil informace, které může zneužít nežádoucí.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Prostředky nasadit pomocí tento plán, podle kterého použít komerční operačních systémů a aplikací softwaru. Tento software používá odvětví osvědčené postupy k zajištění, že citlivé informace, že by v chybových zprávách. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-11b"></a>Ovládací prvek NIST 800-53 ma 11.b

#### <a name="error-handling"></a>Zpracování chyb

**Ma 11.b** systému informace zobrazí chybové zprávy se jenom pro [přiřazení: organizace definované pracovníky nebo rolí].

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Prostředky nasadit pomocí tento plán, podle kterého použít komerční operačních systémů a aplikací softwaru. Tento software používá odvětví osvědčené postupy k poskytnutí chybové zprávy, které jsou vhodné v kontextu používá danou zprávu přijala. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-12"></a>Ovládací prvek NIST 800-53 ma – 12

#### <a name="information-handling-and-retention"></a>Informace o zpracování a uchovávání

**Ma – 12** organizace zpracovává a zachová informace v rámci systému informace a výstup informace ze systému v souladu s platnými zákony federal, objednávky vedení, direktivy, zásady, nařízení, standardy, a provozní požadavky.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za zpracování a informace v rámci zákazníka nasazené prostředky (třeba aplikace, operační systémy, databází a softwaru) a informace o výstup z těchto prostředků zůstanou zachovány. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-si-16"></a>Ovládací prvek NIST 800-53 ma-16

#### <a name="memory-protection"></a>Ochrana paměti

**Ma-16** systém informace implementuje [přiřazení: ochrana definované organizace zabezpečení] k ochraně jeho paměť z provádění neoprávněným kódu.

**Odpovědnosti:** `Customer Only`

|||
|---|---|
| **Zákazníka** | Virtuální počítače nasazené pomocí tento plán spustit operační systémy Windows. Windows má zavedené brání spuštění kódu v umístění s omezeným přístupem paměti ochrana: Ne Execute (NX), místo rozložení náhodného přeskupování (technologie ASLR) adresu a zabránění spuštění dat (DEP). |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |
