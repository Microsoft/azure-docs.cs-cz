---
title: Co je Azure Security Center? | Microsoft Docs
description: Můžete se dozvědět o službě Azure Security Center, jejích klíčových funkcích a způsobu práce.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: bd0e517845b9cfcbe6090dff8d656edcca782c83
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126288"
---
# <a name="what-is-azure-security-center"></a>Co je Azure Security Center?
Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami. Se službou Security Center můžete používat zásady zabezpečení napříč úlohami, omezit vystavení hrozbám a detekovat útoky a reagovat na ně.

Proč používat Security Center?

- **Centralizovaná správa zásad** – Zajistěte dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení díky centrální správě zásad zabezpečení napříč všemi hybridními cloudovými úlohami.
- **Nepřetržité posuzování zabezpečení** – Monitorujte stav zabezpečení počítačů, sítí, služeb úložiště, datových služeb a aplikací za účelem zjišťování potenciálních problémů se zabezpečením.
- **Užitečná doporučení** – Napravujte ohrožení zabezpečení dříve, než je budou moci zneužít útočníci, díky užitečným doporučením zabezpečení s určenou prioritou.
- **Výstrahy a incidenty s určenou prioritou** – Zaměřte se nejprve na nejdůležitější hrozby díky výstrahám a incidentům zabezpečení.
- **Pokročilá cloudová ochrana** – Omezte hrozby díky přístupu k portům pro správu podle potřeby a adaptivnímu řízení aplikací spuštěných na virtuálních počítačích.
- **Integrovaná řešení zabezpečení** – Shromažďujte, prohledávejte a analyzujte data o zabezpečení z nejrůznějších zdrojů, včetně propojených partnerských řešení.

Stránka **Security Center – Přehled** poskytuje rychlý přehled stavu zabezpečení úloh v Azure i mimo Azure a umožňuje tak zkoumat a posuzovat zabezpečení úloh a identifikaci a omezení rizika. Integrovaný řídicí panel poskytuje okamžitý přehled o výstrahách a ohroženích zabezpečení vyžadujících pozornost.

![Přehled][1]

## <a name="centralized-policy-management"></a>Centralizovaná správa zásad
Oddíl **Zásady a dodržování předpisů** (zobrazený nahoře) poskytuje rychlé informace o pokrytí předplatného, zásadách dodržování předpisů a stavu zabezpečení.

### <a name="subscription-coverage"></a>Pokrytí předplatného
Tento oddíl zobrazuje celkový počet předplatných, ke kterým máte přístup (pro čtení a zápis) a úroveň pokrytí předplatného v rámci Security Center (Standard nebo Free):

- **Pokryto (Standard)** – pokrytá předplatná se provozují při maximální úrovni ochrany, kterou Security Center nabízí.
- **Pokryto (Free)** – pokrytá předplatná se provozují při omezené bezplatné úrovni ochrany, kterou Security Center nabízí.
- **Nepokryto** – předplatná v tomto stavu nejsou službou Security Center monitorována.

Při výběru grafu se otevře okno **Pokrytí**. Výběrem některé karty (**Nepokryto**, **Základní pokrytí** nebo **Standardní pokrytí**) zobrazíte seznam předplatných v daném stavu. Výběrem předplatného na některé z těchto karet zobrazíte další informace o tomto předplatném. Tyto informace vám pomohou zjistit vlastníka předplatného a požádat ho o povolení služby Security Center nebo zvýšení pokrytí předplatného.

![Pokrytí službou Security Center][9]

### <a name="policy-compliance"></a>Dodržování zásad
Dodržování zásad je určeno faktory dodržování všech přiřazených zásad. Celkové skóre dodržování předpisů skupiny pro správu, předplatného nebo pracovního prostoru je určeno váženým průměrem těchto přiřazení. Tento vážený průměr ovlivňuje počet zásad v jednom přiřazení a počet prostředků, na které se přiřazení vztahuje.

Například pokud předplatné obsahuje dva virtuální počítače a má přiřazenou iniciativu s pěti zásadami, pak máte v předplatném deset interních hodnocení. Pokud jeden z virtuálních počítačů nedodržuje dvě zásady, pak je celkové skóre dodržování předpisů přiřazení vašeho předplatného 80 %.

V tomto oddílu se zobrazuje vaše celková míra dodržování předpisů a nejméně vyhovující předplatná. Výběrem možnosti **Zobrazit dodržování zásad v prostředí** otevřete okno **Správa zásad**. **Správa zásad** zobrazuje hierarchickou strukturu skupin pro správu, předplatných a pracovních prostorů. Výběrem předplatného nebo skupiny pro správu zde můžete spravovat zásady zabezpečení.

