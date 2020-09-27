---
title: Rozšířená detekce hrozeb Azure | Microsoft Docs
description: Seznamte se s integrovanou funkcí pokročilé detekce hrozeb pro Azure, jako je například služba Azure AD Identity Protection.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 78043e3d95a94d0e9be810164b31ef031234d620
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399597"
---
# <a name="azure-advanced-threat-detection"></a>Rozšířená detekce hrozeb v Azure

Azure nabízí integrované funkce detekce hrozeb prostřednictvím služeb, jako je Azure Active Directory (Azure AD), protokoly Azure Monitor a Azure Security Center. Tato kolekce služeb zabezpečení a možností nabízí jednoduchý a rychlý způsob, jak porozumět tomu, co se děje v nasazeních Azure.

Azure nabízí nejrůznější možnosti konfigurace a přizpůsobení zabezpečení, aby splňovaly požadavky nasazení vaší aplikace. Tento článek popisuje, jak tyto požadavky splnit.

## <a name="azure-active-directory-identity-protection"></a>Ochrana identit služby Azure Active Directory

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) je funkce [Azure Active Directory Premium P2](../../active-directory/active-directory-whatis.md) Edition, která poskytuje přehled o detekcích rizik a potenciálních ohroženích zabezpečení, které mohou ovlivnit identity vaší organizace. Identity Protection používá stávající možnosti detekce anomálií Azure AD, které jsou dostupné prostřednictvím [sestav aktivit Azure AD neobvyklé](../../active-directory/active-directory-reporting-azure-portal.md), a přináší nové typy detekce rizik, které mohou detekovat anomálie v reálném čase.

![Diagram Azure AD Identity Protection](./media/threat-detection/azure-threat-detection-fig1.png)

Identity Protection pomocí adaptivních algoritmů strojového učení a heuristiky detekuje anomálie a detekci rizik, které mohou znamenat, že došlo k ohrožení identity. Pomocí těchto dat aplikace Identity Protection generuje sestavy a výstrahy, aby bylo možné tyto detekce rizik prozkoumat a učinit patřičnou nápravu nebo zmírňující opatření.

Azure Active Directory Identity Protection je více než Nástroj pro monitorování a vytváření sestav. V závislosti na detekcích rizik aplikace Identity Protection počítá úroveň rizika uživatele pro každého uživatele, takže můžete nakonfigurovat zásady založené na rizicích, které budou automaticky chránit identity vaší organizace.

Tyto rizikové zásady, kromě dalších [ovládacích prvků podmíněného přístupu](../../active-directory/active-directory-conditional-access-azure-portal.md) , které jsou poskytovány Azure Active Directory a [EMS](../../active-directory/active-directory-conditional-access-azure-portal.md), mohou automaticky blokovat nebo nabízet adaptivní nápravné akce, které zahrnují obnovení hesla a vynucení vícefaktorového ověřování.

### <a name="identity-protection-capabilities"></a>Možnosti ochrany identity

Azure Active Directory Identity Protection je více než Nástroj pro monitorování a vytváření sestav. Chcete-li chránit identity vaší organizace, můžete nakonfigurovat zásady založené na rizicích, které automaticky reagují na zjištěné problémy v případě dosažení zadané úrovně rizika. Tyto zásady, kromě dalších ovládacích prvků podmíněného přístupu poskytovaných Azure Active Directory a EMS, můžou automaticky blokovat nebo iniciovat adaptivní nápravné akce, včetně resetování hesel a vynucení vícefaktorového ověřování.

Příklady některých způsobů, jak může Azure Identity Protection pomáhat zabezpečit vaše účty a identity, zahrnují:

[Zjišťování detekcí rizik a rizikových účtů](../../active-directory/identity-protection/overview.md)
-   Zjišťuje šest typů detekce rizik pomocí strojového učení a heuristických pravidel.
-   Vypočítat úrovně rizika uživatele.
-   Poskytněte vlastní doporučení pro zlepšení celkového stav zabezpečení tím, že zvýrazníte slabá místa.

[Zkoumání zjištění rizik](../../active-directory/identity-protection/overview.md)
-   Odesílat oznámení pro detekci rizik.
-   Prozkoumejte detekci rizik pomocí relevantních a kontextových informací.
-   Poskytněte základní pracovní postupy pro sledování vyšetřování.
-   Poskytněte snadný přístup k opravám, jako je resetování hesla.

[Zásady podmíněného přístupu na základě rizik](../../active-directory/identity-protection/overview.md)
-   Omezení rizikových přihlášení blokováním přihlášení nebo vyžádáním výzev pro službu Multi-Factor Authentication.
-   Blokování nebo zabezpečení rizikových uživatelských účtů.
-   Vyžaduje, aby se uživatelé zaregistrovali pro službu Multi-Factor Authentication.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

