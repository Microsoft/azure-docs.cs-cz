---
title: Základní hodnoty zabezpečení Azure pro Azure Functions
description: Základní hodnoty zabezpečení Azure pro Azure Functions
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 993cd614f150866817e8d71dbd9dca9be606465f
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035151"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Základní hodnoty zabezpečení Azure pro Azure Functions

Základní plán zabezpečení Azure pro Azure Functions obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Doprovodné** materiály: Integrujte své aplikace Azure Functions do služby Azure Virtual Network. Aplikace Function App běžící v plánu Premium mají stejné možnosti hostování jako webové aplikace v Azure App Service, což zahrnuje funkci "Integration VNet".  Azure Virtual Networks umožňují umístit mnoho vašich prostředků Azure, například Azure Functions, do sítě směrovatelné v jiné síti než v Internetu.

- [Jak integrovat funkce do Azure Virtual Network](./functions-create-vnet.md)

- [Vysvětlení integrace virtuální sítě pro Azure Functions a Azure App Service](../app-service/web-sites-integrate-with-vnet.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Doprovodné** materiály: použijte Azure Security Center a sledujte doporučení pro ochranu sítě a zabezpečte síťové prostředky a síťové konfigurace týkající se vašich aplikací Azure Functions.

Pokud používáte skupiny zabezpečení sítě (skupin zabezpečení sítě) s implementací Azure Functions, povolte protokoly toku NSG a odešlete protokoly do Azure Storage účtu pro audity provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: k plnému zabezpečení vašich Azure Functionsch koncových bodů v produkčním prostředí byste měli zvážit implementaci jedné z následujících funkcí zabezpečení na úrovni aplikace.
- Zapněte App Service ověřování/autorizaci aplikace Function App,
- K ověřování požadavků použijte Azure API Management (APIM) nebo
- Nasaďte aplikaci Function App do Azure App Service Environment.

Kromě toho zajistěte, aby bylo vzdálené ladění pro produkční Azure Functions zakázané. Kromě toho sdílení prostředků mezi zdroji (CORS) by nemělo umožňovat všem doménám přístup k vaší aplikaci Function App v Azure. Povoluje interakci jenom požadovaných domén s vaší aplikací Function App.

Zvažte nasazení brány firewall webových aplikací (WAF) Azure jako součást konfigurace sítě pro další kontrolu příchozího provozu. Povolte nastavení diagnostiky pro protokoly WAF a ingestování do účtu úložiště, centra událostí nebo Log Analytics pracovního prostoru. 

- [Postup zabezpečení Azure Functionsch koncových bodů v produkčním prostředí](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Jak nasadit Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení DDoS Protection Standard ve virtuálních sítích přidružených k vašim aplikacím Functions pro ochranu před útoky DDoS. K odepření komunikace se známými škodlivými nebo nepoužívanými veřejnými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.
Kromě toho nakonfigurujte front-end bránu, jako je například firewall webových aplikací Azure, k ověřování všech příchozích požadavků a odfiltrování škodlivých přenosů. Firewall webových aplikací Azure vám může přispět k zabezpečení aplikace Function kontrolou příchozího webového provozu a zablokovat tak injektáže SQL, skriptování mezi weby, nakládku malwaru a DDoS útoky. Seznámení s WAF vyžaduje buď App Service Environment, nebo použití privátních koncových bodů (Preview). Před použitím v produkčních úlohách zajistěte, aby privátní koncové body již nejsou ve verzi Preview.

- [Možnosti sítí Azure Functions](./functions-networking-options.md)

- [Plán Azure Functions Premium](./functions-premium-plan.md)

- [Úvod do služby App Service Environment](../app-service/environment/intro.md)

- [Důležité aspekty sítí pro službu App Service Environment](../app-service/environment/network-info.md)

- [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

- [Pochopení Azure Security Center adaptivního posílení zabezpečení sítě](../security-center/security-center-adaptive-network-hardening.md)

- [Pochopení Azure Security Center k síťovému Access Control v čase](../security-center/security-center-just-in-time.md)

- [Použití privátních koncových bodů pro Azure Functions](../app-service/networking/private-endpoint.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Doprovodné** materiály: Pokud používáte skupiny zabezpečení sítě (skupin zabezpečení sítě) s implementací Azure Functions, povolte pro audit přenosu dat skupinu zabezpečení sítě a do účtu úložiště zasílejte protokoly. Protokoly toku můžete také odesílat do Log Analyticsho pracovního prostoru a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Postup povolení Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: konfigurace front-endové brány, jako je například firewall webových aplikací Azure pro ověřování všech příchozích požadavků a odfiltrování škodlivých přenosů. Firewall webových aplikací Azure vám může přispět k zabezpečení aplikací funkcí kontrolou příchozího webového provozu a zablokovat vkládání SQL, skriptování mezi weby, nakládku malwaru a DDoS útoky. Seznámení s WAF vyžaduje buď App Service Environment, nebo použití privátních koncových bodů (Preview). Před použitím v produkčních úlohách zajistěte, aby privátní koncové body již nejsou ve verzi Preview.

Alternativně existuje více možností Marketplace, jako je Barracuda WAF pro Azure, které jsou k dispozici na Azure Marketplace, které zahrnují funkce ID a IP adresy.

- [Možnosti sítí Azure Functions](./functions-networking-options.md)

- [Plán Azure Functions Premium](./functions-premium-plan.md)

- [Úvod do služby App Service Environment](../app-service/environment/intro.md)

- [Důležité aspekty sítí pro službu App Service Environment](../app-service/environment/network-info.md)

- [Vysvětlení brány firewall webových aplikací Azure](../web-application-firewall/index.yml)

- [Použití privátních koncových bodů pro Azure Functions](../app-service/networking/private-endpoint.md)

- [Principy cloudové služby Barracuda WAF](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Pokyny**: konfigurace front-endové brány pro vaši síť, například firewall webových aplikací Azure s koncovým šifrováním TLS. Seznámení s WAF vyžaduje buď App Service Environment, nebo použití privátních koncových bodů (Preview). Před použitím v produkčních úlohách zajistěte, aby privátní koncové body již nejsou ve verzi Preview.

- [Možnosti sítí Azure Functions](./functions-networking-options.md)

- [Plán Azure Functions Premium](./functions-premium-plan.md)

- [Úvod do služby App Service Environment](../app-service/environment/intro.md)

- [Důležité aspekty sítí pro službu App Service Environment](../app-service/environment/network-info.md)

- [Vysvětlení brány firewall webových aplikací Azure](../web-application-firewall/index.yml)

- [Jak nakonfigurovat kompletní protokol TLS pomocí Application Gateway s portálem](../application-gateway/end-to-end-ssl-portal.md)

- [Použití privátních koncových bodů pro Azure Functions](../app-service/networking/private-endpoint.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: použití značek Virtual Network služby k definování řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. AzureAppService) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Další informace o použití značek služeb](../virtual-network/service-tags-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě související s vaším Azure Functions. Pomocí aliasů Azure Policy v oborech názvů Microsoft. Web a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich Azure Functions. Můžete také využít integrované definice zásad pro Azure Functions, například:
- CORS by neměl umožňovat každému prostředku přístup k aplikacím funkcí
- Aplikace Function app by měla být přístupná jen přes protokol HTTPS
- Ve vaší aplikaci Function app by se měla použít nejnovější verze TLS.

Plány Azure můžete použít také ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení přístupu na základě role Azure (Azure RBAC) a zásady v jediné definici podrobného plánu. V rámci správy verzí můžete snadno použít podrobný plán na nová předplatná, prostředí a vyladit řízení a správu.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: Pokud používáte skupiny zabezpečení sítě (skupin zabezpečení sítě) s implementací Azure Functions, použijte značky pro skupin zabezpečení sítě a další zdroje informací související se zabezpečením a tokem provozu sítě. U individuálních pravidel NSG použijte pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

Použijte některou z vestavěných definic zásad Azure souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že se vytvoří všechny prostředky s značkami a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny nastavení sítě a prostředků souvisejících s nasazeními Azure Functions. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když dojde ke změně kritických nastavení sítě nebo prostředků. 

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Společnost Microsoft udržuje zdroj času používaný pro prostředky Azure, například Azure Functions pro časová razítka v protokolech.

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro vaše prostředky Azure.

Azure Functions taky nabízí integrovanou integraci s Azure Application Insights k monitorování funkcí. Application Insights shromažďuje údaje o protokolech, výkonu a chybách. Automaticky detekuje anomálie ve výkonu a zahrnuje výkonné analytické nástroje, které vám pomůžou diagnostikovat problémy a pochopit, jak se vaše funkce používají.

Pokud máte integrované vlastní protokolování zabezpečení a auditu v rámci aplikace Function App, povolte nastavení diagnostiky "FunctionAppLogs" a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu úložiště Azure pro archivaci. 

Volitelně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/platform/activity-log.md)

- [Jak nastavit Azure Functions s využitím Azure Application Insights](./functions-monitoring.md)

- [Jak povolit nastavení diagnostiky (uživatelem vygenerované protokoly) pro Azure Functions](./functions-monitor-log-analytics.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro vaše prostředky Azure.

Pokud máte integrované vlastní protokolování zabezpečení a auditu v rámci aplikace Function App, povolte nastavení diagnostiky "FunctionAppLogs" a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu úložiště Azure pro archivaci. 

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/platform/activity-log.md)

- [Jak povolit nastavení diagnostiky (uživatelem vygenerované protokoly) pro Azure Functions](./functions-monitor-log-analytics.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování protokolu pro pracovní prostory Log Analytics přidružené k vašim aplikacím funkcí podle předpisů pro dodržování předpisů vaší organizace.

- [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a také nastavení diagnostiky pro aplikaci Function App a odešlete protokoly do Log Analytics pracovního prostoru. Můžete provádět dotazy v Log Analytics k hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě shromážděných dat.

Povolte Application Insights pro aplikace Function App, abyste mohli shromažďovat data protokolů, výkonu a chyby. Data telemetrie shromážděná Application Insights můžete zobrazit v Azure Portal.

Pokud máte integrované vlastní protokolování zabezpečení a auditu v rámci aplikace Function App, povolte nastavení diagnostiky "FunctionAppLogs" a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu úložiště Azure pro archivaci. 

Volitelně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/platform/activity-log.md)

- [Postup povolení nastavení diagnostiky pro Azure Functions](./functions-monitor-log-analytics.md)

- [Jak nastavit Azure Functions s využitím Azure Application Insights a zobrazit data telemetrie](./functions-monitoring.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a také nastavení diagnostiky pro aplikaci Function App a odešlete protokoly do Log Analytics pracovního prostoru. Můžete provádět dotazy v Log Analytics k hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě shromážděných dat. Můžete vytvářet výstrahy na základě vašich dotazů na pracovní prostor Log Analytics.

Povolte Application Insights pro aplikace Function App, abyste mohli shromažďovat data protokolů, výkonu a chyby. Data telemetrie shromážděná Application Insights můžete zobrazit a vytvořit upozornění v rámci Azure Portal.

Volitelně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/platform/activity-log.md)

- [Postup povolení nastavení diagnostiky pro Azure Functions](./functions-monitor-log-analytics.md)

- [Postup povolení Application Insights pro Azure Functions](./configure-monitoring.md#enable-application-insights-integration)

- [Vytvoření upozornění v rámci Azure](../azure-monitor/learn/tutorial-response.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: nepoužitelné; aplikace Function App nezpracovávají ani nevytváří protokoly související s malwarem.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: nepoužitelné; aplikace Function App nezpracovávají ani nevytváří protokoly související s DNS, které jsou přístupné pro uživatele.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu. 

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: přístup roviny ovládacího prvku k Azure Functions je řízen prostřednictvím Azure Active Directory (AD). Azure AD nemá koncept výchozích hesel.

Přístup k rovině dat je možné ovládat několika způsoby, včetně autorizačních klíčů, omezení sítě a ověření identity Azure AD. Autorizační klíče používají klienti, kteří se připojují k Azure Functions koncovým bodům HTTP a dají se kdykoli znovu vygenerovat. Tyto klíče se ve výchozím nastavení generují pro nové koncové body HTTP.

Pro aplikace Function App je k dispozici více metod nasazení. některé z nich mohou využívat sadu generovaných přihlašovacích údajů. Zkontrolujte metody nasazení, které budou použity pro vaši aplikaci.

- [Zabezpečení koncového bodu HTTP](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Jak získat a znovu vygenerovat autorizační klíče](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Technologie nasazení v Azure Functions](./functions-deployment-technologies.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho, abyste mohli sledovat vyhrazené účty pro správu, můžete použít doporučení z Azure Security Center nebo předdefinovaných zásad Azure, jako je: k předplatnému by měli být přiřazeni více vlastníků, jejichž účty mají oprávnění vlastníka, a proto byste měli z předplatného odebrat oprávnění vlastníka.

- [Použití Azure Security Center k monitorování identity a přístupu (Preview)](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné** materiály: kdykoli je to možné, použijte Azure Active Directory jednotného přihlašování (SSO), než nakonfigurujete individuální samostatná pověření pro přístup k datům do aplikace Function App. Použijte Azure Security Center doporučení pro správu identit a přístupu. K implementaci jednotného přihlašování pro aplikace Functions použijte funkci App Service ověřování/autorizace.

- [Pochopení ověřování a autorizace v Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte Azure Active Directory (AD) Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným Multi-Factor Authentication (MFA), které jsou nakonfigurovány pro přihlášení a konfiguraci prostředků Azure.

- [Další informace o pracovních stanicích s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Pokyny**: pomocí Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) můžete generovat protokoly a výstrahy, když dojde k podezřelé nebo nebezpečné aktivitě v prostředí.

Navíc můžete pomocí zjišťování rizik Azure AD zobrazovat výstrahy a sestavy týkající se rizikového chování uživatelů.

- [Postup nasazení Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Vysvětlení zjišťování rizik Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup k Azure Portal jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: jako centrální ověřování a autorizační systém pro aplikace Function app použijte Azure Active Directory (AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak nakonfigurovat aplikaci Function App, aby používala přihlášení k Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. 

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Doprovodné** materiály: jako centrální ověřování a autorizační systém pro aplikace Function app použijte Azure Active Directory (AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Máte přístup ke zdrojům přihlašovacích aktivit, auditování a rizikových protokolů událostí Azure AD, které vám umožní integrovat s ověřováním Azure Sentinel nebo SIEM třetí strany.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy protokolu můžete nakonfigurovat v rámci Log Analytics.

- [Jak nakonfigurovat aplikaci Function App, aby používala přihlášení k Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Postup zprovoznění služby Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné** materiály: jako centrální ověřování a autorizační systém pro aplikace Function app použijte Azure Active Directory (AD). V případě odchylky chování přihlášení k účtu na rovině ovládacího prvku (Azure Portal) použijte funkce Azure Active Directory (AD) Identity Protection a detekce rizik ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce související s identitami uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: není aktuálně k dispozici; Customer Lockbox se v tuto chvíli nepodporuje pro Azure Functions.

- [Seznam služeb podporovaných Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. aplikace Function app by měly být odděleny virtuální sítí (VNet)/Subnet a odpovídajícím způsobem označeny.

K izolaci sítě můžete použít také soukromé koncové body. Privátní koncový bod Azure je síťové rozhraní, které se ke službě připojuje soukromě a bezpečně (například: Function App App HTTPs) využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu vaší virtuální sítě a tím vlastně přináší službu do vaší virtuální sítě. Soukromé koncové body jsou v (Preview) pro aplikace Function App spuštěné v plánu Premium. Před použitím v produkčních úlohách zajistěte, aby privátní koncové body již nejsou ve verzi Preview.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Možnosti sítí Azure Functions](./functions-networking-options.md)

- [Plán Azure Functions Premium](./functions-premium-plan.md)

- [Principy privátního koncového bodu](../private-link/private-endpoint-overview.md)

- [Použití privátních koncových bodů pro Azure Functions](../app-service/networking/private-endpoint.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: nasaďte automatizovaný nástroj na hraničních sítích, které monitorují neoprávněný přenos citlivých informací a zablokují tyto přenosy, a upozorní odborníky na zabezpečení informací. 

Společnost Microsoft spravuje základní infrastrukturu pro Azure Functions a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost**: netýká se

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: v Azure Portal pro aplikace Function App klikněte v části funkce platformy: sítě: SSL na Povolit nastavení pouze https a nastavte minimální verzi TLS na 1,2.

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: není aktuálně k dispozici; funkce pro identifikaci, klasifikaci a ochranu před únikem informací nejsou aktuálně k dispozici pro Azure Functions. Označení aplikací funkcí, které mohou zpracovávat citlivé informace jako takové a implementace řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: použijte řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k rovině ovládacích prvků aplikace Function app (Azure Portal). 

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Functions a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: při vytváření aplikace Function App musíte vytvořit nebo propojit s účtem Azure Storage pro obecné účely, který podporuje objekty blob, Queue a Table Storage. Důvodem je to, že funkce spoléhají na Azure Storage pro operace, jako je Správa triggerů a spouštění funkcí protokolování. Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete poskytovat klíče spravované zákazníkem pro šifrování dat objektů BLOB a souborů. Aby aplikace Function App mohla získat přístup k účtu úložiště, musí se tyto klíče nacházet v Azure Key Vault.

- [Vysvětlení požadavků na úložiště pro Azure Functions](./storage-considerations.md)

- [Vysvětlení šifrování služby Azure Storage pro neaktivní neaktivní data](../storage/common/storage-service-encryption.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na to, kdy změny probíhají v produkčních funkcích a také v jiných důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: pomocí DevSecOps praxe zajistíte, aby vaše aplikace Functions byly zabezpečené a zůstaly po celou dobu životního cyklu co nejbezpečnější. DevSecOps zahrnuje tým zabezpečení vaší organizace a jejich schopnosti v DevOps postupy, které umožňují zabezpečení na zodpovědnost všech členů týmu.

Kromě toho dodržujte doporučení od Azure Security Center, abyste pomohli zabezpečit aplikace Function App.

- [Postup přidání průběžného ověřování zabezpečení do kanálu CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Pokyny**: Společnost Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Azure Functions, ale můžete použít závažnost doporučení v rámci Azure Security Center a také bezpečné skóre pro měření rizika v rámci vašeho prostředí. Vaše zabezpečené skóre vychází z počtu Security Center doporučení, která jste zmírnili. Chcete-li určit prioritu doporučení pro řešení prvního, zvažte závažnost každého z nich.

- [Referenční příručka k doporučením zabezpečení](../security-center/recommendations-reference.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat a zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.).  Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků Azure. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad: nepovolené typy prostředků povolené typy prostředků

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: udržování inventáře schválených prostředků Azure a softwarových titulů

**Pokyny**: definování schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit ve vašich předplatných. 

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad: nepovolené typy prostředků povolené typy prostředků

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: Omezte schopnost uživatelů pracovat se správcem prostředků Azure prostřednictvím skriptů

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: pro aplikace citlivých nebo vysoce rizikových funkcí implementujte samostatné odběry nebo skupiny pro správu, které zajišťují izolaci.

Nasaďte aplikace s vysokým rizikem do svých vlastních Virtual Network (VNet). Zabezpečení hraničních aplikací pro aplikace Function App se dosahuje prostřednictvím virtuální sítě. Funkce spuštěné v plánu Premium nebo App Service Environment (pomocného programu) se dají integrovat s virtuální sítě. Vyberte nejlepší architekturu pro váš případ použití.

- [Možnosti sítí Azure Functions](./functions-networking-options.md)

- [Plán Azure Functions Premium](./functions-premium-plan.md)

- [Důležité aspekty sítí pro službu App Service Environment](../app-service/environment/network-info.md)

- [Jak vytvořit externí pomocný objekt pro vytváření](../app-service/environment/create-external-ase.md)

Postup vytvoření interního pomocného mechanismu řízení:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro aplikaci Function app pomocí Azure Policy. Použijte aliasy Azure Policy v oboru názvů Microsoft. Web k vytváření vlastních zásad pro auditování nebo prosazování konfigurace aplikací Function App. Můžete také využít integrované definice zásad, například:
- Ve vaší aplikaci Function app by se měla použít spravovaná identita.
- Pro aplikace Function app by mělo být vypnuto vzdálené ladění.
- Aplikace Function app by měla být přístupná jen přes protokol HTTPS

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; i když je možné nasadit místní funkce, je tato směrnice určena pro IaaS výpočetní prostředky. Při nasazování místních funkcí zodpovídáte za zabezpečenou konfiguraci vašeho prostředí.

- [Pochopení místních funkcí](./functions-runtime-install.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: bezpečné ukládání a správu šablon ARM a vlastních definic zásad Azure ve správě zdrojového kódu.

- [Co je infrastruktura jako kód](/azure/devops/learn/what-is-infrastructure-as-code)

- [Zásady návrhu jako pracovní postupy kódu](../governance/policy/concepts/policy-as-code.md)

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Web k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Web k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro prostředky Azure použijte zásady Azure [audit], [Deny] a [nasazení, pokud neexistuje].

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro IaaS výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: Používejte spravované identity společně s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace. Spravované identity umožňují vaší aplikaci Functions ověřování u jakékoli služby, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Vytvoření Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Použití spravovaných identit pro App Service a Azure Functions](../app-service/overview-managed-identity.md)

* [Ověření Key Vault](../key-vault/general/authentication.md)

* [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

- [Použití Key Vault odkazů pro App Service a Azure Functions](../app-service/app-service-key-vault-references.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: použití spravovaných identit k poskytnutí aplikace Function App s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Použití spravovaných identit pro App Service a Azure Functions](../app-service/overview-managed-identity.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro IaaS výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Functions), ale neběží na zákaznickém obsahu.

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro nevýpočetní prostředky, které slouží k ukládání dat.


**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro nevýpočetní prostředky, které slouží k ukládání dat.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Functions), ale neběží na zákaznickém obsahu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: k naplánování pravidelného zálohování aplikace použijte funkci zálohování a obnovení. Aplikace Function App běžící v plánu Premium mají stejné možnosti hostování jako webové aplikace v Azure App Service, což zahrnuje funkci zálohování a obnovení.

Pomocí řešení správy zdrojového kódu, jako je Azure Repos a Azure DevOps, můžete bezpečně ukládat a spravovat váš kód. Sada Azure DevOps Services využívá řadu funkcí úložiště Azure k zajištění dostupnosti dat v případě selhání hardwaru, přerušení služeb nebo havárie v oblasti. Tým Azure DevOps nadto dodržuje postupy, které chrání data před náhodným nebo nepřátelským odstraněním.

- [Zálohování aplikace v Azure](../app-service/manage-backup.md)

- [Pochopení dostupnosti dat v Azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Pokyny**: k naplánování pravidelného zálohování aplikace použijte funkci zálohování a obnovení. Aplikace Function App běžící v plánu Premium mají stejné možnosti hostování jako webové aplikace v Azure App Service, což zahrnuje funkci zálohování a obnovení. Zálohujte spravované klíče zákazníka v rámci Azure Key Vault.

Pomocí řešení správy zdrojového kódu, jako je Azure Repos a Azure DevOps, můžete bezpečně ukládat a spravovat váš kód. Sada Azure DevOps Services využívá řadu funkcí úložiště Azure k zajištění dostupnosti dat v případě selhání hardwaru, přerušení služeb nebo havárie v oblasti. Tým Azure DevOps nadto dodržuje postupy, které chrání data před náhodným nebo nepřátelským odstraněním.

- [Zálohování aplikace v Azure](../app-service/manage-backup.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Pochopení dostupnosti dat v Azure DevOps](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Doprovodné** materiály: Zajistěte, aby bylo možné pravidelně provádět obnovení z funkce zálohování a obnovení. Pokud k zálohování kódu používáte jiné offline umístění, pravidelně zajistěte, aby bylo možné provádět úplné obnovení. Test obnovení zálohovaných spravovaných klíčů zákazníka

- [Obnovení aplikace v Azure ze zálohy](../app-service/web-sites-restore.md)

- [Obnovení aplikace v Azure ze snímku](../app-service/app-service-web-restore-snapshots.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Pokyny**: zálohy z funkce zálohování a obnovení používají účet Azure Storage v předplatném. Azure Storage šifruje všechna data v účtu úložiště v klidovém umístění. Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete poskytovat klíče spravované zákazníkem pro šifrování dat úložiště.

Pokud používáte spravované klíče zákazníka, ujistěte se, že Soft-Delete v Key Vault povolí ochranu klíčů proti náhodnému nebo škodlivému odstranění.

- [Šifrování neaktivních uložených dat ve službě Azure Storage](../storage/common/storage-service-encryption.md)

- [Postup povolení Soft-Delete v Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: pomocí funkce automatizace pracovních postupů v Azure Security Center můžete automaticky spouštět odpovědi na výstrahy a doporučení zabezpečení pomocí Logic Apps.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: řiďte se pravidly společnosti Microsoft o zapojení, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii a provádění testování na základě červeného týmového seskupování a živého průniku na cloudové infrastruktuře, služby a aplikace spravované společností Microsoft.

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)
