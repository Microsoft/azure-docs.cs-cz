---
title: Základní hodnoty zabezpečení Azure pro Logic Apps
description: Základní Logic Apps zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0b74962a192de6a10c09b9855780ed0cd6244515
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967285"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Základní hodnoty zabezpečení Azure pro Logic Apps

Tato základní hodnota zabezpečení platí pro Logic Apps pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Logic Apps. **Ovládací prvky** , které se nevztahují na Logic Apps nebo pro které je odpovědnost od Microsoftu, jsou vyloučené.

Pokud chcete zjistit, jak Logic Apps kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Logic Apps Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: konektory, které běží v globálním, víceklientské Logic Apps službě, jsou nasazené a spravované Microsoftem. Tyto konektory poskytují triggery a akce pro přístup ke cloudovým službám, místním systémům nebo obojímu, včetně Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint a dalších. Značku služby AzureConnectors můžete použít při zadávání pravidel ve skupině zabezpečení sítě nebo Azure Firewall pro povolení přístupu k souvisejícím prostředkům.

Pro Logic Apps, které potřebují přímý přístup k prostředkům ve službě Azure Virtual Network, můžete vytvořit prostředí ISE (Integration Service Environment), kde můžete vytvářet, nasazovat a spouštět aplikace logiky na vyhrazených prostředcích. Některé virtuální sítě Azure používají pro poskytování přístupu ke službám Azure PaaS, jako jsou například Azure Storage, Azure Cosmos DB, Azure SQL Database, partnerské služby nebo služby zákazníkům hostované v Azure, privátní koncové body (privátní propojení Azure). Pokud vaše aplikace logiky potřebují přístup k virtuálním sítím, které používají privátní koncové body, musíte tyto aplikace logiky vytvořit, nasadit a spustit v rámci ISE.

Při vytváření ISE můžete použít buď interní nebo externí koncové body přístupu. Váš výběr určuje, jestli žádosti nebo triggery Webhooku v Logic Apps ve vašem ISE můžou přijímat volání z vnějšku vaší virtuální sítě. Koncové body interního a externího přístupu také ovlivňují, jestli můžete zobrazit historii spuštění vaší aplikace logiky, včetně vstupů a výstupů pro běh, a to z vnitřku nebo vně vaší virtuální sítě.

Ujistěte se, že všechna nasazení podsítí virtuální sítě související s ISE mají skupinu zabezpečení sítě, která se použije pro řízení přístupu k síti specifická pro důvěryhodné porty a zdroje vaší aplikace. Když nasadíte Logic Apps do ISE, použijte privátní odkaz. Privátní odkaz Azure umožňuje přístup ke službám Azure PaaS a zákaznickým/partnerským službám Azure hostovaným na zákazníky přes privátní koncový bod ve vaší virtuální síti. Případně, pokud máte konkrétní případ použití, můžete tento požadavek splnit implementací Azure Firewall. Chcete-li při nastavování pravidel zabezpečení snížit složitost, použijte značky služby, které reprezentují skupiny předpon IP adres pro konkrétní službu Azure.

- [Vysvětlení konektorů pro Logic Apps](../connectors/apis-list.md)

- [Principy značek služeb v Azure](../virtual-network/service-tags-overview.md)

