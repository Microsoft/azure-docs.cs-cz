---
title: Azure pokročilá detekce hrozeb | Dokumenty společnosti Microsoft
description: Přečtěte si o azure ad identity protection a jeho možnosti.
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
ms.openlocfilehash: 3c1c385a87fc302d180729ec2e4bcd1c4a315f6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981466"
---
# <a name="azure-advanced-threat-detection"></a>Rozšířená detekce hrozeb Azure

Azure nabízí integrované funkce detekce hrozeb prostřednictvím služeb, jako je Azure Active Directory (Azure AD), protokoly Azure Monitoru a Azure Security Center. Tato kolekce služeb a funkcí zabezpečení poskytuje jednoduchý a rychlý způsob, jak pochopit, co se děje v rámci nasazení Azure.

Azure nabízí širokou škálu možností konfigurace a přizpůsobení zabezpečení tak, aby splňovaly požadavky nasazení aplikací. Tento článek popisuje, jak splnit tyto požadavky.

## <a name="azure-active-directory-identity-protection"></a>Ochrana identit služby Azure Active Directory

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) je funkce [edice Azure Active Directory Premium P2,](../../active-directory/active-directory-whatis.md) která poskytuje přehled detekcí rizik a potenciálních chyb zabezpečení, které mohou ovlivnit identity vaší organizace. Ochrana identity používá stávající funkce detekce anomálií Azure AD, které jsou dostupné prostřednictvím [sestav anomální aktivity Azure AD](../../active-directory/active-directory-reporting-azure-portal.md)a zavádí nové typy detekce rizik, které dokáží detekovat anomálie v reálném čase.

![Diagram ochrany identit azure ad](./media/threat-detection/azure-threat-detection-fig1.png)

Ochrana identity používá adaptivní algoritmy strojového učení a heuristiky k detekci anomálií a detekce rizik, které by mohly naznačovat, že identita byla ohrožena. Pomocí těchto dat identity Protection generuje sestavy a výstrahy, takže můžete prozkoumat tyto detekce rizik a provést příslušnou nápravu nebo zmírnění opatření.

Azure Active Directory Identity Protection je víc než jen nástroj pro monitorování a vytváření sestav. Na základě detekce rizik služba Identity Protection vypočítá úroveň rizika uživatele pro každého uživatele, takže můžete nakonfigurovat zásady založené na rizicích tak, aby automaticky chránily identity vaší organizace.

Tyto zásady založené na rizicích, kromě dalších [ovládacích prvků podmíněného přístupu,](../../active-directory/active-directory-conditional-access-azure-portal.md) které jsou poskytovány službou Azure Active Directory a [EMS](../../active-directory/active-directory-conditional-access-azure-portal.md), mohou automaticky blokovat nebo nabízet adaptivní nápravné akce, které zahrnují resetování hesla a vynucení vícefaktorového ověřování.

### <a name="identity-protection-capabilities"></a>Možnosti ochrany identity

Azure Active Directory Identity Protection je víc než jen nástroj pro monitorování a vytváření sestav. Chcete-li chránit identity vaší organizace, můžete nakonfigurovat zásady založené na rizicích, které automaticky reagují na zjištěné problémy po dosažení zadané úrovně rizika. Tyto zásady, kromě jiných ovládacích prvků podmíněného přístupu poskytovaných službou Azure Active Directory a EMS, mohou automaticky blokovat nebo iniciovat adaptivní nápravné akce, včetně resetování hesla a vynucení vícefaktorového ověřování.

Mezi příklady některých způsobů, jak azure identity protection může pomoci zabezpečit vaše účty a identity patří:

[Detekce detekce rizik a rizikových účtů](../../active-directory/identity-protection/overview.md)
-   Detekujte šest typů detekce rizik pomocí strojového učení a heuristických pravidel.
-   Vypočítejte úrovně rizika uživatele.
-   Poskytněte vlastní doporučení ke zlepšení celkového stavu zabezpečení zvýrazněním chyb zabezpečení.

[Zkoumání zjišťování rizik](../../active-directory/identity-protection/overview.md)
-   Odesílat oznámení pro detekci rizik.
-   Prozkoumejte detekci rizik pomocí relevantních a kontextových informací.
-   Poskytněte základní pracovní postupy pro sledování vyšetřování.
-   Poskytněte snadný přístup k nápravným akcím, jako je resetování hesla.

[Zásady podmíněného přístupu založené na rizicích](../../active-directory/identity-protection/overview.md)
-   Zmírňte riskantní přihlášení blokováním přihlášení nebo vyžadováním problémů s vícefaktorovým ověřováním.
-   Blokovat nebo zabezpečit rizikové uživatelské účty.
-   Vyžadovat, aby se uživatelé registrovali pro vícefaktorové ověřování.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Pomocí [správy privilegovaných identit (PIM) služby Azure Active Directory](../../active-directory/privileged-identity-management/pim-configure.md)můžete spravovat, řídit a monitorovat přístup v rámci vaší organizace. Tato funkce zahrnuje přístup k prostředkům ve službě Azure AD a dalších online službách Microsoftu, jako je Office 365 nebo Microsoft Intune.

