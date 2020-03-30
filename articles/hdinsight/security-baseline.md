---
title: Základ zabezpečení Azure pro HDInsight
description: Základ zabezpečení Azure pro HDInsight
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: eddfcacd01a67fffa8e3e992e021ed1771d25944
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471332"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Základ zabezpečení Azure pro HDInsight

Azure Security Baseline pro HDInsight obsahuje doporučení, která vám pomohou zlepšit stav zabezpečení vašeho nasazení.

Základní informace pro tyto služby jsou z azure [security benchmark verze 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure s našimi pokyny pro osvědčené postupy.

Další informace naleznete v [tématu Přehled směrného plánu zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace naleznete v [tématu Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

**Pokyny:** Zabezpečení perimetru v Azure HDInsight je dosaženo prostřednictvím virtuálních sítí. Podnikový správce může vytvořit cluster uvnitř virtuální sítě a použít skupinu zabezpečení sítě (NSG) k omezení přístupu k virtuální síti. S clusterem Azure HDInsight budou moct komunikovat jenom povolené IP adresy v pravidlech skupiny zabezpečení sítě. Tato konfigurace poskytuje zabezpečení obvodu. Všechny clustery nasazené ve virtuální síti budou mít také privátní koncový bod, který se překládá na privátní IP adresu uvnitř virtuální sítě pro privátní http přístup k bránám clusteru.


Jak nasadit Azure HDInsight v rámci virtuální sítě a zabezpečené se skupinou zabezpečení sítě:

https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu sítí, podsítí a síťových rozhraní

**Pokyny:** Použijte Azure Security Center a navažte doporučení ochrany sítě pro virtuální síť, podsíť a skupinu zabezpečení sítě, která se používá k zabezpečení clusteru Azure HDInsight. Povolte protokoly toku skupiny zabezpečení sítě (NSG) a odesílejte protokoly do účtu úložiště Azure do auditování provozu. Protokoly toku nsg můžete také odesílat do pracovního prostoru Azure Log Analytics a používat Azure Traffic Analytics k poskytování přehledů o toku provozu ve vašem cloudu Azure. Některé výhody Azure Traffic Analytics jsou možnost vizualizovat aktivitu v síti a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzorce toku provozu a určit chybné konfigurace sítě.


Povolení protokolů toku nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Jak povolit a používat Azure Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Seznamte se se zabezpečením sítě poskytovaným službou Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

**Pokyny**: Nepoužije se; benchmark je určen pro Službu Azure Apps Service nebo výpočetní prostředky hostující webové aplikace.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

**Pokyny:** Pro ochranu před útoky DDoS povolte ochranu Azure DDoS Standard ve virtuální síti, kde se nasazuje váš Azure HDInsight. Pomocí integrované analýzy hrozeb Azure Security Center můžete odepřít komunikaci se známými škodlivými nebo nepoužívanými internetovými IP adresami.


Jak nakonfigurovat ochranu Před sdos:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Seznamte se s integrovanou analýzou hrozeb Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

**Pokyny:** Povolte protokoly toku skupiny zabezpečení sítě (NSG) pro skupinu zabezpečení sítě připojenou k podsíti, která se používá k ochraně clusteru Azure HDInsight. Zaznamenejte protokoly toku nsg do účtu úložiště Azure pro generování záznamů toku. Pokud je to nutné pro zkoumání neobvyklé aktivity, povolte zachytávání paketů Azure Network Watcher.


Povolení protokolů toku nsg:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Jak povolit sledování sítě:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Nasazení síťových systémů detekce vniknutí/prevence vniknutí (IDS/IPS)

**Pokyny:** Požadavek může být splněna Azure řízení zabezpečení ID 1.1; Nasaďte cluster Azure HDInsight do virtuální sítě a zabezpečte pomocí skupiny zabezpečení sítě (NSG).

Existuje několik závislostí pro Azure HDInsight, které vyžadují příchozí provoz. Příchozí provoz správy nelze odeslat prostřednictvím zařízení brány firewall. Zdrojové adresy pro požadovaný provoz správy jsou známy a publikovány. Vytvořte pravidla skupiny zabezpečení sítě s těmito informacemi, která umožní provoz pouze z důvěryhodných umístění a zajistí příchozí provoz do clusterů.

Jak nasadit HDInsight v rámci virtuální sítě a zabezpečení se skupinou zabezpečení sítě:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Seznamte se s závislostmi HDInsight a používáním brány firewall:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

IP adresy hdinsight pro správu:https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

**Pokyny**: Nepoužije se; benchmark je určen pro Službu Azure Apps Service nebo výpočetní prostředky hostující webové aplikace.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

**Pokyny**: Pomocí značek virtuální síťové služby definujte ovládací prvky přístupu k síti ve skupinách zabezpečení sítě (NSG), které jsou připojené k podsíti, ve které je váš cluster Azure HDInsight nasazený. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. apimanagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.


Pochopení a používání značek služeb pro Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro síťové prostředky související s vaším clusterem Azure HDInsight. Pomocí aliasů zásad Azure v oborech názvů Microsoft.HDInsight a Microsoft.Network můžete vytvořit vlastní zásady pro auditování nebo vynucení síťové konfigurace vašeho clusteru Azure HDInsight.


Azure Blueprints můžete také použít ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte artefakty prostředí klíčů, jako jsou šablony Azure Resource Manager, ovládací prvky RBAC a zásady, v jediné definici podrobného plánu. Snadno použít podrobný plán pro nová předplatná a prostředí a doladit řízení a správu prostřednictvím správy verzí.


Jak zobrazit dostupné aliasy zásad Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Jak vytvořit Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentovat pravidla konfigurace provozu

**Pokyny:** Použití značek pro skupinu zabezpečení sítě (NSGs) a další prostředky související se zabezpečením sítě a tokem provozu, které jsou přidruženy k vašemu clusteru Azure HDInsight. Pro jednotlivá pravidla sítě zabezpečení sítě použijte pole Popis k určení obchodní potřeby a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do nebo ze sítě.


Pomocí některé z předdefinovaných definic zásad Azure souvisejících s označováním, jako je například Vyžadovat značku a její hodnotu, můžete zajistit, aby se všechny prostředky vytvořily pomocí značek, a upozornit vás na existující neoznačené prostředky.


Můžete použít Azure PowerShell nebo Azure rozhraní příkazového řádku (CLI) k vyhledávání nebo provádění akcí na prostředky na základě jejich značky.


Jak vytvořit a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


Jak vytvořit virtuální síť:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


Jak vytvořit skupinu zabezpečení zabezpečení s konfigurací zabezpečení:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

**Pokyny:** Pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s nasazením Azure HDInsight. Vytvořte výstrahy v rámci Azure Monitor, který se aktivuje, když dojde ke změnám kritických síťových prostředků.


Jak zobrazit a načíst události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Jak vytvořit výstrahy ve službě Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace naleznete [v tématu Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

**Pokyny:** Microsoft udržuje zdroje času pro součásti clusteru Azure HDInsight, můžete aktualizovat synchronizaci času pro vaše výpočetní nasazení.


Jak nakonfigurovat synchronizaci času pro výpočetní prostředky Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

**Pokyny:** Cluster Azure HDInsight můžete založit do Azure Monitoru a agregovat data zabezpečení generovaná clusterem. Využijte vlastní dotazy ke zjišťování a reakci na hrozby v prostředí. 


Jak zavést cluster Azure HDInsight do Azure Monitoru:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Jak vytvořit vlastní dotazy pro cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

**Pokyny:** Povolte Azure Monitor pro cluster HDInsight, přesměrujte ho do pracovního prostoru Analýzy protokolů. Tím se budou protokolovat relevantní informace o clusteru a metriky operačního systému pro všechny uzly clusteru Azure HDInsight.


Jak povolit protokolování pro cluster HDInsight:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Jak dotaz HDInsight protokoly:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Shromažďování protokolů zabezpečení z operačních systémů

**Pokyny:** Palubní cluster Azure HDInsight do Azure Monitoru. Ujistěte se, že použitý pracovní prostor Log Analytics má nastavenou dobu uchovávání protokolu podle předpisů pro dodržování předpisů vaší organizace.


Jak zavést cluster Azure HDInsight do Azure Monitoru:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Jak nakonfigurovat dobu uchování pracovního prostoru analýzy protokolů:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny:** Palubní cluster Azure HDInsight do Azure Monitoru. Ujistěte se, že použitý pracovní prostor Azure Log Analytics má nastavenou dobu uchovávání protokolů podle předpisů vaší organizace pro dodržování předpisů.


Jak zavést cluster Azure HDInsight do Azure Monitoru:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Jak nakonfigurovat dobu uchování pracovního prostoru analýzy protokolů:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="26-monitor-and-review-logs"></a>2.6: Sledování a kontrola protokolů

**Pokyny:** Pomocí dotazů pracovního prostoru Azure Log Analytics můžete dotazovat protokoly Azure HDInsight:


Jak vytvořit vlastní dotazy pro clustery Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

**Pokyny:** Použijte pracovní prostor Azure Log Analytics pro monitorování a upozorňování na neobvyklé aktivity v protokolech zabezpečení a událostech souvisejících s vaším clusterem Azure HDInsight.


Jak spravovat výstrahy v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts


Jak upozornit na data protokolu analytics protokolu:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

**Pokyny**: Azure HDInsight je dodáván s předinstalovaným a povoleným funkcí služby Clamscan pro bitové kopie uzlu clusteru, ale musíte spravovat software a ručně agregovat nebo monitorovat všechny protokoly, které clamscan vytvoří.


Pochopte Škeble:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

**Pokyny**: Implementujte řešení třetí strany pro protokolování DNS.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

**Pokyny:** Ručně nakonfigurujte protokolování konzoly na základě uzlů.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace naleznete [v tématu Zabezpečení: Identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Vedení soupisu administrativních účtů

**Pokyny:** Udržujte záznam o místním účtu pro správu, který se vytvoří během zřizování clusteru clusteru Azure HDInsight, a také o všech dalších účtech, které vytvoříte. Kromě toho pokud se používá integrace Azure AD, Azure AD má předdefinované role, které musí být explicitně přiřazeny a jsou proto dotazovatelné. Pomocí modulu Azure AD PowerShell můžete provádět adhoc dotazy ke zjišťování účtů, které jsou členy skupin pro správu.


Kromě toho můžete použít azure security center identity a řízení přístupu doporučení.


Jak získat roli adresáře ve službě Azure AD s PowerShellem:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Jak získat členy role adresáře ve službě Azure AD s PowerShellem:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


Jak sledovat identitu a přístup pomocí Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel, pokud je to možné

**Pokyny:** Při zřizování clusteru Azure vyžaduje vytvoření nových hesel pro webový portál a přístup k zabezpečenému prostředí (SSH). Neexistují žádná výchozí hesla změnit, ale můžete zadat různá hesla pro SSH a přístup k webovému portálu.


Jak nastavit hesla při zřizování clusteru Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Použití vyhrazených administrativních účtů

**Pokyny:** Integrace clusteru Authentication for Azure HDInsight s Azure Active Directory. Vytvořte zásady a postupy týkající se používání vyhrazených účtů pro správu.


Kromě toho můžete použít azure security center identity a řízení přístupu doporučení.


Jak integrovat ověřování Azure HDInsight s Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds


Jak sledovat identitu a přístup pomocí Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Použití jednotného přihlašování (SSO) s Azure Active Directory

**Pokyny:** Pomocí azure HDInsight ID Broker se přihlásit k clustery balíček podnikového zabezpečení (ESP) pomocí vícefaktorového ověřování, bez zadání hesla. Pokud jste se už přihlásili k jiným službám Azure, jako je například portál Azure, můžete se přihlásit ke svému clusteru Azure HDInsight pomocí jednotného přihlašování (SSO).


Jak povolit Azure HDInsight ID Broker:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Použití vícefaktorového ověřování pro veškerý přístup založený na službě Azure Active Directory

**Pokyny:** Povolte Azure AD MFA a postupujte podle doporučení Azure Security Center identity a správy přístupu. Clustery Azure HDInsight s nakonfigurovaným balíčkem podnikového zabezpečení lze připojit k doméně, aby uživatelé domény mohli používat svá pověření domény k ověřování pomocí clusterů a spouštění úloh velkých objemů dat. Při ověřování s vícefaktorovým ověřováním (MFA) povoleno, budou uživatelé vyzváni k poskytnutí druhého faktoru ověřování.


Jak povolit vícefaktorové povolení v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Jak sledovat identitu a přístup v rámci Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Pro všechny administrativní úkoly používejte vyhrazené počítače (pracovní stanice s privilegovaným přístupem)

**Pokyny:** Používejte paws (pracovní stanice s privilegovaným přístupem) s vícefaktorovým ověřováním (MFA) nakonfigurovaným pro přihlášení a konfiguraci clusterů Azure HDInsight a souvisejících prostředků.


Další informace o pracovních stanicích s privilegovaným přístupem:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Jak povolit vícefaktorové povolení v Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokolování a upozornění na podezřelou aktivitu z administrativních účtů

**Pokyny:** Clustery Azure HDInsight s nakonfigurovaným balíčkem podnikového zabezpečení lze připojit k doméně, aby uživatelé domény mohli k ověření používat svá pověření domény. Sestavy zabezpečení Služby Azure Active Directory (AAD) můžete použít pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivitě v prostředí AAD. Azure Security Center slouží ke sledování aktivity identity a přístupu.


Jak identifikovat uživatele AAD označené pro rizikovou aktivitu:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


Jak sledovat identitu uživatelů a aktivitu přístupu v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

**Pokyny:** Clustery Azure HDInsight s nakonfigurovaným balíčkem podnikového zabezpečení lze připojit k doméně, aby uživatelé domény mohli k ověření používat svá pověření domény. Pomocí pojmenovaných umístění podmíněného přístupu můžete povolit přístup pouze z určitých logických seskupení oblastí IP adres nebo zemí nebo oblastí.


Jak nakonfigurovat pojmenovaná umístění v Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="39-use-azure-active-directory"></a>3.9: Použití služby Azure Active Directory

**Pokyny:** Použijte Azure Active Directory (AAD) jako centrální ověřovací a autorizační systém. AAD chrání data pomocí silného šifrování pro data v klidovém stavu a při přenosu. AAD také soli, hasha a bezpečně ukládá přihlašovací údaje uživatele.

Clustery Azure HDInsight s nakonfigurovaným balíčkem enterprise security package (ESP) lze připojit k doméně, aby uživatelé domény mohli používat svá pověření domény k ověřování pomocí clusterů.


Jak vytvořit a nakonfigurovat instanci AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


Jak nakonfigurovat balíček podnikového zabezpečení pomocí služby Azure Active Directory Domain Services v Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelně kontrolujte a slaďujte přístup uživatelů

**Pokyny:** Použijte ověřování Azure Active Directory (AAD) s clusterem Azure HDInsight. AAD poskytuje protokoly, které pomáhají zjistit zastaralé účty. Kromě toho můžete pomocí azure identity access reviews efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Přístup uživatele lze pravidelně kontrolovat, aby se zajistilo, že pouze ti praví uživatelé mají trvalý přístup. 


Jak používat recenze přístupu k identitám Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

**Pokyny:** Pomocí protokolů přihlášení a auditování služby Azure Active Directory (AAD) můžete sledovat pokusy o přístup k deaktivovaným účtům. tyto protokoly mohou být integrovány do libovolného nástroje SIEM/monitoring u jiných stran.


Tento proces můžete zjednodušit vytvořením diagnostických nastavení pro uživatelské účty AAD, odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Azure Log Analytics. Nakonfigurujte požadované výstrahy v pracovním prostoru Azure Log Analytics.


Jak integrovat protokoly aktivit Azure do Azure Monitoru:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

**Pokyny:** Clustery Azure HDInsight s nakonfigurovaným balíčkem enterprise security package (ESP) lze připojit k doméně, aby uživatelé domény mohli používat svá pověření domény k ověřování pomocí clusterů.  Pomocí funkce Azure Active Directory (AAD) detekce rizik a ochrana identity nakonfigurujte automatické odpovědi na zjištěné podezřelé akce související s identitami uživatelů. Kromě toho můžete ingestovat data do Azure Sentinelu pro další šetření.


Jak zobrazit aad riskantní přihlášení:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Jak nakonfigurovat a povolit zásady rizik a zásady ochrany identity:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory

**Pokyny**: Není k dispozici; Customer Lockbox ještě není pro Azure HDInsight podporovaný.

Seznam služeb podporovaných customer lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace naleznete [v tématu Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Udržovat soupis citlivých informací

**Pokyny:** Pomocí značek na prostředky související s nasazením Azure HDInsight pomoci při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.


Jak vytvářet a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolovat systémy ukládající nebo zpracovávající citlivé informace

**Pokyny**: Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a výrobu. Clustery Azure HDInsight a všechny přidružené účty úložiště by měly být odděleny virtuální sítí nebo podsítí, odpovídajícím způsobem označeny a zabezpečeny v rámci skupiny zabezpečení sítě (NSG) nebo Azure Firewall. Data clusteru by měla být obsažena v rámci zabezpečeného účtu úložiště Azure nebo Azure Data Lake Storage (Gen1 nebo Gen2).


Zvolte možnosti úložiště pro cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


Jak zabezpečit Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


Jak zabezpečit účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

**Pokyny:** Pro clustery Azure HDInsight ukládání nebo zpracování citlivých informací, označte clusteru a související prostředky jako citlivé pomocí značek. Chcete-li snížit riziko ztráty dat prostřednictvím exfiltrace, omezte odchozí síťový provoz pro clustery Azure HDInsight pomocí azure firewall.


Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.


Jak omezit odchozí provoz pro clustery Azure HDInsight pomocí Azure Firewall:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic


Seznamte se s ochranou zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

**Pokyny**: Šifrování všech citlivých informací při přenosu. Ujistěte se, že všichni klienti, kteří se připojují k vašemu clusteru Azure HDInsight nebo datovým úložištím clusteru (účty úložiště Azure nebo Azure Data Lake Storage Gen1/Gen2), jsou schopni vyjednat TLS 1.2 nebo vyšší. Prostředky Microsoft Azure budou ve výchozím nastavení vyjednávat tls 1.2. 


Principy šifrování Azure Data Lake Storage při přenosu:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


Seznamte se s šifrováním účtu úložiště Azure při přenosu:

https://docs.microsoft.com/azure/storage/blobs/security-recommendations

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

**Pokyny:** Funkce identifikace, klasifikace a prevence ztrát dat ještě nejsou k dispozici pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.



Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.



Seznamte se s ochranou zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Použití Azure RBAC k řízení přístupu k prostředkům

**Pokyny:** Pomocí balíčku Azure HDInsight Enterprise Security Package (ESP) můžete pomocí Apache Rangeru vytvářet a spravovat podrobné zásady řízení přístupu a zamlžování dat pro vaše data uložená v souborech, složkách, databázích, tabulkách a řádcích a sloupcích. Správce hadoopu může nakonfigurovat řízení přístupu na základě rolí (RBAC) pro zabezpečení Apache Hive, HBase, Kafka a Spark pomocí těchto pluginů v Apache Ranger.

Konfigurace zásad RBAC pomocí Apache Ranger umožňuje přidružit oprávnění k roli v organizaci. Tato vrstva abstrakce usnadňuje zajištění, že uživatelé mají pouze oprávnění potřebná k plnění svých pracovních povinností.

Konfigurace balíčků podnikového zabezpečení se službou Azure Active Directory Domain Services v HDInsightu:https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Přehled podnikového zabezpečení v Azure HDInsightu:https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití ochrany před ztrátou dat na hostiteli k vynucení kontroly přístupu

**Pokyny:** Pro clustery Azure HDInsight ukládání nebo zpracování citlivých informací, označte clusteru a související prostředky jako citlivé pomocí značek. Funkce identifikace, klasifikace a prevence ztrát ještě nejsou dostupné pro Azure Storage nebo výpočetní prostředky. Implementujte řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.


Pro základní platformu, která je spravována společností Microsoft, společnost Microsoft považuje veškerý obsah zákazníků za citlivý a zachází velmi daleko, aby se ochránila před ztrátou a expozicí dat zákazníků. Aby byla zákaznická data v Azure stále zabezpečená, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a funkcí ochrany dat.


Seznamte se s ochranou zákaznických dat v Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

**Pokyny:** Pokud pomocí Azure SQL Database k ukládání metadat Apache Hive a Apache Oozie, ujistěte se, že data SQL zůstanou šifrovaná po celou dobu. Pro účty úložiště Azure a úložiště datových jezer (Gen1 nebo Gen2) se doporučuje povolit Microsoftu spravovat vaše šifrovací klíče, ale máte možnost spravovat vlastní klíče.



Jak spravovat šifrovací klíče pro účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal



Jak vytvořit Azure Data Lake Storage pomocí šifrovacích klíčů spravovaných zákazníkem:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal



Principy šifrování pro Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption



Konfigurace transparentního šifrování dat pro databázi SQL pomocí klíčů spravovaných zákazníkem:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Sdíleno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

**Pokyny:** Konfigurace nastavení diagnostiky pro účty úložiště Azure přidružené k clusterům Azure HDInsight pro monitorování a protokolování všech operací CRUD s daty clusteru. Povolte auditování pro všechny účty úložiště nebo úložiště datových jezer přidružených ke clusteru Azure HDInsight.


Jak povolit další protokolování/auditování pro účet úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account


Jak povolit další protokolování/auditování pro Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace naleznete v [tématu Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Spuštění automatizovaných nástrojů pro vyhledávání chyb zabezpečení

**Pokyny**: Implementace řešení správy zranitelnosti jiných výrobců.


Volitelně pokud máte předplatné rapid7, Qualys nebo jiné platformy pro správu zranitelnosti, můžete pomocí akcí skriptů nainstalovat agenty pro hodnocení zranitelnosti na uzly clusteru Azure HDInsight a spravovat uzly prostřednictvím příslušného portálu.


Jak nainstalovat Rapid7 Agent ručně:

https://insightvm.help.rapid7.com/docs/azure-security-center


Jak nainstalovat Qualys Agent ručně:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf


Jak používat akce skriptu:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Nasazení řešení pro správu automatických oprav operačního systému

**Pokyny:** Automatické aktualizace systému byly povoleny pro bitové kopie uzlů clusteru, ale je nutné pravidelně restartovat uzly clusteru, abyste zajistili použití aktualizací.


Microsoft pro údržbu a aktualizaci základních bitových kopií uzlů Azure HDInsight.


Jak nakonfigurovat plán oprav operačního systému pro clustery HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Nasazení automatizovaného řešení pro správu softwarových oprav od jiných výrobců

**Pokyny:** Pomocí akcí skriptů nebo jiných mechanismů opravte clustery Azure HDInsight. Nově vytvořené clustery budou mít vždy nejnovější dostupné aktualizace, včetně nejnovějších oprav zabezpečení.


Jak nakonfigurovat plán oprav operačního systému pro clustery Azure HDInsight založených na Linuxu:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching


Jak používat akce skriptu:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porovnání prohledávačů ohrožení zabezpečení zády k zadní straně

**Pokyny**: Implementujte řešení správy ohrožení zabezpečení od jiných výrobců, které dokáže v průběhu času porovnávat prohledávací výsledky zranitelnosti. Pokud máte předplatné Rapid7 nebo Qualys, můžete pomocí portálu tohoto dodavatele zobrazit a porovnat prohledávací chyby zabezpečení back-to-back.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Použití procesu hodnocení rizik a upřednostnění nápravy zjištěných zranitelných míst

**Pokyny**: Používejte běžný program hodnocení rizik (např. společný systém hodnocení zranitelnosti) nebo výchozí hodnocení rizik poskytovaného vaším nástrojem pro skenování třetí stranou.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace naleznete v [tématu Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Použití Azure Asset Discovery

**Pokyny:** Pomocí Azure Resource Graph můžete dotazovat/zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, sítě, porty a protokoly atd.), včetně clusterů Azure HDInsight v rámci vašeho předplatného.  Ujistěte se, že máte příslušná (číst) oprávnění ve vašem tenantovi a jsou schopni vytvořit výčet všech předplatných Azure, stejně jako prostředky v rámci vašich předplatných.


I když klasické prostředky Azure může být zjištěna prostřednictvím Azure Resource Graph, je vysoce doporučeno vytvářet a používat prostředky Azure Resource Manager do budoucna.


Jak vytvářet dotazy pomocí Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Jak zobrazit předplatná Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Principy Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="62-maintain-asset-metadata"></a>6.2: Udržování metadat datových zdrojů

**Pokyny:** Použijte značky pro prostředky Azure, které poskytují metadata, aby je logicky uspořádali do taxonomie.


Jak vytvářet a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Odstranění neautorizovaných prostředků Azure

**Pokyny:** V případě potřeby použijte značkování, skupiny pro správu a samostatná předplatná k uspořádání a sledování datových zdrojů. Pravidelně odsouhlasete zásoby a zajistěte, aby byly z předplatného včas odstraněny neoprávněné prostředky.


Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription


Jak vytvořit skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create


Jak vytvářet a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržovat soupis schválených prostředků Azure a softwarových titulů

**Pokyny:** Definování seznamu schválených prostředků Azure a schváleného softwaru pro vaše výpočetní prostředky

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorování neschválených prostředků Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků
- Povolené typy prostředků

Pomocí Azure Resource Graph můžete dotazovat/zjišťovat prostředky v rámci vašeho předplatného. Ujistěte se, že jsou schváleny všechny prostředky Azure v prostředí.

Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Sledování neschválených softwarových aplikací v rámci výpočetních prostředků

**Pokyny**: Implementace řešení jiného výrobce pro monitorování uzlů clusteru pro neschválené softwarové aplikace.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků Azure a softwarových aplikací

**Pokyny:** Pomocí Azure Resource Graph můžete dotazovat/zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, sítě, porty a protokoly atd.), včetně clusterů Azure HDInsight v rámci vašeho předplatného.  Odeberte všechny neschválené prostředky Azure, které zjistíte. Pro uzly clusteru Azure HDInsight implementujte řešení jiného výrobce, které odebere nebo upozorní na neschválený software.


Jak vytvářet dotazy pomocí Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="68-use-only-approved-applications"></a>6.8: Používejte pouze schválené aplikace

**Pokyny:** Pro uzly clusteru Azure HDInsight implementujte řešení jiného výrobce, abyste zabránili spuštění neoprávněného softwaru.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="69-use-only-approved-azure-services"></a>6.9: Používejte jenom schválené služby Azure

**Pokyny**: Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků
- Povolené typy prostředků


Jak nakonfigurovat a spravovat zásady Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Jak odepřít konkrétní typ prostředku pomocí zásad Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

**Pokyny:** Pro uzly clusteru Azure HDInsight implementujte řešení jiného výrobce, abyste zabránili spuštění neoprávněných typů souborů.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Omezte možnost uživatelů komunikovat se Správcem prostředků Azure prostřednictvím skriptů

**Pokyny:** Pomocí podmíněného přístupu Azure omezte uživatelům možnost pracovat se Správcem prostředků Azure konfigurací "Blokovat přístup" pro aplikaci Microsoft Azure Management.


Jak nakonfigurovat podmíněný přístup pro blokování přístupu ke Správci prostředků Azure:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Omezení možnosti uživatelů spouštět skripty v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; To se nevztahuje na Azure HDInsight jako uživatelé (non-správci) clusteru nepotřebují přístup k jednotlivým uzlům ke spuštění úloh. Správce clusteru má root přístup ke všem uzlům clusteru.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Nepoužije se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fyzicky nebo logicky oddělit vysoce rizikové aplikace

**Pokyny**: Nepoužije se; benchmark je určen pro Službu Azure Apps Service nebo výpočetní prostředky hostující webové aplikace.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Nepoužije se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace naleznete v [tématu Zabezpečení: Zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů Microsoft.HDInsight vytvořte vlastní zásady pro auditování nebo vynucení konfigurace sítě vašeho clusteru HDInsight.


Jak zobrazit dostupné aliasy zásad Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Vytvoření bezpečných konfigurací operačního systému

**Pokyny:** Image operačního systému Azure HDInsight spravované a spravované společností Microsoft. Zákazník odpovědný za implementaci zabezpečených konfigurací operačního systému uzlů clusteru. 

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Udržování zabezpečených konfigurací prostředků Azure

**Pokyny:** Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení pro vaše clustery Azure HDInsight a související prostředky.


Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Principy efektů zásad Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Udržovat bezpečné konfigurace operačního systému

**Pokyny:** Image operačního systému Azure HDInsight spravované a spravované společností Microsoft. Zákazník odpovědný za implementaci konfigurace stavu na úrovni operačního systému.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládá

**Pokyny:** Pokud používáte vlastní definice zásad Azure, použijte Azure DevOps nebo Azure Repos bezpečně ukládat a spravovat svůj kód.



Jak ukládat kód v Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Dokumentace k azure repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečné ukládání vlastních bitových kopií operačního systému

**Pokyny**: Nepoužije se; vlastní image, které se nevztahují na Azure HDInsight.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Nepoužije se

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů Microsoft.HDInsight vytvořte vlastní zásady pro výstrahu, auditování a vynucení konfigurací systému. Kromě toho vytvořte proces a kanál pro správu výjimek zásad.



Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

**Pokyny**: Implementujte řešení jiného výrobce pro zachování požadovaného stavu operačních systémů uzlů clusteru.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace automatického monitorování konfigurace pro služby Azure

**Pokyny:** Pomocí aliasů zásad Azure v oboru názvů Microsoft.HDInsight vytvořte vlastní zásady pro auditování nebo vynucení konfigurace clusteru HDInsight.


Jak zobrazit dostupné aliasy zásad Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatizovaného monitorování konfigurace operačních systémů

**Pokyny**: Implementujte řešení jiného výrobce pro sledování stavu operačních systémů uzlů clusteru.

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpečná správa tajných kódů Azure

**Pokyny:** Azure HDInsight zahrnuje podporu přineste si vlastní klíč (BYOK) pro Apache Kafka. Tato funkce umožňuje vlastnit a spravovat klíče používané k šifrování dat v klidovém stavu.


Všechny spravované disky v Azure HDInsight jsou chráněné pomocí Šifrování služby Azure Storage Service (SSE). Ve výchozím nastavení jsou data na těchto discích šifrována pomocí klíčů spravovaných společností Microsoft. Pokud povolíte BYOK, poskytnete šifrovací klíč pro Azure HDInsight k jeho použití a správě pomocí azure key vault.


Trezor klíčů může být také používán s nasazeními Azure HDInsight ke správě klíčů pro úložiště clusteru (účty úložiště Azure a Azure Data Lake Storage).


Jak si na Azure HDInsight přivést vlastní klíč pro Apache Kafka:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


Jak spravovat šifrovací klíče pro účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpečná a automatická správa identit

**Pokyny:** Spravované identity lze použít v Azure HDInsight umožněte vašim clusterům přístup ke službám domény Azure Active Directory, přístup k úložišti Klíčů Azure nebo přístup k souborům v Azure Data Lake Storage Gen2.


Principy spravovaných identit pomocí Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

**Pokyny:** Pokud používáte jakýkoli kód související s nasazením Azure HDInsight, můžete implementovat skener přihlašovacích údajů k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault. 


Jak nastavit skener pověření:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace naleznete v [tématu Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Používejte centrálně spravovaný antimalwarový software

**Pokyny**: Azure HDInsight je dodáván s předinstalovaným a povoleným funkcí služby Clamscan pro bitové kopie uzlu clusteru, ale musíte spravovat software a ručně agregovat nebo monitorovat všechny protokoly, které clamscan vytvoří.


Principy funkce Clamscan pro Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

**Pokyny**: Microsoft Antimalware je povolena na podkladovém hostiteli, který podporuje služby Azure, ale neběží na obsah zákazníka.


Předem proskenujte všechny soubory nahrané do všech prostředků Azure souvisejících s nasazením vašeho clusteru Azure HDInsight, jako je úložiště datových jezer, úložiště objektů blob atd. Společnost Microsoft nemá v těchto případech přístup k zákaznickým datům.


Seznamte se s antimalwarovým programem Microsoft pro cloudové služby Azure a virtuální počítače:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Sdíleno

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Zajistěte aktualizaci antimalwarového softwaru a podpisů

**Navádění**: Azure HDInsight je dodáván s předinstalovaným a povoleným službou Clamscan pro image uzlu clusteru. Clamscan bude provádět aktualizace motoru a definice automaticky, ale agregace a správa protokolů bude muset být provedena ručně.


Principy funkce Clamscan pro Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace naleznete [v tématu Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

**Pokyny:** Při použití účtu úložiště Azure pro úložiště dat clusteru HDInsight zvolte příslušnou možnost redundance (LRS,ZRS, GRS, RA-GRS).  Při použití azure sql database pro úložiště dat clusteru Azure HDInsight nakonfigurujte aktivní geografickou replikaci.


Jak nakonfigurovat redundanci úložiště pro účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Jak nakonfigurovat redundanci pro Azure SQL Databases:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

**Pokyny:** Při použití účtu úložiště Azure pro úložiště dat clusteru Azure HDInsight zvolte příslušnou možnost redundance (LRS,ZRS, GRS, RA-GRS). Pokud používáte Azure Key Vault pro jakoukoli část nasazení Azure HDInsight, ujistěte se, že vaše klíče jsou zálohované.


Zvolte možnosti úložiště pro cluster Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


Jak nakonfigurovat redundanci úložiště pro účty úložiště Azure:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Jak zálohovat klíče trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny:** Pokud se azure key vault používá s nasazením Azure HDInsight, otestujte obnovení zálohovaných klíčů spravovaných zákazníky.


Jak si na Azure HDInsight přivést vlastní klíč pro Apache Kafka:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


Jak obnovit klíče trezoru klíčů v Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

**Pokyny:** Pokud se azure key vault používá s nasazením Azure HDInsight, povolte obnovitelné odstranění v trezoru klíčů k ochraně klíčů před náhodným nebo škodlivým odstraněním.


Jak povolit obnovitelné odstranění v azure key vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace naleznete [v tématu Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

**Pokyny**: Ujistěte se, že existují písemné plány reakce na incidenty, které definují role personálu, jakož i fáze zpracování incidentů / správy.



Jak nakonfigurovat automatizaci pracovních postupů v Centru zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

**Pokyny**: Centrum zabezpečení přiřazuje výstrahám závažnost, která vám pomůže určit pořadí, ve kterém se účastníte jednotlivých výstrah, takže když dojde k ohrožení zabezpečení prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění.

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

**Pokyny**: Proveďte cvičení k testování schopností reakce na incidenty vašich systémů na pravidelné kadenci. Identifikujte slabá místa a mezery a podle potřeby plán revidujte. Podívejte se na publikaci NIST: Průvodce testovacími, školicími a cvičebními programy pro plány a schopnosti IT:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Poskytněte kontaktní údaje bezpečnostních incidentů a nakonfigurujte upozornění na bezpečnostní incidenty

**Pokyny**: Kontaktní informace o bezpečnostních incidentech společnost Microsoft použije k tomu, aby vás kontaktovala, pokud centrum MSRC (Microsoft Security Response Center) zjistí, že k vašim datům získala přístup nezákonná nebo neoprávněná strana.



Jak nastavit kontakt zabezpečení Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Začleňte výstrahy zabezpečení do systému reakce na incidenty

**Pokyny:** Exportujte výstrahy a doporučení Centra zabezpečení Azure pomocí funkce Nepřetržitý export. Nepřetržitý export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně. Datový konektor Azure Security Center můžete použít k streamování výstrah Sentinel.



Jak nakonfigurovat nepřetržitý export:

https://docs.microsoft.com/azure/security-center/continuous-export



Jak streamovat výstrahy do Azure Sentinelu:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizace reakce na výstrahy zabezpečení

**Pokyny:** Pomocí funkce Automatizace pracovních postupů v Centru zabezpečení Azure můžete automaticky aktivovat odpovědi prostřednictvím "Logic Apps" na výstrahy zabezpečení a doporučení.



Konfigurace automatizace pracovních postupů a aplikací logiky:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace naleznete [v tématu Bezpečnostní kontrola: Penetrační testy a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Proveďte pravidelné penetrační testování prostředků Azure a zajistěte nápravu všech kritických zjištění zabezpečení do 60 dnů

**Pokyny**: Postupujte podle pravidel společnosti Microsoft a ujistěte se, že penetrační testy neporušují zásady společnosti Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



Další informace o strategii společnosti Microsoft a provádění červených týmů a testování pronikání živých webů proti cloudové infrastruktuře, službám a aplikacím společnosti Microsoft naleznete zde:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

## <a name="next-steps"></a>Další kroky

- Podívejte se na [srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zásad zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
