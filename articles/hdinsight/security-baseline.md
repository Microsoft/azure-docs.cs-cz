---
title: Základní hodnoty zabezpečení Azure pro HDInsight
description: Základní hodnoty zabezpečení Azure pro HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ebc0398eeb7679ffd57e0aa5aae642f6303aaa35
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484606"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Základní hodnoty zabezpečení Azure pro HDInsight

Základní plán zabezpečení Azure pro HDInsight obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Doprovodné**materiály: hraniční zabezpečení ve službě Azure HDInsight se dosahuje pomocí virtuálních sítí. Podnikový správce může vytvořit cluster v rámci virtuální sítě a použít skupinu zabezpečení sítě (NSG) k omezení přístupu k virtuální síti. Pouze povolené IP adresy v příchozích pravidlech skupiny zabezpečení sítě budou moci komunikovat s clusterem Azure HDInsight. Tato konfigurace poskytuje hraniční zabezpečení. Všechny clustery nasazené ve virtuální síti budou mít taky privátní koncový bod, který se přeloží na privátní IP adresu uvnitř Virtual Network pro privátní přístup HTTP ke branám clusteru.

Pokud chcete snížit riziko ztráty dat prostřednictvím exfiltrace, omezte odchozí síťový provoz pro clustery Azure HDInsight pomocí Azure Firewall.

Jak nasadit Azure HDInsight v rámci Virtual Network a zabezpečit pomocí skupiny zabezpečení sítě: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Postup omezení odchozího provozu pro clustery Azure HDInsight pomocí Azure Firewall: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Doprovodné**materiály: použijte Azure Security Center a opravte doporučení k ochraně sítě pro virtuální síť, podsíť a skupinu zabezpečení sítě, která se používá k zabezpečení clusteru Azure HDInsight. Povolte protokoly toku NSG (Network Security Group) a odešlete protokoly do účtu Azure Storage pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Azure Log Analytics a používat Azure Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Azure Analýza provozu je schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a odhalit síťové opravy chyb.

Jak povolit protokoly toku NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a používat Azure Analýza provozu:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Pochopení zabezpečení sítě, které poskytuje Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné**materiály: nepoužitelné; Srovnávací test je určený pro služby Azure apps nebo výpočetní prostředky hostující webové aplikace.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné**materiály: u ochrany před útoky DDoS povolte službu Azure DDoS Standard Protection ve virtuální síti, ve které je vaše Azure HDInsight nasazené. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

Jak nakonfigurovat DDoS Protection:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Pochopení Azure Security Center integrované analýzy hrozeb:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Pokyny**: povolení flog protokolů skupiny zabezpečení sítě (NSG) pro NSG připojené k podsíti používané k ochraně clusteru Azure HDInsight. Zaznamenejte NSG Flow do účtu Azure Storage pro generování záznamů toků. Pokud se to vyžaduje pro prošetření aktivity neobvyklé, povolte zachytávání paketů služby Azure Network Watcher.

Jak povolit protokoly toku NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Postup povolení Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné**materiály: požadavek může splnit ID řízení zabezpečení Azure 1,1; Nasaďte cluster Azure HDInsight do virtuální sítě a zabezpečte ji pomocí skupiny zabezpečení sítě (NSG).

K dispozici je několik závislostí pro Azure HDInsight, které vyžadují příchozí provoz. Příchozí provoz správy nejde odeslat přes zařízení brány firewall. Zdrojové adresy pro požadovaný provoz správy jsou známé a publikované. Vytvořte pravidla skupiny zabezpečení sítě s těmito informacemi, abyste mohli povolit provoz jenom z důvěryhodných umístění, a zabezpečit příchozí provoz do clusterů.

Pokud chcete snížit riziko ztráty dat prostřednictvím exfiltrace, omezte odchozí síťový provoz pro clustery Azure HDInsight pomocí Azure Firewall.

