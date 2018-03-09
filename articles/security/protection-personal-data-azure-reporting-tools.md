---
title: "Zdokumentujte ochrany osobních údajů v Azure, vytváření sestav nástroje | Microsoft Docs"
description: "pomocí Azure služby reporting services a ochrany osobních údajů osobních údajů v iniciativy v souladu s obecné Data Protection nařízení (GDPR)."
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.openlocfilehash: 2c677a1dea001c6a414304fbaa683c3787b355b1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Dokument ochranu osobních údajů v Azure, vytváření sestav nástroje

Tento článek popisuje jak používat Azure služby reporting services a technologií k ochraně osobních údajů osobních údajů. Tyto informace slouží k dosažení souladu s předpisy odvětví nebo státní například obecné Data Protection nařízení (GDPR).
## <a name="scenario"></a>Scénář

Velké výletních společnosti, centrálou ve Spojených státech amerických, je rozšířit jeho operací a nabídnout itineráře v Středomoří, Jaderského a baltský moři, jakož i Britské ostrovy. Chcete-li tyto úsilí, získala menší výletních Víceřádkový na základě v Itálii, Německo, Dánsko a Spojeném království

Společnost používá Microsoft Azure pro zpracování a ukládání podnikových dat. To zahrnuje osobní identifikovatelné údaje, například jména, adresy, telefonních čísel a informace o kreditní kartě z jeho základní globální zákazníka. Ve všech umístěních zahrnuje také tradiční informace lidských zdrojů, jako jsou adresy, telefonních čísel, daň identifikační čísla a další informace o zaměstnance společnosti. Na řádku výletních také udržuje velké databáze potřebu a věrnost program členů, která zahrnuje osobní údaje ke sledování vztahů se zákazníky aktuální a starší.

Zaměstnanci společnosti přístup k síti ze vzdálených pobočkách společnosti a agenty cesta umístěné po celém světě mají přístup k některým prostředkům společnosti.

## <a name="problem-statement"></a>Popis problému

Společnosti musí ochrany osobních údajů zákazníků a zaměstnanců a osobní data prostřednictvím víceúrovňová bezpečnostní strategie, která používá Azure správy a zabezpečení funkce ukládat přísnou kontrolu pro přístup k a zpracování osobních údajů a musí být schopen ukazují jeho ochranná opatření pro vnitřní a vnější auditory.

## <a name="company-goal"></a>Cílem společnosti

V rámci své strategie zabezpečení obrany zabezpečení je cílem společnosti sledovat všechny přístup k a zpracování osobních údajů a zajistěte, aby odpovídající ochrany osobních údajů ochranu osobních údajů naleznete v dokumentaci a vyvíjí.

## <a name="solutions"></a>Řešení

Microsoft Azure nabízí komplexní sledování, protokolování a diagnostiky nástroje ke sledování a záznamů aktivit a událostí souvisejících se přístup k informacím a zpracování osobní údaje, geografické toku dat a jiných výrobců přístup k osobním údajům. Vzhledem k zabezpečení osobních údajů v cloudu je sdílený odpovědnost, Microsoft také poskytuje zákazníkům:

- Podrobné informace o vlastní zpracování dat zákazníků

- Bezpečnostní opatření spravovaný společností Microsoft

- Kde a jak ho odesílá data zákazníků

- Podrobnosti o ochraně osobních údajů společnosti Microsoft vlastní zkontroluje proces

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) je společnosti Microsoft založená na cloudu, víceklientské adresáře a identity management service. Služby přihlášení a funkce vytváření sestav auditování poskytují podrobné přihlášení a informace o aktivitě využití aplikace vám pomohou monitorovat a zajištění řádného přístup k osobním údajům zaměstnanců a zákazníků.

Existují dva typy sestav aktivity:

- [Aktivity sestavy nebo protokoly auditu](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) poskytují podrobné záznamy o aktivity nebo úlohy systému

- [Protokolech sestav aktivity přihlášení](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) se dozvíte, kdo má provedl Každá aktivita uvedených v sestavě auditu