S [Azure Active Directory Privileged identity managementem (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)můžete spravovat, řídit a monitorovat přístup v rámci vaší organizace. Tato funkce zahrnuje přístup k prostředkům v Azure AD a dalších online služby Microsoftu, jako je Microsoft 365 nebo Microsoft Intune.

![Diagram Azure AD Privileged Identity Management](./media/threat-detection/azure-threat-detection-fig2.png)

PIM vám pomůže:

-   Získejte výstrahy a sestavy týkající se správců služby Azure AD a řízení přístupu JIT (just-in-time) do Microsoftu online služby, jako je například Microsoft 365 a Intune.

-   Získejte sestavy o historii přístupu správce a změnách v přiřazeních správce.

-   Získejte výstrahy týkající se přístupu k privilegované roli.

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure monitor](../../azure-monitor/index.yml) jsou cloudové řešení Microsoftu pro správu IT, které pomáhá spravovat a chránit místní i cloudovou infrastrukturu. Vzhledem k tomu, že protokoly Azure Monitor jsou implementovány jako cloudová služba, můžete ji rychle provozovat a s minimálními investicemi do služeb infrastruktury. Nové funkce zabezpečení se dodávají automaticky a šetří průběžné údržby a náklady na upgrade.

Kromě poskytování cenných služeb vlastním způsobem Azure Monitor protokoly mohou integrovat s součástmi nástroje System Center, jako je například [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), a rozšířit tak stávající investice do cloudu do cloudu. Protokoly nástroje System Center a Azure Monitor můžou spolupracovat a poskytovat kompletní hybridní prostředí pro správu.

### <a name="holistic-security-and-compliance-posture"></a>Holistický zabezpečení a dodržování předpisů stav

[Řídicí panel Log Analytics Security and Audit](../../security-center/security-center-intro.md) poskytuje ucelený přehled o stav zabezpečení IT vaší organizace s integrovanými vyhledávacími dotazy pro významné problémy, které vyžadují vaši pozornost. Řídicí panel Security and Audit je domovská obrazovka pro všechno, co souvisí se zabezpečením v protokolech Azure Monitor. Poskytuje celkový přehled o stavu zabezpečení vašich počítačů. Můžete také zobrazit všechny události za posledních 24 hodin, 7 dní nebo jakýkoli jiný vlastní časový rámec.

Protokoly Azure Monitor vám pomůžou rychle a snadno pochopit celkové staví zabezpečení jakéhokoli prostředí, a to vše v kontextu provozu IT, včetně posouzení aktualizací softwaru, antimalwarového posouzení a standardních hodnot konfigurace. Data protokolu zabezpečení jsou snadno dostupná pro zjednodušení procesů auditu zabezpečení a dodržování předpisů.

![Řídicí panel Security and Audit Log Analytics](./media/threat-detection/azure-threat-detection-fig3.jpg)

Řídicí panel Security and Audit Log Analytics se organizuje do čtyř hlavních kategorií:

-   **Domény zabezpečení**: umožňuje podrobněji prozkoumat záznamy zabezpečení v průběhu času. přístup k posouzení malwaru; posouzení aktualizací; zobrazení informací o zabezpečení, identitě a přístupu k síti; Zobrazit počítače s událostmi zabezpečení; a rychlý přístup k řídicímu panelu Azure Security Center.

-   **Významné problémy**: umožňuje rychle identifikovat počet aktivních problémů a závažnost problémů.

-   **Detekce (Preview)**: umožňuje identifikovat vzory útoků tím, že zobrazuje výstrahy zabezpečení při jejich výskytu na vašich prostředcích.

-   **Analýza hrozeb**: umožňuje identifikovat vzory útoků tím, že zobrazuje celkový počet serverů s odchozím škodlivým provozem IP, typ škodlivé hrozby a mapa umístění IP adres.

-   **Běžné dotazy na zabezpečení**: seznam nejběžnějších bezpečnostních dotazů, které můžete použít k monitorování vašeho prostředí. Když vyberete libovolný dotaz, otevře se podokno hledání a zobrazí se výsledky tohoto dotazu.

### <a name="insight-and-analytics"></a>Přehledy a analýzy
V centru [Azure monitor protokolů](../../log-analytics/log-analytics-queries.md) je úložiště hostované v Azure.

![Diagram Insight and Analytics](./media/threat-detection/azure-threat-detection-fig4.png)

Data se shromažďují do úložiště z připojených zdrojů tak, že se nakonfigurují zdroje dat a přidávají řešení do vašeho předplatného.

![Řídicí panel protokolů Azure Monitor](./media/threat-detection/azure-threat-detection-fig5.png)

