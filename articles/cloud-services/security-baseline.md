---
title: Základní hodnoty zabezpečení Azure pro Azure Cloud Services
description: Základní hodnoty zabezpečení služby Azure Cloud Services poskytují pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená ve srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e6a42a39e55e379865332fce8a9aed8dfe78f57b
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201327"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Základní hodnoty zabezpečení Azure pro Azure Cloud Services

Tato základní hodnota zabezpečení platí pro Microsoft Azure Cloud Services pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Cloud Services. **Ovládací prvky** , které se nevztahují k Cloud Services byly vyloučeny.

 
Pokud chcete zjistit, jak Cloud Services kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Cloud Services Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: vytvořte klasický Virtual Network Azure s oddělenými veřejnými a soukromými podsítěmi, abyste vynutili izolaci na základě důvěryhodných portů a ROZSAHŮ IP adres. Tyto virtuální sítě a podsítě musí být prostředky založené na klasickém Virtual Network (klasické nasazení), nikoli aktuální prostředky Azure Resource Manager.  

Povolte nebo zakažte provoz pomocí skupiny zabezpečení sítě, která obsahuje pravidla řízení přístupu založená na směru přenosu, protokolu, zdrojové adrese a portu a cílové adrese a portu. Pravidla skupiny zabezpečení sítě lze kdykoli změnit a změny jsou aplikovány na všechny přidružené instance.

Microsoft Azure Cloud Services (Classic) nelze umístit do Azure Resource Managerch virtuálních sítí. Virtuální sítě založené na Správce prostředků a klasické virtuální sítě založené na nasazení však lze připojit prostřednictvím partnerského vztahu. 

- [Přehled skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md)

