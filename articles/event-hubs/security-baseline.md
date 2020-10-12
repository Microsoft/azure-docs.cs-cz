---
title: Základní hodnoty zabezpečení Azure pro Event Hubs
description: Základní hodnoty zabezpečení Azure pro Event Hubs
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f47e9cb93b90c8a2401d1dfe6c0ff7800b0bcf27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401125"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Základní hodnoty zabezpečení Azure pro Event Hubs

Základní plán zabezpečení Azure pro Event Hubs obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Pokyny**: integrace centra událostí s koncovými body služby virtuální sítě umožňuje zabezpečený přístup k funkcím zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou svázané s virtuálními sítěmi, a cestu síťového provozu, která je zabezpečená na obou koncích.

Po vytvoření vazby na alespoň jeden koncový bod služby virtuální sítě musí příslušný obor názvů Event Hubs nadále přijímat provoz z libovolného místa, ale z nich budou ověřeny podsítě ve virtuálních sítích. Z perspektivy virtuální sítě vytvoří služba Event Hubs obor názvů na koncový bod služby konfiguraci izolovaného síťového tunelu z podsítě virtuální sítě do služby zasílání zpráv. 

Můžete také vytvořit soukromý koncový bod, což je síťové rozhraní, které se připojí soukromě a bezpečně ke službě Azure Event Hubs Service pomocí služby privátního propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. 

Obor názvů Azure Event Hubs můžete zabezpečit také pomocí bran firewall. Azure Event Hubs podporuje řízení přístupu na základě IP adres pro podporu příchozích bran firewall. Pravidla brány firewall můžete nastavit pomocí Azure Portal, Azure Resource Manager šablon nebo prostřednictvím rozhraní příkazového řádku Azure CLI nebo Azure PowerShell.

Použití koncových bodů služby virtuální sítě s Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Další informace najdete v tématu Integrace služby Azure Event Hubs s privátním odkazem Azure: https://docs.microsoft.com/azure/event-hubs/private-link-service .

Povolit integraci virtuálních sítí a brány firewall na Event Hubs oboru názvů: https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Jak nakonfigurovat pravidla brány firewall protokolu IP pro obory názvů Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Doprovodné**materiály: použijte Azure Security Center a sledujte doporučení pro ochranu sítě, která vám pomůžou zabezpečit prostředky Event Hubs v Azure. Pokud používáte virtuální počítače Azure pro přístup k centrům událostí, povolte protokoly toku NSG (Network Security Group) a odešlete protokoly do účtu úložiště pro audit provozu.

Jak povolit protokoly toku NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Principy zabezpečení sítě, které poskytuje Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení DDoS Protection Standard ve virtuálních sítích přidružených k centrům událostí pro ochranu před distribuovanými útoky s cílem odepření služeb (DDoS). K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

Jak nakonfigurovat DDoS Protection: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../virtual-network/manage-ddos-protection.md)

Další informace o Azure Security Center integrované analýze hrozeb: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Pokyny**: Pokud používáte virtuální počítače Azure pro přístup k centrům událostí, povolte protokoly toku NSG (Network Security Group) a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Pokud je to potřeba pro prošetření aktivity neobvyklé, povolte zachytávání paketů Network Watcher.

Jak povolit protokoly toku NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Jak povolit a použít Analýza provozu: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Postup povolení Network Watcher: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: Pokud k přístupu k centrům událostí používáte virtuální počítače Azure, vyberte nabídku z Azure Marketplace, která podporuje funkce ID/IP adresy s možnostmi kontroly zatížení. Pokud se zjišťování vniknutí nebo prevence na základě kontroly datové části pro vaši organizaci nevyžaduje, můžete použít integrovanou funkci brány firewall Azure Event Hubs. Přístup k oboru názvů Event Hubs můžete omezit na omezený rozsah IP adres nebo na konkrétní IP adresu pomocí pravidel brány firewall.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Postup přidání pravidla brány firewall v Event Hubs pro zadanou IP adresu:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Monitorování Azure Security Center**: zatím není k dispozici

**Zodpovědnost**: zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné**materiály: Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené k vašim oborům názvů Azure Event Hubs pomocí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. EventHub a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich Event Hubs oborů názvů. Můžete také využít integrované definice zásad související s Azure Event Hubs, například:

- Centrum událostí by mělo používat koncový bod služby virtuální sítě.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Integrovaná zásada Azure pro obor názvů Event Hubs:  https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Azure Policy ukázky pro síťové služby: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Postup vytvoření Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: používejte značky pro virtuální sítě a další prostředky, které se týkají zabezpečení sítě a toku provozu, které jsou přidruženy k centrům událostí.