- [Pochopení přístupu k prostředkům Azure Virtual Network z Azure Logic Apps pomocí prostředí integračních služeb (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Vysvětlení koncových bodů služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Principy privátního odkazu Azure](../private-link/private-link-overview.md)

- [Principy přístupu ke koncovému bodu ISE](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview#ise-endpoint-access)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Jak nasadit a nakonfigurovat Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak povolit přístup pro ISE](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment#enable-access-for-ise)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: Pokud spouštíte Logic Apps v prostředí ISE (Integration Service Environment), které používá externí přístupový bod, můžete ke snížení rizika exfiltrace dat použít skupinu zabezpečení sítě (NSG). Povolte protokoly toku NSG a odešlete protokoly do účtu Azure Storage pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Principy přístupu ke koncovému bodu ISE](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview#ise-endpoint-access)

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: Pokud vaše aplikace logiky používá Trigger založený na požadavku, který přijímá příchozí volání nebo požadavky, jako je například požadavek nebo Trigger Webhooku, můžete omezit přístup tak, aby vaše aplikace logiky mohla volat jenom autorizovaní klienti.

Pokud spouštíte Logic Apps v prostředí ISE (Integration Service Environment), DDoS Protection Povolte ve virtuální síti, která je přidružená k ISE, možnost standard, abyste se mohli chránit před útoky DDoS. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

Nasaďte Azure Firewall na všech hranicích sítě organizace s povolenou funkcí Analýza hrozeb a nakonfigurované na "výstrahy a zamítnutí" pro škodlivý síťový provoz.

Použijte Azure Security Center k přístupu k síti jenom v čase, abyste mohli nakonfigurovat skupin zabezpečení sítě tak, aby po omezenou dobu omezila počet vystavování koncových bodů na schválené IP adresy

Pomocí Azure Security Center adaptivního posílení zabezpečení sítě doporučujeme doporučit konfigurace NSG, které omezují porty a zdrojové IP adresy na základě skutečného provozu a analýzy hrozeb.

- [Postup zabezpečení příchozích volání na Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-inbound-requests)

- [Jak omezit příchozí IP adresy](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#restrict-inbound-ip-addresses)

- [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

- [Pochopení Azure Security Center adaptivního posílení zabezpečení sítě](../security-center/security-center-adaptive-network-hardening.md)

- [Pochopení Azure Security Center k síťovému Access Control v čase](../security-center/security-center-just-in-time.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: Pokud spouštíte Logic Apps v prostředí ISE (Integration Service Environment), které používá externí přístupový bod, můžete k omezení rizika exfiltrace dat použít skupinu zabezpečení sítě (NSG). Povolte protokoly toku NSG a odešlete protokoly do účtu Azure Storage pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Chcete-li zajistit další ochranu a informace o síťovém provozu, můžete se podívat na protokoly přístupu, které jsou generovány pouze v případě, že jsou povoleny v každé instanci Application Gateway. Pomocí tohoto protokolu můžete zobrazit vzory přístupu Application Gateway a analyzovat důležité informace. Patří sem IP adresa volajícího, požadovaná adresa URL, latence odpovědi, návratový kód a bajtů.

V opačném případě můžete z webu Marketplace využít řešení třetí strany, které tento požadavek splní.

- [Principy přístupu ke koncovému bodu ISE](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview#ise-endpoint-access)

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Jak integrovat API Management do interní virtuální sítě s Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Pochopení protokolů přístupu WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/web-application-firewall-logs#access-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: vyberte nabídku z Azure Marketplace, která podporuje funkce ID/IP adresy s možnostmi kontroly zatížení.  Pokud zjišťování neoprávněných vniknutí nebo prevence na základě kontroly zatížení není požadavkem, je možné použít Azure Firewall s analýzou hrozeb. Azure Firewall filtrování na základě logiky hrozeb může upozorňovat a zamítnout provoz do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu.

Nasaďte řešení brány firewall podle svého výběru na základě hranic sítě vaší organizace za účelem detekce nebo odepření škodlivého provozu.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Postup konfigurace výstrah pomocí Azure Firewall](../firewall/threat-intel.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: Pokud spouštíte Logic Apps v prostředí ISE (Integration Service Environment), nasaďte Azure Application Gateway.

- [Jak integrovat API Management do interní virtuální sítě s Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Postup konfigurace Application Gateway pro použití protokolu HTTPS](../application-gateway/create-ssl-portal.md) 

- [Princip vyrovnávání zatížení vrstvy 7 pomocí bran webových aplikací Azure](../application-gateway/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků, které potřebují přístup k instancím Azure Logic Apps, použijte značky služby virtuální sítě a definujte řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. LogicApps, LogicAppsManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Další informace o použití značek služeb](../virtual-network/service-tags-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky související s vašimi Azure Logic Apps instancemi Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. Logic a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě instancí Azure Logic Apps. Můžete také využít integrované definice zásad, například:

- Měly by být povolené diagnostické protokoly v Logic Apps.

- Měla by být povolená DDoS Protection Standard.

Pomocí plánů Azure můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte artefakty klíčových prostředí, jako jsou například šablony Azure Resource Manager, řízení přístupu na základě role Azure (Azure RBAC) a zásady v jediné definici podrobného plánu. Podrobné sestavování můžete snadno použít pro nová předplatná a prostředí a vyladit řízení a správu prostřednictvím správy verzí.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: u individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují přenos do/ze sítě.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Seznam Azure Policy definic pro Logic Apps](policy-reference.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi Azure Logic Apps instancemi. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: Pokud chcete získat bohatší ladicí informace o aplikacích logiky během běhu, můžete nastavit a použít protokoly Azure monitor k zaznamenávání a ukládání informací o běhových datech a událostech, jako jsou události triggeru, události spuštění a události akcí v pracovním prostoru Log Analytics. Azure Monitor pomáhá monitorovat cloudová a místní prostředí a usnadňuje zajištění jejich dostupnosti a výkonu. Pomocí protokolů Azure Monitor můžete vytvářet dotazy protokolů, které vám pomůžou shromáždit a zkontrolovat tyto informace. Tato diagnostická data můžete také použít s jinými službami Azure, například Azure Storage a Azure Event Hubs.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](/azure/azure-monitor/platform/activity-log)

- [Jak nastavit protokoly Azure Monitor a shromažďovat diagnostická data pro Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: Pokud chcete získat bohatší ladicí informace o aplikacích logiky během běhu, můžete nastavit a použít protokoly Azure monitor k zaznamenávání a ukládání informací o běhových datech a událostech, jako jsou události triggeru, události spuštění a události akcí v pracovním prostoru Log Analytics. Azure Monitor pomáhá monitorovat cloudová a místní prostředí a usnadňuje zajištění jejich dostupnosti a výkonu. Pomocí protokolů Azure Monitor můžete vytvářet dotazy protokolů, které vám pomůžou shromáždit a zkontrolovat tyto informace. Tato diagnostická data můžete také použít s jinými službami Azure, například Azure Storage a Azure Event Hubs.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](/azure/azure-monitor/platform/activity-log)

- [Jak nastavit protokoly Azure Monitor a shromažďovat diagnostická data pro Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. Logic**:

[!INCLUDE [Resource Policy for Microsoft.Logic 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.logic-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: po vytvoření a spuštění aplikace logiky můžete kontrolovat stav spuštění aplikace logiky, historii spuštění, historii aktivačních událostí a výkon. Pro monitorování událostí v reálném čase a bohatší ladění nastavte protokolování diagnostiky pro vaši aplikaci logiky pomocí protokolů Azure Monitor. Tato služba Azure vám pomůže monitorovat cloudové a místní prostředí, abyste mohli snadněji udržovat jejich dostupnost a výkon. Pak můžete najít a zobrazit události, jako jsou události triggeru, události spuštění a události akcí. Uložením těchto informací v protokolech Azure Monitor můžete vytvořit dotazy protokolu, které vám pomůžou najít a analyzovat tyto informace. Tato diagnostická data můžete také použít s jinými službami Azure, například Azure Storage a Azure Event Hubs.

V Azure Monitor nastavte dobu uchování protokolu pro protokoly přidružené k vašim Azure Logic Apps instancí podle předpisů pro dodržování předpisů vaší organizace.

- [Jak monitorovat stav spuštění, zkontrolovat historii triggeru a nastavit výstrahy pro Azure Logic Apps](monitor-logic-apps.md)

- [Postup nastavení parametrů uchovávání protokolů](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: Chcete-li nastavit protokolování pro aplikaci logiky, můžete povolit Log Analytics při vytváření aplikace logiky nebo můžete řešení pro správu Logic Apps nainstalovat do pracovního prostoru Log Analytics pro existující aplikace logiky. Toto řešení poskytuje agregované informace pro vaše spuštění aplikace logiky a zahrnuje konkrétní podrobnosti, jako je stav, doba provádění, stav opětovného odeslání a ID korelace. Pokud pak chcete povolit protokolování a vytváření dotazů pro tyto informace, nastavte protokoly Azure Monitor.

Můžete také povolit nastavení diagnostiky protokolu aktivit Azure a odesílat protokoly do pracovního prostoru Log Analytics. Pomocí dotazů v Log Analytics můžete vyhledávat hledané výrazy, identifikovat trendy, analyzovat vzorce a poskytovat spoustu dalších přehledů na základě dat protokolu aktivit, která se mohla shromažďovat pro Azure Logic Apps.

Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Jak nastavit protokoly Azure Monitor a shromažďovat diagnostická data pro Azure Logic Apps](monitor-logic-apps-log-analytics.md)

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](/azure/azure-monitor/platform/activity-log)

- [Jak shromažďovat a analyzovat protokoly aktivit Azure v Log Analytics v Azure Monitor](/azure/azure-monitor/platform/activity-log-collect)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použijte Azure Security Center se Log Analytics pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech.

Alternativně můžete povolit a začlenit data do Azure Sentinel.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Správa výstrah v Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Upozornění na data protokolu Log Analytics](/azure/azure-monitor/learn/tutorial-response)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

Aby bylo možné snadno získat přístup k dalším prostředkům chráněným službou Azure AD a ověřit vaši identitu bez přihlašování, může vaše aplikace logiky používat spravovanou identitu (dříve Identita spravované služby nebo MSI) místo přihlašovacích údajů nebo tajných kódů. Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte zadávat ani vyměňovat tajné kódy.

Každý koncový bod žádosti v aplikaci logiky má v adrese URL koncového bodu sdílený přístupový podpis (SAS). Pokud sdílíte adresu URL koncového bodu pro aktivační událost na základě požadavku s ostatními stranami, můžete vygenerovat adresy URL zpětného volání, které používají konkrétní klíče a mají datum vypršení platnosti. Tímto způsobem můžete plynule vrátit klíče nebo omezit přístup k aktivaci aplikace logiky na základě konkrétního časového rozmezí.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Ověřování přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps](create-managed-service-identity.md)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Jak zabezpečit přístup a data v Azure Logic Apps pomocí SAS](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#sas)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: Azure Active Directory (Azure AD) a Azure Logic Apps nemají koncept výchozích hesel.

Pokud se používá základní ověřování, budete muset zadat uživatelské jméno a heslo. Při vytváření těchto přihlašovacích údajů se ujistěte, že nakonfigurujete silné heslo pro ověřování.

Pokud používáte infrastrukturu jako kód, neukládejte hesla do kódu a místo toho použijte Azure Key Vault k ukládání a načítání přihlašovacích údajů.

- [Zabezpečení a přístup k datům v Logic Apps](logic-apps-securing-a-logic-app.md)

- [Jak nastavit a načíst tajný klíč z Azure Key Vault](../key-vault/general/quick-create-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrovaných zásad Azure, jako je například:

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

Další informace najdete v následujících referenčních materiálech:

- [Použití Azure Security Center k monitorování identity a přístupu (Preview)](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Pokyny**: použijte registraci aplikace Azure (instanční objekt) k získání tokenu, který se dá použít k interakci s Recovery Servicesmi trezory prostřednictvím volání rozhraní API.

Mnoho konektorů také vyžaduje, abyste nejprve vytvořili připojení k cílové službě nebo systému a poskytovali přihlašovací údaje pro ověření nebo jiné konfigurační údaje, než můžete použít Trigger nebo akci v aplikaci logiky. Například musíte autorizovat připojení k účtu Twitteru pro přístup k datům nebo k příspěvku vaším jménem.

Pro konektory, které používají Azure Active Directory (Azure AD) OAuth, vytvoření připojení znamená přihlášení ke službě, jako je například Office 365, Salesforce nebo GitHub, kde je váš přístupový token zašifrovaný a bezpečně uložený v úložišti tajného úložiště Azure. Jiné konektory, například FTP a SQL, vyžadují připojení, které má podrobnosti o konfiguraci, jako je adresa serveru, uživatelské jméno a heslo. Tyto podrobnosti konfigurace připojení jsou taky šifrované a bezpečně uložené.

- [Volání rozhraní Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Registrace klientské aplikace pomocí Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Pracovní postup spustí informace rozhraní API](/rest/api/logic/workflowtriggers)

- [Principy konfigurace konektoru](../connectors/apis-list.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Doprovodné** materiály: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení k prostředkům Azure a jejich konfigurace.

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

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

Každý koncový bod žádosti v aplikaci logiky navíc má v adrese URL koncového bodu sdílený přístupový podpis (SAS). Aplikaci logiky můžete omezit tak, aby přijímala požadavky pouze z určitých IP adres.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Vysvětlení, jak omezit příchozí IP adresy v Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#restrict-inbound-ip-addresses)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) jako centrální ověřování a systém autorizace pro vaše Azure Logic Apps instance. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Pokud je v Logic Apps podporovaná podpora, využijte spravovanou identitu pro snadný přístup k dalším prostředkům, které jsou chráněné službou Azure AD, a ověřte svoji identitu bez přihlašování, ale přihlašovacích údajů nebo tajných kódů. Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte zadávat ani vyměňovat tajné kódy.

Azure Logic Apps podporuje spravované identity přiřazené systémem i uživatelem. Aplikace logiky může používat buď identitu přiřazenou systémem, nebo jedinou identitu přiřazenou uživatelem, kterou můžete sdílet ve skupině aplikací logiky, ale nikoli obojí. V současné době pouze konkrétní vestavěné triggery a akce podporují spravované identity, nikoli spravované konektory nebo připojení, například:

- HTTP
- Azure Functions
- Azure API Management
- Azure App Services

Další informace najdete v následujících referenčních materiálech:

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Ověřování přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps](create-managed-service-identity.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) jako centrální ověřování a systém autorizace pro vaše Azure Logic Apps instance. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Máte přístup ke zdrojům přihlašovacích aktivit, auditování a rizikových protokolů událostí Azure AD, které vám umožní integrovat s ověřováním Azure Sentinel nebo SIEM třetí strany.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy protokolu můžete nakonfigurovat v rámci Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Postup zprovoznění služby Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: použití funkcí Azure Active Directory (Azure AD) pro rizika a ochranu identity ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

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

**Doprovodné** materiály: konektory, které běží v globálním, víceklientské Logic Apps službě, jsou nasazené a spravované Microsoftem. Tyto konektory poskytují triggery a akce pro přístup ke cloudovým službám, místním systémům nebo obojímu, včetně Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint a dalších.

Pro Logic Apps, které potřebují přímý přístup k prostředkům ve službě Azure Virtual Network, můžete vytvořit prostředí ISE (Integration Service Environment), kde můžete vytvářet, nasazovat a spouštět aplikace logiky na vyhrazených prostředcích. Některé virtuální sítě Azure používají pro poskytování přístupu ke službám Azure PaaS, jako jsou například Azure Storage, Azure Cosmos DB nebo Azure SQL Database, partnerské služby nebo služby zákazníkům hostované v Azure, privátní koncové body (privátní propojení Azure). Pokud vaše aplikace logiky potřebují přístup k virtuálním sítím, které používají privátní koncové body, musíte tyto aplikace logiky vytvořit, nasadit a spustit v rámci ISE.

Při vytváření ISE můžete použít buď interní nebo externí koncové body přístupu. Váš výběr určuje, jestli žádosti nebo triggery Webhooku v Logic Apps ve vašem ISE můžou přijímat volání z vnějšku vaší virtuální sítě.

Kromě toho implementujte izolaci pomocí samostatných předplatných a skupin pro správu pro jednotlivé domény zabezpečení, jako je například typ prostředí a úroveň citlivosti dat. Můžete omezit úroveň přístupu k prostředkům Azure, které vaše aplikace a podniková prostředí vyžadují. Přístup k prostředkům Azure můžete řídit prostřednictvím řízení přístupu na základě role Azure (RBAC).

- [Vysvětlení konektorů pro Logic Apps](../connectors/apis-list.md)

- [Přístup k prostředkům Azure Virtual Network z Azure Logic Apps pomocí prostředí integračních služeb (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md)

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: aktuálně není k dispozici; pro Azure Logic Apps ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací.

Využijte řešení třetích stran z Azure Marketplace na hraničních sítích, které monitorují neoprávněný přenos citlivých informací a zablokují tyto přenosy, a upozorní odborníky na zabezpečení informací. 

Společnost Microsoft spravuje základní infrastrukturu pro Azure Logic Apps a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: šifrování všech citlivých informací během přenosu. V Azure Logic Apps se všechna data během přenosu aplikace logiky šifrují během přenosu pomocí protokolu TLS (Transport Layer Security) a v klidovém stavu. Když si zobrazíte historii spuštění vaší aplikace logiky, Logic Apps ověří váš přístup a pak poskytuje odkazy na vstupy a výstupy pro žádosti a odpovědi pro každé spuštění. U akcí, které zpracovávají všechna hesla, tajné klíče, klíče nebo jiné citlivé informace, ale chcete ostatním uživatelům zabránit v prohlížení těchto dat a přístupu k nim. Například pokud vaše aplikace logiky získá tajný kód z Azure Key Vault, který se má použít při ověřování akce HTTP, chcete tento tajný klíč skrýt ze zobrazení.

Aktivační událost žádosti podporuje pro příchozí požadavky pouze zabezpečení TLS (Transport Layer Security) 1,2. Ujistěte se, že všichni klienti, kteří se připojují k prostředkům Azure, můžou vyjednávat TLS 1,2 nebo vyšší. Odchozí volání pomocí konektoru HTTP podporují protokol TLS (Transport Layer Security) 1,0, 1,1 a 1,2. 

Pokud je to možné, postupujte podle Azure Security Center doporučení pro šifrování v klidovém režimu a šifrování.

- [Zabezpečený přístup a data ve Azure Logic Apps – příchozí volání na triggery na základě požadavků](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-inbound-requests)

- [Zabezpečený přístup a data v Azure Logic Apps – odchozí volání jiných služeb a systémů](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-outbound-requests)

- [Pochopení šifrování při přenosu pomocí Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Pochopení šifrování dat v klidovém případě pomocí Azure](../security/fundamentals/encryption-atrest.md)

- [Nastavení klíčů spravovaných zákazníkem k šifrování dat v klidovém prostředí pro prostředí ISEs (Integration Service Environment) v Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: v Azure Logic Apps mnoho triggerů a akcí má nastavení, které můžete povolit pro zabezpečení vstupů, výstupů nebo obojího zakrytím těchto dat z historie spuštění aplikace logiky.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Logic Apps a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Zabezpečený přístup k datům historie spouštění](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-run-history-data)

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC. 

**Doprovodné** materiály: můžete povolit pouze konkrétní uživatele nebo skupiny, aby mohli spouštět konkrétní úkoly, jako je správa, úpravy a zobrazení Logic Apps. Pokud chcete řídit svá oprávnění, použijte řízení přístupu na základě role Azure (Azure RBAC), abyste členům v rámci předplatného Azure mohli přiřadit přizpůsobené nebo předdefinované role:

- Přispěvatel aplikace logiky: umožňuje spravovat Logic Apps, ale nemůžete pro ně měnit přístup.
- Operátor aplikace logiky: umožňuje číst, povolit a zakázat Logic Apps, ale nemůžete je upravovat ani aktualizovat.

Pokud chcete ostatním uživatelům zabránit ve změně nebo odstranění vaší aplikace logiky, můžete použít Azure Resource Lock. Tato možnost zabraňuje ostatním změnám a odstraňování produkčních prostředků.

- [Zabezpečený přístup k operacím Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-logic-app-operations)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: Azure Logic Apps spoléhá na Azure Storage ukládání a automatické šifrování dat v klidovém umístění. Toto šifrování chrání vaše data a pomáhá splnit závazky zabezpečení vaší organizace a dodržování předpisů. Ve výchozím nastavení používá Azure Storage k šifrování vašich dat klíče spravované Microsoftem.

Když vytvoříte prostředí ISE (Integration Service Environment) pro hostování aplikací logiky a potřebujete větší kontrolu nad šifrovacími klíči používanými Azure Storage, můžete nastavit, používat a spravovat vlastní klíč pomocí Azure Key Vault. Tato funkce se také označuje jako "Bring Your Own Key" (BYOK) a váš klíč se nazývá klíč spravovaný zákazníkem.

- [Šifrování neaktivních dat pro prostředí integrační služby v Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na změny v Azure Logic Apps a také na jiné důležité nebo související prostředky.

- [Vytvoření upozornění pro události protokolu aktivit Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme, abyste vytvořili a používali Azure Resource Manager prostředky, které budou předány dál.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

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

Další informace najdete v následujících referenčních materiálech:

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: Vytvoření inventáře schválených prostředků Azure (například konektorů) a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

Poznámka: vzhledem k zásadám ochrany osobních údajů a ochraně osobních údajů Google můžete použít konektor Gmail jenom se službami schválenými v Google. Tato situace se vyvíjí a může mít vliv na ostatní konektory Google v budoucnu.

- [Seznam všech konektorů Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [Porozumění problémům a omezením pro konektory Gmail](/connectors/gmail/#known-issues-and-limitations)

- [Další informace o zásadách ochrany osobních údajů Google](../connectors/connectors-google-data-security-privacy-policy.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit ve vašich předplatných. 

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

Další informace najdete v následujících referenčních materiálech:

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: prostředky související s vaším Logic Apps, které jsou nutné pro obchodní operace, ale mohou být pro organizaci vyšší rizika, měli byste je izolovat v rámci svého vlastního virtuálního počítače nebo virtuální sítě a dostatečně zabezpečit pomocí Azure firewall nebo skupiny zabezpečení sítě.

Logic Apps, které jsou potřeba pro obchodní operace, ale můžou pro organizaci zvýšit riziko, měli byste je všude, kde to jde, kdykoli je to možné, přes samostatné skupiny prostředků s konkrétními oprávněními a hranicemi Azure RBAC.

- [Jak vytvořit virtuální síť](../virtual-network/quick-create-portal.md) 

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create) 

- [Jak zabezpečit přístup k Logic Apps přes Azure RBAC](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-logic-app-operations)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Logic Apps instance pomocí Azure Policy. Použijte aliasy Azure Policy v oboru názvů Microsoft. Logic, abyste vytvořili vlastní zásady pro auditování nebo prosazování konfigurace vašich Logic Appsch instancí. Například můžete jiným uživatelům zabránit v vytváření a používání připojení k prostředkům, ke kterým chcete omezit přístup.

Kromě toho Azure Resource Manager mít možnost Exportovat šablonu do JavaScript Object Notation (JSON), která by měla být přezkoumána, aby se zajistilo, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

K ochraně citlivých dat a tajných kódů taky používejte zabezpečené parametry.

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Blokovat připojení vytvořená pomocí konektorů v Azure Logic Apps](block-connections-connectors.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Postup nasazení šablon Azure Resource Manager pro Azure Logic Apps](logic-apps-deploy-azure-resource-manager-templates.md)

- [Principy parametrů zabezpečené akce](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-action-parameters)

- [Doporučení zabezpečení pro parametry](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

Definujte a Implementujte standardní konfigurace zabezpečení pro Azure Logic Apps instance pomocí Azure Policy. Použijte aliasy Azure Policy v oboru názvů Microsoft. Logic, abyste vytvořili vlastní zásady pro auditování nebo prosazování konfigurace vašich Logic Appsch instancí. Například můžete jiným uživatelům zabránit v vytváření a používání připojení k prostředkům, ke kterým chcete omezit přístup.

Kromě toho Azure Resource Manager mít možnost Exportovat šablonu do JavaScript Object Notation (JSON), která by měla být přezkoumána, aby se zajistilo, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

Také se ujistěte, že zabezpečíte data v historii spouštění pomocí zmatenosti.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

- [Blokovat připojení vytvořená pomocí konektorů v Azure Logic Apps](block-connections-connectors.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Postup nasazení šablon Azure Resource Manager pro Azure Logic Apps](logic-apps-deploy-azure-resource-manager-templates.md)

- [Zabezpečený přístup ke vstupům a výstupům historie spouštění](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#obfuscate)

- [Zabezpečený přístup ke vstupům parametrů](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#secure-action-parameters)

- [Doporučení zabezpečení pro parametry](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

Kromě toho Azure Resource Manager mít možnost Exportovat šablonu do JavaScript Object Notation (JSON), která by měla být přezkoumána, aby se zajistilo, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

- [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentace k Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů "Microsoft. Logic" k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich prostředků Azure. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů "Microsoft. Logic" k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro prostředky Azure použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje].

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: zabezpečení vstupů a výstupů v historii spuštění aplikace logiky pomocí zmatenosti. Pokud nasazujete v různých prostředích, zvažte parametrizaceí hodnot v definici pracovního postupu vaší aplikace logiky, které se liší v závislosti na těchto prostředích. Tímto způsobem se můžete vyhnout pevně zakódovaným datům pomocí šablony Azure Resource Manager k nasazení aplikace logiky, ochraně citlivých dat definováním zabezpečených parametrů a předání těchto dat jako samostatných vstupů prostřednictvím parametrů šablony pomocí souboru parametrů. Pomocí Key Vault můžete ukládat citlivá data a používat zabezpečené parametry šablon, které tyto hodnoty načítají z Key Vault při nasazení. Pak můžete odkazovat na Trezor klíčů a tajné klíče v souboru parametrů. 

Když vytvoříte prostředí ISE (Integration Service Environment) pro hostování aplikací logiky a potřebujete větší kontrolu nad šifrovacími klíči používanými Azure Storage, můžete nastavit, používat a spravovat vlastní klíč pomocí Azure Key Vault. Tato funkce se také označuje jako "Bring Your Own Key" (BYOK) a váš klíč se nazývá klíč spravovaný zákazníkem.

- [Zabezpečení vstupů a výstupů v historii spuštění v Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#obfuscate)

- [Doporučení zabezpečení pro parametry](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

- [Zabezpečený přístup k vstupům parametrů v Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-parameter-inputs)

- [Předání hodnot zabezpečeného parametru během nasazování pomocí Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Nastavení klíčů spravovaných zákazníkem k šifrování dat v klidovém prostředí pro prostředí ISEs (Integration Service Environment) v Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: pro snadný přístup k dalším prostředkům, které jsou chráněné službou Azure Active Directory (Azure AD) a ověřují vaši identitu bez přihlašování, může vaše aplikace logiky používat spravovanou identitu (dřív identita spravované služby nebo MSI) místo přihlašovacích údajů nebo tajných kódů. Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte zadávat ani vyměňovat tajné kódy.

V současné době pouze konkrétní vestavěné triggery a akce podporují spravované identity, nikoli spravované konektory nebo připojení, například:

- HTTP
- Azure Functions
- Azure API Management
- Azure App Services

Další informace najdete v následujících referenčních materiálech:

- [Jak ověřit přístup k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps](create-managed-service-identity.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Doprovodné** materiály: zabezpečení vstupů a výstupů v historii spuštění aplikace logiky pomocí zmatenosti. Pokud nasazujete v různých prostředích, zvažte parametrizaceí hodnot v definici pracovního postupu vaší aplikace logiky, které se liší v závislosti na těchto prostředích. Tímto způsobem se můžete vyhnout pevně zakódovaným datům pomocí šablony Azure Resource Manager k nasazení aplikace logiky, ochraně citlivých dat definováním zabezpečených parametrů a předání těchto dat jako samostatných vstupů prostřednictvím parametrů šablony pomocí souboru parametrů. Pomocí Key Vault můžete ukládat citlivá data a používat zabezpečené parametry šablon, které tyto hodnoty načítají z Key Vault při nasazení. Pak můžete odkazovat na Trezor klíčů a tajné klíče v souboru parametrů. 

Můžete také implementovat kontrolu přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Zabezpečení vstupů a výstupů v historii spuštění v Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#obfuscate)

- [Doporučení zabezpečení pro parametry](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-best-practices#security-recommendations-for-parameters)

- [Zabezpečený přístup k vstupům parametrů v Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#access-to-parameter-inputs)

- [Předání hodnot zabezpečeného parametru během nasazování pomocí Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použijte centrálně spravovaný antimalwarový software

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Logic Apps), ale neběží na zákaznickém obsahu.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure Backup), ale neběží na vašem obsahu. 

Předem Prohledejte všechny soubory nahrané do nevýpočetních prostředků Azure, například App Service, Data Lake Storage, Blob Storage atd. 

K detekci malwaru nahraného do účtů úložiště použijte detekci hrozeb Azure Security Center pro datové služby. 

- [Pochopení ochrany proti malwaru Microsoftu pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

- [Vysvětlení detekce hrozeb Azure Security Center pro datové služby](/azure/security-center/threat-protection)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: Implementujte řešení zotavení po havárii (Dr) na místě, abyste mohli chránit data, rychle obnovit prostředky, které podporují důležité obchodní funkce, a nechat provoz běžící na zachování kontinuity podnikových služeb (Bc).

Tato strategie zotavení po havárii se zaměřuje na nastavení vaší primární aplikace logiky pro převzetí služeb při selhání v pohotovostním nebo záložní aplikaci logiky v alternativním umístění, kde je k dispozici i Azure Logic Apps. Tímto způsobem, pokud primární utrpí ztráty, přerušení nebo selhání, může sekundární aplikace trvat. Tato strategie vyžaduje, aby vaše sekundární aplikace logiky a závislé prostředky byly nasazené a připravené v alternativním umístění.

Kromě toho byste měli rozšířit základní definici pracovního postupu aplikace logiky na šablonu Azure Resource Manager. Tato šablona definuje infrastrukturu, prostředky, parametry a další informace pro zřizování a nasazení aplikace logiky.

- [Další informace o provozní kontinuitě a zotavení po havárii pro Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Postup automatizace nasazení pro Azure Logic Apps pomocí šablon Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: Implementujte řešení zotavení po havárii (Dr) na místě, abyste mohli chránit data, rychle obnovit prostředky, které podporují důležité obchodní funkce, a nechat provoz běžící na zachování kontinuity podnikových služeb (Bc).

Tato strategie zotavení po havárii se zaměřuje na nastavení vaší primární aplikace logiky pro převzetí služeb při selhání v pohotovostním nebo záložní aplikaci logiky v alternativním umístění, kde je k dispozici i Azure Logic Apps. Tímto způsobem, pokud primární utrpí ztráty, přerušení nebo selhání, může sekundární aplikace trvat. Tato strategie vyžaduje, aby vaše sekundární aplikace logiky a závislé prostředky byly nasazené a připravené v alternativním umístění.

Kromě toho byste měli rozšířit základní definici pracovního postupu aplikace logiky na šablonu Azure Resource Manager. Tato šablona definuje infrastrukturu, prostředky, parametry a další informace pro zřizování a nasazení aplikace logiky.

Každý koncový bod žádosti v aplikaci logiky má v adrese URL koncového bodu sdílený přístupový podpis (SAS). Pokud používáte Azure Key Vault k ukládání tajných klíčů, zajistěte pravidelné automatizované zálohování klíčů a adres URL.

- [Další informace o provozní kontinuitě a zotavení po havárii pro Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Automatizace nasazení pro Azure Logic Apps pomocí šablon Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)

- [Zabezpečený přístup a data v Azure Logic Apps pomocí SAS](https://docs.microsoft.com/azure/logic-apps/logic-apps-securing-a-logic-app#sas)

- [Postup zálohování klíčů Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: vaše strategie zotavení po havárii by se měla zaměřit na nastavení vaší primární aplikace logiky pro převzetí služeb při selhání v pohotovostním nebo zálohovacím programu aplikace logiky v alternativním umístění, kde je taky dostupná Azure Logic Apps. Tímto způsobem, pokud primární utrpí ztráty, přerušení nebo selhání, může sekundární aplikace trvat. Tato strategie vyžaduje, aby vaše sekundární aplikace logiky a závislé prostředky byly nasazené a připravené v alternativním umístění.

Test obnovení zálohovaných klíčů spravovaných zákazníkem Všimněte si, že to platí jenom pro Logic Apps běžící na prostředí ISE (Integration Service Environment).

- [Další informace o provozní kontinuitě a zotavení po havárii pro Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Nastavení klíčů spravovaných zákazníkem k šifrování dat v klidovém prostředí pro prostředí ISEs (Integration Service Environment) v Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: vaše strategie zotavení po havárii by se měla zaměřit na nastavení vaší primární aplikace logiky pro převzetí služeb při selhání v pohotovostním nebo zálohovacím programu aplikace logiky v alternativním umístění, kde je taky dostupná Azure Logic Apps. Tímto způsobem, pokud primární utrpí ztráty, přerušení nebo selhání, může sekundární aplikace trvat. Tato strategie vyžaduje, aby vaše sekundární aplikace logiky a závislé prostředky byly nasazené a připravené v alternativním umístění. 

Ochrana zálohovaných klíčů spravovaných zákazníkem. Všimněte si, že to platí jenom pro Logic Apps běžící na prostředí ISE (Integration Service Environment).

Povolit Soft-Delete a vyprázdnit ochranu v Key Vault a chránit tak klíče proti náhodnému nebo škodlivému odstranění.

- [Další informace o provozní kontinuitě a zotavení po havárii pro Azure Logic Apps](business-continuity-disaster-recovery-guidance.md)

- [Nastavení klíčů spravovaných zákazníkem k šifrování dat v klidovém prostředí pro prostředí ISEs (Integration Service Environment) v Azure Logic Apps](customer-managed-keys-integration-service-environment.md)

- [Postup povolení ochrany Soft-Delete a vyprázdnění v Key Vault](https://docs.microsoft.com/azure/storage/blobs/soft-delete-overview?tabs=azure-portal)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení. 

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Využijte příručku pro zpracování incidentů zabezpečení počítače NIST, která vám pomůže při vytváření vlastního plánu odpovědí na incidenty.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak je Security Center ve vyhledávání nebo v metrikě, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění. 

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka pomocí značek a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure, zejména těch, která zpracovávají citlivá data.  Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: řiďte se pravidly společnosti Microsoft o zapojení, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