- [Partnerský vztah virtuálních sítí](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Doprovodné** materiály: dokumentovat konfiguraci Azure Cloud Services a monitorovat změny. Pomocí konfiguračního souboru služby určete počet instancí rolí, které se mají nasadit pro každou roli ve službě, hodnoty všech nastavení konfigurace a kryptografické otisky pro všechny certifikáty, které jsou přidružené k roli. 

Pokud je služba součástí virtuální sítě, musí být v konfiguračním souboru služby k dispozici informace o konfiguraci sítě a také v konfiguračním souboru virtuální sítě. Výchozí rozšíření konfiguračního souboru služby je. cscfg. Všimněte si, že pro nasazení v klasickém nasazení není Azure Policy podporováno.

Nastavte konfigurační hodnoty cloudové služby v konfiguračním souboru služby (. cscfg) a definici v souboru definice služby (. csdef). Pomocí definičního souboru služby definujte model služby pro aplikaci. Definujte role, které jsou k dispozici pro cloudovou službu, a také zadejte koncové body služby. Protokoluje konfiguraci pro Azure Cloud Services pomocí konfiguračního souboru služby. Jakékoli překonfigurování lze provést prostřednictvím souboru ServiceConfig. cscfg. 

Sledujte volitelnou definici služby NetworkTrafficRules elementu, která omezuje, které role můžou komunikovat se zadanými interními koncovými body. Nakonfigurujte uzel NetworkTrafficRules, volitelný element v definičním souboru služby, abyste určili, jak by měly role vzájemně komunikovat. Umístěte omezení, které role mají přístup k interním koncovým bodům konkrétní role.  Všimněte si, že definice služby se nedá změnit. 

Povolte protokolování toku skupin zabezpečení sítě a odešlete protokoly do účtu Azure Storage k auditování. Odešlete protokoly toku do Log Analytics pracovního prostoru a pomocí Analýza provozu získáte přehledy o vzorech přenosu v tenantovi Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu, identifikovat aktivní body a bezpečnostní hrozby, pochopit vzorce toku přenosů a odhalit nesprávné konfigurace sítě.

- [Azure Resource Manager vs. klasické nasazení – pochopení modelů nasazení a stavu vašich prostředků](../azure-resource-manager/management/deployment-models.md)

- [Konfigurační soubor Cloud Services](schema-cscfg-file.md)

- [Seznam služeb podporovaných nástrojem Azure Policy](/cli/azure/azure-services-the-azure-cli-can-manage?preserve-view=)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Pokyny**: Společnost Microsoft používá protokol TLS (Transport Layer Security) v 1.2 k ochraně dat při cestování mezi Azure Cloud Services a zákazníky. Datová centra Microsoftu vyjednávají připojení TLS k klientským systémům, které se připojují ke službám Azure. Protokol TLS zajišťuje silné ověřování, soukromí zpráv a integritu (povolení detekce manipulace, zachycení a padělání zpráv), interoperability, flexibility algoritmů a snadné nasazení a použití.

- [Základy šifrování](../security/fundamentals/encryption-overview.md)

- [Konfigurace certifikátů TLS/SSL](cloud-services-configure-ssl-certificate-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: Azure Cloud implementuje zabezpečení sítě Multilayer a chrání své služby platforem před distribuovanými útoky DDoS (Denial of-Service). Azure DDoS Protection je součástí procesu nepřetržitého monitorování cloudu Azure, který se průběžně vylepšuje prostřednictvím testování průniku. Tato DDoS Protection je navržená tak, aby odolala nejen útokům z vnějšku, ale i z jiných tenantů Azure. 

Existuje několik různých způsobů, jak zablokovat nebo zamítnout komunikaci nad ochranou na úrovni platformy v rámci Azure Cloud Services. Jsou to: 

-  Vytvoření úlohy po spuštění pro selektivní blokování některých konkrétních IP adres
-  Omezení přístupu webové role Azure k sadě zadaných IP adres úpravou souboru web.config služby IIS

Zabraňte příchozímu provozu na výchozí adresu URL nebo název vaší Cloud Services, například *. cloudapp.NET. V části Konfigurace vazeb webu v souboru definice Cloud Services (. csdef) nastavte hlavičku hostitele na vlastní název DNS*.

Nakonfigurujte pravidlo Odepřít použití na klasické přiřazení správců předplatného. Ve výchozím nastavení platí, že po definování interního koncového bodu může komunikace směrovat z jakékoli role do interního koncového bodu role bez jakýchkoli omezení. Chcete-li omezit komunikaci, je nutné přidat element NetworkTrafficRules do elementu ServiceDefinition v souboru definice služby.

- [Jak můžu blokovat nebo zakázat příchozí provoz na výchozí adresu URL mé cloudové služby](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Ochrana Azure DDOS](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Blokování konkrétní IP adresy](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: Využijte Azure Network Watcher, monitorování výkonu sítě, diagnostiku a analytickou službu, která umožňuje monitorování sítí Azure. Rozšíření virtuálního počítače Network Watcher agenta je požadavkem na zachytávání síťového provozu na vyžádání a dalších pokročilých funkcí na Azure Virtual Machines. Nainstalujte rozšíření virtuálního počítače Network Watcher agenta a zapněte protokoly toku skupin zabezpečení sítě.

Nakonfigurujte protokolování toku ve skupině zabezpečení sítě. Přečtěte si podrobnosti o tom, jak nasadit rozšíření virtuálního počítače Network Watcher do existujícího virtuálního počítače nasazeného pomocí modelu nasazení Classic.

- [Konfigurace protokolování toku ve skupině zabezpečení sítě](../virtual-machines/extensions/network-watcher-linux.md)

- [Další informace o konfiguraci najdete v protokolech toku.](/cli/azure/azure-services-the-azure-cli-can-manage?preserve-view=)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: Azure Cloud Services nemá žádná předdefinovaná možnost ID nebo IP adres. Zákazníci můžou z Azure Marketplace vybrat a nasadit dodatečná síťová ID nebo řešení IP, a to na základě jejich organizačních požadavků. Při používání řešení třetích stran Nezapomeňte důkladně otestovat vybraná ID nebo řešení IP adres pomocí Azure Cloud Services, abyste zajistili správnou činnost a funkčnost.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: certifikáty služeb, které jsou připojené k Azure Cloud Services, umožňují zabezpečenou komunikaci do a ze služby. Tyto certifikáty jsou definované v definici služeb a automaticky se nasazují do virtuálního počítače, na kterém běží instance webové role. Například pro webovou roli můžete použít certifikát služby, který může ověřit vystavený koncový bod HTTPS. 

Chcete-li aktualizovat certifikát, je nutné odeslat nový certifikát a změnit hodnotu kryptografického otisku v konfiguračním souboru služby.

Použijte protokol TLS 1,2, nejčastěji používanou metodu zabezpečení dat, která zajišťuje ochranu proti utajení a ochraně integrity. 

Obecně platí, že k ochraně webových aplikací a jejich zabezpečení proti útokům, jako je OWASP Top 10, můžete nasadit Azure Application Gateway s podporou firewallu webových aplikací pro ochranu webových aplikací. 

- [Certifikáty služby](cloud-services-certs-create.md)

- [Konfigurace TLS pro aplikaci v Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Postup nasazení Application Gateway](../application-gateway/quick-create-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: posílení konfigurace Azure Cloud Services a sledování změn. Konfigurační soubor služby určuje počet instancí rolí, které mají být nasazeny pro každou roli ve službě, hodnoty nastavení konfigurace a kryptografické otisky pro všechny certifikáty přidružené k roli. 

Pokud je vaše služba součástí virtuální sítě, musí být v konfiguračním souboru služby k dispozici informace o konfiguraci sítě, a to i v konfiguračním souboru virtuální sítě. Výchozí rozšíření konfiguračního souboru služby je. cscfg.

Všimněte si, že Azure Policy není u Azure Cloud Services pro vynucení konfigurace podporována.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: skupiny zabezpečení sítě Azure je možné použít k filtrování síťového provozu do a z prostředků azure v Virtual Network Azure. Skupina zabezpečení sítě obsahuje pravidla zabezpečení, která povolují nebo odmítají příchozí síťový provoz do, nebo odchozí síťový provoz z několika typů prostředků Azure. Pro každé pravidlo můžete určit zdroj a cíl, port a protokol.

Pomocí pole Popis pro jednotlivá pravidla skupiny zabezpečení sítě v rámci Azure Cloud Services zdokumentujte pravidla, která umožňují provoz do sítě nebo ze sítě.

- [Postup filtrování síťového provozu pomocí pravidel skupiny zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Doprovodné** materiály: Využijte integrované funkce monitorování koncového bodu a automatického převzetí služeb při selhání služby Azure Traffic Manager. Pomůžou vám poskytovat vysoce dostupné aplikace, které jsou odolné vůči selháním koncových bodů a oblastí Azure. Pokud chcete nakonfigurovat monitorování koncového bodu, musíte zadat určitá nastavení v profilu Traffic Manager.

Získejte přehled z protokolu aktivit, protokolu platformy v Azure, do událostí na úrovni předplatného. Zahrnuje tyto informace, jako je například změna prostředku nebo spuštění virtuálního počítače. Zobrazit protokol aktivit v Azure Portal nebo načíst položky pomocí PowerShellu a rozhraní příkazového řádku. 

Vytvořte nastavení diagnostiky pro odeslání protokolu aktivit Azure Monitor, Azure Event Hubs k přeposílání mimo Azure nebo pro účely archivace Azure Storage. Nakonfigurujte Azure Monitor upozornění na oznámení v případě, že se změní kritické prostředky v Azure Cloud Services. 

- [Protokol aktivit Azure](../azure-monitor/platform/activity-log.md)

- [Vytváření, zobrazování a správa výstrah protokolu aktivit pomocí Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

- [Monitorování Traffic Manager](../traffic-manager/traffic-manager-monitoring.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Microsoft udržuje časové zdroje pro prostředky Azure pro Azure Cloud Services. Zákazníci možná budou muset vytvořit síťové pravidlo, které umožní přístup k časovému serveru používanému ve svém prostředí, a to přes port 123 s protokolem UDP.

- [Přístup k serveru NTP](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: využití dat streamování cloudové služby prostřednictvím programu Azure Event Hubs. Integrujte a odešlete všechna tato data do Azure Sentinel, abyste mohli monitorovat a kontrolovat protokoly nebo používat SIEM třetí strany. Pro centrální správu protokolů zabezpečení nakonfigurujte průběžný export vybraných Azure Security Center dat do Azure Event Hubs a nastavte příslušný konektor pro SIEM. Tady je několik možností pro Azure Sentinel včetně nástrojů třetích stran:

- Azure Sentinel – použít nativní datový konektor pro Security Center výstrahy
- Splunk – použití doplňku Azure Monitor pro Splunk
- IBM QRadar – použití ručně nakonfigurovaného zdroje protokolu
- ArcSight – použití SmartConnector

Další podrobnosti o dostupných konektorech pomocí Azure Sentinel najdete v dokumentaci k ověřovacím službám Azure. 

- [Připojení zdrojů dat](../sentinel/connect-data-sources.md)

- [Integrace s SIEM](../security-center/continuous-export.md)

- [Ukládat diagnostická data](diagnostics-extension-to-storage.md)

- [Konfigurace integrace SIEM pomocí Azure Event Hubs](../security-center/continuous-export.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: konfigurace Azure Diagnostics sady Visual Studio pro řešení potíží s Azure Cloud Services, která zachycuje data systému a protokolu na virtuálních počítačích, včetně instancí virtuálních počítačů, na kterých běží Azure Cloud Services. Diagnostická data se přenesou na účet úložiště podle vašeho výběru. Zapněte diagnostiku v projektech Azure Cloud Services před jejich nasazením.

 
Podívejte se na historii změn pro některé události v protokolu aktivit v rámci Azure Monitor. Auditujte, k jakým změnám došlo během časového období události. Vyberte událost z protokolu aktivit pro hlubší kontrolu pomocí karty historie změn (Preview). Když publikujete Cloud Services Azure ze sady Visual Studio, odešlete diagnostická data do Application Insights. Vytvořte v tuto chvíli prostředek Application Insights Azure nebo odešlete data do existujícího prostředku Azure. 

Službu Azure Cloud Services je možné monitorovat pomocí Application Insights dostupnosti, výkonu, selhání a využití. Vlastní grafy lze přidat do Application Insights tak, abyste viděli data, která jsou nejvíc nejdůležitější. Data instance role se dají shromažďovat pomocí Application Insights SDK v projektu Azure Cloud Services. 

- [Zapnout diagnostiku v aplikaci Visual Studio před nasazením](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?preserve-view=true#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Zobrazit historii změn](../azure-monitor/platform/activity-log.md#view-change-history)

- [Application Insights pro cloudovou službu Azure (Classic)](../azure-monitor/app/cloudservices.md)

- [Nastavení diagnostiky pro cloudovou službu Azure (Classic) a virtuální počítače](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?preserve-view=true&toc=%2fazure%2fcloud-services%2ftoc.json)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny**: můžete použít pokročilé monitorování s využitím Azure Cloud Services, které umožňuje vzorkování a shromažďování dalších metrik v intervalech 5 minut, 1 hodina a 12 hodin. Agregovaná data jsou uložená v účtu úložiště v tabulkách a vyprázdní se po 10 dnech. Použitý účet úložiště je ale nakonfigurovaný rolí a můžete pro různé role použít jiné účty úložiště. Tato konfigurace je nakonfigurována pomocí připojovacího řetězce v souborech. csdef a. cscfg.

Mějte na paměti, že rozšířené monitorování zahrnuje použití rozšíření Azure Diagnostics (sada SDK Application Insights je volitelná) u role, kterou chcete monitorovat. Diagnostické rozšíření pomocí konfiguračního souboru (na roli) s názvem Diagnostics. wadcfgx konfiguruje monitorované metriky diagnostiky. Diagnostické rozšíření Azure shromažďuje a ukládá data v Azure Storagem účtu. Tato nastavení jsou konfigurována v souborech. wadcfgx,. csdef a. cscfg.

- [Seznámení s monitorováním cloudové služby](cloud-services-how-to-monitor.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: základní nebo pokročilé režimy monitorování jsou k dispozici pro Azure Cloud Services. Azure Cloud Services automaticky shromažďuje základní data monitorování (procento využití procesoru, síťové zastavení/ven a čtení a zápis na disku) z hostitelského virtuálního počítače. Podívejte se na shromážděná data monitorování na stránkách přehled a metrika cloudové služby v Azure Portal. 

Povolte diagnostiku v Azure Cloud Services ke shromažďování diagnostických dat, jako jsou protokoly aplikací, čítače výkonu a další, při použití rozšíření Azure Diagnostics. Povolte nebo aktualizujte konfiguraci diagnostiky v cloudové službě, která už je spuštěná pomocí rutiny Set-AzureServiceDiagnosticsExtension, nebo automaticky nasaďte cloudovou službu s diagnostickým rozšířením. Volitelně nainstalujte sadu Application Insights SDK. Odešlete čítače výkonu pro Azure Monitor.

Diagnostické rozšíření Azure shromažďuje a ukládá data v Azure Storagem účtu. Přenos diagnostických dat do Emulátor úložiště Microsoft Azure nebo Azure Storage, protože nejsou trvale uloženy. Jednou v úložišti se dá zobrazit s jedním z několika dostupných nástrojů, jako je například Průzkumník serveru v aplikaci Visual Studio, Průzkumník služby Microsoft Azure Storage, Management Studio Azure. Nakonfigurujte diagnostické metriky, které se mají monitorovat, pomocí konfiguračního souboru (na roli) s názvem Diagnostics. wadcfgx v diagnostickém rozšíření. 

- [Seznámení s monitorováním cloudové služby](cloud-services-how-to-monitor.md)

- [Postup povolení diagnostiky v roli pracovního procesu – integrace s SIEM](../security-center/continuous-export.md)

- [Povolení diagnostiky v Azure Cloud Services s využitím PowerShellu](cloud-services-diagnostics-powershell.md)

- [Ukládání a zobrazení diagnostických dat v Azure Storage](diagnostics-extension-to-storage.md?&amp;preserve-view=true)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: data protokolu Azure Cloud Services můžete sledovat integrací s Azure Sentinel nebo s Siem třetí strany, a to díky tomu, že se povolí upozorňování na aktivity neobvyklé.

- [Integrace s SIEM](../security-center/continuous-export.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: Microsoft Antimalware pro Azure, který chrání Azure Cloud Services a virtuální počítače. Máte možnost kromě toho nasadit řešení zabezpečení od jiných výrobců, například brány fire wall webové aplikace, brány firewall sítě, antimalwarové, odhalování vniknutí a systémy prevence (ID nebo IP adresy) a další.

- [Jaké jsou funkce a možnosti, které poskytuje Azure Basic IP/IDS a DDOS](./cloud-services-configuration-and-management-faq.md?preserve-view=true#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Pokyny**: Společnost Microsoft doporučuje spravovat přístup k prostředkům Azure pomocí řízení přístupu na základě role Azure (RBAC). Azure Cloud Services ale nepodporuje model Azure RBAC, protože to není služba Azure Resource Manager založená na službě a Vy musíte použít klasické předplatné.

Ve výchozím nastavení jsou správce účtu, správce služeb a Co-Administrator tři klasické role Správce předplatného v Azure. 

Klasičtí správci předplatného mají úplný přístup k předplatnému Azure. Mohou spravovat prostředky pomocí portálu Azure Portal, rozhraní API Azure Resource Manageru a rozhraní API modelu nasazení Classic. Účet, který slouží k registraci v Azure, je automaticky nastaven jako účet správce účtu a správce služeb. Další Co-Administrators lze přidat později. 

Správce služby a Co-Administrators mají ekvivalentní přístup k uživatelům, kterým byla přiřazena role vlastníka (role Azure) v oboru předplatného. Správa Co-Administrators nebo zobrazení Správce služby pomocí karty Classic Administrators v Azure Portal. 

Seznam přiřazení rolí pro klasického správce služeb a spolusprávce pomocí PowerShellu pomocí příkazu:

Get-AzRoleAssignment – IncludeClassicAdministrators

Projděte si rozdíly mezi rolemi pro správu předplatného Classic. 

- [Rozdíly mezi třemi rolemi pro správu předplatného pro klasický odběr](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: doporučujeme vytvořit standardní operační postupy kolem používání vyhrazených účtů pro správu na základě dostupných rolí a oprávnění, která jsou nutná k provozování a správě prostředků Azure Cloud Services.

- [Rozdíly mezi rolemi správy pro klasický odběr](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Pokyny**: Nepoužívejte správu samostatných identit pro aplikace, které běží na Azure Cloud Services. Implementujte jednotné přihlašování, abyste se vyhnuli nutnosti uživatelům spravovat více identit a přihlašovacích údajů.

- [Co je jednotné přihlašování (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné** materiály: doporučujeme pro úlohy správy, které vyžadují zvýšená oprávnění, používat zabezpečenou pracovní stanici spravovanou Azure (známá taky jako pracovní stanice s privilegovaným přístupem).

- [Pochopení zabezpečení pracovních stanic spravovaných Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak povolit Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Pokyny**: k inventarizaci prostředků cloudové služby Azure pro citlivé informace použijte rozhraní REST API služby Azure Cloud Service. Dotaz na nasazené prostředky cloudové služby vám umožní získat prostředky konfigurace a. pkg.

 Například několik rozhraní API je uvedeno níže:

- Získat nasazení – operace získat nasazení vrátí informace o konfiguraci, stav a vlastnosti systému pro nasazení.
- Získat balíček – operace získat balíček načte balíček cloudové služby pro nasazení a uloží soubory balíčku do úložiště objektů BLOB v Microsoft Azure.
- Získat vlastnosti cloudové služby – operace získat vlastnosti cloudové služby načte vlastnosti pro zadanou cloudovou službu.

Projděte si dokumentaci rozhraní REST API služby Azure Cloud Service a vytvořte proces ochrany dat citlivých informací na základě požadavků vaší organizace.

- [Získání nasazení](/rest/api/compute/cloudservices/rest-get-deployment)

- [Získat vlastnosti cloudové služby](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Získat balíček](/rest/api/compute/cloudservices/rest-get-package)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: Implementujte izolaci pomocí samostatných předplatných a skupin pro správu pro jednotlivé domény zabezpečení, jako je typ prostředí a úroveň citlivosti dat pro Azure Cloud Services.

Můžete také upravit "permissionLevel" v prvku certifikátu cloudové služby Azure a zadat přístupová oprávnění k procesům rolí. Pokud chcete, aby přístup k privátnímu klíči měly jenom procesy se zvýšenými oprávněními, zadejte oprávnění vyšší úrovně. oprávnění limitedOrElevated umožňuje všem procesům rolí přístup k privátnímu klíči. Možné hodnoty jsou limitedOrElevated nebo vyšší úrovně. Výchozí hodnota je limitedOrElevated.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

- [Schéma WebRole](schema-csdef-webrole.md#Certificate)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: doporučuje se použít řešení od jiného výrobce z Azure Marketplace v síti, abyste mohli monitorovat neoprávněný přenos citlivých informací a blokovat tyto přenosy při upozorňování na odborníky na zabezpečení informací.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Sdílené

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Konfigurace TLS v2 pro Azure Cloud Services. Pomocí Azure Portal přidejte certifikát do připraveného nasazení Azure Cloud Services a přidejte informace o certifikátu do souborů CSDEF a CSCFG služby. Znovu zabalit aplikaci a aktualizujte připravené nasazení na použití nového balíčku. 

Pomocí certifikátů služby v Azure, které jsou připojené k Azure Cloud Services k zajištění zabezpečené komunikace do a ze služby. Zadejte certifikát, který může ověřit vystavený koncový bod HTTPS. V definici služby cloudové služby definujte certifikáty služeb a automaticky je nasaďte na virtuální počítač a spusťte instanci vaší role.

Ověřování pomocí certifikátů API pro správu s certifikáty pro správu) umožňují ověřování pomocí modelu nasazení Classic. Mnoho programů a nástrojů (například Visual Studio nebo Azure SDK) používá tyto certifikáty k automatizaci konfigurace a nasazení různých služeb Azure. 

Pro další informace rozhraní API pro správu služeb Azure poskytuje programový přístup k funkcím správy služeb, které jsou dostupné prostřednictvím Azure Portal. Sadu Azure SDK pro Python můžete použít ke správě účtů Azure Cloud Services a Azure Storage. Sada Azure SDK for Python zalomí rozhraní API pro správu služeb, REST API. Všechny operace rozhraní API se provádí přes protokol TLS a vzájemně se ověřují pomocí certifikátů X. 509 v3. Služba pro správu je k dispozici v rámci služby spuštěné v Azure. Dá se taky získat přímo přes Internet z libovolné aplikace, která může poslat požadavek HTTPS a přijmout odpověď HTTPS.

- [Konfigurace TLS pro aplikaci v Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Použití správy služeb z Pythonu](cloud-services-python-how-to-use-service-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: doporučujeme použít nástroj pro aktivní zjišťování třetí strany k identifikaci všech citlivých informací uložených, zpracovávaných nebo odeslaných technologickými systémy organizace, včetně těch, které jsou umístěné na pracovišti nebo u poskytovatele vzdálené služby, a pak aktualizují inventář citlivých informací organizace.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Sdílené

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: neplatí pro cloudovou službu (Classic). Nezajišťuje ochranu před únikem informací.

Doporučuje se implementovat nástroj třetí strany, jako je automatizované řešení ochrany před únikem informací, které vynutilo řízení přístupu k datům i v případě, že se data zkopírují mimo systém.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby bylo zajištěno zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Sdílené

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: Azure Cloud Services nepodporuje šifrování v klidovém umístění. Je to proto, že Azure Cloud Services je navržený tak, aby byl bezstavový. Azure Cloud Services podporuje externí úložiště, například Azure Storage, které je ve výchozím nastavení šifrované a šifrované v klidovém stavu.  

Data aplikace uložená na dočasných discích nejsou šifrována. Zákazník je zodpovědný za správu a šifrování těchto dat podle potřeby.  

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md)

**Monitorování služby Azure Security Center:** Neuvedeno

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: klasické výstrahy metriky v Azure monitor můžete použít k oznámení, když se jedna z vašich metrik aplikuje na kritické prostředky v překročení prahové hodnoty. Klasické výstrahy metriky jsou starší funkce, které umožňují upozorňování jenom na nedimenzionální metriky. Existují nové funkce s názvem výstrahy metriky, které mají vylepšené funkce pro klasické výstrahy metrik. 

Kromě toho Application Insights můžou monitorovat aplikace Azure Cloud Services z důvodu dostupnosti, výkonu, selhání a využití. Používá kombinovaná data z Application Insights sad SDK a Azure Diagnostics data z Azure Cloud Services.

- [Vytváření, zobrazování a Správa klasických výstrah metrik pomocí Azure Monitor](../azure-monitor/platform/alerts-classic-portal.md)

- [Přehled výstrah metrik](../azure-monitor/platform/alerts-metric-overview.md) 

- [Application Insights pro cloudovou službu Azure (Classic)](../azure-monitor/app/cloudservices.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: tyto informace se vztahují k hostovanému operačnímu systému Azure pro službu Azure Cloud Services Worker a webové role s platformou as a Service (PaaS). Netýká se ale Virtual Machines s infrastrukturou jako službou (IaaS).

Ve výchozím nastavení Azure pravidelně aktualizuje hostovaný operační systém zákazníka na nejnovější podporovanou image v rámci řady operačních systémů, kterou zadala v konfiguraci služby (. cscfg), jako je například Windows Server 2016.

Když zákazník zvolí konkrétní verzi operačního systému pro své nasazení služby Azure Cloud Services, zakáže automatické aktualizace operačního systému a provede opravu jejich zodpovědnosti. Zákazník musí zajistit, aby jejich instance role přijímaly aktualizace, nebo mohly své aplikace vystavit chybám zabezpečení.

- [Hostovaný operační systém Azure](cloud-services-guestos-msrc-releases.md)

- [Zásady podpory a vyřazení hostovaného operačního systému Azure](cloud-services-guestos-retirement-policy.md)

- [Jak nakonfigurovat cloudovou službu (Classic)](cloud-services-how-to-configure-portal.md)

- [Spravovat verzi hostovaného operačního systému](cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizované řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné** materiály: použijte řešení pro správu oprav třetí strany. Zákazníci, kteří už používají Configuration Manager ve svém prostředí, můžou používat taky System Center Updates Publisher, aby jim mohli publikovat vlastní aktualizace do služby Windows Server Update Service. 

To umožňuje Update Management opravit počítače, které používají Configuration Manager jako úložiště aktualizací se softwarem třetích stran.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: Doporučujeme, aby zákazník porozuměl rozsahu jejich rizika DDoS útoku. 

V těchto scénářích doporučujeme promyslet si:

- Jaké nové veřejně dostupné prostředky Azure potřebují chránit?
- Je ve službě jediný bod selhání?
- Jak se dají izolovat služby, aby se omezil dopad útoku, zatímco stále jsou dostupné služby pro platné zákazníky?
- Existují virtuální sítě, ve kterých by měl být povolený DDoS Protection Standard, ale ne?
- Jsou moje služby aktivní/aktivní s převzetím služeb při selhání napříč více oblastmi?

Podpůrná dokumentace:

- [Hodnocení rizik vašich prostředků Azure](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Doprovodné** materiály: neplatí pro Azure Cloud Services. Toto doporučení se týká IaaS výpočetních prostředků.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: doporučuje se pravidelně sjednotit inventář a včas zajistit odstranění neautorizovaných prostředků z předplatného.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: zákazník by měl definovat schválené prostředky Azure a schválený software pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte funkci adaptivního řízení aplikací, která je dostupná v Azure Security Center. Jedná se o inteligentní, automatizované a komplexní řešení od Security Center, které vám pomůže určit, které aplikace se můžou spouštět na počítačích s Windows a Linux, Azure a mimo Azure. Pomáhá také posílit zabezpečení vašich počítačů proti malwaru. 

Tato funkce je k dispozici pro Azure i mimo Azure Windows (všechny verze, Classic nebo Azure Resource Manager) a počítače se systémem Linux.

Security Center využívá strojové učení k analýze aplikací spuštěných na vašich počítačích a k vytvoření seznamu povolených z těchto inteligentních funkcí. Tato funkce značně zjednodušuje proces konfigurace a správy zásad seznamu povolených aplikací, což vám umožní:
- Blokuje nebo upozorňuje na pokusy o spuštění škodlivých aplikací, včetně těch, které by jinak mohly chybět antimalwarová řešení.

- Dodržovat zásady zabezpečení vaší organizace, které vyžadují používání pouze licencovaného softwaru.
- Zamezit používání nežádoucího softwaru ve vašem prostředí.
- Zamezit spouštění starých a nepodporovaných aplikací.
- Zabránit používání konkrétních softwarových nástrojů, které nejsou ve vaší organizaci povolené.
- Umožnit IT oddělení řídit přístup k citlivým datům prostřednictvím používání aplikace.

Další podrobnosti jsou k dispozici na odkazovaných odkazech.

- [Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: použijte funkci adaptivního řízení aplikací, která je dostupná v Azure Security Center. Jedná se o inteligentní, automatizované a komplexní řešení od Security Center, které vám pomůže určit, které aplikace se můžou spouštět na počítačích s Windows a Linux, Azure a mimo Azure. Pomáhá také posílit zabezpečení vašich počítačů proti malwaru. 

Tato funkce je k dispozici pro Azure i mimo Azure Windows (všechny verze, Classic nebo Azure Resource Manager) a počítače se systémem Linux.

Security Center využívá strojové učení k analýze aplikací spuštěných na vašich počítačích a k vytvoření seznamu povolených z těchto inteligentních funkcí. Tato funkce značně zjednodušuje proces konfigurace a správy zásad seznamu povolených aplikací, což vám umožní:

- Blokuje nebo upozorňuje na pokusy o spuštění škodlivých aplikací, včetně těch, které by jinak mohly chybět antimalwarová řešení.

- Dodržovat zásady zabezpečení vaší organizace, které vyžadují používání pouze licencovaného softwaru.

- Zamezit používání nežádoucího softwaru ve vašem prostředí.

- Zamezit spouštění starých a nepodporovaných aplikací.

- Zabránit používání konkrétních softwarových nástrojů, které nejsou ve vaší organizaci povolené.

- Umožnit IT oddělení řídit přístup k citlivým datům prostřednictvím používání aplikace.

Další podrobnosti jsou k dispozici na odkazovaných odkazech.

- [Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: použijte funkci adaptivního řízení aplikací, která je dostupná v Azure Security Center. Jedná se o inteligentní, automatizované a komplexní řešení od Security Center, které vám pomůže určit, které aplikace se můžou spouštět na počítačích s Windows a Linux, Azure a mimo Azure. Pomáhá také posílit zabezpečení vašich počítačů proti malwaru. 

Tato funkce je k dispozici pro Azure i mimo Azure Windows (všechny verze, Classic nebo Azure Resource Manager) a počítače se systémem Linux.

Security Center využívá strojové učení k analýze aplikací spuštěných na vašich počítačích a k vytvoření seznamu povolených z těchto inteligentních funkcí. Tato funkce značně zjednodušuje proces konfigurace a správy zásad seznamu povolených aplikací, což vám umožní:

- Blokuje nebo upozorňuje na pokusy o spuštění škodlivých aplikací, včetně těch, které by jinak mohly chybět antimalwarová řešení.

- Dodržovat zásady zabezpečení vaší organizace, které vyžadují používání pouze licencovaného softwaru.

- Zamezit používání nežádoucího softwaru ve vašem prostředí.

- Zamezit spouštění starých a nepodporovaných aplikací.

- Zabránit používání konkrétních softwarových nástrojů, které nejsou ve vaší organizaci povolené.

- Umožnit IT oddělení řídit přístup k citlivým datům prostřednictvím používání aplikace.

Další podrobnosti jsou k dispozici na odkazovaných odkazech.

- [Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: použijte funkci adaptivního řízení aplikací, která je dostupná v Azure Security Center. Jedná se o inteligentní, automatizované a komplexní řešení od Security Center, které vám pomůže určit, které aplikace se můžou spouštět na počítačích s Windows a Linux, Azure a mimo Azure. Pomáhá také posílit zabezpečení vašich počítačů proti malwaru. 

Tato funkce je k dispozici pro Azure i mimo Azure Windows (všechny verze, Classic nebo Azure Resource Manager) a počítače se systémem Linux.

Security Center využívá strojové učení k analýze aplikací spuštěných na vašich počítačích a k vytvoření seznamu povolených z těchto inteligentních funkcí. Tato funkce značně zjednodušuje proces konfigurace a správy zásad seznamu povolených aplikací, což vám umožní:

- Blokuje nebo upozorňuje na pokusy o spuštění škodlivých aplikací, včetně těch, které by jinak mohly chybět antimalwarová řešení.

- Dodržovat zásady zabezpečení vaší organizace, které vyžadují používání pouze licencovaného softwaru.

- Zamezit používání nežádoucího softwaru ve vašem prostředí.

- Zamezit spouštění starých a nepodporovaných aplikací.

- Zabránit používání konkrétních softwarových nástrojů, které nejsou ve vaší organizaci povolené.

- Umožnit IT oddělení řídit přístup k citlivým datům prostřednictvím používání aplikace.

Další podrobnosti jsou k dispozici na odkazovaných odkazech.

- [Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: použijte funkci adaptivního řízení aplikací, která je dostupná v Azure Security Center. Jedná se o inteligentní, automatizované a komplexní řešení od Security Center, které vám pomůže určit, které aplikace se můžou spouštět na počítačích s Windows a Linux, Azure a mimo Azure. Pomáhá také posílit zabezpečení vašich počítačů proti malwaru. 

Tato funkce je k dispozici pro Azure i mimo Azure Windows (všechny verze, Classic nebo Azure Resource Manager) a počítače se systémem Linux.

Security Center využívá strojové učení k analýze aplikací spuštěných na vašich počítačích a k vytvoření seznamu povolených z těchto inteligentních funkcí. Tato funkce značně zjednodušuje proces konfigurace a správy zásad seznamu povolených aplikací, což vám umožní:
- Blokuje nebo upozorňuje na pokusy o spuštění škodlivých aplikací, včetně těch, které by jinak mohly chybět antimalwarová řešení.

- Dodržovat zásady zabezpečení vaší organizace, které vyžadují používání pouze licencovaného softwaru.
- Zamezit používání nežádoucího softwaru ve vašem prostředí.
- Zamezit spouštění starých a nepodporovaných aplikací.
- Zabránit používání konkrétních softwarových nástrojů, které nejsou ve vaší organizaci povolené.
- Umožnit IT oddělení řídit přístup k citlivým datům prostřednictvím používání aplikace.

Další podrobnosti jsou k dispozici na odkazovaných odkazech.

- [Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: použijte funkci adaptivního řízení aplikací, která je dostupná v Azure Security Center. Jedná se o inteligentní, automatizované a komplexní řešení od Security Center, které vám pomůže určit, které aplikace se můžou spouštět na počítačích s Windows a Linux, Azure a mimo Azure. Pomáhá také posílit zabezpečení vašich počítačů proti malwaru. 

Tato funkce je k dispozici pro Azure i mimo Azure Windows (všechny verze, Classic nebo Azure Resource Manager) a počítače se systémem Linux.

Security Center využívá strojové učení k analýze aplikací spuštěných na vašich počítačích a k vytvoření seznamu povolených z těchto inteligentních funkcí. Tato funkce značně zjednodušuje proces konfigurace a správy zásad seznamu povolených aplikací, což vám umožní:

- Blokuje nebo upozorňuje na pokusy o spuštění škodlivých aplikací, včetně těch, které by jinak mohly chybět antimalwarová řešení.

- Dodržovat zásady zabezpečení vaší organizace, které vyžadují používání pouze licencovaného softwaru.

- Zamezit používání nežádoucího softwaru ve vašem prostředí.

- Zamezit spouštění starých a nepodporovaných aplikací.

- Zabránit používání konkrétních softwarových nástrojů, které nejsou ve vaší organizaci povolené.

- Umožnit IT oddělení řídit přístup k citlivým datům prostřednictvím používání aplikace.

Další podrobnosti jsou k dispozici na odkazovaných odkazech.

- [Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: u citlivých nebo vysoce rizikových aplikací s Azure Cloud Services implementujte samostatné odběry nebo skupiny pro správu, které zajišťují izolaci.

Použijte skupinu zabezpečení sítě, vytvořte příchozí pravidlo zabezpečení, vyberte službu, jako je http, vyberte vlastní port a zadejte prioritu a název. Priorita má vliv na pořadí, ve kterém jsou pravidla použita, čím nižší je číselná hodnota, tím se pravidlo použije dříve. K izolaci nebo segmentaci síťového provozu podle vašich obchodních potřeb budete muset přidružit skupinu zabezpečení sítě k podsíti nebo konkrétnímu síťovému rozhraní.

Další podrobnosti jsou k dispozici na odkazovaných odkazech.

- [Kurz – filtrování síťového provozu pomocí skupiny zabezpečení sítě pomocí Azure Portal](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: použijte doporučení z Azure Security Center jako standardní hodnoty konfigurace pro prostředky Azure Cloud Services. 

Na Azure Portal zvolte Security Center a pak výpočetní &amp; aplikace a Azure Cloud Services, abyste viděli doporučení týkající se vašich prostředků služby.

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: neplatí pro Azure Cloud Services. Vychází z modelu nasazení Classic. Pro zachování zabezpečených konfigurací prostředků Azure se doporučuje použít řešení třetí strany.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: konfigurační soubor cloudové služby Azure ukládá atributy operačního systému pro prostředek. Můžete uložit kopii konfiguračních souborů do účtu zabezpečeného úložiště.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: neplatí pro Azure Cloud Services. Vychází z modelu nasazení Classic a nelze ho spravovat nástrojem Azure Resource Manager konfigurační nástroje založené na nasazení.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné** materiály: neplatí pro Azure Cloud Services. Toto doporučení se vztahuje na výpočetní prostředky založené na infrastruktuře jako služby (IaaS).

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: použijte Azure Security Center k provádění kontrol standardních hodnot vašich prostředků Azure.  

- [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: v Azure Security Center vyberte funkce COMPUTE &amp; Apps a použijte doporučení pro virtuální počítače, servery a kontejnery.

- [Porozumění doporučení týkajících se kontejnerů Azure Security Center](../security-center/container-security.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Pokyny**: Azure Cloud Services je založen na modelu nasazení Classic a Neintegruje se s Azure Key Vault.

Můžete zabezpečit tajné kódy, jako jsou přihlašovací údaje, které se používají v Azure Cloud Services, takže není nutné zadávat heslo pokaždé. Pokud chcete začít, zadejte heslo pro prostý text, převeďte ho na zabezpečený řetězec pomocí příkazu ConvertTo-SecureString, PowerShellu. Dále převeďte tento zabezpečený řetězec na zašifrovaný standardní řetězec pomocí ConvertFrom-SecureString.  Tento zašifrovaný standardní řetězec teď můžete uložit do souboru pomocí Set-content.

Kromě toho se doporučuje ukládat privátní klíče pro certifikáty používané v Azure Cloud Services do zabezpečeného úložiště.

- [Konfigurace vzdálené plochy z PowerShellu](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Doprovodné** materiály: zabezpečení tajných kódů, jako jsou přihlašovací údaje používané v Azure Cloud Services, takže není nutné zadávat heslo pokaždé. 
 

Začněte tím, že zadáte heslo pro prostý text, změníte ho na zabezpečený řetězec pomocí příkazu ConvertTo-SecureString, PowerShellu. Dále převeďte tento zabezpečený řetězec na zašifrovaný standardní řetězec pomocí ConvertFrom-SecureString. Nyní uložte tento zašifrovaný standardní řetězec do souboru pomocí příkazu Set-Content.

Soukromé klíče uložte pro certifikáty používané v Azure Cloud Services do zabezpečeného umístění úložiště.

- [Konfigurace vzdálené plochy z PowerShellu](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Používejte centrálně spravovaný antimalwarový software

**Doprovodné** materiály: Microsoft Antimalware pro Azure je dostupný pro Azure Cloud Services a Virtual Machines. Je to bezplatná ochrana v reálném čase, která pomáhá identifikovat a odstraňovat viry, spyware a další škodlivý software. Vygeneruje výstrahy v případě, že se známý škodlivý nebo nežádoucí software pokusí nainstalovat nebo spustit v systémech Azure. 

Pomocí rutiny antimalwaru založeného na prostředí PowerShell získáte antimalwarovou konfiguraci pomocí rutiny Get-AzureServiceAntimalwareConfig.

Povolte antimalwarové rozšíření pomocí skriptu PowerShellu v úloze po spuštění v Azure Cloud Services.

Vyberte funkci adaptivního řízení aplikací v Azure Security Center inteligentní, automatizované a komplexní řešení. Pomáhá posílit zabezpečení vašich počítačů proti malwaru a umožňuje blokovat nebo upozorňovat na pokusy o spuštění škodlivých aplikací, včetně těch, které by jinak mohly chybět antimalwarová řešení.

- [Jak můžu pomocí automatizovaného způsobu přidat antimalwarové rozšíření pro můj Cloud Services Azure](./cloud-services-configuration-and-management-faq.md?preserve-view=true#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Scénáře nasazení antimalwaru](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění. 

Jasně označte předplatná (například produkci, neprodukční) a vytvořte názvový systém, který bude jasně identifikovat a kategorizovat prostředky Azure.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán. 

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy. 

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Security Center Data můžete streamovat výstrahy do Azure Sentinel. 

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md) 

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: Využijte pravidla zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [V této části najdete další informace o strategii Microsoftu a provádění testování v rámci červeného seskupování a testování průniku na cloudové infrastruktuře, služby a aplikace spravované Microsoftem.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)