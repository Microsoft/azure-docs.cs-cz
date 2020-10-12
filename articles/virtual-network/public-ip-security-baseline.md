---
title: Základní hodnota zabezpečení Azure pro veřejnou IP adresu Azure
description: Základní informace o zásadách zabezpečení Azure pro veřejnou IP adresu najdete v pokynech a prostředcích pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d90384644c58938bfc8a37ec1231b0d12fd60057
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90058696"
---
# <a name="azure-security-baseline-for-azure-public-ip"></a>Základní hodnota zabezpečení Azure pro veřejnou IP adresu Azure

Tato základní hodnota zabezpečení se vztahuje na pokyny od [verze Azure Security test 1,0](../security/benchmarks/overview.md) pro veřejnou IP adresu Azure. Srovnávací test zabezpečení Azure poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure. Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů týkajících se veřejné IP adresy Azure. **Ovládací prvky** , které se nevztahují na veřejnou IP adresu Azure, se vyloučily.  Poznámka: veřejné IP adresy Azure neukládají zákaznická data.

Pokud chcete zjistit, jak se veřejná IP adresa Azure kompletně mapuje na srovnávací test zabezpečení Azure, podívejte se na [úplný soubor mapování standardních hodnot zabezpečení Azure Public IP](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: veřejné IP adresy Azure se dají přiřadit značkám. Použijte značky prostředků pro skupiny zabezpečení sítě a další prostředky, které se týkají zabezpečení sítě.  Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", které zajistí, že se vytvoří všechny prostředky s značkami a upozorní vás na stávající neoznačené prostředky.  

Azure PowerShell nebo Azure CLI je možné použít k vyhledání nebo provádění akcí s prostředky na základě jejich značek. 

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags) 

- [Postup vytvoření Virtual Network Azure](quick-create-portal.md) 

- [Postup filtrování síťového provozu pomocí pravidel skupiny zabezpečení sítě](tutorial-filter-network-traffic.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace a zjišťovat změny vašich veřejných instancí IP. Kromě řídicí plochy (například Azure Portal) negenerují samotné veřejné IP adresy protokoly týkající se síťového provozu.

Veřejná IP adresa poskytuje nástroje pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve službě Azure Virtual Network.

Místo toho můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace a zjišťovat změny pro vaše veřejné instance IP. Jiná než na řídicí rovině (například Azure Portal), samotná veřejná IP adresa negeneruje protokoly auditu. Veřejná IP adresa poskytuje nástroje pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve službě Azure Virtual Network.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny**: pomocí Azure monitor nastavit dobu uchování protokolu pro pracovní prostory Log Analytics přidružené k instancím veřejné IP adresy na dodržování předpisů vaší organizace.

- [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: veřejná IP adresa poskytuje nástroje pro monitorování, diagnostiku, zobrazení metrik a povolení nebo zakázání protokolů pro prostředky ve službě Azure Virtual Network. 

Pomocí protokolu aktivit Azure můžete monitorovat konfigurace a zjišťovat změny pro vaše veřejné instance IP. 

Veřejná IP adresa sama negeneruje protokoly týkající se síťového provozu jiného než na řídicí rovině (například Azure Portal).

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Pokyny**: Konfigurace výstrah na základě protokolů aktivit souvisejících s veřejnou IP adresou. Pomocí Azure Monitor můžete nakonfigurovat výstrahu pro odesílání e-mailových oznámení, volání Webhooku nebo vyvolání aplikace logiky Azure.

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: pomocí řízení přístupu na základě role Azure (Azure RBAC) můžete spravovat přístup k prostředkům Azure, jako jsou veřejné IP instance, pomocí přiřazení rolí. Přiřaďte tyto role uživatelům, skupinám, objektům služby a spravovaným identitám. 

V inventáři nebo dotazování předem definovaných předdefinovaných rolí Azure existují pro určité prostředky prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné**materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. 

Povolený přístup za běhu pomocí Azure Active Directory (Azure AD) Privileged Identity Management (PIM) a Azure Resource Manager. 

- [Další informace o Privileged Identity Management](/azure/active-directory/privileged-identity-management)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné**materiály: povolte Azure Active Directory Multi-Factor Authentication a sledujte Security Center doporučení pro správu identit a přístupu.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s povolenou službou Azure Multi-Factor Authentication (MFA) pro přihlášení a konfiguraci prostředků souvisejících s ověřováním v Azure.

- [Pracovní stanice s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Plánování cloudového nasazení Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné**materiály: použijte Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě.

Zkontrolujte a vyakčníte zjišťování rizik Azure AD pro výstrahy a sestavy týkající se rizikového chování uživatelů.

- [Postup nasazení Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [Vysvětlení zjišťování rizik Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup k Azure Portal jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Objevte zastaralé účty pomocí protokolů v Azure Active Directory (Azure AD). 

Pomocí kontrol přístupu Azure identity můžete efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů je možné pravidelně kontrolovat a ujistit se, že uživatelé mají schválení a pokračování v přístupu.

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Pokyny**: Implementujte integraci s jakýmkoli nástrojem Siem/monitoring na základě vašeho přístupu ke službě Azure Active Directory (Azure AD) pro přihlašovací aktivity, audit a zdroje protokolů událostí.
Zjednodušte tento proces vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Nakonfigurujte požadované výstrahy v pracovním prostoru Log Analytics. 

- [Jak integrovat protokoly aktivit Azure pomocí Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné**materiály: použití funkcí ochrany identity Azure Active Directory (Azure AD) ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce související s identitami uživatelů. Ingestování dat do Azure Sentinel pro další šetření podle potřeby a na základě podnikových požadavků.
- [Jak zobrazit rizikové přihlašování Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme, abyste vytvořili a používali Azure Resource Manager prostředky, které budou předány dál.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků Azure. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Vytvoření skupin pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných.

Pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky v rámci svých předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro veřejnou IP adresu Azure pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich veřejných IP instancí Azure. Můžete také využít předdefinované definice zásad.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvořit vlastní zásady s aliasy zásad](../governance/policy/tutorials/create-custom-policy-definition.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné**materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné**materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

- [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentace k Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro veřejnou IP adresu Azure pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich veřejných IP instancí Azure.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvořit vlastní zásady s aliasy zásad](../governance/policy/tutorials/create-custom-policy-definition.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné**materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Network a vytvořte tak vlastní definice Azure Policy pro upozornění, audit a vymáhání systémových konfigurací. K automatickému vymáhání konfigurací pro prostředky Azure použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje].

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

- [Jak nakonfigurovat automatizaci pracovního postupu v rámci Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Zákazníci můžou k podpoře při vytváření vlastního plánu odpovědí na incident použít taky Průvodce zpracováním incidentů na počítači s NIST.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné**materiály: vyexportujte výstrahy a doporučení Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat ověřovací data výstrah.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné**materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft. 

- [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](/azure/security/benchmarks/overview)
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
