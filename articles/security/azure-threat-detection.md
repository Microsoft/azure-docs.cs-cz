---
title: Azure Advanced detekce hrozeb. | Microsoft Docs
description: Další informace o ochrany identit a možnosti.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: eb1bf9f6465a8a94fd2de75f581817887c333289
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="azure-advanced-threat-detection"></a>Azure Advanced Threat detekce
## <a name="introduction"></a>Úvod

### <a name="overview"></a>Přehled

Společnost Microsoft vyvinula řadu dokumenty White Paper, zabezpečení přehledy, osvědčené postupy a kontrolní seznamy pomáhat Azure zákazníků týkající se různé související se zabezpečením možnosti dostupné v a které obaluje platformě Azure. Témata v rozsahu z hlediska spektra a hloubky a jsou pravidelně aktualizovány. Tento dokument je součástí této řady dle souhrnu v následující části abstraktní.

### <a name="azure-platform"></a>Platformy Azure

Azure je platforma služby veřejného cloudu, která podporuje nejširší výběr operačních systémů, programovací jazyky, rozhraní, nástroje, databází a zařízení.
Podporuje následující programovací jazyky:
-   Spusťte Linux kontejnery s integrace Dockeru.
-   Vývoj aplikací pomocí jazyka JavaScript, Python, .NET, PHP, Java a Node.js
-   Sestavení back EndY pro iOS, Android a Windows zařízení.

Služby veřejného cloudu Azure podporují stejné technologie miliony vývojářů a IT profesionály již spoléhají na a vztah důvěryhodnosti.

Když provádíte migraci do veřejného cloudu s organizací, zodpovídá organizace na ochranu dat a zadejte zabezpečení a zásad správného řízení kolem systému.

Infrastruktura Azure je od zařízení až po aplikace navržena tak, aby umožňovala hostování milionů zákazníků současně a poskytovala důvěryhodný základ pro splnění potřeb zabezpečení jednotlivých podniků. Azure nabízí širokou škálu možností konfigurace a vlastní nastavení zabezpečení pro splnění požadavků nasazení aplikací. Tento dokument vám tyto požadavky splňují pomůže.

### <a name="abstract"></a>Abstraktní

Microsoft Azure nabízí integrovanou funkci zjišťování rozšířené hrozba prostřednictvím služeb, jako třeba Azure Active Directory, Azure Operations Management Suite (OMS) a Azure Security Center. Tato kolekce služeb zabezpečení a možnosti poskytuje jednoduchým a rychlým způsobem zjistit, co se děje v rámci Azure nasazení.

Tento dokument vám pomohou "Microsoft Azure blíží" směrem k ohrožení zabezpečení threat diagnostiky a analýzy rizika spojená s škodlivých aktivit, které jsou cílené na servery a dalším prostředkům služby Azure. To umožňuje identifikovat metody správy pro identifikaci a ohrožení zabezpečení v optimalizované řešení zabezpečení službách a technologiích platformy Azure a zákazníkem.

Tento dokument white paper se zaměřuje na technologie platformy Azure a ovládacích prvků zákazníkem a nebude pokoušet o adresu SLA, ceny modely a DevOps postupem aspekty.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

![Azure Active Directory Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) je funkce [Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) edici, která poskytuje přehled o rizikových událostech a potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Microsoft má byla zabezpečení cloudových identit pro přes deset a s Azure AD Identity Protection společnosti Microsoft je zpřístupnění tyto systémy stejné ochrany podnikových zákazníků. Ochrana identit využívá možností detekce anomálií existující Azure AD je k dispozici prostřednictvím [neobvyklé aktivity sestav Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports)a zavádí nové typy událostí rizik, které můžete zjišťovat anomálie v reálném čase.

Služba Identity Protection používá ke zjištění anomálií a rizikových událostí, které by mohly naznačovat napadení identity, adaptivní algoritmy a heuristiku služby Machine Learning. Na základě těchto dat Identity Protection generuje sestavy a výstrahy, které vám umožní prozkoumat tyto události riziko a proveďte odpovídající nápravu nebo zmírnění akce.

Ale Azure Active Directory Identity Protection je větší než nástroj pro monitorování a vytváření sestav. Na základě riziko událostí, vypočítá ochrany identit úroveň rizika uživatele pro každého uživatele, což umožňuje konfigurovat zásady na základě riziko k automatické ochraně identity vaší organizace.

Tyto zásady na základě riziko, kromě jiných [řízení podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) poskytuje služba Azure Active Directory a [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), můžete automaticky blokovat nebo nabízejí adaptivní nápravné akce, které obsahují Resetování hesel a vynucení služby Multi-Factor authentication.

### <a name="identity-protections-capabilities"></a>Možnosti ochrany identit

