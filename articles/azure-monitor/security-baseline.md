---
title: Základní hodnoty zabezpečení Azure pro Azure Monitor
description: Základní Azure Monitor zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 15026c378c4c399915e99b0910f1e6518f5adfc8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151883"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Základní hodnoty zabezpečení Azure pro Azure Monitor

Tato základní hodnota zabezpečení se vztahuje na pokyny z [srovnávacího testu zabezpečení Azure](../security/benchmarks/overview.md) na Azure monitor. Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure monitor. **Ovládací prvky** , které se nevztahují k Azure monitor byly vyloučeny. Pokud chcete zjistit, jak Azure Monitor kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure monitor Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Azure Monitor je součástí služeb Azure Core a službu Azure Monitor nelze nasadit jako službu samostatně. Azure Monitor komponenty mohou být nasazeny s vašimi prostředky a to může mít vliv na stav zabezpečení těchto prostředků.

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: povolení privátního odkazu Azure umožňuje přístup ke službám Azure SaaS (například Azure monitor) a službám Azure Hosted Customer/Partnerská služba prostřednictvím privátního koncového bodu ve vaší virtuální síti. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu.

Pokud chcete povolit provoz Azure Monitor, pomocí značek služby "AzureMonitor" povolte příchozí a odchozí provoz přes skupiny zabezpečení sítě. Pokud chcete povolit provoz testu monitorování dostupnosti, abyste dosáhli Azure Monitor, použijte značku služby ApplicationInsightsAvailability pro veškerý příchozí provoz prostřednictvím skupin zabezpečení sítě.

Pravidla virtuální sítě umožňují Azure Monitor přijímat jenom komunikace, které se odesílají z vybraných podsítí v rámci virtuální sítě.

Pomocí Log Analytics brány můžete odesílat data do pracovního prostoru Log Analytics v Azure Monitor jménem počítačů, které se nemůžou přímo připojit k Internetu, což brání tomu, aby se počítače připojovaly k Internetu. 

- [Jak nastavit privátní odkaz pro Azure Monitor](platform/private-link-security.md)

