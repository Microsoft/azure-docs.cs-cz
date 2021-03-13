---
title: Základní hodnoty zabezpečení Azure pro Virtual Network NAT
description: Základní hodnoty zabezpečení služby Virtual Network NAT poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 501d5b17358501f17d17e8884ceec0bcbbb2ab1c
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612401"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Základní hodnoty zabezpečení Azure pro Virtual Network NAT

Tato základní hodnota zabezpečení platí pro Microsoft Virtual Network NAT pokyny od [verze Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **ovládacích prvků zabezpečení** , které jsou definovány srovnávacím testem zabezpečení Azure, a souvisejících pokynů, které se vztahují na Virtual Network NAT. **Ovládací prvky** , které se nevztahují na Virtual Network NAT, jsou vyloučené.

 
Pokud chcete zjistit, jak Virtual Network překlad adres (NAT) zcela mapuje na bezpečnostní srovnávací testy zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení služby Virtual Network NAT](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Pokyny**: Virtual Network NAT nepodporuje řízení odchozího provozu se skupinami zabezpečení sítě (NSG).  Příchozí provoz je povolený jenom v reakci na odchozí vycházející tok.

Protokoly toku skupin zabezpečení sítě (NSG) se ale dají použít s prostředky brány NAT k monitorování odchozího původního provozu.

Použijte Azure Security Center a pořiďte si doporučení k zabezpečení síťových prostředků Azure pomocí doporučení pro ochranu sítě. Povolte protokolování toku skupin zabezpečení sítě a odešlete protokoly do účtu Azure Storage k auditování. Protokoly toku můžete také odeslat do pracovního prostoru Log Analytics a potom pomocí Analýza provozu poskytnout přehled o vzorech přenosu v cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu, identifikovat aktivní body a bezpečnostní hrozby, pochopit vzorce toku přenosů a odhalit nesprávné konfigurace sítě. 

- [Přehled služby Virtual Network NAT](./nat-overview.md)

- [Prostředek služby NAT Gateway](./nat-gateway-resource.md)

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: povolení zachytávání paketů Network Watcher pro zkoumání aktivit neobvyklé. 

- [Postup vytvoření instance Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro podsítě nakonfigurované pomocí prostředků brány NAT pomocí předdefinovaných nebo vlastních definicí Azure Policy a přiřazení.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ukázky Azure Policy pro sítě](../governance/policy/samples/built-in-policies.md#network)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny vašich prostředků brány NAT a virtuálních síťových prostředků. V Azure Monitor můžete vytvářet výstrahy, které vám upozorní na změnu důležitých zdrojů.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: ingestování protokolů souvisejících s Virtual Network NAT prostřednictvím Azure monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Případně můžete tato data povolit a zařadit do Azure Sentinel nebo SIEM třetí strany.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky brány NAT s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/essentials/platform-logs-overview.md) 

Virtual Network NAT v současné době nevytváří žádné další diagnostické protokoly, které mohou být konfigurovatelné zákazníky.

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použití Azure Security Center s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech. Místo toho můžete povolit a začlenit data do Azure Sentinel.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md) 

- [Upozornění na data protokolu Log Analytics](../azure-monitor/alerts/tutorial-response.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). 

Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných vytvořil výčet všech předplatných Azure

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně se doporučuje vytvořit a používat prostředky založené na Azure Resource Manager, které se budou překládat.

- [Dotazy na grafy prostředků Azure](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: použití značek pro správu prostředků Azure.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md) 

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md) 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: aktuálně není k dispozici

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit ve vašich předplatných.
Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 
- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení služeb, které můžete zřídit ve vašem prostředí.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: nepoužitelné; Virtual Network NAT nemá žádné konfigurace zabezpečení.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: použijte Azure DevOps k bezpečnému ukládání a správě kódu, jako jsou vlastní definice Azure Policy, Azure Resource Manager šablony a požadované konfigurační skripty stavu. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS. 

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow) 

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: nepoužitelné; Virtual Network NAT nemá žádné konfigurace zabezpečení.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Doprovodné** materiály: pro tuto nabídku by měla být vytvořena procedura reakce na incidenty, aby se zajistilo, že může docházet k odpovídajícím procesům reakce na incidenty, aby získala odpovídající úroveň stanovení priorit do vyřešení incidentů.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Pokyny**: pro tuto nabídku by se mělo vytvořit postup hodnocení incidentu a stanovení priorit, aby se zajistilo správné riziko nebo bodování hrozeb, aby se dosáhlo vhodné úrovně stanovení priorit do vyřešení incidentů.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Pokyny**: pro tuto nabídku by měl být vytvořen a testován postup odezvy zabezpečení incidentu, aby se zajistilo správné zmírnění rizika s odpovídající úrovní stanovení priorit do předpokládaného řešení incidentů.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Pokyny**: pro tuto nabídku by měl být vytvořen a testován postup odezvy zabezpečení incidentu, aby se zajistilo správné zmírnění rizika s odpovídající úrovní stanovení priorit do předpokládaného řešení incidentů.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: pro tuto nabídku by měl být vytvořen a testován postup odezvy zabezpečení incidentu, aby se zajistilo správné zmírnění rizika s odpovídající úrovní stanovení priorit do předpokládaného řešení incidentů.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Pokyny**: pro tuto nabídku by měl být vytvořen a testován postup odezvy zabezpečení incidentu, aby se zajistilo správné zmírnění rizika s odpovídající úrovní stanovení priorit do předpokládaného řešení incidentů.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem. 

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)