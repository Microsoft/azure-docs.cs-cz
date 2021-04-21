---
title: Základní hodnoty zabezpečení Azure pro automatizaci
description: Základní hodnoty zabezpečení služby Automation poskytují pokyny a prostředky pro implementaci doporučení zabezpečení určených ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 7f87fcac0c1b07e108082c4b4f48bde046dc63c7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834493"
---
# <a name="azure-security-baseline-for-automation"></a>Základní hodnoty zabezpečení Azure pro automatizaci

Tato základní hodnota zabezpečení platí pro Azure Automation pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure Automation. **Ovládací prvky** , které se nevztahují k Azure Automation byly vyloučeny.

 
Pokud chcete zjistit, jak Azure Automation kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Automation Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: účet Azure Automation ještě nepodporuje privátní propojení Azure pro omezení přístupu ke službě prostřednictvím privátních koncových bodů. Sady Runbook, které se ověřují a spouštějí u prostředků v Azure, běží v izolovaném prostoru Azure a využívají sdílené back-endové prostředky, které Microsoft zodpovídá za izolaci od sebe. jejich síť není omezená a má přístup k veřejným prostředkům. Azure Automation aktuálně nemá integraci virtuální sítě pro privátní sítě nad rámec podpory pro procesy Hybrid Runbook Worker. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud chcete získat další izolaci pro své Runbooky, můžete použít hybridní pracovní procesy Runbooku běžící na virtuálních počítačích Azure. Když vytváříte virtuální počítač Azure, musíte vytvořit virtuální síť (VNet) nebo použít stávající virtuální síť a nakonfigurovat virtuální počítače s podsítí. Zajistěte, aby všechny nasazené podsítě používaly skupinu zabezpečení sítě s ovládacími prvky pro přístup k síti, které jsou specifické pro vaše aplikace a důvěryhodné porty a zdroje. Požadavky na konkrétní službu najdete v doporučení zabezpečení pro konkrétní službu. Případně, pokud máte konkrétní požadavek, Azure Firewall mohou být použity také k jeho splnění.

