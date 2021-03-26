---
title: Základní hodnoty zabezpečení Azure pro Azure Cosmos DB
description: Základní Azure Cosmos DB zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: dead43f2e9f2e8913bcebde43d543b8df8d33ced
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565669"
---
# <a name="azure-security-baseline-for-azure-cosmos-db"></a>Základní hodnoty zabezpečení Azure pro Azure Cosmos DB

Tato základní hodnota zabezpečení platí pro Azure Cosmos DB pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure.
Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure Cosmos DB. **Ovládací prvky** , které se nevztahují k Azure Cosmos DB byly vyloučeny.

 
Pokud chcete zjistit, jak Azure Cosmos DB kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Cosmos DB Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: pomocí privátního odkazu Azure se můžete připojit k účtu Azure Cosmos prostřednictvím privátního koncového bodu. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Riziko exfiltrace dat můžete také snížit konfigurací striktní sady odchozích pravidel pro skupinu zabezpečení sítě (NSG) a přidružením tohoto NSG k vaší podsíti.

K zabezpečení účtu Azure Cosmos můžete použít také koncové body služby. Povolením koncového bodu služby můžete nakonfigurovat účty Azure Cosmos tak, aby povolovaly přístup jenom z konkrétní podsítě virtuální sítě Azure. Po povolení koncového bodu služby Azure Cosmos DB můžete omezit přístup k účtu Azure Cosmos pomocí připojení z podsítě ve virtuální síti.

Data uložená v účtu Azure Cosmos můžete také zabezpečit pomocí bran firewall protokolu IP. Azure Cosmos DB podporuje řízení přístupu na základě IP adres pro podporu příchozích bran firewall. Bránu firewall protokolu IP můžete nastavit na účtu Azure Cosmos pomocí Azure Portal, Azure Resource Manager šablon nebo prostřednictvím rozhraní příkazového řádku Azure CLI nebo Azure PowerShell.

- [Přehled služby Azure Private Link](../private-link/private-link-overview.md)

- [Postup konfigurace privátního koncového bodu pro Azure Cosmos DB](how-to-configure-private-endpoints.md) 

