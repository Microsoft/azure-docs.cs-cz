---
title: Základní hodnoty zabezpečení Azure pro App Service
description: Základní App Service zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 94ab7e235f0796277d65aebb33d9011fc76b74ce
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532784"
---
# <a name="azure-security-baseline-for-app-service"></a>Základní hodnoty zabezpečení Azure pro App Service

Základní plán zabezpečení Azure pro App Service obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení. Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview-v1.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na App Service. **Ovládací prvky** , které se nevztahují k App Service byly vyloučeny.

Pokud chcete zjistit, jak App Service kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot App Service Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: při použití App Service v izolované cenové úrovni, která se také označuje jako App Service Environment (pomocného programu), můžete nasadit přímo do podsítě v rámci služby Azure Virtual Network. Pomocí skupin zabezpečení sítě můžete své Azure App Service Environment zabezpečit blokováním příchozího a odchozího provozu do prostředků ve virtuální síti nebo omezením přístupu k aplikacím v App Service Environment.

Ve výchozím nastavení obsahují skupiny zabezpečení sítě implicitní pravidlo odepření s nejnižší prioritou, což vyžaduje, abyste explicitně přidali pravidla povolení. Přidejte pravidla povolení pro skupinu zabezpečení sítě na základě přístupu k nejmenšímu privilegovanému přístupu k síti. Základní virtuální počítače, které se používají k hostování App Service Environment, nejsou přímo přístupné, protože jsou v rámci předplatného spravovaného společností Microsoft.

Chraňte App Service Environment směrováním provozu prostřednictvím brány firewall webových aplikací (WAF) s podporou Azure Application Gateway. Použijte koncové body služby ve spojení s Application Gateway k zabezpečení příchozího provozu publikování do vaší aplikace.  

Ve víceklientské App Service (aplikace, která není v izolované úrovni) použijte skupiny zabezpečení sítě k blokování odchozího provozu z vaší aplikace. Umožněte vašim aplikacím přistupovat k prostředkům v nebo prostřednictvím Virtual Network pomocí funkce Virtual Network Integration. Tato funkce se dá použít taky k blokování odchozího provozu do veřejných adres z aplikace. Integraci Virtual Network nelze použít k poskytnutí příchozího přístupu k aplikaci.  

Zabezpečte příchozí provoz do vaší aplikace pomocí:
- Omezení přístupu – řada pravidel povolení nebo odepření, která řídí příchozí přístup
- Koncové body služby – můžou odepřít příchozí provoz mimo zadané virtuální sítě nebo podsítě.
- Soukromé koncové body – vystavení vaší aplikace vašemu Virtual Network s použitím privátní IP adresy. Se soukromými koncovými body povolenými u vaší aplikace už není přístup k Internetu.

Při použití funkce integrace Virtual Network s virtuálními sítěmi ve stejné oblasti použijte skupiny zabezpečení sítě a směrovací tabulky s uživatelsky definovanými trasami. Trasy definované uživatelem lze umístit do podsítě Integration, aby bylo možné odesílat odchozí přenosy podle účelu.  

Zvažte implementaci Azure Firewall k centrálnímu vytváření, prosazování a protokolování zásad připojení aplikace a sítě napříč vašimi předplatnými a virtuálními sítěmi. Azure Firewall používá statickou veřejnou IP adresu pro prostředky virtuální sítě, což umožňuje, aby mimo brány firewall identifikovala provoz, který pochází z vaší virtuální sítě. 

- [Uzamčení App Service Environment](environment/firewall-integration.md)

