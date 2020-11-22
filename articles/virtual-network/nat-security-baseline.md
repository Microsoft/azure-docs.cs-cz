---
title: Základní hodnoty zabezpečení Azure pro Virtual Network NAT
description: Základní hodnoty zabezpečení služby Virtual Network NAT poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4ab71138f110df19ec84fa8707b480ad1bc6e72c
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255141"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Základní hodnoty zabezpečení Azure pro Virtual Network NAT

Tato základní hodnota zabezpečení platí pro Microsoft Virtual Network NAT pokyny od [verze Azure Security test 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview-v1) . Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure.
Obsah je seskupen podle **ovládacích prvků zabezpečení** , které jsou definovány srovnávacím testem zabezpečení Azure, a souvisejících pokynů, které se vztahují na Virtual Network NAT. **Ovládací prvky** , které se nevztahují na Virtual Network NAT, jsou vyloučené.

 
Pokud chcete zjistit, jak Virtual Network překlad adres (NAT) zcela mapuje na bezpečnostní srovnávací testy zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení služby Virtual Network NAT](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Pokyny**: Virtual Network NAT nepodporuje řízení odchozího provozu se skupinami zabezpečení sítě (NSG).  Příchozí provoz je povolený jenom v reakci na odchozí vycházející tok.

Protokoly toku skupin zabezpečení sítě (NSG) se ale dají použít s prostředky brány NAT k monitorování odchozího původního provozu.

Použijte Azure Security Center a pořiďte si doporučení k zabezpečení síťových prostředků Azure pomocí doporučení pro ochranu sítě. Povolte protokolování toku skupin zabezpečení sítě a odešlete protokoly do účtu Azure Storage k auditování. Protokoly toku můžete také odeslat do pracovního prostoru Log Analytics a potom pomocí Analýza provozu poskytnout přehled o vzorech přenosu v cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu, identifikovat aktivní body a bezpečnostní hrozby, pochopit vzorce toku přenosů a odhalit nesprávné konfigurace sítě. 

- [Přehled služby Virtual Network NAT](https://docs.microsoft.com/azure/virtual-network/nat-overview)

- [Prostředek služby NAT Gateway](https://docs.microsoft.com/azure/virtual-network/nat-gateway-resource)

- [Jak povolit protokoly toku skupin zabezpečení sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal) 

- [Postup povolení a použití Analýza provozu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: povolení zachytávání paketů Network Watcher pro zkoumání aktivit neobvyklé. 

- [Postup vytvoření instance Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro podsítě nakonfigurované pomocí prostředků brány NAT pomocí předdefinovaných nebo vlastních definicí Azure Policy a přiřazení.

- [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Ukázky Azure Policy pro sítě](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny vašich prostředků brány NAT a virtuálních síťových prostředků. V Azure Monitor můžete vytvářet výstrahy, které vám upozorní na změnu důležitých zdrojů.

- [Jak zobrazit a načíst události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Vytváření výstrah v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: ingestování protokolů souvisejících s Virtual Network NAT prostřednictvím Azure monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Případně můžete tato data povolit a zařadit do Azure Sentinel nebo SIEM třetí strany.

- [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard) 

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky brány NAT s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 

- [Principy protokolování a různých typů protokolů v Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview) 

Virtual Network NAT v současné době nevytváří žádné další diagnostické protokoly, které mohou být konfigurovatelné zákazníky.

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použití Azure Security Center s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech. Místo toho můžete povolit a začlenit data do Azure Sentinel.

- [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard) 

- [Správa výstrah v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) 

- [Upozornění na data protokolu Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). 

Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných vytvořil výčet všech předplatných Azure

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně se doporučuje vytvořit a používat prostředky založené na Azure Resource Manager, které se budou překládat.

- [Dotazy na grafy prostředků Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: použití značek pro správu prostředků Azure.

- [Vytvoření dalších předplatných Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) 

- [Postup vytvoření Skupiny pro správu](https://docs.microsoft.com/azure/governance/management-groups/create) 

- [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: aktuálně není k dispozici

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit ve vašich předplatných.
Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) 
- [Jak vytvářet dotazy pomocí Azure graphu](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení služeb, které můžete zřídit ve vašem prostředí.

- [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: nepoužitelné; Virtual Network NAT nemá žádné konfigurace zabezpečení.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: použijte Azure DevOps k bezpečnému ukládání a správě kódu, jako jsou vlastní definice Azure Policy, Azure Resource Manager šablony a požadované konfigurační skripty stavu. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS. 

- [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [O oprávněních a skupinách v Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: nepoužitelné; Virtual Network NAT nemá žádné konfigurace zabezpečení.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Doprovodné** materiály: pro tuto nabídku by měla být vytvořena procedura reakce na incidenty, aby se zajistilo, že může docházet k odpovídajícím procesům reakce na incidenty, aby získala odpovídající úroveň stanovení priorit do vyřešení incidentů.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Pokyny**: pro tuto nabídku by se mělo vytvořit postup hodnocení incidentu a stanovení priorit, aby se zajistilo správné riziko nebo bodování hrozeb, aby se dosáhlo vhodné úrovně stanovení priorit do vyřešení incidentů.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Pokyny**: pro tuto nabídku by měl být vytvořen a testován postup odezvy zabezpečení incidentu, aby se zajistilo správné zmírnění rizika s odpovídající úrovní stanovení priorit do předpokládaného řešení incidentů.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Pokyny**: pro tuto nabídku by měl být vytvořen a testován postup odezvy zabezpečení incidentu, aby se zajistilo správné zmírnění rizika s odpovídající úrovní stanovení priorit do předpokládaného řešení incidentů.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: pro tuto nabídku by měl být vytvořen a testován postup odezvy zabezpečení incidentu, aby se zajistilo správné zmírnění rizika s odpovídající úrovní stanovení priorit do předpokládaného řešení incidentů.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Pokyny**: pro tuto nabídku by měl být vytvořen a testován postup odezvy zabezpečení incidentu, aby se zajistilo správné zmírnění rizika s odpovídající úrovní stanovení priorit do předpokládaného řešení incidentů.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft. 

- [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [Přehled Azure Security benchmark v2](/azure/security/benchmarks/overview) .
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