Použití dvou společně, můžete sledovat historii každý úkol provést, a kdo každý provedl. Oba typy sestav jsou přizpůsobitelná a je možné filtrovat.

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>Přístupu protokoly auditu a zabezpečení

Protokoly auditu a zabezpečení je přístupná z portálu služby Active Directory třemi různými způsoby: pomocí **aktivity** části (vyberte buď **protokoly auditu** nebo **přihlášení**), nebo z **uživatelů a skupin** nebo **podnikové aplikace, které** pod **spravovat** ve službě Active Directory. Sestavy lze také přistupovat prostřednictvím Azure Active Directory, vytváření sestav rozhraní API. 

1. Na portálu Azure vyberte **Azure Active Directory.**

2. V **aktivity** vyberte **protokoly auditu.**

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. Přizpůsobení zobrazení seznamu kliknutím **sloupce** na panelu nástrojů.

4.  Vyberte položku v zobrazení seznamu zobrazíte všechny dostupné podrobné informace o něm.

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

Generování sestav Azure Active Directory zahrnuje také dva typy sestav zabezpečení **uživatelé označení příznakem rizik** a **rizikové přihlášení**, který vám může pomoct sledovat potenciální rizika v prostředí Azure.

Další informace o službě generování sestav najdete v tématu [generování sestav Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)

Navštivte [sestav Azure Active Directory aktivity](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports) pro další podrobnosti o sestav, které jsou dostupné v Azure Active Directory. Tento web obsahuje podrobné informace o tom, jak přístup a použít [protokoly auditu sestavy aktivit](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) a [přihlašovací aktivita sestavy](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) na portálu. Také obsahuje informace o [uživatelé označení příznakem rizik](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk) a [rizikové přihlášení](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins) o zabezpečení.

Přejděte [auditování Azure Active Directory referenční dokumentace rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) lokality pro další informace o tom, jak připojit k Azure Directory reporting prostřednictvím kódu programu.

### <a name="log-analytics"></a>Log Analytics

