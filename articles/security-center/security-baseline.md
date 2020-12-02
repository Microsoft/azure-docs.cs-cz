---
title: Základní hodnoty zabezpečení Azure pro Security Center
description: Základní Security Center zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3b648168cd80920b8042f1edeacca893c21b98e2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498944"
---
# <a name="azure-security-baseline-for-security-center"></a>Základní hodnoty zabezpečení Azure pro Security Center

Tato základní hodnota zabezpečení se vztahuje na pokyny z [srovnávacího testu zabezpečení Azure](../security/benchmarks/overview.md) na Azure Security Center. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure Security Center. **Ovládací prvky** , které se nevztahují k Azure Security Center byly vyloučeny. Pokud chcete zjistit, jak Azure Security Center kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Security Center Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: Azure Security Center je základní nabídka Azure. Virtuální síť, podsíť ani skupinu zabezpečení sítě nelze přidružit přímo k Security Center. Pokud povolíte shromažďování dat pro výpočetní prostředky, Security Center ukládá data, která shromažďuje prostřednictvím pracovního prostoru Log Analytics, můžete tento pracovní prostor nakonfigurovat tak, aby používal privátní odkaz pro přístup k datům pracovního prostoru přes privátní koncový bod ve vaší virtuální síti. Také pokud použití shromažďování dat Security Center spoléhá na Log Analytics agenta nasazeného na vaše servery ke shromažďování dat zabezpečení a zajištění ochrany těchto výpočetních prostředků. Agent Log Analytics vyžaduje, aby byly pro správnou operaci s Security Center otevřené konkrétní porty a protokoly. Uzamknout sítě, aby povolovaly jenom tyto požadované porty a protokoly, a přidejte jenom další pravidla, která vaše aplikace vyžaduje k tomu, aby fungovala přes skupiny zabezpečení sítě.

- [Shromažďování dat v Azure Security Center](security-center-enable-data-collection.md)

- [Souborového síťový provoz se skupinou zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)

- [Požadavky na bránu firewall pro použití agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Principy privátního odkazu Azure](../private-link/private-link-overview.md) 

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: Nabídka Azure Security Center není přímo integrována s virtuální sítí, ale může shromažďovat data ze serverů konfigurovaných pomocí agenta Log Analytics nasazeného ve vašich sítích. Servery, které jsou nakonfigurovány k odesílání dat Security Center vyžadují, aby určité porty a protokoly mohly správně komunikovat. Definování a prosazování standardních konfigurací zabezpečení pro tyto síťové prostředky pomocí Azure Policy.

Pomocí plánů Azure můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, přiřazení rolí a Azure Policy přiřazení v rámci jedné definice podrobného plánu. Pro nasazení Security Center konfigurací a souvisejících síťových prostředků můžete použít podrobný plán na nové předplatné.

- [Shromažďování dat v Azure Security Center](security-center-enable-data-collection.md)

