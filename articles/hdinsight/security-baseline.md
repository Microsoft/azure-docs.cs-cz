---
title: Základní hodnoty zabezpečení Azure pro HDInsight
description: Základní hodnoty zabezpečení služby HDInsight poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf2360bda55735aa8ef4258da5ae47f673f4d71b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738918"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Základní hodnoty zabezpečení Azure pro HDInsight

Tato základní hodnota zabezpečení se vztahuje na pokyny od [verze Azure Security test 1,0](../security/benchmarks/overview-v1.md) na HDInsight. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah se seskupuje pomocí **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů týkajících se služby HDInsight. **Ovládací prvky** , které se nevztahují na HDInsight, se vyloučily.

 
Pokud chcete zjistit, jak se HDInsight kompletně mapuje na test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení HDInsight](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: hraniční zabezpečení ve službě Azure HDInsight se dosahuje pomocí virtuálních sítí. Podnikový správce může vytvořit cluster v rámci virtuální sítě a použít skupinu zabezpečení sítě (NSG) k omezení přístupu k virtuální síti. Pouze povolené IP adresy v příchozích pravidlech skupiny zabezpečení sítě budou moci komunikovat s clusterem Azure HDInsight. Tato konfigurace poskytuje hraniční zabezpečení. Všechny clustery nasazené ve virtuální síti budou mít taky privátní koncový bod, který se přeloží na privátní IP adresu uvnitř Virtual Network pro privátní přístup HTTP ke branám clusteru.

Pokud chcete snížit riziko ztráty dat prostřednictvím exfiltrace, omezte odchozí síťový provoz pro clustery Azure HDInsight pomocí Azure Firewall.

- [Jak nasadit Azure HDInsight v rámci Virtual Network a zabezpečit pomocí skupiny zabezpečení sítě](hdinsight-create-virtual-network.md)

- [Postup omezení odchozího provozu pro clustery Azure HDInsight pomocí Azure Firewall](hdinsight-restrict-outbound-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: použijte Azure Security Center a opravte doporučení k ochraně sítě pro virtuální síť, podsíť a skupinu zabezpečení sítě, která se používá k zabezpečení clusteru Azure HDInsight. Povolte protokoly toku NSG (Network Security Group) a odešlete protokoly do účtu Azure Storage k auditu provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Azure Log Analytics a používat Azure Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Azure Analýza provozu je schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a odhalit síťové opravy chyb.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Jak povolit a používat Azure Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: u ochrany před útoky DDoS povolte službu Azure DDoS Standard Protection ve virtuální síti, ve které je vaše Azure HDInsight nasazené. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

- [Jak nakonfigurovat DDoS Protection](/azure/virtual-network/manage-ddos-protection)

- [Pochopení Azure Security Center integrované analýzy hrozeb](/azure/security-center/security-center-alerts-service-layer)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: povolení protokolů Flow skupiny zabezpečení sítě (NSG) pro NSG připojené k podsíti používané k ochraně clusteru Azure HDInsight. Zaznamenejte NSG Flow do účtu Azure Storage pro generování záznamů toků. Pokud se to vyžaduje pro prošetření aktivity neobvyklé, povolte zachytávání paketů služby Azure Network Watcher.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: požadavek může splnit ID řízení zabezpečení Azure 1,1; Nasaďte cluster Azure HDInsight do virtuální sítě a zabezpečte ji pomocí skupiny zabezpečení sítě (NSG).

K dispozici je několik závislostí pro Azure HDInsight, které vyžadují příchozí provoz. Příchozí provoz správy nejde odeslat přes zařízení brány firewall. Zdrojové adresy pro požadovaný provoz správy jsou známé a publikované. Vytvořte pravidla skupiny zabezpečení sítě s těmito informacemi, abyste mohli povolit provoz jenom z důvěryhodných umístění, a zabezpečit příchozí provoz do clusterů.

Pokud chcete snížit riziko ztráty dat prostřednictvím exfiltrace, omezte odchozí síťový provoz pro clustery Azure HDInsight pomocí Azure Firewall.

- [Postup nasazení služby HDInsight v rámci Virtual Network a zabezpečení pomocí skupiny zabezpečení sítě](hdinsight-create-virtual-network.md)

- [Pochopení závislostí HDInsight a využití brány firewall](hdinsight-restrict-outbound-traffic.md)

- [IP adresy pro správu služby HDInsight](hdinsight-management-ip-addresses.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služby virtuální sítě můžete definovat řízení přístupu k síti u skupin zabezpečení sítě (NSG), které jsou připojené k podsíti, ve které je nasazený cluster Azure HDInsight. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Pochopení a používání značek služeb pro Azure HDInsight](/azure/virtual-network/security-overview#service-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky týkající se vašeho clusteru Azure HDInsight. Pomocí aliasů Azure Policy v oborech názvů Microsoft. HDInsight a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě v clusteru Azure HDInsight.

Pomocí plánů Azure můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení a zásady služby Azure RBAC v rámci jedné definice podrobného plánu. Podrobné sestavování můžete snadno použít pro nová předplatná a prostředí a vyladit řízení a správu prostřednictvím správy verzí.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro skupinu zabezpečení sítě (skupin zabezpečení sítě) a další prostředky související se zabezpečením a tokem provozu sítě, které jsou přidruženy k vašemu clusteru Azure HDInsight. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure (CLI) můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

- [Jak vytvořit virtuální síť](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které se vztahují k nasazením Azure HDInsight. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

- [Vytváření výstrah v Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: cluster Azure HDInsight můžete připojit k Azure monitor k agregaci dat zabezpečení generovaných clusterem. Využijte vlastní dotazy k detekci a reakci na hrozby v prostředí. 

- [Jak připojit cluster Azure HDInsight k Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Vytvoření vlastních dotazů pro cluster Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: Povolte Azure monitor pro cluster HDInsight, nasměrujte ho do pracovního prostoru Log Analytics. Tím se zaprotokolují relevantní informace o clusteru a metriky OS pro všechny uzly clusteru Azure HDInsight.

- [Postup povolení protokolování pro cluster HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Dotazování protokolů HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: zprovoznění clusteru Azure HDInsight pro Azure monitor. Ujistěte se, že použitý pracovní prostor Log Analytics má dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace.

- [Jak připojit cluster Azure HDInsight k Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](/azure/azure-monitor/platform/manage-cost-storage)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: zprovoznění clusteru Azure HDInsight pro Azure monitor. Ujistěte se, že se v pracovním prostoru Azure Log Analytics používala doba uchování protokolu nastavená v souladu s předpisy pro dodržování předpisů vaší organizace.

- [Jak připojit cluster Azure HDInsight k Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](/azure/azure-monitor/platform/manage-cost-storage)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: použití dotazů v pracovním prostoru Azure Log Analytics k dotazování na protokoly služby Azure HDInsight:

- [Vytváření vlastních dotazů pro clustery Azure HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Pokyny**: pracovní prostor Azure Log Analytics slouží k monitorování a upozorňování na aktivity neobvyklé v protokolech zabezpečení a událostech týkajících se vašeho clusteru Azure HDInsight.

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: Azure HDInsight se dodává s ClamScan předinstalovaným a povoleným pro Image uzlů clusteru, ale musíte software spravovat a ručně agregovat nebo monitorovat všechny protokoly, které ClamScan vytvoří.

- [Principy ClamScan](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: implementace řešení třetí strany pro protokolování DNS.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Pokyny**: ruční konfigurace protokolování konzoly na základě jednotlivých uzlů.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Udržujte si záznam místního účtu pro správu, který se vytvoří během zřizování clusteru Azure HDInsight, i u všech dalších účtů, které vytvoříte. Kromě toho, pokud se používá Integrace Azure Active Directory (Azure AD), Azure AD má předdefinované role, které se musí explicitně přiřadit a proto se Queryable. Pomocí modulu Azure AD PowerShell můžete provádět dotazy ad hoc a zjišťovat účty, které jsou členy skupin pro správu.

Kromě toho můžete použít Azure Security Center doporučení pro správu identit a přístupu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Jak monitorovat identitu a přístup pomocí Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: při zřizování clusteru vyžaduje Azure vytvořit nová hesla pro webový portál a přístup k Secure Shell (SSH). Neexistují žádná výchozí hesla, která by bylo možné změnit, ale můžete zadat různá hesla pro přístup SSH a webový portál.

- [Nastavení hesel při zřizování clusteru Azure HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Pokyny**: integrace ověřování pro cluster Azure HDInsight pomocí Azure Active Directory (Azure AD). Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu.

Kromě toho můžete použít Azure Security Center doporučení pro správu identit a přístupu.

- [Jak integrovat ověřování Azure HDInsight s Azure AD](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Jak monitorovat identitu a přístup pomocí Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné** materiály: pomocí služby Azure HDInsight ID Broker se přihlaste k clusterům balíček zabezpečení podniku (ESP) pomocí vícefaktorového ověřování bez zadání hesla. Pokud jste se už přihlásili k jiným službám Azure, jako je Azure Portal, můžete se ke svému clusteru Azure HDInsight přihlásit pomocí jednotného přihlašování (SSO).

- [Jak povolit službu Azure HDInsight ID Broker](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Azure Security Center doporučení pro správu identit a přístupu. Clustery Azure HDInsight s nakonfigurovaným Balíček zabezpečení podniku můžou být připojené k doméně, aby uživatelé domény mohli k ověřování s clustery používat svoje doménové přihlašovací údaje a spouštět úlohy s velkými objemy dat. Při ověřování s povoleným vícefaktorového ověřováním se uživatelům zobrazí výzva k zadání druhého ověřovacího faktoru.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné** materiály: Používejte privilegovaným přístupem (Privileged Access Workstations) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení k a konfiguraci clusterů Azure HDInsight a souvisejících prostředků.

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: clustery Azure HDInsight s nakonfigurovaným balíček zabezpečení podniku můžou být připojené k doméně, aby uživatelé domény mohli k ověřování použít svoje přihlašovací údaje do domény. Sestavy zabezpečení služby Azure Active Directory (Azure AD) můžete použít pro generování protokolů a výstrah v případě, že dojde k podezřelé nebo nebezpečné aktivitě v prostředí Azure AD. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Jak monitorovat aktivitu identity uživatelů a přístupu v Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Doprovodné** materiály: clustery Azure HDInsight s nakonfigurovaným balíček zabezpečení podniku můžou být připojené k doméně, aby uživatelé domény mohli k ověřování použít svoje přihlašovací údaje do domény. Pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Clustery Azure HDInsight s nakonfigurovaným Balíček zabezpečení podniku (ESP) se dají připojit k doméně, aby uživatelé domény mohli k ověřování s clustery použít svoje přihlašovací údaje do domény.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Postup konfigurace Balíček zabezpečení podniku s využitím Azure AD Domain Services ve službě Azure HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: používejte ověřování pomocí Azure Active Directory (Azure AD) s clusterem Azure HDInsight. Azure AD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatele se může pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Pokyny**: pomocí Azure Active Directory (Azure AD) přihlašování a protokolů auditu můžete monitorovat pokusy o přístup k deaktivovaným účtům. Tyto protokoly je možné integrovat do jakéhokoli nástroje SIEM/monitoring třetí strany.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD, odesláním protokolů auditu a přihlašovacích protokolů do pracovního prostoru Azure Log Analytics. Nakonfigurujte požadované výstrahy v pracovním prostoru Azure Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: clustery Azure HDInsight s nakonfigurovaným balíček zabezpečení podniku (ESP) se dají připojit k doméně, aby uživatelé domény mohli k ověřování s clustery použít svoje přihlašovací údaje do domény. Pomocí Azure Active Directory (Azure AD) detekce rizik a Identity Protection můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Kromě toho můžete ingestovat data do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: není k dispozici; Customer Lockbox ještě není pro Azure HDInsight podporované.

- [Seznam podporovaných služeb Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: použití značek u prostředků souvisejících s nasazeními Azure HDInsight, které pomáhá při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Clustery Azure HDInsight a všechny přidružené účty úložiště by se měly oddělit pomocí virtuální sítě/podsítě, vhodně označit a zabezpečit v rámci skupiny zabezpečení sítě (NSG) nebo Azure Firewall. Data clusteru by se měla zahrnout do zabezpečeného Azure Storage účtu nebo Azure Data Lake Storage (Gen1 nebo Gen2).

- [Volba možností úložiště pro cluster Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)

- [Postup zabezpečení Azure Data Lake Storage](../data-lake-store/data-lake-store-security-overview.md)

- [Postup zabezpečení účtů Azure Storage](/azure/storage/common/storage-security-guide)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: u clusterů Azure HDInsight, které ukládají nebo zpracovávají citlivé informace, označte cluster a související prostředky jako citlivé pomocí značek. Pokud chcete snížit riziko ztráty dat prostřednictvím exfiltrace, omezte odchozí síťový provoz pro clustery Azure HDInsight pomocí Azure Firewall.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Postup omezení odchozího provozu pro clustery Azure HDInsight pomocí Azure Firewall](hdinsight-restrict-outbound-traffic.md)

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: šifrování všech citlivých informací během přenosu. Ujistěte se, že všichni klienti připojující se ke clusteru Azure HDInsight nebo k úložištím dat clusteru (Azure Storage účty nebo Azure Data Lake Storage Gen1/Gen2) můžou vyjednávat TLS 1,2 nebo vyšší. Microsoft Azure prostředky vyjednávat TLS 1,2 ve výchozím nastavení. 

- [Pochopení šifrování Azure Data Lake Storage při přenosu](../data-lake-store/data-lake-store-security-overview.md)

- [Principy šifrování Azure Storage účtu při přenosu](../storage/blobs/security-recommendations.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC. 

**Doprovodné** materiály: pomocí protokolu ESP (Azure HDInsight balíček zabezpečení podniku) můžete pomocí Apache Ranger vytvářet a spravovat zásady pro nenáročné řízení přístupu k datům uloženým v souborech, složkách, databázích, tabulkách a řádcích a sloupcích. Správce Hadoop může nakonfigurovat řízení přístupu na základě role (RBAC) pro zabezpečení Apache Hive, HBA, Kafka a Sparku pomocí těchto modulů plug-in v Apache Ranger.

Konfigurace zásad RBAC pomocí Apache Ranger vám umožní přidružit oprávnění k roli v organizaci. Tato vrstva abstrakce usnadňuje zajištění, že lidé mají pouze oprávnění potřebná k provádění jejich pracovních odpovědností.

- [Konfigurace Balíček zabezpečení podniku s využitím služeb domény Azure Active Directory (Azure AD) v HDInsight](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Přehled podnikového zabezpečení ve službě Azure HDInsight](domain-joined/hdinsight-security-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: u clusterů Azure HDInsight, které ukládají nebo zpracovávají citlivé informace, označte cluster a související prostředky jako citlivé pomocí značek. Pro Azure Storage nebo výpočetní prostředky ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: pokud používáte Azure SQL Database k ukládání Apache Hive a metadat Apache Oozie, zajistěte, aby data SQL zůstala vždy šifrovaná. U Azure Storageových účtů a Data Lake Storage (Gen1 nebo Gen2) doporučujeme, abyste Microsoftu povolili správu šifrovacích klíčů, ale máte možnost spravovat vlastní klíče.

- [Správa šifrovacích klíčů pro účty Azure Storage](/azure/storage/common/storage-encryption-keys-portal)

- [Vytvoření Azure Data Lake Storage pomocí šifrovacích klíčů spravovaných zákazníkem](../data-lake-store/data-lake-store-get-started-portal.md)

- [Vysvětlení šifrování pro Azure SQL Database](/azure/sql-database/sql-database-technical-overview#data-encryption)

- [Postup konfigurace transparentní šifrování dat pro SQL Database pomocí klíčů spravovaných zákazníkem](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: Konfigurace nastavení diagnostiky pro účty Azure Storage přidružené k clusterům Azure HDInsight za účelem monitorování a protokolování všech operací CRUD proti datům clusteru. Povolte auditování pro všechny účty úložiště nebo úložiště Data Lake přidružené k clusteru Azure HDInsight.

- [Jak povolit další protokolování/auditování pro účet Azure Storage](/azure/storage/common/storage-monitor-storage-account)

- [Jak povolit další protokolování/auditování pro Azure Data Lake Storage](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Pokyny**: implementace řešení pro správu ohrožení zabezpečení třetí strany.

Volitelně, pokud máte Rapid7, Qualys nebo jakékoli jiné předplatné platformy pro správu ohrožení zabezpečení, můžete použít akce skriptu k instalaci agentů posouzení ohrožení zabezpečení na uzlech clusteru Azure HDInsight a ke správě uzlů přes příslušný portál.

- [Ruční instalace agenta Rapid7](https://insightvm.help.rapid7.com/docs/install)

- [Ruční instalace agenta Qualys](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [Použití akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: pro Image uzlů clusteru byly povoleny automatické aktualizace systému, je však nutné pravidelně restartovat uzly clusteru, aby bylo zajištěno, že budou aktualizace aplikovány.

Microsoft zachovává a aktualizuje základní image uzlů Azure HDInsight.

- [Jak nakonfigurovat plán oprav operačního systému pro clustery HDInsight](hdinsight-os-patching.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizovaného řešení pro správu oprav pro softwarové tituly třetích stran

**Pokyny**: pomocí akcí skriptů nebo jiných mechanismů opravte clustery Azure HDInsight. Nově vytvořené clustery budou mít vždycky nejnovější dostupné aktualizace, včetně nejnovějších oprav zabezpečení.

- [Jak nakonfigurovat plán oprav operačního systému pro clustery Azure HDInsight se systémem Linux](hdinsight-os-patching.md)

- [Použití akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: implementace řešení pro správu ohrožení zabezpečení třetí strany, které má možnost porovnat prověřování ohrožení zabezpečení v průběhu času. Pokud máte předplatné Rapid7 nebo Qualys, můžete pomocí portálu tohoto dodavatele zobrazit a porovnat kontroly ohrožení zabezpečení zpětného vyhledávání.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: Využijte společný program pro vyhodnocování rizik (např. běžný systém vyhodnocování chyb zabezpečení) nebo výchozí hodnocení rizik poskytovaná skenovacím nástrojem třetí strany.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat na všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.), včetně clusterů Azure HDInsight v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

I když je možné zjistit klasické prostředky Azure prostřednictvím Azure Resource graphu, důrazně se doporučuje vytvořit a použít prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: definování seznamu schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Pokyny**: Implementujte řešení třetí strany pro monitorování uzlů clusteru pro neschválené softwarové aplikace.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat na všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.), včetně clusterů Azure HDInsight v rámci vašich předplatných.  Odeberte všechny neschválené prostředky Azure, které zjistíte. Pro uzly clusteru Azure HDInsight implementujte řešení třetí strany, které vám umožní odebrat nebo upozornit na neschválený software.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: u uzlů clusteru Azure HDInsight implementujte řešení třetích stran, které zabraňuje neoprávněnému softwaru v provádění.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků

- Povolené typy prostředků

Další informace najdete v následujících referenčních materiálech:

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: u uzlů clusteru Azure HDInsight implementujte řešení třetí strany, které zabrání v provádění neautorizovaných typů souborů.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. HDInsight k vytvoření vlastních zásad pro auditování nebo vymáhání konfigurace sítě vašeho clusteru HDInsight.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: image operačního systému Azure HDInsight spravované a udržované Microsoftem. Zákazník zodpovědný za implementaci zabezpečených konfigurací pro operační systém uzlů clusteru

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení pro clustery Azure HDInsight a související prostředky.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: image operačního systému Azure HDInsight spravované a udržované Microsoftem. Zákazník zodpovědný za implementaci konfigurace stavu na úrovni operačního systému.

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

- [Kurz k Azure Repos Git](/azure/devops/repos/git/gitworkflow)

- [Dokumentace k Azure Repos](/azure/devops/repos/index)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. HDInsight k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Pokyny**: Implementujte řešení třetí strany a udržujte požadovaný stav pro operační systémy uzlu clusteru.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. HDInsight k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace clusteru HDInsight.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Pokyny**: Implementujte řešení třetí strany a sledujte stav operačního systému vašeho uzlu clusteru.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: Azure HDInsight zahrnuje podporu BRING Your Own Key (BYOK) pro Apache Kafka. Tato funkce vám umožní vlastnit a spravovat klíče používané k šifrování neaktivních dat.

Všechny spravované disky ve službě Azure HDInsight jsou chráněné pomocí šifrování služby Azure Storage (SSE). Ve výchozím nastavení se data na těchto discích šifrují pomocí klíčů spravovaných Microsoftem. Pokud povolíte BYOK, poskytnete šifrovací klíč pro Azure HDInsight, který bude používat a spravovat pomocí Azure Key Vault.

Key Vault taky můžete použít s nasazeními Azure HDInsight ke správě klíčů pro úložiště clusteru (účty Azure Storage a Azure Data Lake Storage).

- [Postup převedení vlastního klíče pro Apache Kafka v Azure HDInsight](/azure/hdinsight/kafka/apache-kafka-byok)

- [Správa šifrovacích klíčů pro účty Azure Storage](/azure/storage/common/storage-encryption-keys-portal)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: spravované identity lze použít ve službě Azure HDInsight, aby mohly vaše clustery přistupovat ke službě Azure Active Directory (Azure AD) Domain Services, přístupu Azure Key Vault nebo k souborům v Azure Data Lake Storage Gen2.

- [Pochopení spravovaných identit pomocí Azure HDInsight](hdinsight-managed-identities.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Doprovodné** materiály: Pokud používáte jakýkoliv kód související s nasazením Azure HDInsight, můžete implementovat kontrolu přihlašovacích údajů, abyste mohli identifikovat přihlašovací údaje v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použijte centrálně spravovaný antimalwarový software

**Doprovodné** materiály: Azure HDInsight se dodává s ClamScan předinstalovaným a povoleným pro Image uzlů clusteru, ale musíte software spravovat a ručně agregovat nebo monitorovat všechny protokoly, které ClamScan vytvoří.

- [Principy ClamScan pro Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure, ale neběží na zákaznickém obsahu.

Předem Prohledejte všechny nahrané soubory do všech prostředků Azure souvisejících s nasazením clusteru Azure HDInsight, jako je Data Lake Storage, Blob Storage atd. Společnost Microsoft nemá přístup k zákaznickým datům v těchto instancích.

- [Pochopení Microsoft antimalwaru pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: Azure HDInsight se dodává s ClamScan předinstalovaným a povoleným pro Image uzlů clusteru. ClamScan bude provádět automatické aktualizace modulu a definic, ale agregace a správa protokolů bude nutné provést ručně.

- [Principy ClamScan pro Azure Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: při použití účtu Azure Storage pro úložiště dat clusteru HDInsight vyberte odpovídající možnost redundance (LRS, ZRS, GRS, RA-GRS).  Při použití Azure SQL Database pro úložiště dat clusteru Azure HDInsight nakonfigurujte aktivní geografickou replikaci.

- [Jak nakonfigurovat redundanci úložiště pro účty Azure Storage](../storage/common/storage-redundancy.md)

- [Jak nakonfigurovat redundanci pro Azure SQL Database](/azure/sql-database/sql-database-active-geo-replication)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: při použití účtu Azure Storage pro úložiště dat clusteru Azure HDInsight vyberte odpovídající možnost redundance (LRS, ZRS, GRS, RA-GRS). Pokud používáte Azure Key Vault pro jakoukoli část nasazení Azure HDInsight, ujistěte se, že jsou vaše klíče zálohované.

- [Volba možností úložiště pro cluster Azure HDInsight](hdinsight-hadoop-compare-storage-options.md)

- [Jak nakonfigurovat redundanci úložiště pro účty Azure Storage](../storage/common/storage-redundancy.md)

- [Postup zálohování klíčů Key Vault v Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny**: Pokud se pro nasazení Azure HDInsight používá Azure Key Vault, proveďte test obnovení zálohovaných klíčů spravovaných zákazníkem.

- [Postup převedení vlastního klíče pro Apache Kafka v Azure HDInsight](/azure/hdinsight/kafka/apache-kafka-byok)

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: Pokud se pro nasazení Azure HDInsight používá Azure Key Vault, povolte pro ochranu klíčů před náhodným nebo úmyslným odstraněním obnovitelné odstranění v Key Vault.

- [Jak povolit obnovitelné odstranění Azure Key Vault](/azure/key-vault/key-vault-ovw-soft-delete)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: vývoj Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování incidentů a správu, které se mají zjišťovat, na kontrolu po jednotlivých událostech. 

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Pomoc při tvorbě vlastního plánu odpovědí na incidenty najdete v příručce pro zpracování incidentů v počítači s NIST.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Pokyny**: Security Center přiřadí závažnosti výstrahám, které vám pomůžou určit prioritu pořadí, ve kterém se zúčastníte jednotlivých výstrah, takže pokud dojde k ohrožení určitého prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem. 

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