Jak nasadit HDInsight v rámci Virtual Network a zabezpečit pomocí skupiny zabezpečení sítě: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Pochopení závislostí HDInsight a využití brány firewall: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

IP adresy správy HDInsight: https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné**materiály: nepoužitelné; Srovnávací test je určený pro služby Azure apps nebo výpočetní prostředky hostující webové aplikace.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služby virtuální sítě můžete definovat řízení přístupu k síti u skupin zabezpečení sítě (NSG), které jsou připojené k podsíti, ve které je nasazený cluster Azure HDInsight. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Pochopení a používání značek služeb pro Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky týkající se vašeho clusteru Azure HDInsight. Pomocí aliasů Azure Policy v oborech názvů Microsoft. HDInsight a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě v clusteru Azure HDInsight.

Pomocí Azure modrotisky můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení a zásady služby Azure RBAC v rámci jedné definice podrobného plánu. Podrobné sestavování můžete snadno použít pro nová předplatná a prostředí a vyladit řízení a správu prostřednictvím správy verzí.

Jak zobrazit dostupné aliasy Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Postup vytvoření Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: používejte značky pro skupinu zabezpečení sítě (skupin zabezpečení sítě) a další prostředky související se zabezpečením a tokem provozu sítě, které jsou přidruženy k vašemu clusteru Azure HDInsight. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure (CLI) můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak vytvořit virtuální síť:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Vytvoření NSG s konfigurací zabezpečení:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které se vztahují k nasazením Azure HDInsight. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

Jak zobrazit a načíst události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Postup při vytváření výstrah v Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Společnost Microsoft udržuje časové zdroje pro součásti clusteru Azure HDInsight, můžete aktualizovat synchronizaci času pro vaše výpočetní nasazení.

Jak nakonfigurovat časovou synchronizaci pro výpočetní prostředky Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné**materiály: cluster Azure HDInsight můžete připojit k Azure monitor k agregaci dat zabezpečení generovaných clusterem. Využijte vlastní dotazy k detekci a reakci na hrozby v prostředí. 

Jak připojit cluster Azure HDInsight k Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Vytváření vlastních dotazů pro cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné**materiály: Povolte Azure monitor pro cluster HDInsight, nasměrujte ho do pracovního prostoru Log Analytics. Tím se zaprotokolují relevantní informace o clusteru a metriky OS pro všechny uzly clusteru Azure HDInsight.

Jak povolit protokolování pro cluster HDInsight:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Dotaz na protokoly HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné**materiály: zprovoznění clusteru Azure HDInsight pro Azure monitor. Ujistěte se, že použitý pracovní prostor Log Analytics má dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace.

Jak připojit cluster Azure HDInsight k Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Jak nakonfigurovat Log Analytics dobu uchovávání pracovního prostoru:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné**materiály: zprovoznění clusteru Azure HDInsight pro Azure monitor. Ujistěte se, že se v pracovním prostoru Azure Log Analytics používala doba uchování protokolu nastavená v souladu s předpisy pro dodržování předpisů vaší organizace.

Jak připojit cluster Azure HDInsight k Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Jak nakonfigurovat Log Analytics dobu uchovávání pracovního prostoru:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: použití dotazů v pracovním prostoru Azure Log Analytics k dotazování na protokoly služby Azure HDInsight:

Vytváření vlastních dotazů pro clustery Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Pokyny**: pracovní prostor Azure Log Analytics slouží k monitorování a upozorňování na aktivity neobvyklé v protokolech zabezpečení a událostech týkajících se vašeho clusteru Azure HDInsight.

Správa výstrah v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Jak upozornit na data protokolu Log Analytics:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné**materiály: Azure HDInsight se dodává s ClamScan předinstalovaným a povoleným pro Image uzlů clusteru, ale musíte software spravovat a ručně agregovat nebo monitorovat všechny protokoly, které ClamScan vytvoří.