Zdroje dat a řešení každý vytvoří samostatné typy záznamů s vlastní sadou vlastností, ale můžete je i nadále analyzovat v dotazech do úložiště. Stejné nástroje a metody můžete použít pro práci s nejrůznějšími daty, která jsou shromažďována různými zdroji.


Většina vašich interakcí s protokoly Azure Monitor je prostřednictvím Azure Portal, která se spouští v jakémkoli prohlížeči a poskytuje přístup k nastavení konfigurace a několika nástrojům pro analýzy shromážděných dat a práci s nimi. Z portálu můžete použít:
* [Hledání v protokolu](../../log-analytics/log-analytics-queries.md) , kde můžete vytvářet dotazy pro analýzu shromážděných dat.
* [Řídicí panely](../../azure-monitor/learn/tutorial-logs-dashboards.md), které můžete přizpůsobit pomocí grafických zobrazení vašich nejcennějších hledání.
* [Řešení](../../monitoring/monitoring-solutions.md), která poskytují další funkce a analytické nástroje.

![Analytické nástroje](./media/threat-detection/azure-threat-detection-fig6.png)

Řešení přidávají funkce do protokolů Azure Monitor. Primárně běží v cloudu a poskytují analýzu dat, která jsou shromážděna v úložišti Log Analytics. Řešení mohou také definovat nové typy záznamů, které mají být shromažďovány, které lze analyzovat pomocí hledání v protokolu nebo pomocí dalšího uživatelského rozhraní, které řešení poskytuje v řídicím panelu Log Analytics.

Řídicí panel Security and Audit je příkladem těchto typů řešení.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatizace a řízení: výstraha o posunech konfigurace zabezpečení

Azure Automation automatizuje procesy správy pomocí runbooků, které jsou založené na PowerShellu a běží v cloudu. Runbooky se mohou provádět také na serveru ve vašem místním datovém centru a mohou spravovat místní prostředky. Azure Automation poskytuje správu konfigurace pomocí prostředí PowerShell pro konfiguraci požadovaného stavu (DSC).

![Diagram Azure Automation](./media/threat-detection/azure-threat-detection-fig7.png)

Můžete vytvářet a spravovat prostředky DSC hostované v Azure a použít je v cloudových a místních systémech. Díky tomu můžete definovat a automaticky vymáhat jejich konfiguraci nebo získat sestavy na posun, aby se zajistilo, že konfigurace zabezpečení zůstanou v zásadách.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomáhá chránit vaše prostředky Azure. Poskytuje integrované monitorování zabezpečení a správu zásad napříč předplatnými Azure. V rámci služby můžete definovat zásady pro větší členitost v předplatných Azure i [skupinám prostředků](../../azure-resource-manager/management/manage-resources-portal.md) .

![Diagram Azure Security Center](./media/threat-detection/azure-threat-detection-fig8.png)

Výzkumníci v oblasti zabezpečení ze společnosti Microsoft neustále vyhledávají nové hrozby. Mají přístup k rozsáhlé sadě telemetrických údajů získávaných díky globálního prezenci společnosti Microsoft v cloudových i místních prostředích. Tento rozsáhlý a různorodý soubor datových sad umožňuje společnosti Microsoft objevovat nová schémata a trendy útoků v rámci jejích místních produktů pro zákazníky a podniky a rovněž i v rámci online služeb.

Security Center tak můžou rychle aktualizovat algoritmy detekce, jako by útočníci vydávat nové a stále důmyslnější zneužití. Tento přístup vám pomůže udržet si krok s rychlým přesunutím ohroženého prostředí.

![Security Center detekce hrozeb](./media/threat-detection/azure-threat-detection-fig9.jpg)

Detekce hrozeb ve službě Security Center funguje tak, že se automaticky shromažďují informace o zabezpečení ze všech prostředků Azure, ze sítě a připojených partnerských řešení. Analyzuje tyto informace a koreluje informace z různých zdrojů, aby bylo možné identifikovat hrozby.

U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit.

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Průlomy v technologiích pro velké objemy dat a [strojové učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) se používají k vyhodnocení událostí napříč celými prostředky infrastruktury cloudu. Pokročilá analýza dokáže detekovat hrozby, které by nebylo možné identifikovat prostřednictvím ručních přístupů a předpovědi vývoje útoků. Tyto typy analýz zabezpečení jsou pokryté v dalších oddílech.

### <a name="threat-intelligence"></a>Analýza hrozeb

Microsoft má přístup k obrovské množství globálních analýz hrozeb.

Telemetrie se nachází z různých zdrojů, jako jsou Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, DCU (Microsoft Digital Zločins Unit) a Microsoft Security Response Center (MSRC).

![Zjištění analýzy hrozeb](./media/threat-detection/azure-threat-detection-fig10.jpg)