![Správa zásad][10]

Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení. Ve službě Security Center definujete zásady a přizpůsobíte je typu úlohy nebo citlivosti dat. Tím určíte, co má Security Center monitorovat a doporučovat. Kliknutím na skupinu pro správu nebo předplatné můžete zásady zabezpečení ve službě Security Center upravit. Můžete také použít Azure Policy a vytvořit nové definice, definovat další zásady a přiřazovat zásady skupinám pro správu.

Výběrem možnosti **Upravit nastavení >** upravíte následující nastavení služby Security Center na úrovni předplatného, skupiny pro správu, skupiny prostředků nebo pracovního prostoru:

- **Shromažďování dat:** Určuje zřizování agentů a nastavení [shromažďování dat](security-center-enable-data-collection.md) zabezpečení.
- **E-mailová oznámení:** Určuje kontakty zabezpečení a nastavení [e-mailových oznámení](security-center-provide-security-contact-details.md).
- **Cenová úroveň:** Definujete [výběr cen](security-center-pricing.md) úrovně Free nebo Standard. Zvolená úroveň určí, které funkce Security Center budou dostupné pro prostředky v daném rozsahu.
- **Upravit konfigurace zabezpečení:** Umožňuje zobrazit a změnit konfigurace operačního systému, jejichž posouzením identifikuje Security Center potenciální ohrožení zabezpečení.

Další informace najdete v tématu [Integrace zásad zabezpečení ve službě Security Center se službou Azure Policy](security-center-azure-policy.md).

### <a name="manage-and-govern-your-security-posture"></a>Správa a řízení stavu zabezpečení
V oblasti **Zásady a dodržování předpisů** na pravé straně řídicího panelu najdete přehledy, na jejichž základě můžete okamžitě zlepšit svůj stav zabezpečení. Můžete například:

- Definovat a přiřadit zásady služby Security Center kvůli kontrole a sledování dodržování předpisů podle standardů zabezpečení
- Zpřístupnit výstrahy zabezpečení služby Security Center konektoru SIEM
- Dodržování předpisů v průběhu času

## <a name="continuous-security-assessment"></a>Nepřetržité posuzování zabezpečení
Oddíl Hygiena zabezpečení prostředků v oblasti **Security Center – přehled** nabízí rychlý pohled na hygienu zabezpečení prostředků a zobrazuje počet zjištěných problémů a stav zabezpečení jednotlivých typů prostředků. Nepřetržité posuzování pomáhá zjišťovat potenciální problémy se zabezpečením, jako jsou systémy s chybějícími aktualizacemi zabezpečení nebo zveřejněné síťové porty.

### <a name="secure-score"></a>Bezpečnostní skóre
Bezpečnostní skóre služby Azure Security Center posuzuje vaše doporučení k zabezpečení a řadí je podle priority, takže víte, která doporučení provést jako první. To vám pomůže najít nejvážnější ohrožení zabezpečení, abyste mohli vyšetřování vést správným směrem. Bezpečnostní skóre je měřicím nástrojem, který vám pomáhá posílit zabezpečení a dosáhnout zabezpečení úlohy. Další informace najdete v článku o [bezpečnostním skóre ve službě Azure Security Center](security-center-secure-score.md).

### <a name="health-monitoring"></a>Monitorování stavu
Při výběru typu prostředku v oblasti **Monitorování stavu prostředků** se zobrazí seznam prostředků a všech zjištěných ohrožení zabezpečení. Mezi typy prostředků patří výpočty a aplikace, sítě, data a úložiště a identita a přístup.

Vybrali jsme **Výpočty a aplikace**. Oblast **Výpočty** obsahuje čtyři karty:

- **Přehled:** Monitorování a doporučení identifikovaná službou Security Center
- **Virtuální počítače a počítače:** Seznam virtuálních počítačů, počítačů a aktuální stav jejich zabezpečení
- **Cloudové služby:** Seznam webových a pracovních rolí monitorovaných službou Security Center
- **App Service (Preview):** Seznam webových aplikací a prostředí App Service a aktuální stav jejich zabezpečení

![Monitorování stavu zabezpečení][3]

