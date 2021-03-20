---
title: Základní hodnoty zabezpečení Azure pro Container Registry
description: Základní Container Registry zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: container-registry
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 795b938913dad149739c4591bc2a9c221d784c84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716363"
---
# <a name="azure-security-baseline-for-container-registry"></a>Základní hodnoty zabezpečení Azure pro Container Registry

Tato základní hodnota zabezpečení platí pro Container Registry pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Container Registry. **Ovládací prvky** , které se nevztahují k Container registry byly vyloučeny.

 
Pokud chcete zjistit, jak Container Registry kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Container Registry Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: Azure Virtual Network poskytuje zabezpečenou a privátní síť pro vaše Azure a místní prostředky. Omezením přístupu k privátní službě Azure Container Registry z Azure Virtual Network zajistíte, aby se do registru přistupují pouze prostředky ve virtuální síti. U různých scénářů můžete také nakonfigurovat pravidla brány firewall tak, aby povolovala přístup k registru jenom z konkrétních IP adres.
Z za bránou firewall nakonfigurujte pravidla přístupu brány firewall a značky služeb pro přístup k registru kontejneru.

- [Omezení přístupu ke službě Azure Container Registry pomocí virtuální sítě Azure nebo pravidel brány firewall](container-registry-vnet.md) 

- [Konfigurace pravidel pro přístup ke službě Azure Container Registry za bránou firewall](container-registry-firewall-access-rules.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) .

**Azure Policy předdefinované definice – Microsoft. ContainerRegistry**:

[!INCLUDE [Resource Policy for Microsoft.ContainerRegistry 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.containerregistry-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Pokyny**: použijte Azure Security Center a opravte doporučení k ochraně sítě, abyste chránili vaše síťové prostředky v Azure. Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Ochrana síťových prostředků](../security-center/security-center-network-recommendations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: povolení DDoS Standard Protection ve vašich virtuálních sítích pro ochranu před útoky DDoS. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb. Nasaďte Azure Firewall na všech hranicích sítě organizace s povolenou funkcí Analýza hrozeb a nakonfigurované na "výstrahy a zamítnutí" pro škodlivý síťový provoz.

K nakonfigurování skupin zabezpečení sítě můžete použít Azure Security Center jenom v čase, abyste mohli omezit expozici koncových bodů ke schváleným IP adresám po omezenou dobu.
Pomocí Azure Security Center adaptivního posílení zabezpečení sítě můžete také doporučit konfigurace NSG, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb.

- [Jak nakonfigurovat DDoS Protection](/azure/virtual-network/manage-ddos-protection)
- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)
- [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/security-center-alerts-service-layer.md)
- [Pochopení Azure Security Center adaptivního posílení zabezpečení sítě](../security-center/security-center-adaptive-network-hardening.md)
- [Azure Security Center pouze v čase Access Control sítě](../security-center/security-center-just-in-time.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: povolení protokolů toků skupin zabezpečení sítě (NSG) pro NSG připojené k podsíti, která se používá k ochraně služby Azure Container Registry. Můžete nahrávat protokoly toku NSG do účtu Azure Storage pro generování záznamů toku. Pokud se to vyžaduje pro prošetření aktivity neobvyklé, povolte zachytávání paketů služby Azure Network Watcher.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: vyberte nabídku z Azure Marketplace, která podporuje funkce ID/IP adresy s možnostmi kontroly zatížení. Pokud zjišťování neoprávněných vniknutí nebo prevence na základě kontroly zatížení není požadavkem, je možné použít Azure Firewall s analýzou hrozeb. Azure Firewall filtrování na základě logiky hrozeb může upozorňovat a zamítnout provoz do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu.

Nasaďte řešení brány firewall podle svého výběru na základě hranic sítě vaší organizace za účelem detekce nebo odepření škodlivého provozu.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Postup konfigurace výstrah pomocí Azure Firewall](../firewall/threat-intel.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků, které potřebují přístup ke svému registru kontejneru, použijte značky služby virtuální sítě pro službu Azure Container Registry k definování řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby AzureContainerRegistry v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo zamítnout přenos pro odpovídající službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Povolení přístupu podle značky služby](https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené ke službě Azure Container registry pomocí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. ContainerRegistry a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě pro Registry kontejnerů. 

Plány Azure můžete použít ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení a zásady Azure RBAC v rámci jedné definice podrobného plánu. Pomocí správy verzí můžete snadno použít podrobný plán na nová předplatná a vyladit řízení a správu.

- [Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy](container-registry-azure-policy.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: zákazník může pomocí plánů Azure zjednodušit rozsáhlá nasazení Azure tím, že zabalí artefakty prostředí klíčů, jako jsou například šablony Azure Resource Manager, řízení a zásady služby Azure RBAC v jediné definici podrobného plánu. Pomocí správy verzí můžete snadno použít podrobný plán na nová předplatná a vyladit řízení a správu.

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které se vztahují k registrům kontejnerů. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

- [Vytváření výstrah v Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení generovaných službou Azure Container Registry. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé nebo archivní úložiště.

- [Protokoly Azure Container Registry pro vyhodnocení a auditování diagnostiky](container-registry-diagnostics-audit-logs.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: Azure monitor shromažďuje protokoly prostředků (dříve nazývané diagnostické protokoly) pro události řízené uživateli v registru. Shromažďování a používání těchto dat pro audit událostí ověřování v registru a poskytování úplných informací o aktivitách v artefaktech registru, jako jsou například události Pull a push, abyste mohli diagnostikovat problémy se zabezpečením v registru.

- [Protokoly Azure Container Registry pro vyhodnocení a auditování diagnostiky](container-registry-diagnostics-audit-logs.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nelze použít. Srovnávací test je určený pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

- [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů Azure Container registry pro chování neobvyklé a pravidelné kontroly výsledků. Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu.

- [Protokoly Azure Container Registry pro vyhodnocení a auditování diagnostiky](container-registry-diagnostics-audit-logs.md)

- [Principy Log Analyticsho pracovního prostoru](/azure/azure-monitor/log-query/get-started-portal)

- [Jak provádět vlastní dotazy v Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Pokyny**: pracovní prostor Azure Log Analytics slouží k monitorování a upozorňování na aktivity neobvyklé v protokolech zabezpečení a událostech souvisejících se službou Azure Container Registry.

- [Protokoly Azure Container Registry pro vyhodnocení a auditování diagnostiky](container-registry-diagnostics-audit-logs.md)

- [Upozornění na data protokolu Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: nelze použít. Azure Container Registry nezpracovává ani nevytváří protokoly související s malwarem.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: nelze použít. Azure Container Registry je koncový bod a neinicializuje komunikaci a služba se nedotazuje na DNS.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: nelze použít. Srovnávací test je určený pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

Pro každý registr kontejnerů Azure můžete sledovat, jestli je integrovaný účet správce povolený nebo zakázaný. Zakáže účet, pokud se nepoužívá.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Účet správce Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: Azure Active Directory (Azure AD) nemá koncept výchozích hesel. Další prostředky Azure, které vyžadují heslo, vynutí vytvoření hesla s požadavky na složitost a minimální délkou hesla, která se liší v závislosti na službě. Zodpovídáte za aplikace třetích stran a služby Marketplace, které mohou používat výchozí hesla.

Pokud je výchozí účet správce služby Azure Container Registry povolený, vytvoří se automaticky složitá hesla, která by se měla střídat. Zakáže účet, pokud se nepoužívá.

- [Účet správce Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Vytvořte také procedury pro povolení předdefinovaného účtu správce registru kontejneru. Zakáže účet, pokud se nepoužívá.

- [Pochopení Azure Security Center identity a přístupu](../security-center/security-center-identity-access.md)

- [Účet správce Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné** materiály: kdykoli je to možné, použijte službu Azure Active Directory (Azure AD) SSO místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte Azure Security Center doporučení pro správu identit a přístupu.

Pro individuální přístup k registru kontejneru použijte individuální přihlašovací údaje integrované s Azure AD.

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Individuální přihlášení ke registru kontejneru](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné** materiály: Používejte privilegovaným přístupem (Privileged Access Workstations) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení k prostředkům Azure a jejich konfiguraci.

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití sestav zabezpečení Azure Active Directory (Azure AD) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: máte přístup k aktivitě pro přihlášení k Azure Active Directory (Azure AD), událostem auditu a rizikovým protokolům událostí, které vám umožní integrovat se všemi nástroji/Monitoring (Security Information and Event Management) (Siem).

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: použití funkcí Azure Active Directory (Azure AD) pro rizika a ochranu identity ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. 

- [Zobrazení rizikových přihlášení Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: není k dispozici; Customer Lockbox aktuálně není pro Azure Container Registry podporovaná.

- [Seznam podporovaných služeb Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: použití značek prostředků k usnadnění sledování registrů Azure Containers, které ukládají nebo zpracovávají citlivé informace.

Značky a image kontejneru verze nebo jiné artefakty v registru a zámky imagí nebo úložišť, které vám pomůžou při sledování imagí, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

- [Doporučení pro označování a naznačení verzí imagí kontejneru](container-registry-image-tag-version.md)

- [Uzamknutí image kontejneru ve službě Azure Container Registry](container-registry-image-lock.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných registrů kontejnerů, předplatných nebo skupin pro správu pro vývoj, testování a produkci. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být dostatečně izolované.

Prostředky by měly být oddělené virtuální sítí nebo podsítí, vhodně označené a zabezpečené skupinou zabezpečení sítě (NSG) nebo Azure Firewall.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Vytvoření skupin pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

- [Omezení přístupu ke službě Azure Container Registry pomocí virtuální sítě Azure nebo pravidel brány firewall](container-registry-vnet.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak nakonfigurovat výstrahu nebo upozornění a odepřít pomocí Azure Firewall](../firewall/threat-intel.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: nasaďte automatizovaný nástroj na hraničních sítích, které monitorují neoprávněný přenos citlivých informací a zablokují tyto přenosy, a upozorní odborníky na zabezpečení informací.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: Ujistěte se, že všichni klienti, kteří se připojují k vašemu Azure Container Registry, můžou vyjednávat TLS 1,2 nebo vyšší. Microsoft Azure prostředky vyjednávat TLS 1,2 ve výchozím nastavení.

Pokud je to možné, postupujte podle Azure Security Center doporučení pro šifrování v klidovém režimu a šifrování.

- [Pochopení šifrování při přenosu pomocí Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Container Registry. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: použití řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k datům a prostředkům v registru kontejnerů Azure. 

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Azure Container Registry role a oprávnění](container-registry-roles.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: Pokud je to nutné pro dodržování předpisů na výpočetních prostředcích, implementujte nástroj třetí strany, jako je například automatizované řešení pro ochranu před únikem informací na hostiteli, které vynutilo řízení přístupu k datům i v případě, že se data zkopírují mimo systém.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: používejte pro všechny prostředky Azure šifrování v klidovém provozu. Ve výchozím nastavení jsou všechna data ve službě Azure Container Registry v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem.

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md)

- [Klíče spravované zákazníkem v Azure Container Registry](https://aka.ms/acr/cmk)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: Azure monitor shromažďuje protokoly prostředků (dříve nazývané diagnostické protokoly) pro události řízené uživateli v registru. Shromažďování a používání těchto dat pro audit událostí ověřování v registru a poskytování úplných informací o aktivitách v artefaktech registru, jako jsou události Pull a pull, abyste mohli diagnostikovat provozní problémy s registrem.

- [Protokoly Azure Container Registry pro vyhodnocení a auditování diagnostiky](container-registry-diagnostics-audit-logs.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: dodržujte doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení u imagí kontejneru. Volitelně můžete nasadit řešení jiných výrobců z Azure Marketplace k provádění posouzení ohrožení zabezpečení imagí.

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Integrace služby Azure Container Registry se službou Security Center (Preview)](/azure/security-center/azure-container-registry-integration)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: Společnost Microsoft provádí správu oprav v základních systémech, které podporují Azure Container Registry.

Automatizuje aktualizace imagí kontejneru, když se zjistí aktualizace základních imagí z operačního systému a dalších oprav.

- [Základní aktualizace obrázků pro úlohy Azure Container Registry](container-registry-tasks-base-images.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizovaného řešení pro správu oprav pro softwarové tituly třetích stran

**Pokyny**: k opravě imagí aplikace můžete použít řešení třetích stran.  Můžete také spouštět úlohy Azure Container Registry pro automatizaci aktualizací imagí aplikace v registru kontejnerů na základě oprav zabezpečení nebo jiných aktualizací v základních bitových kopiích.

- [Základní aktualizace obrázků pro úlohy ACR](container-registry-tasks-base-images.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: integrujte Azure Container Registry (ACR) s Azure Security Center a umožněte tak pravidelné prohledávání imagí kontejneru pro ohrožení zabezpečení. Volitelně můžete nasadit řešení jiných výrobců z Azure Marketplace a provádět pravidelné kontroly ohrožení zabezpečení imagí.

- [Integrace služby Azure Container Registry se službou Security Center (Preview)](/azure/security-center/azure-container-registry-integration)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: integrujte Azure Container Registry (ACR) s Azure Security Center, abyste umožnili pravidelné prohledávání imagí kontejneru pro ohrožení zabezpečení a klasifikaci rizik. Volitelně můžete nasadit řešení třetích stran z Azure Marketplace a provádět pravidelné kontroly ohrožení zabezpečení imagí a hodnocení rizik.

- [Integrace služby Azure Container Registry se službou Security Center (Preview)](/azure/security-center/azure-container-registry-integration)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: Azure Container Registry uchovává metadata včetně značek a manifestů pro image v registru. Použijte doporučené postupy pro označování artefaktů.

- [O registrech, úložištích a obrázcích](container-registry-concepts.md)

- [Doporučení pro označování a naznačení verzí imagí kontejneru](container-registry-image-tag-version.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Azure Container Registry uchovává metadata včetně značek a manifestů pro image v registru. Použijte doporučené postupy pro označování artefaktů.

- [O registrech, úložištích a obrázcích](container-registry-concepts.md)

- [Doporučení pro označování a naznačení verzí imagí kontejneru](container-registry-image-tag-version.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: budete muset vytvořit inventarizaci schválených prostředků Azure podle potřeb vaší organizace.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit ve vašich předplatných.

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy](container-registry-azure-policy.md)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Pokyny**: analýza a sledování protokolů Azure Container registry pro chování neobvyklé a pravidelné kontroly výsledků. Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu.

- [Protokoly Azure Container Registry pro vyhodnocení a auditování diagnostiky](container-registry-diagnostics-audit-logs.md)

- [Principy Log Analyticsho pracovního prostoru](/azure/azure-monitor/log-query/get-started-portal)

- [Jak provádět vlastní dotazy v Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: Azure Automation poskytuje úplnou kontrolu během nasazení, operací a vyřazení úloh a prostředků z provozu. Můžete implementovat vlastní řešení pro odebrání neautorizovaných prostředků Azure.

- [Seznámení se službou Azure Automation](../automation/automation-intro.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: nelze použít. Srovnávací test je navržený pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: využijte Azure Policy k omezení služeb, které můžete zřídit ve vašem prostředí.

- [Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy](container-registry-azure-policy.md)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: nelze použít. Srovnávací test je navržený pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí konfigurací specifických pro operační systém nebo prostředků třetích stran omezte schopnost uživatelů spouštět skripty v rámci výpočetních prostředků Azure.

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Pokyny**: pomocí konfigurací specifických pro operační systém nebo prostředků třetích stran omezte schopnost uživatelů spouštět skripty v rámci výpočetních prostředků Azure.

- [Například způsob řízení spouštění skriptu prostředí PowerShell v prostředích systému Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: software, který je potřeba pro obchodní operace, ale může pro organizaci zvýšit riziko, že by se měl izolovat v rámci vlastního virtuálního počítače nebo virtuální sítě a dostatečně zabezpečený pomocí Azure firewall nebo skupiny zabezpečení sítě.

- [Jak vytvořit virtuální síť](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: k údržbě konfigurací zabezpečení pro všechny prostředky Azure použijte Azure Policy nebo Azure Security Center.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy](container-registry-azure-policy.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: využijte Azure Security Center doporučení "náprava ohrožení zabezpečení v konfiguracích zabezpečení v Virtual Machines" pro udržování konfigurací zabezpečení ve všech výpočetních prostředcích.

- [Jak monitorovat Azure Security Center doporučení](../security-center/security-center-recommendations.md)

- [Jak opravit Azure Security Center doporučení](../security-center/security-center-remediate-recommendations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

- [Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy](container-registry-azure-policy.md)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nelze použít. Srovnávací test je určený pro výpočetní prostředky.

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu Azure Repos.

- [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentace k Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nelze použít. Srovnávací testy platí pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: použijte Azure Policy k upozornění, auditu a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy](container-registry-azure-policy.md)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné** materiály: nelze použít. Srovnávací testy platí pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: použijte Azure Security Center k provádění kontrol standardních hodnot vašich prostředků Azure.

Pomocí Azure Policy můžete umístit omezení typu prostředků, které se dají vytvořit v předplatných.

- [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy](container-registry-azure-policy.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nelze použít. Srovnávací testy platí pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace.

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Vytvoření Key Vault](../key-vault/general/quick-create-portal.md)

- [Ověření Key Vault](../key-vault/general/authentication.md)

- [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

- [Použití identity spravované v Azure v úlohách Azure Container Registry](container-registry-tasks-authentication-managed-identity.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: použití spravovaných identit k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Postup konfigurace spravovaných identit](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)

- [Použití spravované identity k ověření ve službě Azure Container Registry](container-registry-authentication-managed-identity.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použijte centrálně spravovaný antimalwarový software

**Doprovodné** materiály: použití antimalwaru Microsoftu pro Azure Cloud Services a Virtual Machines k nepřetržitému monitorování a obraně prostředků. Pro Linux použijte antimalwarové řešení třetích stran.

- [Jak nakonfigurovat Microsoft Antimalware pro Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Container Registry), ale neběží na zákaznickém obsahu.

Předem Prohledejte všechny soubory nahrané do nevýpočetních prostředků Azure, například App Service, Data Lake Storage, Blob Storage atd.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nelze použít. Srovnávací test je určený pro výpočetní prostředky. Microsoft zpracovává anti-malware pro základní platformu.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: data v registru kontejnerů Microsoft Azure se vždy automaticky replikují, aby se zajistila odolnost a vysoká dostupnost. Azure Container Registry kopíruje vaše data, aby byla chráněná před plánovanými a neplánovanými událostmi.

Volitelně můžete geograficky replikovat registr kontejnerů, aby se zachovaly repliky registru v několika oblastech Azure. 

- [Geografická replikace v Azure Container Registry](container-registry-geo-replication.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: Volitelně můžete zálohovat image kontejnerů importem z jednoho registru do jiného.

Zálohujte klíče spravované zákazníkem v Azure Key Vault pomocí nástrojů příkazového řádku Azure nebo sad SDK.

- [Import imagí kontejneru do registru kontejneru](container-registry-import-images.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny**: testování obnovení zálohovaných klíčů spravovaných zákazníkem v Azure Key Vault pomocí nástrojů příkazového řádku Azure nebo sad SDK.

- [Postup obnovení klíčů Azure Key Vault v Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: můžete povolit Soft-Delete v Azure Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

- [Postup povolení Soft-Delete v Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

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

**Doprovodné** materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

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

- [Jak nastavit kontakt zabezpečení pro Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

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

**Doprovodné** materiály: Využijte pravidla zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu:  https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [V této části najdete další informace o strategii Microsoftu a provádění testování v rámci červeného seskupování a testování průniku na cloudové infrastruktuře, služby a aplikace spravované Microsoftem.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