![Diagram správy privilegovaných identit Azure AD](./media/threat-detection/azure-threat-detection-fig2.png)

PIM vám pomůže:

-   Získejte upozornění a sestavy o správcích Azure AD a přístupu pro správu microsoftu online službám, jako jsou Office 365 a Intune.

-   Získejte sestavy o historii přístupu správce a změnách v přiřazeních správců.

-   Získejte upozornění na přístup k privilegované roli.

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure Monitoru](../../azure-monitor/index.yml) je cloudové řešení pro správu IT od Microsoftu, které vám pomůže spravovat a chránit místní a cloudovou infrastrukturu. Vzhledem k tomu, že protokoly Azure Monitor uplatní jako cloudovou službu, můžete mít zprovoznění a zprovoznění rychle s minimálními investicemi do infrastrukturních služeb. Nové funkce zabezpečení jsou dodávány automaticky, což šetří průběžné náklady na údržbu a upgrade.

Kromě poskytování cenných služeb samostatně, protokoly Azure Monitor můžete integrovat s součástmi System Center, jako je [například System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), rozšířit své stávající investice do správy zabezpečení do cloudu. Protokoly System Center a Azure Monitor můžou spolupracovat a poskytovat tak kompletní hybridní správu.

### <a name="holistic-security-and-compliance-posture"></a>Holistické zabezpečení a dodržování předpisů

[Řídicí panel Zabezpečení a audit služby Log Analytics](../../security-center/security-center-intro.md) poskytuje komplexní přehled o stavu zabezpečení IT vaší organizace s integrovanými vyhledávacími dotazy na významné problémy, které vyžadují vaši pozornost. Řídicí panel zabezpečení a auditování je domovskou obrazovkou pro vše, co souvisí se zabezpečením v protokolech Azure Monitoru. Poskytuje celkový přehled o stavu zabezpečení vašich počítačů. Můžete také zobrazit všechny události za posledních 24 hodin, 7 dní nebo jakýkoli jiný vlastní časový rámec.

Protokoly Azure Monitoru vám pomohou rychle a snadno pochopit celkový stav zabezpečení libovolného prostředí, a to vše v kontextu operací IT, včetně vyhodnocení aktualizací softwaru, antimalwarového hodnocení a směrných plánů konfigurace. Data protokolu zabezpečení jsou snadno přístupná pro zjednodušení procesů auditu zabezpečení a dodržování předpisů.

![Řídicí panel Zabezpečení a audit analýzy protokolů](./media/threat-detection/azure-threat-detection-fig3.jpg)

Řídicí panel Zabezpečení a audit analýzy protokolů je uspořádán do čtyř hlavních kategorií:

-   **Domény zabezpečení**: Umožňuje dále zkoumat záznamy zabezpečení v průběhu času; přístup k hodnocení malwaru; hodnocení aktualizací; zobrazení informací o zabezpečení sítě, identitě a přístupu; zobrazení počítačů s událostmi zabezpečení; a rychle přistupovat k řídicímu panelu Azure Security Center.

-   **Významné problémy**: Umožňuje rychle identifikovat počet aktivních problémů a závažnost problémů.

-   **Detekce (náhled)**: Umožňuje identifikovat vzory útoku zobrazením výstrah zabezpečení, jak k nim dochází proti vašim prostředkům.

-   **Threat Intelligence**: Umožňuje identifikovat vzory útoků zobrazením celkového počtu serverů s odchozím škodlivým přenosem IP adres, typem škodlivé hrozby a mapou umístění IP adres.

-   **Běžné dotazy zabezpečení**: Uvádí nejběžnější dotazy zabezpečení, které můžete použít ke sledování vašeho prostředí. Když vyberete libovolný dotaz, otevře se podokno Hledání a zobrazí výsledky tohoto dotazu.

### <a name="insight-and-analytics"></a>Přehled a analytika
V centru [protokolů Azure Monitor](../../log-analytics/log-analytics-queries.md) je úložiště, které hostuje Azure.

![Diagram přehledů a analýz](./media/threat-detection/azure-threat-detection-fig4.png)

Data do úložiště můžete shromažďovat z připojených zdrojů konfigurací zdrojů dat a přidáním řešení do předplatného.

![Řídicí panel Azure Monitor protokoluje](./media/threat-detection/azure-threat-detection-fig5.png)

Zdroje dat a řešení každý vytvořit samostatné typy záznamů s vlastní sadou vlastností, ale stále můžete analyzovat společně v dotazech do úložiště. Můžete použít stejné nástroje a metody pro práci s různými daty, která jsou shromažďována z různých zdrojů.