Vytváření a používání značek: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s Azure Event Hubs. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

Jak zobrazit a načíst události protokolu aktivit Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Postup při vytváření výstrah v Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Doprovodné**materiály: nepoužitelné; Microsoft udržuje čas používaný pro prostředky Azure, jako je například Azure Event Hubs, pro časová razítka v protokolech.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné**materiály: v rámci Azure monitor Nakonfigurujte protokoly související s centry událostí v nastaveních diagnostiky protokolů aktivit a centra událostí, aby se protokoly odesílaly do pracovního prostoru Log Analytics, který se má dotazovat nebo do účtu úložiště pro dlouhodobé archivace úložiště.

Postup konfigurace nastavení diagnostiky pro Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Seznámení s protokolem aktivit Azure: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné**materiály: povolení nastavení diagnostiky pro obor názvů Azure Event Hubs. Existují tři kategorie nastavení diagnostiky pro Azure Event Hubs: archivní protokoly, provozní protokoly a protokoly automatického škálování. Povolte provozní protokoly pro zaznamenávání informací o tom, co se děje během operací Event Hubs, konkrétně typ operace, včetně vytvoření centra událostí, použitých prostředků a stavu operace.

Kromě toho můžete povolit nastavení diagnostiky protokolu aktivit Azure a odeslat je do účtu Azure Storage, centra událostí nebo pracovního prostoru Log Analytics. Protokoly aktivit poskytují přehled o operacích, které byly provedeny v Event Hubs Azure a dalších prostředcích. Pomocí protokolů aktivit můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) provedené v oborech názvů Azure Event Hubs.

Postup povolení nastavení diagnostiky pro Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Jak povolit nastavení diagnostiky pro protokol aktivit Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné**materiály: v rámci Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace za účelem zachycení a kontroly incidentů souvisejících s centrem událostí.

Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné**materiály: analýza a sledování protokolů pro neobvyklé chování a pravidelné kontroly výsledků souvisejících s centry událostí. Pomocí Log Analytics Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo jiného dodavatele SIEM.
 

Další informace o pracovním prostoru Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Postup provádění vlastních dotazů v Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Jak připojit Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Doprovodné**materiály: v rámci Azure monitor Nakonfigurujte protokoly týkající se Azure Event Hubs v protokolu aktivit a Event Hubs nastavení diagnostiky k odesílání protokolů do log Analyticsho pracovního prostoru, který se má dotazovat nebo do účtu úložiště pro dlouhodobé archivace úložiště. Pomocí Log Analytics pracovního prostoru můžete vytvářet výstrahy pro aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech.

Alternativně můžete povolit a začlenit data do Azure Sentinel. 

Pochopení protokolu aktivit Azure: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Postup konfigurace nastavení diagnostiky pro Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Jak upozornit na data protokolu Log Analytics pracovního prostoru: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Jak připojit Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center**: zatím není k dispozici

**Zodpovědnost**: zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné**materiály: nepoužitelné; Centrum událostí nezpracovává protokolování proti malwaru.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné**materiály: nepoužitelné; Event Hubs nezpracovává ani nevytváří protokoly související s DNS.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné**materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné**materiály: Azure Active Directory (AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu. 

Jak získat roli adresáře ve službě Azure AD pomocí prostředí PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Jak získat členy role adresáře ve službě Azure AD pomocí prostředí PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: přístup roviny ovládacího prvku k Event Hubs je řízen prostřednictvím Azure Active Directory (AD). Azure AD nemá koncept výchozích hesel.

Přístup k rovině dat pro Event Hubs se řídí prostřednictvím Azure AD se spravovanými identitami nebo Registrace aplikací a také signaturami sdíleného přístupu. Sdílené přístupové podpisy používají klienti připojující se k centrům událostí a dají se kdykoli znovu vygenerovat.

Vysvětlení sdílených přístupových podpisů pro Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné**materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrovaných zásad Azure, jako je například:

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.

- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

Použití Azure Security Center k monitorování identity a přístupu (Preview): https://docs.microsoft.com/azure/security-center/security-center-identity-access

Jak používat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné**materiály: Microsoft Azure poskytuje integrované řízení přístupu pro prostředky a aplikace založené na Azure Active Directory (AD). Klíčovou výhodou použití Azure AD s Azure Event Hubs je, že už nemusíte ukládat přihlašovací údaje do kódu. Místo toho můžete požádat o přístupový token OAuth 2,0 z platformy Microsoft Identity Platform. Název prostředku pro vyžádání tokenu je https: \/ /eventhubs.Azure.NET/. Azure AD ověřuje objekt zabezpečení (uživatel, skupina nebo instanční objekt), který spouští aplikaci. Pokud je ověření úspěšné, služba Azure AD vrátí přístupový token do aplikace a aplikace pak může pomocí přístupového tokenu autorizovat požadavky na prostředky Azure Event Hubs.

Jak ověřit aplikaci s využitím služby Azure AD pro přístup k prostředkům Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Principy jednotného přihlašování pomocí služby Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné**materiály: povolte Azure Active Directory Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu, která vám pomůžou chránit vaše prostředky s povoleným centrem událostí.

Jak povolit vícefaktorové ověřování v Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Jak monitorovat identitu a přístup v rámci Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným Multi-Factor Authentication (MFA), které jsou nakonfigurovány pro přihlášení a konfiguraci prostředků s podporou centra událostí.

Další informace o pracovních stanicích s privilegovaným přístupem: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Jak povolit vícefaktorové ověřování v Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Pokyny**: pomocí Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) můžete generovat protokoly a výstrahy, když dojde k podezřelé nebo nebezpečné aktivitě v prostředí. Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy týkající se rizikového chování uživatelů. Pro další protokolování odešlete výstrahy Azure Security Center detekce rizik do Azure Monitor a nakonfigurujte vlastní výstrahy a oznámení pomocí skupin akcí.

