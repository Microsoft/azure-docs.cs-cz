---
title: Základní plán zabezpečení Azure pro službu Azure Sentinel
description: Základní hodnoty zabezpečení Azure Sentinel poskytují pokyny a zdroje pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a827268444fdbc2cdbbe5ac1220ad7d49a2a254f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604232"
---
# <a name="azure-security-baseline-for-azure-sentinel"></a>Základní plán zabezpečení Azure pro službu Azure Sentinel

Tato základní hodnota zabezpečení se vztahuje na pokyny od [verze Azure Security test 1,0](../security/benchmarks/overview-v1.md) až Azure Sentinel. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na službu Azure Sentinel. **Ovládací prvky** , které se nevztahují na službu Azure Sentinel, se vyloučily.

 
Pokud chcete zjistit, jak se Azure Sentinel kompletně mapuje na bezpečnostní srovnávací testy zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení Azure Sentinel](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: nelze přidružit virtuální síť, podsíť ani skupinu zabezpečení sítě přímo ke službě Azure Sentinel. Můžete ale povolit privátní koncový bod Azure pro pracovní prostor Log Analytics přidružený k Azure Sentinel a omezit tak komunikaci do a z vašich privátních sítí.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které se vztahují k pracovnímu prostoru Sentinel Azure. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických prostředcích.

- [Jak povolit auditování v Azure Sentinel](resources.md)

- [Jak zobrazit a načíst události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)
 

- [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: protokoly auditu Azure Sentinel se udržují v protokolech aktivit Azure. Tato data můžete zobrazit jejich streamování z protokolu aktivit Azure do služby Azure Sentinel, kde pak můžete na svém základě provádět výzkum a analýzu.

- [Jak povolit auditování v Azure Sentinel](resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: protokoly auditu pro službu Azure Sentinel se udržují v protokolech aktivit Azure. Tato data můžete zobrazit jejich streamování z protokolu aktivit Azure do služby Azure Sentinel, kde pak můžete na svém základě provádět výzkum a analýzu.

- [Jak povolit auditování v Azure Sentinel](resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování protokolu pro Log Analytics pracovní prostory, které jsou přidružené k pracovním prostorům Sentinel Azure v souladu s předpisy pro dodržování předpisů vaší organizace.

- [Postup nastavení parametrů uchovávání protokolů](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: protokoly auditu Azure Sentinel se udržují v protokolech aktivit Azure. Tato data můžete zobrazit jejich streamování z protokolu aktivit Azure do Azure Sentinel, kde pak můžete provádět hledání a analýzy. Analyzujte a monitorujte protokoly z instancí protokolu aktivit služby Azure Sentinel pro neobvyklé chování. Pomocí oddílu Logs v pracovním prostoru Sentinel Azure můžete provádět dotazy nebo vytvářet výstrahy na základě protokolů Sentinel.

- [Jak povolit auditování v Azure Sentinel](resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: protokoly auditu Azure Sentinel se udržují v protokolech aktivit Azure. Tato data můžete zobrazit jejich streamování z protokolu aktivit Azure do Azure Sentinel, kde pak můžete provádět hledání a analýzy. Analyzujte a monitorujte protokoly z instancí protokolu aktivit služby Azure Sentinel pro neobvyklé chování. Pomocí oddílu Logs v pracovním prostoru Sentinel Azure můžete provádět dotazy nebo vytvářet výstrahy na základě protokolů Sentinel.

- [Jak povolit auditování v Azure Sentinel](resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: udržování inventáře uživatelských účtů, které mají přístup správce k rovině ovládacího prvku (například Azure Portal) pracovního prostoru služby Azure Sentinel. 

K nakonfigurování řízení přístupu na základě role Azure (Azure RBAC) můžete použít podokno identita a řízení přístupu v Azure Portal pro vaše předplatné. Role se aplikují na uživatele, skupiny, instanční objekty a spravované identity ve službě Azure Active Directory (Azure AD). Služba Azure Sentinel taky využívá Azure RBAC k poskytování předdefinovaných rolí pro správu, jako je třeba Přispěvatel Azure Sentinel, který je možné přiřadit uživatelům, skupinám a službám v Azure. 

- [Porozumění vlastním rolím](../role-based-access-control/custom-roles.md)

- [Pochopení Azure RBAC v Azure Sentinel](roles.md)

- [Jak nakonfigurovat službu Azure RBAC pro sešity](quickstart-get-visibility.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrovaných zásad Azure, jako je například:

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

Další informace jsou k dispozici na odkazovaných odkazech.

- [Použití Azure Security Center k monitorování identity a přístupu (Preview)](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné** materiály: přístup roviny ovládacího prvku pro váš pracovní prostor Azure Sentinel (např. Azure Portal) je k dispozici prostřednictvím REST API a podporuje jednotné přihlašování. Pro ověření nastavte hlavičku autorizace pro vaše požadavky na JSON Web Token, které získáte z Azure Active Directory (Azure AD).

- [Pochopení Log Analytics Azure REST API](/rest/api/loganalytics/)

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Doprovodné** materiály: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s povoleným vícefaktorového ověřováním Azure AD pro přihlášení a konfiguraci prostředků souvisejících s ověřováním Azure Sentinel. 
 

 
- [Pracovní stanice s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [Plánování nasazení vícefaktorového ověřování Azure AD založeného na cloudu](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě.

Navíc můžete pomocí zjišťování rizik Azure AD zobrazovat výstrahy a sestavy týkající se rizikového chování uživatelů.

- [Postup nasazení Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Vysvětlení zjišťování rizik Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup k Azure Portal jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální systém ověřování a autorizace pro instance služby Azure Sentinel použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak vytvořit a nakonfigurovat nového tenanta Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) jako centrální systém ověřování a autorizace pro vaše pracovní prostory Azure Sentinel. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Máte přístup k aktivitě přihlašování, auditu a rizikovým událostem služby Azure AD, které vám umožní integrovat s ověřováním Azure Sentinel nebo pomocí informací o systému a řešení pro správu událostí třetích stran.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy protokolu můžete nakonfigurovat v rámci Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Postup zprovoznění služby Azure Sentinel](quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné** materiály: pro odchylku chování přihlášení k účtu na rovině ovládacího prvku (např. Azure Portal) použijte funkce Azure Active Directory (Azure AD) Identity Protection a detekce rizik ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Jak zobrazit rizikové přihlášení ke službě Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: aktuálně není k dispozici; Customer Lockbox ještě není podporovaný pro pracovní prostor Azure Sentinel nebo Log Analytics.

- [Seznam služeb podporovaných Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Doprovodné** materiály: Implementujte samostatné odběry, které mohou volitelně zahrnovat skupiny pro správu pro vývoj, testování a produkční pracovní prostory Sentinel.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Pokud používáte Azure nebo místní virtuální počítač jako službu předávání syslog, budete muset nakonfigurovat démona syslog (rsyslog nebo syslog-ng), aby komunikovala v TLS.

- [Připojení externího řešení pomocí běžných formátů událostí](connect-common-event-format.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Pokyny**: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou v Azure k dispozici. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: v Azure Portal můžete použít podokno Správa identit a přístupu (IAM) ke konfiguraci řízení přístupu na základě role v Azure (Azure RBAC). Role se aplikují na uživatele, skupiny, instanční objekty a spravované identity ve službě Active Directory. Můžete použít předdefinované role Azure nebo vlastní role pro jednotlivce a skupiny. 

Služba Azure Sentinel používá službu Azure RBAC k poskytování předdefinovaných rolí, které je možné přiřadit uživatelům, skupinám a službám v Azure. Pomocí Azure RBAC můžete v rámci svého týmu zabezpečení provozu pomocí a vytváření rolí udělit odpovídající přístup k Sentinel Azure. Na základě rolí máte detailní kontrolu nad tím, co můžou uživatelé s přístupem k Azure Sentinel sledovat. Role Azure můžete přiřadit přímo do pracovního prostoru Azure Sentinel nebo do předplatného nebo skupiny prostředků, do které pracovní prostor patří. Existují tři konkrétní předdefinované role Sentinel Azure:

- Čtečka Sentinel Azure
- Respondér služby Azure Sentinel
- Přispěvatel Sentinel Azure

Kromě vyhrazených rolí Azure Sentinel Azure jsou k dispozici integrované role Azure a Log Analytics, které poskytují širší sadu oprávnění, která zahrnují přístup k vašemu pracovnímu prostoru Azure Sentinel a dalším prostředkům:

Role Azure zahrnují vlastníka, přispěvatele a čtenáře. Role Azure udělují přístup napříč všemi prostředky Azure, včetně Log Analyticsch pracovních prostorů a prostředků Sentinel Azure.

Mezi role Log Analytics patří Log Analytics přispěvatel a Log Analytics Reader. Role Log Analytics udělují přístup ve všech vašich pracovních prostorech Log Analytics.

Každý sešit Sentinel je navíc prostředkem Azure a pro správu přístupu můžete přiřadit role uživatelům.

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Porozumění vlastním rolím](../role-based-access-control/custom-roles.md)

- [Pochopení rolí v Sentinel a Log Analytics](roles.md)

- [Jak nakonfigurovat službu Azure RBAC pro sešity](quickstart-get-visibility.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: pracovní prostory Azure Sentinel a Azure monitor Log Analytics aktuálně používají klíče spravované společností Microsoft k šifrování libovolných obsažených dat v klidovém prostředí. Možnost přenášet vlastní klíč ještě není plně podporovaná pro Sentinel, ale v blízké budoucnosti bude. 

 
- [Přehled Azure Monitor klíč spravovaný zákazníkem](https://docs.microsoft.com/azure/azure-monitor/logs/customer-managed-keys#customer-managed-key-overview) 

 
- [Vysvětlení klíčů spravovaných zákazníkem v Azure Sentinel (Preview)](customer-managed-keys.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: protokoly auditu Azure Sentinel se udržují v protokolech aktivit Azure. Tato data můžete zobrazit jejich streamování z protokolu aktivit Azure do služby Azure Sentinel, kde pak můžete na svém základě provádět výzkum a analýzu. V části "protokoly" v pracovním prostoru Azure Sentinel můžete vytvořit upozornění na změny v produkčních pracovních prostorech Azure Sentinel i na další důležité nebo související prostředky.

- [Jak povolit auditování v Azure Sentinel](resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme, abyste vytvořili a používali Azure Resource Manager prostředky, které budou předány dál.

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

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků Azure. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných zákazníka pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

Další informace jsou k dispozici na odkazovaných odkazech.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných.

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro váš pracovní prostor Log Analytics přidružený k vašemu pracovnímu prostoru Sentinel pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. OperationalInsights můžete vytvořit vlastní zásady, které budou auditovat nebo vysazovat konfiguraci vašich Log Analytics pracovních prostorů.
 

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: použití zásad Azure [Deny] a [nasazení, pokud neexistuje] efekty pro vymáhání zabezpečených nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice zásad Azure, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.
 

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

 
- [Dokumentace k Azure Repos](/azure/devops/repos/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. OperationalInsights, abyste vytvořili vlastní zásady pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. OperationalInsights, abyste vytvořili vlastní zásady pro upozornění, audit a prosazování konfigurace systému. Použijte zásady Azure [audit], [Deny] a [nasazení, pokud neexistují] efekty pro automatické vymáhání konfigurací pro prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Pokyny**: Azure Sentinel podporuje shromažďování protokolů z mnoha zdrojů pomocí různých konektorů. Některé z těchto konektorů vyžadují instalaci pomocí klíče pracovního prostoru Log Analytics. Při nastavování těchto konektorů použijte Azure Key Vault k ukládání klíčů pro zjednodušení správy tajných kódů a zabránění náhodnému vystavení přihlašovacích údajů.

- [Připojení Sentinel ke zdrojům dat](connect-data-sources.md)

- [Vytvoření Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Jak zajistit Key Vault ověřování pomocí spravované identity](../key-vault/general/assign-access-policy-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Sentinel a Log Analytics), ale neběží na zákaznickém obsahu. 

Vaše zodpovědnost za veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, včetně pracovního prostoru Log Analytics, vám umožní předem prověřit. Microsoft nemůže získat přístup k zákaznickým datům, a proto nemůže za vás provádět kontrolu proti malwarovým kontrolám zákaznického obsahu.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: povolení Soft-Delete v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění. V případě, že uživatel Odvolá šifrovací klíč klíče, a to buď odstraněním nebo odebráním přístupu pro službu Azure Sentinel, během jedné hodiny Azure Sentinele změní a bude fungovat, jako by data už nebudou dostupná. V tomto okamžiku bude znemožněna jakákoli operace, která využívá trvalé prostředky úložiště, jako je například ingestování dat, trvalé změny konfigurace a vytvoření incidentu. Dříve uložená data nebudou smazána, ale zůstanou nepřístupná. Nepřístupná data se řídí zásadami uchovávání dat a budou se v souladu s těmito zásadami vyprázdnit.

Jediná operace, kterou je možné provést po odvolání nebo odstranění šifrovacího klíče, je odstranění účtu.

Pokud se po odvolání obnoví přístup, Azure Sentinel obnoví přístup k datům během hodiny.

- [Postup povolení Soft-Delete v Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Vysvětlení klíčů spravovaných zákazníkem v Azure Sentinel](customer-managed-keys.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

 

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

 

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](connect-azure-security-center.md)

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