Většina vaší interakce s protokoly Azure Monitoru probíhá prostřednictvím portálu Azure, který běží v libovolném prohlížeči a poskytuje vám přístup k nastavení konfigurace a k několika nástrojům pro analýzu a interakci se shromážděnými daty. Z portálu můžete použít:
* [Protokol vyhledávání,](../../log-analytics/log-analytics-queries.md) kde vytvářet dotazy k analýze shromážděných dat.
* [Řídicí panely](../../azure-monitor/learn/tutorial-logs-dashboards.md), které si můžete přizpůsobit pomocí grafického zobrazení nejcennějších vyhledávání.
* [řešení](../../monitoring/monitoring-solutions.md), která poskytují další nástroje pro funkčnost a analýzu.

![Analytické nástroje](./media/threat-detection/azure-threat-detection-fig6.png)

Řešení přidávají funkce do protokolů Azure Monitoru. Primárně běží v cloudu a poskytují analýzu dat, která jsou shromažďována v úložišti analýzy protokolů. Řešení může také definovat nové typy záznamů, které mají být shromažďovány, které lze analyzovat pomocí hledání protokolu nebo pomocí dalšího uživatelského rozhraní, které poskytuje řešení v řídicím panelu analýzy protokolů.

Příkladem těchto typů řešení je řídicí panel Zabezpečení a audit.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatizace a řízení: Výstraha na posuny konfigurace zabezpečení

Azure Automation automatizuje administrativní procesy pomocí runbooků, které jsou založené na PowerShellu a běží v cloudu. Runbooky se mohou provádět také na serveru ve vašem místním datovém centru a mohou spravovat místní prostředky. Azure Automation poskytuje správu konfigurace pomocí konfigurace požadovaného stavu prostředí PowerShell (DSC).

![Diagram automatizace Azure](./media/threat-detection/azure-threat-detection-fig7.png)

Můžete vytvářet a spravovat prostředky DSC, které jsou hostované v Azure, a použít je v cloudových a místních systémech. Tímto způsobem můžete definovat a automaticky vynucovat jejich konfiguraci nebo získat sestavy na drift, abyste zajistili, že konfigurace zabezpečení zůstanou v rámci zásad.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomáhá chránit vaše prostředky Azure. Poskytuje integrované monitorování zabezpečení a správu zásad napříč vašimi předplatnými Azure. V rámci služby můžete definovat police proti předplatným Azure a [skupiny prostředků](../../azure-resource-manager/management/manage-resources-portal.md) pro větší rozlišovací schopnost.

![Diagram Centra zabezpečení Azure](./media/threat-detection/azure-threat-detection-fig8.png)

Výzkumníci v oblasti zabezpečení ze společnosti Microsoft neustále vyhledávají nové hrozby. Mají přístup k rozsáhlé sadě telemetrických údajů získávaných díky globálního prezenci společnosti Microsoft v cloudových i místních prostředích. Tento rozsáhlý a různorodý soubor datových sad umožňuje společnosti Microsoft objevovat nová schémata a trendy útoků v rámci jejích místních produktů pro zákazníky a podniky a rovněž i v rámci online služeb.

Security Center tak může rychle aktualizovat své detekční algoritmy, protože útočníci vydávají nové a stále sofistikovanější exploity. Tento přístup vám pomůže držet krok s rychle se pohybujícím prostředím hrozeb.

![Detekce hrozeb centra zabezpečení](./media/threat-detection/azure-threat-detection-fig9.jpg)

Detekce hrozeb ve službě Security Center funguje tak, že se automaticky shromažďují informace o zabezpečení ze všech prostředků Azure, ze sítě a připojených partnerských řešení. Analyzuje tyto informace, koreluje informace z více zdrojů, aby identifikoval hrozby.

U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit.

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Průlomy v technologiích velkých objemů dat a [strojového učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) se používají k vyhodnocení událostí v celé cloudové struktuře. Pokročilá analytika dokáže detekovat hrozby, které by nebylo možné identifikovat pomocí manuálních přístupů a předpovídání vývoje útoků. Tyto typy analýzy zabezpečení jsou popsány v dalších částech.

### <a name="threat-intelligence"></a>Analýza hrozeb

Společnost Microsoft má přístup k obrovskému množství globálních informací o hrozbách.

Telemetrie proudí z různých zdrojů, jako je Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) a Microsoft Security Response Center (MSRC).

![Zjištění analýzy hrozeb](./media/threat-detection/azure-threat-detection-fig10.jpg)

Výzkumní pracovníci také dostávají informace o informacích o hrozbách, které jsou sdíleny mezi hlavními poskytovateli cloudových služeb, a předplatili informační kanály pro analýzu hrozeb od třetích stran. Služba Azure Security Center vás pomocí těchto informací může upozornit na hrozby známých útočníků. Možné příklady:

-   **Využití výkonu strojového učení**: Azure Security Center má přístup k obrovskému množství dat o aktivitách cloudové sítě, které lze použít k detekci hrozeb zaměřených na vaše nasazení Azure.

-   **Detekce hrubé síly**: Strojové učení se používá k vytvoření historického vzoru pokusů o vzdálený přístup, který umožňuje detekovat útoky hrubou silou proti secure shellu (SSH), protokolu RDP (Remote Desktop Protocol) a portům SQL.

-   **Odchozí Detekce DDoS a botnetu**: Běžným cílem útoků, které cílí na cloudové prostředky, je použít výpočetní sílu těchto prostředků k provádění dalších útoků.

-   **Nové servery behaviorální analýzy a virtuální počítače**: Po ohrožení zabezpečení serveru nebo virtuálního počítače útočníci používají širokou škálu technik pro spuštění škodlivého kódu v tomto systému a zároveň se vyhnou detekci, zajistí trvalost a vyloupou ovládací prvky zabezpečení.

-   **Azure SQL Database Threat Detection**: Detekce hrozeb pro Azure SQL Database, která identifikuje neobvyklé databázové aktivity, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

### <a name="behavioral-analytics"></a>Behaviorální analýza

Behaviorální analýza je technika, která analyzuje a porovnává data se sadou známých schémat. Tato schémata však nepředstavují jednoduché příznaky. Určují se prostřednictvím komplexních algoritmů strojového učení, které se aplikují na rozsáhlé datové sady.

![Zjištění behaviorální analýzy](./media/threat-detection/azure-threat-detection-fig11.jpg)

Vzory jsou také určeny pečlivou analýzou škodlivého chování odbornými analytiky. Azure Security Center můžete použít behaviorální analýzy k identifikaci ohrožených prostředků na základě analýzy protokolů virtuálních strojů, protokoly virtuálních síťových zařízení, protokoly prostředků infrastruktury, výpisy stavu systému a další zdroje.

Kromě toho jsou vzorce korelovány s dalšími signály, aby se zjistily, zda nejsou k dispozici podpůrné důkazy o rozsáhlé kampani. Tato korelace pomáhá identifikovat události, které jsou konzistentní se zavedenými ukazateli ohrožení zabezpečení.

Možné příklady:
-   **Podezřelé spouštění procesů:** Útočníci používají řadu technik k tajnému spouštění škodlivého softwaru. Útočník může například dát malwaru stejné názvy jako legitimní systémové soubory, ale umístit tyto soubory do alternativního umístění, použít název, který je podobný názvu neškodného souboru, nebo maskovat skutečnou příponu souboru. Modely Centra zabezpečení zpracovávají chování a monitorují provádění procesů, aby zjistily odlehlé hodnoty, jako jsou tyto.

-   **Skrytý malware a pokusy o zneužití**: Sofistikovaný malware se může vyhnout tradičním antimalwarovým produktům tím, že nikdy nezapisuje na disk nebo šifruje softwarové komponenty uložené na disku. Takový malware však může být detekován pomocí analýzy paměti, protože malware musí zanechat stopy v paměti, aby fungoval. Pokud dojde k chybě softwaru, ve výpisu stavu systému se zaznamená část paměti v době selhání. Analýzou paměti v výpisu stavu systému Azure centrum zabezpečení dokáže detekovat techniky používané ke zneužití zranitelnosti v softwaru, přístup u důvěrných dat a tajně přetrvávat v kompromitovaném počítači, aniž by to ovlivnilo výkon vašeho počítače. Stroj.

-   **Boční pohyb a vnitřní průzkum**: Chcete-li zachovat v ohrožené síti a lokalizovat a získat cenná data, útočníci se často pokoušejí přesunout bočně z ohroženého počítače k ostatním v rámci stejné sítě. Security Center monitoruje aktivity procesů a přihlášení a zjišťuje pokusy o rozšíření základny útočníka v síti, jako je například vzdálené spuštění příkazu, zjišťování sítě a výčet účtu.

-   **Škodlivé skripty prostředí PowerShell**: PowerShell mohou útočníci používat ke spuštění škodlivého kódu na cílových virtuálních počítačích pro různé účely. Služba Security Center kontroluje aktivitu prostředí PowerShell a hledá známky podezřelé aktivity.

-   **Odchozí útoky:** Útočníci často cílí na cloudové prostředky s cílem využít je k dalším útokům. Ohrožené virtuální počítače mohou být například použity ke spuštění útoků hrubou silou proti jiným virtuálním počítačům, k odesílání nevyžádané pošty nebo skenování otevřených portů a dalších zařízení na internetu. Služba Security Center pomocí strojového učení, které uplatňuje na síťový provoz, dokáže detekovat odchozí síťovou komunikaci vybočující z normy. Když je zjištěn spam, Security Center také koreluje neobvyklý e-mailový provoz s inteligencí z Office 365, aby zjistil, zda je pošta pravděpodobně hanebná nebo výsledek legitimní e-mailové kampaně.