Azure Active Directory Identity Protection je větší než monitorování a vytváření sestav nástroje. Pokud chcete ochránit identity ve vaší organizaci, můžete nakonfigurovat zásady na základě rizik, které automaticky reagovat na zjištěné problémy, pokud bylo dosaženo úroveň zadaný rizika. Tyto zásady, kromě jiných ovládacích prvků podmíněného přístupu poskytuje Azure Active Directory a EMS, můžete buď automaticky blokovat nebo zahájit adaptivní nápravných akcí, které resetuje včetně heslo a vynucování služby Multi-Factor authentication.

Uvedené příklady některých způsobech, kterými ochrany identit Azure může pomoci zabezpečit vaše účty a identity zahrnují:

[Zjišťování rizikových událostech a rizikové účty:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Zjišťování šest typů událostí rizik pomocí strojového učení a heuristické pravidla
-   Výpočet úrovní rizika uživatele
-   Poskytování vlastních doporučení pro zlepšení celkové postavení zabezpečení podle zvýraznění ohrožení zabezpečení

[Zkoumání rizikových událostí:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Odesílání oznámení o rizikových událostech
-   Zkoumání rizikových událostí pomocí relevantní a kontextové informace
-   Poskytuje základní pracovní postupy pro sledování šetření
-   Poskytuje snadný přístup k nápravné akce, jako je například resetování hesla

[Zásady podmíněného přístupu na základě rizika:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Zásady pro zmírnění rizikové přihlášení blokování přihlášení nebo že vyřeší problémy spojené služby Multi-Factor authentication.
-   Zásady na blokování nebo zabezpečený rizikové uživatelské účty
-   Zásady budou muset uživatelé zaregistrovat u služby Multi-Factor authentication

### <a name="azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM)

S [Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure),

![Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

můžete spravovat, řízení a monitorování přístupu v rámci vaší organizace. To zahrnuje přístup k prostředkům v Azure AD a dalších online službách Microsoftu jako Office 365 nebo Microsoft Intune.

Azure AD Privileged Identity Management vám pomůže:

-   Objeví se výstraha a tvorba sestav o Správci služby Azure AD a "právě v čase" pro správu přístup k Microsoft Online Services, jako je Office 365 a Intune

-   Získání sestavy o historii přístup správce a změny v přiřazení správců

-   Získání výstrahy týkající se přístupu k privilegované role.

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) je společnosti Microsoft založená na cloudu IT řešení správy, které pomáhá spravovat a chránit místní a cloudové infrastruktury. Vzhledem k tomu, že je OMS implementována jako cloudová služba, je možné ji zprovoznit velmi rychle a s minimální investicí do služeb infrastruktury. Nové funkce zabezpečení se dodávají automaticky, ukládání průběžnou údržbu a upgradovat náklady.

Kromě poskytování služeb cenné svoje vlastní OMS může integrovat součástí produktu System Center, jako [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/) rozšířit existující zabezpečení správy investice do cloudu. System Center a OMS mohou díky vzájemné spolupráci poskytnout úplné hybridní prostředí pro správu.

### <a name="holistic-security-and-compliance-posture"></a>Komplexní zabezpečení a dodržování předpisů postavení

[OMS zabezpečení a Audit řídicí panel](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) poskytuje komplexní pohled na vaše organizace IT postavení zabezpečení s integrovaný vyhledávací dotazy pro významné problémy, které vyžadují vaši pozornost. Zabezpečení a Audit řídicí panel je na domovskou obrazovku všem související se zabezpečením v OMS. Poskytuje celkový přehled o stavu zabezpečení vašich počítačů. Umožňuje také zobrazit všechny události za posledních 24 hodin, 7 dní nebo za jakýkoli zadaný interval.

Řídicí panely OMS vám pomohou rychle a snadno porozumíte celkové zabezpečení postavení jakékoli prostředí, všechny v kontextu IT oddělení, včetně: posouzení aktualizace softwaru, antimalwarových a standardní hodnoty konfigurace. Data protokolu zabezpečení je navíc snadno přístupné zefektivnění procesy auditu zabezpečení a dodržování předpisů.

Řídicí panel Zabezpečení a audit v OMS je rozdělen do čtyř hlavních kategorií:

![Řídicí panel Zabezpečení a audit v OMS](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   **Zabezpečení domény:** v této oblasti, bude možné dále prozkoumat záznamy zabezpečení v čase, přístup k posouzením malwaru, aktualizovat hodnocení, zabezpečení sítě, informace o přístupu a identit, počítače s událostmi zabezpečení a rychle mít přístup k řídicímu panelu Azure Security Center.

-   **Významné problémy:** tato možnost vám umožňuje rychle zjistit počet aktivní problémy a závažnost těchto problémů.

-   **Detekce (Preview):** umožňuje identifikaci vzorů útoků vizualizací výstrahy zabezpečení, která je uloženo na vaše prostředky.

-   **Analýzou hrozeb:** umožňuje identifikaci vzorů útoků vizualizací celkový počet servery s odchozím škodlivým provozem IP, typu zjištění ohrožení a mapu, která ukazuje, kde jsou tyto IP adresy pocházejících z.

-   **Běžné dotazy zabezpečení:** této možnosti získáte seznam nejčastějších dotazů zabezpečení, které můžete použít k monitorování prostředí. Po kliknutí na jednu z těchto dotazů, otevře se okno pro vyhledávání ve výsledcích pro tento dotaz.

### <a name="insight-and-analytics"></a>Statistiky a analýza
Ve středu [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) je OMS úložiště, který je hostován v cloudu Azure.

![Statistiky a analýza](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Data se do úložiště shromažďují z připojených zdrojů tak, že se konfigurují zdroje dat a přidávají řešení do vašeho předplatného.

![předplatné](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Zdroje dat a řešení vytvářejí různé typy záznamů, které mají vlastní sady vlastností, ale dají se přesto analyzovat společně v dotazech zasílaných do úložiště. To vám umožňuje používat stejné nástroje a metody pro práci s různými druhy dat shromážděných různými prostředky.


Většina interakce se analýzy protokolů je prostřednictvím portálu OMS, který běží v libovolného prohlížeče a poskytuje vám přístup k nastavení konfigurace a několik nástrojů k analýze a fungovat na shromážděná data. Z portálu, můžete použít [protokolu hledání](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) tam, kde vytvoříte dotazy k analýze shromážděných dat [řídicí panely](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards), které můžete přizpůsobit pomocí grafické zobrazení nejcennější hledání a [řešení](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), které poskytují další funkce a analytických nástrojích.

![nástrojů pro analýzu](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Řešení do služby Log Analytics přidávají funkčnost. Primárně běží v cloudu a poskytují analýzu dat shromážděných v úložišti OMS. Můžou také definovat nové typy záznamů, které se mají shromáždit a které se dají analyzovat pomocí hledání v protokolu nebo prostřednictvím dalšího uživatelského rozhraní poskytnutého řešením v řídicím panelu OMS.
Zabezpečení a Audit je příkladem těchto typů řešení.



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automatizace a řízení: drifts výstrah Konfigurace zabezpečení

Automatizace Azure umožňuje automatizovat procesy správy s sady runbook, které jsou založené na prostředí PowerShell a spusťte v cloudu Azure. Runbooky se mohou provádět také na serveru ve vašem místním datovém centru a mohou spravovat místní prostředky. Azure Automation nabízí správy konfigurací pomocí prostředí PowerShell DSC (Konfigurace požadovaného stavu).

![Azure Automation](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Můžete vytvořit a spravovat prostředky DSC hostované v Azure a použít je do cloudu a místních systémech definovat a automaticky vynutit jejich konfigurace nebo získat sestavy o odlišily pomohou zajistit, že konfigurace zabezpečení zůstat v rámci zásad.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomáhá chránit prostředky v Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure. V rámci služby, budete moci definovat zásady nejen pro svá předplatná Azure, ale i proti [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), aby mohli být podrobnější.

![Azure Security Center](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Výzkumníci v oblasti zabezpečení ze společnosti Microsoft neustále vyhledávají nové hrozby. Mají přístup k rozsáhlé sadě telemetrických údajů získávaných díky globálního prezenci společnosti Microsoft v cloudových i místních prostředích. Tento rozsáhlý a různorodý soubor datových sad umožňuje společnosti Microsoft objevovat nová schémata a trendy útoků v rámci jejích místních produktů pro zákazníky a podniky a rovněž i v rámci online služeb.

Proto Security Center můžete rychle aktualizovat své detekční algoritmy jako útočníci vydání nové a stále sofistikované zneužití. Tento přístup umožňuje držet krok s prostředím přesunutí fast hrozeb.

![Security Center](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

Detekce hrozeb ve službě Security Center funguje tak, že se automaticky shromažďují informace o zabezpečení ze všech prostředků Azure, ze sítě a připojených partnerských řešení.  Analyzuje tyto informace korelace informace z více zdrojů, k identifikaci hrozeb.
U výstrah zabezpečení se ve službě Security Center stanoví priority spolu s doporučením, jak danou hrozbu vyřešit.

Služba Security Center využívá pokročilou analýzu zabezpečení, která daleko překračuje možnosti detekce založené na signaturách či příznacích. Změnám ve velkých objemů dat a [strojového učení](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologie, které se používají k vyhodnocení události napříč celou cloudových prostředků infrastruktury – zjišťování hrozeb, které by bylo možné zjistit pomocí ruční přístupy a predikci vývoj útoky. Tyto analýzy zabezpečení zahrnuje následující položky.

### <a name="threat-intelligence"></a>Analýza hrozeb

Společnost Microsoft má k dispozici rozsáhlé zdroje globální analýzy hrozeb.
Telemetrická data proudí z více zdrojů, například Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft digitální činů jednotky (DCU) a Microsoft Security Response Center (MSRC).

![Analýza hrozeb](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Výzkumní pracovníci také od třetích stran dostávají informace z analýzy hrozeb, které se sdílí mezi většinou poskytovatelů a předplatitelů cloudových služeb do kanálů analýzy hrozeb. Služba Azure Security Center vás pomocí těchto informací může upozornit na hrozby známých útočníků. Možné příklady:

-   **Využití Power Machine Learning -** Azure Security Center má přístup k velká množství dat o síti aktivita v cloudu, který můžete použít k detekci hrozeb cílení na vaše nasazení Azure. Příklad:

-   **Útok hrubou silou detekce -** Machine learning se používá k vytvoření historických vzor pokusů vzdálený přístup, umožňující zjistit útoky hrubou silou na porty SSH, RDP a SQL.

-   **Odchozí DDoS a detekce Botnet** -běžné cílem útoků cílení na cloudové prostředky je použít výpočetní výkon těchto prostředků k provedení jiným útokům.

-   **Nové chování Analytics serverů a virtuálních počítačů -** po ohrožení server nebo virtuální počítač, útočníci využívají širokou škálu techniky provést škodlivý kód v tomto systému při vyloučení detekce, zajištění trvalosti a spravovatelný ovládací prvky zabezpečení.

-   **Detekce hrozeb databáze Azure SQL -** detekce hrozeb pro databázi SQL Azure, které identifikuje nezvyklé databázové aktivity, které indikují neobvyklou a potenciálně škodlivé pokusí o přístup k nebo zneužití databáze.

### <a name="behavioral-analytics"></a>Behaviorální analýza

Behaviorální analýza je technika, která analyzuje a porovnává data se sadou známých schémat. Tato schémata však nepředstavují jednoduché příznaky. Určují se prostřednictvím komplexních algoritmů strojového učení, které se aplikují na rozsáhlé datové sady.

![Behaviorální analýza](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


Určují se také prostřednictvím pečlivé analýzy škodlivého chování, kterou provádí zkušení analytici. Azure Security Center slouží k identifikaci ohroženými prostředky na základě analýzy protokolů virtuálního počítače, protokolů zařízení virtuální sítě, protokoly prostředků infrastruktury, výpisy stavu systému a další zdroje pro vypracování analýzy chování.

Kromě toho se pomocí vzájemné souvislosti s dalšími signály hledají podpůrné důkazy rozšířené kampaně. Tato korelace pomáhá identifikovat události, které jsou konzistentní se zavedenými ukazateli ohrožení zabezpečení.

Možné příklady:
-   **Provádění podezřelé procesu:** útočníci využít několik postupů k spuštění škodlivého softwaru bez detekce. Útočník může například udělit malwaru stejné názvy jako soubory legitimní systému, ale tyto soubory umístit do alternativního umístění, použijte název, který se velmi podobně jako soubor neškodné nebo maskování true příponu souboru. Modely služby Security Center zpracovávají chování a monitorují spouštění procesů s cílem odhalit právě takovéto mimořádné hodnoty.

-   **Skrytý malwaru a jeho zneužití pokusů:** sofistikované malwaru můžete obejít tradiční antimalwarové produkty nikdy zápis na disk nebo šifrování softwarové komponenty, které jsou uložená na disku. Však takové malwaru lze zjistit pomocí analýza paměti jako malware musí zůstat trasování v paměti funkce. Pokud dojde k chybě softwaru, ve výpisu stavu systému se zaznamená část paměti v době selhání. Analýzou paměť v stav systému, Azure Security Center může rozpoznat postupy používané k zneužívají ohrožení zabezpečení v softwaru, přístup k důvěrných dat a tajně zachovat v rámci ohroženého počítače bez dopadu na výkon vaší počítač.

-   **Pomoci odhalit laterální pohyb a interní rekognoskace:** pro uchování v ohrožení zabezpečení sítě a vyhledejte/sklizně cenných dat, útočníci často usilují o následně k laterálnímu pohybu z ohroženého počítače k ostatním v rámci stejné síti. Security Center monitoruje aktivity procesu a přihlaste se ke zjištění pokusy o rozbalte útočníka dostane v rámci sítě, jako je vzdálené spouštění příkazů, zjišťování sítě a účtů.

-   **Škodlivých skriptů prostředí PowerShell:** prostředí PowerShell můžete použít útočníků ke spuštění škodlivého softwaru na cílových virtuálních počítačů pro různé účely. Služba Security Center kontroluje aktivitu prostředí PowerShell a hledá známky podezřelé aktivity.

-   **Odchozí útoků:** útočníci často cíle cloudových prostředků s cílem pomocí těchto prostředků připojit další útoky. Ohrožené virtuální počítače, může například použít k spuštění útoky hrubou silou na jiné virtuální počítače, odesílání nevyžádané pošty nebo skenování otevřené porty a jinými zařízeními v síti Internet. Služba Security Center pomocí strojového učení, které uplatňuje na síťový provoz, dokáže detekovat odchozí síťovou komunikaci vybočující z normy. Při zasílání nevyžádané pošty, Security Center také korelaci provoz neobvyklou e-mailu s intelligence z Office 365 k určení, zda je e-mailu pravděpodobně nefarious nebo výsledek legitimní e-mailové kampaně.

### <a name="anomaly-detection"></a>Detekce anomálií

Služba Azure Security Center také identifikuje hrozby pomocí detekce anomálií. Oproti behaviorální analýze (která závisí na známých schématech odvozovaných z velkých datových sad), je detekce anomálií více „personalizovaná“ a zaměřuje se na standardní hodnoty specifické pro vaše nasazení. Pomocí strojového učení se určí běžné úrovně aktivity pro vaše nasazení a poté se vygenerují pravidla definující neobvyklé hodnoty, které by mohly představovat událost zabezpečení. Zde naleznete příklad:

-   **Příchozí připojení RDP/SSH útoky hrubou silou:** vaše nasazení může mít zaneprázdněn virtuálních počítačů s mnoha přihlášení každý den a dalších virtuálních počítačů, které mají několik nebo všechny přihlášení. Azure Security Center můžete určit činnost přihlášení standardních hodnot pro tyto virtuální počítače a použít strojového učení k definování kolem aktivity normální přihlášení. Pokud je rozdíl oproti směrného plánu definované pro související s přihlášením charakteristiky, pak může být vygenerována výstraha. A strojové učení tu zase určuje, co je významné.

### <a name="continuous-threat-intelligence-monitoring"></a>Analýzou hrozeb nepřetržité monitorování

Azure Security Center funguje s zabezpečení dat vědecké účely týmy pro výzkum a po celém světě které neustále monitorovat změny v povahu hrozeb. To zahrnuje následující iniciativy:

-   **Monitorování intelligence Threat:** Threat intelligence zahrnuje mechanismy, ukazatelů, dopad a řešitelné Rady o existující nebo vznikající hrozby. Tyto informace se sdílí v bezpečnostní komunitě a společnost Microsoft kanály analýzy hrozeb z interních i externích zdrojů nepřetržitě monitoruje.

-   **Signál sdílení:** přehled o zabezpečení týmy ve společnosti Microsoft široké portfolio cloudové a místní služby, serverů a klientů endpoint zařízení jsou sdíleny a analyzovat.

-   **Zabezpečení odborníky společnosti Microsoft:** probíhající zapojení týmy ve Microsoft, které fungují v polích specializované zabezpečení, jako je forenzních a webové detekce útoku.

-   **Detekce ladění:** algoritmy, které se spouští nad skutečné zákazníka datových sad a výzkumných pracovníků zabezpečení pracovat s zákazníkům ověřit výsledky. Pravdivě a falešně pozitivní výsledky pak slouží ke zlepšování algoritmů strojového učení.

Toto celkové úsilí přineslo nové a vylepšené způsoby detekce, ze kterých budete mít okamžitý prospěch – a nemusíte sami nic dělat.

## <a name="advanced-threat-detection-features---other-azure-services"></a>Funkce zjišťování Advanced Threat – jinými službami Azure

### <a name="virtual-machine-microsoft-antimalware"></a>Virtuálního počítače: Antimalware od Microsoftu

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pro Azure je řešení jednoho agenta pro aplikace a klienta prostředí, navržená tak, aby spuštěný na pozadí bez lidského zásahu. Můžete nasadit ochranu na základě potřeb vašich zatížení aplikací, s buď základní zabezpečení výchozím nebo advanced vlastní konfigurace, včetně monitorování proti malwaru. Azure antimalwarových je možnost zabezpečení pro virtuální počítače Azure a je automaticky nainstalován na všech virtuálních počítačích Azure PaaS.

**Funkce Azure k nasazení a povolit Antimalware od Microsoftu pro vaše aplikace**

#### <a name="microsoft-antimalware-core-features"></a>Microsoft Antimalware klíčových funkcí

-   **Ochrana v reálném čase -** monitoruje aktivity v cloudové služby a na virtuálních počítačích, na zjištění a blokování malwaru provádění.

-   **Naplánované prohledávání -** pravidelně provádí cílové kontrolu ke zjištění malwaru, včetně aktivně spouštění programů.

-   **Malwarové nápravy -** automaticky provádět akce se zjištěným malwarem, jako je například odstranit nebo umístění do karantény škodlivé soubory a čištění položky škodlivé registru.

-   **Aktualizace podpisu -** automaticky nainstaluje nejnovější podpisy ochrany (definice virů) je aktuální na předem určené frekvenci ochrany.

-   **Aktualizace antimalwarový stroj –** automaticky aktualizuje modul Antimalware od Microsoftu.

-   **Aktualizace platformy antimalwarových –** automaticky aktualizuje platformou Antimalware od Microsoftu.

-   **Aktivní ochranu -** sestavy telemetrie metadata o zjištěných hrozeb a podezřelé prostředky k Microsoft Azure a zajistit rychlou reakci na vyvíjející se hrozba šířku a povolení v reálném čase synchronní podpis doručení prostřednictvím Microsoft Active Protection systém (MAPS).

-   **Ukázky vytváření sestav -** poskytuje a sestavy ukázky pro službu Microsoft Antimalware pomohou Upřesnit, služby a povolte řešení potíží.

-   **Vyloučení –** umožňuje aplikace a správců služeb ke konfiguraci určitých souborů a procesy a jednotky je vyloučit z ochrany a kontrolu výkonu nebo z jiných důvodů.

-   **Shromažďování událostí antimalwarových -** zaznamenává stav antimalwarové služby, podezřelé aktivity a nápravné akce prováděné v protokolu událostí operačního systému a shromažďuje je do účtu úložiště Azure zákazníka.

### <a name="azure-sql-database-threat-detection"></a>Detekce hrozeb databáze Azure SQL

[Služba detekce hrozeb databáze Azure SQL](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) je nová funkce intelligence zabezpečení vytvořené ve službě Azure SQL Database. Práce se po celý den na další, profil a zjišťovat nezvyklé databázové aktivity, databáze SQL Azure služba detekce hrozeb identifikuje potenciální ohrožení databáze.

Osoby zabezpečení nebo jiné určený správce můžete získat okamžité odeslání oznámení o aktivitách podezřelé databáze, kdy k nim dojde. Jednotlivá oznámení najdete podrobnosti podezřelé aktivity a doporučuje postup dál prozkoumat a zmírnit riziko.

Detekce hrozeb databáze SQL Azure v současné době zjistí potenciální ohrožení zabezpečení a prostřednictvím injektáže SQL a vzory přístupu k databázi neobvyklé.

Po přijetí e-mailové oznámení detekce hrozeb, jsou uživatelé zobrazit záznamy relevantní auditu pomocí přímý odkaz v e-mailu, které se otevře prohlížeč formátu auditu a přejděte nebo předkonfigurované auditování šablony aplikace Excel, která zobrazuje záznamy relevantní auditu kolem čas podezřelé události podle následující:
-   Úložiště auditu pro databázi nebo server s nezvyklé databázové aktivity

-   Relevantní auditu úložiště tabulku, která byla použita v době události zapsat protokol auditu

-   Audit záznamy následujících hodin vzhledem k tomu, že dojde k události.

-   Záznamy auditu s podobnou ID události v době události (pro některé detektory volitelné)

Detektory Threat databáze SQL použijte jednu z následujících metod zjišťování:

-   **Deterministickou detekci –** zjistí podezřelou vzory (na základě pravidel) v dotazech klienta SQL, které odpovídají známé útoky. Tato metoda má vysokou detekce a nízkou falešně pozitivní, ale omezené pokrytí, protože spadají do kategorie "atomic detekce".

-   **Chování detekce –** vad neobvyklé aktivity, která je neobvyklé chování pro databázi, která nebyla během posledních 30 dnů.  Příklad SQL klienta neobvyklé aktivity může být Špička neúspěšných přihlášení nebo dotazy, velký objem dat, které se extrahují, neobvyklé kanonický dotazy a neznámého adresy IP použité pro přístup k databázi

### <a name="application-gateway-web-application-firewall"></a>Brány Firewall webových aplikací Application Gateway

[Web Application Firewall](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) je funkce [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) , který poskytuje ochranu k webovým aplikacím, které používají aplikační brány pro standardní [řízení doručení aplikace](https://kemptechnologies.com/in/application-delivery-controllers)funkce. Brány firewall webových aplikací dosahuje tím, že jejich ochrany proti většinu [OWASP top 10 známých chyb zabezpečení webové](https://www.owasp.org/index.php/Top_10_2010-Main)

![Firewally aplikace brány webové aplikace](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   Ochrana před útoky prostřednictvím injektáže SQL.

-   Ochrana před skriptováním mezi weby.

-   Ochrana před běžnými webovými útoky, jako je například injektáž příkazů, pronášení požadavků HTTP, rozdělování odpovědí protokolu HTTP a útok pomocí vložení vzdáleného souboru.

-   Ochrana před narušením protokolu HTTP.

-   Ochrana před anomáliemi protokolu HTTP, jako například chybějící údaj user-agent hostitele nebo hlavičky Accept.

-   Ochrana před roboty, prohledávacími moduly a skenery.

-   Detekce časté nesprávné konfigurace aplikace (tedy Apache, IIS, atd.)

Vám konfigurace firewall webových aplikací na Application Gateway poskytuje následující výhody:

-   Ochráníte své webové aplikace před webovými chybami zabezpečení a útoky bez nutnosti upravovat back-endový kód.

-   Ochráníte více webových aplikací současně za službou Application Gateway. Služba Application Gateway podporuje za jednou bránou hostování až 20 webů, které můžete ochránit před webovými útoky.

-   Můžete monitorovat útoky na své webové aplikace pomocí sestavy vygenerované v reálném čase z protokolů WAF služby Application Gateway.

-   Některé kontroly dodržování předpisů vyžadují, aby všechny internetové koncové body byly chráněné řešením WAF. Používáním služby Application Gateway s povoleným WAF můžete splnit tyto požadavky dodržování předpisů.

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>Detekce anomálií – rozhraní API vytvořené s nástroji Azure Machine Learning

Detekce anomálií je rozhraní API vytvořené s nástroji Azure Machine Learning, které jsou užitečné pro různé typy neobvyklé vzory zjišťování v datové řady čas. Rozhraní API přiřadí anomálií skóre pro každý datový bod v časové řady, který můžete použít pro generování výstrahy, monitorování pomocí řídicích panelů nebo připojení se všemi systémy lístků.

[API detekce anomálií](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) může zjistit následující typy anomálií na data řady čas:

-   **Špičky a klesne:** například při monitorování počet neúspěšných přihlášení ke službě nebo počet rezervace web elektronického obchodu,, neobvyklé špičky vyhrazené IP adresy může znamenat útoky na zabezpečení nebo přerušení služeb.

-   **Kladné a záporné trendy:** při monitorování využití paměti v oblasti výpočetních, například zmenšit velikost volné paměti je naznačuje výslednou potenciální nevrácená paměť systému; při monitorování délka fronty služby, může znamenat trvalé vzestupný trend základní problém softwaru.

-   **Úroveň změny a změny v dynamické rozsahu hodnot:** například změní úroveň v latenci služby po upgradu služby nebo nižší úrovně výjimek po upgradu může být zajímavé pro monitorování.

Machine learning na základě rozhraní API umožňuje:

-   **Flexibilní a robustní detekce:** modely detekce anomálií umožňují uživatelům konfigurovat nastavení velkých a malých písmen a zjišťovat anomálie mezi sezónní a sezónní datovými sadami. Uživatele můžete upravit modelem detekce anomálií, abyste zjišťování rozhraní API menší nebo velká a malá písmena podle jejich potřeb. To by znamenalo, detekce anomálií viditelné menší nebo v datech a bez sezónní vzory.

-   **Škálovatelná a včas detekce:** tradičním způsobem, jakým monitorování se nachází prahové hodnoty nastavit znalostmi odborníků domény jsou nákladná a není škálovatelné na miliony Dynamická změna datových sad. Modely detekce anomálií v tomto rozhraní API se naučili a modely jsou automaticky přizpůsobená z dat historická i v reálném čase.

-   **Proaktivní a možné použít zjišťování:** pomalé trendu a detekce úrovně změn je možné použít pro včasné detekce anomálií. Časná neobvyklé signály zjistil umožňuje přímé člověka ke zkoumání a fungují v problémových oblastí.  Kromě toho příčina analysis modely a výstrah nástroje mohou být vytvořeny na základě této detekce anomálií rozhraní API služby.

Detekce anomálií rozhraní API je efektivní a efektivní řešení pro širokou škálu scénářů, jako je stav služby & klíčového ukazatele výkonu monitorování, IoT, monitorování výkonu a sledování síťových přenosů. Tady je několik oblíbených scénářů, kde může být užitečné toto rozhraní API:
- IT oddělení potřebují nástroje pro sledování událostí, kód chyby, protokolu využití a výkonu (procesoru, paměti a tak dále) v časovém limitu.

-   Online obchodu lokality chcete sledovat aktivity zákazníka, zobrazení stránek, kliknutí a tak dále.

-   Nástroj společnosti chtějí sledování spotřeby horních, plynu, elektrické energie a dalším prostředkům.

-   Služby pro zařízení nebo vytváření chcete monitorovat teploty, vlhkosti, provozu a tak dále.

-   IoT/výrobců chcete používat data snímačů v časové řady monitorování pracovní postup, kvality a tak dále.

-   Poskytovatelé služeb, jako je například telefonní centra nutné monitorování služby vyžádání trendu, objem incidentů, počkejte délka fronty a tak dále.

-   Obchodní analýza skupiny chcete sledovat klíčové ukazatele výkonu. (například prodejní svazek chráněny zákazníka, ceny) neobvyklé pohyb v reálném čase.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) je zásadní součástí zásobníku Microsoft Cloud Security. Je komplexní řešení, které mohou pomoci vaší organizace, když přesouváte plně využít výhod potenciálu cloudových aplikací, ale mějte řízení prostřednictvím lepší přehled o aktivity. Také pomáhá zvýšit ochranu důležitých dat napříč cloudovými aplikacemi.

S nástroji, které pomáhají možnost odhalit stínové IT, vyhodnocování rizik, vynucování zásad, prozkoumat aktivit a zastavení hrozeb vaší organizace můžete více bezpečně přesunout do cloudu a současně zachovat kontrolu nad důležitými daty.

<table style="width:100%">
 <tr>
   <td>Informace</td>
   <td>Možnost odhalit stínové IT s Cloud App Security. Získejte potřebný přehled díky zjišťování aplikací, aktivit, uživatelů, dat a souborů ve vašem cloudovém prostředí. Zjistěte aplikacím třetích stran, které jsou připojené k vašemu cloudu.</td>
 </tr>
 <tr>
   <td>Prozkoumat</td>
   <td>Prozkoumejte cloudových aplikací pomocí forenzních cloudových nástrojů se můžete ponořit přímým do rizikových aplikací, konkrétní uživatele a soubory v síti. Najít vzorů v datech shromážděných z vašeho cloudu. Generování sestav pro monitorování vašeho cloudu.</td>

 </tr>
 <tr>
   <td>Řízení</td>
   <td>Zmírnění rizik pomocí nastavení zásad a výstrahy k dosažení maximální kontroly nad síťovými přenosy v cloudu. Pomocí Cloud App Security svoje uživatele migrovat do alternativních bezpečné, schválené cloudových aplikací.</td>

 </tr>
 <tr>
   <td>Ochrana</td>
   <td>Pomocí Cloud App Security schválit nebo aplikacím, vynucovat ochranu před únikem dat, řízení oprávnění a sdílení a generovat vlastní sestavy a výstrahy.</td>

 </tr>
 <tr>
   <td>Řízení</td>
   <td>Zmírnění rizik pomocí nastavení zásad a výstrahy k dosažení maximální kontroly nad síťovými přenosy v cloudu. Pomocí Cloud App Security svoje uživatele migrovat do alternativních bezpečné, schválené cloudových aplikací.</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integruje s vaším cloudovým podle viditelnost

-   Využívání Cloud Discovery k namapování a identifikování vašeho cloudového prostředí a cloudových aplikací vaše organizace používá.


-   Schvalování a zákazu aplikací ve vašem cloudu.



-   Použití konektorů snadno nasadit aplikace, které využívají poskytovatele rozhraní API, viditelnost a zásady správného řízení aplikací, které se připojují k.

-   Vám pomáhá průběžné řízení podle nastavení a pak průběžně doladění, zásady.

Cloud App Security na shromažďování dat z těchto zdrojů, spustí pokročilé analýzy na data. Ji okamžitě vás upozorní na neobvyklé aktivity a dává vám podrobně je prozkoumat do vašeho cloudového prostředí. Můžete nakonfigurovat zásadu v Cloud App Security a použít k ochraně všechno, co ve vašem cloudovém prostředí.

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Možnosti ATD třetích stran prostřednictvím Azure Marketplace

### <a name="web-application-firewall"></a>Web Application Firewall (Brána firewall webových aplikací)

Brány Firewall webových aplikací kontroluje příchozí webové přenosy a bloky vložení SQL skriptování mezi servery, malwaru nahrávání & aplikace DDoS a jiným útokům zaměřený na vaše webové aplikace. Je také zkontroluje, zda obsahuje odpovědi ze serveru back endové webové pro prevenci ztráty dat (DLP). Modul řízení integrované přístupu umožňuje správcům vytvořit zásady řízení granulární přístupu pro ověřování, autorizace a monitorování (AAA), která umožňuje organizacím silné ověřování a uživatelského ovládacího prvku.

**Označuje:**
-   Zjišťuje a blokuje SQL injekce, skriptování mezi, nahrávání malware, aplikace DDoS nebo jiným útokům proti vaší aplikace.

-   Ověřování a řízení přístupu.

-   Kontroluje odchozí provoz zjistit citlivá data a můžete maskování nebo blokovat informace z úniku.

-   Zrychluje doručování obsahu webové aplikace, pomocí funkcí, jako je ukládání do mezipaměti, kompresi a další optimalizace provoz.

Příklad brány firewall webových aplikací v Azure Marketplace k dispozici jsou následující:

[Brány Firewall webových aplikací barracuda, Brocade virtuální brány Firewall webových aplikací (Brocade vWAF), SecureSphere společnosti Imperva a brány Firewall ThreatSTOP IP.](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>Další kroky

- [Možnosti detekce v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Funkce Rozšířené zjišťování Azure Security Center pomáhá identifikovat active hrozeb cílení na vaše prostředky Microsoft Azure a poskytuje přehled potřeby rychle reagovat.

- [Detekce hrozeb databáze Azure SQL](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Služba detekce hrozeb databáze Azure SQL pomohly vyřešit jejich pochybnostmi potenciální hrozby do své databáze.