Další informace najdete v článku o [monitorování stavu zabezpečení](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Užitečná doporučení
Security Center analyzuje stav zabezpečení vašich prostředků Azure i mimo Azure a identifikuje potenciální ohrožení zabezpečení. Při výběru možnosti **Doporučení** v oblasti **Prostředky** se zobrazí seznam doporučení k zabezpečení seřazený podle priority, který vás provede procesem řešení problémů se zabezpečením.

![Doporučení][4]

Další informace najdete v článku o [správě doporučení k zabezpečení](security-center-recommendations.md).

### <a name="most-prevalent-recommendations"></a>Převládající doporučení
Oblast **Prostředky** na pravé straně řídicího panelu nabízí seznam převládajících doporučení, která platí pro největší počet prostředků. Převládající doporučení udávají, kam byste měli upřít svou pozornost. Při výběru šipky vpravo se zobrazí doporučení s největším dopadem.

![Převládající doporučení][11]

Jedná se o jediné doporučení, které má ten největší dopad v rámci vašeho prostředí. Jeho provedení povede k maximálnímu vylepšení dodržování předpisů. V tomto příkladu se doporučení týká použití šifrování disku. Výběrem možnosti **Zlepšit dodržování předpisů** zobrazíte popis tohoto doporučení a seznam ovlivněných prostředků.

![Použít šifrování disku][12]

## <a name="threat-protection"></a>Ochrana před hrozbami
Tato oblast obsahuje přehled výstrah zabezpečení zjištěných u prostředků a úroveň závažnosti těchto výstrah.

### <a name="prioritized-alerts-and-incidents"></a>Výstrahy a incidenty s určenou prioritou
Security Center s využitím pokročilých analýz a globální analýzy hrozeb detekuje příchozí útoky a aktivitu po porušení zabezpečení. U výstrah se určuje priorita a seskupují se do incidentů, které pomáhají soustředit se nejprve na nejdůležitější hrozby. Můžete také vytvářet vlastní výstrahy zabezpečení.

Výběrem možnosti **Upozornění zabezpečení podle závažnosti** nebo **Upozornění zabezpečení v průběhu času** zobrazíte podrobné informace o výstrahách.

![Výstrahy a incidenty s určenou prioritou][7]

Můžete rychle posoudit rozsah a dopad útoku s využitím vizuálního a interaktivního prostředí pro šetření a blíže zkoumat data zabezpečení s využitím předdefinovaných nebo ad hoc dotazů.

Další informace najdete v článku o [správě výstrah zabezpečení a reakci na ně](security-center-managing-and-responding-alerts.md).

Pravá strana řídicího panelu obsahuje informace, na jejichž základě určíte, které výstrahy vyřešit jako první, a zjistíte, kde jsou nejběžnější ohrožení zabezpečení:

- **Nejčastěji napadané prostředky:** Konkrétní prostředky s největším počtem výstrah
- **Převládající upozornění:** Typy výstrah, které ovlivňují největší počet prostředků

## <a name="just-in-time-vm-access"></a>Přístup k virtuálním počítačům podle potřeby
Omezte plochu vystavenou síťovým útokům díky řízenému přístupu k portům pro správu virtuálních počítačů Azure podle potřeby a výrazně tak omezte vystavení útokům hrubou silou a jiným síťovým útokům.

![Přístup k virtuálním počítačům podle potřeby][5]

Určete pravidla způsobu, jakým se uživatelé můžou připojovat k virtuálním počítačům. V případě potřeby je o přístup možné zažádat ze služby Security Center nebo přes PowerShell. Pokud je žádost v souladu s pravidly, automaticky se udělí přístup po požadovanou dobu.

Další informace najdete v tématu popisujícím [správu přístupu k virtuálním počítačům pomocí metody právě včas](security-center-just-in-time.md).

## <a name="adaptive-application-controls"></a>Adaptivní řízení aplikací
Blokujte malware a další nechtěné aplikace použitím doporučení pro přidání na seznam povolených upravených pro vaše konkrétní úlohy Azure a využívajících strojové učení.

![Adaptivní řízení aplikací][6]

Kontrolujte a kliknutím používejte doporučená pravidla přidávání aplikací na seznam povolených generovaná službou Security Center nebo upravujte již nakonfigurovaná pravidla.

Další informace najdete v článku [Adaptivní řízení aplikací](security-center-adaptive-application.md).

## <a name="integrate-your-security-solutions"></a>Integrace řešení zabezpečení
V Security Center můžete shromažďovat, prohledávat a analyzovat data zabezpečení z nejrůznějších zdrojů, včetně propojených partnerských řešení, jako jsou síťové brány firewall a další služby Microsoftu.

![Integrace řešení zabezpečení][8]

Další informace najdete v článku [Integrace řešení zabezpečení](security-center-partner-integration.md).

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít využívat Security Center, musíte mít předplatné Microsoft Azure. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Cenová úroveň Free služby Security Center je povolená s vaším předplatným Azure. Pokud chcete využívat pokročilé možnosti správy zabezpečení a detekce hrozeb, musíte upgradovat na cenovou úroveň Standard. Úroveň Standard je po dobu prvních 60 dní zdarma. Další informace najdete na [stránce s cenami za Security Center](https://azure.microsoft.com/pricing/details/security-center/).
- Pokud už jste připraveni povolit Security Center, článek [Rychlý Start: Onboarding předplatného Azure na Security Center Standard](security-center-get-started.md) vás provede postupem.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
[9]: ./media/security-center-intro/coverage.png
[10]: ./media/security-center-intro/policy-management.png
[11]: ./media/security-center-intro/highest-impact.png
[12]: ./media/security-center-intro/apply-disk-encryption.png