### <a name="anomaly-detection"></a>Detekce anomálií

Služba Azure Security Center také identifikuje hrozby pomocí detekce anomálií. Oproti behaviorální analýze (která závisí na známých schématech odvozovaných z velkých datových sad), je detekce anomálií více „personalizovaná“ a zaměřuje se na standardní hodnoty specifické pro vaše nasazení. Strojové učení se používá k určení normální aktivity pro vaše nasazení a pak jsou generována pravidla k definování mimořádných podmínek, které by mohly představovat událost zabezpečení. Zde naleznete příklad:

-   **Příchozí útoky hrubou silou RDP/SSH**: Vaše nasazení mohou mít zaneprázdněné virtuální počítače s mnoha přihlášeními každý den a dalšími virtuálními počítači, které mají málo přihlášení, pokud existují. Azure Security Center můžete určit základní přihlašovací aktivity pro tyto virtuální počítače a pomocí strojového učení definovat kolem běžné aktivity přihlášení. Pokud dojde k rozporu se směrným plánem definovaným pro charakteristiky související s přihlášením, může být generována výstraha. A strojové učení tu zase určuje, co je významné.

### <a name="continuous-threat-intelligence-monitoring"></a>Průběžné monitorování analýzy hrozeb

Azure Security Center spolupracuje s týmy pro výzkum zabezpečení a datové vědy po celém světě, které průběžně monitorují změny v prostředí hrozeb. To zahrnuje následující iniciativy:

-   **Sledování analýzy hrozeb**: Analýza hrozeb zahrnuje mechanismy, ukazatele, důsledky a užitečné rady o stávajících nebo vznikajících hrozbách. Tyto informace jsou sdíleny v komunitě zabezpečení a společnost Microsoft průběžně monitoruje kanály analýzy hrozeb z interních a externích zdrojů.

-   **Sdílení signálu**: Přehledy od bezpečnostních týmů v širokém portfoliu cloudových a místních služeb, serverů a koncových zařízení klienta společnosti Microsoft jsou sdíleny a analyzovány.

-   **Specialisté na zabezpečení společnosti Microsoft**: Pokračující spolupráce s týmy napříč Microsoftem, které pracují ve specializovaných oblastech zabezpečení, jako je forenzní analýza a detekce webových útoků.

-   **Optimalizace detekce**: Algoritmy jsou spuštěny proti skutečným datovým souborům zákazníků a výzkumní pracovníci zabezpečení spolupracují se zákazníky na ověření výsledků. Pravdivě a falešně pozitivní výsledky pak slouží ke zlepšování algoritmů strojového učení.

Tyto společné úsilí vyvrcholí novými a vylepšenými detekcemi, které můžete okamžitě využít. Není tu žádná akce, kterou bys mohl podniknout.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Pokročilé funkce detekce hrozeb: Další služby Azure

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuální počítače: Microsoft antimalware

[Antimalwarový malware Microsoftu](antimalware.md) pro Azure je řešení pro jednoho agenta pro aplikace a prostředí tenantů, které je navrženo tak, aby běželo na pozadí bez lidského zásahu. Ochranu můžete nasadit na základě potřeb úloh aplikace pomocí základní vlastní konfigurace zabezpečené ve výchozím nastavení nebo pokročilé vlastní konfigurace, včetně antimalwarového monitorování. Azure antimalware je možnost zabezpečení pro virtuální počítače Azure, které se automaticky nainstalují na všechny virtuální počítače Azure PaaS.

#### <a name="microsoft-antimalware-core-features"></a>Základní funkce antimalwaru společnosti Microsoft

Tady jsou funkce Azure, které nasazují a povolují antimalwarový program Microsoft pro vaše aplikace:

-   **Ochrana v reálném čase**: Monitoruje aktivitu v cloudových službách a na virtuálních počítačích, aby detekoval a blokoval spouštění malwaru.

-   **Plánované prohledávání**: Pravidelně provádí cílené prohledávání za účelem detekce malwaru, včetně aktivně spuštěných programů.

-   **Náprava malwaru**: Automaticky působí na zjištěný malware, jako je odstranění nebo umístění škodlivých souborů do karantény a vyčištění škodlivých položek registru.

-   **Aktualizace podpisů**: Automaticky nainstaluje nejnovější podpisy ochrany (definice virů), aby bylo zajištěno, že ochrana je aktuální na předem určené frekvenci.

-   **Aktualizace modulu antimalwaru**: Automaticky aktualizuje modul Microsoft Antimalware Engine.

