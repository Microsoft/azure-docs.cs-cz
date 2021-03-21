---
title: Směrný plán zabezpečení Azure pro sdílení dat Azure
description: Základní hodnoty zabezpečení Azure Data Share poskytují pokyny a zdroje pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: data-share
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d423a8aaf3ace78ccb4b2eb1d8424c6a1dfb61bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101705972"
---
# <a name="azure-security-baseline-for-azure-data-share"></a>Směrný plán zabezpečení Azure pro sdílení dat Azure

Tato základní hodnota zabezpečení se vztahuje na pokyny od [Azure Security test 1,0](../security/benchmarks/overview-v1.md) pro Microsoft Azure sdílení dat. Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah se seskupuje podle **ovládacích prvků zabezpečení** , které definuje srovnávací test zabezpečení Azure, a souvisejících pokynů vztahujících se na Azure Data Share. **Ovládací prvky** , které se nevztahují na sdílenou složku Azure, se vyloučily.

 
Pokud chcete zjistit, jak se Azure Data Share kompletně mapuje na srovnávací test zabezpečení Azure, přečtěte si [kompletní soubor mapování standardních hodnot zabezpečení Azure Data Share](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: ingestování protokolů souvisejících s Azure Data Share prostřednictvím Azure monitor k agregaci dat zabezpečení generovaných zařízeními koncových bodů, síťovými prostředky a dalšími systémy zabezpečení. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Případně můžete tato data povolit a zařadit do Azure Sentinel nebo SIEM třetí strany.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) 

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: protokoly aktivit, které jsou automaticky k dispozici, obsahují všechny operace zápisu (Put, post, DELETE) pro prostředky Azure Data Share s výjimkou operací čtení (Get). Protokoly aktivit se dají použít k nalezení chyby při odstraňování potíží nebo sledování, jak uživatel ve vaší organizaci změnil prostředek.

Povolte diagnostické protokoly pro Azure Data Share, konkrétně protokoly diagnostiky pro MicrosoftDataShareSentShareSnapshotsLog &amp; MicrosoftDataShareReceivedShareSnapshotsLog. Tyto protokoly vám umožní zachytit klíčové informace, jako je čas zahájení synchronizace, čas ukončení, stav a další podrobnosti. Tyto protokoly můžou být klíčové pro pozdější zkoumání incidentů zabezpečení a provádění forenzních cvičení.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) 

- [Principy protokolování a různých typů protokolů v Azure](/azure/azure-monitor/platform/platform-logs-overview)

- [Jak nakonfigurovat nastavení diagnostiky pro protokol aktivit Azure](/azure/azure-monitor/platform/activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: Zajistěte, aby všechny účty úložiště nebo pracovní prostory Log Analytics používané pro ukládání protokolů sdílené složky Azure měly dobu uchování protokolu nastavenou v souladu s předpisy pro dodržování předpisů vaší organizace.

- [Jak nakonfigurovat dobu uchování Log Analytics pracovního prostoru](/azure/azure-monitor/platform/manage-cost-storage)

- [Ukládání protokolů prostředků v Azure Storagem účtu](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů souvisejících s prostředky sdílené složky Azure pro chování neobvyklé a pravidelné kontroly výsledků. Pomocí Azure Monitor a pracovního prostoru Log Analytics můžete prohlížet protokoly a provádět dotazy na data protokolu.

Případně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Začínáme s Log Analytics dotazy](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Jak provádět vlastní dotazy v Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použití Azure Security Center s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech. Případně můžete povolit a začlenit data do Azure Sentinel.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md) 

- [Upozornění na data protokolu Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné** materiály: Azure Data Share podporuje ověřování pomocí jednotného přihlašování s Azure Active Directory (Azure AD). Snižte počet identit a přihlašovacích údajů, které uživatelé musí spravovat, povolením jednotného přihlašování pro službu s již existujícími identitami vaší organizace.

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure AD a postupujte Azure Security Center doporučení pro přístup k identitě a přístupu.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: Použijte zabezpečenou pracovní stanici spravovanou v Azure (také známou jako pracovní stanice s privilegovaným přístupem nebo privilegovaným přístupem) pro úlohy správy, které vyžadují zvýšená oprávnění.

- [Pochopení zabezpečení pracovních stanic spravovaných Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Postup povolení vícefaktorového ověřování Azure AD](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Azure Data Share pracuje s obecnými integrovanými rolemi Azure ](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure AD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. K efektivní správě členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí můžete navíc použít kontroly identity a přístupu v Azure AD. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/) 

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Azure Data Share pracuje s obecnými integrovanými rolemi Azure ](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: máte přístup k Azure Active Directory (Azure AD) pro přihlašovací aktivity, audit a zdroje protokolu událostí, které vám umožní integraci s jakýmkoli nástrojem Siem/monitoring.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

- [Jak integrovat protokoly aktivit Azure pomocí Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné** materiály: použití funkcí ochrany identity Azure Active Directory (Azure AD) ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce související s identitami uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: ke správě přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: pomocí řízení přístupu na základě role Azure (Azure RBAC) můžete spravovat přístup k datům a prostředkům souvisejícím s prostředky sdílené složky Azure, jinak používat metody řízení přístupu ke konkrétním službám.

- [Konfigurace řízení přístupu na základě role Azure](../role-based-access-control/role-assignments-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet Azure monitor výstrahy, pro které se změny projeví u kritických prostředků Azure.

- [Vytvoření upozornění pro události protokolu aktivit Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: Azure Data Share neumožňuje instalaci nástrojů pro kontrolu ohrožení zabezpečení pro své prostředky.

Obecně platí, že pro posouzení ohrožení zabezpečení na virtuálních počítačích Azure, imagí kontejneru a SQL serverech použijte doporučení od Azure Security Center.

K provádění posouzení ohrožení zabezpečení v síťových zařízeních a webových aplikacích použijte řešení třetí strany. Při provádění vzdálených kontrol nepoužívejte jediný, trvalá a administrativní účet. Zvažte implementaci metodologie zřizování JIT pro účet skenování. Přihlašovací údaje pro účet pro kontrolu by měly být chráněné, monitorované a používané jenom pro kontrolu ohrožení zabezpečení.

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Doprovodné** materiály: u svých prostředků Azure, skupin prostředků a předplatných můžete použít značky k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure, skupiny prostředků a předplatná k jejich logickému uspořádání do taxonomie. Každá značka se skládá z názvu a páru hodnot. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry tam, kde je to vhodné, k uspořádání a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných.
Pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky v rámci svých předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: odebrání prostředků Azure, když už nepotřebujete, můžete to udělat prostřednictvím Azure Portal, PowerShellu nebo rozhraní příkazového řádku.

- [Odstranění prostředku a skupiny prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)

Azure Data Share nezveřejňuje operační systém ani neumožňuje instalovat softwarové aplikace třetích stran na svých prostředcích.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení služeb, které můžete zřídit ve vašem prostředí.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci "Microsoft Azure Management".

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: použijte Azure DevOps k bezpečnému ukládání a správě kódu, jako jsou vlastní definice Azure Policy, Azure Resource Manager šablony a požadované konfigurační skripty stavu. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps, nebo Active Directory, pokud je integrovaná se sadou TFS.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů datashare k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak používat aliasy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: použití spravovaných identit k poskytování služby Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů v kódu.

- [Postup konfigurace spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