- [Připojení počítačů bez přístupu k Internetu pomocí Log Analytics brány v Azure Monitor](platform/gateway.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné**materiály: Azure monitor je základní služba a nepodporuje nasazení přímo do virtuální sítě, její základní infrastruktura je zpracována společností Microsoft. U prostředků, které vytvářejí síťová připojení k Azure Monitor nabídky, ale Zabezpečte svou síť pomocí skupiny zabezpečení sítě. Povolte protokoly toku skupin zabezpečení sítě a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku můžete také odesílat do Log Analyticsho pracovního prostoru a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Při použití Azure Monitor s privátním odkazem získáte přístup k protokolování sítě, jako jsou data zpracovaná privátním koncovým bodem (v/v).

- [Požadavky na síť pro agenty Azure Monitor](platform/log-analytics-agent.md#network-requirements)

- [Připojení počítačů bez přístupu k Internetu pomocí Log Analytics brány v Azure Monitor](platform/gateway.md)

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné**materiály: Pokud chcete povolit Azure monitor provozu, pomocí značek služby AzureMonitor povolte příchozí a odchozí provoz přes skupiny zabezpečení sítě. Pokud chcete povolit provoz testu monitorování dostupnosti, abyste dosáhli Azure Monitor, použijte značku služby ApplicationInsightsAvailability pro veškerý příchozí provoz prostřednictvím skupin zabezpečení sítě. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Pochopení a použití značek služeb](../virtual-network/service-tags-overview.md) 

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: Azure monitor je součástí služeb Azure Core a nelze je nasadit jako službu samostatně. Azure Monitor součásti, včetně agenta Azure Monitor a sady Application Insights SDK, mohou být nasazeny s vašimi prostředky a to může mít vliv na stav zabezpečení těchto prostředků.

- [Požadavky na síť pro agenty Azure Monitor](platform/log-analytics-agent.md#network-requirements)

- [Připojení počítačů bez přístupu k Internetu pomocí Log Analytics brány v Azure Monitor](platform/gateway.md) 

- [Viz Začínáme s Application Insights](./app/app-insights-overview.md#get-started)

- [Jak nastavit webové testy dostupnosti](app/monitor-web-app-availability.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny v síťových prostředcích, které souvisejí s Azure monitor. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny těchto důležitých síťových prostředků.

- [Jak zobrazit a načíst události protokolu aktivit Azure](./platform/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné**materiály: Azure monitor pomocí protokolů aktivit protokolovat změny svých prostředků. Tyto protokoly můžete exportovat do Azure Storage, centra událostí nebo pracovního prostoru Log Analytics. Ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení generovaných zařízeními koncového bodu, síťovými prostředky a dalšími systémy zabezpečení. V rámci Azure Monitor můžete provádět dotazování a provádění analýz s daty, používat Azure Storage účty pro jakékoliv dlouhodobé nebo archivní úložiště protokolů.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](platform/diagnostic-settings.md)

- [Jak shromažďovat protokoly interního hostitele virtuálních počítačů Azure pomocí Azure Monitor](learn/quick-collect-azurevm.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: Azure monitor používá protokoly aktivit, protokol aktivit je automaticky povolen a ukládá operace prováděné na Azure Monitorch prostředcích, jako například: kdo spustil operaci, kdy k operaci došlo, stavu operace a dalších užitečných informací o auditu. 

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](platform/diagnostic-settings.md)

- [Principy protokolování a různých typů protokolů v Azure](platform/platform-logs-overview.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné**materiály: v Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro jakékoliv dlouhodobé nebo archivní úložiště protokolů.

- [Změnit dobu uchovávání dat v Log Analytics](platform/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro neobvyklé chování a pravidelné prohlížení výsledků. Pomocí Azure Monitor a pracovního prostoru Log Analytics můžete prohlížet protokoly a provádět dotazy na data protokolu.

Případně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Začínáme s Log Analytics dotazy](log-query/get-started-portal.md)

- [Jak provádět vlastní dotazy v Azure Monitor](log-query/get-started-queries.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné**materiály: použití Azure Security Center s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech. Případně můžete povolit a začlenit data do Azure Sentinel.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](learn/tutorial-response.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: řízení přístupu na základě role v Azure (Azure RBAC) umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro určité prostředky jsou předem definované předdefinované role a tyto role můžou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné**materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Pomocí Azure AD Privileged Identity Management privilegovaných rolí pro služby společnosti Microsoft a Azure Resource Manager můžete také povolit přístup za běhu nebo jen tolik. 

- [Přehled Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné**materiály: kdykoli je to možné, použijte Azure Active Directory jednotného přihlašování (SSO) místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte Azure Security Center doporučení pro správu identit a přístupu.

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné**materiály: Povolte službu Azure AD MFA a sledujte doporučení pro přístup k identitě Azure Security Center.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: Použijte zabezpečenou pracovní stanici spravovanou v Azure (také známou jako pracovní stanice s privilegovaným přístupem nebo privilegovaným přístupem) pro úlohy správy, které vyžadují zvýšená oprávnění.

- [Pochopení zabezpečení pracovních stanic spravovaných Azure](../active-directory/devices/concept-azure-managed-workstation.md)

- [Jak povolit Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné**materiály: použití sestav a monitorování zabezpečení Azure Active Directory k detekci, kdy dojde k podezřelé nebo nebezpečné aktivitě v prostředí. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu. 

- [Jak identifikovat uživatele Azure AD označené příznakem rizika pro rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak monitorovat identitu uživatelů a aktivity přístupu v Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Azure AD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. 

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné**materiály: máte přístup k aktivitám přihlášení ke službě Azure AD, k auditu a rizikovým zdrojům protokolu událostí, které vám umožní integraci s jakýmkoli nástrojem Siem/monitoring. Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro Azure Active Directory uživatelských účtů a odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

- [Jak integrovat protokoly aktivit Azure do Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center Monitoring**: zrušit nastavení. Zadejte hodnotu do pole pracovní položky.

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné**materiály: použití funkcí rizika a ochrany identity v Azure AD ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Jak zobrazit rizikové přihlašování Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: Pokud je to možné, využijte značky, které vám pomůžou při sledování Azure Monitorch prostředků, které ukládají nebo zpracovávají citlivé informace, jako je například Log Analytics

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Správa přístupu k datům protokolů a pracovním prostorům ve službě Azure Monitor](platform/manage-access.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: Implementujte izolaci pomocí samostatných předplatných a skupin pro správu pro jednotlivé domény zabezpečení, jako je například typ prostředí a úroveň citlivosti dat. Můžete omezit úroveň přístupu k vašemu Azure Monitor a související prostředky, které vaše aplikace a podniková prostředí vyžadují. Přístup k Azure Monitor můžete řídit prostřednictvím Azure Active Directory řízení přístupu na základě role.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Azure monitor ve výchozím nastavení vyjednává TLS 1,2. Ujistěte se, že klienti, kteří se připojují k prostředkům Azure, můžou vyjednávat TLS 1,2 nebo vyšší. 

Application Insights a Log Analytics obou pokračovat, umožní ingestovat data TLS 1,1 a TLS 1,0. Data mohou být omezena na TLS 1,2 nakonfigurováním na straně klienta.

- [Bezpečné posílání dat pomocí protokolu TLS 1,2](platform/data-security.md#sending-data-securely-using-tls-12)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné**materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure monitor. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.
Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Pochopení ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: ke správě přístupu k Azure monitor použijte řízení přístupu na základě role (RBAC) Azure.

- [Role, oprávnění a zabezpečení v Azure Monitor](platform/roles-permissions-security.md)

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné**materiály: Azure monitor zajistí, že všechna data a uložené dotazy budou v klidovém stavu zašifrované pomocí klíčů spravovaných Microsoftem (MMK). Azure Monitor taky nabízí možnost šifrování pomocí vlastního klíče, který je uložený ve vaší Azure Key Vault a k němuž má přístup úložiště pomocí spravovaného ověřování identity přiřazené systémem. Tento klíč spravovaný zákazníkem (CMK) může být buď software, nebo hardware-HSM Protected.

- [Azure Monitor klíčů spravovaných zákazníkem](platform/customer-managed-keys.md)

- [Zabezpečení dat Log Analytics](platform/data-security.md)

- [Shromažďování, uchování a ukládání dat v nástroji Application Insights](app/data-retention-privacy.md)

- [Vysvětlení šifrování v klidovém umístění v Azure](../security/fundamentals/encryption-atrest.md) 

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné**materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro změny v Azure monitor a souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Pokyny**: použijte program pro běžné hodnocení rizik (například běžný systém vyhodnocování chyb zabezpečení) nebo výchozí hodnocení rizika poskytovaná skenovacím nástrojem třetí strany.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure CLI můžete zadávat dotazy a zjišťovat Azure monitor prostředky v rámci předplatných. Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

- [Azure Monitor CLI](/cli/azure/monitor?view=azure-cli-latest)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

- [Role, oprávnění a zabezpečení v Azure Monitor](platform/roles-permissions-security.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro Azure Monitor prostředků, které dávají metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Azure monitor souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné**materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných.

Pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky v rámci svých předplatných.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné**materiály: sjednotit inventář v pravidelných intervalech a zajistit, aby se v předplatném včas odstranily neautorizované Azure monitor související prostředky.  

- [Odstranit pracovní prostor Azure Log Analytics](platform/delete-workspace.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: pomocí Azure Policy můžete omezit, které Azure monitor související prostředky můžete zřídit ve vašem prostředí. 

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci "Microsoft Azure Management".

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí vlastních definic Azure Policy můžete auditovat nebo vymáhat konfiguraci Azure monitor souvisejících prostředků. Můžete také použít předdefinované definice Azure Policy jako dostupné.

Azure Resource Manager taky umožňuje exportovat šablonu do JavaScript Object Notation (JSON), která by se měla zkontrolovat, aby se zajistilo, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

Pro vaše prostředky Azure můžete také použít doporučení z Azure Security Center jako standardní hodnoty konfigurace zabezpečení.

Pokud používáte funkce APM živého streamování, zajistěte, aby byl kanál zabezpečený s tajným klíčem rozhraní API, a to i klíč instrumentace.

- [Zabezpečení APM Live Metrics Stream](app/live-stream.md#secure-the-control-channel)

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Doporučení zabezpečení – referenční příručka](../security-center/recommendations-reference.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: použijte zásady Azure [Deny] a [nasazení, pokud neexistují], abyste vynutili zabezpečená nastavení v rámci Azure monitor souvisejících prostředků.  Kromě toho můžete použít šablony Azure Resource Manager k údržbě konfigurace zabezpečení vašich Azure Monitor souvisejících prostředků, které vaše organizace vyžaduje.

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Přehled šablon Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné**materiály: pomocí Azure DevOps bezpečně ukládejte a spravujte kód, jako je vlastní zásady Azure a šablony Azure Resource Manager. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure monitor související prostředky pomocí Azure Policy. Pomocí vlastních definic Azure Policy můžete auditovat nebo vymáhat konfiguraci zabezpečení vašich Azure Monitor souvisejících prostředků. Můžete také využít integrované definice zásad související s vašimi konkrétními prostředky.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Aliasy Azure Policy](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné**materiály: použijte Azure Security Center k provádění kontrol standardních hodnot vašich Azure monitor souvisejících s prostředky.  Kromě toho použijte Azure Policy k upozornění a auditování konfigurace prostředků Azure.

- [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné**materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro podporované prostředky související se službou Azure monitor.

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Služby, které podporují spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Vytvoření Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Jak zajistit Key Vault ověřování pomocí spravované identity](../key-vault/general/assign-access-policy-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné**materiály: použití spravovaných identit k poskytování služby Azure s automaticky spravovanou identitou v Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Azure Monitorch prostředků bez jakýchkoli přihlašovacích údajů ve vašem kódu. 

- [Postup konfigurace spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure monitor souvisejících prostředků), ale neběží na vašem obsahu. 

Předem Prohledejte všechny nahrané soubory do příslušných Azure Monitor souvisejících prostředků, jako je například pracovní prostor Log Analytics.

K detekci malwaru nahraného do účtů úložiště použijte detekci hrozeb Azure Security Center pro datové služby. 

- [Pochopení ochrany proti malwaru Microsoftu pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

- [Vysvětlení detekce hrozeb Azure Security Center pro datové služby](../security-center/azure-defender.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: pomocí Azure Resource Manager exportujte Azure monitor a související prostředky do šablony JavaScript Object Notation (JSON), která se dá použít jako záloha pro Azure monitor a související konfigurace.  K automatickému spouštění zálohovacích skriptů použijte Azure Automation. 

- [Správa pracovního prostoru Log Analytics pomocí šablon Azure Resource Manager](./samples/resource-manager-workspace.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [O Azure Automation](../automation/automation-intro.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: pomocí Azure Resource Manager exportujte Azure monitor a související prostředky do šablony JavaScript Object Notation (JSON), která se dá použít jako záloha pro Azure monitor a související konfigurace.  Zálohování klíčů spravovaných zákazníkem v rámci služby Azure Key Vault Pokud Azure Monitor související prostředky používají klíče spravované zákazníkem, 

- [Správa pracovního prostoru Log Analytics pomocí šablon Azure Resource Manager](./samples/resource-manager-workspace.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné**materiály: Zajistěte, aby bylo možné pravidelně provádět obnovování pomocí Azure Resource Manager zálohovaných souborů šablon.  Test obnovení zálohovaných klíčů spravovaných zákazníkem

- [Správa pracovního prostoru Log Analytics pomocí šablon Azure Resource Manager](./samples/resource-manager-workspace.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné**materiály: pomocí Azure DevOps můžete bezpečně ukládat a spravovat kód, jako jsou vlastní zásady Azure, Azure Resource Manager šablony. K ochraně prostředků, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS.   K ochraně klíčů spravovaných zákazníkem použijte řízení přístupu na základě rolí. 

Kromě toho povolte Soft-Delete a vyprázdnit ochranu v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění. Pokud se Azure Storage používá k ukládání záloh šablon Azure Resource Manager, povolte obnovitelné odstranění, aby se data ukládala a obnovila při odstraňování objektů BLOB nebo snímků objektů BLOB. 

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Postup povolení ochrany Soft-Delete a vyprázdnění v Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Obnovitelné odstranění objektů blob služby Azure Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Využijte příručku pro zpracování incidentů zabezpečení počítače NIST, která vám pomůže při vytváření vlastního plánu odpovědí na incidenty.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka pomocí značek a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure, zejména těch, která zpracovávají citlivá data.  Máte zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel. 

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md) 

- [Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné**materiály: řiďte se pravidly společnosti Microsoft o zapojení, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

- [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [plánech zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)