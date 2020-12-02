---
title: Základní hodnoty zabezpečení Azure pro Azure IoT Hub
description: Základní hodnoty zabezpečení služby Azure IoT Hub poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 891366060cddaf2a046de36fa9300432977ec903
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457735"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Základní hodnoty zabezpečení Azure pro Azure IoT Hub

Základní hodnota zabezpečení Azure pro Microsoft Azure IoT Hub obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení. Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů. Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: ve výchozím nastavení se názvy hostitelů IoT Hub mapují k veřejnému koncovému bodu s veřejně směrovatelné IP adresou přes Internet. Různí zákazníci sdílí tento IoT Hub veřejný koncový bod a zařízení IoT v různých sítích a místních sítích mají přístup k nim.

IoT Hub funkce, včetně směrování zpráv, nahrávání souborů a hromadného importu a exportu zařízení, taky vyžadují připojení z IoT Hub k prostředku Azure vlastněné zákazníkem přes jeho veřejný koncový bod. Tyto cesty k připojení společně vytvářejí přenos z provozu od IoT Hub k zákaznickým prostředkům.

Doporučuje se omezit připojení k prostředkům Azure (včetně Azure IoT Hub) prostřednictvím virtuální sítě, kterou vlastníte, a pracovat s cílem omezit vystavení připojení v izolované síti a povolit místní síťové připojení přímo k páteřní síti Azure. Pomocí privátního a privátního koncového bodu Azure můžete v případě potřeby povolit privátní přístup k vašim službám z jiných virtuálních sítí.

Nechejte otevřené hardwarové porty ve vašich zařízeních na minimum, aby nedocházelo k nežádoucímu přístupu. Kromě toho mechanismy sestavení pro prevenci nebo detekci fyzické manipulace se zařízením.