Postup nasazení Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Vysvětlení zjišťování rizik Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Jak nakonfigurovat skupiny akcí pro vlastní výstrahy a oznámení: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.



Jak nakonfigurovat pojmenovaná umístění v Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné**materiály: použití Azure Active Directory (AD) jako centrálního ověřování a autorizačního systému pro prostředky Azure, jako je například Event Hubs. To umožňuje řízení přístupu na základě role (RBAC) pro správu citlivých prostředků.

 Jak vytvořit a nakonfigurovat instanci Azure AD: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Další informace o tom, jak Azure Event Hubs integruje s Azure Active Directory (AAD), najdete v tématu Autorizace přístupu k prostředkům Event Hubs pomocí Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Azure Active Directory (AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

V dalším případě pravidelně otáčejte Event Hubs "sdílené přístupové podpisy".

Pochopení sestav Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Jak používat kontroly přístupu Azure identity: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Princip sdílených přístupových podpisů pro Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Doprovodné**materiály: máte přístup k aktivitě přihlašování Azure Active Directory (AD), událostem auditu a rizikovým protokolům událostí, které umožňují integraci s jakýmkoli nástrojem Siem/monitoring.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy protokolu můžete nakonfigurovat v rámci Log Analytics.

Jak integrovat protokoly aktivit Azure do Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Autorizovat přístup k prostředkům Event Hubs pomocí Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Pokyny**: pomocí funkcí Azure Active Directory ochrany identit a detekce rizik můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce související s vašimi prostředky s podporou Event Hubs. Pomocí služby Azure Sentinel byste měli povolit automatizované odezvy pro implementaci reakcí zabezpečení vaší organizace.

Jak zobrazit rizikové přihlašování Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Jak nakonfigurovat a povolit zásady pro rizika ochrany identity: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak připojit Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné**materiály: aktuálně není k dispozici; Customer Lockbox ještě není pro Event Hubs podporovaná.

Seznam služeb podporovaných Customer Lockbox: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: aktuálně není k dispozici.

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: pomocí značek u prostředků souvisejících s vaším Event Hubs pomáhat při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

Vytváření a používání značek: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Obory názvů Event Hubs by měly být oddělené pomocí virtuální sítě s povolenými koncovými body služby a odpovídajícím způsobem označeny.

Obor názvů Azure Event Hubs můžete také zabezpečit pomocí bran firewall. Azure Event Hubs podporuje řízení přístupu na základě IP adres pro podporu příchozích bran firewall. Pravidla brány firewall můžete nastavit pomocí Azure Portal, Azure Resource Manager šablon nebo prostřednictvím rozhraní příkazového řádku Azure CLI nebo Azure PowerShell.

Jak vytvořit další předplatná Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu: https://docs.microsoft.com/azure/governance/management-groups/create

Nakonfigurujte pravidla brány firewall protokolu IP pro Azure Event Hubs obory názvů: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Jak vytvářet a používat značky: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Postup vytvoření Virtual Network: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné**materiály: při použití virtuálních počítačů pro přístup k centrům událostí můžete pomocí virtuálních sítí, koncových bodů služby, Event Hubs brány firewall, skupin zabezpečení sítě a značek služeb zmírnit možnost exfiltraceí dat.

Microsoft spravuje základní infrastrukturu pro Azure Event Hubs a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

Nakonfigurujte pravidla brány firewall protokolu IP pro Azure Event Hubs obory názvů: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Pochopení Virtual Networkch koncových bodů služby pomocí Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integrace Azure Event Hubs s privátním propojením Azure: https://docs.microsoft.com/azure/event-hubs/private-link-service

Pochopení skupin zabezpečení sítě a značek služeb: https://docs.microsoft.com/azure/virtual-network/security-overview

Pochopení ochrany zákaznických dat v Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Azure Event Hubs ve výchozím nastavení vynutil komunikaci zašifrovanou pomocí protokolu TLS. V tuto chvíli jsou podporované TLS verze 1,0, 1,1 a 1,2. TLS 1,0 a 1,1 ale mají cestu k vyřazení celého oboru, takže pokud je to možné, použijte protokol TLS 1,2.

Vysvětlení funkcí zabezpečení Event Hubs najdete v tématu zabezpečení sítě:  https://docs.microsoft.com/azure/event-hubs/network-security

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné**materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou pro Azure Event Hubs k dispozici. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

Pochopení ochrany zákaznických dat v Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: sdílená

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: Azure Event Hubs podporuje použití Azure Active Directory (AD) k autorizaci požadavků na Event Hubs prostředky. Pomocí Azure AD můžete použít řízení přístupu na základě role (RBAC) k udělení oprávnění objektu zabezpečení, který může být uživatel nebo instanční objekt služby.

Pochopení služby Azure AD RBAC a dostupných rolí pro Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné**materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Event Hubs a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

Pochopení ochrany zákaznických dat v Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: Azure Event Hubs podporuje možnost šifrování neaktivních dat buď pomocí klíčů spravovaných Microsoftem nebo klíčů spravovaných zákazníkem. Tato funkce umožňuje vytvořit, otočit, zakázat a odvolat přístup k klíčům spravovaným zákazníkem, které se používají k šifrování neaktivních dat služby Azure Event Hubs.

Postup konfigurace klíčů spravovaných zákazníkem pro šifrování Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné**materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Azure Event Hubs a dalších důležitých nebo souvisejících prostředcích.

Vytváření upozornění pro události protokolu aktivit Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné**materiály: nepoužitelné; Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Event Hubs.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné**materiály: nepoužitelné; Microsoft provádí správu oprav pro základní systémy, které podporují Event Hubs.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

**Doprovodné**materiály: nepoužitelné; Srovnávací test je určený pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné**materiály: nepoužitelné; Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Event Hubs.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné**materiály: nepoužitelné; Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Event Hubs.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky (včetně oborů názvů Azure Event Hubs) v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

Jak vytvářet dotazy pomocí Azure Resource graphu: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit vaše předplatná Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Porozumění službě Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

Vytváření a používání značek: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování oborů názvů Azure Event Hubs a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Jak vytvořit další předplatná Azure: https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu: https://docs.microsoft.com/azure/governance/management-groups/create

Vytváření a používání značek: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: udržování inventáře schválených prostředků Azure a softwarových titulů

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Kromě toho použijte Azure Resource Graph k dotazování nebo zjišťování prostředků v rámci předplatných.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure graphu: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku s Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Omezení schopnosti uživatelů pracovat s Azure Resource Manager prostřednictvím skriptů</div>

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nasazení služby Azure Event Hubs. Použijte aliasy Azure Policy v oboru názvů Microsoft. EventHub k vytvoření vlastních zásad pro auditování nebo vymáhání konfigurací. Můžete také využít integrované definice zásad pro Azure Event Hubs například:

- Měly by být povolené diagnostické protokoly v centru událostí.

- Centrum událostí by mělo používat koncový bod služby virtuální sítě.

Integrovaná zásada Azure pro obor názvů Event Hubs: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Jak zobrazit dostupné aliasy Azure Policy: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné**materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistují] pro vymáhání zabezpečených nastavení napříč prostředky s povolenými Event Hubs. 

