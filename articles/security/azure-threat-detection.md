---
title: Rozšířená detekce hrozeb | Dokumentace Microsoftu
description: Přečtěte si o Azure AD Identity Protection a její možnosti.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 32e705c5c338d9bd7c16514b2dc2ab081a9caf6a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115551"
---
# <a name="azure-advanced-threat-detection"></a>Rozšířená detekce hrozeb Azure

Azure nabízí integrované funkce detekci pokročilých hrozeb prostřednictvím služeb, jako je Azure Active Directory (Azure AD), Azure Log Analytics a Azure Security Center. Tato skupina zabezpečení služeb a možností poskytuje rychlý a jednoduchý způsob, jak pochopit, co se děje ve vašich nasazeních Azure.

Azure nabízí širokou škálu možnosti konfigurace a vlastního nastavení zabezpečení pro splnění požadavků nasazení aplikací. Tento článek popisuje, jak budou odpovídat vašim požadavkům.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) je [Azure Active Directory Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) edition funkce, která poskytuje přehled o rizikových událostech a potenciálních chybách zabezpečení, které můžou ovlivnit identity vaší organizace. Služba identity Protection používá existující detekce anomálií funkcím služby Azure AD, které jsou k dispozici prostřednictvím [Azure AD neobvyklé aktivity sestavy](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports)a zavádí nové typy rizikových událostí, které dokáží detekovat anomálie v reálném čase.

![Azure AD Identity Protection diagramu](./media/azure-threat-detection/azure-threat-detection-fig1.png)

Identity Protection používá algoritmů adaptivní strojového učení a heuristik ke zjištění anomálií a rizikových událostí, které může znamenat, že došlo k napadení o identitu. Na základě těchto dat Identity Protection generuje sestavy a upozornění tak, aby mohli tyto rizikové události prozkoumat a odpovídajících nápravných nebo zmírňující opatření.

Azure Active Directory Identity Protection je větší než monitorování a vytváření sestav nástroje. Podle rizikové události, Identity Protection vypočítá úroveň rizika uživatele pro jednotlivé uživatele tak, abyste mohli nakonfigurovat zásady automaticky chránit identity vaší organizace.

Tyto zásady na základě rizik, kromě jiných [řízení podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) , které jsou k dispozici v Azure Active Directory a [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), můžete automaticky blokovat nebo nabízejí adaptivní nápravné akce, která Zahrnout resetování hesel a vynucování služby Multi-Factor authentication.

### <a name="identity-protection-capabilities"></a>Možnosti ochrany identit

Azure Active Directory Identity Protection je větší než monitorování a vytváření sestav nástroje. K ochraně identity ve vaší organizaci, můžete nakonfigurovat zásady, které automaticky reagují na zjištěné problémy, když se dosáhne úroveň zadané rizika. Tyto zásady, kromě jiných ovládacích prvků podmíněný přístup poskytuje Azure Active Directory a EMS, můžete buď automaticky blokovat nebo zahájit adaptivní nápravných akcí, které resetuje heslo včetně a vynucování služby Multi-Factor authentication.

Příklady některých způsobu, jakým Azure Identity Protection může pomoci zabezpečit vaše účty a identity, zahrnují:

