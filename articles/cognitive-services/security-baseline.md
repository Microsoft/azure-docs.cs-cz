---
title: Základní hodnoty zabezpečení Azure pro Cognitive Services
description: Základní Cognitive Services zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 218810183f547d4e90043364a318615a204df9d8
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044851"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Základní hodnoty zabezpečení Azure pro Cognitive Services

Tato základní hodnota zabezpečení platí pro Cognitive Services pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Cognitive Services. **Ovládací prvky** , které se nevztahují k Cognitive Services byly vyloučeny.

 
Pokud chcete zjistit, jak Cognitive Services kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Cognitive Services Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: Microsoft Azure Cognitive Services poskytuje vrstvený model zabezpečení. Tento model umožňuje zabezpečit účty služeb Cognitive Services pro konkrétní podmnožinu sítí. Když jsou nakonfigurovaná pravidla sítě, můžou k účtu přistupovat pouze aplikace, které žádají o data přes zadanou sadu sítí. Můžete omezit přístup k vašim prostředkům pomocí filtrování požadavků a povolit pouze požadavky, které pocházejí ze zadaných IP adres, rozsahů IP adres nebo ze seznamu podsítí ve službě Azure Virtual Network.

Podpora služby Virtual Network a koncového bodu služby pro Cognitive Services je omezená na konkrétní sadu oblastí.

- [Jak nakonfigurovat virtuální sítě Azure Cognitive Services](./cognitive-services-virtual-networks.md?tabs=portal)