Principy ClamScan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: implementace řešení třetí strany pro protokolování DNS.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Pokyny**: ruční konfigurace protokolování konzoly na základě jednotlivých uzlů.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a Access Control](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné**materiály: Udržujte si záznam místního účtu pro správu, který se vytvoří během zřizování clusteru Azure HDInsight, i u všech dalších účtů, které vytvoříte. Kromě toho, pokud se používá Integrace Azure AD, Azure AD má předdefinované role, které se musí explicitně přiřadit a proto se Queryable. Pomocí modulu Azure AD PowerShell můžete provádět dotazy ad hoc a zjišťovat účty, které jsou členy skupin pro správu.

Kromě toho můžete použít Azure Security Center doporučení pro správu identit a přístupu.

Jak získat roli adresáře ve službě Azure AD pomocí prostředí PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole

Jak získat členy role adresáře ve službě Azure AD pomocí prostředí PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember

Jak monitorovat identitu a přístup pomocí Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: při zřizování clusteru vyžaduje Azure vytvořit nová hesla pro webový portál a přístup k Secure Shell (SSH). Neexistují žádná výchozí hesla, která by bylo možné změnit, ale můžete zadat různá hesla pro přístup SSH a webový portál.

Jak nastavit hesla při zřizování clusteru Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Pokyny**: integrace ověřování pro cluster Azure HDInsight pomocí Azure Active Directory. Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu.

Kromě toho můžete použít Azure Security Center doporučení pro správu identit a přístupu.

Jak integrovat ověřování Azure HDInsight pomocí Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Jak monitorovat identitu a přístup pomocí Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné**materiály: pomocí služby Azure HDInsight ID Broker se přihlaste k clusterům balíček zabezpečení podniku (ESP) pomocí Multi-Factor Authentication bez zadání hesla. Pokud jste se už přihlásili k jiným službám Azure, jako je Azure Portal, můžete se ke svému clusteru Azure HDInsight přihlásit pomocí jednotného přihlašování (SSO).

Jak povolit službu Azure HDInsight ID Broker:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné**materiály: Povolte Azure AD MFA a sledujte Azure Security Center doporučení pro správu identit a přístupu. Clustery Azure HDInsight s nakonfigurovaným Balíček zabezpečení podniku můžou být připojené k doméně, aby uživatelé domény mohli k ověřování s clustery používat svoje doménové přihlašovací údaje a spouštět úlohy s velkými objemy dat. Při ověřování pomocí vícefaktorového ověřování (MFA) se uživatelům zobrazí výzva k zadání druhého ověřovacího faktoru.

Jak povolit vícefaktorové ověřování v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorovat identitu a přístup v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné**materiály: použití privilegovaným přístupem (privilegovaných pracovních stanic) se službou Multi-Factor Authentication (MFA) nakonfigurovaným pro přihlášení k a konfiguraci clusterů Azure HDInsight a souvisejících prostředků.

Další informace o pracovních stanicích s privilegovaným přístupem:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové ověřování v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Doprovodné**materiály: clustery Azure HDInsight s nakonfigurovaným balíček zabezpečení podniku můžou být připojené k doméně, aby uživatelé domény mohli k ověřování použít svoje přihlašovací údaje do domény. Sestavy zabezpečení služby Azure Active Directory (AAD) můžete použít pro generování protokolů a výstrah v případě, že v prostředí AAD dojde k podezřelé nebo nebezpečné aktivitě. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

Jak identifikovat uživatele AAD označené příznakem rizika pro rizikové aktivity:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Jak monitorovat aktivitu identity uživatelů a přístupu v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Doprovodné**materiály: clustery Azure HDInsight s nakonfigurovaným balíček zabezpečení podniku můžou být připojené k doméně, aby uživatelé domény mohli k ověřování použít svoje přihlašovací údaje do domény. Pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

Jak nakonfigurovat pojmenovaná umístění v Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné**materiály: jako centrální ověřování a systém autorizací použijte Azure Active Directory (AAD). AAD chrání data pomocí silného šifrování pro data v klidovém umístění a při přenosu. AAD taky soli, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Clustery Azure HDInsight s nakonfigurovaným Balíček zabezpečení podniku (ESP) se dají připojit k doméně, aby uživatelé domény mohli k ověřování s clustery použít svoje přihlašovací údaje do domény.

Jak vytvořit a nakonfigurovat instanci AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Jak nakonfigurovat Balíček zabezpečení podniku pomocí Azure Active Directory Domain Services ve službě Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: použijte ověřování pomocí Azure Active Directory (AAD) u vašeho clusteru Azure HDInsight. AAD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatele se může pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. 

Jak používat kontroly přístupu Azure identity:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Pokyny**: pomocí Azure Active Directory (AAD) přihlašování a protokolů auditu můžete monitorovat pokusy o přístup k deaktivovaným účtům; Tyto protokoly je možné integrovat do jakéhokoli nástroje SIEM/monitoring třetí strany.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty AAD, odesláním protokolů auditu a přihlašovacích protokolů do pracovního prostoru Azure Log Analytics. Nakonfigurujte požadované výstrahy v pracovním prostoru Azure Log Analytics.

Jak integrovat protokoly aktivit Azure do Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Pokyny**: clustery Azure HDInsight s nakonfigurovaným balíček zabezpečení podniku (ESP) se dají připojit k doméně, aby uživatelé domény mohli k ověřování s clustery použít svoje přihlašovací údaje do domény.  Pomocí funkce zjišťování rizik v Azure Active Directory (AAD) a funkce Ochrana identity můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Kromě toho můžete ingestovat data do služby Azure Sentinel pro další šetření.

Postup zobrazení rizikových přihlášení AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady pro rizika ochrany identity:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné**materiály: není k dispozici; Customer Lockbox ještě není pro Azure HDInsight podporované.

Seznam podporovaných služeb Customer Lockbox: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: použití značek u prostředků souvisejících s nasazeními Azure HDInsight, které pomáhá při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Clustery Azure HDInsight a všechny přidružené účty úložiště by se měly oddělit pomocí virtuální sítě/podsítě, vhodně označit a zabezpečit v rámci skupiny zabezpečení sítě (NSG) nebo Azure Firewall. Data clusteru by se měla zahrnout do zabezpečeného Azure Storage účtu nebo Azure Data Lake Storage (Gen1 nebo Gen2).

Vyberte možnosti úložiště pro cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Postup zabezpečení Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Postup zabezpečení Azure Storage účtů:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné**materiály: u clusterů Azure HDInsight, které ukládají nebo zpracovávají citlivé informace, označte cluster a související prostředky jako citlivé pomocí značek. Pokud chcete snížit riziko ztráty dat prostřednictvím exfiltrace, omezte odchozí síťový provoz pro clustery Azure HDInsight pomocí Azure Firewall.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Postup omezení odchozího provozu pro clustery Azure HDInsight pomocí Azure Firewall:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Pochopení ochrany zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné**materiály: šifrování všech citlivých informací během přenosu. Ujistěte se, že všichni klienti připojující se ke clusteru Azure HDInsight nebo k úložištím dat clusteru (Azure Storage účty nebo Azure Data Lake Storage Gen1/Gen2) můžou vyjednávat TLS 1,2 nebo vyšší. Microsoft Azure prostředky vyjednávat TLS 1,2 ve výchozím nastavení. 

Pochopení Azure Data Lake Storage šifrování při přenosu:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Principy šifrování Azure Storage účtu při přenosu:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Pochopení ochrany zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Doprovodné**materiály: pomocí protokolu ESP (Azure HDInsight balíček zabezpečení podniku) můžete pomocí Apache Ranger vytvářet a spravovat zásady pro nenáročné řízení přístupu k datům uloženým v souborech, složkách, databázích, tabulkách a řádcích a sloupcích. Správce Hadoop může nakonfigurovat řízení přístupu na základě role (RBAC) pro zabezpečení Apache Hive, HBA, Kafka a Sparku pomocí těchto modulů plug-in v Apache Ranger.

Konfigurace zásad RBAC pomocí Apache Ranger vám umožní přidružit oprávnění k roli v organizaci. Tato vrstva abstrakce usnadňuje zajištění, že lidé mají pouze oprávnění potřebná k provádění jejich pracovních odpovědností.

Balíček zabezpečení podniku konfigurace pomocí Azure Active Directory Domain Services ve službě HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Přehled podnikového zabezpečení ve službě Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné**materiály: u clusterů Azure HDInsight, které ukládají nebo zpracovávají citlivé informace, označte cluster a související prostředky jako citlivé pomocí značek. Pro Azure Storage nebo výpočetní prostředky ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Pochopení ochrany zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné**materiály: pokud používáte Azure SQL Database k ukládání Apache Hive a metadat Apache Oozie, zajistěte, aby data SQL zůstala vždy šifrovaná. U Azure Storageových účtů a Data Lake Storage (Gen1 nebo Gen2) doporučujeme, abyste Microsoftu povolili správu šifrovacích klíčů, ale máte možnost spravovat vlastní klíče.

Správa šifrovacích klíčů pro účty Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Vytvoření Azure Data Lake Storage pomocí šifrovacích klíčů spravovaných zákazníkem:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Vysvětlení šifrování pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Jak nakonfigurovat transparentní šifrování dat pro SQL Database pomocí zákaznických klíčů:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: Konfigurace nastavení diagnostiky pro účty Azure Storage přidružené k clusterům Azure HDInsight za účelem monitorování a protokolování všech operací CRUD proti datům clusteru. Povolte auditování pro všechny účty úložiště nebo úložiště Data Lake přidružené k clusteru Azure HDInsight.

Jak povolit další protokolování/auditování pro účet Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Jak povolit další protokolování/auditování pro Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Pokyny**: implementace řešení pro správu ohrožení zabezpečení třetí strany.

Volitelně, pokud máte Rapid7, Qualys nebo jakékoli jiné předplatné platformy pro správu ohrožení zabezpečení, můžete použít akce skriptu k instalaci agentů posouzení ohrožení zabezpečení na uzlech clusteru Azure HDInsight a ke správě uzlů přes příslušný portál.

Ruční instalace agenta Rapid7:

https://insightvm.help.rapid7.com/docs/install

Ruční instalace agenta Qualys:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Jak používat akce skriptů:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné**materiály: pro Image uzlů clusteru byly povoleny automatické aktualizace systému, je však nutné pravidelně restartovat uzly clusteru, aby bylo zajištěno, že budou aktualizace aplikovány.

Microsoft zachovává a aktualizuje základní image uzlů Azure HDInsight.

Jak nakonfigurovat plán oprav operačního systému pro clustery HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

**Pokyny**: pomocí akcí skriptů nebo jiných mechanismů opravte clustery Azure HDInsight. Nově vytvořené clustery budou mít vždycky nejnovější dostupné aktualizace, včetně nejnovějších oprav zabezpečení.

Jak nakonfigurovat plán oprav operačního systému pro clustery Azure HDInsight se systémem Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Jak používat akce skriptů:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné**materiály: implementace řešení pro správu ohrožení zabezpečení třetí strany, které má možnost porovnat prověřování ohrožení zabezpečení v průběhu času. Pokud máte předplatné Rapid7 nebo Qualys, můžete pomocí portálu tohoto dodavatele zobrazit a porovnat kontroly ohrožení zabezpečení zpětného vyhledávání.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné**materiály: Využijte společný program pro vyhodnocování rizik (např. běžný systém vyhodnocování chyb zabezpečení) nebo výchozí hodnocení rizik poskytovaná skenovacím nástrojem třetí strany.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny**: pomocí Azure Resource graphu můžete v rámci předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.), včetně clusterů Azure HDInsight.  Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