[Zjišťování rizikových událostech a rizikových účty](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Zjištění šest typy rizikových událostí pomocí strojového učení a heuristiky pravidla.
-   Vypočítá úrovně rizika uživatele.
-   Zadejte vlastní doporučení k vylepšení celkové postavení zabezpečení zvýrazněním ohrožení zabezpečení.

[Zkoumání rizikových událostí](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Odesílání oznámení pro rizikové události.
-   Prošetřete rizikové události pomocí relevantní a kontextové informace.
-   Zadejte základní pracovní postupy ke sledování šetření.
-   Poskytování snadného přístupu k nápravné akce, jako je resetování hesel.

[Zásady na základě rizik, podmíněný přístup](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
-   Zmírnění rizikových přihlášení blokování přihlášení nebo vyžadování výzvy ověřování službou Multi-Factor Authentication.
-   Blokovat nebo zabezpečení rizikových uživatelské účty.
-   Vyžadovat od uživatelů registraci k vícefaktorovému ověřování.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

S [Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), můžete spravovat, řídit a sledovat přístup v rámci vaší organizace. Tato funkce zahrnuje přístup k prostředkům ve službě Azure AD a dalších online službách Microsoftu, jako je Office 365 nebo Microsoft Intune.

![Azure AD Privileged Identity Management diagram](./media/azure-threat-detection/azure-threat-detection-fig2.png)

PIM umožňuje:

-   Získání výstrah a sestav o Správci služby Azure AD a just-in-time (JIT) pro správu přístupu k online službám Microsoftu, jako je Office 365 a Intune.

-   Získejte sestavy obsahující historii přístupů správce a změny v přiřazení správce.

-   Dostávejte upozornění týkající se přístupu k privilegovanou roli.

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) je Microsoftu založené na cloudu IT řešení pro správu, která pomáhá spravovat a chránit místní a cloudovou infrastrukturu. Protože Log Analytics je implementovaná jako cloudová služba, můžete mít ji zprovoznit rychle s minimálními investicemi do infrastrukturních služeb. Nové funkce zabezpečení jsou poskytována automaticky, uložení průběžnou údržbu a upgrade náklady.

Kromě poskytování přínosných služeb sama o sobě, Log Analytics může integrovat s komponentami nástroje System Center, jako [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), rozšířit vaše stávající zabezpečení investice do správy do cloudu. System Center a Log Analytics můžete společně poskytují správu úplné hybridní prostředí.

### <a name="holistic-security-and-compliance-posture"></a>Komplexní zabezpečení a dodržování předpisů, aby detekoval

[Řídicí panel Log Analytics zabezpečení a Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) poskytuje ucelený přehled o vaší organizaci stavu zabezpečení IT pomocí předdefinovaných vyhledávacích dotazů na důležité problémy, které vyžadují vaši pozornost. Řídicí panel zabezpečení a Audit je domovskou obrazovkou pro všechno, co související se zabezpečením v Log Analytics. Poskytuje celkový přehled o stavu zabezpečení vašich počítačů. Můžete také zobrazit všechny události z posledních 24 hodin, 7 dní, nebo všechny vlastní časový rámec.

Log Analytics, vám pomohou rychle a snadno pochopit celkové postavení zabezpečení všech prostředí, všechny v rámci IT operace, včetně softwaru o aktualizaci, posouzení antimalwaru a standardní hodnoty konfigurace. Data protokolu zabezpečení jsou snadno přístupné pro zjednodušení procesu audit zabezpečení a dodržování předpisů.

![Řídicí panel Log Analytics zabezpečení a Audit](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

Řídicí panel Log Analytics zabezpečení a Audit je uspořádaný do čtyř hlavních kategorií:

-   **Domény zabezpečení**: Umožňuje dále prozkoumat záznamy zabezpečení v průběhu času; posouzení malwaru přístup; posouzení aktualizací; zobrazení zabezpečení sítě, identitu a přístup k informacím; Zobrazit počítače s událostmi zabezpečení; a rychlý přístup k řídicím panelu Azure Security Center.

-   **Významné problémy**: Umožňuje rychle identifikovat celou řadu aktivních problémů a závažnost problémů.

-   **Zjištění (Preview)**: Umožňuje identifikovat vzory útoků zobrazením výstrahy zabezpečení, jak se objeví na vaše prostředky.

-   **Hrozeb**: Umožňuje identifikovat vzory útoků tím, že zobrazuje celkový počet serverů s odchozími škodlivými přenosy, typu škodlivých hrozeb a mapu umístění IP adresy.

-   **Běžné dotazy na zabezpečení**: Obsahuje seznam nejběžnějších bezpečnostních dotazů, které vám umožní monitorovat vaše prostředí. Při výběru jakéhokoli dotazu v podokně hledání otevře a zobrazí výsledky pro daný dotaz.

### <a name="insight-and-analytics"></a>Službu Insight and analytics
V centru [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) je úložiště, které je hostovaný v Azure.

![Diagram službu Insight and analytics](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Shromažďování dat do úložiště z připojených zdrojů tak, že konfigurace zdroje dat a přidání řešení do vašeho předplatného.

![Řídicí panel Log Analytics ](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Zdroje dat a řešení vytvořit samostatný záznam typy s vlastní sadu vlastností, ale stále je můžete analyzovat společně v dotazech do úložiště. Můžete stejné nástroje a metody pro práci s širokou škálu dat, která se shromažďují z různých zdrojů.


Většina vaší práce s využitím Log Analytics je na webu Azure portal, který běží v jakémkoli prohlížeči a poskytuje přístup k nastavení konfigurace a několika nástrojům pro analýzy a reagovat na shromážděná data. Z portálu můžete použít:
* [Prohledávání protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) kde je možné vytvářet dotazy pro analýzy shromážděných dat.
* [Řídicí panely](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards), které můžete přizpůsobit pomocí grafických zobrazení nejdůležitějších hledání.
* [Řešení](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), která poskytují další funkce a analytické nástroje.

![Nástroje pro analýzu](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Řešení do služby Log Analytics přidávají funkčnost. Jsou primárně běží v cloudu a poskytují analýzu dat, která se shromažďují v úložišti Log Analytics. Řešení může také definovat nové typy záznamů které se mají shromáždit, které se dají analyzovat pomocí prohledávání protokolu nebo prostřednictvím dalšího uživatelského rozhraní, které toto řešení poskytuje v řídicím panelu Log Analytics.

Řídicí panel zabezpečení a Audit je příkladem tyto druhy řešení.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatizace a řízení: Upozornění na drifts konfigurace zabezpečení

Azure Automation umožňuje automatizovat procesy správy pomocí sady runbook, které jsou založené na prostředí PowerShell a spouštět v cloudu. Runbooky se mohou provádět také na serveru ve vašem místním datovém centru a mohou spravovat místní prostředky. Azure Automation poskytuje správu konfigurace pomocí prostředí PowerShell Desired State Configuration (DSC).

![Diagram služby Azure Automation](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Můžete vytvořit a spravovat prostředky DSC, které jsou hostované v Azure a použít je u cloudových a místních systémech. Tímto způsobem, můžete definovat a automaticky vynutily jejich konfiguraci nebo získat sestavy o odchylek k zajištění zabezpečení konfigurace zůstanou v rámci zásad.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center k ochraně vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure. V rámci služby, můžete definovat zásady pro oba svá předplatná Azure a [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) pro větší členitosti.

![Azure Security Center diagramu](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Výzkumníci v oblasti zabezpečení ze společnosti Microsoft neustále vyhledávají nové hrozby. Mají přístup k rozsáhlé sadě telemetrických údajů získávaných díky globálního prezenci společnosti Microsoft v cloudových i místních prostředích. Tento rozsáhlý a různorodý soubor datových sad umožňuje společnosti Microsoft objevovat nová schémata a trendy útoků v rámci jejích místních produktů pro zákazníky a podniky a rovněž i v rámci online služeb.

Díky tomu se Security Center můžete rychle aktualizovat své detekční algoritmy, jak útočníci provádějí nové a stále sofistikovanější kousky. Tento přístup pomáhá udržet krok s prostředím hrozeb rychlé tempo.

![Detekce hrozeb služby Security Center](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Detekce hrozeb ve službě Security Center funguje tak, že se automaticky shromažďují informace o zabezpečení ze všech prostředků Azure, ze sítě a připojených partnerských řešení. Služba tyto informace analyzuje, přitom koreluje data z více zdrojů, a identifikovat hrozby.

U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit.

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Prorazit ve velkých objemů dat a [strojového učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologie se používají k vyhodnocování událostí v rámci prostředků cloudové infrastruktury. Pokročilé analýzy můžete detekovat hrozby, kterých by bylo možné zjistit pomocí manuálních metod a předvídání vývoje útoků. Tyto typy analytics zabezpečení jsou popsané v následujících částech.

### <a name="threat-intelligence"></a>Analýza hrozeb

Microsoft má přístup k obrovské množství globální analýzy hrozeb.

Telemetrie toků z víc zdrojů, jako je například Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) a Microsoft Security Response Center (MSRC).

![Zjištění Threat intelligence](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Výzkumní pracovníci také přijímat informace analýzy hrozeb, jež jsou sdílena mezi poskytovatelů hlavní cloudových služeb a přihlášení k odběru kanálů analýzy hrozeb od třetích stran. Služba Azure Security Center vás pomocí těchto informací může upozornit na hrozby známých útočníků. Možné příklady:

-   **Sílu machine learningu**: Azure Security Center má přístup k obrovské množství dat o aktivitě cloudové sítě, který slouží ke zjištění hrozeb vaše nasazení Azure.

-   **Detekce útoků hrubou silou**: Machine learning slouží k vytvoření historických vzor vzdáleného přístupu pokusů o přihlášení, což umožňuje detekovat útoky hrubou silou na porty Secure Shell (SSH), protokolu RDP (Remote Desktop) a SQL.

-   **Odchozí před útoky DDoS a botnet detekce**: Běžné cílem útoků, které cílí na cloudové prostředky je výpočetní výkon než tyto prostředky používat k nějakému útoku, jiné.

-   **Nové servery analýzy chování a virtuální počítače**: Jakmile dojde k ohrožení server nebo virtuální počítač, útočníci využívat celou řadu technik k nepozorovaně spustit škodlivý kód v daném systému při obcházení zjišťování, zajištění trvalosti a spravovatelný ovládací prvky zabezpečení.

-   **Detekce hrozeb Azure SQL Database**: Detekce hrozeb pro Azure SQL Database, která identifikuje anomálie v činnosti databáze, které označují a potenciálně nebezpečné pokusí o přístup k databázím nebo jejich zneužití.

### <a name="behavioral-analytics"></a>Behaviorální analýza

Behaviorální analýza je technika, která analyzuje a porovnává data se sadou známých schémat. Tato schémata však nepředstavují jednoduché příznaky. Určují se prostřednictvím komplexních algoritmů strojového učení, které se aplikují na rozsáhlé datové sady.

![Behaviorální analýza zjištění](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)

Tyto vzory se dají se určují také prostřednictvím pečlivé analýzy škodlivého chování pomocí provádí zkušení analytici. Azure Security Center může pomocí analýzy chování identifikovat ohrožené prostředky na základě analýzy protokolů virtuálního počítače, virtuální síťové zařízení protokoly, protokolů prostředků infrastruktury, výpisy stavu systému a dalších zdrojů.

Kromě toho se vzory korelují s dalšími signály hledají podpůrné důkazy rozšířených kampaně. Tato korelace pomáhá identifikovat události, které jsou konzistentní se zavedenými ukazateli ohrožení zabezpečení.

Možné příklady:
-   **Podezřelé spouštění procesů**: Útočníci používají řadu technik k spouštění škodlivého softwaru. Útočník může například malware pojmenovat stejné názvy jako legitimní systémové soubory, ale umístí tyto souboru do alternativních umístění, použijte název, který je podobný neškodnému souboru nebo zamaskuje skutečnou příponu. Modely Security Center zpracovávají chování a spuštění procesu monitorování ke zjištění právě takovéto mimořádné hodnoty.

-   **Skrytý malware a pokusy o zneužití**: Sofistikovaný malware může obejít tradiční antimalwarové produkty nikdy nezapisuje na disk nebo softwarové komponenty ukládané na disk šifruje. Takový malware lze ale zjistit pomocí analýzy paměti, protože malware musí v paměti zanechat stopy, pro funkci. Pokud dojde k chybě softwaru, ve výpisu stavu systému se zaznamená část paměti v době selhání. Díky analýze paměti ve výpisu, můžete Azure Security Center zjišťovat techniky, využívají zranitelností softwaru, přístup k důvěrným datům a nenápadně přetrvávají v napadeném počítači, aniž by to mělo dopad na výkon vaší počítač.

-   **Laterální přesuny a interní sondování**: Pokud chcete zachovat v ohrožení zabezpečení sítě a vyhledat a získejte cenné dat, nastavení se útočníci často pokouší přesunout z jednoho napadeného počítače ostatním uživatelům v rámci stejné sítě. Security Center monitoruje aktivity procesů a přihlašování k objevit pokusy útočníka základnu v rámci této sítě, jako je vzdálené spouštění příkazů, zjišťování sítě a výčtu účtů.

-   **Škodlivé skripty prostředí PowerShell**: PowerShell umožňuje útočníci v cílových virtuálních počítačích nepozorovaně spustit škodlivý kód pro různé účely. Služba Security Center kontroluje aktivitu prostředí PowerShell a hledá známky podezřelé aktivity.

-   **Odchozí útoky**: Útočníci často cílí na cloudové prostředky s cílem využít je k dalším útokům. Ohrožené virtuální počítače, může například použít pro spouštění útoků hrubou silou proti jiným virtuálním počítačům, odesílání nevyžádané pošty nebo skenování otevřených portů a dalších zařízení na Internetu. Služba Security Center pomocí strojového učení, které uplatňuje na síťový provoz, dokáže detekovat odchozí síťovou komunikaci vybočující z normy. Když se zjistí nevyžádané pošty, Security Center také koreluje neobvyklý e-mailový provoz pomocí informací ze služby Office 365 k určení, zda je e-mailu. pravděpodobně neslouží pro nekalé účely nebo výsledkem legitimní e-mailové kampaně.

### <a name="anomaly-detection"></a>Detekce anomálií

Služba Azure Security Center také identifikuje hrozby pomocí detekce anomálií. Oproti behaviorální analýze (která závisí na známých schématech odvozovaných z velkých datových sad), je detekce anomálií více „personalizovaná“ a zaměřuje se na standardní hodnoty specifické pro vaše nasazení. Machine learning se použije pro určí běžné úrovně aktivity pro vaše nasazení, a poté se vygenerují pravidla definující neobvyklé hodnoty, které by mohly představovat událost zabezpečení. Zde naleznete příklad:

-   **Příchozí útoky hrubou silou RDP/SSH**: Nasazení může být vytížené virtuální počítače s mnoha přihlášení každý den a dalších virtuálních počítačů, které mají několik, pokud existuje, přihlašovací údaje. Azure Security Center můžete určit standardní hodnoty pro přihlašovací aktivitu pro tyto virtuální počítače a pomocí strojového učení definovat kolem běžné přihlašovací aktivity. Pokud je rozdíl oproti standardní hodnoty definované pro vlastnosti související s přihlášením, může být vygenerována výstraha. A strojové učení tu zase určuje, co je významné.

### <a name="continuous-threat-intelligence-monitoring"></a>Průběžné monitorování analýzy hrozeb

Azure Security Center funguje s zabezpečení výzkumu a data science týmy po celém světě, který neustále monitorují změny ve světě hrozeb. To zahrnuje následující iniciativy:

-   **Monitorování analýzy hrozeb**: Analýza hrozeb zahrnuje mechanismy, ukazatele, důsledky a praktické rady týkající se stávajících nebo nově vznikajících hrozeb. Tyto informace se sdílí v bezpečnostní komunitě a společnost Microsoft neustále monitoruje kanálů analýzy hrozeb z interních i externích zdrojů.

-   **Sdílení signálu**: Přehledy na základě zabezpečení týmy napříč širokého portfolia Microsoft cloud a místní služby, serverů a klientských koncových zařízení se sdílí a analyzují.

-   **Specialisty Microsoftu a zabezpečení**: Průběžné zapojování týmů v rámci Microsoftu, které pracují ve specializovaných oblastech zabezpečení, jako je například forenzní analýzy a web detekce útoku.

-   **Optimalizace detekce**: Datových sadách reálných zákazníků se spouští algoritmy a výzkumníci pracují se zákazníky na ověřování výsledků. Pravdivě a falešně pozitivní výsledky pak slouží ke zlepšování algoritmů strojového učení.

Toto celkové úsilí přineslo nové a vylepšené způsoby detekce, které můžete využívat výhod okamžitě. Neexistuje žádná akce vám umožní.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Funkce detekce pokročilé hrozby: Další služby Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuální počítače: Antimalware od Microsoftu

[Microsoft antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro Azure je řešení jednoho agenta pro aplikace a prostředí tenanta, navržený ke spouštění na pozadí bez zásahu člověka. Je možné nasadit ochranu na základě potřeb vaší aplikace úlohy s využitím buď základní zabezpečení výchozím nebo Rozšířené vlastní konfigurace, včetně antimalwarový monitorování. Azure antimalware je možnost zabezpečení pro virtuální počítače Azure, který se automaticky nainstaluje na všech virtuálních počítačů Azure PaaS.

#### <a name="microsoft-antimalware-core-features"></a>Microsoft antimalware základní funkce

Toto jsou funkce služby Azure, nasazení a povolit Microsoft antimalware pro vaše aplikace:

-   **Ochrana v reálném čase**: Monitoruje aktivity ve službě cloud services a na virtuálních počítačích ke zjištění a blokování spuštění malwaru.

-   **Naplánované prohledávání**: Provádí pravidelné cílové skenování pro detekci malwaru, včetně aktivně spuštěné programy.

-   **Malwarové nápravy**: Automaticky zpracovává zjištěného malwaru, jako je například odstranění nebo umístění do karantény škodlivých souborů a čištění položky škodlivé registru.

-   **Aktualizace signatur**: Automaticky nainstaluje nejnovější signatury ochrany (definice virů), který zajišťuje ochranu na frekvenci předem určené.

-   **Antimalwarový stroj aktualizuje**: Automaticky aktualizuje Microsoft Antimalware Engine.

-   **Antimalwarová platforma aktualizace**: Antimalwarová platforma Microsoft automaticky aktualizuje.

-   **Aktivní ochranu**: Sestavy telemetrie metadata o zjištěných hrozeb a podezřelých zdrojů do služby Microsoft Azure, aby možnostech rychlé reakce na neustále se vyvíjející světě hrozeb umožňuje v reálném čase synchronní podpis doručení prostřednictvím systému Microsoft active protection.

-   **Ukázky reporting**: Poskytuje a sestavám ukázky ke službě Microsoft antimalware a pomůžou vylepšit službu a umožňují řešit potíže.

-   **Vyloučení**: Umožňuje aplikaci a správců služeb ke konfiguraci určitých souborů, procesů a jednotky pro vyloučení z ochrany a kontrolu pro výkon a z jiných důvodů.

-   **Shromažďování událostí Antimalwarové**: Zaznamenává stav antimalwarové služby, podezřelé aktivity a nápravné akce prováděné v protokolu událostí operačního systému a shromažďuje do účtu úložiště Azure zákazníka.

### <a name="azure-sql-database-threat-detection"></a>Detekce hrozeb Azure SQL Database

[Detekce hrozeb Azure SQL Database](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) je nová funkce inteligence zabezpečení integrované do služby Azure SQL Database. Funguje neustále a zobrazuje se naučíte, profil a detekuje neobvyklé databázové aktivity, detekce hrozeb služby Azure SQL Database identifikuje potenciální ohrožení databáze.

Vedoucí pracovníci pověření ochranou zabezpečení nebo jiné určené správce můžete získat okamžité odeslání oznámení o podezřelých databázových aktivitách, když k nim dojde. Jednotlivým oznámením poskytuje podrobnosti o podezřelé aktivitě a doporučuje jak dále zkoumat a zmírnit hrozby.

V současné době detekce hrozeb služby Azure SQL Database zjistí potenciální ohrožení zabezpečení a útoky prostřednictvím injektáže SQL a neobvyklých databázových vzorce přístupu.

Při přijetí e-mailové oznámení detekce hrozeb, uživatelé se můžou k procházení a zobrazení záznamů auditu relevantní prostřednictvím přímého odkazu v e-mailu. Odkaz otevře prohlížeč formátu auditu nebo předem auditování šablony aplikace Excel, který zobrazuje záznamy relevantní auditu v době výskytu podezřelé události podle následujícího schématu:

-   Auditovat úložiště pro databáze nebo serveru s neobvyklé databázové aktivity.

-   Tabulka úložiště relevantní auditu, použitý k zápisu protokolu auditu v době události.

-   Záznamy hodina ihned po výskytu události auditu.

-   Záznamy se podobně jako ID události auditu v době události (volitelné pro některé detektory).

Detektory hrozeb SQL Database použijte jednu z následujících metod zjišťování:

-   **Deterministickou detekci**: Detekuje podezřelé vzorce (na základě pravidel) v dotazech SQL klienta, které odpovídají známé útoky. Tento přístup má zjišťování vysoké a nízké falešně pozitivní, ale omezené pokrytí, protože spadají do kategorie "atomic detekcí."

-   **Chování detekce**: Detekuje neobvyklé aktivity, která je neobvyklé chování v databázi, která nebyla během posledních 30 dnů. Příklady neobvyklé aktivity klienta SQL může být prudký nárůst neúspěšných přihlášení nebo dotazy, velký objem dat extrahují, neobvyklé canonical dotazů nebo neznámé IP adresy používané pro přístup k databázi.

### <a name="application-gateway-web-application-firewall"></a>Firewall webových aplikací pomocí Application Gateway

[Webové brány Firewall aplikací (WAF)](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) je funkce [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) , která poskytuje ochranu webovým aplikacím, které používají službu application gateway pro úroveň standard [řízení doručování aplikací](https://kemptechnologies.com/in/application-delivery-controllers) funkce. Firewall webových aplikací k tomu je chrání před většinou z [Open Web Application zabezpečení projektu (OWASP) top 10 nejčastějších webových chyb zabezpečení](https://www.owasp.org/index.php/Top_10_2010-Main).

![Diagram aplikace brány Firewall webových aplikací](./media/azure-threat-detection/azure-threat-detection-fig13.png)

Ochranu patří:

-   Ochrana prostřednictvím injektáže SQL.

-   Mezi ochranou skriptování.

-   Běžné webové útoků ochranu, jako je například injektáž příkazů, pronášení, rozdělování odpovědí protokolu HTTP požadavku HTTP a útok vzdáleného souboru.

-   Ochrana před narušením protokolu HTTP.

-   Ochrana proti anomálie protokolu HTTP, jako je například chybějící user-agent hostitele nebo hlavičky accept.

-   Ochrana před roboty, prohledávacími moduly a skenery.

-   Detekce běžných chyb v konfiguraci aplikací (to znamená, Apache, IIS atd.).

Konfigurace WAF ve Vaše brána application gateway poskytuje následující výhody:

-   Chrání webové aplikace před webovými chybami zabezpečení a útoky bez nutnosti upravovat back endový kód.

-   Chrání více webových aplikací současně za službou application gateway. Služba application gateway podporuje hostování až 20 webů.

-   Monitorování webové aplikace před útoky s využitím v reálném čase, které jsou generovány pomocí protokolů waf služby application gateway.

-   Pomáhá splnit požadavky na dodržování předpisů. Některé kontroly dodržování předpisů vyžadují všech internetových koncových bodů chráněné řešením WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Detekce anomálií rozhraní API: Sestavován Azure Machine Learning

Rozhraní API pro detekci anomálií je rozhraní API, které jsou užitečné ke zjištění různých anomálních vzorů ve vašich datech časových řad. Rozhraní API přiřazuje hodnocení anomálie na každý datový bod v časové řadě, který slouží ke generování výstrah, monitorování prostřednictvím řídicích panelů nebo propojení se systémy vytváření tiketů.

[Rozhraní API pro detekci anomálií](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) dokáže detekovat následující typy anomálií v datech časových řad:

-   **Špičky a poklesy**: Když monitorujete počet pokusů o přihlášení ke službě nebo počtu rezervací na webu elektronického obchodování, neobvyklé špičky nebo vyhrazené IP adresy můžou značit útoky na zabezpečení nebo přerušení služeb.

-   **Pozitivní a negativní trendy**: V případě, že monitorujete využití paměti ve výpočetním prostředí, zmenšování velikosti volné paměti označuje potenciální nevrácená paměť. Pro monitorování délky fronty služby může trvalé stoupající trend značit problém se základním softwarem.

-   **Změny úrovní a změny dynamických rozsahů hodnot**: Změny na úrovni latence služby po upgradu služby nebo nižší úrovně výjimek po upgradu může být zajímavé monitorovat.

Machine learning API umožní:

-   **Detekce pružnější a odolnější**: Modely detekce anomálií umožňují uživatelům konfigurovat nastavení citlivosti a detekci anomálií mezi sezónní a sezónní datovými sadami. Uživatelům můžete upravit model detekce anomálií jednodušeji rozhraní API pro detekci méně nebo více citlivé podle jejich potřeb. To by znamenalo zjišťování méně nebo více viditelné anomálie v datech a nemusíte sezónní vzory.

-   **Škálovatelná a včasné rozpoznávání**: Tradičním způsobem monitorování s využitím této prahové hodnoty stanovené odborné znalosti domény jsou nákladné a není škálovatelné milionům Dynamická změna datových sad. Zkušenosti modely detekce anomálií v toto rozhraní API a modely jsou automaticky optimalizovaná z dat v reálném čase i historickými.

-   **Proaktivní a užitečné detekce**: Pomalé trendů a změna úrovně zjišťování lze použít pro včasnou detekci anomálií. Časná neobvyklé signály, které jsou zjištěny umožňuje přímé člověka vyšetřovat a reagovat na problémových oblastí. Kromě toho hlavní příčina modelů analýzy a výstrah nástroje mohou být vytvořeny na tuto službu rozhraní API detekce anomálií.

Rozhraní API pro detekci anomálií je účinný a efektivní řešení pro širokou škálu scénářů, jako je například stav služeb a klíčových ukazatelů výkonu, monitorování, IoT, monitorování výkonu a sledování síťových přenosů. Tady jsou některé oblíbené scénáře, ve kterém toto rozhraní API může být užitečné:

- Oddělení IT potřebují nástroje pro sledování událostí, kód chyby, protokolů využití a výkonu (procesor, paměť atd.) včas.

-   Servery online obchod chcete sledovat aktivity zákazníků, zobrazení stránek, kliknutí a tak dále.

-   Nástroj společnosti chtějí sledovat spotřeby vody, plynu, elektrické energie a dalším prostředkům.

-   Služby správy pro zařízení nebo v budově chcete monitorovat teploty, vlhkosti, provoz a tak dále.

-   IoT/výrobci chcete použít data ze senzorů v časové řadě monitorování pracovní postup, kvality a tak dále.

-   Poskytovatelé služeb, jako je například volání centra, potřebujete k monitorování služby vyžádání trend objem incidentů, délka čekací fronty a tak dále.

-   Business analytics skupiny chcete sledovat klíčové ukazatele výkonu (jako je objem prodeje, zabarvení zákazníka nebo ceny) neobvyklé pohyb v reálném čase.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) je zásadní součástí sady Microsoft Cloud Security. Je komplexní řešení, které může vaší organizaci pomoct při přesunu do plně využít potenciál cloudových aplikací. Zajišťuje vám kontrolu díky lepšímu přehledu o aktivity. Zvyšuje také úroveň ochrany důležitých dat napříč cloudovými aplikacemi.

Díky nástrojům, které usnadňují odhalení stínového IT, vyhodnocování rizik, vynucování zásad, prošetřování aktivit a zastavení hrozeb, může vaše organizace bezpečně přejít na cloudové řešení a zároveň si zachovat kontrolu nad důležitými daty.

| | |
|---|---|
| Informace | Možnost odhalte stínové IT s Cloud App Security. Získejte potřebný přehled díky zjišťování aplikací, aktivit, uživatelů, dat a souborů ve vašem cloudovém prostředí. Objevte aplikace třetích stran, které jsou připojené k vašemu cloudu.|
|Prozkoumat | Cloudové aplikace můžete prošetřete pomocí forenzních cloudových nástrojů podrobné informace o rizikových aplikacích, konkrétních uživatelů a souborech v síti. Najdete vzorce v datech shromážděných z vašeho cloudu. Generování sestav pro monitorování vašeho cloudu. |
| Řízení | Zmírnění rizik pomocí nastavení zásad a výstrah můžete dosáhnout tak maximální kontroly nad síťovými přenosy v cloudu. Pomocí Cloud App Security můžete svoje uživatele migrovat do alternativních bezpečných a schválených cloudových aplikací. |
| Ochrana | Cloud App Security můžete schválit nebo zakazují, aby se aplikace, vynucovat ochranu před únikem informací, řídit oprávnění a sdílení a generovat vlastní sestavy a upozornění. |
| Řízení | Zmírnění rizik pomocí nastavení zásad a výstrah můžete dosáhnout tak maximální kontroly nad síťovými přenosy v cloudu. Pomocí Cloud App Security můžete svoje uživatele migrovat do alternativních bezpečných a schválených cloudových aplikací. |
| | |


![Cloud App Security diagramu](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integruje viditelnost s cloudem těmito:

-   Díky využívání Cloud Discovery k namapování a identifikování vašeho cloudového prostředí a cloudových aplikací vaší organizace používá.

-   Schválení nebo zrušení zákazu aplikací ve vašem cloudu.

-   pomocí snadno použitelných aplikačních konektorů, které využít rozhraní API poskytovatelů, pro viditelnost a zásady správného řízení aplikací, které se připojují k.

-   dokážete mít nepřetržitou kontrolu díky nastavení a následnému průběžnému dolaďování, zásady.

Na shromažďování dat z těchto zdrojů, Cloud App Security spustí pokročilé analýzy. Ji okamžitě vás upozorní na neobvyklé aktivity a umožní vám podrobně prozkoumat cloudové prostředí. Můžete nakonfigurovat zásady v Cloud App Security a použít ji k ochraně všechno, co ve vašem cloudovém prostředí.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Rozšířená detekce hrozeb funkce třetích stran v Tržišti Azure Marketplace

### <a name="web-application-firewall"></a>Web Application Firewall (Brána firewall webových aplikací)

Web Application Firewall kontroluje příchozí webový provoz a blokuje SQL injektáže, skriptování napříč weby, nahrávání malwaru, útoky DDoS aplikace a další útoky určenou pro vaše webové aplikace. Také kontroluje odpovědi z back endové webové servery pro prevence ztráty dat (DLP). Stroj Accessu integrovaného ovládacího prvku umožňuje správcům vytvořit zásady řízení pro ověřování, autorizaci, granulární přístup a monitorování účtů (AAA), který poskytuje organizacím silného ověřování a uživatelského ovládacího prvku.

Firewall webových aplikací poskytuje následující výhody:

-   Zjišťuje a blokuje SQL injektáže skriptování napříč weby, nahrávání malwaru, útoky DDoS a další útoky proti vašim aplikaci.

-   Ověřování a řízení přístupu.

-   Kontroluje odchozí provoz do zjištění citlivých dat a můžete maskovat nebo blokovat informace z úniku navýšení kapacity.

-   Zrychluje dodávání obsahu webové aplikace, pomocí funkcí, jako je ukládání do mezipaměti, kompresi a další optimalizace provozu.

Příklady firewallů webových aplikací, které jsou k dispozici na webu Azure Marketplace najdete v tématu [Barracuda WAF, Brocade virtuální firewallu webových aplikací (vWAF), Imperva SecureSphere a brány firewall protokolu IP ThreatSTOP](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Další postup

- [Funkce detekce ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities): Pomáhá identifikovat aktivní hrozby, které cílí vašich prostředků Azure a poskytuje přehledy, které je potřeba rychle reagovat.

- [Detekce hrozeb Azure SQL Database](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Pomůže vyřešit vaše obavy týkající se potenciálních ohrožení databáze.
