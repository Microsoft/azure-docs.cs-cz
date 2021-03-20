---
title: Základní hodnoty zabezpečení Azure pro API Management
description: Základní API Management zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: api-management
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7c5fbbd8cdfd75819bffcfb93fc81900feb4a612
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101652364"
---
# <a name="azure-security-baseline-for-api-management"></a>Základní hodnoty zabezpečení Azure pro API Management

Tato základní hodnota zabezpečení platí pro API Management pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na API Management. **Ovládací prvky** , které se nevztahují k API Management byly vyloučeny.

 
Pokud chcete zjistit, jak API Management kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot API Management Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: Azure API Management lze nasadit v rámci Azure Virtual Network (VNET), aby mohl přistupovat k back-end službám v síti. Portál pro vývojáře a služba API Management Gateway je možné nakonfigurovat tak, aby byly přístupné buď z Internetu (externí), nebo jenom v rámci virtuální sítě (interní).

- Externí: API Management brána a portál pro vývojáře jsou přístupné z veřejného Internetu prostřednictvím externího nástroje pro vyrovnávání zatížení. Brána má přístup k prostředkům v rámci virtuální sítě.

- Interní: API Management brána a portál pro vývojáře jsou přístupné jenom z virtuální sítě prostřednictvím interního nástroje pro vyrovnávání zatížení. Brána má přístup k prostředkům v rámci virtuální sítě.

Příchozí a odchozí provoz do podsítě, ve které je nasazená API Management, se dá řídit pomocí skupiny zabezpečení sítě.

- [Použití služby Azure API Management s virtuálními sítěmi](api-management-using-with-vnet.md)

- [Použití služby Azure API Management s interní virtuální sítí](api-management-using-with-internal-vnet.md)

