---
title: Poznámky k verzi pro Azure Security Center
description: Popis toho, co je nového a co se změnilo v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: f41b87f50dfac15c6228398a2c9d1c6ae470903d
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260929"
---
# <a name="whats-new-in-azure-security-center"></a>Co je nového v Azure Security Center?

Zabezpečení Azure je v aktivním vývoji a průběžně přijímá vylepšení. Tato stránka vám poskytne informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nové funkce
- Opravy chyb
- Zastaralé funkce

Tato stránka se pravidelně aktualizuje, takže ji můžete často znovu navštěvovat. Pokud hledáte položky starší než šest měsíců, najdete je v archivu, kde najdete novinky [v Azure Security Center](release-notes-archive.md).


## <a name="june-2020"></a>Červen 2020

### <a name="secure-score-api-preview"></a>Rozhraní API pro hodnocení zabezpečení (Preview)

K vašemu skóre teď můžete přistupovat prostřednictvím [rozhraní API pro zabezpečené skóre](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (aktuálně ve verzi Preview). Metody rozhraní API poskytují flexibilitu pro dotazování na data a vytváření vlastních mechanismů generování sestav v rámci vašich zabezpečených výsledků v průběhu času. Můžete například použít rozhraní API pro **zabezpečení skóre** k získání skóre pro konkrétní předplatné. Kromě toho můžete použít rozhraní API **ovládacích prvků bezpečného řízení skóre** k vypsání ovládacích prvků zabezpečení a aktuálního skóre vašich předplatných.

Příklady externích nástrojů, které jsou dostupné s rozhraním API pro zabezpečené skóre, najdete v části [bezpečné skóre naší komunity GitHubu](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Přečtěte si další informace o [zabezpečeném řízení hodnocení a zabezpečení v Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Pokročilé zabezpečení dat pro počítače s SQL (Azure, ostatní cloudy a Prem) (Preview)

Azure Security Center pokročilé zabezpečení dat pro počítače SQL teď chrání SQL servery hostované v Azure, v jiných cloudových prostředích i v místních počítačích. Tím se rozšiřuje ochrana vašich serverů SQL Azure Native tak, aby plně podporovala hybridní prostředí.

Rozšířené zabezpečení dat poskytuje posouzení ohrožení zabezpečení a rozšířenou ochranu před internetovými útoky pro vaše počítače SQL bez ohledu na jejich umístění.

Instalace se skládá ze dvou kroků:

1. Nasazení agenta Log Analytics do hostitelského počítače SQL Server, aby se zajistilo připojení k účtu Azure.

1. Povolení volitelného balíčku na stránce ceny a nastavení Security Center.

Přečtěte si další informace o [pokročilém zabezpečení dat pro počítače s SQL](security-center-iaas-advanced-data.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Dvě nová doporučení pro nasazení agenta Log Analytics do počítačů ARC v Azure (Preview)

Přidali jsme dvě nová doporučení, která vám pomůžou nasadit [agenta Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) do vašich počítačů s obloukem Azure ARC a zajistit, aby byly chráněné pomocí Azure Security Center:

- **Agent Log Analytics musí být nainstalovaný na počítačích se systémem Windows Azure ARC (Preview).**
- **Agent Log Analytics musí být nainstalovaný na počítačích se systémem Linux (Preview).**

Tato nová doporučení se zobrazí ve stejných čtyřech kontrolních prvcích zabezpečení jako stávající (související) doporučení, **Agent monitorování by měl být nainstalovaný na vašich počítačích**: opravit konfigurace zabezpečení, použít adaptivní řízení aplikací, použít aktualizace systému a povolit službu Endpoint Protection.

Doporučení také obsahují možnosti rychlé opravy, které vám pomůžou urychlit proces nasazení. 

Další informace o těchto dvou nových doporučeních najdete v tabulce s [doporučeními pro výpočty a aplikace](recommendations-reference.md#recs-computeapp) .

Přečtěte si další informace o tom, jak Azure Security Center používá agenta v tématu [co je agent Log Analytics?](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent).

Přečtěte si další informace o [rozšířeních pro počítače s obloukem Azure ARC](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nové doporučení pro použití skupin zabezpečení sítě k ochraně virtuálních počítačů, které nejsou přístupné pro Internet

Kontrola zabezpečení "implementovat osvědčené postupy zabezpečení" teď obsahuje následující nové doporučení:

- **Virtuální počítače, které nejsou přístupné z Internetu, by měly být chráněné pomocí skupin zabezpečení sítě**

Stávající doporučení: **internetové virtuální počítače by měly být chráněné pomocí skupin zabezpečení sítě**, nerozlišují se mezi internetovými a neinternetovými virtuálními počítači. U obou se vygenerovalo doporučení s vysokou závažností, pokud se virtuální počítač nepřiřadil do skupiny zabezpečení sítě. Toto nové doporučení odděluje nepřístupné počítače k Internetu, aby se snížily falešně pozitivní a vyhnuli se zbytečným výstrahám s vysokou závažností.

Další informace najdete v tabulce [doporučení sítě](recommendations-reference.md#recs-network) .



## <a name="may-2020"></a>Květen 2020


### <a name="alert-suppression-rules-preview"></a>Pravidla pro potlačení výstrah (Preview)

Tato nová funkce (aktuálně ve verzi Preview) pomáhá snižovat únavu výstrah. Pomocí pravidel můžete automaticky skrýt výstrahy, které se neškodného nebo které souvisejí s běžnými aktivitami ve vaší organizaci. To vám umožní soustředit se na nejrelevantnější hrozby. 

Budou se vygenerovat i výstrahy, které odpovídají povoleným pravidlům potlačení, ale jejich stav se nastaví na zastaveno. Můžete zobrazit stav v Azure Portal, nebo máte přístup k výstrahám zabezpečení Security Center.

Pravidla potlačení definují kritéria, pro která se mají automaticky odkládat výstrahy. Obvykle byste pravidlo potlačení používali k těmto akcím:

- potlačit výstrahy, které jste identifikovali jako falešně pozitivní

- potlačit výstrahy, které se spouštějí příliš často, aby byly užitečné

Přečtěte si další informace o [potlačení výstrah z ochrany před hrozbami v Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Posouzení ohrožení zabezpečení virtuálního počítače je teď všeobecně dostupné

Úroveň Standard Security Center nyní zahrnuje integrované posouzení ohrožení zabezpečení pro virtuální počítače bez dalších poplatků. Toto rozšíření používá Qualys, ale oznamuje své závěry přímo zpátky do Security Center. Nepotřebujete licenci Qualys ani účet Qualys – všechno se bez problémů zpracovává v Security Center.

Nové řešení může průběžně kontrolovat vaše virtuální počítače a odhalit tak ohrožení zabezpečení a prezentovat zjištění v Security Center. 

K nasazení řešení použijte nové doporučení zabezpečení:

"Povolte integrované řešení posouzení ohrožení zabezpečení na virtuálních počítačích (s technologií Qualys)."

Přečtěte si další informace o [integrovaném posouzení ohrožení zabezpečení Security Center pro virtuální počítače](built-in-vulnerability-assessment.md).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Změny přístupu k virtuálnímu počítači JIT (just-in-time)

Security Center obsahuje volitelnou funkci pro ochranu portů pro správu vašich virtuálních počítačů. To zajišťuje ochranu před nejběžnějšími formami útoků hrubou silou.

Tato aktualizace přináší následující změny této funkce:

- Doporučení, které doporučuje povolit JIT na virtuálním počítači, bylo přejmenováno. Dřív je potřeba na virtuálních počítačích použít řízení přístupu k síti za běhu: porty pro správu virtuálních počítačů by se měly chránit pomocí řízení přístupu k síti za běhu.

- Doporučení je nastavené tak, aby se aktivovalo jenom v případě, že existují otevřené porty pro správu.

Přečtěte si další informace o [funkci přístupu JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Vlastní doporučení se přesunula do samostatného řízení zabezpečení.

Jedním z bezpečnostních mechanismů zavedených pomocí zvýšeného zabezpečeného skóre bylo "implementace osvědčených postupů zabezpečení". Všechna vlastní doporučení vytvořená pro vaše předplatná se automaticky umístila do tohoto ovládacího prvku. 

Aby bylo snazší najít vaše vlastní doporučení, přesunuli jsme je do vyhrazeného řízení zabezpečení "Custom doporučení". Tento ovládací prvek nemá žádný vliv na vaše zabezpečené skóre.

Přečtěte si další informace o ovládacích prvcích zabezpečení v [rozšířeném zabezpečeném skóre (Preview) v Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Přepínač přidaný pro zobrazení doporučení v ovládacích prvcích nebo jako nestrukturovaný seznam

Řízení zabezpečení jsou logické skupiny souvisejících doporučení zabezpečení. Odrážejí vaše zranitelné plochy pro útok. Ovládací prvek je sada doporučení zabezpečení s pokyny, které vám pomůžou implementovat tato doporučení.

Pokud chcete hned zjistit, jak dobře vaše organizace zabezpečuje jednotlivé plochy pro útok, Projděte si skóre pro jednotlivé ovládací prvky zabezpečení.

Ve výchozím nastavení se vaše doporučení zobrazují v ovládacích prvcích zabezpečení. Z této aktualizace je můžete zobrazit také jako seznam. Pokud je chcete zobrazit jako jednoduchý seznam seřazený podle stavu ovlivněných prostředků, použijte nový přepínač seskupit podle ovládacích prvků. Přepínač je nad seznamem na portálu.

Ovládací prvky zabezpečení – a tento přepínač jsou součástí nového prostředí pro zajištění skóre. Nezapomeňte nám poslat svůj názor přímo z portálu.

Přečtěte si další informace o ovládacích prvcích zabezpečení v [rozšířeném zabezpečeném skóre (Preview) v Azure Security Center](secure-score-security-controls.md).


### <a name="expanded-security-control-implement-security-best-practices"></a>Rozšířené řízení zabezpečení "implementovat osvědčené postupy zabezpečení" 

Jedním z ovládacích prvků zabezpečení zavedených pomocí rozšířeného zabezpečeného skóre je implementace osvědčených postupů zabezpečení. Pokud je v tomto ovládacím prvku doporučení, nemá to vliv na zabezpečené skóre. 

V této aktualizaci se tři doporučení přesunuly mimo ovládací prvky, ve kterých byly původně umístěné, a do tohoto osvědčeného postupu. Provedli jsme tento krok, protože jsme zjistili, že riziko těchto tří doporučení je nižší, než bylo původně v myšlenkě.

Kromě toho byla do tohoto ovládacího prvku zavedena dvě nová doporučení a přidána.

Existují tři doporučení, která jste přesunuli:

- **Pro účty s oprávněním ke čtení v předplatném** (původně v ovládacím prvku povolit MFA) by měla být povolená MFA.
- **Externí účty s oprávněním ke čtení by se měly z předplatného odebrat** (původně v ovládacím prvku Správa přístupu a oprávnění).
- **Pro vaše předplatné by se měla určit maximálně 3 vlastníci** (v ovládacím prvku Správa přístupu a oprávnění).

K ovládacímu prvku jsou přidána dvě nová doporučení:

- **Rozšíření konfigurace hosta by se mělo nainstalovat na virtuální počítače s Windows (verze Preview)** – pomocí [Azure Policy konfigurace hosta](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) poskytuje viditelnost ve virtuálních počítačích s nastavením serveru a aplikací (jenom Windows).

- **Na vašich počítačích by mělo být povolené ochrany před zneužitím v programu Windows Defender (Preview)** – ochrana před zneužitím v programu Windows defender využívá Azure Policyho agenta konfigurace hostů. Ochrana před zneužitím má čtyři komponenty, které jsou navržené tak, aby se zařízení zamkly na širokou škálu útoků, a to v případě, že se v případě malwaru povoluje vyrovnávat rizika zabezpečení a požadavky na produktivitu (jenom Windows).

Další informace o systému Windows Defender zneužití Guard v [vytváření a nasazení zásad pro ochranu před zneužitím](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Přečtěte si další informace o ovládacích prvcích zabezpečení ve [zvýšeném zabezpečeném skóre (Preview)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Vlastní zásady s vlastními metadaty jsou teď všeobecně dostupné.

Vlastní zásady jsou teď součástí prostředí Security Center doporučení, bezpečného skóre a řídicího panelu standardů dodržování předpisů regulativních předpisů. Tato funkce je teď všeobecně dostupná a umožňuje rozšíření pokrytí posouzení zabezpečení vaší organizace v Security Center. 

Vytvořte vlastní iniciativu v zásadách Azure, přidejte do ní zásady a připojte je k Azure Security Center a vizualizujte je jako doporučení.

Nyní jsme také přidali možnost Upravit metadata vlastního doporučení. Mezi možnosti metadat patří závažnost, nápravný postup, informace o hrozbách a další.  

Přečtěte si další [informace o vylepšení vlastních doporučení s podrobnými informacemi](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Funkce analýzy výpisu stavu systému při migraci na detekci útoku typu soubor 

Do [detekce útoku typu soubor](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless)se integruje možnosti detekce CDA (Windows Crash Analysis). Analýza detekce útoků bez souborů přináší vylepšené verze těchto výstrah zabezpečení pro počítače s Windows: zjištěné injektáže kódu, zjištěná maskující modul Windows, zjištěná skrytý spustitelný kód a zjištěn podezřelý segment kódu.

Některé z výhod tohoto přechodu:

- **Proaktivní a včasné zjišťování malwaru** – přístup k diskovým diskům (CDA) se zabývá tím, že se nevyskytne chyba a následně se spustí analýza, aby bylo možné najít škodlivé Při použití detekce útoků pomocí souborů dojde k proaktivní identifikaci hrozeb v paměti, které jsou spuštěny. 

- **Obohacené výstrahy** – výstrahy zabezpečení z detekce útoků bez souborů zahrnují rozšíření, která nejsou k dispozici ze služby CDA, například informace o aktivních síťových připojeních. 

- **Agregace výstrah** – Pokud disk CDA zjistil více vzorů útoku v rámci jednoho výpisu stavu systému, aktivovalo se několik výstrah zabezpečení. Detekce útoků bez souborů kombinuje všechny identifikované vzory útoků ze stejného procesu do jediné výstrahy. tím se eliminuje nutnost korelace více výstrah.

- **Omezené požadavky na pracovní prostor Log Analytics** – výpisy stavu systému obsahující potenciálně citlivá data se už nebudou nahrávat do vašeho pracovního prostoru Log Analytics.



## <a name="april-2020"></a>Duben 2020

### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamické balíčky pro dodržování předpisů jsou teď všeobecně dostupné.

Řídicí panel dodržování předpisů Azure Security Center nyní zahrnuje **dynamické balíčky kompatibility** (teď všeobecně dostupné) ke sledování dalších odvětví a regulativních standardů.

Dynamické balíčky pro dodržování předpisů můžete přidat do svého předplatného nebo skupiny pro správu ze stránky Security Center zásady zabezpečení. Po zprovoznění standardního nebo srovnávacího testu se standardně zobrazí na řídicím panelu dodržování předpisů, kde jsou všechna přidružená data o dodržování předpisů namapovaná jako posouzení. Zobrazí se souhrnná zpráva o všech standardech, které jsou k dispozici ke stažení.

Nyní můžete přidat standardy jako:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **Oficiální Velká Británie a Velká Británie NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nový)** (což je ucelenější reprezentace Azure CIS 1.1.0)

Kromě toho jsme nedávno přidali **srovnávací testy zabezpečení Azure**, které jsou určené pro zabezpečení a osvědčené postupy pro dodržování předpisů v závislosti na běžných architekturách dodržování předpisů, a pokyny specifické pro Azure. Další standardy budou na řídicím panelu podporovány, jakmile budou k dispozici.  
 
Přečtěte si další informace o [přizpůsobení sady standardů na řídicím panelu dodržování legislativních předpisů](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Doporučení identity teď jsou součástí Azure Security Center úrovně Free.

Doporučení zabezpečení pro identitu a přístup na úrovni Free Azure Security Center jsou teď všeobecně dostupná. Tato funkce je součástí snahy o zpřístupnění funkcí CSPM (Cloud Security stav Management) zdarma. Až do této chvíle byly tato doporučení dostupná jenom na cenové úrovni Standard.

Příklady doporučení pro identitu a přístup zahrnují:

- U účtů s oprávněním vlastníka v předplatném by mělo být povoleno vícefaktorové ověřování.
- "Pro vaše předplatné by se měla určit maximálně tři vlastníci."
- "Zastaralé účty by se měly odebrat z vašeho předplatného".

Pokud máte předplatné na cenové úrovni Free, jejich zabezpečená skóre budou ovlivněna touto změnou, protože nebyly nikdy posuzovány pro jejich identitu a zabezpečení přístupu.

Další informace o [identitě a doporučeních pro přístup](recommendations-reference.md#recs-identity).

Přečtěte si další informace o [identitě a přístupu k monitorování](security-center-identity-access.md).


## <a name="march-2020"></a>Březen 2020

### <a name="workflow-automation-is-now-generally-available"></a>Automatizace pracovních postupů je teď všeobecně dostupná.

Funkce automatizace pracovního postupu Azure Security Center je teď všeobecně dostupná. Použijte ji k automatické aktivaci Logic Apps výstrah zabezpečení a doporučení. Kromě toho jsou k dispozici ruční aktivační události pro výstrahy a všechna doporučení, která mají možnost Rychlá oprava k dispozici.

Každý program zabezpečení zahrnuje několik pracovních postupů pro reakci na incidenty. Tyto procesy mohou zahrnovat oznamování relevantních účastníků, spuštění procesu správy změn a uplatnění specifických kroků k nápravě. Odborníci na zabezpečení doporučují automatizovat tolik kroků těchto postupů, jako je to možné. Automatizace snižuje režijní náklady a může zvýšit zabezpečení tím, že zajistí, že kroky procesu budou provedeny rychle, konzistentně a podle vašich předdefinovaných požadavků.

Další informace o funkcích automatických a ručních Security Center pro spouštění pracovních postupů najdete v tématu [automatizace pracovních postupů](workflow-automation.md).

Přečtěte si další informace o [vytváření Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integrace Azure Security Center s centrem pro správu Windows

Je teď možné přesunout místní servery Windows z centra pro správu Windows přímo na Azure Security Center. Security Center pak se v jednom podokně skla zobrazí informace o zabezpečení pro všechny prostředky centra pro správu systému Windows, včetně místních serverů, virtuálních počítačů a dalších PaaS úloh.

Po přesunutí serveru z centra pro správu Windows na Azure Security Center budete moct:

- Zobrazení výstrah zabezpečení a doporučení v rozšíření Security Center centra pro správu systému Windows.
- Prohlédněte si stav zabezpečení a načtěte další podrobné informace o spravovaných serverech centra pro správu Windows v Security Center v rámci Azure Portal (nebo přes rozhraní API).

Přečtěte si další informace o [tom, jak integrovat Azure Security Center s centrem pro správu Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Ochrana pro službu Azure Kubernetes

Azure Security Center rozšiřuje své funkce zabezpečení kontejnerů, aby chránily službu Azure Kubernetes Service (AKS).

Oblíbená Open Source platforma Kubernetes byla přijata, takže je teď v oboru standard pro orchestraci kontejnerů. Navzdory této rozšířené implementaci se stále ještě nepředpokládá, jak zabezpečit Kubernetes prostředí. Ochrana předních ploch v kontejnerových aplikacích vyžaduje, aby bylo zajištěno zabezpečené a nepřetržité monitorování infrastruktury pro potenciální hrozby.

Security Center obrany zahrnuje:

- **Zjišťování a viditelnost** – nepřetržité zjišťování spravovaných instancí AKS v rámci předplatných registrovaných pro Security Center.
- **Doporučení zabezpečení** – doporučení pro dodržování předpisů, která vám pomůžou dodržovat osvědčené postupy zabezpečení pro AKS. Tato doporučení jsou zahrnutá ve vašem zabezpečeném skóre, abyste se ujistili, že se zobrazují jako součást stav zabezpečení vaší organizace. Příklad doporučení týkající se AKS najdete v tématu řízení přístupu na základě role by mělo sloužit k omezení přístupu ke clusteru služby Kubernetes.
- **Ochrana před hrozbami** – prostřednictvím průběžné analýzy nasazení AKS vás Security Center vás upozorní na hrozby a škodlivou aktivitu zjištěnou na úrovni clusteru host a AKS.

Přečtěte si další informace o [integraci služeb Azure Kubernetes Services s Security Center](azure-kubernetes-service-integration.md).

Přečtěte si další informace o [funkcích zabezpečení kontejnerů v Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Vylepšené prostředí za běhu

Funkce, operace a uživatelské rozhraní pro nástroje Azure Security Center za běhu, které zabezpečují vaše porty pro správu, jsou vylepšené následujícím způsobem: 

- **Pole odůvodnění** – při žádosti o přístup k virtuálnímu počítači (VM) na stránce Azure Portal za běhu je k dispozici nové volitelné pole, ve kterém můžete zadat odůvodnění žádosti. Informace zadané do tohoto pole lze sledovat v protokolu aktivit. 
- **Automatické čištění redundantních pravidel JIT (just-in-time)** – při každé aktualizaci zásad JIT se automaticky spustí nástroj pro vyčištění, který kontroluje platnost celého RuleSet. Nástroj vyhledá neshody mezi pravidly v zásadách a pravidly v NSG. Pokud nástroj pro vyčištění najde neshodu, určí příčinu a v případě, že je to bezpečné, odstraní předdefinovaná pravidla, která už nepotřebujete. Čisticí nikdy neodstraní pravidla, která jste vytvořili. 

Přečtěte si další informace o [funkci přístupu JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Dvě bezpečnostní doporučení pro webové aplikace, které jsou zastaralé

Nepoužívá se dvě doporučení zabezpečení související s webovými aplikacemi: 

- Pravidla pro webové aplikace v IaaS skupin zabezpečení sítě by měla být zpřísněná.
    (Související zásady: pravidla skupin zabezpečení sítě pro webové aplikace v IaaS by měla být Posílená.)

- Přístup k App Services by měl být omezený.
    (Související zásady: přístup k App Services by měl být omezený [Preview])

Tato doporučení se už nebudou zobrazovat v seznamu Security Centerch doporučení. Související zásady již nebudou zahrnuty v iniciativě s názvem "Security Center výchozí".

Přečtěte si další informace o [doporučeních zabezpečení](recommendations-reference.md).



## <a name="february-2020"></a>Únor 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Detekce útoků typu soubor pro Linux (Preview)

Díky tomu, že útočníci zvyšují stealthier metody, aby se vyhnuli detekci, Azure Security Center kromě systému Windows rozšířit rozpoznávání útoků bez souborů pro Linux. Útoky bez souborů zneužívají chyby softwaru, vkládají škodlivé datové části do neškodných systémových procesů a skrývají se v paměti. Tyto techniky:

- Minimalizace nebo odstranění trasování malwaru na disku
- výrazné omezení pravděpodobnosti detekce pomocí řešení pro kontrolu malwaru na disku

Pokud chcete tuto hrozbu vyhodnotit, Azure Security Center pro Windows v říjnu 2018 vydávat detekci útoku bez souborů a teď má rozšířenou detekci útoku bez souborů i na Linux. 


## <a name="january-2020"></a>Leden 2020

### <a name="enhanced-secure-score-preview"></a>Rozšířené zabezpečené skóre (Preview)

Vylepšená verze funkce zabezpečeného skóre Azure Security Center je teď dostupná ve verzi Preview. V této verzi se více doporučení seskupují do ovládacích prvků zabezpečení, které lépe odráží zranitelné plochy pro útoky (například omezují přístup k portům pro správu).

Seznamte se se změnami se zabezpečeným skóre ve fázi Preview a zjistěte další nápravy, které vám pomůžou lépe zabezpečit vaše prostředí.

Přečtěte si další informace o [rozšířeném zabezpečení skóre (Preview) v Azure Security Center](secure-score-security-controls.md).