Výzkumníki také dostanou informace analýzy hrozeb, které jsou sdíleny mezi hlavními poskytovateli cloudových služeb, a přihlásí se k odběru kanálů pro analýzu hrozeb od třetích stran. Služba Azure Security Center vás pomocí těchto informací může upozornit na hrozby známých útočníků. Možné příklady:

-   Využití **síly Machine Learning**: Azure Security Center má přístup k obrovskému množství dat o aktivitě cloudové sítě, které se dá použít ke zjišťování hrozeb cílících na nasazení Azure.

-   **Detekce hrubou silou**: strojové učení slouží k vytvoření historických vzorů pokusů o vzdálený přístup, které umožňuje detekovat útoky hrubou silou proti Secure Shell (SSH), protokol RDP (Remote Desktop Protocol) (RDP) a portům SQL.

-   **Detekce odchozích DDoS a botnetu**: běžným cílem útoků, které cílí na cloudové prostředky, je použití výpočetní síly těchto prostředků ke spouštění dalších útoků.

-   **Nové analytické servery a**virtuální počítače pro chování: po ohrožení bezpečnosti serveru nebo virtuálního počítače využívají útočníci širokou škálu technik pro spouštění škodlivého kódu v tomto systému, přičemž se vyhnete detekci, zajištění Persistence a už nemusí komplikovaně řízení zabezpečení.

-   **Azure SQL Database detekce hrozeb**: detekce hrozeb pro Azure SQL Database, která identifikuje neobvyklé databázové aktivity, které označují neobvyklé a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

### <a name="behavioral-analytics"></a>Analýza chování

Behaviorální analýza je technika, která analyzuje a porovnává data se sadou známých schémat. Tato schémata však nepředstavují jednoduché příznaky. Určují se prostřednictvím komplexních algoritmů strojového učení, které se aplikují na rozsáhlé datové sady.

![Zjištění chování při analýze](./media/threat-detection/azure-threat-detection-fig11.jpg)

Vzorce jsou také určeny pečlivou analýzou škodlivých chování zkušenými analytiky. Azure Security Center může pomocí analýzy chování identifikovat ohrožené prostředky na základě analýzy protokolů virtuálních počítačů, protokolů virtuálních síťových zařízení, protokolů prostředků infrastruktury, výpisů stavu systému a dalších zdrojů.

Kromě toho jsou vzory korelace s jinými signály, aby kontrolovaly podpůrný důkaz pro rozšířenou kampaň. Tato korelace pomáhá identifikovat události, které jsou konzistentní se zavedenými ukazateli ohrožení zabezpečení.

Možné příklady:
-   **Podezřelé spouštění procesů:** Útočníci používají řadu technik k tajnému spouštění škodlivého softwaru. Útočník by například mohl dát malwaru stejný název jako legitimní systémové soubory, ale umístit tyto soubory do alternativního umístění, použít název, který je podobný jako neškodný soubor, nebo maskovat příponu souboru na hodnotu true. Security Center modely chování procesu a monitorují provádění procesů, aby bylo možné zjistit odlehlé hodnoty, jako jsou tyto.

-   **Skrytý malware a pokusy o zneužití**: důmyslný malware může obejít tradiční antimalwarové produkty tím, že nikdy nezapisuje na disk nebo nešifruje softwarové komponenty uložené na disku. Takový malware je však možné zjistit pomocí analýzy paměti, protože malware musí ponechat trasování v paměti, aby fungoval. Pokud dojde k chybě softwaru, ve výpisu stavu systému se zaznamená část paměti v době selhání. Díky analýze paměti ve výpisu stavu systému může Azure Security Center detekovat techniky používané pro zneužití ohrožení zabezpečení softwaru, přístup k důvěrným datům a nenápadně trvalý v napadeném počítači, aniž by to ovlivnilo výkon počítače.

-   **Boční pohyb a interní rekognoskace**: Pokud chcete zachovat napadenou síť a vyhledat a získat cenná data, útočníci se často snaží přesunout z napadeného počítače na jiné v rámci stejné sítě. Security Center monitoruje aktivity procesu a přihlášení ke zjištění pokusů o rozšíření dostane útočníka v rámci sítě, jako je například spuštění vzdáleného příkazu, zjišťování sítě a výčet účtů.

-   **Škodlivé skripty PowerShellu**: útočníci můžou použít prostředí PowerShell k provádění škodlivého kódu na cílových virtuálních počítačích pro různé účely. Služba Security Center kontroluje aktivitu prostředí PowerShell a hledá známky podezřelé aktivity.

-   **Odchozí útoky:** Útočníci často cílí na cloudové prostředky s cílem využít je k dalším útokům. Ohrožené virtuální počítače mohou být například použity ke spuštění útoků hrubou silou proti jiným virtuálním počítačům, k odesílání spamu nebo k prohledávání otevřených portů a dalších zařízení na internetu. Služba Security Center pomocí strojového učení, které uplatňuje na síťový provoz, dokáže detekovat odchozí síťovou komunikaci vybočující z normy. Když se zjistí spam, Security Center taky koreluje neobvyklý e-mailový přenos pomocí inteligentních dat z Microsoft 365 a určí, jestli se e-mail nejspíš nekalé nebo výsledek legitimní e-mailové kampaně.

