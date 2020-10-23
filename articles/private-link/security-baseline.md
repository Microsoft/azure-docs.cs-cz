---
title: Základní plán zabezpečení Azure pro privátní propojení Azure
description: Základní hodnoty zabezpečení služby Azure Private Link poskytují pokyny a zdroje pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: private-link
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1f9e4fd2f7f55f916743074b57f89788c0d32f7d
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427495"
---
# <a name="azure-security-baseline-for-azure-private-link"></a>Základní plán zabezpečení Azure pro privátní propojení Azure

Tato základní hodnota zabezpečení se vztahuje na doprovodné materiály z [Azure Security srovnávacích testů](../security/benchmarks/overview.md) na Azure Private. Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Obsah se seskupuje pomocí **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na privátní připojení Azure. Nevztahují se **ovládací prvky** , které se vztahují na privátní propojení Azure. Pokud chcete zjistit, jak se privátní propojení Azure kompletně mapuje na srovnávací test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot azure Virtual Network Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace prostředků a zjišťovat změny v síťových prostředcích souvisejících s privátním propojením. 

Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné**materiály: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení generovaných síťovými prostředky, jako jsou koncové body privátních odkazů, virtuální sítě a skupiny zabezpečení sítě. 

V rámci Azure Monitor můžete Log Analytics pracovní prostory použít k dotazování a provádění analýz a používání účtů Azure Storage pro dlouhodobé a archivační úložiště.

