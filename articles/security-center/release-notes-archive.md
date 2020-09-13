---
title: Archivace novinek v Azure Security Center
description: Popis toho, co je nového a co se změnilo ve Azure Security Center před šesti měsíci a dřívějšími verzemi.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: 2012f590aa833bea1b49955958095917a6479665
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438564"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archivujte, co je nového v Azure Security Center?

Stránka [co je nového ve službě Azure Security Center?](release-notes.md) na stránce poznámky k verzi obsahuje aktualizace za posledních šest měsíců, zatímco Tato stránka obsahuje starší položky.

Tato stránka poskytuje informace o:

- Nové funkce
- Opravy chyb
- Zastaralé funkce


## <a name="march-2020"></a>Březen 2020

Aktualizace v březnu zahrnují:

- [Automatizace pracovních postupů je teď všeobecně dostupná.](#workflow-automation-is-now-generally-available)
- [Integrace Azure Security Center s centrem pro správu Windows](#integration-of-azure-security-center-with-windows-admin-center)
- [Ochrana pro službu Azure Kubernetes](#protection-for-azure-kubernetes-service)
- [Vylepšené prostředí za běhu](#improved-just-in-time-experience)
- [Dvě bezpečnostní doporučení pro webové aplikace, které jsou zastaralé](#two-security-recommendations-for-web-applications-deprecated)


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

Přečtěte si další informace o [rozšířeném zabezpečeném skóre (Preview)](secure-score-security-controls.md).



## <a name="november-2019"></a>Listopad 2019

Aktualizace v listopadu zahrnují:
 - [Ochrana před hrozbami pro Azure Key Vault v oblasti Severní Amerika (Preview)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Ochrana před hrozbami pro Azure Storage zahrnuje šetření reputace malwaru](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automatizace pracovního postupu pomocí Logic Apps (Preview)](#workflow-automation-with-logic-apps-preview)
 - [Rychlá oprava pro hromadné prostředky je všeobecně dostupná](#quick-fix-for-bulk-resources-generally-available)
 - [Skenovat image kontejneru pro ohrožení zabezpečení (Preview)](#scan-container-images-for-vulnerabilities-preview)
 - [Další standardy dodržování předpisů v legislativě (Preview)](#additional-regulatory-compliance-standards-preview)
 - [Ochrana před hrozbami pro službu Azure Kubernetes (Preview)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Posouzení ohrožení zabezpečení pro virtuální počítače (Preview)](#virtual-machine-vulnerability-assessment-preview)
 - [Pokročilé zabezpečení dat pro SQL servery na Azure Virtual Machines (Preview)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Podpora vlastních zásad (Preview)](#support-for-custom-policies-preview)
 - [Rozšíření pokrytí Azure Security Center s platformou pro komunitu a partnery](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Pokročilá integrace s exportem doporučení a výstrah (Preview)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Připojování Prem serverů k Security Center z centra pro správu Windows (Preview)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Ochrana před hrozbami pro Azure Key Vault v oblasti Severní Amerika (Preview)

Azure Key Vault je zásadní služba pro ochranu dat a zlepšení výkonu cloudových aplikací tím, že nabízí možnost centrálně spravovat klíče, tajné klíče, kryptografické klíče a zásady v cloudu. Vzhledem k tomu, že Azure Key Vault ukládá citlivá a důležitá firemní data, vyžaduje maximální zabezpečení pro trezory klíčů a data, která jsou v nich uložená.

Podpora ochrany před internetovými útoky pro Azure Key Vault poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k trezorům klíčů nebo jejich zneužití. Azure Security Center Tato nová vrstva ochrany umožňuje zákazníkům řešit hrozby proti trezorům klíčů, aniž by se museli odborníkem na zabezpečení nebo spravovat systémy monitorování zabezpečení. Tato funkce je ve verzi Public Preview ve Severní Amerika oblastech.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Ochrana před hrozbami pro Azure Storage zahrnuje šetření reputace malwaru

Ochrana před hrozbami pro Azure Storage nabízí nové detekce, které využívají Microsoft Threat Intelligence ke zjišťování nahraných malwaru pro Azure Storage pomocí analýzy reputace hodnoty hash a podezřelého přístupu z aktivního uzlu pro ukončení práce (proxy server anonymizace). Zjištěný malware můžete nyní zobrazit v rámci účtů úložiště pomocí Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automatizace pracovního postupu pomocí Logic Apps (Preview)

Organizace s centrálně spravovaným zabezpečením a IT/operacemi implementují interní pracovní postupy pro řízení požadavků v rámci organizace, pokud jsou zjištěny nesrovnalosti ve svých prostředích. V mnoha případech jsou tyto pracovní postupy možné opakovat a automatizace může výrazně zjednodušit procesy v rámci organizace.

Dnes zavádíme novou funkci v Security Center, která zákazníkům umožňuje vytvářet konfigurace automatizace využívající Azure Logic Apps a k vytváření zásad, které je automaticky spouštějí na základě konkrétních zjištění ASC, jako jsou doporučení nebo výstrahy. Aplikaci logiky Azure je možné nakonfigurovat tak, aby prodala jakoukoli vlastní akci podporovanou obrovské komunitou konektorů aplikací logiky, nebo použijte jednu ze šablon, které poskytuje Security Center, jako je odeslání e-mailu nebo otevření lístku™ ServiceNow.

Další informace o funkcích automatických a ručních Security Center pro spouštění pracovních postupů najdete v tématu [automatizace pracovních postupů](workflow-automation.md).

Další informace o vytváření Logic Apps najdete v tématu [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Rychlá oprava pro hromadné prostředky je všeobecně dostupná

Díky mnoha úlohám, které uživatel předává jako součást zabezpečeného skóre, může být schopnost efektivně opravovat problémy v rámci velkého loďstva náročná.

Aby se zjednodušila náprava chybných konfigurací zabezpečení a bylo možné rychle opravit doporučení pro velké objemy prostředků a zlepšit vaše zabezpečené skóre, použijte rychlou opravu nápravy.

Tato operace vám umožní vybrat prostředky, u kterých chcete uplatnit nápravu, a spustit nápravnou akci, která nakonfiguruje nastavení vaším jménem.

Rychlá oprava je všeobecně dostupná pro zákazníky v současnosti jako součást stránky doporučení Security Center.

Podívejte se, která doporučení mají v referenční příručce povolená Rychlá oprava [pro doporučení zabezpečení](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Skenovat image kontejneru pro ohrožení zabezpečení (Preview)

Azure Security Center teď můžou naskenovat image kontejneru v Azure Container Registry ohrožení zabezpečení.

Skenování imagí funguje tak, že analyzuje soubor image kontejneru a pak zkontroluje, jestli existují známá ohrožení zabezpečení (využívá Qualys).

Při doručování nových imagí kontejneru do Azure Container Registry se tato kontrola automaticky aktivuje. Zjištěná ohrožení zabezpečení se budou považovat za Security Center doporučení a zahrnutá v zabezpečeném skóre Azure spolu s informacemi o tom, jak je opravit tak, aby se snížila plocha pro útok, které povolují.


### <a name="additional-regulatory-compliance-standards-preview"></a>Další standardy dodržování předpisů v legislativě (Preview)

Řídicí panel dodržování předpisů poskytuje přehledy o stav dodržování předpisů na základě posouzení Security Center. Řídicí panel ukazuje, jak vaše prostředí splňuje ovládací prvky a požadavky určené konkrétními regulativními standardy a oborovými srovnávacími testy, a poskytuje doporučená doporučení pro řešení těchto požadavků.

Řídicí panel dodržování předpisů má doposud podporované čtyři integrované standardy: Azure CIS 1.1.0, PCI-DSS, ISO 27001 a SOC-TSP. Nyní oznamujeme vydání verze Public Preview dalších podporovaných standardů: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Kanada federální PBMM a Velká Británie s NHSem UK. Vydáváme také aktualizovanou verzi Azure CIS 1.1.0, která se pokryje s dalšími ovládacími prvky Standard a vylepšení rozšiřitelnosti.

[Přečtěte si další informace o přizpůsobení sady standardů na řídicím panelu dodržování legislativních předpisů](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Ochrana před hrozbami pro službu Azure Kubernetes (Preview)

Kubernetes se rychle stane novým standardem pro nasazení a správu softwaru v cloudu. Několik lidí má rozsáhlou zkušenost s Kubernetes a mnoho se zaměřuje jenom na Obecné řízení a správu a přehlédnout bezpečnostní aspekty. Kubernetes prostředí je třeba pečlivě nakonfigurovat tak, aby bylo zabezpečené, a tím zajistit, že se pro útočníky neotevřou žádné dveře zaměřené na přístup k kontejneru. Security Center rozšiřuje svou podporu v prostoru kontejneru na jednu z nejrychlejších rostoucích služeb v Azure – Azure Kubernetes Service (AKS).

Nové funkce ve verzi Public Preview zahrnují:

- **Zjišťování & viditelnost** – nepřetržité zjišťování spravovaných instancí AKS v rámci registrovaných předplatných Security Center.
- **Doporučení pro bezpečné skóre** – akce, které umožňují zákazníkům dodržovat osvědčené postupy zabezpečení v AKS jako součást zabezpečeného skóre zákazníka, jako je například "Access Control na základě rolí by se měly používat k omezení přístupu ke clusteru služby Kubernetes".
- **Detekce hrozeb** – zjistilo se hostování a analýzy založené na clusterech, jako je například "zjištěná privilegovaný kontejner".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Posouzení ohrožení zabezpečení pro virtuální počítače (Preview)

Aplikace, které jsou nainstalované na virtuálních počítačích, by mohly často obsahovat chyby zabezpečení, které by mohly vést k porušení virtuálního počítače. Oznamujeme, že úroveň Standard Security Center zahrnuje integrované posouzení ohrožení zabezpečení pro virtuální počítače bez dalších poplatků. Posouzení ohrožení zabezpečení, které využívá Qualys ve verzi Public Preview, vám umožní průběžně kontrolovat všechny nainstalované aplikace na virtuálním počítači a vyhledat ohrožené aplikace a prezentovat závěry v prostředí Security Centerového portálu. Security Center se postará o všechny operace nasazení, aby uživatel nemusel žádné další práce vyžadovat. Až budeme pokračovat, plánujeme vám poskytnout možnosti posouzení ohrožení zabezpečení pro podporu jedinečných obchodních potřeb zákazníků.

[Přečtěte si další informace o posouzení ohrožení zabezpečení pro Azure Virtual Machines](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Pokročilé zabezpečení dat pro SQL servery na Azure Virtual Machines (Preview)

Podpora Azure Security Center ochrany před hrozbami a posouzení ohrožení zabezpečení pro SQL databáze běžící na virtuálních počítačích s IaaS je teď ve verzi Preview.

[Posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) je snadno konfigurovatelná služba, která může zjišťovat, sledovat a pomáhat opravovat potenciální ohrožení zabezpečení databáze. Poskytuje přehled o stav zabezpečení jako součást služby Azure Secure skore a zahrnuje kroky pro řešení problémů se zabezpečením a vylepšení fortifications databáze.

[Rozšířená ochrana před internetovými útoky](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k SQL serveru nebo jeho zneužití. Nepřetržitě monitoruje vaši databázi pro podezřelé aktivity a poskytuje výstrahy zabezpečení zaměřené na akce na vzorech přístupu k databázi neobvyklé. Tyto výstrahy obsahují podrobnosti o podezřelé aktivitě a doporučené akce pro šetření a zmírnění hrozby.


### <a name="support-for-custom-policies-preview"></a>Podpora vlastních zásad (Preview)

Azure Security Center teď podporuje vlastní zásady (ve verzi Preview).

Naši zákazníci chtějí rozšíření jejich aktuálního hodnocení zabezpečení v Security Center rozšířením o vlastní posouzení zabezpečení na základě zásad, které vytvářejí v Azure Policy. Díky podpoře vlastních zásad je teď to možné.

Tyto nové zásady budou součástí možností Security Centerch doporučení, bezpečného skóre a řídicího panelu standardů dodržování předpisů regulativních předpisů. Díky podpoře vlastních zásad teď můžete vytvořit vlastní iniciativu v Azure Policy a pak ji přidat jako zásadu v Security Center a vizualizovat ji jako doporučení.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Rozšíření pokrytí Azure Security Center s platformou pro komunitu a partnery

Použijte Security Center pro příjem doporučení nejen od Microsoftu, ale také z existujících řešení od partnerů, jako je například Check Point, Tenable a CyberArk s mnoha dalšími integracemi, které přicházejí.  Jednoduchý tok zprovoznění Security Center může propojit vaše stávající řešení s Security Center, což vám umožní zobrazit doporučení zabezpečení stav na jednom místě, spouštět sjednocené sestavy a využívat všechny funkce Security Center proti integrovaným i partnerským doporučením. Můžete také exportovat Security Center doporučení do partnerských produktů.

[Přečtěte si další informace o přidružení inteligentního zabezpečení Microsoftu](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Pokročilá integrace s exportem doporučení a výstrah (Preview)

Pokud chcete povolit scénáře na podnikové úrovni nad Security Center, je teď možné spotřebovávat Security Center výstrahy a doporučení na dalších místech s výjimkou Azure Portal nebo rozhraní API. Tyto aplikace je možné přímo exportovat do centra událostí a do Log Analytics pracovních prostorů. Tady je několik pracovních postupů, které můžete vytvořit kolem těchto nových funkcí:

- Pomocí exportu do Log Analytics pracovního prostoru můžete pomocí Power BI vytvářet vlastní řídicí panely.
- Při exportu do centra událostí budete moct exportovat Security Center výstrahy a doporučení pro systémů Siem třetí strany do řešení třetí strany v reálném čase nebo Azure Průzkumník dat.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Připojování Prem serverů k Security Center z centra pro správu Windows (Preview)

Centrum pro správu Windows je portál pro správu serverů Windows, kteří nejsou nasazeni v Azure, nabízejí několik funkcí správy Azure, jako jsou zálohování a aktualizace systému. Nedávno jsme přidali možnost připojit tyto servery mimo Azure, aby byly chráněny pomocí ASC přímo z prostředí centra pro správu systému Windows.

Díky této nové službě se uživatelé budou moci připojit k Azure Security Center serveru WAC a povolit zobrazování výstrah a doporučení zabezpečení přímo v prostředí centra pro správu systému Windows.


## <a name="september-2019"></a>Září 2019

Aktualizace v září zahrnují:

 - [Správa pravidel pomocí adaptivních vylepšení řízení aplikací](#managing-rules-with-adaptive-application-controls-improvements)
 - [Řízení doporučení zabezpečení kontejneru pomocí Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Správa pravidel pomocí adaptivních vylepšení řízení aplikací

Prostředí pro správu pravidel pro virtuální počítače, které používá adaptivní řízení aplikací, se zlepšilo. Adaptivní řízení aplikací Azure Security Center vám pomůžou řídit, které aplikace se můžou na virtuálních počítačích spouštět. Kromě obecného vylepšení správy pravidel vám nová výhoda umožní řídit, které typy souborů budou chráněné při přidávání nového pravidla.

[Přečtěte si další informace o adaptivních řízeních aplikací](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Řízení doporučení zabezpečení kontejneru pomocí Azure Policy

Doporučení Azure Security Center k nápravě ohrožení zabezpečení v kontejneru zabezpečení se teď dá povolit nebo zakázat prostřednictvím Azure Policy.

Pokud chcete zobrazit povolené zásady zabezpečení, z Security Center otevřete stránku zásady zabezpečení.


## <a name="august-2019"></a>Srpen 2019

Aktualizace v srpnu zahrnují:

 - [Přístup k virtuálnímu počítači JIT (just-in-time) pro Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Zvýšení zabezpečení stav (Preview) jedním kliknutím na nápravu](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Správa napříč tenanty](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Přístup k virtuálnímu počítači JIT (just-in-time) pro Azure Firewall 

Přístup k virtuálnímu počítači JIT (just-in-time) pro Azure Firewall je teď všeobecně dostupný. Použijte ho k zabezpečení vašich Azure Firewall chráněných prostředí kromě vašich prostředí chráněných NSG.

Přístup k virtuálnímu počítači JIT snižuje riziko útoků sítě tím, že poskytuje řízený přístup k virtuálním počítačům jenom v případě potřeby, a to pomocí pravidel NSG a Azure Firewall.

Když pro své virtuální počítače povolíte JIT, vytvoříte zásadu, která určuje, jak dlouho mají porty zůstat otevřené, a schválené IP adresy, ze kterých se k těmto portům dostanete. Tato zásada vám pomůže udržet si kontrolu nad tím, co můžou uživatelé dělat při žádosti o přístup.

Požadavky se zaznamenávají do protokolu aktivit Azure, takže můžete snadno sledovat a auditovat přístup. Stránka za běhu také pomáhá rychle identifikovat stávající virtuální počítače s povoleným JIT a virtuálními počítači, kde se doporučuje JIT.

[Přečtěte si další informace o Azure firewall](https://docs.microsoft.com/azure/firewall/overview).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Zvýšení zabezpečení stav (Preview) jedním kliknutím na nápravu

Bezpečné skóre je nástroj, který vám pomůže vyhodnotit stav zabezpečení zatížení. Kontroluje vaše doporučení na zabezpečení a určuje jejich prioritu za vás, takže víte, jaká doporučení je třeba provést jako první. To vám pomůže najít nejzávažnější slabá místa zabezpečení k určení priorit šetření.

Aby bylo možné zjednodušit nápravu neúspěšných konfigurací zabezpečení a pomáhat vám rychle vylepšit vaše zabezpečené skóre, Přidali jsme novou funkci, která vám umožní napravit doporučení na hromadných prostředcích jediným kliknutím.

Tato operace vám umožní vybrat prostředky, u kterých chcete uplatnit nápravu, a spustit nápravnou akci, která nakonfiguruje nastavení vaším jménem.

Podívejte se, která doporučení mají v referenční příručce povolená Rychlá oprava [pro doporučení zabezpečení](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Správa napříč tenanty

Security Center teď podporuje scénáře správy mezi klienty jako součást služby Azure Lighthouse. To vám umožní získat přehled a spravovat stav zabezpečení pro více tenantů v Security Center. 

[Přečtěte si další informace o prostředích pro správu mezi klienty](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Červenec 2019

### <a name="updates-to-network-recommendations"></a>Aktualizace doporučení k síti

Azure Security Center (ASC) spustili nová síťová doporučení a vylepšila některé stávající. Díky tomu Security Center zajišťuje ještě větší ochranu sítě pro vaše prostředky. 

[Přečtěte si další informace o doporučeních sítě](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>Červen 2019

### <a name="adaptive-network-hardening---generally-available"></a>Adaptivní posílení sítě – všeobecně dostupné

Jednou z největších ploch pro útoky na úlohy spuštěné ve veřejném cloudu jsou připojení k veřejnému Internetu a z něj. Naši zákazníci mají obtížné zjistit, která pravidla skupiny zabezpečení sítě (NSG) by měla být zavedena, aby bylo zajištěno, že úlohy Azure budou dostupné pouze pro požadované zdrojové rozsahy. Díky této funkci se Security Center dozvíte o síťových přenosech a způsobech připojení úloh Azure a poskytuje doporučení pro pravidla NSG pro virtuální počítače v Internetu. Díky tomu může náš zákazník lépe konfigurovat zásady přístupu k síti a omezit jejich vystavení útokům. 

[Přečtěte si další informace o adaptivním posílení zabezpečení sítě](security-center-adaptive-network-hardening.md).