### <a name="anomaly-detection"></a>Detekce anomálií

Služba Azure Security Center také identifikuje hrozby pomocí detekce anomálií. Oproti behaviorální analýze (která závisí na známých schématech odvozovaných z velkých datových sad), je detekce anomálií více „personalizovaná“ a zaměřuje se na standardní hodnoty specifické pro vaše nasazení. Machine Learning se používá k určení normální aktivity pro vaše nasazení a pak se generují pravidla, která definují izolované podmínky, které by mohly představovat událost zabezpečení. Zde naleznete příklad:

-   **Příchozí útoky pomocí protokolu RDP/SSH**: vaše nasazení můžou mít každý den zaneprázdněné virtuální počítače s velkým množstvím přihlašovacích údajů a jiné virtuální počítače, které mají v nějakém případě nějaké přihlašovací údaje. Azure Security Center může pro tyto virtuální počítače určit aktivitu pro přihlašovací údaje směrného plánu a pomocí strojového učení definovat informace o běžných přihlašovacích aktivitách. Pokud dojde k nesouladu s směrným plánem definovaným pro vlastnosti související s přihlášením, může být vygenerována výstraha. A strojové učení tu zase určuje, co je významné.

### <a name="continuous-threat-intelligence-monitoring"></a>Průběžné monitorování analýzy hrozeb

Azure Security Center pracuje se službou Security Research a týmy pro datové vědy po celém světě, které neustále monitorují změny v rámci hrozby. To zahrnuje následující iniciativy:

-   **Monitorování analýzy hrozeb**: Analýza hrozeb zahrnuje mechanismy, ukazatele, dopady a Rady s případnými názory na stávající nebo nově vznikající hrozby. Tyto informace jsou sdílené v komunitě zabezpečení a Microsoft neustále monitoruje kanály o hrozbách hrozeb z interních a externích zdrojů.

-   **Sdílení signálu**: přehledy z bezpečnostních týmů v rámci širokého portfolia Microsoftu pro cloudové a místní služby, servery a zařízení koncového bodu klienta se sdílejí a analyzují.

-   **Odborníci na zabezpečení Microsoftu**: nepřetržitá zapojení s týmy v rámci Microsoftu, které fungují ve specializovaných polích zabezpečení, jako je forenzní a detekce útoků na web.

-   **Optimalizace detekce**: algoritmy jsou spouštěny proti skutečným zákaznickým datovým sadám a výzkumníki zabezpečení pracují se zákazníky k ověření výsledků. Pravdivě a falešně pozitivní výsledky pak slouží ke zlepšování algoritmů strojového učení.

Tato kombinovaná snaha má za následek nové a vylepšené detekce, na které můžete okamžitě využít výhody. K dispozici není žádná akce, kterou je potřeba provést.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Funkce Rozšířené detekce hrozeb: další služby Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuální počítače: Microsoft Antimalware

[Microsoft Antimalware](antimalware.md) pro Azure je jedním z agentů pro aplikace a klientská prostředí navržená tak, aby běžela na pozadí bez zásahu člověka. Ochranu můžete nasadit v závislosti na potřebách úloh aplikací, a to buď pomocí základní zabezpečené, nebo pokročilé vlastní konfigurace, včetně antimalwarového monitorování. Azure antimalware je možnost zabezpečení pro virtuální počítače Azure, která je automaticky nainstalovaná na všech virtuálních počítačích Azure PaaS.

#### <a name="microsoft-antimalware-core-features"></a>Základní funkce Microsoft antimalwaru

Tady jsou funkce Azure, které nasazují a povolují Microsoft Antimalware pro vaše aplikace:

-   **Ochrana v reálném čase**: monitoruje aktivity v cloudových službách a na virtuálních počítačích pro detekci a blokování spuštění malwaru.

-   **Naplánované prohledávání**: pravidelně provádí cílené prohledávání za účelem zjištění malwaru, včetně aktivně spouštěných programů.

-   **Náprava malwaru**: automaticky funguje u zjištěného malwaru, jako je odstranění nebo karanténa škodlivých souborů a vyčištění škodlivých položek registru.

-   **Aktualizace signatur**: automaticky nainstaluje nejnovější podpisy ochrany (definice virů), aby se zajistila aktuálnost ochrany v předem určené četnosti.

-   **Aktualizace antimalwarového stroje**: automaticky aktualizuje modul Microsoft Antimalware.

-   **Aktualizace antimalwarové platformy**: automaticky aktualizuje platformu Microsoft Antimalware.