- [Otevřít projekt zabezpečení webové aplikace (OWASP) Top 10 – ochrana ohrožení zabezpečení](https://owasp.org/www-project-top-ten/)

- [Skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md)

- [Integrace aplikace s virtuální sítí Azure](web-sites-integrate-with-vnet.md)

- [Důležité aspekty sítí pro službu App Service Environment](environment/network-info.md)

- [Jak vytvořit externí pomocný objekt pro vytváření](environment/create-external-ase.md)

- [Postup vytvoření interního pomocného mechanismu](environment/create-ilb-ase.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Pokyny**: Implementace doporučení ochrany sítě z Azure Security Center k zabezpečení síťových prostředků a konfigurací souvisejících s vašimi aplikacemi App Service a rozhraními API.

Použijte Azure Firewall k posílání provozu a centrálnímu vytváření, prosazování a protokolování zásad připojení k aplikacím a síťovým připojením napříč předplatnými a virtuálními sítěmi. Azure Firewall používá statickou veřejnou IP adresu pro prostředky virtuální sítě, což umožňuje, aby mimo brány firewall identifikovala přenosy, které pocházejí z vašeho Virtual Network. Služba Azure Firewall je také plně integrovaná s Azure Monitor pro protokolování a analýzu.

- [Přehled Azure Firewall](../firewall/overview.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Postup povolení monitorování a ochrany App Service](/azure/security-center/defender-for-app-service-intro)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: Zabezpečte internetovou aplikaci dostupnou v App Service Environment (pomocného mechanismu) pomocí:
- Nasazení firewallu webových aplikací (WAF) s Azure Application Gateway před internetovou aplikací
- Pro zabezpečení příchozího provozu do Application Gateway použijte omezení přístupu.
- Zabezpečte aplikaci pomocí Azure Active Directory (Azure AD), abyste zajistili ověřování.
- Nastavte minimální verzi protokolu TLS na 1,2.
- Nastavení aplikace jenom na HTTPS

Zastavte veškerý provoz aplikací odchozího prostřednictvím Azure Firewall zařízení a sledujte protokoly. 

Zabezpečení aplikace přístupné pro Internet ve víceklientské App Service (například, ne v izolované úrovni)
- Nasazení zařízení s povolenou bránou firewall webových aplikací před aplikací
- Použití omezení přístupu nebo koncových bodů služby k zabezpečení příchozího provozu na zařízení firewallu webových aplikací (WAF)
- Zabezpečte aplikaci pomocí Azure AD, abyste zajistili ověřování.
- Nastavte minimální verzi protokolu TLS na 1,2.
- Nastavení aplikace jenom na HTTPS
- Pomocí integrace virtuální sítě a nastavení aplikace WEBSITE_VIRTUAL NETWORK_ROUTE_ALL zpřístupnit veškerý odchozí provoz v závislosti na skupinách zabezpečení sítě a uživatelem definovaných tras v integrační podsíti.

Podobně jako aplikace služby App Service Environment můžete všechny přenosy aplikací řídit pomocí Azure Firewall zařízení a monitorovat protokoly v aplikaci.

Kromě toho si přečtěte a sledujte doporučení v dokumentu App Service Environment.

- [Uzamčení App Service Environment](environment/firewall-integration.md)

- [Firewall webových aplikací Azure v Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Omezení přístupu Azure App Service](app-service-ip-restrictions.md)

- [Sledujte výstrahy WAF a snadno Sledujte trendy pomocí Azure Monitor ](../azure-monitor/overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: zabezpečte App Service Environment, jak je popsáno v tématu zamykání App Service Environment dokumentace. Použijte funkci integrované analýzy hrozeb v Azure Security Center k odepření komunikace se známými nebo nepoužívanými veřejnými IP adresami. K zabezpečení příchozího provozu do Application Gateway použijte omezení přístupu. 

Zabezpečte App Service více tenantů (aplikace není v izolované úrovni) s veřejným koncovým bodem pro Internet. Povoluje provoz jenom z konkrétní podsítě v rámci vašeho Virtual Network a blokuje všechno ostatní. Pomocí omezení přístupu nakonfigurujte seznam síťových Access Controlch seznamů (omezení IP adres), abyste mohli uzamknout povolený příchozí provoz.

Definujte prioritu mezi seznamem povolených a zakázaných pro správu přístupu k síti k vaší aplikaci. Tento seznam může zahrnovat IP adresy nebo Virtual Network podsítě. Implicitní pravidlo Odepřít vše existuje na konci seznamu, pokud obsahuje jednu nebo více položek. Tato funkce funguje se všemi App Service hostovanými pracovními funkcemi, mezi které patří aplikace, Web Apps, API Apps, Linux, aplikace pro Linux a funkce. 

Pomocí koncových bodů služby omezte přístup k vaší webové aplikaci z Virtual Network Azure. Omezte přístup k App Service více tenantů (aplikace není v izolované vrstvě) z vybraných podsítí s koncovými body služby. 

- [Omezení statické IP adresy služby Azure App Service](app-service-ip-restrictions.md)

- [Firewall webových aplikací Azure v Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Konfigurace firewallu webových aplikací (WAF) pro App Service Environment](environment/app-service-app-service-environment-web-application-firewall.md)

- [Zabezpečte si pomocného mechanismu zabezpečení, jak je popsáno v tématu uzamčení App Service Environment](environment/firewall-integration.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: monitoruje požadavky a odpovědi, které se odesílají do a z App Service aplikací pomocí Security Center. Útoky na webovou aplikaci lze monitorovat pomocí Application Gateway v reálném čase, který má bránu firewall webových aplikací, povolenou s integrovaným protokolováním z Azure Monitor a sledovat výstrahy brány firewall webových aplikací a snadno sledovat trendy.

- [Firewall webových aplikací Azure v Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: Správa provozu aplikace v App Service Environment:

- Zabezpečte App Service Environment, jak je popsáno v tématu uzamčení App Service Environment
- Nasazení Application Gateway s bránou firewall webových aplikací Azure před internetovými aplikacemi
- Nastavení aplikace tak, aby byla dostupná jenom přes HTTPS

Správa provozu pro internetovou aplikaci, která je k dispozici ve víceklientské App Service (ne v izolované úrovni): 

- Nasazení Application Gateway s povoleným firewallem webových aplikací Azure před internetovými aplikacemi
- Pomocí omezení přístupu nebo koncových bodů služby Zabezpečte příchozí přenosy do firewallu webových aplikací. Funkce omezení přístupu funguje se všemi App Service hostovanými pracovními funkcemi, včetně Web Apps, API Apps, aplikací pro Linux, kontejnerových aplikací pro Linux a funkcí.

- Nastavte aplikaci tak, aby byla dostupná jenom přes HTTPS.
- Omezte přístup k vaší App Service aplikaci pomocí omezení statických IP adres, aby přijímaly jenom provoz z virtuální IP adresy ve službě Application Gateway jako jedinou adresu s přístupem.

Další informace najdete v odkazovaných odkazech.

- [Omezení statické IP adresy služby Azure App Service](app-service-ip-restrictions.md)

- [Firewall webových aplikací Azure v Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Jak nakonfigurovat kompletní protokol TLS pomocí Application Gateway s portálem](../application-gateway/end-to-end-ssl-portal.md)

- [Zabezpečte si pomocného mechanismu zabezpečení, jak je popsáno v tématu uzamčení App Service](/azure/app-service/environment/firewall-integration)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: App Service má počet koncových bodů, které se používají ke správě služby. Tyto adresy koncových bodů jsou také součástí značky služby AppServiceManagement IP. Značka AppServiceManagement se používá jenom s App Service Environment k povolení takových přenosů. 

Můžete povolit nebo zamítnout přenos pro odpovídající službu zadáním názvu značky služby v příslušném poli zdroj nebo cíl pravidla. App Service příchozí adresy jsou sledovány ve značce služby IP AppService. Neexistuje žádná značka služby IP, která obsahuje odchozí adresy, které používá App Service.

Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Značky služby virtuální sítě](../virtual-network/service-tags-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě související s vašimi aplikacemi App Service. 

Spravujte konfigurace zabezpečení pomocí Azure Policy aliasů v oborech názvů Microsoft. Web a Microsoft. Network. Vytvořte vlastní zásady, které budou auditovat nebo vymáhat konfiguraci sítě vašich aplikací App Service. 

Použijte předdefinované definice zásad pro App Service, například:
- Aplikace by měla používat koncový bod služby virtuální sítě.
- Aplikace by měla být přístupná jen přes protokol HTTPS
- Nastavte minimální verzi protokolu TLS na aktuální verzi.

Další informace najdete v odkazovaných odkazech.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak nakonfigurovat kompletní protokol TLS pomocí Application Gateway s portálem](../application-gateway/end-to-end-ssl-portal.md)

- [Zabezpečte si pomocného mechanismu zabezpečení, jak je popsáno v tématu uzamčení App Service](/azure/app-service/environment/firewall-integration)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro skupiny zabezpečení sítě a další související prostředky, včetně toku provozu v App Service.

Určete potřebu podniku, dobu trvání a tak dále, s polem Popis pro všechna pravidla, která umožňují provoz do nebo ze sítě pro jednotlivá pravidla skupin zabezpečení sítě.

Použijte některou z vestavěných definic Azure Policy souvisejících s efekty označování, jako je "vyžadovat značku a její hodnotu", abyste zajistili, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na všechny existující neoznačené prostředky. Pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure můžete vyhledat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Omezení přístupu Azure App Service](/azure/app-service/app-service-ip-restrictions)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny nastavení sítě a prostředků souvisejících s App Service. 

Použijte jednu z několika předdefinovaných Azure Policy integrovaných definic pro App Service, jako je například zásada, která Audituje aplikace pro použití služby koncového bodu virtuální sítě. V rámci Azure Monitor můžete vytvářet výstrahy, které se aktivují, když dojde ke změnám kritických nastavení sítě nebo prostředků. 

Přečtěte si podrobné výstrahy a doporučení zabezpečení v Security Center, na portálu nebo prostřednictvím programových nástrojů. Exportujte tyto informace nebo je odešlete do jiných monitorovacích nástrojů ve vašem prostředí. K dispozici jsou nástroje pro export výstrah a doporučení buď ručně, nebo průběžně a nepřetržitě. Pomocí těchto nástrojů můžete:
 
- Průběžný export do pracovního prostoru Log Analytics
- Průběžný export do Azure Event Hubs (pro integrace s systémů Siem třetích stran)
- Exportovat do souboru CSV (jednou)

Doporučuje se vytvořit proces s automatizovanými nástroji pro monitorování konfigurací síťových prostředků a rychlé rozpoznávání změn.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

- [Export doporučení a výstrah zabezpečení](../security-center/continuous-export.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: Integrujte svůj App Service Environment (pomocného programu) s Azure monitor a odešlete protokoly do Azure Storage, Azure Event Hubs nebo Log Analytics. Povolte nastavení diagnostiky protokolů aktivit Azure pro protokolování auditu řídicí roviny. Výstrahy zabezpečení z Security Center jsou publikovány v protokolu aktivit Azure. Můžete auditovat data protokolu aktivit Azure, která vám umožní určit, co, kdo a kdy se má u všech operací zápisu (PUT, POST, DELETE) provést na úrovni řídicích roviny pro Azure App Service a další prostředky Azure. Uložte dotazy pro budoucí použití, připněte výsledky dotazu na řídicí panely Azure a vytvořte výstrahy protokolu. Pro přístup k telemetrie programově taky použijte REST API přístupu k datům v Application Insights.

Pro připojení k různým zdrojům dat a konektorům na základě vašich podnikových požadavků použijte Microsoft Azure Sentinel (SIEM), škálovatelnou, cloudovou a nativní správu událostí zabezpečení (). Můžete také povolit a začlenit data do systému SIEM (Security Information Management) jiného výrobce, jako je například Barracuda v Azure Marketplace.

- [Protokolování aktivity pomocného protokolu](environment/using-an-ase.md#logging)

- [Postup povolení nastavení diagnostiky pro Azure App Service](troubleshoot-diagnostic-logs.md)

- [Postup povolení Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Export telemetrie z Application Insights](../azure-monitor/app/export-telemetry.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: povolení nastavení diagnostiky protokolů aktivit Azure pro řízení protokolování auditu App Service. Odešlete protokoly do pracovního prostoru Log Analytics, centra událostí Azure nebo účtu služby Azure Storage.
Možnost "co, kdo a kdy" pro jakékoli operace zápisu (PUT, POST, DELETE) prováděná na úrovni řídicích roviny se dá určit pomocí dat protokolu aktivit Azure pro App Service a dalších prostředků Azure.

Kromě toho Azure Key Vault poskytuje centralizovanou správu tajných kódů pomocí zásad přístupu a historie auditu. 

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/platform/activity-log.md)

- [Postup povolení nastavení diagnostiky pro Azure App Service](troubleshoot-diagnostic-logs.md)

- [Správce prostředků operace](../role-based-access-control/resource-provider-operations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování protokolu pro pracovní prostory Log Analytics přidružené k vašim App Service prostředkům podle předpisů pro dodržování předpisů vaší organizace.
- [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: Zkontrolujte nastavení diagnostiky protokolu aktivit Azure v prostředcích App Service a protokoly, které se odesílají do pracovního prostoru Log Analytics. Můžete provádět dotazy v Log Analytics k hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě shromážděných dat.

Pro aplikace App Service můžete použít Application Insights a shromažďovat data o protokolech, výkonu a chybách. Zobrazit data telemetrie shromážděná Application Insights v rámci Azure Portal.

Pokud jste nasadili Firewall webových aplikací (WAF), můžete monitorovat útoky na aplikace App Service pomocí protokolu brány firewall webových aplikací v reálném čase. Protokol je integrovaný do Azure Monitor, aby sledoval výstrahy brány firewall webových aplikací a mohli snadno sledovat trendy.

Pro integraci s různými zdroji dat a konektory podle požadavků použijte Azure Sentinel, škálovatelnou a cloudovou správu událostí zabezpečení (SIEM). Volitelně můžete povolit a začlenit data do řešení pro správu událostí zabezpečení třetí strany v Azure Marketplace.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/platform/activity-log.md)

- [Postup povolení Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Jak integrovat App Service Environment s využitím Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Postup zprovoznění služby Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Pokyny**: Konfigurace Security Center ve vašem předplatném Azure a kontrola vygenerovaných výstrah. Pomocí Azure Monitor můžete získat data protokolu aktivit do centra událostí, kde je může číst řešení SIEM (Security Information Management), jako je například Azure Sentinel. 

Pomocí protokolu brány firewall webových aplikací v reálném čase s nasazenou bránou firewall webových aplikací Azure (WAF) Sledujte útoky na vaše aplikace App Service. Protokol je integrovaný do Azure Monitor, aby mohl sledovat výstrahy firewallu webových aplikací (WAF) a snadno sledovat trendy.

- [Jak integrovat App Service Environment s využitím Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Export doporučení a výstrah zabezpečení](../security-center/continuous-export.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a dotazovat na ně. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Použití spravovaných identit pro App Service a Azure Functions](overview-managed-identity.md?context=azure%2Factive-directory%2Fmanaged-identities-azure-resources%2Fcontext%2Fmsi-context&amp;tabs=dotnet)

- [Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal](../role-based-access-control/role-assignments-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: Azure Active Directory (Azure AD) nemá koncept výchozích hesel. Poskytuje přístup roviny řízení k App Service.

Obecně se vyhnete implementaci výchozích hesel pro přístup uživatelů při sestavování vlastních aplikací. Použijte jednoho poskytovatele identity, který je ve výchozím nastavení dostupný pro App Service, jako je například Azure AD, účet Microsoft, Facebook, Google nebo Twitter.

Zakáže anonymní přístup, pokud ho nepotřebujete podporovat. 

- [Zprostředkovatelé identity jsou ve výchozím nastavení k dispozici v Azure App Service](overview-authentication-authorization.md#identity-providers)

- [Ověřování a autorizace v Azure App Service a Azure Functions](overview-authentication-authorization.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí funkcí pro správu identit a přístupu v Security Center můžete monitorovat a sledovat počet účtů pro správu. 

Použijte doporučení z Security Center nebo integrovaných zásad Azure, jako jsou:

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník. 
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

Vytvořte proces pro monitorování konfigurací síťových prostředků a detekci změn účtů pro správu.

- [Použití Azure Security Center k monitorování identity a přístupu](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Další informace o udělení přístupu uživatelům k aplikacím](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné** materiály: ověření App Service prostřednictvím Azure Active Directory (Azure AD). Poskytuje službu OAuth 2,0 pro vašeho poskytovatele identity a povoluje autorizovaný přístup k mobilním a webovým aplikacím. 

Aplikace App Service používají federované identity, ve kterých poskytovatel identity od jiného výrobce spravuje identity uživatelů a tok ověřování za vás. Tito zprostředkovatelé identity jsou k dispozici ve výchozím nastavení:

- Azure AD
- Účet Microsoft

- Facebook

- Google

- Twitter

Pokud povolíte ověřování a autorizaci jedním z těchto poskytovatelů, je k dispozici koncový bod přihlášení pro ověřování uživatelů a ověření tokenů ověřování od poskytovatele.

- [Pochopení ověřování a autorizace v Azure App Service](overview-authentication-authorization.md#identity-providers)

- [Další informace o ověřování a autorizaci v Azure App Service](overview-authentication-authorization.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte ve službě Azure Active Directory (Azure AD) funkci vícefaktorového ověřování a sledujte doporučení pro správu identit a přístupu v Security Center.

Implementace vícefaktorového ověřování pro Azure AD. Správci musí zajistit, aby byly účty předplatného na portálu chráněné. Předplatné je zranitelné vůči útokům, protože spravuje prostředky, které jste vytvořili. 

- [Azure Security MFA](/previous-versions/azure/security/develop/secure-aad-app)

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Doprovodné** materiály: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení k prostředkům Azure a jejich konfigurace.

- [Další informace o pracovních stanicích s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Pokyny**: použití PRIVILEGED Identity Management (PIM) v Azure Active Directory (Azure AD) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelým nebo nebezpečným aktivitám.

Navíc můžete pomocí zjišťování rizik Azure AD zobrazovat výstrahy a sestavy týkající se rizikového chování uživatelů.

Ochrana před hrozbami v Security Center poskytuje komplexní obranu pro vaše prostředí, které zahrnuje ochranu před hrozbami pro výpočetní prostředky Azure, jako jsou počítače s Windows, počítače se systémem Linux, App Service a kontejnery Azure.

- [Postup nasazení Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Vysvětlení zjišťování rizik Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Ochrana před hrozbami pro výpočetní prostředky Azure](../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup k Azure Portal jenom z konkrétních logických skupin rozsahů IP adres, zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: použití Azure Active Directory (Azure AD) jako centrálního ověřování a autorizačního systému pro vaše aplikace App Service. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data a také soli, hodnoty hash a bezpečně ukládá přihlašovací údaje uživatele.

- [Jak nakonfigurovat Azure App Service aplikace tak, aby používaly přihlášení k Azure AD](configure-authentication-provider-aad.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Objevte zastaralé účty pomocí protokolů poskytovaných službou Azure Active Directory (Azure AD). Pomocí kontrol přístupu Azure identity můžete efektivně spravovat členství ve skupinách a přístup k podnikovým aplikacím a také přiřazení rolí. Pravidelně kontrolujte přístup uživatelů, abyste měli jistotu, že budou mít přístup jenom zamýšlení uživatelé. 

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: použití Azure Active Directory (Azure AD) jako centrálního ověřování a autorizačního systému pro vaše aplikace App Service. Azure AD chrání data pomocí silného šifrování pro neaktivní a přenosové, soli, hodnoty hash a bezpečně ukládá přihlašovací údaje uživatele.

Přístup k informacím o aktivitě, auditování a zdrojích protokolu událostí přihlášení ke službě Azure AD vám umožní integraci s ověřováním Azure Sentinel nebo SIEM (Security Information Management) od jiného výrobce. Zjednodušte proces vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy protokolu je možné nakonfigurovat v rámci Log Analytics.

- [Jak nakonfigurovat Azure App Service aplikace tak, aby používaly přihlášení k Azure AD](configure-authentication-provider-aad.md)

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Postup zprovoznění služby Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné** materiály: použití Azure Active Directory (Azure AD) jako centrálního ověřování a autorizačního systému pro vaše aplikace App Service. 

Pomocí Azure AD Identity Protection můžete nakonfigurovat automatizované odpovědi na zjištěné podezřelé akce týkající se identit uživatelů, jako je například odchylka chování přihlášení k účtu u řídicí roviny pomocí Azure Portal. Můžete také ingestovat data do služby Azure Sentinel pro další šetření. 

- [Jak nakonfigurovat aplikaci Azure App Service, aby používala přihlášení k Azure AD](configure-authentication-provider-aad.md)

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: není k dispozici pro App Service. Customer Lockbox se pro Azure App Service nepodporuje.

- [Seznam služeb podporovaných Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: pomocí značek pomáhají při sledování app Servicech prostředků, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Doprovodné** materiály: pro App Service Environment implementujte samostatné předplatná, skupiny pro správu nebo obojí, pro vývoj, testování a produkční prostředí. Aplikace, které zpracovávají citlivé informace z jiných aplikací, můžete izolovat stejným způsobem. Nasaďte aplikaci App Service do Virtual Network. Pro další izolaci aplikací použijte skupiny zabezpečení sítě a podsítě. 

K dispozici jsou dva typy nasazení App Service prostředí (pomocného mechanismu). Oba umožňují izolovat provoz na základě vašich obchodních požadavků.

- Externí prostředí aplikační služby – zpřístupňuje App Service Environment hostované aplikace na IP adrese přístupné pro Internet.

-  Služba aplikační služby interního nástroje pro vyrovnávání zatížení (interního nástroje) – zpřístupňuje App Service Environment hostované aplikace na IP adrese v rámci vašeho Virtual Network. Interním koncovým bodem je interní nástroj pro vyrovnávání zatížení (interního nástroje), což znamená, proč se nazývá interního nástroje pomocného modulu pro čtení. 

Pro víceklientské App Service (aplikace, která není v izolované vrstvě), použijte integraci Virtual Network pro přístup vaší aplikace k prostředkům ve vaší virtuální síti.  Používejte přístup k privátnímu webu, aby aplikace byla přístupná jenom z privátní sítě, jako je třeba jedna z virtuální sítě Azure. Virtual Network Integration se používá jenom pro odchozí volání z vaší aplikace do vaší Virtual Network. Funkce Virtual Network Integration se chová jinak, když se používá s virtuální sítí ve stejné oblasti a s virtuálními sítěmi v jiných oblastech. 
 
- [Důležité aspekty sítí pro službu App Service Environment](environment/network-info.md)

- [Jak vytvořit externí pomocný objekt pro vytváření](environment/create-external-ase.md)

- [Postup vytvoření interního pomocného mechanismu](environment/create-ilb-ase.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: i když funkce ochrany před únikem informací, klasifikací a ztrátou ještě nejsou k dispozici pro App Service, můžete snížit riziko exfiltrace dat z virtuální sítě tak, že odeberete všechna pravidla, kde cíl používá značku pro Internet nebo služby Azure. 

Společnost Microsoft spravuje základní infrastrukturu pro App Service a implementuje striktní ovládací prvky, které zabrání ztrátě nebo expozici vašich dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: použijte výchozí minimální verzi TLS 1,2 nakonfigurovanou v nastavení TLS/SSL pro šifrování všech informací při přenosu. Také zajistěte, aby všechny požadavky na připojení HTTP byly přesměrovány na HTTPS.

- [Pochopení šifrování při přenosu pro Azure App Service Web Apps](security-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: aktuálně není k dispozici. Pro App Service ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací. 

Označte App Service aplikace, které mohou zpracovávat citlivé informace. Implementujte řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Microsoft spravuje základní platformu a zpracovává všechna zákaznická data jako citlivá a směřuje k velkým délkám, aby se předešlo ochraně před ztrátou a únikem dat zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: použití řízení přístupu na základě role (Azure RBAC) v Azure Active Directory (Azure AD) k řízení přístupu k rovině ovládacího prvku App Service na Azure Portal.

- [Konfigurace řízení přístupu na základě role Azure](../role-based-access-control/role-assignments-portal.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: obsah webu v aplikaci App Service, jako jsou soubory, je uložený v Azure Storage, který automaticky zašifruje obsah v klidovém formátu. Vyberte možnost ukládat tajné klíče aplikace do Key Vault a načíst je za běhu.

Tajné kódy poskytnuté zákazníkem jsou v klidovém stavu zašifrované a ukládají se do konfiguračních databází App Service.

Všimněte si, že i když se místně připojené disky můžou na webech jako dočasné úložiště (například D:\Local a% TMP%), nejsou v klidovém stavu šifrované.

- [Porozumět ovládacím prvkům ochrany dat pro Azure App Service]()

- [Vysvětlení šifrování Azure Storage v klidovém umístění](../storage/common/storage-service-encryption.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na jakékoli změny v produkčních App Servicech aplikacích a dalších důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Pokyny**: pomocí DevSecOps praxe zajistíte, aby byly vaše aplikace App Service zabezpečené a zůstaly zabezpečené po celou dobu jejich životního cyklu. DevSecOps zahrnuje tým zabezpečení vaší organizace a jejich schopnosti DevOps postupy, které umožňují zabezpečení zodpovědností všech členů týmu.

Pokud chcete zabezpečit App Service aplikace, přečtěte si a sledujte doporučení Security Center.

- [Postup přidání průběžného ověřování zabezpečení do kanálu CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Pokyny**: Společnost Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují App Service. Můžete ale použít závažnost doporučení v rámci Security Center a také bezpečné skóre pro měření rizika v rámci vašeho prostředí. Vaše zabezpečené skóre vychází z počtu Security Center doporučení, která jste zmírnili. Chcete-li určit prioritu doporučení pro řešení prvního, zvažte závažnost každého z nich.

- [Referenční příručka k doporučením zabezpečení](../security-center/recommendations-reference.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty, protokoly atd.). Zajistěte, aby se pro vašeho tenanta používala příslušná oprávnění, a můžete vytvořit výčet všech předplatných Azure i prostředků v rámci předplatných.

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použijte značky pro prostředky Azure pomocí metadat k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Pokyny**: použití značek, skupin pro správu a samostatných předplatných podle potřeby k uspořádání a sledování prostředků Azure. V rámci tohoto procesu proveďte pravidelné sjednocení inventáře a zajistěte odebrání neautorizovaných prostředků z vašich předplatných.

Pomocí následujících integrovaných definic zásad můžete pro typ prostředků, které se mají vytvořit v zákaznických předplatných, zvolit Azure Policy.

- Žádné povolené typy prostředků
- Povolené typy prostředků

Další informace najdete v odkazovaných odkazech.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky na základě potřeb vaší organizace.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných.

Pomocí Azure Resource graphu se můžete dotazovat nebo zjišťovat prostředky v rámci svých předplatných.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat na prostředky v rámci předplatných a zjistit, jestli jsou zjištěné prostředky Azure schválené na základě zásad vaší organizace.

Pomocí WebJobs v App Service můžete monitorovat neschválené softwarové aplikace, které jsou nasazené ve výpočetních prostředcích. Pomocí WebJobs můžete spustit program nebo skript ve stejné instanci jako webová aplikace, aplikace API nebo mobilní aplikace. Definujte konfigurace a monitorování úloh WebJob pomocí protokolů. Na stránce s podrobnostmi o spuštění webové úlohy vyberte možnost přepnout výstup. zobrazí se text obsahu protokolu. Všimněte si, že WebJobs ještě nejsou podporované pro App Service v systému Linux.

- [Spouštění úloh na pozadí pomocí WebJobs v Azure App Service](webjobs-create.md)

- [Kurz – vytváření a Správa zásad pro prosazování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Rychlý Start – spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Pokyny**: Ujistěte se, že všechny prostředky Azure přítomné v prostředí jsou schválené. Použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných. Odeberte všechny nasazené softwarové aplikace, které nebyly schválené podle vašich zásad vaší organizace.

- [Spouštění úloh na pozadí pomocí WebJobs v Azure App Service](webjobs-create.md)

- [Kurz – vytváření a Správa zásad pro prosazování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Rychlý Start – spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Pokyny**: Ujistěte se, že všechny prostředky Azure přítomné v prostředí jsou schválené. Použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných. Odeberte všechny nasazené softwarové aplikace, které nebyly schválené podle vašich zásad vaší organizace. 

- [Spouštění úloh na pozadí pomocí WebJobs v Azure App Service](webjobs-create.md)

- [Kurz – vytváření a Správa zásad pro prosazování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Rychlý Start – spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Pokyny**: Vytvoření procesu pro pravidelné kontroly neautorizovaných služeb Azure za účelem zajištění, že se ve svých předplatných používají jenom autorizované služby Azure.

Pomocí Azure Resource graphu v rámci tohoto procesu můžete zadávat dotazy nebo zjišťovat prostředky v rámci svých předplatných. Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

Nakonfigurujte Azure Policy pro vložení omezení typu prostředků, které se dají vytvořit v předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Pomocí WebJobs v App Service můžete monitorovat neschválené softwarové aplikace nasazené v rámci prostředků počítače. Pomocí WebJobs můžete spustit program nebo skript ve stejné instanci jako webová aplikace, aplikace API nebo mobilní aplikace. Definujte konfigurace a monitorování úloh WebJob pomocí protokolů. Na stránce s podrobnostmi o spuštění webové úlohy vyberte možnost přepnout výstup. zobrazí se text obsahu protokolu. Všimněte si, že WebJobs ještě nejsou podporované pro App Service v systému Linux.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Spouštění úloh na pozadí pomocí WebJobs v Azure App Service](webjobs-create.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: Implementujte proces pro inventarizaci a kontrolu softwarových titulů v předplatných v pravidelných intervalech, abyste ve svých předplatných použili jenom autorizované služby Azure.

Pomocí Azure Resource graphu v rámci tohoto procesu můžete zadávat dotazy nebo zjišťovat prostředky v rámci předplatných. Ujistěte se, že všechny prostředky Azure zjištěné v prostředí jsou schválené.

Nakonfigurujte Azure Policy pro omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Podobně použijte WebJobs v App Service k inventarizaci neschválených softwarových aplikací nasazených v rámci prostředků počítače. Pomocí protokolů definujte jejich konfiguraci a monitorování. Na stránce s podrobnostmi o spuštění webové úlohy vyberte možnost přepnout výstup. zobrazí se text obsahu protokolu. Všimněte si, že WebJobs ještě nejsou podporované pro App Service v systému Linux.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: Konfigurace podmíněného přístupu Azure pro omezení možnosti uživatelů pracovat s Azure Resource Manager pomocí konfigurace "blokování přístupu" pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: WebJobs v App Service umožňují zákazníkům spustit program nebo skript ve stejné instanci jako webová aplikace, aplikace API nebo mobilní aplikace. Zodpovídáte za definování konfigurace pro omezení nebo omezení skriptů, které organizace nepovoluje. App Service neposkytuje mechanismus pro nativně omezeného spuštění skriptu. Všimněte si, že WebJobs ještě nejsou podporované pro App Service v systému Linux.

- [Spouštění úloh na pozadí pomocí WebJobs v Azure App Service](webjobs-create.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: implementace samostatných předplatných nebo skupin pro správu, aby poskytovaly izolaci pro vysoce rizikové App Service aplikací. Nasaďte aplikaci s vyšším rizikem na vlastní Virtual Network, protože zabezpečení hraničního prostředí v App Service dosahuje využití virtuálních sítí. App Service Environment je nasazení App Service do podsítě ve službě Azure Virtual Network. 

Existují dva typy prostředí služby Application Service Environment, externí prostředí služby Application Service a interního nástroje (interní Load Balancer) aplikační služby. Vyberte nejlepší architekturu podle vašich požadavků.

- [Důležité aspekty sítí pro službu App Service Environment](environment/network-info.md)

- [Vytvoření externího prostředí App Service](environment/create-external-ase.md)

- [Vytvoření a použití interní Load Balancer App Service Environment](environment/create-ilb-ase.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro App Service nasazených aplikací s využitím Azure Policy.

Použijte aliasy Azure Policy v oboru názvů Microsoft. Web k vytváření vlastních zásad pro auditování nebo prosazování konfigurace App Service Web Apps.

Použijte předdefinované definice zásad, jako například:
- App Service by měl používat koncový bod služby virtuální sítě
- Webové aplikace by měly být přístupné jenom přes HTTPS.

- Použití nejnovější verze TLS ve vašich aplikacích

Doporučuje se dokumentovat v procesu použití integrovaných definic zásad pro standardizované účely.   

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [odepřít] a [nasadit, pokud neexistuje] efekty pro vymáhání zabezpečených nastavení napříč vašimi aplikacemi Azure App Service.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: vyberte Azure DevOps nebo Azure Repos, abyste mohli bezpečně ukládat a spravovat kód při použití vlastních definicí Azure Policy.

Pomocí stávajícího kanálu průběžné integrace (CI) a průběžného doručování (CD) nasaďte známou zabezpečenou konfiguraci.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Web k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Vývoj procesu a kanálu pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. Web k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. 

Použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistují], efekty pro automatické vymáhání konfigurací pro prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použití spravovaných identit k poskytování App Service aplikací s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity umožňují vašim aplikacím ověřování v jakékoli službě, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu. Ujistěte se, že je v Azure Key Vault povolené obnovitelné odstranění.

- [Jak povolit obnovitelné odstranění v Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Jak používat spravované identity pro App Service](overview-managed-identity.md)

- [Jak zajistit Key Vault ověřování pomocí spravované identity](../key-vault/general/assign-access-policy-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: pomocí spravovaných identit poskytněte App Service nasazené aplikace s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity umožňují aplikacím ověřování v jakékoli službě, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Jak používat spravované identity pro App Service](overview-managed-identity.md)

- [Jak zajistit Key Vault ověřování pomocí spravované identity](../key-vault/general/assign-access-policy-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: funkce zálohování a obnovení v App Service umožňuje snadno vytvořit zálohy aplikací ručně nebo podle plánu. Zálohy můžete nakonfigurovat tak, aby se zachovaly až do neurčitého množství času. Aplikaci můžete obnovit do snímku předchozího stavu přepsáním existující aplikace nebo obnovením do jiné aplikace.

App Service můžou zálohovat následující informace do účtu služby Azure Storage a kontejneru, u kterého jste nakonfigurovali, aby používala aplikaci:
- Konfigurace aplikací
- Obsah souboru
- Databáze připojená k vaší aplikaci

Zajistěte, aby k pravidelným a automatickým zálohám probíhala frekvence definovaná v zásadách vaší organizace.

- [Vysvětlení možnosti zálohování Azure App Service](manage-backup.md)

- [Klíče spravované zákazníkem pro šifrování Azure Storage](../storage/common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: k zálohování aplikací použijte funkci zálohování a obnovení App Service. Funkce zálohování vyžadují účet Azure Storage k uložení informací o zálohování vaší aplikace.

- Azure Storage poskytuje šifrování v místních klíčích poskytovaných systémem, nebo na vašich vlastních klíčích spravovaných zákazníky. V takovém případě se data vaší aplikace ukládají, když neběží ve webové aplikaci v Azure.
- Spuštění z balíčku pro nasazení je funkce nasazení App Service. Umožňuje nasadit obsah webu z Azure Storage účtu pomocí adresy URL sdíleného přístupového podpisu (SAS).

- Odkazy na Key Vault jsou funkcí zabezpečení App Service. Umožňuje import tajných kódů za běhu jako nastavení aplikace. Použijte k zašifrování adresy URL SAS vašeho účtu Azure Storage.

Další informace jsou k dispozici na odkazovaných odkazech.

- [Zálohování aplikace v Azure](manage-backup.md)

- [Obnovení aplikace běžící v Azure App Service](web-sites-restore.md)

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Model šifrování a tabulka správy klíčů](../security/fundamentals/encryption-atrest.md)

- [Šifrování v klidovém formátu pomocí klíčů spravovaných zákazníkem](configure-encrypt-at-rest-using-cmk.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: pravidelně testujte proces obnovení pro všechny zálohy vašich App Servicech aplikací.

- [Zálohování aplikace v Azure](manage-backup.md)

- [Postup obnovení webové aplikace v Azure App Service](web-sites-restore.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: App Service zálohy se ukládají v rámci účtu Azure Storage. Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 šifrování AES, je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2. Šifrování Azure Storage se v systému Windows podobá šifrování BitLockeru.

Azure Storage šifrování je povolené pro všechny účty úložiště, včetně účtů úložiště Správce prostředků a Classic. Šifrování Azure Storage nelze zakázat. Vzhledem k tomu, že vaše data jsou zabezpečená ve výchozím nastavení, nemusíte upravovat kód ani aplikace, abyste mohli využívat Azure Storage šifrování.

Ve výchozím nastavení se data v účtu úložiště šifrují pomocí klíčů spravovaných Microsoftem. Pro šifrování vašich dat můžete spoléhat na klíče spravované Microsoftem, nebo můžete šifrování spravovat pomocí vlastních klíčů. Ujistěte se, že je v Azure Key Vault povolené obnovitelné odstranění.

- [Pochopení Azure Storage šifrování pro neaktivní neaktivní data](../storage/common/storage-service-encryption.md)

- [Jak povolit obnovitelné odstranění v Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

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

Navíc můžete jasně označit předplatná (například produkci, neprodukční) a vytvořit názvový systém, který bude jasně identifikovat a kategorizovat prostředky Azure.

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si průvodce publikováním v NIST – příručka pro testování, školení a cvičení programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Security Center Data můžete streamovat Sentinel pro výstrahy podle obchodních potřeb.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: Využijte pravidla zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Můžete najít další informace o strategii Microsoftu a provádění testování na základě červeného seskupování a testování průniku v rámci cloudové infrastruktury, služeb a aplikací spravovaných Microsoftem.

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)