-   **Aktualizace antimalwarové platformy**: Automaticky aktualizuje antimalwarovou platformu společnosti Microsoft.

-   **Aktivní ochrana**: Hlásí telemetrická metadata o zjištěných hrozbách a podezřelých prostředcích do Microsoft Azure, aby byla zajištěna rychlá reakce na vyvíjející se prostředí hrozeb, což umožňuje doručování synchronních podpisů v reálném čase prostřednictvím systému aktivní ochrany společnosti Microsoft.

-   **Ukázky vykazování**: Poskytuje a hlásí ukázky antimalwarové službě Microsoft, které pomáhají službu upřesnit a povolit řešení potíží.

-   **Vyloučení**: Umožňuje správcům aplikací a služeb konfigurovat určité soubory, procesy a jednotky pro vyloučení z ochrany a skenování z důvodu výkonu a z jiných důvodů.

-   **Kolekce událostí antimalwaru**: Zaznamenává stav antimalwarové služby, podezřelé aktivity a nápravné akce provedené v protokolu událostí operačního systému a shromažďuje je do účtu úložiště Azure zákazníka.

### <a name="azure-sql-database-threat-detection"></a>Azure SQL Database Threat Detection

[Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) je nová funkce inteligence zabezpečení integrovaná do služby Azure SQL Database. Azure SQL Database Threat Detection nepřetržitě pracuje na tom, aby se naučil, profiloval a detekoval neobvyklé databázové aktivity, a identifikuje potenciální hrozby pro databázi.

Bezpečnostní pracovníci nebo jiní určení správci mohou získat okamžité oznámení o podezřelých databázových aktivitách, jakmile k nim dojde. Každé oznámení obsahuje podrobnosti o podezřelé aktivitě a doporučuje, jak dále prozkoumat a zmírnit hrozbu.

V současné době Azure SQL Database Detekce hrozeb detekuje potenciální chyby zabezpečení a útoky injektáže SQL a neobvyklé vzory přístupu k databázi.

Po obdržení e-mailového oznámení o detekci hrozeb mohou uživatelé procházet a prohlížet příslušné záznamy auditu prostřednictvím přímého odkazu v e-mailu. Odkaz otevře prohlížeč auditu nebo předem nakonfigurovanou šablonu aplikace Excel pro auditování, která zobrazuje příslušné záznamy auditu v době podezřelé události, podle následujících následujících položek:

-   Auditovat úložiště pro databázi/server s aktivitami neobvyklé databáze.

-   Příslušná tabulka úložiště auditu, která byla použita v době události k zápisu protokolu auditu.

-   Auditovat záznamy hodiny bezprostředně následující po výskytu události.

-   Auditovat záznamy s podobným ID události v době události (volitelné pro některé detektory).

Detektory hrozeb sql database používají jednu z následujících metod detekce:

-   **Deterministické zjišťování**: Detekuje podezřelé vzory (pravidla založená) v dotazech klienta SQL, které odpovídají známým útokům. Tato metodika má vysokou detekci a nízké falešně pozitivní, ale omezené pokrytí, protože spadá do kategorie "atomové detekce."

-   **Detekce chování**: Detekuje anomální aktivitu, což je abnormální chování v databázi, která nebyla vidět během posledních 30 dnů. Příklady anomální aktivity klienta SQL mohou být špička neúspěšných přihlášení nebo dotazů, velký objem extrahování dat, neobvyklé kanonické dotazy nebo neznámé IP adresy používané pro přístup k databázi.

### <a name="application-gateway-web-application-firewall"></a>Brána firewall webové aplikace brány aplikace