Jak nakonfigurovat a spravovat Azure Policy:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Další informace o Azure Policych důsledcích:  https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: Pokud používáte vlastní definice Azure Policy pro Event Hubs nebo související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

Jak ukládat kód v Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentaci: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. EventHub k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. EventHub k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro nasazení Azure Event Hubs a související prostředky použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje].

Jak nakonfigurovat a spravovat Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné**materiály: u virtuálních počítačů Azure nebo webových aplikací běžících na Azure App Service se používá pro přístup k centrům událostí, použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy sdíleného přístupového podpisu pro nasazení Event Hubs Azure. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Konfigurace klíčů spravovaných zákazníkem pro Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Jak integrovat se spravovanými identitami Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Postup vytvoření Key Vault: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Ověření Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Jak přiřadit zásadu přístupu Key Vault: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné**materiály: u virtuálních počítačů Azure nebo webových aplikací běžících na Azure App Service se používá pro přístup k centrům událostí, použití identita spravované služby ve spojení s Azure Key Vault k zjednodušení a zabezpečení Event Hubs Azure. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Azure Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Konfigurace klíčů spravovaných zákazníkem pro Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Postup konfigurace spravovaných identit: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Jak integrovat se spravovanými identitami Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

Jak nastavit skener přihlašovacích údajů: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na zákaznickém obsahu.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: předběžná kontrola veškerého obsahu, který se nahrává do nevýpočetních prostředků Azure, jako je například Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL atd. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například mezipaměť Azure pro Redis), ale neběží na zákaznickém obsahu.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: Konfigurace geografického zotavení po havárii pro Azure Event Hubs. Při výpadku provozu celé oblasti Azure nebo Datacenter (Pokud se nepoužívají žádné zóny dostupnosti) je důležité, aby zpracování dat pokračovalo v práci v jiné oblasti nebo datacentru. V takovém případě geografické zotavení po havárii a geografická replikace jsou důležité funkce pro všechny podniky. Azure Event Hubs podporuje jak geografické zotavení po havárii, tak i geografickou replikaci na úrovni oboru názvů. 

