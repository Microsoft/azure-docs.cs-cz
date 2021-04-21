---
title: Základní hodnoty zabezpečení Azure pro Azure Functions
description: Základní Azure Functions zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 90ae774a4e243b854758c1c7a6cc10a882cd584f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831073"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Základní hodnoty zabezpečení Azure pro Azure Functions

Tato základní hodnota zabezpečení platí pro Azure Functions pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure Functions. **Ovládací prvky** , které se nevztahují k Azure Functions byly vyloučeny.

 
Pokud chcete zjistit, jak Azure Functions kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Functions Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: Integrujte své aplikace Azure Functions do služby Azure Virtual Network. Aplikace Function App běžící v plánu Premium mají stejné možnosti hostování jako webové aplikace v Azure App Service, což zahrnuje funkci "Integration VNet".  Azure Virtual Networks umožňují umístit mnoho vašich prostředků Azure, například Azure Functions, do sítě směrovatelné v jiné síti než v Internetu.

- [Jak integrovat funkce do Azure Virtual Network](functions-create-vnet.md)

- [Vysvětlení integrace virtuální sítě pro Azure Functions a Azure App Service](../app-service/web-sites-integrate-with-vnet.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: použijte Azure Security Center a sledujte doporučení pro ochranu sítě a zabezpečte síťové prostředky a síťové konfigurace týkající se vašich aplikací Azure Functions.

Pokud používáte skupiny zabezpečení sítě s implementací Azure Functions, povolte skupiny zabezpečení sítě směrování protokolů a odesílání protokolů do Azure Storage účtu pro audity provozu. Skupiny zabezpečení sítě můžete také odesílat do Log Analyticsho pracovního prostoru a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: k plnému zabezpečení vašich Azure Functionsch koncových bodů v produkčním prostředí byste měli zvážit implementaci jedné z následujících funkcí zabezpečení na úrovni aplikace.

- Zapnout App Service ověřování nebo autorizaci aplikace Function App

- Ověřování požadavků pomocí Azure API Management (APIM)

- Nasaďte aplikaci Function App do Azure App Service Environment.

Kromě toho zajistěte, aby bylo vzdálené ladění pro produkční Azure Functions zakázané. Kromě toho sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat všem doménám přístup k vaší aplikaci Function App v Azure. Povoluje interakci jenom požadovaných domén s vaší aplikací Function App.

Zvažte nasazení brány firewall webových aplikací (WAF) Azure jako součást konfigurace sítě pro další kontrolu příchozího provozu. Povolte nastavení diagnostiky pro protokoly WAF a ingestování do účtu úložiště, centra událostí nebo Log Analytics pracovního prostoru. 

- [Postup zabezpečení Azure Functionsch koncových bodů v produkčním prostředí](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Jak nasadit Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení DDoS Protection Standard ve virtuálních sítích přidružených k aplikacím Function App pro ochranu před útoky DDoS. Používejte funkce analýzy hrozeb v Azure Security Center integrovány k odepření komunikace se známými škodlivými nebo nepoužívanými veřejnými IP adresami.

Kromě toho nakonfigurujte front-end bránu, jako je například firewall webových aplikací Azure, k ověřování všech příchozích požadavků a odfiltrování škodlivých přenosů. Firewall webových aplikací Azure vám může přispět k zabezpečení aplikace Function kontrolou příchozího webového provozu a zablokovat tak injektáže SQL, skriptování mezi weby, nakládku malwaru a DDoS útoky. Zavedení firewallu webových aplikací vyžaduje App Service Environment nebo použití privátních koncových bodů. Pro funkce hostované v plánech Premium a App Service můžete použít soukromé koncové body.

- [Možnosti sítí Azure Functions](functions-networking-options.md)

- [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

- [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: Pokud používáte skupinu zabezpečení sítě s implementací Azure Functions, povolte pro audit provozu protokoly toku skupin zabezpečení sítě a odešlete protokoly do účtu úložiště. Protokoly toku můžete také odesílat do Log Analyticsho pracovního prostoru a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: konfigurace front-endové brány, jako je například firewall webových aplikací Azure pro ověřování všech příchozích požadavků a odfiltrování škodlivých přenosů. Firewall webových aplikací Azure vám může přispět k zabezpečení aplikací funkcí kontrolou příchozího webového provozu a zablokovat vkládání SQL, skriptování mezi weby, nakládku malwaru a DDoS útoky. Zavedení firewallu webových aplikací vyžaduje App Service Environment nebo použití privátních koncových bodů. Pro funkce hostované v plánech Premium a App Service můžete použít soukromé koncové body.

Alternativně je k dispozici několik možností Marketplace, jako je například firewall webových aplikací Barracuda pro Azure, které jsou k dispozici na Azure Marketplace, které zahrnují funkce zjišťování a prevence vniknutí.

- [Možnosti sítí Azure Functions](functions-networking-options.md)

- [Plán Azure Functions Premium](functions-premium-plan.md)

- [Úvod do služby App Service Environment](../app-service/environment/intro.md)

- [Důležité aspekty sítí pro službu App Service Environment](../app-service/environment/network-info.md) 

- [Vysvětlení brány firewall webových aplikací Azure](../web-application-firewall/overview.md)

- [Použití privátních koncových bodů pro Azure Functions](../app-service/networking/private-endpoint.md)

- [Principy cloudové služby Barracuda WAF](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Pokyny**: konfigurace front-endové brány pro vaši síť, například firewall webových aplikací Azure s koncovým šifrováním TLS. Zavedení firewallu webových aplikací vyžaduje App Service Environment nebo použití privátních koncových bodů. Pro funkce hostované v plánech Premium a App Service můžete použít soukromé koncové body.

- [Možnosti sítí Azure Functions](functions-networking-options.md)

- [Plán Azure Functions Premium](functions-premium-plan.md)

- [Úvod do služby App Service Environment](../app-service/environment/intro.md)

- [Důležité aspekty sítí pro službu App Service Environment](../app-service/environment/network-info.md) 

- [Vysvětlení brány firewall webových aplikací Azure](../web-application-firewall/overview.md)

- [Jak nakonfigurovat kompletní protokol TLS pomocí Application Gateway s portálem](../application-gateway/end-to-end-ssl-portal.md)

- [Použití privátních koncových bodů pro Azure Functions](../app-service/networking/private-endpoint.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: použití značek Virtual Network služby k definování řízení přístupu k síti ve skupině zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. AzureAppService) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Další informace o použití značek služeb](../virtual-network/service-tags-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě související s vaším Azure Functions. Pomocí aliasů Azure Policy v oborech názvů Microsoft. Web a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich Azure Functions. Můžete také využít integrované definice zásad pro Azure Functions, například:

- CORS by neměl umožňovat každému prostředku přístup k aplikacím funkcí

- Aplikace Function app by měla být přístupná jen přes protokol HTTPS

- Ve vaší aplikaci Function app by se měla použít nejnovější verze TLS.

Plány Azure můžete použít také ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení přístupu na základě role Azure (Azure RBAC) a zásady v jediné definici podrobného plánu. V rámci správy verzí můžete snadno použít podrobný plán na nová předplatná, prostředí a vyladit řízení a správu.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: Pokud používáte skupinu zabezpečení sítě s implementací Azure Functions, použijte značky pro skupin zabezpečení sítě a další zdroje informací týkající se zabezpečení sítě a toku provozu. Pro jednotlivá pravidla skupiny zabezpečení sítě použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání a tak dále pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z vestavěných definic zásad Azure souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že se vytvoří všechny prostředky s značkami a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny nastavení sítě a prostředků souvisejících s nasazeními Azure Functions. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když dojde ke změně kritických nastavení sítě nebo prostředků. 

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro vaše prostředky Azure.

Azure Functions taky nabízí integrovanou integraci s Azure Application Insights k monitorování funkcí. Application Insights shromažďuje údaje o protokolech, výkonu a chybách. Automaticky detekuje anomálie ve výkonu a zahrnuje výkonné analytické nástroje, které vám pomůžou diagnostikovat problémy a pochopit, jak se vaše funkce používají.

Pokud máte integrované vlastní protokolování zabezpečení a auditu v rámci aplikace Function App, povolte nastavení diagnostiky "FunctionAppLogs" a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu úložiště Azure pro archivaci. 

Volitelně můžete povolit a začlenit data do Azure Sentinel nebo do řešení pro správu událostí a informací o systému třetích stran. 

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Jak nastavit Azure Functions s využitím Azure Application Insights](functions-monitoring.md)

- [Jak povolit nastavení diagnostiky (uživatelem vygenerované protokoly) pro Azure Functions](functions-monitor-log-analytics.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro vaše prostředky Azure.

Pokud máte integrované vlastní protokolování zabezpečení a auditu v rámci aplikace Function App, povolte nastavení diagnostiky "FunctionAppLogs" a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu úložiště Azure pro archivaci. 

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Jak povolit nastavení diagnostiky (uživatelem vygenerované protokoly) pro Azure Functions](functions-monitor-log-analytics.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro IaaS výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování protokolu pro pracovní prostory Log Analytics přidružené k vašim aplikacím funkcí podle předpisů pro dodržování předpisů vaší organizace.

- [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a také nastavení diagnostiky pro aplikaci Function App a odešlete protokoly do Log Analytics pracovního prostoru. Můžete provádět dotazy v Log Analytics k hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě shromážděných dat.

Povolte Application Insights pro aplikace Function App, abyste mohli shromažďovat data protokolů, výkonu a chyby. Data telemetrie shromážděná Application Insights můžete zobrazit v Azure Portal.

Pokud máte integrované vlastní protokolování zabezpečení a auditu v rámci aplikace Function App, povolte nastavení diagnostiky "FunctionAppLogs" a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu úložiště Azure pro archivaci. 

Volitelně můžete povolit a začlenit data do Azure Sentinel nebo do řešení pro správu událostí a informací o systému třetích stran.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Postup povolení nastavení diagnostiky pro Azure Functions](functions-monitor-log-analytics.md)

- [Jak nastavit Azure Functions s využitím Azure Application Insights a zobrazit data telemetrie](functions-monitoring.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a také nastavení diagnostiky pro aplikaci Function App a odešlete protokoly do Log Analytics pracovního prostoru. Můžete provádět dotazy v Log Analytics k hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě shromážděných dat. Můžete vytvářet výstrahy na základě vašich dotazů na pracovní prostor Log Analytics.

Povolte Application Insights pro aplikace Function App, abyste mohli shromažďovat data protokolů, výkonu a chyby. Data telemetrie shromážděná Application Insights můžete zobrazit a vytvořit upozornění v rámci Azure Portal.

Volitelně můžete povolit a začlenit data do Azure Sentinel nebo do řešení pro správu událostí a informací o systému třetích stran.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Postup povolení nastavení diagnostiky pro Azure Functions](functions-monitor-log-analytics.md)

- [Postup povolení Application Insights pro Azure Functions](./configure-monitoring.md#enable-application-insights-integration)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: přístup roviny ovládacího prvku k Azure Functions je řízen prostřednictvím Azure Active Directory (Azure AD). Azure AD nemá koncept výchozích hesel.

Přístup k rovině dat je možné ovládat několika způsoby, včetně autorizačních klíčů, omezení sítě a ověření identity Azure AD. Autorizační klíče používají klienti, kteří se připojují k Azure Functions koncovým bodům HTTP a dají se kdykoli znovu vygenerovat. Tyto klíče se ve výchozím nastavení generují pro nové koncové body HTTP.

Pro aplikace Function App je k dispozici více metod nasazení. některé z nich mohou využívat sadu generovaných přihlašovacích údajů. Zkontrolujte metody nasazení, které budou použity pro vaši aplikaci.

- [Zabezpečení koncového bodu HTTP](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Jak získat a znovu vygenerovat autorizační klíče](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Technologie nasazení v Azure Functions](functions-deployment-technologies.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrovaných zásad Azure, jako je například:

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.

- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

Další informace jsou k dispozici na odkazovaných odkazech.

- [Použití Azure Security Center k monitorování identity a přístupu ](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné** materiály: kdykoli je to možné, použijte službu Azure Active Directory (Azure AD) SSO, ale nekonfigurujte jednotlivé samostatné přihlašovací údaje pro přístup k datům do aplikace Function App. Použijte Azure Security Center doporučení pro správu identit a přístupu. K implementaci jednotného přihlašování pro aplikace Functions použijte funkci App Service ověřování/autorizace.

- [Pochopení ověřování a autorizace v Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

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

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: jako centrální ověřování a autorizační systém pro aplikace Function app použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak nakonfigurovat aplikaci Function App, aby používala přihlášení k Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: jako centrální ověřování a autorizační systém pro aplikace Function app použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Máte přístup ke zdrojům přihlašovacích aktivit, auditování a rizikových protokolů událostí Azure AD, které vám umožní integrovat s ověřováním Azure Sentinel nebo SIEM třetí strany.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy protokolu můžete nakonfigurovat v rámci Log Analytics.

- [Jak nakonfigurovat aplikaci Function App, aby používala přihlášení k Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Postup zprovoznění služby Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné** materiály: jako centrální ověřování a autorizační systém pro aplikace Function app použijte Azure Active Directory (Azure AD). V případě odchylky chování přihlášení k účtu na rovině ovládacího prvku (Azure Portal) použijte funkce Azure AD Identity Protection a detekce rizik ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

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

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Aplikace Function app by měly být odděleny virtuální sítí (VNet)/Subnet a odpovídajícím způsobem označeny.

K izolaci sítě můžete použít také soukromé koncové body. Privátní koncový bod Azure je síťové rozhraní, které se ke službě připojuje soukromě a bezpečně (například: Function App App HTTPs) využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu vaší virtuální sítě a tím vlastně přináší službu do vaší virtuální sítě. Pro funkce hostované v plánech Premium a App Service můžete použít soukromé koncové body.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Možnosti sítí Azure Functions](functions-networking-options.md)

- [Plán Azure Functions Premium](functions-premium-plan.md)

- [Principy privátního koncového bodu](../private-link/private-endpoint-overview.md)

- [Použití privátních koncových bodů pro Azure Functions](../app-service/networking/private-endpoint.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: v Azure Portal pro aplikace Function App klikněte v části funkce platformy: sítě: SSL na Povolit nastavení pouze https a nastavte minimální verzi TLS na 1,2.

- [Vyžadovat protokol HTTPS u aplikací Function App](./security-concepts.md#require-https)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: není aktuálně k dispozici; funkce pro identifikaci, klasifikaci a ochranu před únikem informací nejsou aktuálně k dispozici pro Azure Functions. Označení aplikací funkcí, které mohou zpracovávat citlivé informace jako takové a implementace řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: pomocí řízení přístupu na základě role Azure (Azure RBAC) můžete spravovat přístup k rovině ovládacích prvků aplikace Function app (Azure Portal).

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Functions a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: při vytváření aplikace Function App musíte vytvořit nebo propojit s účtem Azure Storage pro obecné účely, který podporuje objekty blob, Queue a Table Storage. Důvodem je to, že funkce spoléhají na Azure Storage pro operace, jako je Správa triggerů a spouštění funkcí protokolování. Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete poskytovat klíče spravované zákazníkem pro šifrování dat objektů BLOB a souborů. Aby aplikace Function App mohla získat přístup k účtu úložiště, musí se tyto klíče nacházet v Azure Key Vault.

- [Vysvětlení požadavků na úložiště pro Azure Functions](storage-considerations.md)

- [Vysvětlení šifrování služby Azure Storage pro neaktivní neaktivní data](../storage/common/storage-service-encryption.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na to, kdy změny probíhají v produkčních funkcích a také v jiných důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: pomocí DevSecOps praxe zajistíte, aby vaše aplikace Functions byly zabezpečené a zůstaly po celou dobu životního cyklu co nejbezpečnější. DevSecOps zahrnuje tým zabezpečení vaší organizace a jejich schopnosti v DevOps postupy, které umožňují zabezpečení na zodpovědnost všech členů týmu.

Kromě toho dodržujte doporučení od Azure Security Center, abyste pomohli zabezpečit aplikace Function App.

- [Postup přidání průběžného ověřování zabezpečení do kanálu CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Pokyny**: Společnost Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Azure Functions, ale můžete použít závažnost doporučení v rámci Azure Security Center a také bezpečné skóre pro měření rizika v rámci vašeho prostředí. Vaše zabezpečené skóre vychází z počtu Security Center doporučení, která jste zmírnili. Chcete-li určit prioritu doporučení pro řešení prvního, zvažte závažnost každého z nich.

- [Referenční příručka k doporučením zabezpečení](../security-center/recommendations-reference.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.). Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

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

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných zákazníka, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Další informace jsou k dispozici na odkazovaných odkazech.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: definování schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit ve vašich předplatných. 

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Další informace jsou k dispozici na odkazovaných odkazech.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

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

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro aplikaci Function app pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Web můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace aplikací Functions. Můžete také využít integrované definice zásad, například:

- Ve vaší aplikaci Function app by se měla použít spravovaná identita.

- Pro aplikace Function app by mělo být vypnuto vzdálené ladění.

- aplikace Function app by měla být přístupná jen přes protokol HTTPS

Další informace jsou k dispozici na odkazovaných odkazech.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: bezpečné ukládání a správu šablon ARM a vlastních definic zásad Azure ve správě zdrojového kódu.

- [Co je infrastruktura jako kód](/azure/devops/learn/what-is-infrastructure-as-code)

- [Zásady návrhu jako pracovní postupy kódu](../governance/policy/concepts/policy-as-code.md)

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Web k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Web k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro prostředky Azure použijte zásady Azure [audit], [Deny] a [nasazení, pokud neexistuje].

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: Používejte spravované identity společně s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace. Spravované identity umožňují vaší aplikaci Functions ověřování u jakékoli služby, která podporuje ověřování Azure Active Directory (Azure AD), včetně Key Vault, bez jakýchkoli přihlašovacích údajů v kódu.

- [Vytvoření Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Použití spravovaných identit pro App Service a Azure Functions](../app-service/overview-managed-identity.md)

- [Ověření Key Vault](../key-vault/general/authentication.md)

- [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

- [Použití Key Vault odkazů pro App Service a Azure Functions](../app-service/app-service-key-vault-references.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: použití spravovaných identit k poskytnutí aplikace Function App s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Použití spravovaných identit pro App Service a Azure Functions](../app-service/overview-managed-identity.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: k naplánování pravidelného zálohování aplikace použijte funkci zálohování a obnovení. Aplikace Function App běžící v plánu Premium mají stejné možnosti hostování jako webové aplikace v Azure App Service, což zahrnuje funkci zálohování a obnovení.

Pomocí řešení správy zdrojového kódu, jako je Azure Repos a Azure DevOps, můžete bezpečně ukládat a spravovat váš kód. Sada Azure DevOps Services využívá řadu funkcí úložiště Azure k zajištění dostupnosti dat v případě selhání hardwaru, přerušení služeb nebo havárie v oblasti. Tým Azure DevOps nadto dodržuje postupy, které chrání data před náhodným nebo nepřátelským odstraněním.

- [Zálohování aplikace v Azure](../app-service/manage-backup.md)

- [Pochopení dostupnosti dat v Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: k naplánování pravidelného zálohování aplikace použijte funkci zálohování a obnovení. Aplikace Function App běžící v plánu Premium mají stejné možnosti hostování jako webové aplikace v Azure App Service, což zahrnuje funkci zálohování a obnovení. Zálohování klíčů spravovaných zákazníkem v rámci Azure Key Vault.

Pomocí řešení správy zdrojového kódu, jako je Azure Repos a Azure DevOps, můžete bezpečně ukládat a spravovat váš kód. Sada Azure DevOps Services využívá řadu funkcí úložiště Azure k zajištění dostupnosti dat v případě selhání hardwaru, přerušení služeb nebo havárie v oblasti. Tým Azure DevOps nadto dodržuje postupy, které chrání data před náhodným nebo nepřátelským odstraněním.

- [Zálohování aplikace v Azure](../app-service/manage-backup.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Pochopení dostupnosti dat v Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: Zajistěte, aby bylo možné pravidelně provádět obnovení z funkce zálohování a obnovení. Pokud k zálohování kódu používáte jiné offline umístění, pravidelně zajistěte, aby bylo možné provádět úplné obnovení. Test obnovení zálohovaných klíčů spravovaných zákazníkem

- [Obnovení aplikace v Azure ze zálohy](../app-service/web-sites-restore.md)

- [Obnovení aplikace v Azure ze snímku](../app-service/app-service-web-restore-snapshots.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: zálohy z funkce zálohování a obnovení používají účet Azure Storage v předplatném. Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete poskytovat klíče spravované zákazníkem pro šifrování dat úložiště.

Pokud používáte spravované klíče zákazníka, ujistěte se, že Soft-Delete v Key Vault povolí ochranu klíčů proti náhodnému nebo škodlivému odstranění.

- [Šifrování neaktivních uložených dat ve službě Azure Storage](../storage/common/storage-service-encryption.md)

- [Postup povolení Soft-Delete v Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Průvodce zpracováním incidentů zabezpečení počítače s NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

**Odpovědnost:** Sdílené

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

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: pomocí funkce automatizace pracovních postupů v Azure Security Center můžete automaticky spouštět odpovědi na výstrahy a doporučení zabezpečení pomocí Logic Apps.

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

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)
