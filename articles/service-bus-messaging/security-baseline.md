---
title: Základní hodnoty zabezpečení Azure pro Service Bus
description: Základní Service Bus zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: service-bus-messaging
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d98a794aaa2b91e1d095bcbd0301369f35de6c78
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91373099"
---
# <a name="azure-security-baseline-for-service-bus"></a>Základní hodnoty zabezpečení Azure pro Service Bus

Základní plán zabezpečení Azure pro Service Bus obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení. Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů. Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí 

**Doprovodné**materiály: integrace Service Bus se službou privátního propojení Azure umožňuje zabezpečený privátní přístup k funkcím zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou svázané s virtuálními sítěmi. Vytvořte připojení privátního koncového bodu ke svému oboru názvů Service Bus. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím tohoto privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy.

Obor názvů Azure Service Bus můžete zabezpečit také pomocí bran firewall. Azure Service Bus podporuje řízení přístupu na základě IP adres pro podporu příchozích bran firewall. Pravidla brány firewall můžete nastavit pomocí Azure Portal, Azure Resource Manager šablon nebo prostřednictvím rozhraní příkazového řádku Azure CLI nebo Azure PowerShell.

- [Povolení přístupu k Azure Service Bus obory názvů prostřednictvím privátních koncových bodů](private-link-service.md)