[Analýza protokolu](https://azure.microsoft.com/services/log-analytics/) můžete [shromažďování dat z Azure monitorování](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) korelující s jinými daty a poskytnout další analýzu. Azure monitorování shromažďuje a analyzuje data sledování prostředí Azure. 

Nástrojů pro analýzu v analýzy protokolů například protokol hledání, zobrazení a řešení fungovat na všech shromážděná data vám poskytnou centralizované analýzu celé prostředí. Analýzy protokolů můžete agregovat a analyzovat protokoly událostí systému Windows, protokoly služby IIS a systémové protokoly, které lze zjistit potenciální narušení osobní údaje, které mohla vystavit osobních dat neautorizovaným uživatelům.

#### <a name="how-do-i-use-log-analytics"></a>Použití analýzy protokolů

Analýzy protokolů můžete přistupovat prostřednictvím portálu OMS nebo portálu Azure, z libovolného webového prohlížeče. Log Analytics poskytuje dotazovací jazyk pro rychlé načítání a slučování dat v úložišti. Můžete vytvořit a uložit hledání protokolu přímo analyzovat data v portálu.

Pokud chcete vytvořit pracovní prostor analýzy protokolů na portálu Azure, postupujte takto:

1. Vyberte **analýzy protokolů** ze seznamu služeb v Marketplace.

2. Vyberte **vytvořit,** pak zadejte název pracovní prostor OMS, vyberte předplatné, skupinu prostředků, umístění a cenovou úroveň.

3. Klikněte na tlačítko **OK** k zobrazení seznamu vašich pracovních prostorů.

4. Vyberte pracovní prostor zobrazíte její podrobnosti.

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

Přejděte [analýzy protokolů dokumentaci](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) Další informace o službě.

Přejděte [začít pracovat s pracovní prostor analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started) kurzu vytvořit pracovní prostor služby vyhodnocení a získejte základní informace o tom, jak používat službu.

Navštivte následující webové stránky pro podrobnější informace o tom, jak připojit k použití protokolu Analytics s protokoly popsané výše:

[Zdroje dat protokoly událostí systému Windows v analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Služba IIS přihlásí analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Syslog zdroje dat v analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Protokol činnosti Azure monitorování nebo Azure 

[Azure monitorování](https://azure.microsoft.com/services/monitor/) poskytuje základní úroveň infrastruktura metriky a protokoly pro většinu služby ve službě Microsoft Azure.
Monitorování můžete proniknout hloubkové o aplikací Azure. Azure monitorování spoléhá na rozšíření diagnostiky Azure (Windows nebo Linux) ke shromažďování většina protokoly a metriky na úrovni aplikace. [Protokol činnosti Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) je jeden z prostředků, můžete zobrazit pomocí Azure monitorování. Sleduje každé volání rozhraní API a poskytuje širokou řadu informace o aktivitách, které se vyskytují v [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
Protokol aktivit (dříve označovaný jako provozní nebo protokoly auditu) můžete vyhledat informace o vašem prostředku, jak je vidět infrastruktura Azure. 

I když většinu informace zaznamenané v protokolu aktivit se vztahuje k výkonu a stavu služby, je také informace, které souvisí s ochrany dat. Pomocí protokolu činnosti, můžete určit ", kdo a kdy" pro všechny zápisu operace (PUT, POST, DELETE) na prostředky ve vašem předplatném Azure.

Například poskytuje záznam, když správce odstraní skupinu zabezpečení sítě, což by mohlo mít vliv na ochranu osobních údajů. Položky protokolu aktivit jsou uloženy v Azure monitorování 90 dní.

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>Použití data shromažďovaná společností monitorování Azure?

Existuje několik způsobů, jak používat data v protokolu aktivit a dalším prostředkům Azure monitorování.

- Může Streamovat data do jiných umístění skutečné řádku.

- Data můžete uložit pro delší dobu než výchozí hodnoty, pomocí [účtu úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) a nastavení zásady uchovávání informací.

- Můžete visual data v grafy, pomocí [portál Azure](https://azure.microsoft.com/features/azure-portal/), [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), [Microsoft PowerBI](https://powerbi.microsoft.com/), nebo nástrojů pro vizualizaci třetích stran.

- Můžete dát dotaz na data pomocí monitorování REST API služby Azure, rozhraní příkazového řádku, [prostředí PowerShell](https://docs.microsoft.com/powershell/) rutin, nebo .NET SDK.

Chcete-li začít s monitorováním Azure, vyberte **všechny služby** na portálu Azure.

1. Přejděte dolů k položce **monitorování** v **sledování a správu** části.

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  Monitorování se otevře v **protokol aktivit** zobrazení.

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

Můžete vytvářet a ukládat dotazy pro běžné filtry a pak připnout nejdůležitější dotazy na řídicí panel portálu. Díky tomu se dozvíte o všech výskytech událostí, které splňují příslušná kritéria.

1. Můžete filtrovat zobrazení skupiny prostředků, timespan a kategorie události.

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. Dotazy na portálu řídicí panel můžete připnout pak kliknutím **Pin** tlačítko. To vám pomůže vytvořit jediný zdroj informací pro provozních dat na vaše služby. Název dotazu a počet výsledků, zobrazí se na řídicím panelu.

Monitorování můžete taky zobrazit metriky pro všechny prostředky Azure, konfiguraci nastavení diagnostiky a výstrahy a hledání v protokolu. Další informace o tom, jak používat Azure monitorování a protokol aktivit najdete v tématu [Začínáme s Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

### <a name="azure-diagnostics"></a>Diagnostika Azure 

Možnosti diagnostiky v Azure umožňuje shromažďování dat z různých zdrojů. Protokoly událostí systému Windows, které obsahují protokolu zabezpečení, může být obzvláště užitečný pro sledování a dokumentace ochrany osobních údajů. Protokol zabezpečení sleduje události úspěchy a chyby přihlášení, a také změny oprávnění, detekce vzory označující určité typy útoků, změny zásady zabezpečení, změn členství ve skupinách zabezpečení a mnoho dalšího.

Například 4695 ID události upozorňuje na pokus o unprotection kontrolovatelný chráněných dat. To se vztahují k Data Protection API (DPAPI), která pomáhá chránit data, jako jsou privátní klíče uložené přihlašovací údaje a jiné důvěrné údaje.

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>Jak povolit rozšíření diagnostiky pro virtuální počítače Windows?

Chcete-li povolit rozšíření diagnostiky pro virtuální počítač s Windows, která shromažďovat data protokolu, můžete použít PowerShell. Kroky pro to, jsou závislé na modelu nasazení použijete (Resource Manager nebo Classic). Pokud chcete povolit rozšíření diagnostiky na existující virtuální počítač, který byl vytvořen pomocí modelu nasazení Resource Manager, můžete použít [rutiny prostředí PowerShell Set-AzureRMVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1).

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$diagnosticsconfig_path* je cesta k souboru, který obsahuje konfiguraci diagnostiky v kódu XML. Podrobné pokyny k povolení Azure Diagnostics na virtuálním počítači, najdete v části [pomocí Powershellu povolit v virtuálního počítače se systémem Windows Azure Diagnostics.](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)

Rozšíření diagnostiky Azure může přenést shromážděná data do účtu úložiště Azure nebo odeslat do služby, například Application Insights. Pak můžete data pro auditování.

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>Jak ukládat a zobrazení diagnostických dat?

Je důležité si pamatovat, že diagnostických dat není uložena jako trvale, pokud ho přenést na emulátor úložiště Microsoft Azure nebo do úložiště Azure. K ukládání a zobrazení diagnostických dat ve službě Azure Storage, postupujte takto:

1. Zadejte účet úložiště v souboru ServiceConfiguration.cscfg souboru. Služba objektů Blob nebo služby Table, v závislosti na typu dat, můžete použít Azure Diagnostics. Protokoly událostí systému Windows se ukládají ve formátu tabulky.

2. Přeneste data. Může požádat o přenos diagnostických dat pomocí konfiguračního souboru. SDK 2.4 a předchozí můžete také provést žádost prostřednictvím kódu programu.

3. Zobrazení dat, pomocí [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer), [Průzkumníka serveru](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) v sadě Visual Studio, nebo [Azure Diagnostics Manager](https://www.cerebrata.com/products/azure-diagnostics-manager) v Azure Management Studio.

Další informace o tom, jak provést každý z těchto kroků najdete v tématu [úložiště a zobrazení diagnostických dat ve službě Azure Storage.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)

### <a name="azure-storage-analytics"></a>Azure Storage Analytics 

Analytika úložiště protokoly podrobné informace o úspěšných a nezdařených požadavků a služba úložiště. Tyto informace slouží k monitorování jednotlivých požadavků, které vám můžou pomoct při dokumentace přístup pro osobní data uložená ve službě. Však není povoleno protokolování Storage Analytics ve výchozím nastavení svého účtu úložiště. Můžete ji povolit na portálu Azure.

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>Jak lze nakonfigurovat monitorování pro účet úložiště?

Pokud chcete nakonfigurovat monitorování pro účet úložiště, postupujte takto:

1. Vyberte **účty úložiště** na portálu Azure, pak vyberte název účtu, který chcete sledovat.

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. V **monitorování** vyberte **diagnostiky.**

3.  Vyberte **typ** metriky dat, kterou chcete sledovat pro každou službu (objekt Blob, Table, soubor). Dáte pokyn, aby úložiště Azure se uložit protokolů diagnostiky pro čtení, zápisu a žádosti o odstranění objektů blob, tabulky a fronty služby, vyberte **protokoly objektů Blob, tabulka protokoly** a **protokoly ve frontě.**

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. Pomocí posuvníku v dolní části, nastavte **uchování** zásad ve dnech (hodnota 1 – 365). Výchozí je sedm dní.

5. Vyberte **Uložit** pro aplikaci nastavení konfigurace.

Položky protokolu protokolování úložiště obsahovat tyto informace o jednotlivých požadavků:

- Načasování informace, jako je čas spuštění, začátku do konce latenci a dobu serveru.

- Zadejte podrobnosti o operaci úložiště jako operaci, klíč objektu úložiště klienta je přístup k, úspěch nebo neúspěch a stavový kód HTTP vrácen do klienta.

- Podrobnosti ověřování, například typ ověřování použil příslušný klient.

- Informace o souběžnosti například hodnota ETag a naposledy upravené časové razítko.

- Velikosti zprávy požadavku a odpovědi.

Podrobnější pokyny o tom, jak povolit protokolování Storage Analytics najdete v článku [monitorování účtu úložiště na portálu Azure.](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

### <a name="azure-security-center"></a>Azure Security Center 

[Azure Security Center](https://azure.microsoft.com/services/security-center/) sleduje stav zabezpečení vašich prostředků Azure, aby bylo možné zabránit detekovat hrozby a poskytovat doporučení pro reagovat. Poskytuje několik způsobů, jak pomoci dokumentu vaše bezpečnostní opatření, které ochrany osobních údajů osobních údajů.

Sledování stavu zabezpečení pomáhá zajistit dodržování zásad zabezpečení. Sledování zabezpečení je proaktivní strategie, kdy se auditují vaše prostředky a identifikují ty systémy, které nesplňují organizační standardy nebo osvědčené postupy. Můžete sledovat stav zabezpečení v následujících zdrojích informací:

- Výpočetní (virtuální počítače a cloudové služby)

- Sítě (virtuální sítě)

- Úložiště a data (server a databáze auditování a zjišťování hrozeb, šifrování TDE, šifrování úložiště)

- Aplikace (potenciální potíže se zabezpečením)

Problémy se zabezpečením v některém z těchto kategorií může představovat hrozbu pro ochranu osobních údajů osobní data.

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>Jak lze zobrazit stav zabezpečení prostředků Moje Azure?

Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure. Můžete zobrazit všechny potenciální ohrožení zabezpečení, který ji identifikuje v **prevence** část řídicího panelu.

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. V **prevence** vyberte **výpočetní** dlaždici. Uvidíte zde **přehled,** spolu s **virtuální počítače** seznam všech virtuálních počítačů a stavy jejich zabezpečení a **cloudových služeb** seznam webových a pracovních rolí monitorovat Security Center.

2. Na **přehled** kartě, druhé doporučení zobrazíte další informace.

3. Na **virtuální počítače** , vyberte virtuální počítač k zobrazení dalších podrobností.

Při shromažďování dat je povolené v Azure Security Center, Microsoft Monitoring Agent je automaticky zřízený na všechny stávající a nově podporované virtuální počítače, které jsou nasazeny. Data shromážděná z tohoto agenta je uložen v buď existující [analýzy protokolů](https://azure.microsoft.com/services/log-analytics/) prostoru přidružený k předplatnému nebo nový pracovní prostor.

[Hrozby sestavy Intelligence](https://docs.microsoft.com/azure/security-center/security-center-threat-report) jsou poskytovány Security Center. Tyto poskytují užitečné informace, které pomohou rozpoznat útočníka identitu, cíle, aktuálním a historickém útoku kampaně a svoji, nástroje a postupy, které slouží. Informace o zmírnění a nápravy je rovněž obsažena.

Primárním účelem tyto hrozby sestavy je pomůže efektivně reagovat na okamžitou hrozby a opatření následně k zmírnit problém. Informace v sestavách, může být užitečné také při dokumentu vaší reakcí na incidenty pro generování sestav a auditování účely.

Sestavy Intelligence hrozbu prezentovány v. Formátu PDF, přístup prostřednictvím odkazu ve **sestavy** pole z **podezřelé proces spuštěný** okno pro každou výstrahu zabezpečení v Azure Security Center.

Další informace o tom, jak zobrazit a používat sestavy Intelligence hrozby, naleznete v části [Azure Security Center Threat Intelligence sestavy.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

## <a name="next-steps"></a>Další kroky:

[Začínáme s Azure Active Directory, vytváření sestav rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[Co je služba Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Přehled monitorování v Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Úvod do protokol činnosti Azure (video)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