Jak vytvářet dotazy pomocí Azure Resource graphu:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit vaše předplatná Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Porozumění službě Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: udržování inventáře schválených prostředků Azure a softwarových titulů

**Doprovodné**materiály: definování seznamu schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure graphu: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Pokyny**: Implementujte řešení třetí strany pro monitorování uzlů clusteru pro neschválené softwarové aplikace.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: pomocí Azure Resource graphu můžete v rámci předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.), včetně clusterů Azure HDInsight.  Odeberte všechny neschválené prostředky Azure, které zjistíte. Pro uzly clusteru Azure HDInsight implementujte řešení třetí strany, které vám umožní odebrat nebo upozornit na neschválený software.

Jak vytvářet dotazy pomocí Azure graphu:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné**materiály: u uzlů clusteru Azure HDInsight implementujte řešení třetích stran, které zabraňuje neoprávněnému softwaru v provádění.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku s Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné**materiály: u uzlů clusteru Azure HDInsight implementujte řešení třetí strany, které zabrání v provádění neautorizovaných typů souborů.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: Omezte schopnost uživatelů pracovat se správcem prostředků Azure prostřednictvím skriptů

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.

Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné**materiály: nepoužitelné; Neplatí to pro Azure HDInsight, protože uživatelé (kteří nejsou správci) clusteru nepotřebují přístup k jednotlivým uzlům ke spouštění úloh. Správce clusteru má kořenový přístup ke všem uzlům clusteru.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné**materiály: nepoužitelné; Srovnávací test je určený pro služby Azure apps nebo výpočetní prostředky hostující webové aplikace.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. HDInsight k vytvoření vlastních zásad pro auditování nebo vymáhání konfigurace sítě vašeho clusteru HDInsight.