- [Povolení přístupu k oboru názvů Azure Service Bus z konkrétních IP adres nebo rozsahů](service-bus-ip-filtering.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Pokyny**: Pokud používáte virtuální počítače Azure pro přístup k Service Bus entit, povolte protokoly toku NSG (Network Security Group) a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě. 

Použijte Azure Security Center a dodržujte doporučení k ochraně sítě, které vám pomůžou zabezpečit prostředky Service Bus v Azure.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

- [Principy zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení DDoS Protection Standard ve virtuálních sítích přidružených k vašim oborům názvů Service Bus pro ochranu před distribuovanými útoky na útoky DDoS (Denial of Service). K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

- [Jak nakonfigurovat DDoS Protection](../virtual-network/manage-ddos-protection.md)

- [Azure Security Center Intelligence Integrated Threat Intelligence](/azure/security-center/security-center-alerts-service-layer)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: Pokud k přístupu k Service Bus entit používáte virtuální počítače Azure, můžete pomocí Network Watcher zachytávání paketů prozkoumat aktivity neobvyklé.

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: Pokud k přístupu k entitám Service Bus používáte virtuální počítače Azure, vyberte nabídku z Azure Marketplace, která podporuje funkce ID/IP adresy s možnostmi kontroly zatížení. Pokud se zjišťování vniknutí nebo prevence na základě kontroly datové části pro vaši organizaci nevyžaduje, můžete použít integrovanou funkci brány firewall Azure Service Bus. Přístup k oboru názvů Service Bus můžete omezit na omezený rozsah IP adres nebo na konkrétní IP adresu pomocí pravidel brány firewall.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Postup přidání pravidla brány firewall v Service Bus obory názvů pro zadanou IP adresu](service-bus-ip-filtering.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí Virtual Networkch značek služeb můžete definovat řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall, která filtrují provoz do a z Service Bus prostředků. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ServiceBus) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny. 

- [Pochopení a použití značek služeb](../virtual-network/service-tags-overview.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené k vašim oborům názvů Azure Service Bus pomocí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. ServiceBus a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich Service Bus oborů názvů. Můžete také využít integrované definice zásad související s Azure Service Bus, například:

- Service Bus by měl používat koncový bod služby virtuální sítě
- Měly by být povolené diagnostické protokoly v Service Bus.

Pokud předdefinované definice nevyhovují potřebám vaší organizace, můžete také vytvořit vlastní definice zásad.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Integrovaná zásada Azure pro obor názvů Service Bus](/azure/service-bus-messaging/policy-samples#azure-service-bus-messaging)

- [Ukázky Azure Policy pro sítě](../governance/policy/samples/built-in-policies.md#network)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: používejte značky pro virtuální sítě a další prostředky, které se týkají zabezpečení sítě a toku provozu, které jsou přidruženy k vašemu Service Bus obory názvů. Pro jednotlivá pravidla skupiny zabezpečení sítě použijte pole Popis k zadání obchodních potřeb, doby trvání a dalších popisných informací pro všechna pravidla, která umožňují provoz do nebo ze sítě přidružené k vašim Service Busm oborům názvů. 

Použijte některou z vestavěných definic zásad Azure souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že se vytvoří všechny prostředky s značkami a upozorní vás na stávající neoznačené prostředky. 

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek. 

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags) 

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md) 

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s Azure Service Bus. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení generovaných prostředky Service Bus. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz, ke konfiguraci Azure Storage účtů pro dlouhodobé nebo archivní úložiště. Můžete také nakonfigurovat protokoly týkající se Service Bus, které se mají odeslat do Azure Sentinel, nebo do SIEM třetí strany.

- [Postup konfigurace nastavení diagnostiky pro Azure Service Bus](service-bus-diagnostic-logs.md)

- [Principy protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné**materiály: Povolte nastavení diagnostiky pro váš obor názvů Azure Service Bus. Azure Service Bus aktuálně podporuje protokoly aktivit a provozu a diagnostické protokoly. Protokoly aktivit obsahují informace o operacích provedených v rámci úlohy. Diagnostické protokoly poskytují bohatší informace o operacích a akcích, které se provádí v oboru názvů pomocí rozhraní API nebo prostřednictvím klientů pro správu pomocí jazykové sady SDK. Konkrétně tyto protokoly zachytí typ operace, včetně vytvoření fronty, použitých prostředků a stavu operace.

- [Postup povolení nastavení diagnostiky pro Azure Service Bus](service-bus-diagnostic-logs.md)

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné**materiály: v rámci Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace za účelem zachycení a kontroly incidentů souvisejících s Service Bus.

- [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné**materiály: analýza a sledování protokolů pro neobvyklé chování a pravidelné kontroly výsledků souvisejících s vašimi entitami Service Bus. Pomocí Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolů související s Service Bus.

- [Další informace o pracovním prostoru Log Analytics](../azure-monitor/log-query/get-started-portal.md)

- [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné**materiály: použití Azure Security Center s Log Analytics pracovním prostorem pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech. Alternativně můžete také povolit a začlenit data do Azure Sentinel.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: řízení přístupu na základě role (RBAC) v Azure umožňuje spravovat přístup k prostředkům Azure prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Existují předdefinované předdefinované role pro Service Bus, tyto role mohou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI, Azure PowerShell nebo Azure Portal.

- [Předdefinované role pro Azure Service Bus](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: přístup roviny ovládacího prvku k Service Bus je řízen prostřednictvím Azure Active Directory (Azure AD). Azure AD nemá koncept výchozích hesel.

Přístup k rovině dat pro Service Bus se řídí přes Azure AD pomocí spravovaných identit, Registrace aplikací nebo podpisů sdíleného přístupu. Sdílené přístupové podpisy používají klienti připojující se k vašemu Service Busmu oboru názvů a je možné je kdykoli znovu vygenerovat.

- [Vysvětlení sdílených přístupových podpisů pro Service Bus](service-bus-sas.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné**materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrovaných zásad Azure, jako je například:

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

Pokud předdefinované definice nevyhovují potřebám vaší organizace, můžete také vytvořit vlastní definice zásad.

- [Použití Azure Security Center k monitorování identity a přístupu](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné**materiály: Microsoft Azure poskytuje integrované řízení přístupu pro prostředky a aplikace založené na Azure Active Directory (Azure AD). Klíčovou výhodou použití Azure AD s Azure Service Bus je, že už nemusíte ukládat přihlašovací údaje do kódu. Místo toho můžete požádat o přístupový token OAuth 2,0 z platformy Microsoft Identity Platform. Název prostředku pro vyžádání tokenu https://servicebus.azure.net/ . Azure AD ověřuje objekt zabezpečení (uživatel, skupina nebo instanční objekt), který spouští aplikaci. Pokud je ověření úspěšné, služba Azure AD vrátí přístupový token do aplikace a aplikace pak může pomocí přístupového tokenu autorizovat požadavek na Azure Service Bus prostředků.

- [Jak ověřit aplikaci s využitím služby Azure AD pro přístup k prostředkům Service Bus](authenticate-application.md)

- [Princip jednotného přihlašování s Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné**materiály: povolte Azure Active Directory Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu, která vám pomůžou chránit vaše prostředky s povoleným Service Bus.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Pokyny**: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným Multi-Factor Authentication (MFA), které jsou nakonfigurovány pro přihlášení a konfiguraci prostředků s podporou Service Bus.

- [Další informace o pracovních stanicích s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné**materiály: použití sestav a monitorování zabezpečení Azure Active Directory k detekci, kdy dojde k podezřelé nebo nebezpečné aktivitě v prostředí. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

- [Jak identifikovat uživatele Azure AD označené příznakem rizika pro rizikové aktivity](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Jak monitorovat identitu uživatelů a aktivity přístupu v Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění Azure AD povolte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí. 

- [Jak nakonfigurovat pojmenovaná umístění služby Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné**materiály: použití Azure Active Directory (AD) jako centrálního ověřování a autorizačního systému pro prostředky Azure, jako je například Service Bus. To umožňuje řízení přístupu na základě role v Azure (Azure RBAC) pro správu citlivých prostředků.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Autorizace přístupu k prostředkům Service Bus pomocí Azure Active Directory](authenticate-application.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

V dalším případě pravidelně otáčejte sdílený přístupový podpis oboru názvů Service Bus.

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

- [Principy podpisů sdíleného přístupu pro obor názvů Service Bus](service-bus-sas.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné**materiály: máte přístup k aktivitě pro přihlášení k Azure Active Directory (Azure AD), událostem auditu a rizikovým protokolům událostí, které vám umožní integrovat se službou Azure Sentinel nebo nástrojem Siem třetí strany.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Pak v Azure Monitor můžete nakonfigurovat požadované výstrahy protokolu pro určité akce, ke kterým dojde v protokolech.

- [Jak integrovat protokoly aktivit Azure do Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Autorizace přístupu k prostředkům Service Bus pomocí Azure Active Directory](authenticate-application.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: pomocí funkcí Azure Active Directory ochrany identit a detekce rizik můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce související s vašimi prostředky s podporou Service Bus. Pomocí služby Azure Sentinel byste měli povolit automatizované odezvy pro implementaci reakcí zabezpečení vaší organizace.

- [Jak zobrazit rizikové přihlašování Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné**materiály: aktuálně není k dispozici; Customer Lockbox ještě není pro Service Bus podporovaná.

- [Seznam služeb podporovaných Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: pomocí značek u prostředků souvisejících s vaším Service Bus pomáhat při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Doprovodné**materiály: implementace samostatných předplatných a skupin pro správu pro vývoj, testování a produkci. Obory názvů Service Bus by měly být oddělené virtuálními sítěmi s nakonfigurovanými privátními koncovými body a vhodně označené.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Jak vytvářet a používat značky](/azure/azure-resource-manager/resource-group-using-tags)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny**: při použití virtuálních počítačů pro přístup k entitám Service Bus použijte virtuální sítě, soukromé koncové body, Service Bus bránu firewall, skupiny zabezpečení sítě a značky služeb, abyste zmírnili možnost exfiltraceí dat.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Service Bus a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Konfigurace pravidel brány firewall protokolu IP pro Azure Service Bus obory názvů](service-bus-ip-filtering.md)

- [Povolení přístupu k oboru názvů Azure Service Bus z konkrétních virtuálních sítí](private-link-service.md)

- [Povolení přístupu k Azure Service Bus obory názvů prostřednictvím privátních koncových bodů](private-link-service.md)

- [Pochopení skupin zabezpečení sítě a značek služeb](/azure/virtual-network/security-overview)

- [Pochopení ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Azure Service Bus ve výchozím nastavení vynutila komunikaci zašifrovaná pomocí protokolu TLS. V tuto chvíli jsou podporované TLS verze 1,0, 1,1 a 1,2. TLS 1,0 a 1,1 ale mají cestu k vyřazení v celém oboru, takže použijte protokol TLS 1,2 nebo novější, pokud je to možné.

- [Vysvětlení funkcí zabezpečení Service Bus najdete v tématu zabezpečení sítě.](network-security.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné**materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Service Bus. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Pochopení ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: Azure Service Bus podporuje použití Azure Active Directory (Azure AD) k autorizaci požadavků na Service Bus entit. Pomocí Azure AD můžete použít řízení přístupu na základě role Azure (Azure RBAC) k udělení oprávnění objektu zabezpečení, který může být uživatel nebo instančního objektu aplikace.

- [Informace o RBAC a dostupných rolích Azure pro Azure Service Bus](authenticate-application.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné**materiály: Azure Service Bus podporuje možnost šifrování neaktivních dat buď pomocí klíčů spravovaných Microsoftem nebo klíčů spravovaných zákazníkem. Tato funkce umožňuje vytvořit, otočit, zakázat a odvolat přístup k klíčům spravovaným zákazníkem, které se používají k šifrování Azure Service Bus neaktivní data.

- [Postup konfigurace klíčů spravovaných zákazníkem pro šifrování Azure Service Bus](configure-customer-managed-key.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné**materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Azure Service Bus a dalších důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky (včetně Azure Service Bus oborů názvů) v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Azure Service Bus oborů názvů a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné**materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Kromě toho použijte Azure Resource Graph k dotazování a zjišťování prostředků v rámci předplatných.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

Pokud předdefinované definice nevyhovují potřebám vaší organizace, můžete také vytvořit vlastní definice zásad.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Service Bus nasazení. Můžete také využít integrované definice zásad pro Azure Service Bus, jako jsou:

- Měly by být povolené diagnostické protokoly v Service Bus.
- Service Bus by měl použít koncový bod služby virtuální sítě pro omezení síťového provozu do vašich privátních sítí.

Pomocí aliasů Azure Policy v oboru názvů Microsoft. ServiceBus můžete vytvořit vlastní zásady pro auditování nebo vymáhání konfigurací.

- [Předdefinované zásady Azure pro Service Bus ](/azure/service-bus-messaging/policy-samples)

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné**materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč vašimi prostředky nebo aplikacemi s podporou Service Bus.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Další informace o Azure Policych efektech](../governance/policy/concepts/effects.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. ServiceBus můžete vytvořit vlastní zásady pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. ServiceBus můžete vytvořit vlastní zásady pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro nasazení Azure Service Bus a související prostředky použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje].

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné**materiály: u virtuálních počítačů Azure nebo webových aplikací, které běží na Azure App Service slouží pro přístup k vašim Service BUSM entitám, použijte identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy sdíleného přístupového podpisu pro vaše Azure Service Bus nasazení. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

- [Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Service Bus](service-bus-managed-service-identity.md)

- [Konfigurace klíčů spravovaných zákazníkem pro Service Bus](configure-customer-managed-key.md)

- [Vytvoření Key Vault](/azure/key-vault/quick-create-portal)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné**materiály: u virtuálních počítačů Azure nebo webových aplikací běžících na Azure App Service se používá pro přístup k vašim Service BUSM entitám, použití identita spravované služby ve spojení s Azure Key Vault pro zjednodušení a zabezpečení Azure Service Bus. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Azure Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Service Bus](service-bus-managed-service-identity.md)

- [Konfigurace klíčů spravovaných zákazníkem pro Service Bus](configure-customer-managed-key.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: předběžná kontrola veškerého obsahu, který se nahrává do nevýpočetních prostředků Azure, jako jsou Azure Service Bus, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL atd. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure, ale neběží na zákaznickém obsahu.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: Konfigurace geografického zotavení po havárii pro Azure Service Bus. Při výpadku provozu celé oblasti Azure nebo Datacenter (Pokud se nepoužívají žádné zóny dostupnosti) je důležité, aby zpracování dat pokračovalo v práci v jiné oblasti nebo datacentru. V takovém případě geografické zotavení po havárii a geografická replikace jsou důležité funkce pro všechny podniky. Azure Service Bus podporuje jak geografické zotavení po havárii, tak i geografickou replikaci na úrovni oboru názvů.

- [Vysvětlení geografického zotavení po havárii pro Azure Service Bus](service-bus-geo-dr.md)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné**materiály: Azure Service Bus poskytuje šifrování neaktivních dat pomocí šifrování služby Azure Storage (Azure SSE). Service Bus spoléhá na Azure Storage uložení dat a ve výchozím nastavení se všechna data uložená pomocí Azure Storage šifrují pomocí klíčů spravovaných Microsoftem. Pokud používáte Azure Key Vault k ukládání klíčů spravovaných zákazníkem, zajistěte pravidelné automatizované zálohování vašich klíčů.

Pomocí následujícího příkazu PowerShellu Zajistěte pravidelné automatizované zálohování Key Vault tajných kódů: Backup-AzKeyVaultSecret

- [Postup konfigurace klíčů spravovaných zákazníkem pro šifrování Azure Service Busch dat v klidovém umístění](configure-customer-managed-key.md)

- [Postup zálohování Key Vault tajných kódů](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny**: test obnovení zálohovaných klíčů spravovaných zákazníkem, který slouží k šifrování Service Bus dat.

- [Postup konfigurace klíčů spravovaných zákazníkem pro šifrování Azure Service Busch dat v klidovém umístění](configure-customer-managed-key.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: povolení obnovitelného odstranění v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění. Azure Service Bus vyžaduje, aby klíče spravované zákazníkem byly obnovitelné odstranění a nakonfigurovány.

- [Jak povolit obnovitelné odstranění v Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Nastavení trezoru klíčů s klíči](../event-hubs/configure-customer-managed-key.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: vývoj Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování incidentů a správu, které se mají zjišťovat, na kontrolu po jednotlivých událostech. 

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Pomoc při tvorbě vlastního plánu odpovědí na incidenty najdete v příručce pro zpracování incidentů v počítači s NIST.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete označit odběry pomocí značek a vytvořit systém pojmenování k identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data. Je vaše zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu. 

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy. 

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel. 

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md) 

- [Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: 

Použijte funkci automatizace pracovních postupů Azure Security Center k automatickému spouštění odpovědí na výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure. 

- [Jak nakonfigurovat automatizaci pracovního postupu v Security Center](../security-center/workflow-automation.md)

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