V závislosti na organizačních požadavcích společnosti můžete také povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Protokolování a sledování privátního odkazu](private-link-overview.md#logging-and-monitoring)

- [Protokolování diagnostiky pro skupinu zabezpečení sítě](../virtual-network/virtual-network-nsg-manage-log.md)

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné**materiály: povolí Azure monitor protokoly aktivit, které protokolují operace prováděné na soukromých linkách, například, kdo tuto operaci zahájil, kdy k ní došlo, stavu operace a dalších užitečných informací o auditu. 

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Zobrazení a načtení událostí protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

- [Protokolování a sledování privátního odkazu](private-link-overview.md#logging-and-monitoring)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné**materiály: u protokolů souvisejících s privátním odkazem nastavte Log Analytics dobu uchování pracovního prostoru podle předpisů pro dodržování předpisů vaší organizace v Azure monitor. Používejte účty Azure Storage pro jakékoliv dlouhodobé nebo archivní úložiště protokolů.

- [Změnit dobu uchovávání dat v Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Jak nakonfigurovat zásady uchovávání informací pro protokoly Azure Storage účtů](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro neobvyklé chování a pravidelné kontroly výsledků. Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu.

Další možností je povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Principy Log Analyticsho pracovního prostoru](../azure-monitor/log-query/get-started-portal.md)

- [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné**materiály: použijte Security Center nakonfigurované s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech.

Povolte a zapněte data do Azure Sentinel nebo SIEM třetí strany na základě podnikových požadavků vaší organizace.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: Používejte předdefinované role správce Azure Active Directory (Azure AD), které se dají explicitně přiřadit a dotazovat na ně. Spusťte modul Azure AD PowerShellu, který provede ad hoc dotazy a zjistí účty, které jsou členy skupin pro správu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné**materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí funkcí správy identit a přístupu Security Center můžete monitorovat počet účtů pro správu.

Pomocí služby Azure Active Directory (Azure AD) Privileged Identity Management privilegované role pro služby Microsoftu a Azure Resource Manager. můžete taky povolit jen přizpůsobený přístup za běhu.

- [Další informace o Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné**materiály: kdykoli je to možné, používejte jednotné přihlašování s Azure Active Directory, ale nemusíte konfigurovat jednotlivé samostatné přihlašovací údaje na službu.

- [Jednotné přihlašování k aplikacím v Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné**materiály: Povolte službu Multi-Factor Authentication (Azure AD) pro Azure Active Directory (Azure AD) a postupujte podle Security Center doporučení pro správu identit a přístupu.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Pokyny**: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s Multi-Factor Authentication nakonfigurovanou pro přihlášení k a konfiguraci síťových prostředků Azure.

- [Další informace o pracovních stanicích s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné**materiály: využití funkce detekce rizik Azure Active Directory (Azure AD) k zobrazení výstrah a sestav na základě rizikového chování uživatelů. Ingestovat výstrahy Security Center detekce rizik do Azure Monitor a nakonfigurovat vlastní výstrahy a oznámení pomocí skupin akcí.

- [Principy zjišťování rizik Azure Security Center (podezřelá aktivita)](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Jak integrovat protokoly aktivit Azure do Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Jak nakonfigurovat skupiny akcí pro vlastní výstrahy a oznámení](../azure-monitor/platform/action-groups.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data a také soli, hodnoty hash a bezpečně ukládá přihlašovací údaje uživatele.  

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Azure Active Directory poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Pomocí kontrol přístupu k identitám Azure můžete také efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Pokyny**: k integraci s jakýmkoli nástrojem Siem/monitoring použijte informace Azure Active Directory o aktivitě, auditování a zdrojích protokolu událostí pro přihlášení ke službě Azure AD.

Zjednodušte tento proces vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Nakonfigurujte požadované výstrahy v rámci Log Analytics pracovního prostoru.

- [Jak integrovat protokoly aktivit Azure do Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: použití funkcí Azure Active Directory (Azure AD) pro rizika a ochranu identity ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů pro vaše síťové prostředky. 

Ingestujte data do Azure Sentinel pro další šetření.

- [Jak zobrazit rizikové přihlašování Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: Implementujte izolaci pomocí samostatných předplatných a skupin pro správu pro jednotlivé domény zabezpečení, jako je například typ prostředí a úroveň citlivosti dat. 

Omezte úroveň přístupu k prostředkům Azure pomocí vašich aplikací a podnikových prostředí na základě podnikových požadavků. 

Řízení přístupu k prostředkům Azure prostřednictvím řízení přístupu na základě role Azure (RBAC).

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné**materiály: šifrování všech citlivých informací během přenosu. Ujistěte se, že všichni klienti, kteří se připojují k prostředkům Azure ve virtuálních sítích, můžou vyjednávat TLS 1,2 nebo vyšší. Privátní propojení nekoliduje se základními protokoly. Používejte osvědčené postupy pro jiné nabídky používané zákazníky.

Pokud je to možné, postupujte podle Security Center doporučení pro šifrování v klidovém režimu a šifrování.

- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: použití řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k datům a prostředkům, v opačném případě použití metod řízení přístupu specifických pro službu.

- [Tady se seznamte s krátkým popisem a jedinečným ID předdefinovaných rolí Azure.](../role-based-access-control/built-in-roles.md)

Pokud chcete načíst seznam rolí ve vašem prostředí, zavolejte PowerShell cmd "Get-AzRoleDefinition" nebo "AZ role definition list".

Projděte si možnosti, abyste mohli řídit expozici vaší služby prostřednictvím nastavení "viditelnost". v privátním odkazu. Tato nastavení viditelnosti rozhodují, jestli se příjemce může připojit k vaší službě. 

Zpřístupněte službu jako soukromou pro využití z jiných virtuálních sítí (jenom oprávnění Azure RBAC). Omezte expozici omezené sadě důvěryhodných předplatných nebo ji nastavte jako veřejnou, aby všechna předplatná Azure mohla vyžadovat připojení ve službě privátních odkazů.

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Vlastnosti služby privátního propojení](private-link-service-overview.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor výstrah protokolu aktivit můžete vytvořit upozornění na důležité prostředky Azure, jako jsou třeba služby a koncové body privátního propojení. 

- [Protokolování diagnostiky pro skupinu zabezpečení sítě](private-link-overview.md#logging-and-monitoring)

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny síťové prostředky, jako jsou služby privátního propojení a koncové body v rámci vašich předplatných. 

Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použijte značky pro prostředky Azure pomocí metadat k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování privátních odkazů a souvisejících prostředků. 

Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné**materiály: Vytvořte inventarizaci schválených prostředků a softwaru Azure pro výpočetní prostředky na základě potřeb vaší organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

K dotazování a zjišťování prostředků v rámci předplatných taky použijte graf Azure Resource. To může pomáhat v prostředích s vysokým zabezpečením, jako jsou ty s účty úložiště.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

- [Vzorové předdefinované služby Azure Policy pro privátní propojení](../governance/policy/samples/built-in-policies.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="67-remove-unapproved-azure-resources"></a>6,7: Odeberte neschválené prostředky Azure.

**Pokyny**: zákazník může zabránit vytváření nebo používání prostředků pomocí Azure Policy podle požadavků zásad společnosti zákazníka. Můžete implementovat vlastní proces pro odebrání neautorizovaných prostředků.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

- [Vzorové předdefinované služby Azure Policy pro privátní propojení](../governance/policy/samples/built-in-policies.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování a prosazování konfigurace síťových prostředků Azure spolu s integrovanými Azure Policy definicemi.

Azure Resource Manager má možnost Exportovat šablonu v JavaScript Object Notation (JSON). Tento artefakt by měl být přezkoumán, aby se zajistilo, že konfigurace splňují nebo překračují požadavky na zabezpečení vaší organizace.

Implementujte doporučení z Security Center jako standardní hodnoty konfigurace pro vaše prostředky Azure.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Vzorové předdefinované služby Azure Policy pro privátní propojení](../governance/policy/samples/built-in-policies.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: pomocí Azure Resource Manager šablon a Azure Policy můžete bezpečně nakonfigurovat prostředky Azure přidružené k soukromému odkazu a souvisejícím prostředkům.  

Šablony Azure Resource Manager jsou soubory založené na JavaScript Object Notation (JSON) používané k nasazení prostředků Azure. všechny vlastní šablony bude nutné ukládat a udržovat zabezpečeně v úložišti kódu. 

Použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

- [Informace o vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Porozumění efektům Azure Policy](../governance/policy/concepts/effects.md)

- [Ukázky šablon Azure Resource Manager pro privátní koncové body](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Vzorové předdefinované služby Azure Policy pro privátní propojení](../governance/policy/samples/built-in-policies.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné**materiály: pomocí Azure DevOps můžete bezpečně ukládat a spravovat kód, jako jsou vlastní zásady Azure, šablony Azure Resource Manager a požadované konfigurační skripty. 

Udělení nebo odmítnutí oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným ve službě Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps pro jejich přístup, nebo Active Directory, pokud jsou integrované s Team Foundation Server.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro prostředky Azure pomocí Azure Policy. 

Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich prostředků Azure. 

Využijte také předdefinované definice zásad týkající se jakýchkoli konkrétních prostředků spravovaných v rámci vašich předplatných.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak používat aliasy](../governance/policy/concepts/definition-structure.md#aliases)

- [Vzorové předdefinované služby Azure Policy pro privátní propojení](../governance/policy/samples/built-in-policies.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: pomocí Azure Policy auditovat konfigurace prostředků Azure. Azure Policy lze například použít ke zjišťování prostředků, které nejsou konfigurovány s privátním koncovým bodem.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vzorové předdefinované služby Azure Policy pro privátní propojení](../governance/policy/samples/built-in-policies.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné**materiály: použijte Azure Resource Manager k nasazení služeb privátního propojení, koncových bodů a souvisejících prostředků. Azure Resource Manager poskytuje možnost exportu šablon, které se dají použít jako zálohy k obnovení koncových bodů privátních odkazů a souvisejících prostředků. 

Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony.

- [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md)

- [Ukázky šablon Azure Resource Manager pro privátní koncové body](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Úvod do Azure Automation](../automation/automation-intro.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné**materiály: použijte Azure Resource Manager k nasazení služeb privátního propojení, koncových bodů a souvisejících prostředků. Azure Resource Manager poskytuje možnost exportu šablon, které se dají použít jako zálohy k obnovení koncových bodů privátních odkazů a souvisejících prostředků.  

Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony.  

Zálohování klíčů spravovaných zákazníkem v rámci Azure Key Vault.

- [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md)

- [Ukázky šablon Azure Resource Manager pro privátní koncové body](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Úvod do Azure Automation](../automation/automation-intro.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné**materiály: Ověřte schopnost pravidelně provádět nasazení Azure Resource Manager šablon pro izolované předplatné na základě vašich obchodních požadavků. 

Také ověřte obnovení zálohovaných klíčů spravovaných zákazníkem.

- [Nasazení prostředků pomocí šablon ARM a Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné**materiály: pomocí Azure DevOps bezpečně ukládejte a spravujte kód, jako je Azure Resource Manager šablon. 

Udělení nebo odmítnutí oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným ve službě Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps pro přístup k těmto prostředkům nebo ve službě Active Directory, pokud jsou integrované s Team Foundation Server.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. 

Zajistěte zápis plánů reakcí na incidenty, které definují všechny role pracovníků a fází zpracování incidentů nebo správu, od detekce po přezkoumání po jednotlivých událostech.

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Jasně označte předplatná (například produkci, neprodukční) pomocí značek a vytvořte systém pro pojmenování, který bude jasně identifikovat a kategorizovat prostředky Azure, zejména ty, které zpracovávají citlivá data.  

Zákazník je zodpovědný za to, že má navýšit nápravu výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. 

Projděte si incidenty, vystavte výskyt, abyste měli jistotu, že byly vyřešeny problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: exportovat výstrahy Security Center a doporučení pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. 

Pomocí konektoru Security Center Data můžete také streamovat výstrahy do Azure Sentinel, podle potřeby podle vašich obchodních operací.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Pokyny**: pomocí funkce automatizace pracovních postupů v Security Center můžete automaticky aktivovat odpovědi pomocí Logic Apps výstrah zabezpečení a doporučení k ochraně vašich prostředků Azure.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné**materiály: Sledujte pravidla zapojení Microsoftu a zajistěte, aby testy průniku neporušily zásady Microsoftu. 

Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

- [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](/azure/security/benchmarks/overview)
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