-   **Aktivní ochrana**: oznamuje metadata telemetrie o zjištěných hrozbách a podezřelých materiálech, aby bylo možné Microsoft Azure zajistit rychlou odezvu na vývojovou ochranu v reálném čase prostřednictvím systému Microsoft Active Protection System.

-   **Vytváření sestav ukázek**: poskytuje a sestavuje ukázky služby Microsoft antimalwar Service, které vám pomůžou službu zdokonalit a povolit řešení potíží.

-   **Vyloučení**: umožňuje správcům aplikací a služeb nakonfigurovat určité soubory, procesy a jednotky pro vyloučení z ochrany a kontrolu výkonu a dalších důvodů.

-   **Shromažďování antimalwarových událostí**: zaznamenává stav antimalwarové služby, podezřelé aktivity a nápravné akce provedené v protokolu událostí operačního systému a shromažďuje je do účtu Azure Storage zákazníka.

### <a name="azure-sql-database-threat-detection"></a>Azure SQL Database detekce hrozeb

[Azure SQL Database detekce hrozeb](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) je nová funkce Security Intelligence integrovaná do služby Azure SQL Database. Při práci s časem se naučíte, profilovat a zjišťují aktivity databáze neobvyklé Azure SQL Database detekce hrozeb identifikuje potenciální hrozby pro databázi.

Bezpečnostní důstojníci nebo jiní určení Správci mohou získat okamžité oznámení o podezřelých databázových činnostech, když k nim dojde. Každé oznámení poskytuje podrobné informace o podezřelé aktivitě a doporučuje, jak tuto hrozbu dále prozkoumat a zmírnit.

V současné době Azure SQL Database detekce hrozeb detekuje potenciální ohrožení zabezpečení a útoky prostřednictvím injektáže SQL a neobvyklé vzory přístupu k databázi.

Po přijetí e-mailového oznámení s detekcí hrozeb můžou uživatelé procházet a zobrazovat relevantní záznamy auditu prostřednictvím přímých odkazů v e-mailu. Odkaz otevře prohlížeč auditu nebo předem nakonfigurovanou excelovou šablonu auditu, která zobrazuje relevantní záznamy auditu v době výskytu podezřelé události, a to v závislosti na následujících případech:

-   Auditujte úložiště pro databázi nebo server s neobvyklémi databázovými aktivitami.

-   Příslušná tabulka úložiště auditu, která se použila v době události pro zápis protokolu auditu.

-   Auditujte záznamy hodin hned po výskytu události.

-   Auditujte záznamy s podobným ID události v době události (volitelné pro některé detektory).

SQL Database detektory hrozeb používají jednu z následujících metodologií detekce:

-   **Deterministické zjišťování**: detekuje podezřelé vzorce (založené na pravidlech) v dotazech SQL klienta, které odpovídají známým útokům. Tato metodika má vysokou detekci a nízkou falešně pozitivní, ale omezené pokrytí, protože spadá do kategorie "zjišťování atomických".

-   **Detekce chování**: detekuje aktivitu neobvyklé, což je neobvyklé chování v databázi, které během posledních 30 dnů nevidělo. Příklady aktivity neobvyklé klientů SQL může být špičkou neúspěšných přihlášení nebo dotazů, velkým objemem dat, které se extrahují, neobvyklými kanonickými dotazy nebo neznámými IP adresami používanými pro přístup k databázi.

### <a name="application-gateway-web-application-firewall"></a>Application Gateway Firewall webových aplikací