Jak zobrazit dostupné aliasy Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné**materiály: image operačního systému Azure HDInsight spravované a udržované Microsoftem. Zákazník zodpovědný za implementaci zabezpečených konfigurací pro operační systém uzlů clusteru 


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné**materiály: použijte Azure Policy [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení pro clustery Azure HDInsight a související prostředky.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Pochopení Azure Policych účinků:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné**materiály: image operačního systému Azure HDInsight spravované a udržované Microsoftem. Zákazník zodpovědný za implementaci konfigurace stavu na úrovni operačního systému.


**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné**materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

[Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Dokumentace k Azure Repos](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné**materiály: nepoužitelné; vlastní image neplatí pro Azure HDInsight.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: netýká se

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. HDInsight k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Pokyny**: Implementujte řešení třetí strany a udržujte požadovaný stav pro operační systémy uzlu clusteru.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. HDInsight k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace clusteru HDInsight.

[Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

[Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Pokyny**: Implementujte řešení třetí strany a sledujte stav operačního systému vašeho uzlu clusteru.

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné**materiály: Azure HDInsight zahrnuje podporu BRING Your Own Key (BYOK) pro Apache Kafka. Tato funkce vám umožní vlastnit a spravovat klíče používané k šifrování neaktivních dat.

Všechny spravované disky ve službě Azure HDInsight jsou chráněné pomocí šifrování služby Azure Storage (SSE). Ve výchozím nastavení se data na těchto discích šifrují pomocí klíčů spravovaných Microsoftem. Pokud povolíte BYOK, poskytnete šifrovací klíč pro Azure HDInsight, který bude používat a spravovat pomocí Azure Key Vault.

Key Vault taky můžete použít s nasazeními Azure HDInsight ke správě klíčů pro úložiště clusteru (účty Azure Storage a Azure Data Lake Storage).

Jak přenést vlastní klíč pro Apache Kafka v Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Správa šifrovacích klíčů pro účty Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné**materiály: spravované identity se dají použít ve službě Azure HDInsight, které svým clusterům umožní přístup k Azure Active Directory doménovým službám, přístupu Azure Key Vault nebo k souborům v Azure Data Lake Storage Gen2.

Pochopení spravovaných identit pomocí Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Doprovodné**materiály: Pokud používáte jakýkoliv kód související s nasazením Azure HDInsight, můžete implementovat kontrolu přihlašovacích údajů, abyste mohli identifikovat přihlašovací údaje v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

Jak nastavit skener přihlašovacích údajů:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné**materiály: Azure HDInsight se dodává s ClamScan předinstalovaným a povoleným pro Image uzlů clusteru, ale musíte software spravovat a ručně agregovat nebo monitorovat všechny protokoly, které ClamScan vytvoří.

Vysvětlení ClamScan pro Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure, ale neběží na zákaznickém obsahu.

Předem Prohledejte všechny nahrané soubory do všech prostředků Azure souvisejících s nasazením clusteru Azure HDInsight, jako je Data Lake Storage, Blob Storage atd. Společnost Microsoft nemá přístup k zákaznickým datům v těchto instancích.

Pochopení ochrany proti malwaru Microsoftu pro Azure Cloud Services a Virtual Machines:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné**materiály: Azure HDInsight se dodává s ClamScan předinstalovaným a povoleným pro Image uzlů clusteru. ClamScan bude provádět automatické aktualizace modulu a definic, ale agregace a správa protokolů bude nutné provést ručně.

Vysvětlení ClamScan pro Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: při použití účtu Azure Storage pro úložiště dat clusteru HDInsight vyberte odpovídající možnost redundance (LRS, ZRS, GRS, RA-GRS).  Při použití Azure SQL Database pro úložiště dat clusteru Azure HDInsight nakonfigurujte aktivní geografickou replikaci.

Jak nakonfigurovat redundanci úložiště pro účty Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Jak nakonfigurovat redundanci pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Pokyny**: při použití účtu Azure Storage pro úložiště dat clusteru Azure HDInsight vyberte odpovídající možnost redundance (LRS, ZRS, GRS, RA-GRS). Pokud používáte Azure Key Vault pro jakoukoli část nasazení Azure HDInsight, ujistěte se, že jsou vaše klíče zálohované.

Vyberte možnosti úložiště pro cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Jak nakonfigurovat redundanci úložiště pro účty Azure Storage:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Postup zálohování klíčů Key Vault v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Pokyny**: Pokud se pro nasazení Azure HDInsight používá Azure Key Vault, testovací obnovení zálohovaných spravovaných klíčů zákazníka.

Jak přenést vlastní klíč pro Apache Kafka v Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Postup obnovení klíčů trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Pokyny**: Pokud se pro nasazení Azure HDInsight používá Azure Key Vault, povolte Soft-Delete v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

Postup povolení Soft-Delete v Azure Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Doprovodné**materiály: Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují role pracovníků a také fáze zpracování a správy incidentů.

Postup konfigurace automatizace pracovních postupů v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Pokyny**: Security Center přiřadí závažnosti výstrahám, které vám pomůžou určit prioritu pořadí, ve kterém se zúčastníte jednotlivých výstrah, takže pokud dojde k ohrožení určitého prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán. Přečtěte si téma publikace NIST: Průvodce pro testování, školení a cvičení programů pro plány a možnosti IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba.

Jak nastavit Azure Security Center kontakt zabezpečení:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné**materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat ověřovací data výstrah.

Postup konfigurace průběžného exportu:

https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech důležitých zjištění zabezpečení do 60 dnů.

**Doprovodné**materiály: řiďte se prosím pravidly zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Další informace o strategii a provádění testování na základě červeného seskupování a testování průniku na webu pomocí spravované cloudové infrastruktury, služeb a aplikací Microsoftu najdete tady: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