[Brána firewall webových aplikací (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) je funkce [brány Aplikace Azure,](../../application-gateway/application-gateway-web-application-firewall-overview.md) která poskytuje ochranu webovým aplikacím, které používají aplikační bránu pro standardní funkce [řízení doručování aplikací.](https://kemptechnologies.com/in/application-delivery-controllers) Brána firewall webových aplikací to provádí tak, že je chrání před většinou [10 nejčastějších webových chyb zabezpečení projektu Open Web Application Security Project (OWASP).](https://www.owasp.org/index.php/Top_10_2010-Main)

![Diagram brány webových aplikací pro aplikační aplikace](./media/threat-detection/azure-threat-detection-fig13.png)

Ochrany zahrnují:

-   Ochrana proti vstřikování SQL.

-   Ochrana skriptování mezi pracemi.

-   Společná ochrana webových útoků, jako je například vkládání příkazů, pašování požadavků HTTP, rozdělení odpovědi HTTP a útok na vzdálené zahrnutí souborů.

-   Ochrana proti porušení protokolu HTTP.

-   Ochrana proti anomáliím protokolu HTTP, jako je například chybějící hostitelský uživatelský agent a přijímat záhlaví.

-   Prevence proti robotům, prohledávačům a skenerům.

-   Detekce běžných konfigurací aplikace (tj. Apache, IIS a tak dále).

Konfigurace WAF v bráně aplikace poskytuje následující výhody:

-   Chrání webovou aplikaci před webovými chybami zabezpečení a útoky bez úprav back-endového kódu.

-   Chrání více webových aplikací současně za aplikační bránou. Aplikační brána podporuje hostování až 20 webů.

-   Monitoruje webové aplikace proti útokům pomocí sestav v reálném čase, které jsou generovány protokoly WAF aplikační brány.

-   Pomáhá splnit požadavky na dodržování předpisů. Některé kontroly dodržování předpisů vyžadují, aby všechny koncové body směřující k internetu byly chráněny řešením WAF.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Rozhraní API pro detekci anomálií: Vytvořeno pomocí Azure Machine Learning

Rozhraní API detekce anomálií je rozhraní API, které je užitečné pro detekci různých neobvyklých vzorů v datech časových řad. Rozhraní API přiřadí skóre anomálií každému datovému bodu v časových řadách, které lze použít pro generování výstrah, monitorování prostřednictvím řídicích panelů nebo připojení k systémům prodeje lístků.

Rozhraní [API pro detekci anomálií](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) dokáže zjistit následující typy anomálií v datech časových řad:

-   **Špičky a poklesy**: Pokud sledujete počet selhání přihlášení ke službě nebo počet ponechaných pokladny na webu elektronického obchodování, neobvyklé špičky nebo poklesy mohou znamenat útoky zabezpečení nebo přerušení služeb.

-   **Pozitivní a negativní trendy**: Při sledování využití paměti v počítači, zmenšení volné velikosti paměti označuje potenciální nevracení paměti. Pro sledování délky fronty služby může trvalý vzestupný trend znamenat základní problém se softwarem.

-   **Změny úrovně a změny dynamického rozsahu hodnot**: Sledování změn úrovně latencí služby po upgradu služby nebo nižších úrovních výjimek po upgradu může být zajímavé.

Rozhraní API založené na strojovém učení umožňuje:

-   **Flexibilní a robustní detekce**: Modely detekce anomálií umožňují uživatelům konfigurovat nastavení citlivosti a detekovat anomálie mezi sezónními a nesezónními datovými sadami. Uživatelé mohou upravit model detekce anomálií tak, aby rozhraní API pro detekci bylo méně nebo citlivější podle svých potřeb. To by znamenalo detekci méně nebo více viditelných anomálií v datech se sezónními vzory a bez jejich chování.

-   **Škálovatelná a včasná detekce**: Tradiční způsob monitorování se současnými prahovými hodnotami stanovenými znalostmi domén odborníků je nákladný a nelze škálovat na miliony dynamicky se měnících datových sad. Modely detekce anomálií v tomto rozhraní API se učí a modely jsou automaticky vyladěny z historických dat i dat v reálném čase.

-   **Proaktivní a žalovatelné zjišťování**: Pro včasnou detekci anomálií lze použít pomalou detekci trendu a změny úrovně. Časné abnormální signály, které jsou detekovány, mohou být použity k nasměrování lidí k vyšetřování a jednání v problémových oblastech. Kromě toho hlavní příčina analýzy modely a nástroje pro upozorňování lze vyvíjet nad tuto službu detekce anomálií rozhraní API.

Rozhraní API pro detekci anomálií je efektivní a efektivní řešení pro širokou škálu scénářů, jako je stav služby a monitorování klíčových ukazatelů výkonu, IoT, monitorování výkonu a monitorování síťového provozu. Zde jsou některé oblíbené scénáře, kde toto rozhraní API může být užitečné:

- ODDĚLENÍ IT potřebují nástroje ke sledování událostí, kódu chyby, protokolu využití a výkonu (procesor, paměť a tak dále) včas.

-   Weby online obchodu chtějí sledovat aktivity zákazníků, zobrazení stránek, kliknutí a tak dále.

-   Energetické společnosti chtějí sledovat spotřebu vody, plynu, elektřiny a dalších zdrojů.

-   Služby facility nebo správy budov chtějí monitorovat teplotu, vlhkost, provoz a tak dále.

-   Výrobci IoT/výrobci chtějí používat data ze senzorů v časových řadách ke sledování toku práce, kvality a tak dále.

-   Poskytovatelé služeb, jako jsou například centra volání, musí sledovat trend poptávky po službách, objem incidentů, délku fronty čekání a tak dále.

-   Skupiny obchodní analýzy chtějí monitorovat abnormální pohyb obchodních ki (například objem prodeje, nálady zákazníků nebo ceny) v reálném čase.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) je důležitou součástí zásobníku Microsoft Cloud Security. Jedná se o komplexní řešení, které může vaší organizaci pomoci při přechodu plně využít příslib cloudových aplikací. Díky lepšímu přehledu o aktivitě budete mít kontrolu. Zvyšuje také úroveň ochrany důležitých dat napříč cloudovými aplikacemi.

Díky nástrojům, které usnadňují odhalení stínového IT, vyhodnocování rizik, vynucování zásad, prošetřování aktivit a zastavení hrozeb, může vaše organizace bezpečně přejít na cloudové řešení a zároveň si zachovat kontrolu nad důležitými daty.

| | |
|---|---|
| Nenechte si ujít | Odhalte pomocí Cloud App Security stínové IT. Získejte přehled díky zjišťování aplikací, aktivit, uživatelů, dat a souborů ve vašem cloudovém prostředí. Zjišťujte aplikace třetích stran, které jsou připojené k vašemu cloudu.|
|Prověřování | Prošetřete cloudové aplikace pomocí cloudových forenzních nástrojů, které vám umožní zjistit podrobné informace o rizikových aplikacích, konkrétních uživatelích a souborech v síti. Najděte vzorce v datech shromážděných z cloudu. Generujte sestavy pro monitorování cloudu. |
| Řízení | Nastavením zásad a upozornění můžete zmírnit rizika a dosáhnout tak maximální kontroly nad síťovými přenosy v cloudu. Pomocí Cloud App Security můžete svoje uživatele migrovat do bezpečných a schválených alternativních cloudových aplikací. |
| Ochrana | Pomocí cloudového zabezpečení aplikací můžete sankcionovat nebo zakázat aplikace, vynucovat prevenci ztráty dat, řídit oprávnění a sdílení a generovat vlastní sestavy a výstrahy. |
| Řízení | Nastavením zásad a upozornění můžete zmírnit rizika a dosáhnout tak maximální kontroly nad síťovými přenosy v cloudu. Pomocí Cloud App Security můžete svoje uživatele migrovat do bezpečných a schválených alternativních cloudových aplikací. |
| | |


![Diagram zabezpečení cloudových aplikací](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integruje viditelnost s vaším cloudem takto:

-   Pomocí Cloud Discovery můžete mapovat a identifikovat cloudové prostředí a cloudové aplikace, které vaše organizace používá.

-   Sankce a zákaz aplikací ve vašem cloudu.

-   Pomocí snadno nasadit připojit konektory aplikací, které využívají rozhraní API poskytovatele, pro viditelnost a zásady správného řízení aplikací, které se připojíte k.

-   Pomáháme vám mít nepřetržitou kontrolu nastavením a následným neustálým dolaďováním zásad.

Při shromažďování dat z těchto zdrojů na něm cloudapp security spouští propracovanou analýzu. Okamžitě vás upozorní na neobvyklé aktivity a umožní vám podrobně prozkoumat cloudové prostředí. V Cloud App Security můžete nakonfigurovat zásady a používat je k ochraně všechno, co máte ve svém cloudovém prostředí.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Pokročilé funkce detekce hrozeb třetích stran prostřednictvím Azure Marketplace

### <a name="web-application-firewall"></a>Web Application Firewall (Brána firewall webových aplikací)

Brána firewall webových aplikací kontroluje příchozí webový provoz a blokuje injektáže SQL, skriptování mezi weby, nahrávání malwaru, útoky DDoS aplikací a další útoky zaměřené na vaše webové aplikace. Také kontroluje odpovědi z back-endových webových serverů pro prevenci ztráty dat (DLP). Integrovaný modul řízení přístupu umožňuje správcům vytvářet podrobné zásady řízení přístupu pro ověřování, autorizaci a účtování (AAA), což organizacím poskytuje silné ověřování a řízení uživatelů.

Brána firewall webových aplikací poskytuje následující výhody:

-   Detekuje a blokuje sql injekce, skriptování mezi weby, nahrávání malwaru, ddos aplikace nebo jakékoli jiné útoky proti vaší aplikaci.

-   Ověřování a řízení přístupu.

-   Prohledá odchozí provoz, aby zjistil citlivá data, a může maskovat nebo blokovat únik informací.

-   Urychluje doručování obsahu webových aplikací pomocí funkcí, jako je ukládání do mezipaměti, komprese a další optimalizace provozu.

Příklady bran firewall webových aplikací, které jsou k dispozici na azure marketplace, najdete v [tématu Barracuda WAF, Brána firewall virtuální webové aplikace Brocade (vWAF), Imperva SecureSphere a brána firewall IP ThreatSTOP](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Další kroky

- [Reakce na dnešní hrozby](../../security-center/security-center-alerts-overview.md#respond-threats): Pomáhá identifikovat aktivní hrozby, které cílí na vaše prostředky Azure, a poskytuje přehledy, které potřebujete k rychlé reakci.

- [Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): Pomáhá řešit vaše obavy z potenciálních hrozeb pro vaše databáze.