[Firewall webových aplikací (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) je funkce služby [Azure Application Gateway](../../application-gateway/application-gateway-web-application-firewall-overview.md) , která poskytuje ochranu webových aplikací, které používají Aplikační bránu pro standardní funkce [řízení doručování aplikací](https://kemptechnologies.com/in/application-delivery-controllers) . Firewall webových aplikací to dělá tak, že je chrání před většinou z [OWASP (Open Web Application Security Project) hlavních 10 běžných webových chyb zabezpečení](https://owasp.org/www-project-top-ten/).

![Application Gateway diagram firewallu webových aplikací](./media/threat-detection/azure-threat-detection-fig13.png)

K ochraně patří:

-   Ochrana injektáže SQL.

-   Ochrana skriptování mezi weby

-   Běžné ochrany webových útoků, jako je vkládání příkazů, podkládání požadavků HTTP, rozdělování odpovědí HTTP a útok na vzdálené zahrnutí souborů.

-   Ochrana proti narušení protokolu HTTP.

-   Ochrana proti anomáliím protokolu HTTP, například chybějícímu uživatelskému agentovi a hlavičkám přijetí hostitele.

-   Prevence proti roboty, prohledávacím modulům a skenerům.

-   Detekce běžných neobvyklých konfigurací aplikací (tj. Apache, IIS atd.).

Konfigurace WAF ve vaší aplikační bráně přináší následující výhody:

-   Chrání webové aplikace před útoky z webových aplikací a útoků beze změny kódu back-endu.

-   Chrání více webových aplikací současně za aplikační bránou. Aplikační brána podporuje hostování až 20 webů.

-   Monitoruje webové aplikace proti útokům pomocí sestav v reálném čase generovaných protokoly WAF služby Application Gateway.

-   Pomáhá splnit požadavky na dodržování předpisů. Některé ovládací prvky dodržování předpisů vyžadují ochranu všech internetových koncových bodů řešením WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Rozhraní API pro detekci anomálií: sestavené s Azure Machine Learning

Rozhraní API pro detekci anomálií je rozhraní API, které je užitečné pro detekci nejrůznějších neobvyklé vzorů v datech časových řad. Rozhraní API přiřadí skóre anomálií každému datovému bodu v časové řadě, který se dá použít pro generování výstrah, monitorování prostřednictvím řídicích panelů nebo připojení k systémům pro lístky.

[Rozhraní API pro detekci anomálií](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) dokáže detekovat následující typy anomálií v datech časových řad:

-   **Špičky a**nedodržení: Pokud sledujete počet neúspěšných přihlášení ke službě nebo počtu rezervací na webu elektronického obchodování, neobvyklé špičky nebo nedodržení můžou znamenat útoky na zabezpečení nebo přerušení služeb.

-   **Pozitivní a negativní trendy**: když sledujete využití paměti v výpočetním prostředí, zmenší se velikost volné paměti, což znamená potenciální nevracení paměti. V případě monitorování délky fronty služeb může trvalý vzestupný trend označovat základní problém se softwarem.

-   **Změny úrovně a změny v dynamickém rozsahu hodnot**: Změna úrovně v latencích služby po upgradu služby nebo nižší úrovni výjimek po upgradu může být zajímavá pro monitorování.

Rozhraní API založené na strojovém učení umožňuje:

-   **Flexibilní a robustní detekce**: modely detekce anomálií umožňují uživatelům konfigurovat nastavení citlivosti a detekovat anomálie mezi sezónními a nesezónními datovými sadami. Uživatelé můžou upravit model detekce anomálií, aby rozhraní API pro detekci bylo podle svých potřeb méně nebo citlivější. To by znamenalo zjištění méně nebo více viditelných anomálií v datech s a bez sezónních vzorů.

-   **Škálovatelné a včasné zjišťování**: tradiční způsob monitorování s využitím současných prahových hodnot, které odborníci na doménu stanoví, jsou nákladné a Neškálovatelné s miliony dynamicky se měnícími se sadami dat. Dozvěděly se modely detekce anomálií v tomto rozhraní API a modely jsou automaticky vyladěny z historických dat i z dat v reálném čase.

-   **Proaktivní a zjistitelné zjišťování**: pro detekci počátečních anomálií lze použít pomalé zjišťování trendů a změn úrovně. Zjištěné počáteční neobvyklé signály se dají použít k tomu, aby se lidé mohli prozkoumat a reagovat na problémové oblasti. V této službě rozhraní API pro detekci anomálií je navíc možné vyvíjet modely analýz hlavní příčiny a nástroje pro upozorňování.

Rozhraní API pro detekci anomálií je efektivní a efektivní řešení pro široké spektrum scénářů, jako je monitorování stavu služby a sledování klíčových ukazatelů výkonu, IoT, sledování výkonu a monitorování síťových přenosů. Tady je několik oblíbených scénářů, ve kterých může být toto rozhraní API užitečné:

- Oddělení IT potřebují včas sledovat nástroje pro sledování událostí, kód chyby, protokol využití a výkon (CPU, paměť a tak dále).

-   Weby online Commerce chtějí sledovat aktivity zákazníků, zobrazení stránek, kliknutí a tak dále.

-   Pomocné společnosti chtějí sledovat spotřebu vody, plynu, elektřiny a dalších prostředků.

-   Zařízení nebo služby pro správu, které chtějí monitorovat teplotu, vlhkost, provoz atd.

-   IoT/výrobci chtějí používat data senzorů v časové řadě k monitorování pracovního toku, kvality a tak dále.

-   Poskytovatelé služeb, například volací centra, potřebují monitorovat trend poptávky za služby, objem incidentu, délku fronty čekání atd.

-   Skupiny obchodních analýz chtějí monitorovat obchodní klíčové ukazatele výkonu (například objem prodeje, zákaznická zabarvení nebo ceny) abnormální pohyb v reálném čase.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) je kritická součást sady Microsoft Cloud Security Stack. Jde o komplexní řešení, které může vaší organizaci pomáhat při přesunu, abyste plně využili výhod svých cloudových aplikací. Díky lepšímu přehledu o aktivitách zajišťuje kontrolu. Zvyšuje také úroveň ochrany důležitých dat napříč cloudovými aplikacemi.

Díky nástrojům, které usnadňují odhalení stínového IT, vyhodnocování rizik, vynucování zásad, prošetřování aktivit a zastavení hrozeb, může vaše organizace bezpečně přejít na cloudové řešení a zároveň si zachovat kontrolu nad důležitými daty.

| Kategorie | Popis |
| -------- | ----------- |
| Zjišťování | Odhalte pomocí Cloud App Security stínové IT. Získejte přehled díky zjišťování aplikací, aktivit, uživatelů, dat a souborů ve vašem cloudovém prostředí. Zjišťujte aplikace třetích stran, které jsou připojené k vašemu cloudu.|
|Prověřování | Prošetřete cloudové aplikace pomocí cloudových forenzních nástrojů, které vám umožní zjistit podrobné informace o rizikových aplikacích, konkrétních uživatelích a souborech v síti. Najděte vzorce v datech shromážděných z cloudu. Generujte sestavy pro monitorování cloudu. |
| Řízení | Nastavením zásad a upozornění můžete zmírnit rizika a dosáhnout tak maximální kontroly nad síťovými přenosy v cloudu. Pomocí Cloud App Security můžete svoje uživatele migrovat do bezpečných a schválených alternativních cloudových aplikací. |
| Ochrana | Pomocí Cloud App Security můžete schvalovat nebo zakazovat aplikace, vymáhat ochranu před únikem informací, řídit oprávnění a sdílení a generovat vlastní sestavy a výstrahy. |
| Řízení | Nastavením zásad a upozornění můžete zmírnit rizika a dosáhnout tak maximální kontroly nad síťovými přenosy v cloudu. Pomocí Cloud App Security můžete svoje uživatele migrovat do bezpečných a schválených alternativních cloudových aplikací. |


![Diagram Cloud App Security](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integruje viditelnost s vaším cloudem:

-   Použití Cloud Discovery k mapování a identifikaci cloudového prostředí a cloudových aplikací, které vaše organizace používá.

-   Schválení a zakázání aplikací v cloudu.

-   Pomocí snadno nasazených konektorů aplikací, které využívají rozhraní API poskytovatele, získáte přehled a řízení aplikací, ke kterým se připojujete.

-   Pomoc s průběžným řízením nastavením a následným průběžným vyladěním zásad.

Při shromažďování dat z těchto zdrojů Cloud App Security spouští sofistikovanou analýzu. Okamžitě vás upozorní na neobvyklé aktivity a umožní vám podrobně prozkoumat cloudové prostředí. V Cloud App Security můžete nakonfigurovat zásady a používat je k ochraně všechno, co máte ve svém cloudovém prostředí.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Pokročilé možnosti detekce hrozeb od jiných výrobců prostřednictvím Azure Marketplace

### <a name="web-application-firewall"></a>Web Application Firewall (Brána firewall webových aplikací)

Firewall webových aplikací kontroluje příchozí webový provoz a blokuje injektáže SQL, skriptování mezi weby, nakládku malwaru, útoky aplikací DDoS a další útoky zaměřené na vaše webové aplikace. Také kontroluje odpovědi z back-endové webové servery na ochranu před únikem informací (DLP). Integrovaný modul řízení přístupu umožňuje správcům vytvářet podrobné zásady řízení přístupu pro ověřování, autorizaci a monitorování účtů (AAA), které organizacím poskytuje silné ověřování a uživatelský ovládací prvek.

Firewall webových aplikací nabízí následující výhody:

-   Detekuje a blokuje injektáže SQL, skriptování mezi weby, nakládku malwaru, aplikace DDoS nebo jakékoli jiné útoky proti vaší aplikaci.

-   Ověřování a řízení přístupu.

-   Vyhledá odchozí provoz a detekuje citlivá data a může maskovat nebo blokovat únik informací.

-   Zrychluje doručování obsahu webové aplikace pomocí funkcí, jako je ukládání do mezipaměti, komprese a další optimalizace provozu.

Příklady bran firewall webových aplikací, které jsou k dispozici v Azure Marketplace, najdete v tématu [Barracuda WAF, firewall pro Virtual Web Application firewall (vWAF), Imperva SecureSphere a ThreatSTOP IP adresa](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Další kroky

- [Reakce na dnešní hrozby](../../security-center/security-center-alerts-overview.md#respond-threats): pomáhá identifikovat aktivní hrozby, které cílí na vaše prostředky Azure, a poskytuje přehledy, které potřebujete k rychlé reakci.

- [Azure SQL Database detekce hrozeb](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): pomáhá řešit vaše obavy týkající se potenciálních hrozeb pro vaše databáze.