- [Vytvoření skupiny zabezpečení sítě s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

- [Postup konfigurace brány firewall protokolu IP v Cosmos DB](how-to-configure-firewall.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft.DocumentDB**:

[!INCLUDE [Resource Policy for Microsoft.DocumentDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.documentdb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: použijte Azure Security Center a sledujte doporučení pro ochranu sítě, která vám pomůžou zabezpečit síťové prostředky týkající se vašeho účtu Azure Cosmos.

Když jsou virtuální počítače nasazené ve stejné virtuální síti jako účet Azure Cosmos, můžete použít skupinu zabezpečení sítě (NSG) a snížit tak riziko exfiltraceí dat. Povolte protokoly toku NSG a odešlete protokoly do účtu Azure Storage pro audity provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Pokyny**: pomocí funkce sdílení prostředků mezi zdroji (CORS) Povolte webové aplikaci spuštěné v jedné doméně přístup k prostředkům v jiné doméně. Webové prohlížeče implementují omezení zabezpečení označované jako zásady stejného původu, které brání webové stránce v volání rozhraní API v jiné doméně. CORS ale poskytuje zabezpečený způsob, jak, aby zdrojová doména mohla volat rozhraní API v jiné doméně. Po povolení podpory CORS pro váš účet Azure Cosmos se vyhodnotí jenom ověřené požadavky, které určují, jestli jsou povolené podle pravidel, která jste zadali.

- [Konfigurace sdílení prostředků mezi zdroji](how-to-configure-cross-origin-resource-sharing.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Azure Cosmos nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je se systémy centrálního monitorování zabezpečení.

Povolte DDoS Protection Standard ve virtuálních sítích přidružených k vašim Azure Cosmos DB instancím, abyste se mohli chránit před útoky DDoS. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

- [Jak nakonfigurovat Azure Cosmos DB rozšířené ochrany před internetovými útoky](cosmos-db-advanced-threat-protection.md)

- [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

- [Pochopení Azure Security Center integrované analýzy hrozeb](../security-center/azure-defender.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: povolení protokolu Flow skupiny zabezpečení sítě (NSG) a odesílání protokolů do účtu úložiště pro audit provozu. Protokoly toku NSG můžete odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Azure Cosmos nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je se systémy centrálního monitorování zabezpečení. 

- [Jak nakonfigurovat Cosmos DB rozšířené ochrany před internetovými útoky](cosmos-db-advanced-threat-protection.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků, které potřebují přístup k vašemu účtu Azure Cosmos, použijte značky služby Virtual Network k definování řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. AzureCosmosDB) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Další informace o použití značek služeb](../virtual-network/service-tags-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky pomocí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft.DocumentDB a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě instancí služby Azure Cosmos DB. Můžete také využít integrované definice zásad pro Azure Cosmos DB, například:

- Nasazení rozšířené ochrany před internetovými útoky pro účty Cosmos DB

- Cosmos DB by měl používat koncový bod služby virtuální sítě

Plány Azure můžete použít také ke zjednodušení rozsáhlých nasazení Azure tím, že zabalíte klíčové artefakty prostředí, jako jsou například šablony Azure Resource Manager, řízení přístupu na základě role Azure (Azure RBAC) a zásady v jediné definici podrobného plánu. V rámci správy verzí můžete snadno použít podrobný plán na nová předplatná, prostředí a vyladit řízení a správu.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro síťové prostředky přidružené k vašemu nasazení Azure Cosmos DB, aby je bylo možné logicky uspořádat do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi Azure Cosmos DB instancemi. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích. 

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení vygenerovaných Azure Cosmos DB. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty úložiště pro dlouhodobé a archivační úložiště. Alternativně můžete začlenit data do Azure Sentinel nebo incidentu zabezpečení a řízení událostí (SIEM) třetí strany. 

- [Postup povolení diagnostických protokolů pro Azure Cosmos DB](./monitor-cosmos-db.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: Povolte nastavení diagnostiky pro Azure Cosmos DB a odešlete protokoly do pracovního prostoru Log Analytics nebo účtu úložiště Azure. Nastavení diagnostiky v Azure Cosmos DB se používají ke shromažďování protokolů prostředků. Tyto protokoly jsou zachyceny na žádost a jsou také označovány jako **protokoly roviny dat**. Mezi Příklady operací s rovinou dat patří odstranění, vložení a čtení. 

Můžete také povolit nastavení diagnostiky protokolů aktivit Azure a odesílat tyto protokoly do stejného Log Analytics pracovního prostoru, který používáte pro Azure Cosmos DB protokoly.

- [Postup povolení nastavení diagnostiky pro Azure Cosmos DB](./monitor-cosmos-db.md)

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování protokolu pro Log Analytics pracovní prostory, které jsou přidružené k vašim Azure Cosmos DB instancí podle předpisů pro dodržování předpisů vaší organizace.

- [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: můžete provádět dotazy v Log Analytics pracovní prostor pro hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě Azure Cosmos DB protokolů, které jste shromáždili.

- [Postup provádění dotazů pro Azure Cosmos DB v pracovních prostorech Log Analytics](monitor-cosmos-db.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: v Azure Security Center povolit rozšířenou ochranu před internetovými útoky pro Azure Cosmos DB monitorovat aktivity neobvyklé v protokolech zabezpečení a událostech. Povolte nastavení diagnostiky v Azure Cosmos DB a odešlete protokoly do pracovního prostoru Log Analytics.

 

Pracovní prostor Log Analytics můžete také připojit do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení. Kromě toho můžete pomocí Azure Monitor vytvořit vlastní výstrahy protokolu v pracovním prostoru Log Analytics.

- [Seznam výstrah ochrany před hrozbami pro Azure Cosmos DB](../security-center/alerts-reference.md#alerts-azurecosmos)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Vytváření, zobrazování a správa výstrah protokolu pomocí Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: podokno identita a řízení přístupu (IAM) můžete použít v Azure Portal ke konfiguraci řízení přístupu na základě role (RBAC) a udržování inventáře na Azure Cosmos DBch prostředcích. Role se aplikují na uživatele, skupiny, instanční objekty a spravované identity ve službě Azure Active Directory (Azure AD). Pro jednotlivce a skupiny můžete použít předdefinované role nebo vlastní role.

Azure Cosmos DB poskytuje vestavěnou službu Azure RBAC pro běžné scénáře správy v Azure Cosmos DB. Jednotlivec, který má profil ve službě Azure AD, může přiřadit tyto role Azure uživatelům, skupinám, instančním objektům nebo spravovaným identitám, aby mohl udělit nebo odepřít přístup k prostředkům a operacím s prostředky Azure Cosmos DB.

K vyhledání účtů, které jsou členy skupin pro správu, můžete také použít modul Azure AD PowerShell k provádění dotazů ad hoc.

Některé akce v Azure Cosmos DB se navíc dají řídit pomocí hlavních klíčů pro Azure AD a pro konkrétní účet. K řízení přístupu ke klíčům použijte nastavení účtu disableKeyBasedMetadataWriteAccess.

- [Pochopení řízení přístupu na základě role v Azure Cosmos DB](role-based-access-control.md)

- [Sestavujte vlastní role pomocí Azure Cosmos DBch akcí (Microsoft.Docobor názvů umentDB).](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

- [Vytvoření nové role v Azure AD](../role-based-access-control/custom-roles.md)

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Omezení přístupu uživatelů jenom na datové operace](how-to-restrict-user-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: koncept výchozích nebo prázdných hesel neexistuje ve vztahu k Azure Active Directory (Azure AD) nebo Azure Cosmos DB. Místo toho Azure Cosmos DB používá dva typy klíčů k ověřování uživatelů a poskytování přístupu k jeho datům a prostředkům; hlavní klíče a tokeny prostředků. Klíče lze kdykoli znovu vygenerovat.

- [Princip zabezpečeného přístupu k datům v Azure Cosmos DB](secure-access-to-data.md)

- [Postup opětovného generování Azure Cosmos DB klíčů](./manage-with-powershell.md#regenerate-keys)

- [Postup při programovém přístupu ke klíčům pomocí Azure AD](certificate-based-authentication.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: nepoužitelné; Azure Cosmos DB nepodporuje účty správců. Veškerý přístup je integrovaný do Azure Active Directory (Azure AD) a řízení přístupu na základě role Azure (Azure RBAC).

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné** materiály: Azure Cosmos DB používá dva typy klíčů k autorizaci uživatelů a nepodporuje jednotné Sign-On (SSO) na úrovni datové roviny. Přístup k rovině ovládacího prvku pro Cosmos DB je k dispozici prostřednictvím REST API a podporuje jednotné přihlašování. Pro ověření nastavte hlavičku autorizace pro vaše požadavky na JSON Web Token, které získáte z Azure Active Directory (Azure AD).

- [Principy služby Azure Database for Cosmos DB REST API](/rest/api/cosmos-db/)

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

- [Další informace o pracovních stanicích s privilegovaným přístupem](/security/compass/privileged-access-devices)
 
- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Azure Cosmos nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je se systémy centrálního monitorování zabezpečení.

Kromě toho můžete použít Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě.

Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy týkající se rizikového chování uživatelů.

- [Postup nasazení Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Vysvětlení zjišťování rizik Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: umožňuje nakonfigurovat podmínku umístění zásad podmíněného přístupu a spravovat pojmenovaná umístění. Pomocí pojmenovaných umístění můžete vytvořit logická seskupení rozsahů IP adres nebo zemí a oblastí. Můžete omezit přístup k citlivým prostředkům, jako jsou například instance Azure Cosmos DB, do nakonfigurovaných pojmenovaných umístění.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

- [Jak nakonfigurovat a spravovat ověřování Azure AD pomocí Azure SQL](../azure-sql/database/authentication-aad-configure.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Kromě toho můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatele se může pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: můžete vytvořit nastavení diagnostiky pro uživatelské účty Azure Active Directory (Azure AD), Odeslat protokoly auditu a protokoly přihlášení do pracovního prostoru Log Analytics, kde můžete nakonfigurovat požadované výstrahy.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné** materiály: použití rozšířené ochrany před internetovými útoky (ATP) pro Azure Cosmos DB. ATP pro Azure Cosmos DB poskytuje další vrstvu zabezpečení, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k účtům Azure Cosmos nebo jejich zneužití. Tato vrstva ochrany umožňuje řešit hrozby a integrovat je se systémy centrálního monitorování zabezpečení.

Můžete také použít funkci Azure Active Directory (Azure AD) Identity Protection a detekce rizik ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Kromě toho můžete protokoly ingestovat do služby Azure Sentinel pro další šetření.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: pomocí značek pomáhají při sledování Azure Cosmos DB instancí, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Instance Azure Cosmos DB jsou odděleny virtuální sítí nebo podsítí, vhodně označené a zabezpečené v rámci skupiny zabezpečení sítě (NSG) nebo Azure Firewall. Instance Azure Cosmos DB ukládající citlivé údaje by měly být izolované. Pomocí privátního odkazu Azure se můžete připojit k účtu Azure Cosmos DB instance prostřednictvím privátního koncového bodu. Soukromý koncový bod je sada privátních IP adres v podsíti v rámci vaší virtuální sítě. Pak můžete omezit přístup k vybraným privátním IP adresám. 

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Postup konfigurace privátního koncového bodu pro Azure Cosmos DB](how-to-configure-private-endpoints.md)

- [Vytvoření skupiny zabezpečení sítě s konfigurací zabezpečení](../virtual-network/tutorial-filter-network-traffic.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny**: můžete nasadit rozšířenou ochranu před internetovými útoky pro Azure Cosmos DB, která detekuje aktivity neobvyklé s neobvyklými a potenciálně škodlivými pokusy o přístup k databázím nebo jejich zneužití. V současné době může aktivovat následující výstrahy:

- Přístup z neobvyklých umístění

- Neobvyklé extrakce dat

Kromě toho při použití virtuálních počítačů pro přístup k instancím Azure Cosmos DB použijte privátní linku, bránu firewall, skupiny zabezpečení sítě a značky služeb, aby se snížila možnost exfiltraceí dat. Společnost Microsoft spravuje základní infrastrukturu pro Azure Cosmos DB a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Jak nakonfigurovat Cosmos DB rozšířené ochrany před internetovými útoky](cosmos-db-advanced-threat-protection.md)

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: automatické funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Cosmos DB. Pro klasifikaci a analýzu dat ale můžete použít integraci služby Azure Kognitivní hledání. Pokud je to nutné pro účely dodržování předpisů, můžete také implementovat řešení třetí strany.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Indexovat data Azure Cosmos DB pomocí Azure Kognitivní hledání](../search/search-howto-index-cosmosdb.md?bc=%2fazure%2fcosmos-db%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcosmos-db%2ftoc.json)

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Doprovodné** materiály: Azure Cosmos DB poskytuje integrované řízení přístupu na základě role Azure (Azure RBAC) pro běžné scénáře správy v Azure Cosmos DB. Jednotlivec, který má profil v Azure Active Directory (Azure AD), může přiřadit tyto role Azure uživatelům, skupinám, instančním objektům nebo spravovaným identitám, aby mohl udělit nebo odepřít přístup k prostředkům a operacím Azure Cosmos DB prostředků. Přiřazování rolí je vymezené jenom na úrovni řízení a přístup, což zahrnuje přístup k účtům Azure Cosmos, databázím, kontejnerům a nabídkám (propustnost).

- [Jak implementovat službu Azure RBAC v Azure Cosmos DB](role-based-access-control.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: všechna uživatelská data uložená v Cosmos DB jsou ve výchozím nastavení šifrována v klidovém stavu. Neexistují žádné ovládací prvky, které by bylo možné vypnout. Azure Cosmos DB používá šifrování AES-256 ve všech oblastech, kde je účet spuštěný.

Ve výchozím nastavení spravuje Microsoft klíče, které slouží k šifrování dat v účtu Azure Cosmos. Volitelně můžete zvolit, že se má přidat druhá vrstva šifrování s vlastními klíči.

- [Principy šifrování v klidovém umístění pomocí Azure Cosmos DB](database-encryption-at-rest.md)

- [Princip správy klíčů pro šifrování v klidovém umístění pomocí Azure Cosmos DB]()

- [Postup konfigurace klíčů spravovaných zákazníkem pro váš účet Azure Cosmos DB](how-to-setup-cmk.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Azure Cosmos DB.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: postupujte podle doporučení Azure Security Center pro vaše Azure Cosmos DB instance. 

Microsoft provádí systémovou opravu a správu ohrožení zabezpečení na podkladových hostitelích, kteří podporují vaše Azure Cosmos DB instance. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Podporované funkce dostupné ve službě Azure Security Center](../security-center/security-center-services.md?tabs=features-windows)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Portal nebo grafu prostředků Azure můžete v rámci svých předplatných zjistit všechny prostředky (bez omezení Azure Cosmos DB, ale taky včetně prostředků, jako jsou výpočetní prostředky, jiné úložiště, sítě, porty a protokoly atd.). Ujistěte se, že máte ve svém tenantovi příslušná oprávnění a že máte v rámci předplatných všechny odběry Azure i prostředky.

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Principy řízení přístupu na základě role v Azure](../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek u instancí Azure Cosmos DB a souvisejících prostředků s metadaty k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

- [Které Azure Cosmos DB prostředky podporují značky](../azure-resource-manager/management/tag-support.md#microsoftdocumentdb)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků, mimo jiné Azure Cosmos DB prostředky. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Kromě toho se pomocí grafu prostředků Azure Dotazujte na prostředky v rámci předplatných a vyhledejte je.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

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

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změnám prostředků v rámci vysoce zabezpečeného prostředí.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Cosmos DB instance pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů **Microsoft.DocumentDB** můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace instancí Cosmos DB. Můžete také využít integrované definice zásad pro Azure Cosmos DB, například:
- Nasazení rozšířené ochrany před internetovými útoky pro účty Cosmos DB
- Cosmos DB by měl používat koncový bod služby virtuální sítě

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: Pokud používáte vlastní definice Azure Policy pro Cosmos DB nebo související prostředky, použijte Azure Repos k ukládání a správě definic vašich vlastních zásad jako kódu.

- [Návrh pracovních postupů pro zásady jako kód](../governance/policy/concepts/policy-as-code.md)

- [Dokumentace k Azure Repos](/azure/devops/repos/git/gitworkflow)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů "Microsoft.DocumentDB" k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: použijte aliasy Azure Policy v oboru názvů "Microsoft.DocumentDB" k vytvoření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro vaše Azure Cosmos DB instance a související prostředky použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje]. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: u virtuálních počítačů Azure nebo webových aplikací, které běží na Azure App Service slouží pro přístup k instancím Azure Cosmos DB, použijte identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů Azure Cosmos DB. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Vytvoření Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Ověření Key Vault](../key-vault/general/authentication.md)

- [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: u virtuálních počítačů Azure nebo webových aplikací, které běží na Azure App Service slouží pro přístup k instancím Azure Cosmos DB, použijte identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů Azure Cosmos DB.

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

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

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure CosmosDB), ale neběží na zákaznickém obsahu.

Vaše zodpovědnost za všechny soubory nahrané do nevýpočetních prostředků Azure, včetně Azure Cosmos DB, je vaše zodpovědnost. Microsoft nemůže získat přístup k zákaznickým datům, a proto nemůže za vás provádět kontrolu proti malwarovým kontrolám zákaznického obsahu.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: Azure Cosmos DB automaticky provede zálohování vašich dat v pravidelných intervalech. Pokud dojde k odstranění databáze nebo kontejneru, můžete odeslat lístek podpory nebo zavolat podporu Azure, která obnoví data z automatických online zálohování. Podpora Azure je dostupná jenom pro vybrané plány, jako je standard, vývojář a plány vyšší než. Aby bylo možné obnovit konkrétní snímek zálohy, Azure Cosmos DB vyžaduje, aby data byla k dispozici po dobu trvání cyklu zálohování pro daný snímek. 

Pokud chcete ukládat přihlašovací údaje pro instance Cosmos DB pomocí Key Vault, zajistěte pravidelné automatizované zálohování vašich klíčů.

- [Vysvětlení Azure Cosmos DB automatizované zálohy](online-backup-and-restore.md)

- [Postup obnovení dat v Azure Cosmos DB](./online-backup-and-restore.md)

- [Postup zálohování klíčů Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: Pokud se databáze nebo kontejner odstraní, můžete vytvořit lístek podpory nebo zavolat podporu Azure, která obnoví data z automatických online zálohování. Podpora Azure je dostupná jenom pro vybrané plány, jako je standard, vývojář a plány vyšší než. Aby bylo možné obnovit konkrétní snímek zálohy, Azure Cosmos DB vyžaduje, aby data byla k dispozici po dobu trvání cyklu zálohování pro daný snímek.

Testovací obnovení tajných kódů uložených v Azure Key Vault pomocí prostředí PowerShell. Rutina Restore-AzureKeyVaultKey vytvoří klíč v zadaném trezoru klíčů. Tento klíč je replikou záložního klíče ve vstupním souboru a má stejný název jako původní klíč.

- [Vysvětlení Azure Cosmos DB automatizované zálohy](online-backup-and-restore.md)

- [Postup obnovení dat v Azure Cosmos DB](./online-backup-and-restore.md)

- [Postup obnovení tajných kódů Azure Key Vault](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: vzhledem k tomu, že všechna uživatelská data uložená v Cosmos DB jsou šifrována v klidovém stavu a v přenosu, není nutné provádět žádnou akci. Dalším způsobem, jak to dát, je toto šifrování v klidovém stavu "zapnuto". Neexistují žádné ovládací prvky, které by bylo možné vypnout nebo zapnout. Azure Cosmos DB používá šifrování AES-256 ve všech oblastech, kde je účet spuštěný.

Povolí Soft-Delete v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

- [Pochopení šifrování dat v Azure Cosmos DB](database-encryption-at-rest.md)

- [Postup povolení Soft-Delete v Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [K podpoře při vytváření vlastního plánu odpovědí na incidenty můžete také využít příručku pro zpracování incidentů v počítači s NIST.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak si nejste jistí, jak si Security Center ve vyhledávání, nebo na analýzu, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, že při aktivitě, která vedla k výstraze, byl škodlivý záměr.

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

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

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