Vysvětlení geografického zotavení po havárii pro Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Pokyny**: Azure Event Hubs poskytuje šifrování neaktivních dat pomocí šifrování služby Azure Storage (Azure SSE). Event Hubs spoléhá na Azure Storage uložení dat a ve výchozím nastavení se všechna data uložená pomocí Azure Storage šifrují pomocí klíčů spravovaných Microsoftem. Pokud používáte Azure Key Vault k ukládání klíčů spravovaných zákazníkem, zajistěte pravidelné automatizované zálohování vašich klíčů.

Pomocí následujícího příkazu PowerShellu Zajistěte pravidelné automatizované zálohování Key Vault tajných kódů: Backup-AzKeyVaultSecret

Postup konfigurace klíčů spravovaných zákazníkem pro šifrování dat Azure Event Hubs v klidovém umístění: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Postup při zálohování Key Vault tajných kódů: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Pokyny**: testování obnovení zálohovaných spravovaných klíčů zákazníků.

 

Postup obnovení klíčů trezoru klíčů v Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Pokyny**: povolení obnovitelného odstranění v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění. Azure Event Hubs vyžaduje, aby klíče spravované zákazníky měly obnovitelné odstranění a nenakonfigurovaly se.

Nakonfigurujte obnovitelné odstranění pro účet Azure Storage, který se používá k zachytávání dat Event Hubs. Všimněte si, že tato funkce ještě není podporovaná Azure Data Lake Storage Gen 2.

Jak povolit obnovitelné odstranění v Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Nastavení trezoru klíčů pomocí klíčů: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Obnovitelné odstranění pro objekty blob Azure Storage: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Doprovodné**materiály: Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují role pracovníků a také fáze zpracování a správy incidentů.

Postup konfigurace automatizace pracovních postupů v rámci Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Pokyny**: Security Center přiřadí závažnosti výstrahám, které vám pomůžou určit prioritu pořadí, ve kterém se zúčastníte jednotlivých výstrah, takže pokud dojde k ohrožení určitého prostředku, můžete se k němu hned dostat. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

Přečtěte si téma publikace NIST: Průvodce pro testování, školení a cvičení programů pro plány a možnosti IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy. 

Jak nastavit Azure Security Center kontakt zabezpečení: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné**materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat ověřovací data výstrah.

Postup konfigurace průběžného exportu: https://docs.microsoft.com/azure/security-center/continuous-export

Jak streamovat výstrahy do Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech důležitých zjištění zabezpečení do 60 dnů.

**Doprovodné**materiály: řiďte se prosím pravidly zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .
Další informace o strategii a provádění testování na základě červeného seskupování a testování průniku na webu pomocí spravované cloudové infrastruktury, služeb a aplikací Microsoftu najdete tady: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [plánech zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)