- [Přehled virtuálních sítí Azure](../virtual-network/virtual-networks-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Pokyny**: když Virtual Machines jsou nasazené ve stejné virtuální síti jako kontejner Cognitive Services, můžete pomocí skupin zabezpečení sítě snížit riziko exfiltrace dat. Povolit skupinám zabezpečení sítě směrování protokolů a odesílání protokolů do účtu Azure Storage pro audit provozu. Skupiny zabezpečení sítě můžete také odesílat do Log Analyticsho pracovního prostoru a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: pokud používáte Cognitive Services v rámci kontejneru, můžete rozšířit nasazení kontejneru pomocí řešení firewallu webových aplikací s front-endu, které filtruje škodlivý provoz a podporuje komplexní šifrování TLS, a přitom udržuje koncový bod kontejneru privátní a bezpečný. 

Pamatujte, že Cognitive Services kontejnery jsou požadovány k odeslání informací o měření pro účely fakturace. Jedinou výjimkou jsou offline kontejnery, které sledují jinou metodologii fakturace. Nepovedlo se zakázat seznam různých síťových kanálů, na kterých se spoléhá Cognitive Services kontejnery, aby se zabránilo tomu, že kontejner nebude fungovat. Hostitel by měl povolený seznam port 443 a následující domény:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Všimněte si také, že je nutné zakázat hloubkovou kontrolu paketů pro vaše řešení brány firewall na zabezpečených kanálech, které kontejnery Cognitive Services vytvoří na serverech společnosti Microsoft. V takovém případě se zabrání správnému fungování kontejneru.

- [Principy zabezpečení služby Azure Cognitive Services Container](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: Pokud jsou virtuální počítače nasazené ve stejné virtuální síti jako kontejner Cognitive Services, definujte a Implementujte standardní konfigurace zabezpečení pro související síťové prostředky s Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. Cognitiveservices Account a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě pro instance Azure cache. Můžete také využít integrované definice zásad, například:

- Měla by být povolená DDoS Protection Standard.

Pomocí Azure modrotisky můžete zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení přístupu na základě role Azure (Azure RBAC) a zásady v rámci jedné definice podrobného plánu. Podrobné sestavování můžete snadno použít pro nová předplatná a prostředí a vyladit řízení a správu prostřednictvím správy verzí.

Pokud používáte Cognitive Services v rámci kontejneru, můžete rozšířit nasazení kontejnerů pomocí řešení firewallu webových aplikací s front-endu, které filtruje škodlivý provoz a podporuje komplexní šifrování TLS, a přitom udržuje koncový bod kontejneru privátní a zabezpečený. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

- [Principy zabezpečení služby Azure Cognitive Services Container](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: Pokud jsou virtuální počítače nasazené ve stejné virtuální síti jako kontejner Cognitive Services, můžete pomocí skupin zabezpečení sítě snížit riziko exfiltrace dat. Povolit skupinám zabezpečení sítě směrování protokolů a odesílání protokolů do účtu Azure Storage pro audit provozu. Skupiny zabezpečení sítě můžete také odesílat do Log Analyticsho pracovního prostoru a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: pokud používáte Cognitive Services v rámci kontejneru, můžete rozšířit nasazení kontejneru pomocí řešení firewallu webových aplikací s front-endu, které filtruje škodlivý provoz a podporuje komplexní šifrování TLS, a přitom zachovat privátní a zabezpečené koncové body kontejneru.  Můžete vybrat nabídku z Azure Marketplace, která podporuje funkčnost IDENTIFIKÁTORů/IP adres, s možností zakázat kontrolu datové části.

Pamatujte, že Cognitive Services kontejnery jsou požadovány k odeslání informací o měření pro účely fakturace. Jedinou výjimkou jsou offline kontejnery, protože sledují jinou metodologii fakturace. Nepovedlo se zakázat seznam různých síťových kanálů, na kterých se spoléhá Cognitive Services kontejnery, aby se zabránilo tomu, že kontejner nebude fungovat. Hostitel by měl povolený seznam port 443 a následující domény:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Všimněte si také, že je nutné zakázat hloubkovou kontrolu paketů pro vaše řešení brány firewall na zabezpečených kanálech, které kontejnery Cognitive Services vytvoří na serverech společnosti Microsoft. V takovém případě se zabrání správnému fungování kontejneru.

- [Principy zabezpečení služby Azure Cognitive Services Container](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: pokud používáte Cognitive Services v rámci kontejneru, můžete rozšířit nasazení kontejneru pomocí řešení firewallu webových aplikací s front-endu, které filtruje škodlivý provoz a podporuje komplexní šifrování TLS, a přitom zachovat privátní a zabezpečené koncové body kontejneru. 

Pamatujte, že Cognitive Services kontejnery jsou požadovány k odeslání informací o měření pro účely fakturace. Jedinou výjimkou jsou offline kontejnery, které sledují jinou metodologii fakturace. Nepovedlo se zakázat seznam různých síťových kanálů, na kterých se spoléhá Cognitive Services kontejnery, aby se zabránilo tomu, že kontejner nebude fungovat. Hostitel by měl povolený seznam port 443 a následující domény:

- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Všimněte si také, že je nutné zakázat hloubkovou kontrolu paketů pro vaše řešení brány firewall na zabezpečených kanálech, které kontejnery Cognitive Services vytvoří na serverech společnosti Microsoft. V takovém případě se zabrání správnému fungování kontejneru.

- [Principy zabezpečení služby Azure Cognitive Services Container](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Pokyny**: pomocí značek služby virtuální sítě můžete definovat řízení přístupu k síti pro skupiny zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (například ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Skupiny zabezpečení aplikací můžete použít také ke zjednodušení složité konfigurace zabezpečení. Skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace. Můžete seskupovat virtuální počítače a na základě těchto skupin definovat zásady zabezpečení sítě.

- [Značky služby virtuální sítě](../virtual-network/service-tags-overview.md)

- [Skupiny zabezpečení aplikací](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky související s vaším Cognitive Services kontejnerem Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. Cognitiveservices Account a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě pro instance Azure cache.

Pomocí Azure modrotisky můžete také zjednodušit rozsáhlá nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení přístupu na základě role (Azure RBAC) a zásady, a to v rámci jedné definice podrobného podrobného plánu. Podrobné sestavování můžete snadno použít pro nová předplatná a prostředí a vyladit řízení a správu prostřednictvím správy verzí.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro síťové prostředky přidružené k vašemu Cognitive Servicesmu kontejneru, aby je bylo možné logicky uspořádat do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vaším Cognitive Servicesm kontejnerem. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu úložiště Azure pro archivaci. Protokoly aktivit poskytují přehled o operacích, které byly provedeny na kontejneru Cognitive Services na úrovni řídicích rovin. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni řídicích roviny pro instance služby Azure cache pro Redis.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro vaše prostředky Azure.

Kromě toho Cognitive Services odesílá diagnostické události, které je možné shromažďovat a používat pro účely analýzy, upozorňování a vytváření sestav. Nastavení diagnostiky pro kontejner Cognitive Services můžete nakonfigurovat pomocí Azure Portal. K účtu úložiště, centru událostí nebo pracovnímu prostoru Log Analytics můžete odeslat jednu nebo více diagnostických událostí.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Použití nastavení diagnostiky pro pro Azure Cognitive Services](diagnostic-logging.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor nastavte dobu uchování pracovního prostoru Log Analytics podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

- [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do pracovního prostoru Log Analytics. Tyto protokoly poskytují bohatá a často používaná data o provozu prostředku, který se používá k identifikaci a ladění problémů. Provádějte dotazy v Log Analytics k hledání podmínek, identifikujte trendy, analyzujte vzorce a poskytněte spoustu dalších přehledů na základě dat protokolů aktivit, které se mohly shromažďovat pro Azure Cognitive Services.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Jak shromažďovat a analyzovat protokoly aktivit Azure v pracovním prostoru Log Analytics v Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: upozornění na podporované metriky můžete vygenerovat v Cognitive Services v části výstrahy a metriky v Azure monitor.

Nakonfigurujte nastavení diagnostiky pro kontejner Cognitive Services a odešlete protokoly do pracovního prostoru Log Analytics. V rámci pracovního prostoru Log Analytics nakonfigurujte výstrahy, které se mají provést, když dojde k předdefinované sadě podmínek. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Vytváření, zobrazování a správa výstrah protokolu pomocí Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?amp;preserve-view=true&view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?amp;preserve-view=true&view=azureadps-2.0)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: přístup roviny ovládacího prvku k Cognitive Services je řízen prostřednictvím Azure Active Directory (Azure AD). Azure AD nemá koncept výchozích hesel.

Přístup k rovině dat k Cognitive Services je řízen prostřednictvím přístupových klíčů. Tyto klíče používají klienti, kteří se připojují ke svojí mezipaměti a dají se kdykoli znovu vygenerovat.

Nedoporučujeme vytvářet výchozí hesla do aplikace. Místo toho můžete ukládat hesla v Azure Key Vault a pak je pomocí Azure AD načíst.

- [Jak znovu vygenerovat Azure cache pro přístupové klíče Redis](../azure-cache-for-redis/cache-configure.md#settings)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Security Center nebo integrovaných zásad Azure, jako je například:

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

- [Použití Azure Security Center k monitorování identity a přístupu (Preview)](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Pokyny**: Cognitive Services používá přístupové klíče k ověřování uživatelů a nepodporuje jednotné přihlašování (SSO) na úrovni datové roviny. Přístup k rovině ovládacího prvku pro Cognitive Services je k dispozici prostřednictvím REST API a podporuje jednotné přihlašování. Pro ověření nastavte hlavičku autorizace pro vaše požadavky na webový token JSON (JavaScript Object Notation), který získáte z Azure Active Directory (Azure AD).

- [Pochopení Cognitive Services Azure REST API](/rest/api/cognitiveservices/)

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Security Center doporučení pro správu identit a přístupu.

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

**Pokyny**: Konfigurace pojmenovaných umístění v Azure Active Directory (Azure AD) podmíněný přístup pro povolení přístupu pouze ze specifických logických skupin rozsahů IP adres nebo zemí a oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data a také soli, hodnoty hash a bezpečně ukládá přihlašovací údaje uživatele. Pokud váš případ použití podporuje ověřování AD, použijte Azure AD k ověřování požadavků na rozhraní Cognitive Services API. 

V současné době platí jenom rozhraní API pro počítačové zpracování obrazu, rozhraní API pro rozpoznávání tváře, rozhraní API pro analýzu textu, moderní čtečka, funkce pro rozpoznávání formulářů, detektor anomálií a všechny služby Bingu s výjimkou Vlastní vyhledávání Bingu ověřování pomocí Azure AD.

- [Ověření požadavků na Cognitive Services](./authentication.md#authenticate-with-azure-active-directory)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Kromě toho zákazník využije kontroly přístupu Azure identity k efektivní správě členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí. Přístup uživatele se může pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom aktivní uživatelé.

Zákazníkům udržovat inventář API Managementch uživatelských účtů, podle potřeby sjednotit přístup. V API Management jsou vývojáři uživateli rozhraní API, které vystavíte pomocí API Management. Ve výchozím nastavení jsou nově vytvořené účty pro vývojáře aktivní a přidruženy ke skupině Developers. Účty pro vývojáře, které jsou v aktivním stavu, se dají použít pro přístup ke všem rozhraním API, pro která mají předplatná.

- [Správa uživatelských účtů ve službě Azure API Management](../api-management/api-management-howto-create-or-invite-developers.md)

- [Jak získat seznam API Management uživatelů](/powershell/module/az.apimanagement/get-azapimanagementuser?amp;preserve-view=true&view=azps-4.8.0)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: máte přístup k aktivitě přihlášení k Azure Active Directory (Azure AD), událostem auditu a rizikovým protokolům událostí, které umožňují integraci s ověřováním Azure Sentinel nebo Siem třetí strany.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy protokolu můžete nakonfigurovat v rámci Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Postup zprovoznění služby Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné** materiály: pro odchylku chování přihlášení k účtu na rovině ovládacího prvku použijte funkce Azure Active Directory (Azure AD) Identity Protection a detekce rizik ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce související s identitami uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: není k dispozici pro Cognitive Services. Customer Lockbox ještě není pro Cognitive Services podporovaná.

- [Seznam služeb podporovaných Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

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

**Doprovodné** materiály: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Prostředky by měly být oddělené virtuálními sítěmi nebo podsítěmi, vhodně označené a zabezpečené skupinou zabezpečení sítě nebo Azure Firewall. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být dostatečně izolované. Pokud Virtual Machines ukládáte nebo zpracováváte citlivá data, implementujte zásady a postupy, abyste je mohli vypnout, pokud se nepoužívají.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Vytvoření Virtual Network](../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Jak nakonfigurovat výstrahu nebo upozornění a odepřít pomocí Azure Firewall](../firewall/threat-intel.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: zatím není k dispozici. Pro Cognitive Services ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací.

Společnost Microsoft spravuje základní infrastrukturu pro Cognitive Services a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: všechny koncové body Cognitive Services jsou zpřístupněny přes protokol HTTP vynutil TLS 1,2. S vynutilým protokolem zabezpečení se zákazníci, kteří se pokoušejí zavolat na Cognitive Services koncový bod, musí dodržovat tyto pokyny:

- Operační systém klienta (OS) musí podporovat protokol TLS 1,2.
- Jazyk (a platforma), který se používá k provedení volání HTTP, musí jako součást žádosti zadat TLS 1,2. V závislosti na jazyku a platformě se protokol TLS provádí implicitně nebo explicitně.

- [Principy zabezpečení transportní vrstvy pro Azure Cognitive Services](cognitive-services-security.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Cognitive Services. Označení instancí obsahujících citlivé údaje jako takové a implementace řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Microsoft spravuje základní platformu a považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC. 

**Pokyny**: použijte řízení přístupu na základě role Azure (Azure RBAC) k řízení přístupu k Cognitive Services rovině ovládacího prvku (tj. Azure Portal). 

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Pokyny**: šifrování v klidovém provozu pro Cognitive Services závisí na používané konkrétní službě. Ve většině případů se data šifrují a dešifrují s využitím 256 šifrování AES kompatibilního se standardem FIPS 140-2. Šifrování a dešifrování je transparentní, což znamená, že pro zákazníky od Microsoftu jsou spravováni šifrování a přístup. Zákaznická data jsou ve výchozím zabezpečení zabezpečená a nepotřebují upravovat kód nebo aplikace, aby mohli využívat šifrování.

K ukládání klíčů spravovaných zákazníkem můžete použít taky Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů.

- [Seznam služeb, které zašifrují informace v klidovém znění](./encryption/cognitive-services-encryption-keys-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Cognitive Services a dalších důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu můžete v rámci svých předplatných dotazovat nebo zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty, protokoly atd.). Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?amp;preserve-view=true&view=azps-4.8.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování mezipaměti Azure pro instance Redis a související prostředky. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

Kromě toho použijte Azure Resource Graph k dotazování nebo zjišťování prostředků v rámci předplatných.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

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

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro kontejner Cognitive Services s Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Cognitiveservices Account můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace mezipaměti Azure pro instance Redis.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?amp;preserve-view=true&view=azps-4.8.0)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [nasazení, pokud neexistují] efekty, abyste vynutili zabezpečená nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: Pokud používáte vlastní definice Azure Policy nebo šablony Azure Resource Manager pro Cognitive Services kontejnery a související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/?amp;preserve-view=true&view=azure-devops)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů Microsoft. cache k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. cognitiveservices Account můžete vytvořit vlastní definice Azure Policy pro upozornění, audit a vymáhání systémových konfigurací. Použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistují] efekty, abyste automaticky vynutili konfigurace pro Azure cache pro instance Redis a související prostředky.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: u virtuálních počítačů Azure nebo webových aplikací běžících na Azure App Service se používá pro přístup k rozhraní API Cognitive Services použijte identita spravované služby ve spojení s Azure Key Vault pro zjednodušení a zabezpečení Cognitive Services správy klíčů. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Vytvoření Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Ověření Key Vault](../key-vault/general/authentication.md)

- [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: u virtuálních počítačů Azure nebo webových aplikací běžících na Azure App Service se používá pro přístup k rozhraní API Cognitive Services použijte identita spravované služby ve spojení s Azure Key Vault pro zjednodušení a zabezpečení Cognitive Services správy klíčů. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Azure Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Postup konfigurace spravovaných identit](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například mezipaměť Azure pro Redis), ale neběží na zákaznickém obsahu.

Prohledejte veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, například App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL a tak dále. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: data v Microsoft azurem účtu úložiště se vždycky automaticky replikují, aby se zajistila odolnost a vysoká dostupnost. Azure Storage kopíruje vaše data, aby byla chráněná před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, sítě nebo výpadků výpadků a obrovských přirozených havárií. Můžete se rozhodnout, že budete replikovat data ve stejném datovém centru, napříč datovými centry oblastí v rámci stejné oblasti nebo v geograficky oddělených oblastech. 

Funkci správy životního cyklu můžete také použít k zálohování dat do archivní úrovně. Navíc můžete povolit obnovitelné odstranění záloh uložených v účtu úložiště.

- [Porozumění Azure Storage redundanci a Service-Level smluv](../storage/common/storage-redundancy.md)

- [Správa životního cyklu úložiště objektů blob v Azure](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Obnovitelné odstranění objektů blob služby Azure Storage](../storage/blobs/soft-delete-blob-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: k nasazení Cognitive Services a souvisejících prostředků použijte Azure Resource Manager. Azure Resource Manager poskytuje možnost exportovat šablony, což vám umožní znovu nasadit řešení v celém životním cyklu vývoje a mít jistotu, že se prostředky nasadí v konzistentním stavu. Použijte Azure Automation k pravidelnému volání rozhraní API pro export Azure Resource Manager šablony. Zálohování předsdílených klíčů v rámci Azure Key Vault.

- [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md)

- [Postup vytvoření prostředku Cognitive Services pomocí šablony Azure Resource Manager](./create-account-resource-manager-template.md?tabs=portal)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Skupiny prostředků – Exportovat šablonu](/rest/api/resources/resourcegroups/exporttemplate)

- [Úvod do Azure Automation](../automation/automation-intro.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: Zajistěte, aby v případě potřeby pravidelně prováděly nasazení Azure Resource Manager šablon pro izolované předplatné do izolovaného předplatného. Test obnovení zálohovaných předsdílených klíčů.

- [Nasazení prostředků pomocí šablon ARM a Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-4.8.0)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: pomocí Azure DevOps bezpečně ukládejte a spravujte šablony Azure Resource Manager. K ochraně prostředků, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps nebo Active Directory, pokud jsou integrované s Team Foundation Server.

K ochraně klíčů spravovaných zákazníkem použijte řízení přístupu na základě role Azure. Povolit Soft-Delete a vyprázdnit ochranu v Key Vault a chránit tak klíče proti náhodnému nebo škodlivému odstranění. 

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?amp;preserve-view=true&view=azure-devops)

- [O oprávněních a skupinách v Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [Obnovitelné odstranění objektů blob služby Azure Storage](../storage/blobs/soft-delete-blob-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [K podpoře při vytváření vlastního plánu odpovědí na incidenty můžete také využít příručku pro zpracování incidentů v počítači s NIST.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění. 

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

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem. 

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)