- [Virtuální sítě a virtuální počítače v Azure](../virtual-machines/network-overview.md)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak nasadit a nakonfigurovat Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Prostředí pro spuštění sady Runbook](./automation-runbook-execution.md#runbook-execution-environment)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: Azure Automation aktuálně nemá integraci virtuální sítě pro privátní sítě nad rámec podpory pro procesy Hybrid Runbook Worker. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte Hybrid Runbook Worker zálohovaný virtuálními počítači Azure, ujistěte se, že podsíť obsahující tyto pracovní procesy je povolená se skupinou zabezpečení sítě (NSG), a nakonfigurujte protokoly toku pro přeposílání protokolů do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také přesměrovat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

I když se pravidla NSG a uživatelsky definované trasy nevztahují na soukromý koncový bod, protokoly toku NSG a informace monitorování pro odchozí připojení se pořád podporují a dají se použít.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: Azure Automation aktuálně nemá integraci virtuální sítě pro privátní sítě nad rámec podpory pro procesy Hybrid Runbook Worker. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte hybridní pracovní procesy Runbooku, které jsou zajištěné virtuálními počítači Azure, zajistěte ve svých virtuálních sítích hostujících procesy Hybrid Runbook Worker (DDoS) standard Protection, abyste se mohli chránit před útoky DDoS. Pomocí Azure Security Center integrované analýzy hrozeb můžete komunikaci se známými škodlivými IP adresami odepřít.  Nakonfigurujte Azure Firewall na všech segmentech Virtual Network, s povolenou funkcí Analýza hrozeb a nastavte **výstrahy a odmítnutí** pro škodlivý síťový provoz.

Přístup k síti pro virtuální počítače s Windows můžete omezit tak, že na základě omezeného časového období omezíte vystavení virtuálních počítačů s Windows na schválené IP adresy pomocí nástroje Azure Security Center jenom v čase.  K omezení portů a zdrojových IP adres na základě skutečného provozu a analýzy hrozeb použijte taky Azure Security Center doporučení adaptivního posílení zabezpečení sítě pro konfigurace NSG.

- [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

- [Pochopení Azure Security Center adaptivního posílení zabezpečení sítě](../security-center/security-center-adaptive-network-hardening.md)

- [Pochopení Azure Security Center k síťovému Access Control v čase](../security-center/security-center-just-in-time.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: Azure Automation v současné době nemá integraci virtuální sítě pro privátní sítě, a to nad rámec podpory Hybrid Runbook Worker, tento ovládací prvek se nedá použít, pokud používáte službu Inbox bez hybridních pracovních procesů.

Pokud používáte hybridní pracovní procesy Runbooku, které jsou zajištěné virtuálními počítači Azure, můžete nahrávat protokoly NSG Flow do účtu úložiště a vygenerovat záznamy toků pro Azure Virtual Machines, které fungují jako pracovní procesy Runbooku. Při zkoumání aktivity neobvyklé můžete povolit zachytávání paketů Network Watcher, aby bylo možné zkontrolovat síťový provoz pro neobvyklou a neočekávanou aktivitu.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: Azure Automation aktuálně nemá integraci virtuální sítě pro privátní sítě nad rámec podpory pro procesy Hybrid Runbook Worker. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte hybridní pracovní procesy Runbooku hostované na virtuálních počítačích Azure, můžete kombinovat zachycení paketů poskytovaná Network Watcher a open source ID pomocí nástrojů pro detekci neoprávněného vniknutí do sítě pro zajištění široké škály hrozeb pro tyto pracovní počítače. V případě potřeby můžete také nasadit Azure Firewall do segmentů Virtual Network, s povolenou funkcí Analýza hrozeb a nakonfigurovanou na "výstrahy a zamítnutí" pro škodlivý síťový provoz.

- [Zjišťování neoprávněných vniknutí k síti pomocí Network Watcher a open source nástrojů](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Postup konfigurace výstrah pomocí Azure Firewall](../firewall/threat-intel.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek Virtual Network služeb můžete definovat řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall nakonfigurovaných v Azure, které vyžadují přístup k vašim prostředkům Automation. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (například GuestAndHybridManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Pochopení a používání značek služeb](../virtual-network/service-tags-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky, které používá Azure Automation s Azure Policy.

Pomocí plánů Azure můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou šablony Azure Resource Manageru, řízení a zásady Azure RBAC, a to v jediné definici podrobného plánu. Můžete použít podrobný plán na nová předplatná a vyladit řízení a správu prostřednictvím správy verzí.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ukázky Azure Policy pro sítě](../governance/policy/samples/built-in-policies.md#network)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro skupin zabezpečení sítě a další prostředky, které se týkají zabezpečení sítě a toku provozu. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny v síťových prostředcích. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: dopředná data protokolu Azure monitor protokoly pro agregaci dat zabezpečení vygenerovaných prostředky Azure Automation. V rámci Azure Monitor můžete pomocí dotazů protokolu vyhledávat a provádět analýzy a používat účty Azure Storage k dlouhodobému a Archivačnímu úložišti. Azure Automation může do pracovního prostoru Log Analytics odeslat stav úlohy Runbooku, datové proudy úloh, data konfigurace stavu služby Automation, správu aktualizací a protokoly změn nebo inventáře. Tyto informace jsou viditelné z rozhraní API Azure Portal, Azure PowerShell a Azure Monitor protokolů, které vám umožní provádět jednoduché šetření.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Předávání dat úloh Azure Automation do protokolů Azure Monitoru](automation-manage-send-joblogs-log-analytics.md)

- [Integrace DSC s protokoly Azure Monitor](automation-dsc-diagnostics.md)

- [Podporované oblasti pro propojený pracovní prostor Log Analytics](how-to/region-mappings.md)

- [Dotazy Update Management protokoly](update-management/query-logs.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení Azure monitor pro přístup k protokolům auditu a aktivitám, které zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky. 

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Zobrazení a načtení událostí protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Podrobnosti o uchovávání dat pro účty Automation](./automation-managing-data.md#data-retention)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro neobvyklé chování a pravidelné kontroly výsledků. Pomocí Azure Monitor dotazů protokolu můžete prohlížet protokoly a provádět dotazy na data protokolu.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Pochopení dotazů protokolu v Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

- [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použijte Azure Security Center se Azure monitor pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech.

Alternativně můžete povolit a začlenit data do Azure Sentinel.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Azure Monitor](../azure-monitor/alerts/tutorial-response.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: implementace řešení od jiného výrobce z Azure Marketplace pro řešení protokolování DNS podle potřeby vaší organizace.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: Používejte předdefinované role správce Azure Active Directory (Azure AD), které se dají explicitně přiřadit a můžou se dotazovat na ně. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu. Při každém použití účtů služby Automation jako účtu Spustit jako pro vaše Runbooky se ujistěte, že tyto instanční objekty jsou ve vašem inventáři sledovány, protože mají často čas se zvýšenými oprávněními. Odstraňte všechny nepoužívané účty Spustit jako pro minimalizaci vystaveného prostoru pro útoky.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Odstranění účet Spustit jako nebo Spustit jako pro Classic](delete-run-as-account.md)

- [Správa účtu Azure Automation spustit jako](manage-runas-account.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: Azure Automation účet nemá koncept výchozích hesel.  Zákazníci jsou odpovědní za aplikace třetích stran a služby Marketplace, které mohou používat výchozí hesla, která se spouštějí na službě nebo její procesy Hybrid Runbook Worker.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu. Při každém použití účtů služby Automation jako účtu Spustit jako pro vaše Runbooky se ujistěte, že tyto instanční objekty jsou ve vašem inventáři sledovány, protože mají často čas se zvýšenými oprávněními. Oborujte tyto identity s nejmenšími privilegovanými oprávněními, které potřebují, aby vaše Runbooky úspěšně prováděly automatizovaný proces. Odstraňte všechny nepoužívané účty Spustit jako pro minimalizaci vystaveného prostoru pro útoky.

Pomocí Azure Active Directory (Azure AD) Privileged Identity Management privilegovaných rolí pro služby Microsoftu a Azure Resource Manager můžete taky povolit přístup za běhu nebo jen tolik.

- [Další informace o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

- [Odstranění účet Spustit jako nebo Spustit jako pro Classic](delete-run-as-account.md)

- [Správa účtu Azure Automation spustit jako](manage-runas-account.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné** materiály: kdykoli je to možné, používejte jednotné přihlašování s Azure Active Directory (Azure AD) místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte Azure Security Center doporučení pro správu identit a přístupu.

- [Jednotné přihlašování k aplikacím v Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

- [Použití Azure AD k ověření v Azure](automation-use-azure-ad.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné** materiály: použití privilegovaným přístupem s vícefaktorového ověřováním nakonfigurovaným pro přihlášení a konfiguraci prostředků účtů Azure Automation v produkčních prostředích. 

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu 

**Doprovodné** materiály: využití zjišťování rizik Azure Active Directory (Azure AD) k zobrazení výstrah a sestav na základě rizikového chování uživatelů. V případě potřeby může zákazník přeposílat Azure Security Center výstrahy detekce rizik Azure Monitor a nakonfigurovat vlastní výstrahy a oznámení pomocí skupin akcí.

- [Principy zjišťování rizik Azure Security Center (podezřelá aktivita)](../active-directory/identity-protection/overview-identity-protection.md)

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak nakonfigurovat skupiny akcí pro vlastní výstrahy a oznámení](../azure-monitor/alerts/action-groups.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění 

**Doprovodné** materiály: doporučuje se používat pojmenovaná umístění podmíněného přístupu k povolení přístupu jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí. 

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele. Pokud používáte hybridní pracovní procesy Runbooku, můžete místo účtů spustit jako využít spravované identity, aby bylo možné zajistit bezproblémové zabezpečení zabezpečených oprávnění.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Použití ověřování Runbooku u spravovaných identit](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. Při každém použití účtů účtu Spustit jako pro vaše Runbooky zajistěte, aby tyto instanční objekty byly také sledovány ve vašem inventáři, protože mají často vyšší oprávnění. Odstraňte všechny nepoužívané účty Spustit jako pro minimalizaci vystaveného prostoru pro útoky.

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

- [Odstranění účet Spustit jako nebo Spustit jako pro Classic](delete-run-as-account.md)

- [Správa účtu Azure Automation spustit jako](manage-runas-account.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: máte přístup k aktivitě pro přihlášení k Azure Active Directory (Azure AD), událostem auditu a rizikovým protokolům událostí, které umožňují integraci s jakýmkoli nástrojem Siem/monitoring.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: pomocí funkcí Azure Active Directory (Azure AD) a ochrany Identity Protection můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů v síťovém prostředku. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: u účtů Azure Automation může podpora Microsoftu přistupovat k metadatům prostředků platformy během otevřeného případu podpory bez použití jiného nástroje.

Pokud ale použijete hybridní pracovní procesy Runbooku, které jsou zajištěné virtuálními počítači Azure a potřebujete získat přístup k zákaznickým datům (například během žádosti o podporu), použijte Customer Lockbox (Preview) pro virtuální počítače Azure a zkontrolujte a schvalte nebo odmítněte žádosti o přístup k datům zákazníků.

- [Porozumění Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: pomocí značek pomáhají při sledování Azure Automationch prostředků, které ukládají nebo zpracovávají citlivé informace. 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Izolujte prostředí pomocí samostatných prostředků účtu Automation. Prostředky jako procesy Hybrid Runbook Worker by se měly oddělit pomocí virtuální sítě/podsítě, vhodně označit a zabezpečit v rámci skupiny zabezpečení sítě (NSG) nebo Azure Firewall. U virtuálních počítačů, které ukládají nebo zpracovávají citlivá data, implementujte zásady a postupy pro jejich vypnutí, pokud se nepoužívají.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak nakonfigurovat výstrahu nebo upozornění a odepřít pomocí Azure Firewall](../firewall/threat-intel.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: při použití funkce Hybrid Runbook Worker použijte řešení třetích stran z Azure Marketplace na hraničních sítích, které monitorují neoprávněný přenos citlivých informací a zablokují tyto přenosy, a upozorní odborníky na zabezpečení informací.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft zpracovává veškerý obsah zákazníků jako citlivý a chráněný proti ztrátám a expozici zákaznických dat. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: šifrování všech citlivých informací během přenosu. Ujistěte se, že všichni klienti připojující se k prostředkům Azure v Azure Virtual Networks můžou vyjednávat TLS 1,2 nebo vyšší. Azure Automation plně podporuje a vynutila přenosovou vrstvu (TLS) 1,2 a všechna volání klientů nebo novější verze všech externích koncových bodů HTPS (prostřednictvím webhooků, uzlů DSC, hybrid Runbook Worker).

Pokud je to možné, postupujte podle Azure Security Center doporučení pro šifrování v klidovém režimu a šifrování.

- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Azure Automation vynucení TLS 1,2](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: k identifikaci všech citlivých informací uložených, zpracovávaných nebo odeslaných technologickými systémy organizace, včetně těch, které se nacházejí v lokalitě nebo u poskytovatele vzdálené služby, a aktualizaci inventáře citlivých informací organizace, použijte nástroj pro aktivní zjišťování třetí strany.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: použití řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k prostředkům Azure Automation pomocí vestavěných definic rolí, přiřazení přístupu uživatelům, kteří přistupují k vašim prostředkům Automation, v rámci přístupového modelu s nejnižším oprávněním nebo s dostatečným oprávněním. Pokud používáte hybridní pracovní procesy Runbooku, využijte spravované identity pro tyto virtuální počítače, abyste se vyhnuli používání instančních objektů. při použití s více klienty nebo procesy Hybrid Runbook Worker se ujistěte, že se k identitě pracovních procesů Runbooku používaly náležitě vymezená oprávnění Azure RBAC.

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Oprávnění sady Runbook pro Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Správa oprávnění rolí a zabezpečení](automation-role-based-access-control.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: Azure Automation aktuálně nezveřejňuje podkladové virtuální počítače Runbooku pro více tenantů a tato činnost je zpracována platformou. Tento ovládací prvek se nedá použít, pokud používáte službu, která je součástí služby Box bez procesů Hybrid Runbook Worker.

Pokud používáte hybridní pracovní procesy Runbooku, které jsou zajištěné virtuálními počítači Azure, musíte použít řešení ochrany před únikem informací od jiného výrobce, abyste vynutili řízení přístupu k vašim hostovaným Hybrid Runbook Worker virtuálním počítačům.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: použití klíčů spravovaných zákazníkem pomocí Azure Automation. Azure Automation podporuje použití klíčů spravovaných zákazníkem k šifrování všech používaných zabezpečených prostředků, jako jsou například přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Využijte šifrované proměnné se sadami Runbook pro všechny vaše trvalé vyhledávání proměnných musí zabránit nezamýšlené expozici.

Pokud používáte hybridní pracovní procesy Runbooku, virtuální disky na virtuálních počítačích se zašifrují v klidovém stavu pomocí šifrování na straně serveru nebo přes ADE (Azure Disk Encryption). Azure Disk Encryption využívá funkci BitLocker systému Windows k šifrování spravovaných disků pomocí klíčů spravovaných zákazníkem v rámci virtuálního počítače hosta. Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se zlepšuje v ADE tím, že vám umožní používat pro vaše virtuální počítače jakékoli typy operačních systémů a image šifrováním dat ve službě úložiště.

- [Šifrování na straně serveru Azure Managed disks](../virtual-machines/disk-encryption.md)

- [Azure Disk Encryption pro virtuální počítače s Windows](../virtual-machines/windows/disk-encryption-overview.md)

- [Použití klíčů spravovaných zákazníkem pro účet Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Spravované proměnné v Azure Automation](shared-resources/variables.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. Automation**:

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na důležité prostředky Azure, jako jsou síťové součásti, účty Azure Automation a runbooky. 

- [Protokolování diagnostiky pro skupinu zabezpečení sítě](../private-link/private-link-overview.md#logging-and-monitoring)

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: dodržujte doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení vašich prostředků Azure.

- [Doporučení zabezpečení v Azure Security Center](../security-center/security-center-recommendations.md)

- [Referenční informace o Security Center doporučení](../security-center/recommendations-reference.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Pokyny**: Exportovat výsledky kontroly v konzistentních intervalech a porovnat výsledky a ověřit, zda byly chyby zabezpečení opraveny. Při použití doporučení správy ohrožení zabezpečení navrhovaného Azure Security Center se může zákazník na portálu vybraného řešení překlopit a zobrazit historická data kontroly.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: použijte výchozí hodnocení rizik (Secure skore) poskytované Azure Security Center, které vám pomůžou určit prioritu nápravy zjištěných chyb zabezpečení.

- [Pochopení Azure Security Center zabezpečeného skóre](../security-center/secure-score-security-controls.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny Azure Automation prostředky v rámci předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie. 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Azure Automationch prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného. Odstraňte všechny nepoužívané účty Spustit jako pro minimalizaci vystaveného prostoru pro útoky.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Odstranění účet Spustit jako nebo Spustit jako pro Classic](delete-run-as-account.md)

- [Správa účtu Azure Automation spustit jako](manage-runas-account.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: budete muset vytvořit inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad: 

- Žádné povolené typy prostředků 
- Povolené typy prostředků 

K dotazování a zjišťování prostředků v rámci předplatných navíc použijte graf prostředků Azure. To může pomáhat v prostředích s vysokým zabezpečením, jako jsou ty s účty úložiště. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Azure Policy ukázkové předdefinované pro Azure Automation](policy-reference.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: zákazník může zabránit vytváření nebo používání prostředků pomocí Azure Policy podle pokynů společnosti zákazníka. Můžete implementovat vlastní proces pro odebrání neautorizovaných prostředků. V rámci Azure Automation nabídky je možné instalovat, odebírat a spravovat prostředí PowerShell nebo moduly Pythonu, ke kterým mají Runbooky přístup prostřednictvím portálu nebo rutin. Neschválený nebo starý modul by měl být pro Runbooky odebrán nebo aktualizován.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Spravovat modul v Azure Automation](shared-resources/modules.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: použití zásad podmíněného přístupu Azure k omezení možnosti uživatelů při interakci s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management z nezabezpečených nebo neschválených umístění nebo zařízení. 

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace Azure Automation a souvisejících prostředků. Můžete také použít předdefinované definice Azure Policy.

Azure Resource Manager taky umožňuje exportovat šablonu do JavaScript Object Notation (JSON), která by se měla zkontrolovat, aby se zajistilo, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

Pro vaše prostředky Azure můžete také použít doporučení z Azure Security Center jako standardní hodnoty konfigurace zabezpečení.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy ukázkové předdefinované pro Azure Automation](policy-reference.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: pomocí Azure Resource Manager šablon a Azure Policy bezpečně nakonfigurujte prostředky Azure přidružené k Azure Automation. Šablony Azure Resource Manager jsou soubory založené na formátu JSON, které slouží k nasazení prostředků Azure, a všechny vlastní šablony bude nutné ukládat a udržovat bezpečně v úložišti kódu. Pomocí funkce integrace správy zdrojového kódu můžete své Runbooky v účtu Automation udržovat v aktuálním stavu pomocí skriptů v úložišti správy zdrojových kódů. Použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

- [Použití integrace správy zdrojového kódu](source-control-integration.md)

- [Informace o vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Porozumění efektům Azure Policy](../governance/policy/concepts/effects.md)

- [Nasazení účtu Automation pomocí šablony Azure Resource Manager](/azure/automation/quickstart-create-automation-account-template)

- [Azure Policy ukázkové předdefinované pro Azure Automation](policy-reference.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: pomocí Azure DevOps bezpečně ukládejte a spravujte váš kód, jako jsou vlastní zásady Azure, šablony Azure Resource Manager a požadované konfigurační skripty stavu. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS. Pomocí funkce integrace správy zdrojového kódu můžete své Runbooky v účtu Automation udržovat v aktuálním stavu pomocí skriptů v úložišti správy zdrojových kódů.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Použití integrace správy zdrojového kódu](source-control-integration.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro prostředky Azure pomocí Azure Policy. Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich prostředků Azure. Můžete také využít integrované definice zásad související s vašimi konkrétními prostředky. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak používat aliasy](../governance/policy/concepts/definition-structure.md#aliases)

- [Azure Policy ukázkové předdefinované pro Azure Automation](policy-reference.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: pomocí Azure Policy pro upozornění a audit konfigurací prostředků Azure můžete použít zásady ke zjištění určitého prostředku, který není nakonfigurovaný s privátním koncovým bodem.

Pokud používáte funkci Hybrid Runbook Worker, využijte Azure Security Center k provádění kontrol standardních hodnot pro virtuální počítače Azure.  Mezi další metody automatizované konfigurace patří: Konfigurace stavu Azure Automation.

- [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy ukázkové předdefinované pro Azure Automation](policy-reference.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: pomocí Azure Resource Manager nasaďte účty Azure Automation a související prostředky. Azure Resource Manager poskytuje možnost exportovat šablony, které se dají použít jako zálohy k obnovení účtů Azure Automation a souvisejících prostředků. Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony.

Pomocí funkce integrace správy zdrojového kódu můžete své Runbooky v účtu Automation udržovat v aktuálním stavu pomocí skriptů v úložišti správy zdrojových kódů.

- [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md)

- [Odkaz na šablonu Azure Resource Manager pro prostředky Azure Automation](/azure/templates/microsoft.automation/allversions)

- [Vytvoření účtu Automation pomocí šablony Azure Resource Manager](quickstart-create-automation-account-template.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Úvod do Azure Automation](automation-intro.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Použití klíčů spravovaných zákazníkem pro účet Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Použití integrace správy zdrojového kódu](source-control-integration.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: pomocí Azure Resource Manager nasaďte účty Azure Automation a související prostředky. Azure Resource Manager poskytuje možnost exportovat šablony, které se dají použít jako zálohy k obnovení účtů Azure Automation a souvisejících prostředků. Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony. Zálohujte klíče spravované zákazníkem v rámci Azure Key Vault. Runbooky můžete exportovat do souborů skriptu pomocí Azure Portal nebo PowerShellu.

- [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md)

- [Odkaz na šablonu Azure Resource Manager pro prostředky Azure Automation](/azure/templates/microsoft.automation/allversions)

- [Vytvoření účtu Automation pomocí šablony Azure Resource Manager](quickstart-create-automation-account-template.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Úvod do Azure Automation](automation-intro.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Použití klíčů spravovaných zákazníkem pro účet Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Zálohování dat Azure pro účty Automation](./automation-managing-data.md#data-backup)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: Zajistěte, aby v případě potřeby pravidelně prováděly nasazení Azure Resource Manager šablon pro izolované předplatné do izolovaného předplatného. Test obnovení zálohovaných klíčů spravovaných zákazníkem

- [Nasazení prostředků pomocí šablon ARM a Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Použití klíčů spravovaných zákazníkem pro účet Automation](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: pomocí Azure DevOps bezpečně ukládejte a spravujte kód, jako je Azure Resource Manager šablon. K ochraně prostředků, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS.

Pomocí funkce integrace správy zdrojového kódu můžete své Runbooky v účtu Automation udržovat v aktuálním stavu pomocí skriptů v úložišti správy zdrojových kódů.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Použití integrace správy zdrojového kódu](source-control-integration.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění. 

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka pomocí značek a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure, zejména těch, která zpracovávají citlivá data.  Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení. 

**Doprovodné** materiály: řiďte se pravidly společnosti Microsoft o zapojení, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)