- [Integrace služby API Management v interní virtuální síti se službou Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: příchozí a odchozí provoz do podsítě, ve které API Management nasadit, se dají řídit pomocí skupin zabezpečení sítě (skupin zabezpečení sítě). Nasaďte NSG do podsítě API Management a povolte protokoly toku NSG a odešlete protokoly do účtu Azure Storage pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Upozornění: při konfiguraci NSG v podsíti API Management existuje sada portů, které je potřeba otevřít. Pokud některý z těchto portů není dostupný, API Management nemusí správně fungovat a může být nepřístupný.

- [Principy konfigurací NSG pro Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Pokyny**: k ochraně důležitých webových a HTTP rozhraní api nakonfigurujte API Management v rámci Virtual Network (VNET) v interním režimu a nakonfigurujte Application Gateway Azure. Application Gateway je služba PaaS. Funguje jako reverzní proxy server a poskytuje vyrovnávání zatížení L7, směrování, Firewall webových aplikací (WAF) a další služby.

Kombinování API Management zřízené v interní virtuální síti s Application Gateway front-endu umožňuje následující scénáře:
- Pomocí jednoho API Management prostředku můžete vystavit všechna rozhraní API interním i externím zákazníkům.
- K vystavení podmnožiny rozhraní API externím příjemcům použijte jeden API Management prostředek.
- Poskytněte možnost přepínat a vypínat přístup k API Management z veřejného Internetu.

Poznámka: Tato funkce je dostupná na úrovních API Management úrovně Premium a Developer.

- [Jak integrovat API Management do interní virtuální sítě s Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Pochopení Application Gateway Azure](/azure/application-gateway/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: Konfigurace API Management v rámci Virtual Network (VNET) v interním režimu a konfigurace Application Gateway Azure. Application Gateway je služba PaaS. Funguje jako reverzní proxy server a poskytuje vyrovnávání zatížení L7, směrování, Firewall webových aplikací (WAF) a další služby.

Kombinování API Management zřízené v interní virtuální síti s Application Gateway front-endu umožňuje následující scénáře:
- Pomocí jednoho API Management prostředku můžete vystavit všechna rozhraní API interním i externím zákazníkům.
- K vystavení podmnožiny rozhraní API externím příjemcům použijte jeden API Management prostředek.
- Poskytněte možnost přepínat a vypínat přístup k API Management z veřejného Internetu.

Poznámka: Tato funkce je dostupná na úrovních API Management úrovně Premium a Developer.

K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

- [Jak integrovat API Management do interní virtuální sítě s Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Pochopení Application Gateway Azure](/azure/application-gateway/)

- [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: příchozí a odchozí provoz do podsítě, ve které API Management nasadit, se dají řídit pomocí skupin zabezpečení sítě (NSG). Nasaďte NSG do podsítě API Management a povolte protokoly toku NSG a odešlete protokoly do účtu Azure Storage pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

Upozornění: při konfiguraci NSG v podsíti API Management existuje sada portů, které je potřeba otevřít. Pokud některý z těchto portů není dostupný, API Management nemusí správně fungovat a může být nepřístupný.

- [Principy konfigurací NSG pro Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: Konfigurace API Management v rámci Virtual Network (VNET) v interním režimu a konfigurace Application Gateway Azure. Application Gateway je služba PaaS. Funguje jako reverzní proxy server a poskytuje vyrovnávání zatížení L7, směrování, Firewall webových aplikací (WAF) a další služby. Application Gateway WAF zajišťuje ochranu před běžnými zneužitími a ohroženími zabezpečení a může běžet v následujících dvou režimech:
- Detekční režim: monitoruje a protokoluje všechny výstrahy hrozeb. Diagnostiku protokolování pro Application Gateway můžete zapnout v části Diagnostika. Musíte se ujistit, že je vybraný a zapnutý protokol WAF. Firewall webových aplikací neblokuje příchozí požadavky, když pracuje v režimu detekce.
- Režim prevence: blokuje vniknutí a útoky, které pravidla zjišťují. Útočník obdrží výjimku "403 neoprávněný přístup" a připojení je zavřené. Režim prevence zaznamenává takové útoky do protokolů WAF.

Kombinování API Management zřízené v interní virtuální síti s Application Gateway front-endu umožňuje následující scénáře:
- Pomocí jednoho API Management prostředku můžete vystavit všechna rozhraní API interním i externím zákazníkům.
- K vystavení podmnožiny rozhraní API externím příjemcům použijte jeden API Management prostředek.
- Poskytněte možnost přepínat a vypínat přístup k API Management z veřejného Internetu.

Poznámka: Tato funkce je dostupná na úrovních API Management úrovně Premium a Developer.

- [Jak integrovat API Management do interní virtuální sítě s Application Gateway](api-management-howto-integrate-internal-vnet-appgateway.md)

- [Vysvětlení Azure Application Gateway WAF](../web-application-firewall/ag/ag-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: ke správě provozu do rozhraní API web/http nasaďte API Management do Virtual Network (VNET) přidružených k App Service Environment v externím nebo interním režimu.

V interním režimu nakonfigurujte Application Gateway Azure před API Management. Application Gateway je služba PaaS. Funguje jako reverzní proxy server a poskytuje vyrovnávání zatížení L7, směrování, Firewall webových aplikací (WAF) a další služby. Application Gateway WAF zajišťuje ochranu před běžným zneužitím a ohrožením zabezpečení.

Kombinování API Management zřízené v interní virtuální síti s Application Gateway front-endu umožňuje následující scénáře:
- Pomocí jednoho API Management prostředku můžete vystavit všechna rozhraní API interním i externím zákazníkům.
- K vystavení podmnožiny rozhraní API externím příjemcům použijte jeden API Management prostředek.
- Poskytněte možnost přepínat a vypínat přístup k API Management z veřejného Internetu.

Poznámka: Tato funkce je dostupná na úrovních API Management úrovně Premium a Developer.

- [Vystavení privátních rozhraní API externím příjemcům](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

- [Jak používat API Management v rámci virtuální sítě](api-management-using-with-vnet.md)

- [Firewall webových aplikací Azure v Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Pochopení Application Gateway Azure](/azure/application-gateway/overview)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služby Virtual Network (VNET) definujte řízení přístupu k síti u skupin zabezpečení sítě (skupin zabezpečení sítě) používaných ve vašich API Managementch podsítích. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Upozornění: při konfiguraci NSG v podsíti API Management existuje sada portů, které je potřeba otevřít. Pokud některý z těchto portů není dostupný, API Management nemusí správně fungovat a může být nepřístupný.

- [Porozumění a používání značek služeb](../virtual-network/service-tags-overview.md)

- [Porty vyžadované pro API Management](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě související s nasazeními Azure API Management. Pomocí aliasů Azure Policy v oborech názvů Microsoft. ApiManagement a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě pro nasazení API Management Azure a souvisejících prostředků.

Plány Azure můžete použít také ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení přístupu na základě role Azure (Azure RBAC) a zásady v jediné definici podrobného plánu. V rámci správy verzí můžete snadno použít podrobný plán na nová předplatná, prostředí a vyladit řízení a správu.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: používejte značky pro skupiny zabezpečení sítě (skupin zabezpečení sítě) a další zdroje informací týkající se zabezpečení sítě a toku provozu. Pro jednotlivá pravidla NSG můžete použít pole Popis k zadání obchodních potřeb a/nebo doby trvání (atd.) pro všechna pravidla, která umožňují provoz do/ze sítě.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků přidružených k nasazením Azure API Management. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

- [Vytváření výstrah v Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: v rámci Azure monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz, posílání protokolů do Azure Storage pro dlouhodobé nebo archivní úložiště nebo offline analýzu nebo pro export protokolů do jiného analytického řešení v Azure a jinde pomocí Azure Event Hubs. Služba Azure API Management ve výchozím nastavení ukládá protokoly a metriky do Azure Monitor. Úroveň podrobností protokolování se dá nakonfigurovat na úrovni služby a rozhraní API pro jednotlivé služby.

Kromě Azure Monitor může být služba Azure API Management integrovaná s jednou nebo několika službami Azure Application Insights. Nastavení protokolování pro Application Insights je možné nakonfigurovat pro jednotlivé služby nebo rozhraní API.

Volitelně můžete povolit a začlenit data do Azure Sentinel nebo incidentu zabezpečení a správu událostí třetích stran (SIEM).

- [Postup konfigurace nastavení diagnostiky](/azure/azure-monitor/platform/diagnostic-settings#create-in-azure-portal)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Jak začít s Azure Monitor a integrací SIEM třetích stran](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Jak vytvořit vlastní kanál pro protokolování a analýzu](api-management-howto-log-event-hubs.md)

- [Postup při integraci s Azure Application Insights](api-management-howto-app-insights.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: v případě protokolování auditu řídicí roviny povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly aktivit do Log Analytics pracovního prostoru pro účely vytváření sestav a analýzy, Azure Storage pro dlouhodobou úschovu do Azure Event Hubs pro export v jiných analytických řešeních v Azure a jinde. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni řídicích roviny služby Azure API Management.

V případě protokolování auditu roviny dat poskytují diagnostické protokoly podrobné informace o operacích a chybách, které jsou důležité pro auditování, a také pro účely řešení potíží. Diagnostické protokoly se liší od protokolů aktivit. Protokoly aktivit poskytují přehled o operacích provedených ve vašich prostředcích Azure. Diagnostické protokoly poskytují přehled o operacích, které provedly vaše prostředky.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](/azure/azure-monitor/platform/activity-log)

- [Postup povolení nastavení diagnostiky pro Azure API Management](/Azure/api-management/api-management-howto-use-azure-monitor#resource-logs)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

- [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Postup archivace protokolů na účet Azure Storage](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: Azure API Management průběžně generuje protokoly a metriky do Azure monitor a poskytuje téměř v reálném čase přehled o stavu vašich rozhraní API. Díky pracovním prostorům Azure Monitor a Log Analytics můžete zkontrolovat, dotazovat, vizualizovat, směrovat, archivovat, konfigurovat výstrahy a provádět akce s metrikami a protokoly přicházející ze API Management a souvisejících prostředků. Analyzujte a monitorujte protokoly pro chování neobvyklé a pravidelně kontrolujte výsledky.

Volitelně můžete integrovat API Management s Azure Application Insights a použít ji jako primární nebo sekundární nástroj pro sledování, trasování, generování sestav a upozorňování.

- [Jak monitorovat a prohlížet protokoly pro Azure API Management](/Azure/api-management/api-management-howto-use-azure-monitor)

- [Jak provádět vlastní dotazy v Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

- [Principy Log Analyticsho pracovního prostoru](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Postup při integraci s Azure Application Insights](api-management-howto-app-insights.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a také nastavení diagnostiky pro instance služby Azure API Management a odešlete protokoly do pracovního prostoru Log Analytics. Můžete provádět dotazy v Log Analytics k hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě shromážděných dat. Můžete vytvářet výstrahy na základě vašich dotazů na pracovní prostor Log Analytics.

Vytvořte výstrahy metriky, abyste měli jistotu, že se děje něco neočekávaného. Můžete například dostávat oznámení, když vaše instance Azure API Management překročila očekávanou kapacitu špičky v určitém časovém období, nebo pokud se v předdefinovaném časovém intervalu vyskytl určitý počet neautorizovaných požadavků brány nebo chyb.

Volitelně můžete integrovat API Management s Azure Application Insights a použít ji jako primární nebo sekundární nástroj pro sledování, trasování, generování sestav a upozorňování.

Volitelně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](/azure/azure-monitor/platform/activity-log)

- [Postup povolení nastavení diagnostiky pro Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#resource-logs)

- [Jak nakonfigurovat pravidlo výstrahy pro Azure API Management](/Azure/api-management/api-management-howto-use-azure-monitor#set-up-an-alert-rule-for-unauthorized-request)

- [Jak zobrazit metriky kapacity instance služby Azure API Management](api-management-capacity.md)

- [Postup při integraci s Azure Application Insights](api-management-howto-app-insights.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: udržování inventáře účtů, které mají přístup pro správu k rovině řízení API Management Azure (Azure Portal).

Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a jsou Queryable. API Management spoléhá na tyto role a Role-Based Access Control pro zajištění jemně odstupňované správy přístupu pro API Management služby a entity.

Kromě toho API Management obsahuje integrovanou skupinu Administrators v uživatelském systému API Management. Skupiny v API Management ovládají viditelnost rozhraní API na portálu pro vývojáře a členové skupiny Administrators uvidí všechna rozhraní API.

Pro správu a údržbu účtů pro správu použijte doporučení od Azure Security Center.

- [Použití řízení přístupu na základě role ve službě API Management](api-management-role-based-access-control.md)

- [Jak získat seznam uživatelů v rámci instance služby Azure API Management](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Jak získat seznam uživatelů přiřazených k roli adresáře v Azure AD pomocí PowerShellu](/powershell/module/az.resources/get-azroleassignment)

- [Jak získat definici role adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/az.resources/get-azroledefinition)

- [Pochopení doporučení identity a přístupu z Azure Security Center](https://docs.microsoft.com/azure/security-center/recommendations-reference#identityandaccess-recommendations)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: Azure API Management nemá koncept výchozích hesel/klíčů.

Předplatná Azure API Management, která jsou jedním ze způsobů zabezpečení přístupu k rozhraním API, ale přidávají dvojici generovaných klíčů předplatného. Zákazníci můžou tyto klíče předplatného kdykoli znovu vygenerovat.

- [Principy předplatných Azure API Management](api-management-subscriptions.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrovaných zásad Azure, jako je například:
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

- [Použití Azure Security Center k monitorování identity a přístupu (Preview)](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Pokyny**: Azure API Management lze nakonfigurovat tak, aby využil Azure Active Directory (Azure AD) jako zprostředkovatele identity pro ověřování uživatelů na portálu pro vývojáře, aby bylo možné využívat možnosti jednotného přihlašování nabízené službou Azure AD. Po nakonfigurování se noví uživatelé portálu pro vývojáře můžou rozhodnout, že budou postupovat předem při prvním ověření prostřednictvím služby Azure AD a pak na portálu po ověření dokončí proces registrace.

- [Autorizace vývojářských účtů pomocí Azure AD v Azure API Management](api-management-howto-aad.md)

Alternativně lze proces přihlášení nebo přihlášení dále přizpůsobit prostřednictvím delegování. Delegování vám umožňuje používat stávající web ke zpracování přihlašování a předplatného pro vývojáře k produktům, a to na rozdíl od používání integrované funkce portálu pro vývojáře. Umožňuje vašemu webu vlastnit uživatelská data a provádět ověřování těchto kroků vlastním způsobem.

- [Delegování registrace uživatele a předplatného produktu](api-management-howto-setup-delegation.md)

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

- [Vysvětlení zjišťování rizik Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup k Azure Portal jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: kdykoli je to možné, použijte Azure Active Directory (Azure AD) jako centrální ověřování a systém autorizací. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Nakonfigurujte portál pro vývojáře Azure API Management pro ověřování vývojářských účtů pomocí Azure AD.

Nakonfigurujte instanci Azure API Management tak, aby chránila vaše rozhraní API pomocí protokolu OAuth 2,0 s Azure AD.

- [Jak autorizovat vývojářské účty pomocí Azure AD v Azure API Management](api-management-howto-aad.md)

- [Jak chránit rozhraní API pomocí OAuth 2,0 s Azure AD a API Management](api-management-howto-protect-backend-with-aad.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Zákazníci můžou využít kontroly přístupu Azure identity k efektivní správě členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít k dispozici jenom oprávnění uživatelé s odpovídajícím přístupem.

Zákazníci můžou udržovat inventář API Managementch uživatelských účtů a podle potřeby sjednotit přístup. V API Management jsou vývojáři příjemci rozhraní API, které jsou vystavené s API Management. Ve výchozím nastavení jsou nově vytvořené účty pro vývojáře aktivní a přidruženy ke skupině Developers. Účty pro vývojáře, které jsou v aktivním stavu, se dají použít pro přístup ke všem rozhraním API, pro která mají předplatná.

Správci můžou vytvářet vlastní skupiny nebo využívat externí skupiny v přidružených klientech Azure AD. Vlastní a externí skupiny můžete používat společně se systémovými skupinami, a nastavovat tak vývojářům viditelnost produktů s rozhraním API a přístup k nim.

- [Správa uživatelských účtů ve službě Azure API Management](api-management-howto-create-or-invite-developers.md)

- [Jak získat seznam API Management uživatelů](/powershell/module/az.apimanagement/get-azapimanagementuser)

- [Vytváření skupin a jejich používání ke správě účtů vývojářů ve službě Azure API Management](api-management-howto-create-groups.md)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Pokyny**: konfigurace instance služby Azure API Management pro ověřování vývojářských účtů pomocí Azure Active Directory (Azure AD) jako poskytovatele identity v Azure API Management.

Nakonfigurujte instanci Azure API Management tak, aby chránila vaše rozhraní API pomocí protokolu OAuth 2,0 s Azure AD.

Nakonfigurujte zásady ověřování JWT na příchozí požadavky rozhraní API, které vám pomůžou vyhovět existenci a platnosti platného tokenu.

Vytvořte nastavení diagnostiky pro uživatelské účty Azure AD a odešlete protokoly auditu a protokoly přihlášení do pracovního prostoru Log Analytics. Nakonfigurujte požadované výstrahy v rámci Log Analytics. Kromě toho můžete připojit Log Analytics pracovní prostor k Azure Sentinel nebo k SIEM třetí strany.

Pomocí zásad můžete nakonfigurovat rozšířené monitorování pomocí API Management `log-to-eventhub` , zachytit další kontextové informace vyžadované pro analýzu zabezpečení a odeslat je do Azure Sentinel nebo Siem třetí strany.

- [Jak autorizovat vývojářské účty pomocí Azure AD v Azure API Management](api-management-howto-aad.md)

- [Jak chránit rozhraní API pomocí OAuth 2,0 s Azure AD a API Management](api-management-howto-protect-backend-with-aad.md)

- [Zásady omezení přístupu ke službě API Management](api-management-access-restriction-policies.md)

- [Jak integrovat protokoly služby Azure AD do Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Postup zprovoznění služby Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Pokročilé monitorování rozhraní API](api-management-log-to-eventhub-sample.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné** materiály: pro odchylku chování přihlášení k účtu na rovině ovládacího prvku (Azure Portal) použijte funkce Azure Active Directory (Azure AD) Identity Protection a detekce rizik ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce související s identitami uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: není aktuálně k dispozici; Customer Lockbox se v současnosti pro Azure API Management nepodporuje.

- [Seznam služeb podporovaných Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Instance služby Azure API Management by měly být odděleny virtuální sítí (VNet)/Subnet a odpovídajícím způsobem označeny.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

- [Použití služby Azure API Management s virtuálními sítěmi](api-management-using-with-vnet.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: není aktuálně k dispozici; funkce pro identifikaci, klasifikaci a ochranu před únikem informací nejsou v současnosti pro Azure API Management k dispozici.

Microsoft spravuje základní infrastrukturu pro Azure API Management a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: volání roviny správy se provádí prostřednictvím Azure Resource Manager přes TLS. Vyžaduje se platný webový token JSON (JWT). Volání roviny dat je možné zabezpečit pomocí protokolu TLS a jednoho z podporovaných mechanismů ověřování (například klientský certifikát nebo token JWT).

- [Pochopení ochrany dat v Azure API Management](/azure/api-management/api-management-security-controls#data-protection)

- [Správa nastavení TLS v Azure API Management](api-management-howto-manage-protocols-ciphers.md)

- [Ochrana rozhraní API v Azure API Management s využitím Azure Active Directory (Azure AD)](api-management-howto-protect-backend-with-aad.md)

- [Ochrana rozhraní API v Azure API Management s využitím Azure AD B2C](howto-protect-backend-frontend-azure-ad-b2c.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: ještě není k dispozici; pro Azure API Management ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací. Označte službu Azure API Management Services, která může zpracovávat citlivé informace jako takové a implementovat řešení třetích stran, pokud to vyžaduje pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC. 

**Pokyny**: k řízení přístupu k Azure API Management použijte řízení přístupu na základě role Azure (Azure RBAC). Azure API Management spoléhá na řízení přístupu na základě role v Azure, které umožňuje jemně odstupňovanou správu přístupu pro API Management služby a entity (například rozhraní API a zásady).

- [Jak používat řízení přístupu na základě role Azure v Azure API Management](api-management-role-based-access-control.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft spravuje základní infrastrukturu pro Azure API Management a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na to, kdy změny probíhají v produkčních Azure Functionsch aplikacích i v jiných důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](/azure/azure-monitor/platform/alerts-activity-log)

- [Jak používat Azure Monitor a protokol aktivit Azure v Azure API Management](api-management-howto-use-azure-monitor.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: není aktuálně k dispozici; posouzení ohrožení zabezpečení v Azure Security Center není aktuálně k dispozici pro Azure API Management.

Nadřazená platforma byla zkontrolována a opravena společností Microsoft. Zkontrolujte bezpečnostní mechanismy, které jsou k dispozici pro omezení ohrožení zabezpečení související s konfigurací služby.

- [Principy ovládacích prvků zabezpečení dostupných pro Azure API Management](/azure/api-management/api-management-security-controls)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizovaného řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: není aktuálně k dispozici; posouzení ohrožení zabezpečení v Azure Security Center není aktuálně k dispozici pro Azure API Management.

Nadřazená platforma byla zkontrolována a opravena společností Microsoft. Zákazník si zkontroluje bezpečnostní mechanismy, které jsou k dispozici, k omezení ohrožení zabezpečení souvisejícího s konfigurací služby.

- [Principy ovládacích prvků zabezpečení dostupných pro Azure API Management](/azure/api-management/api-management-security-controls)

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

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Jak vytvářet a používat značky](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků Azure. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných zákazníka, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

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

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

- [Řízení přístupu na základě role v Azure API Management](api-management-role-based-access-control.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro službu Azure API Management pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. ApiManagement můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace služeb Azure API Management.

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro služby Azure API Management services pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. ApiManagement můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace instancí Azure API Management. Použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice zásad Azure, použijte Azure DevOps nebo Azure Repos k bezpečnému ukládání a správě konfigurace služby Azure API Management.

- [Jak ukládat soubory v Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentace k Azure Repos](/azure/devops/repos/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro služby Azure API Management services pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. ApiManagement můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace instancí Azure API Management. Použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: k provedení správy konfigurace pro Azure API Management použijte sadu prostředků sady Azure API Management DevOps.

Kromě toho definujte a Implementujte standardní konfigurace zabezpečení pro služby Azure API Management Services pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. ApiManagement můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace instancí Azure API Management. Použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: použijte identita spravované služby vygenerované Azure Active Directory (Azure AD), aby mohla instance API Management snadno a bezpečně přistupovat k dalším prostředkům chráněným službou Azure AD, jako je například Azure Key Vault.

- [Postup vytvoření spravované identity pro instanci API Management](api-management-howto-use-managed-service-identity.md)

- [Zásada pro ověření pomocí spravované identity](https://docs.microsoft.com/azure/api-management/api-management-authentication-policies#ManagedIdentity)

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

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure API Management), ale neběží na zákaznickém obsahu.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro nevýpočetní prostředky, které slouží k ukládání dat.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure API Management), ale neběží na zákaznickém obsahu.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro nevýpočetní prostředky, které slouží k ukládání dat.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure API Management), ale neběží na zákaznickém obsahu.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: publikováním a správou rozhraní API prostřednictvím služby Azure API Management využijete výhod možností infrastruktury chyb a infrastruktury, které byste jinak navrhli, implementovali a spravovali ručně. API Management podporuje nasazení ve více oblastech, což umožňuje, aby se rovina dat impervious k regionálním selháním bez nutnosti přidávat provozní režii.

Funkce zálohování a obnovení služby API Management poskytují potřebné stavební bloky pro implementaci strategie zotavení po havárii. Operace zálohování a obnovení je možné provést ručně nebo automaticky.

- [Postup nasazení API Management roviny dat do více oblastí](api-management-howto-deploy-multi-region.md)

- [Implementace zotavení po havárii pomocí zálohování a obnovení služby ve službě Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

- [Postup volání operace API Management Backup](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup)

- [Jak volat operaci obnovení API Management](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: operace zálohování a obnovení, které poskytuje Azure API Management provést úplné zálohování a obnovení systému.

Spravované identity lze použít k získání certifikátů z Azure Key Vault pro API Management vlastních názvů domén. Zálohujte všechny certifikáty, které jsou uložené v Azure Key Vault.

- [Implementace zotavení po havárii pomocí zálohování a obnovení služby ve službě Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

- [Postup zálohování certifikátů Azure Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: Ověřte zálohy pomocí testu obnovení služby a certifikátů ze záloh.

- [Jak volat operaci obnovení API Management](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

- [Postup obnovení certifikátů Azure Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: Azure API Management zapisuje zálohy na účty Azure Storage ve vlastnictví zákazníka. Pokud chcete svoji zálohu chránit, postupujte podle Azure Storage doporučení zabezpečení.

- [Implementace zotavení po havárii pomocí zálohování a obnovení služby ve službě Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

- [Doporučení zabezpečení pro úložiště objektů BLOB](../storage/blobs/security-recommendations.md)

Povolí Soft-Delete v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

- [Postup povolení Soft-Delete v Key Vault](../storage/blobs/soft-delete-blob-overview.md)

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

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka pomocí značek a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure, zejména těch, která zpracovávají citlivá data. Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

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

**Doprovodné** materiály: Využijte funkci automatizace pracovních postupů v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: řiďte se prosím pravidly zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [Další informace o strategii a provádění testování na základě červeného seskupování a testování průniku na webu společnosti Microsoft pro cloudovou infrastrukturu, služby a aplikace Microsoftu najdete tady.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
