---
title: Základní hodnoty zabezpečení Azure pro VPN Gateway
description: Základní VPN Gateway zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8221e814b4555a75d596cec3ab00aa3b63e41a14
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197400"
---
# <a name="azure-security-baseline-for-vpn-gateway"></a>Základní hodnoty zabezpečení Azure pro VPN Gateway

Tato základní hodnota zabezpečení platí pro VPN Gateway pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na VPN Gateway. **Ovládací prvky** , které se nevztahují k VPN Gateway byly vyloučeny.

Pokud chcete zjistit, jak VPN Gateway kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot VPN Gateway Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: při práci s podsítěmi brány VPN se vyhněte přidružení skupiny zabezpečení sítě (NSG) k podsíti brány. Pokud byste k této podsíti přidružili skupinu zabezpečení sítě, brána sítě VPN by mohla přestat fungovat podle očekávání.  V Virtual Network ale povolte skupiny zabezpečení sítě pro jiné podsítě mimo bránu VPN.

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md) 

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md) 

- [Vytvoření brány sítě VPN založené na trasách pomocí Azure Portal](./tutorial-create-gateway-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: použijte Azure Security Center a sledujte doporučení k ochraně síťových prostředků v Azure pomocí doporučení pro ochranu sítě. 

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: Povolte zachytávání paketů brány VPN v bráně nebo konkrétním připojení v závislosti na vašich požadavcích.

- [Konfigurace zachycení paketů pro brány VPN](packet-capture.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky pomocí Azure Policy.

Pomocí plánů Azure můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, přiřazení Azure RBAC a přiřazení Azure Policy v rámci jedné definice podrobného plánu. Můžete použít podrobný plán na nové nebo existující odběry a jemně vyladit řízení a správu prostřednictvím správy verzí.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Ukázky Azure Policy pro sítě](../governance/policy/samples/built-in-policies.md#network) 

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny prostředků virtuální sítě. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny důležitých prostředků souvisejících s vaší bránou VPN.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: ingestování aktivit a diagnostických protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení generovaných síťovými prostředky, jako jsou vaše brány VPN Gateway. Použijte Azure Monitor k dotazování a provádění analýz dat protokolu, použijte účty Azure Storage k dlouhodobému a Archivačnímu uložení těchto protokolů. 

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Nastavení výstrah pro události diagnostického protokolu z VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení nastavení diagnostiky na vašich VPN Gatewaych prostředcích pro přístup k protokolům auditu, zabezpečení a diagnostiky. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky. 

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště. 

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro neobvyklé chování a pravidelné prohlížení výsledků. Pomocí Azure Monitor a pracovního prostoru Log Analytics můžete prohlížet protokoly a provádět dotazy na data protokolu. 

Případně můžete povolit a začlenit data do Azure Sentinel nebo třetí strany SIEM. 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Začínáme s Log Analytics dotazy](../azure-monitor/log-query/log-analytics-tutorial.md) 

- [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použití Azure Security Center s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech.

Alternativně můžete povolit a začlenit data do Azure Sentinel.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Pokyny**: implementace řešení od jiného výrobce z Azure Marketplace pro řešení protokolování DNS podle požadavků vaší organizace.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: řízení přístupu na základě role v Azure (Azure RBAC) umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro určité prostředky existují předdefinované role, které je možné inventarizovat nebo dotazovat pomocí nástrojů, jako jsou Azure CLI, Azure PowerShell nebo Azure Portal.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. 

Můžete také povolit přístup za běhu pomocí Azure AD Privileged Identity Management a Azure Resource Manager. 

- [Další informace o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné** materiály: kdykoli je to možné, použijte Azure Active Directory jednotného přihlašování (SSO) místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte Azure Security Center doporučení pro identitu a přístup. 

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte službu Azure AD MFA a sledujte doporučení pro přístup k identitě Azure Security Center. 

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Pokyny**: Použijte zabezpečenou pracovní stanici spravovanou v Azure (také známou jako pracovní stanice s privilegovaným přístupem nebo privilegovaným přístupem) pro úlohy správy, které vyžadují zvýšená oprávnění.

- [Pochopení zabezpečení pracovních stanic spravovaných Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak povolit Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití sestav a monitorování zabezpečení Azure Active Directory k detekci, kdy dojde k podezřelé nebo nebezpečné aktivitě v prostředí. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění Azure AD povolte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí. 

- [Jak nakonfigurovat pojmenovaná umístění služby Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele. 

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. K efektivní správě členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí můžete navíc použít kontroly identity a přístupu v Azure AD. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. 

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml) 

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: máte přístup ke zdrojům přihlašovacích aktivit, auditem a rizikovým protokolům událostí Azure AD, které vám umožní integraci s jakýmkoli nástrojem Siem/monitoring. 

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics. 

- [Jak integrovat protokoly aktivit Azure pomocí Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Pokyny**: pomocí Azure AD identity Protectionch funkcí můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření. 

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: brány VPN mají vyhrazené instance virtuálních počítačů pro každou zákaznickou virtuální síť. Implementujte izolaci pomocí samostatných virtuálních sítí, předplatných a skupin pro správu pro jednotlivé domény zabezpečení, jako je například typ prostředí a úroveň citlivosti dat. Můžete omezit úroveň přístupu k prostředkům Azure, které vaše aplikace a podniková prostředí vyžadují. Přístup k prostředkům Azure můžete řídit prostřednictvím řízení přístupu na základě role Azure (RBAC).

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny**: použití řešení třetí strany z Azure Marketplace v hraničních sítích pro monitorování neoprávněného přenosu citlivých informací a zablokování takových přenosů při upozornění na odborníky na zabezpečení informací. 

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a chrání před ztrátou a expozicí zákaznických dat. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat. 

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: brány sítě VPN šifrují zákazníky mezi bránami Azure VPN a místními zařízeními VPN (P2S) zákazníků. Brány VPN také podporují šifrování VNet-to-VNet.

Pro příslušné prostředky ve vaší virtuální síti postupujte podle Azure Security Center doporučení pro šifrování v klidovém provozu a šifrování při přenosu.

- [O typech sítě VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype)

- [Informace o zařízeních VPN a parametrech protokolu IPsec/IKE pro připojení typu Site-to-Site ke službě VPN Gateway](vpn-gateway-about-vpn-devices.md#ipsec)

- [O kryptografických požadavcích a branách Azure VPN Gateway](vpn-gateway-about-compliance-crypto.md)

- [Konfigurace zásad IPsec/IKE pro připojení S2S VPN nebo připojení typu VNet-to-VNet](vpn-gateway-ipsecikepolicy-rm-powershell.md)

- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: k identifikaci všech citlivých informací uložených, zpracovávaných a přenášených vašimi technologickými systémy, včetně těch, které se nacházejí v lokalitě nebo u poskytovatele vzdálené služby, a k aktualizaci inventáře citlivých informací organizace použijte nástroj pro aktivní zjišťování třetí strany.

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: použití řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k datům a prostředkům, v opačném případě použití metod řízení přístupu specifických pro službu. Použijte předdefinované role, jako je vlastník, přispěvatel nebo přispěvatel sítě, a pak přiřaďte roli příslušnému oboru. Přiřaďte specifická oprávnění pro podmnožinu možností virtuální sítě vytvořením vlastní role a přiřazením konkrétních oprávnění vyžadovaných pro virtuální sítě, podsítě, brány sítě VPN, síťových rozhraní, skupin zabezpečení sítě a směrovacích tabulek k této roli.

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Plánování virtuálních sítí](../virtual-network/virtual-network-vnet-plan-design-arm.md#permissions)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: nakonfigurujte výstrahy Azure monitor, které aktivují protokoly aktivit Azure, aby se změny projevily u důležitých prostředků Azure, jako jsou vaše brány VPN. 

- [Nastavení výstrah pro VPN Gateway metriky](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)

- [Nastavení výstrah pro události diagnostického protokolu z VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Pokyny**: použijte program pro běžné hodnocení rizik (například běžný systém vyhodnocování chyb zabezpečení) nebo výchozí hodnocení rizika poskytovaná skenovacím nástrojem třetí strany.

- [Publikování v NIST – běžný systém vyhodnocování ohrožení zabezpečení](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky, které se vztahují k BRÁNÁM sítě VPN v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v předplatných všechny prostředky. Kromě toho můžete k vytvoření výčtu prostředků VPN Gateway použít také rozhraní příkazového řádku Azure CLI.

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

- [Rozhraní příkazového řádku Azure pro bránu VPN](/cli/azure/network/vnet-gateway?view=azure-cli-latest)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek k VPN Gateway prostředkům k logickému uspořádání v závislosti na definované taxonomii.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry tam, kde je to vhodné, k organizování a sledování VPN Gatewaych prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného. 

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md) 

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md) 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Pokyny**: pomocí Azure Policy můžete klást omezení typů prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Navíc můžete pomocí grafu prostředků Azure zadávat dotazy a zjišťovat prostředky v rámci předplatných. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](../virtual-network/policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: zákazníci můžou zabránit vytváření prostředků nebo jejich použití přiřazením Azure Policy definic podle požadavků na zabezpečení organizace. Pro odebrání neschválených nebo neautorizovaných prostředků ale musíte implementovat vlastní proces.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](../virtual-network/policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure AD omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci Microsoft Azure Management. 

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace síťových prostředků Azure včetně VPN Gateway. Můžete také použít předdefinované definice Azure Policy.

Azure Resource Manager má možnost Exportovat šablonu ve formátu JSON Script Object Notation (JSON), která by měla být přezkoumána, aby se zajistilo, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

Pro vaše prostředky Azure můžete také použít doporučení z Azure Security Center jako standardní hodnoty konfigurace zabezpečení.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](../virtual-network/policy-reference.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: pomocí Azure Resource Manager šablon a přiřazení Azure Policy můžete bezpečně nakonfigurovat prostředky Azure přidružené k VPN Gateway a souvisejícím prostředkům. Šablony Azure Resource Manager jsou soubory založené na formátu JSON, které se používají k nasazení virtuálního počítače společně s prostředky Azure a vlastní šablona bude potřeba zachovat. Společnost Microsoft provádí údržbu základních šablon.  Pokud chcete vymáhat zabezpečená nastavení napříč prostředky Azure, použijte Azure Policy v [Deny] a [nasadit, pokud neexistují].

- [Informace o vytváření šablon Azure Resource Manager](../virtual-machines/windows/ps-template.md)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Porozumění efektům Azure Policy](../governance/policy/concepts/effects.md)

- [Ukázkové šablony Azure Resource Manageru pro virtuální síť](../virtual-network/template-samples.md)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](../virtual-network/policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: použijte Azure DevOps k bezpečnému ukládání a správě kódu, jako jsou vlastní definice Azure Policy, Azure Resource Manager šablony a požadované konfigurační skripty stavu. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS. 

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro prostředky Azure pomocí Azure Policy. Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurací vašich prostředků Azure. Můžete také využít integrované definice zásad, které souvisejí s vašimi konkrétními prostředky. Kromě toho můžete použít Azure Automation k nasazení změn konfigurace. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak používat aliasy](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: přiřaďte definice Azure Policy k měření konfigurací prostředků, které se týkají VPN Gateway prostředků. Pomocí Azure Policy Insights můžete auditovat konfigurace prostředků a upozorňovat na kritické změny konfigurace.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Příklady předdefinovaných modulů Azure Policy pro virtuální síť](../virtual-network/policy-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Pokyny**: služba VPN Gateway interně ukládá a přenáší předem sdílené klíče a certifikáty zákazníka v šifrovaných formulářích. Zákazníci musí chránit předsdílené klíče nebo certifikáty ve vlastních systémech.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: Azure P2S VPN podporuje tři metody ověřování:

- Na základě certifikátu
- RADIUS
- Azure Active Directory (Azure AD)

Azure AD se doporučuje, protože umožňuje využívat spravované identity.

- [Konfigurace tenanta](openvpn-azure-ad-tenant.md)

- [Konfigurace tenanta s několika klientskými aplikacemi](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurace Multi-Factor Authentication](openvpn-azure-ad-mfa.md)

- [Konfigurace klienta VPN](openvpn-azure-ad-client.md)

- [Postup konfigurace spravovaných identit](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: k nasazení prostředků VPN Gateway použijte Azure Resource Manager. Azure Resource Manager poskytuje možnost exportovat šablony, které se dají použít jako zálohy k obnovení prostředků VPN Gateway. Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony.

- [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md)

- [Ukázkové šablony Azure Resource Manageru pro virtuální síť](../virtual-network/template-samples.md)

- [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Úvod do Azure Automation](../automation/automation-intro.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: k nasazení prostředků VPN Gateway použijte Azure Resource Manager. Azure Resource Manager poskytuje možnost exportovat šablony, které se dají použít jako zálohy k obnovení služby VPN Gateway a souvisejících prostředků. Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony.

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Úvod do Azure Automation](../automation/automation-intro.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: Zajistěte, aby v případě potřeby pravidelně prováděly nasazení Azure Resource Manager šablon pro izolované předplatné do izolovaného předplatného.

- [Nasazení prostředků pomocí šablon ARM a Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: pomocí Azure DevOps bezpečně ukládejte a spravujte kód, třeba vlastní definice Azure Policy a šablony Azure Resource Manager. K ochraně prostředků, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Obnovitelné odstranění objektů blob služby Azure Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: vývoj Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování incidentů a správu, které se mají zjišťovat, na kontrolu po jednotlivých událostech. 

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Pomoc při tvorbě vlastního plánu odpovědí na incidenty najdete v příručce pro zpracování incidentů v počítači s NIST.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete označit odběry pomocí značek a vytvořit systém pojmenování k identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data.  Je vaše zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu. 

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a pak podle potřeby upravte plán odpovědí. 

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy. 

- [Nastavení kontaktu ve službě Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel. 

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md) 

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovních postupů Azure Security Center k automatickému spouštění odpovědí na výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure. 

- [Jak nakonfigurovat automatizaci pracovního postupu v Security Center](../security-center/workflow-automation.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem. 

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)