- [Podpora virtuálních sítí IoT](virtual-network-support.md)
- [osvědčené postupy pro velké objemy sítí](../iot-fundamentals/security-recommendations.md?context=azure%2fiot-hub%2frc%2frc#networking)
- [Přehled privátních odkazů Azure](../private-link/private-link-overview.md)
- [Skupina zabezpečení sítě Azure](../virtual-network/network-security-groups-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Doprovodné** materiály: použijte Azure Security Center a postupujte podle doporučení pro ochranu sítě, abyste se mohli lépe zabezpečit síťové prostředky Azure. Povolte protokolování toku skupin zabezpečení sítě a odešlete protokoly do účtu Azure Storage k auditování. Protokoly toku můžete také odeslat do pracovního prostoru Log Analytics a potom pomocí Analýza provozu poskytnout přehled o vzorech přenosu v cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu, identifikovat aktivní body a bezpečnostní hrozby, pochopit vzorce toku přenosů a odhalit nesprávné konfigurace sítě.
 
- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)
 
- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: zablokujte známé škodlivé IP adresy s IoT Hub pravidly filtru IP. Škodlivé pokusy se také zaznamenávají a upozorňují prostřednictvím Azure Security Center pro IoT.

Azure DDoS Protection Basic už je povolená a k dispozici není žádná další cena jako součást IoT Hub. Nepřetržité monitorování provozu a zmírnění běžných útoků na úrovni sítě v reálném čase zajišťuje stejnou ochranu, jakou využívají online služby Microsoftu. Celá škála globální sítě Azure se dá použít k distribuci a zmírnění provozu útoku napříč oblastmi.

- [IoT Hub filtr IP adres](iot-hub-ip-filtering.md)

- [Azure Security Center pro komunikaci s podezřelými IP adresami IoT](../defender-for-iot/concept-security-alerts.md)

- [Spravovat Azure DDoS Protection Basic](../ddos-protection/ddos-protection-overview.md)

- [Ochrana před hrozbami v Azure Security Center](../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro nabídky, které vytváří síťové pakety, které mohou být zaznamenávány a zobrazovány zákazníky. IoT Hub nevydává síťové pakety, které jsou na straně zákazníka, a není navržené pro nasazení přímo do Azure Virtual Networks.

**Monitorování Azure Security Center**: ne

**Odpovědnost**: netýká se

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: vyberte nabídku z Azure Marketplace, která podporuje funkce ID/IP adres s možnostmi kontroly zatížení.  Pokud kontrola datové části není požadavkem, je možné použít Azure Firewall analýzy hrozeb. Azure Firewall filtrování založené na základě logiky hrozeb slouží k upozorňování a zablokování provozu do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu.

Nasaďte řešení brány firewall podle svého výběru na základě hranic sítě vaší organizace za účelem detekce a blokování škodlivého provozu. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Postup konfigurace výstrah pomocí Azure Firewall](../firewall/threat-intel.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků, které potřebují přístup k vašemu IoT Hub Azure, použijte značky služby Virtual Network k definování řízení přístupu k síti u skupin zabezpečení sítě nebo na Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (například AzureIoTHub) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Jak používat značky služeb pro Azure IoT](iot-hub-understand-ip-address.md)
- [Další informace o použití značek služeb](../virtual-network/service-tags-overview.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené k vašim oborům názvů Azure IoT Hub pomocí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. Devices a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich Machine Learning oborů názvů. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: pomocí značek pro síťové prostředky přidružené k nasazení služby Azure IoT Hub můžete logicky uspořádat do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s Azure IoT Hub. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Společnost Microsoft udržuje zdroj času používaný pro prostředky Azure, jako je například Azure IoT Hub pro časová razítka v protokolech.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení vygenerovaných službou Azure IoT Hub. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty úložiště pro dlouhodobé a archivační úložiště. Případně můžete povolit a začlenit data do Azure Sentinel nebo incidentu zabezpečení a správu událostí třetích stran (SIEM).

- [Nastavení protokolů služby Azure IoT](monitor-iot-hub-reference.md#resource-logs)
- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení nastavení diagnostiky Azure IoT v prostředcích Azure pro přístup k protokolům auditu, zabezpečení a zdrojů. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

- [Nastavení protokolů IoT Hub Azure](monitor-iot-hub-reference.md#resource-logs)

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování protokolu pro pracovní prostory Log Analytics přidružené k vašim instancím Azure IoT Hub podle předpisů pro dodržování předpisů vaší organizace.

- [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: analýza a sledování protokolů pro neobvyklé chování a pravidelné prohlížení výsledků z Azure IoT Hub. Pomocí Azure Monitor a pracovního prostoru Log Analytics můžete prohlížet protokoly a provádět dotazy na data protokolu.

Případně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Monitorování stavu Azure IoT](monitor-iot-hub.md)
- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Začínáme s Log Analytics dotazy](../azure-monitor/log-query/log-analytics-tutorial.md)
   
- [ Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: použijte Azure Security Center pro IoT s pracovním prostorem Log Analytics pro monitorování a upozorňování na aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech. Případně můžete povolit a začlenit data do Azure Sentinel. Můžete také definovat provozní výstrahy pomocí Azure Monitor, které mohou mít vliv na zabezpečení, například při neočekávaném poklesu provozu.

- [Monitorování stavu služby Azure IoT Hub](monitor-iot-hub.md)
- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)
- [Azure Security Center pro výstrahy IoT](../defender-for-iot/concept-security-alerts.md)

- [Upozornění na data protokolu Log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: nepoužitelné; Azure IoT Hub nezpracovává ani nevytváří protokoly související s malwarem.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: nepoužitelné; Azure IoT Hub nezpracovává ani nevytváří protokoly související s DNS.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: řízení přístupu na základě role v Azure (Azure RBAC) umožňuje spravovat přístup ke službě Azure IoT Hub prostřednictvím přiřazení rolí. Tyto role můžete přiřadit uživatelům, skupinám instančních objektů a spravovaným identitám. Pro určité prostředky jsou předem definované předdefinované role a tyto role můžou být v inventáři nebo prostřednictvím nástrojů, jako je Azure CLI nebo Azure PowerShell, nebo Azure Portal. 

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: Správa přístupu k prostředkům Azure IoT Hub je řízena prostřednictvím služby Azure Active Directory (Azure AD). Azure AD nemá koncept výchozích hesel.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu.

Můžete také povolit přístup za běhu k účtům pro správu pomocí Azure AD Privileged Identity Management a Azure Resource Manager.

- [Další informace o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné** materiály: pro uživatele, kteří přistupují k IoT Hub, použijte Azure Active Directory jednotné přihlašování. Použijte Azure Security Center doporučení pro identitu a přístup. 

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: povolení Azure AD MFA k ochraně vašeho celkového tenanta Azure a výhod všech služeb. Služba IoT Hub nemá podporu MFA.  

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: Použijte zabezpečenou pracovní stanici spravovanou v Azure (také známou jako pracovní stanice s privilegovaným přístupem nebo privilegovaným přístupem) pro úlohy správy, které vyžadují zvýšená oprávnění.

- [Pochopení zabezpečení pracovních stanic spravovaných Azure](../active-directory/devices/concept-azure-managed-workstation.md)

- [Jak povolit Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití sestav a monitorování zabezpečení Azure Active Directory k detekci, kdy dojde k podezřelé nebo nebezpečné aktivitě v prostředí. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)
- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Doprovodné** materiály: pro uživatele, kteří přistupují k IoT Hub, není podmíněný přístup podporován. Pokud to chcete zmírnit, pomocí pojmenovaných umístění Azure AD povolte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí/oblastí pro váš celkový tenant Azure a využívání všechny služby, včetně IoT Hub. 

- [Jak nakonfigurovat pojmenovaná umístění služby Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: pro přístup uživatelů k IoT Hub použijte jako centrální ověřování a systém autorizaci Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Pro přístup k zařízením a službám používá IoT Hub k ověřování zařízení a služeb tokeny zabezpečení a tokeny sdíleného přístupového podpisu (SAS), aby se předešlo posílání klíčů v síti. 

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub tokeny zabezpečení](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure AD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. K efektivní správě členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí můžete navíc použít kontroly identity a přístupu v Azure AD. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. 

Pokud v prostředí dojde k podezřelé nebo nebezpečné aktivitě, použijte Azure AD Privileged Identity Management (PIM) pro generování protokolů a výstrah.

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)
- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)
- [Nasazení Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: máte přístup ke zdrojům přihlašovacích aktivit, auditem a rizikovým protokolům událostí Azure AD, které vám umožní integraci s jakýmkoli nástrojem Siem/monitoring. 

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics. 

Uživatelské Azure Monitor protokoly prostředků pro monitorování neautorizovaných pokusů o připojení v kategorii připojení.

- [Jak integrovat protokoly aktivit Azure pomocí Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Konfigurace protokolů zdrojů pro Centrum IoT](monitor-iot-hub.md#collection-and-routing)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Pokyny**: pomocí Azure AD identity Protectionch funkcí můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.
  
- [ Jak zobrazit rizikové přihlašování Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
  
- [ Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
  
- [ Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: ve scénářích podpory, kde Microsoft potřebuje získat přístup k zákaznickým datům, bude vyžádána přímo od zákazníka. 

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.
 
- [ Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: Implementujte izolaci pomocí samostatných předplatných a skupin pro správu pro jednotlivé domény zabezpečení, jako je například typ prostředí a úroveň citlivosti dat. Můžete omezit úroveň přístupu k prostředkům Azure, které vaše aplikace a podniková prostředí vyžadují. Přístup k prostředkům Azure můžete řídit prostřednictvím Azure RBAC.
  
- [ Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)
- [ Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)
- [ Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny**: použití řešení třetí strany z Azure Marketplace v hraničních sítích pro monitorování neoprávněného přenosu citlivých informací a zablokování takových přenosů při upozornění na odborníky na zabezpečení informací.

Pro základní platformu spravovanou Microsoftem Microsoft považuje veškerý obsah zákazníka za citlivý a chrání před ztrátou a únikem informací o zákaznících. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: IoT Hub používá protokol TLS (Transport Layer Security) k zabezpečení připojení ze zařízení a služeb IoT. V současné době jsou podporovány tři verze protokolu TLS, a to verze 1,0, 1,1 a 1,2. Při připojování k IoT Hub důrazně doporučujeme použít TLS 1,2 jako upřednostňovanou verzi protokolu TLS.

Pokud je to možné, postupujte podle Azure Security Center doporučení pro šifrování v klidovém režimu a šifrování.

- [Podpora TLS v IoT Hub](iot-hub-tls-support.md)
- [Pochopení šifrování při přenosu pomocí Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou pro Azure IoT Hub k dispozici. Pokud je to potřeba pro účely dodržování předpisů, implementujte řešení třetí strany.

V případě základní platformy Azure spravované Microsoftem považuje Microsoft veškerý obsah zákazníka za citlivý a vede k velkým délkám, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: ke správě přístupu k prostředkům použijte službu Azure RBAC.

**Doprovodné** materiály: přístup k řízení přístupu pomocí Azure RBAC pro řízení přístupu k IoT Hub. Pro přístup k IoT Hub pro rovinu dat použijte zásady sdíleného přístupu pro IoT Hub.

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Řízení přístupu k IoT Hubu](iot-hub-devguide-security.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

Microsoft spravuje základní infrastrukturu pro Azure IoT Hub a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: IoT Hub podporuje šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem (CMK), označovaných také jako "Přineste si vlastní klíč" (BYOK).

Azure IoT Hub poskytuje šifrování neaktivních a přenášených dat, která se zapisují v našich datacentrech, a při přístupu k nim dešifruje. Ve výchozím nastavení používá IoT Hub k šifrování neaktivních dat klíče spravované společností Microsoft.

- [Šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem pro IoT Hub](iot-hub-customer-managed-keys.md)

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Azure IoT Hub a dalších důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: nepoužitelné; Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Azure IoT Hub.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: nepoužitelné; Microsoft provádí správu oprav v základních systémech, které podporují Azure IoT Hub. 

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizované řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek u prostředků Azure (ne všechny prostředky podporují značky, ale nejvíc) k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry tam, kde je to vhodné, k uspořádání a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.
  
- [ Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)
  
- [ Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)
  
- [ Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

Každý IoT Hub má registr identit, který se dá použít k vytvoření prostředků pro jednotlivé zařízení ve službě. Do seznamu povolených nebo blokovaných identit zařízení můžete přidat jednotlivé identity nebo skupiny a povolit tak úplnou kontrolu nad přístupem k zařízení.

- [IoT Hub registr identit](iot-hub-devguide-identity-registry.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných. 

Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

* Žádné povolené typy prostředků
* Povolené typy prostředků

K dotazování a zjišťování prostředků v rámci předplatných navíc použijte graf prostředků Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure AD omezte schopnost uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.
  
- [ Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro službu Azure IoT Hub pomocí Azure Policy. Použijte aliasy Azure Policy v oboru názvů "Microsoft. Devices" k vytvoření vlastních zásad pro auditování nebo prosazování konfigurace služeb Azure IoT Hub.

Azure Resource Manager má možnost Exportovat šablonu do JavaScript Object Notation (JSON), která by měla být přezkoumána, aby konfigurace splňovala požadavky na zabezpečení vaší organizace.

Můžete také použít doporučení z Azure Security Center jako standardní hodnoty konfigurace pro prostředky Azure.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure. Kromě toho můžete použít šablony Azure Resource Manager k údržbě konfigurace zabezpečení vašich prostředků Azure, které vaše organizace vyžaduje.  
 
- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)
- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)
- [Přehled šablon Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy pro Azure IoT Hub nebo související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)
- [Dokumentace k Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů "Microsoft. Devices" k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Jak používat aliasy](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: použijte Azure Security Center k provádění kontrol standardních hodnot vašich prostředků Azure. Kromě toho použijte Azure Policy k upozornění a auditování konfigurace prostředků Azure. 
 
- [ Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Pokyny**: IoT Hub používá tokeny zabezpečení a tokeny sdíleného přístupového podpisu (SAS) k ověřování zařízení a služeb, aby nedocházelo k posílání klíčů v síti. 

Spravované identity používejte společně s Azure Key Vault k zjednodušení správy tajných kódů pro vaše cloudové aplikace.

- [IoT Hub tokeny zabezpečení](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Jak používat spravované identity pro IoT Hub](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

- [Vytvoření trezoru klíčů](../key-vault/general/quick-create-portal.md)
- [Jak zajistit Key Vault ověřování pomocí spravované identity](../key-vault/general/assign-access-policy-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: IoT Hub používá tokeny zabezpečení a tokeny sdíleného přístupového podpisu (SAS) k ověřování zařízení a služeb, aby nedocházelo k posílání klíčů v síti. 

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů v kódu.

- [IoT Hub tokeny zabezpečení](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Postup konfigurace spravovaných identit pro IoT Hub](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 
 
- [  Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Používejte centrálně spravovaný antimalwarový software

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na zákaznickém obsahu.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure IoT Hub), ale neběží na zákaznickém obsahu.

Je vaše zodpovědnost za to, že se veškerý obsah nahrává do prostředků Azure, které nejsou ve výpočetním prostředí, předem. Microsoft nemůže získat přístup k zákaznickým datům, a proto nemůže za vás provádět kontrolu proti malwarovým kontrolám zákaznického obsahu.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované

**Doprovodné** materiály: nepoužitelné; Tento srovnávací test je určený pro výpočetní prostředky. Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure, ale neběží na zákaznickém obsahu.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: služba Azure IoT Hub Service poskytuje metody a rozhraní, díky kterým budou vaše služby IoT Hub vysoce dostupné a obnovitelné z havárií na základě konkrétních obchodních cílů. 

- [Vysoká dostupnost služby IoT Hub a zotavení po havárii](iot-hub-ha-dr.md)

- [Postup klonování IoT Hub](iot-hub-how-to-clone.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: Azure IoT Hub doporučuje, aby sekundární centrum IoT obsahovalo všechny identity zařízení, které se můžou k řešení připojit. Řešení by mělo uchovávat geograficky replikované zálohy identit zařízení a odeslat je do sekundárního služby IoT Hub předtím, než přepnete aktivní koncový bod pro daná zařízení. V tomto kontextu je užitečná funkce exportu identity zařízení IoT Hub.

- [Vysoká dostupnost služby IoT Hub a zotavení po havárii](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [Export identity IoT Hub zařízení](iot-hub-bulk-identity-mgmt.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny**: Azure IoT Hub doporučuje, aby sekundární centrum IoT obsahovalo všechny identity zařízení, které se můžou k řešení připojit. Řešení by mělo uchovávat geograficky replikované zálohy identit zařízení a odeslat je do sekundárního služby IoT Hub předtím, než přepnete aktivní koncový bod pro daná zařízení. V tomto kontextu je užitečná funkce exportu identity zařízení IoT Hub.

Pravidelně provádějte obnovování dat obsahu v zálohování. Ujistěte se, že můžete obnovit zálohovaných klíčů spravovaných zákazníkem.

- [Vysoká dostupnost služby IoT Hub a zotavení po havárii](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [Export identity IoT Hub zařízení](iot-hub-bulk-identity-mgmt.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: povolení ochrany před náhodným odstraněním a vyprázdněním v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění. Pokud se pro ukládání záloh používá Azure Storage, povolte obnovitelné odstranění, abyste mohli data ukládat a obnovovat při odstraňování objektů BLOB nebo snímků objektů BLOB.
 
 
- [Pochopení Azure RBAC](../role-based-access-control/overview.md)
- [Obnovitelné odstranění pro Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: vývoj Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování incidentů a správu, které se mají zjišťovat, na kontrolu po jednotlivých událostech. 
  
- [ Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [ Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [  Pomoc při tvorbě vlastního plánu odpovědí na incidenty najdete v příručce pro zpracování incidentů v počítači s NIST.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

  
 Navíc můžete označit odběry pomocí značek a vytvořit systém pojmenování k identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data. Je vaše zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu.
  
- [ Výstrahy zabezpečení v Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [ Použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a pak podle potřeby upravte plán odpovědí.
  
- [ Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.
  
- [ Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.
  
- [ Postup konfigurace průběžného exportu](../security-center/continuous-export.md)
 
- [ Jak streamovat výstrahy do Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovních postupů Azure Security Center k automatickému spouštění odpovědí na výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.
  
- [ Jak nakonfigurovat automatizaci pracovního postupu v Security Center](../security-center/workflow-automation.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)