- [Požadavky na bránu firewall pro použití agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Ukázky Azure Policy pro sítě](../governance/policy/samples/built-in-policies.md#network)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: Nabídka Azure Security Center není přímo integrována s virtuální sítí, ale může shromažďovat data ze serverů konfigurovaných pomocí agenta Log Analytics nasazeného ve vašich sítích. Servery, které jsou nakonfigurovány k odesílání dat Security Center vyžadují, aby určité porty a protokoly mohly správně komunikovat. Definování a prosazování standardních konfigurací zabezpečení pro tyto síťové prostředky pomocí Azure Policy.

Použijte značky prostředků pro skupiny zabezpečení sítě a další prostředky, jako jsou servery ve vašich sítích, které jsou nakonfigurované tak, aby odesílaly protokoly zabezpečení Azure Security Center. Pro jednotlivá pravidla skupiny zabezpečení sítě použijte pole Popis k dokumentaci pravidel, která umožňují provoz do/ze sítě. 

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledat nebo provádět akce s prostředky na základě jejich značek. 

- [Shromažďování dat v Azure Security Center](security-center-enable-data-collection.md)

- [Požadavky na bránu firewall pro použití agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md) 

- [Postup vytvoření Virtual Network Azure](../virtual-network/quick-create-portal.md) 

- [Postup filtrování síťového provozu pomocí pravidel skupiny zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny síťových prostředků, které souvisejí s Azure Security Center. V Azure Monitor můžete vytvářet výstrahy, které vám upozorní na to, kdy probíhají změny kritických prostředků.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log) 

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: agregovaná data zabezpečení vygenerovaná Azure Security Center a připojenými zdroji pomocí centrálního pracovního prostoru Log Analytics. 

Nakonfigurujte shromažďování dat Security Center pro posílání dat zabezpečení a událostí z připojených výpočetních prostředků Azure do pracovního prostoru centrálního Log Analytics. Kromě shromažďování dat můžete pomocí funkce průběžného exportu streamovat výstrahy zabezpečení a doporučení vygenerovaná Security Center do pracovního prostoru centrálního Log Analytics. V Azure Monitor se můžete dotazovat a provádět analýzy dat zabezpečení vygenerovaných z Security Center a vašich připojených prostředků Azure. 

Případně můžete odesílat data vytvořená Security Center do Azure Sentinel nebo do SIEM třetí strany.

- [Průběžný export Security Center dat](continuous-export.md)

- [Shromažďování dat v Azure Security Center](security-center-enable-data-collection.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Jak shromažďovat protokoly interního hostitele virtuálních počítačů Azure pomocí Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit Azure monitor jsou automaticky dostupné, tyto protokoly obsahují všechny operace zápisu pro váš prostředek, jako je Azure Security Center, včetně toho, jaké operace byly provedeny, kdy operace začala a kdy k nim došlo. Odešlete protokoly aktivit Azure do pracovního prostoru Log Analytics pro konsolidaci protokolů a zvýšené uchovávání.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Odeslání protokolů aktivit do Log Analytics pracovního prostoru](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště. 

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: Analyzujte a monitorujte protokoly vytvořené Azure Security Center a jeho připojenými zdroji pro chování neobvyklé a pravidelné revize výsledků. Pomocí Azure Monitor a pracovního prostoru Log Analytics můžete prohlížet protokoly a provádět dotazy na data protokolu.

Případně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Začínáme s Log Analytics dotazy](../azure-monitor/log-query/log-analytics-tutorial.md) 

- [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: Konfigurace výstrah protokolu Azure monitor pro dotazování na nechtěné nebo neobvyklé aktivity zaznamenané protokolem aktivit nebo daty vytvořenými Azure Security Center. Nastavte skupiny akcí tak, aby se vaše organizace upozornila, a když se pro aktivitu neobvyklé iniciuje upozornění protokolu. Pomocí funkce automatizace pracovního postupu Security Center můžete aktivovat Logic Apps na výstrahách zabezpečení a doporučeních. Pracovní postupy Security Center lze použít k upozornění uživatelů na reakci na incidenty nebo k nápravě prostředků na základě informací o výstrahách.

Případně můžete povolit a začlenit data související s a vyprodukovanými Azure Security Center do Azure Sentinel. Sentinel Azure podporuje playbooky, které umožňují automatické reakce na hrozby související s zabezpečením.

- [Automatizace pracovního postupu Azure Security Center](workflow-automation.md)

- [Správa výstrah v Azure Security Center](security-center-managing-and-responding-alerts.md) 

- [Upozornění na data protokolu Log Analytics](../azure-monitor/learn/tutorial-response.md)

- [Nastavení automatizovaných reakcí na hrozby ve službě Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

- [Protokolování výstrah v Azure Monitor](../azure-monitor/platform/alerts-unified-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: řízení přístupu na základě role v Azure (Azure RBAC) umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro určité prostředky existují předdefinované role, které je možné inventarizovat nebo dotazovat pomocí nástrojů, jako jsou Azure CLI, Azure PowerShell nebo Azure Portal. Azure Security Center má předdefinované role pro nástroj Security Reader nebo Security admin, který umožňuje uživatelům číst nebo aktualizovat zásady zabezpečení a odpustit výstrahy a doporučení.

- [Oprávnění ve službě Azure Security Center](security-center-permissions.md)

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu pro platformu Azure nebo specificky pro Azure Security Center nabídku. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu v Azure Active Directory. Security Center má také předdefinované role pro "admin admin", které umožňují uživatelům aktualizovat zásady zabezpečení a zastavovat výstrahy a doporučení, nezapomeňte si projít a sjednotit všechny uživatele, kteří mají toto přiřazení role v pravidelných intervalech.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrovaných zásad Azure, jako je například:

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

- [Oprávnění ve službě Azure Security Center](security-center-permissions.md)

- [Použití Azure Security Center k monitorování identity a přístupu (Preview)](security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné** materiály: kdykoli je to možné, použijte Azure Active Directory jednotného přihlašování (SSO) místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte Azure Security Center doporučení pro identitu a přístup.

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: povolení Azure Active Directory MFA pro přístup k Azure Security Center a Azure Portal, postupujte podle všech Security Centerch doporučení identity a přístupu. 

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: Použijte zabezpečenou pracovní stanici spravovanou v Azure (také známou jako pracovní stanice s privilegovaným přístupem nebo privilegovaným přístupem) pro úlohy správy, které vyžadují zvýšená oprávnění.

- [Pochopení zabezpečení pracovních stanic spravovaných Azure](../active-directory/devices/concept-azure-managed-workstation.md)

- [Jak povolit Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití sestav a monitorování zabezpečení Azure Active Directory k detekci, kdy dojde k podezřelé nebo nebezpečné aktivitě v prostředí. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md) 

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění Azure AD povolte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí. 

- [Jak nakonfigurovat pojmenovaná umístění služby Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: použití služby Azure Active Directory (Azure AD) jako centrálního ověřování a autorizačního systému při použití Azure Security Center. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele. Azure Security Center má předdefinované role, které se přiřazují jako zabezpečení admin, které umožňuje uživatelům aktualizovat zásady zabezpečení a zastavovat výstrahy a doporučení.

- [Oprávnění ve službě Azure Security Center](security-center-permissions.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. K efektivní správě členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí můžete navíc použít kontroly identity a přístupu v Azure AD. Přístup uživatelů v souvislosti s Azure Security Center se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. 

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml) 

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: máte přístup ke zdrojům přihlašovacích aktivit, auditem a rizikovým protokolům událostí Azure AD, které vám umožní integraci s jakýmkoli nástrojem Siem/monitoring. 

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.  

- [Jak integrovat protokoly aktivit Azure pomocí Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu 

**Pokyny**: pomocí Azure AD identity Protectionch funkcí můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření. 

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: pomocí značek můžete posloužit ke sledování prostředků Azure, jako je Log Analytics pracovní prostor, který ukládá citlivé informace o zabezpečení z Azure Security Center.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: Implementujte izolaci pomocí samostatných předplatných a skupin pro správu pro jednotlivé domény zabezpečení, jako je například typ prostředí a úroveň citlivosti dat. Můžete omezit úroveň přístupu k prostředkům Azure, které vaše aplikace a podniková prostředí vyžadují. Přístup k prostředkům Azure můžete řídit prostřednictvím Azure RBAC.

Ve výchozím nastavení se Azure Security Center data ukládají do back-endové služby Security Center. Pokud vaše organizace přidala požadavky na ukládání těchto dat ve vlastních zdrojích, můžete nakonfigurovat Log Analytics pracovní prostor pro ukládání Security Centerch dat, výstrah a doporučení. Při používání vlastního pracovního prostoru můžete přidat další oddělení konfigurací různých pracovních prostorů v závislosti na tom, ve kterém prostředí data pocházejí.

- [Průběžný export Security Center dat](continuous-export.md)

- [Shromažďování dat v Azure Security Center](security-center-enable-data-collection.md)

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md) 

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md) 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: šifrování všech citlivých informací během přenosu. Ujistěte se, že klienti, kteří se připojují k prostředkům Azure, můžou vyjednávat TLS 1,2 nebo vyšší. Všechny virtuální počítače, které jsou nakonfigurované s agentem Log Analytics a posílat data do služby Azure Security Center, by měly být nakonfigurované tak, aby používaly protokol TLS 1,2.

Pokud je to možné, postupujte podle Azure Security Center doporučení pro šifrování v klidovém režimu a šifrování. 

- [Bezpečné posílání dat do Log Analytics](../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)

- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC. 

**Pokyny**: pomocí řízení přístupu na základě role Azure (Azure RBAC) můžete spravovat přístup k datům a prostředkům souvisejícím s Azure Security Center. Azure Security Center má předdefinované role pro nástroj Security Reader nebo Security admin, který umožňuje uživatelům číst nebo aktualizovat zásady zabezpečení a odpustit výstrahy a doporučení. Pracovní prostor Log Analytics, ve kterém jsou uložena data shromážděná Security Center obsahuje také předdefinované role, které můžete přiřadit jako ' Log Analytics Reader ', ' Log Analytics Přispěvatel ' a další. Přiřaďte nejnižší povolující roli, kterou uživatelé potřebují k dokončení požadovaných úkolů. Přiřaďte například roli Čtenář uživatelům, kteří potřebují jenom zobrazit informace o stavu zabezpečení prostředku, ale neprovádějí akci, jako je třeba použití doporučení nebo úprav zásad.

- [Oprávnění pro pracovní prostor Azure Log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Oprávnění ve službě Azure Security Center](security-center-permissions.md)

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: Azure Security Center používá nakonfigurovaný pracovní prostor Log Analytics k ukládání dat, upozornění a doporučení, která generuje. Nakonfigurujte klíč spravovaný zákazníkem (CMK) pro pracovní prostor, který jste nakonfigurovali pro Security Center shromažďování dat. CMK povolí šifrování všech dat uložených v pracovním prostoru a jejich odeslání pomocí Azure Key Vaultho klíče vytvořeného a vlastněného vámi. 

- [Klíč spravovaný zákazníkem v Azure Monitoru](../azure-monitor/platform/customer-managed-keys.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor můžete vytvářet výstrahy, když se změny projeví u důležitých prostředků Azure souvisejících s Azure Security Center. Tyto změny mohou zahrnovat akce, které upravují konfigurace související se Security Center, jako je například zakázání výstrah nebo doporučení, nebo aktualizace nebo odstranění úložišť dat.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

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

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat na všechny prostředky související s Azure Security Center ve vašich předplatných. Zajistěte, aby ve vašem tenantovi byla vhodná (čtení) oprávnění, a vytvořte výčet všech předplatných Azure pro zjišťování Security Centerch prostředků 

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md) 

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: pomocí značek můžete posloužit ke sledování prostředků Azure, jako je Log Analytics pracovní prostor, který ukládá citlivé informace o zabezpečení z Azure Security Center.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Azure Security Centerch prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných. 

Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: Odeberte prostředky Azure související s Azure Security Center, když už je nepotřebujete v rámci inventarizace a procesu kontroly vaší organizace.

- [Odstranění prostředku a skupiny prostředků Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Security Center a jejího připojeného pracovního prostoru prostřednictvím Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. OperationalInsights a Microsoft. Security můžete vytvořit vlastní definice Azure Policy pro auditování nebo vymáhání konfigurace Security Center a Log Analytics pracovního prostoru.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použití efektů Azure Policy pro Deny a deploy, pokud neexistuje, aby bylo možné vynutilit zabezpečená nastavení napříč prostředky Azure. Kromě toho můžete použít šablony Azure Resource Manager k údržbě konfigurace zabezpečení vašich prostředků Azure, které vaše organizace vyžaduje. 

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md) 

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md) 

- [Přehled šablon Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: použijte Azure DevOps k bezpečnému ukládání a správě kódu, jako jsou vlastní definice Azure Policy, Azure Resource Manager šablony a požadované konfigurační skripty stavu. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS. 

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro prostředky Azure pomocí Azure Policy. Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace Azure Security Center souvisejících prostředků. Kromě toho můžete použít Azure Automation k nasazení změn konfigurace. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak používat aliasy](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: Používejte předdefinované definice Azure Policy a také Azure Policy aliasy v Microsoft. OperationalInsights a Microsoft. Zabezpečení: obory názvů pro vytváření vlastních zásad pro upozornění, audit a prosazování konfigurací prostředků Azure. K automatickému vymáhání konfigurací pro prostředky Azure použijte zásady Azure pro audit, odepřít a nasazení, pokud neexistuje.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: Azure Security Center používá nakonfigurovaný pracovní prostor Log Analytics k ukládání dat, upozornění a doporučení, která generuje. Nakonfigurujte klíč spravovaný zákazníkem (CMK) pro pracovní prostor, který jste nakonfigurovali pro Security Center shromažďování dat. CMK povolí šifrování všech dat uložených v pracovním prostoru a jejich odeslání pomocí Azure Key Vaultho klíče vytvořeného a vlastněného vámi. 

- [Klíč spravovaný zákazníkem v Azure Monitoru](../azure-monitor/platform/customer-managed-keys.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Doprovodné** materiály: Azure Security Center neslouží k ukládání nebo zpracování souborů. Vaše zodpovědnost za veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, včetně pracovního prostoru Log Analytics, vám umožní předem prověřit.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování 

**Doprovodné** materiály: Sledujte přístup infrastruktury jako kódu (IAC) a použijte Azure Resource Manager k nasazení Azure Security Center souvisejících prostředků v šabloně JavaScript Object Notation (JSON), která se dá použít jako záloha pro konfigurace související s prostředky.

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Šablony Azure Resource Manager pro prostředek zabezpečení](/azure/templates/microsoft.security/allversions)

- [O Azure Automation](../automation/automation-intro.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: Azure Security Center používá pracovní prostor Log Analytics k ukládání dat, výstrah a doporučení, která generuje. Můžete nakonfigurovat Azure Monitor a pracovní prostor, který Security Center používá k povolení klíče spravovaného zákazníkem. Pokud používáte Key Vault k ukládání klíčů spravovaných zákazníkem, zajistěte pravidelné automatizované zálohování vašich klíčů.

- [Postup zálohování klíčů Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: Zajistěte, aby bylo možné pravidelně provádět obnovování pomocí Azure Resource Manager zálohovaných souborů šablon. Test obnovení zálohovaných klíčů spravovaných zákazníkem

- [Správa pracovního prostoru Log Analytics pomocí šablon Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: pomocí Azure DevOps bezpečně ukládejte a spravujte kód, třeba vlastní definice Azure Policy a šablony Azure Resource Manager. K ochraně prostředků, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS. K ochraně klíčů spravovaných zákazníkem použijte řízení přístupu na základě role Azure.

Kromě toho povolte Soft-Delete a vyprázdnit ochranu v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.  Pokud se Azure Storage používá k ukládání záloh šablon Azure Resource Manager, povolte obnovitelné odstranění, aby se data ukládala a obnovila při odstraňování objektů BLOB nebo snímků objektů BLOB. 

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Postup povolení ochrany Soft-Delete a vyprázdnění v Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

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

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete označit odběry pomocí značek a vytvořit systém pojmenování k identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data.  Je vaše zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu. 

- [Výstrahy zabezpečení ve službě Azure Security Center](security-center-alerts-overview.md) 

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a pak podle potřeby upravte plán odpovědí. 

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy. 

- [Jak nastavit kontakt zabezpečení pro Azure Security Center](security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel. 

- [Postup konfigurace průběžného exportu](continuous-export.md) 

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovních postupů Azure Security Center k automatickému spouštění odpovědí na výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure. 

- [Jak nakonfigurovat automatizaci pracovního postupu v Security Center](workflow-automation.md)

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