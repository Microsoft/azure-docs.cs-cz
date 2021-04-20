---
title: Základní hodnoty zabezpečení Azure pro Key Vault
description: Základní Key Vault zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1f56de94df4fd5d4dd154ae8485edb9eed88364c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753339"
---
# <a name="azure-security-baseline-for-key-vault"></a>Základní hodnoty zabezpečení Azure pro Key Vault

Tato základní hodnota zabezpečení platí pro Key Vault pokynů od [zabezpečení Azure Security test 1,0](../../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Key Vault. **Ovládací prvky** , které se nevztahují na Key Vault nebo pro které je odpovědnost od Microsoftu, jsou vyloučené.

Pokud chcete zjistit, jak Key Vault kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Key Vault Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: Integrace Azure Key Vault s privátním odkazem Azure. Služba privátního propojení Azure umožňuje přístup ke službám Azure (například Azure Key Vault) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti.

Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

- [Jak integrovat Key Vault s privátním propojením Azure](/azure/key-vault/private-link-service)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. webtrezor**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Doprovodné** materiály: použijte Azure Security Center a sledujte doporučení pro ochranu sítě, která vám pomůžou zabezpečit vaše Key Vault nakonfigurované prostředky v Azure. 

- [Další informace o zabezpečení sítě, které poskytuje Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: povolení Azure DDoS Protection Standard ve virtuálních sítích Azure přidružených k vašim Key Vault instancím pro ochranu před distribuovanými útoky DoS (Denial-of-Service). K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

 
- [Správa Azure DDoS Protection Standard pomocí Azure Portal](/azure/virtual-network/manage-ddos-protection)

- [Detekce hrozeb pro vrstvu služby Azure v Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: Tento požadavek je možné splnit konfigurací rozšířené ochrany před internetovými útoky (ATP) pro Azure Key Vault. ATP poskytuje další vrstvu zabezpečení, která je součástí služby Security Intelligence. Tento nástroj detekuje potenciálně nebezpečné pokusy o přístup k účtům Azure Key Vault a jejich zneužití.

Když Azure Security Center detekuje aktivitu neobvyklé, zobrazí výstrahy. Také pošle e-mailem správce předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak zjistit a opravit zjištěné hrozby.

- [Nastavení služby Advanced Threat Protection pro Azure Key Vault](/azure/security-center/advanced-threat-protection-key-vault)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků, které potřebují přístup k instancím Azure Key Vault, použijte značky služeb Azure pro Azure Key Vault k definování řízení přístupu k síti u skupin zabezpečení sítě nebo na Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

- [Přehled značek služeb Azure](../../virtual-network/service-tags-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené k vašim Azure Key Vault instancí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. klíčů a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich Azure Key Vault. Můžete také využít integrované definice zásad související s Azure Key Vault, například:
- Key Vault by měl používat koncový bod služby virtuální sítě

Další informace najdete v následujících referenčních materiálech:

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)

- [Ukázky Azure Policy](/azure/governance/policy/samples)

- [Definování a přiřazení podrobného plánu na portálu](../../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro prostředky, které se týkají zabezpečení sítě a toku provozu pro vaše Azure Key Vault instance pro poskytování metadat a logické organizace.

Použijte předdefinované Azure Policy definice týkající se označování, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledat nebo provádět akce s prostředky na základě jejich značek.

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi Azure Key Vault instancemi. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Zobrazení a načtení událostí protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

- [Vytváření, zobrazování a správa výstrah protokolu aktivit pomocí Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení vygenerovaných Azure Key Vault. V rámci Azure Monitor můžete k dotazování a provádění analýz použít pracovní prostor Azure Log Analytics a používat účty Azure Storage k dlouhodobému a Archivačnímu úložišti. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Protokolování Azure Key Vault](/azure/key-vault/key-vault-logging)

- [Rychlý Start: jak na desku zapnout službu Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení nastavení diagnostiky na vašich instancích Azure Key Vault pro přístup k protokolům auditu, zabezpečení a diagnostiky. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

- [Protokolování Azure Key Vault](/azure/key-vault/key-vault-logging)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. webtrezor**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor pro pracovní prostor Log Analytics, který se používá k ukládání protokolů Azure Key Vault, nastavte dobu uchování podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

- [Změna doby uchovávání dat](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro neobvyklé chování a pravidelné kontroly výsledků pro prostředky chráněné vaším Azure Key Vault. Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [On-Board – Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Začínáme s Log Analytics v Azure Monitor](/azure/azure-monitor/log-query/get-started-portal)

- [Začínáme s dotazy protokolů v Azure Monitoru](/azure/azure-monitor/log-query/get-started-queries)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: v Azure Security Center povolte pro Key Vault rozšířenou ochranu před internetovými útoky (ATP). Povolte nastavení diagnostiky v Azure Key Vault a odešlete protokoly do pracovního prostoru Log Analytics. Připojte pracovní prostor Log Analytics do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení.

- [Rychlý start: Zprovoznění služby Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Správa a reakce na upozornění zabezpečení ve službě Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Reakce na události s upozorněními služby Azure Monitor](/azure/azure-monitor/learn/tutorial-response)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Údržba inventáře aplikací Azure Active Directory (Azure AD) a také všech uživatelských účtů, které mají přístup k vašim Azure Key Vault klíčům, tajným klíčům a certifikátům. K dotazování a sjednocení Key Vault přístupu můžete použít buď Azure Portal, nebo PowerShell. Chcete-li zobrazit přístup v prostředí PowerShell, použijte následující příkaz:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Properties. AccessPolicies

- [Registrace aplikace ve službě Azure AD](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Zabezpečení přístupu k trezoru klíčů](security-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem použití vyhrazených účtů pro správu, které mají přístup k vašim instancím Azure Key Vault. Pomocí Azure Security Center správy identit a přístupu (aktuálně ve verzi Preview) můžete monitorovat počet aktivních účtů pro správu.

- [Monitorovat identitu a přístup (Preview)](../../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Pokyny**: pomocí instančního objektu Azure ve spojení s AppID, TenantID a ClientSecret můžete hladce ověřit vaši aplikaci a načíst token, který se použije pro přístup k Azure Key Vault tajným klíčům.

- [Ověřování služba-služba pro Azure Key Vault pomocí .NET](/azure/key-vault/service-to-service-authentication)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte doporučení Azure Security Center Správa identit a přístupu (aktuálně ve verzi Preview), která vám pomůžou chránit vaše prostředky s podporou centra událostí.

- [Plánování nasazení Multi-Factor Authentication služby Azure AD](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitorovat identitu a přístup (Preview)](../../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Pokyny**: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným vícefaktorového ověřováním Azure AD, které je nakonfigurované pro přihlášení a konfiguraci prostředků s povoleným Key Vault.

- [Pracovní stanice s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Plánování nasazení vícefaktorového ověřování Azure AD založeného na cloudu](../../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy týkající se rizikového chování uživatelů. Pro další protokolování odešlete výstrahy Azure Security Center detekce rizik do Azure Monitor a nakonfigurujte vlastní výstrahy a oznámení pomocí skupin akcí.

Povolit rozšířenou ochranu před internetovými útoky (ATP) pro Azure Key Vault pro generování výstrah pro podezřelou aktivitu.

- [Nasazení Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Nastavení rozšířené ochrany před internetovými útoky pro Azure Key Vault (Preview)](/azure/security-center/advanced-threat-protection-key-vault)

- [Výstrahy pro Azure Key Vault (Preview)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Detekce rizik Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Vytváření a správa skupin akcí na webu Azure Portal](/azure/azure-monitor/platform/action-groups)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: umožňuje nakonfigurovat podmínku umístění zásad podmíněného přístupu a spravovat pojmenovaná umístění. Pomocí pojmenovaných umístění můžete vytvořit logická seskupení rozsahů IP adres nebo zemí a oblastí. Přístup k citlivým prostředkům, jako jsou Key Vault tajné klíče, můžete omezit na vaše nakonfigurovaná pojmenovaná umístění.

- [Jaká je podmínka umístění v rámci podmíněného přístupu Azure Active Directory (Azure AD)?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: použití Azure Active Directory (Azure AD) jako centrálního ověřování a autorizačního systému pro prostředky Azure, jako je například Key Vault. Díky tomu může řízení přístupu na základě role Azure (Azure RBAC) spravovat citlivé prostředky.

- [Vytvoření nového tenanta ve službě Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Pokyny**: Projděte si protokoly Azure Active Directory (Azure AD), které vám pomůžou zjistit zastaralé účty s Azure Key Vault administrativní role. Navíc můžete pomocí kontrol přístupu Azure AD efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím, které se dají použít pro přístup k Azure Key Vault a přiřazení rolí. Přístup uživatelů by se měl pravidelně kontrolovat, například každých 90 dní, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Dokumentace k monitorování a sestavy Azure AD](/azure/active-directory/reports-monitoring/)

- [Co jsou kontroly přístupu Azure AD?](../../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: povolení nastavení diagnostiky pro Azure Key Vault a Azure Active Directory (Azure AD), odesílání všech protokolů do log Analyticsho pracovního prostoru. Nakonfigurujte požadovaná upozornění (například pokusy o přístup k zakázaným tajným klíčům) v rámci Log Analytics.

- [Integrace protokolů služby Azure AD s protokoly Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Migrace ze starého řešení Key Vault](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Pokyny**: použití funkcí ochrany identit a detekce rizik Azure Active Directory (Azure AD) ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se vašich Azure Key Vault chráněných prostředků. Pomocí služby Azure Sentinel byste měli povolit automatizované odezvy pro implementaci reakcí zabezpečení vaší organizace.

- [Sestava rizikových přihlášení na portálu Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Postupy: konfigurace a povolení zásad rizik](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: pomocí značek pomáhají při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace na Azure Key Vault povolené prostředky. 

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: pomocí koncových bodů služby virtuální sítě, které jsou nakonfigurovány k omezení přístupu ke konkrétním podsítím, můžete zabezpečit přístup k Azure Key Vault.

Po uplatnění pravidel brány firewall můžete provádět operace Azure Key Vault roviny dat pouze v případě, že požadavek pochází z povolených podsítí nebo rozsahů IP adres. To platí také pro Azure Key Vault přístup v Azure Portal. I když můžete z Azure Portal přejít k trezoru klíčů, možná nebudete moct zobrazit seznam klíčů, tajných kódů ani certifikátů, pokud klientský počítač není na seznamu povolených. To má vliv také na Azure Key Vault pro výběr a další služby Azure. Možná budete moct zobrazit seznamy trezorů klíčů, ale ne seznam klíčů, pokud pravidla firewallu brání vašemu klientskému počítači.

- [Konfigurace Azure Key Vault bran firewall a virtuálních sítí](/azure/key-vault/key-vault-network-security)

- [Koncové body služby Azure Key Vault pro virtuální síť](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: všechna data uložená v Azure Key Vault se považují za citlivá. Pro řízení přístupu k Azure Key Vault tajným klíčům použijte Azure Key Vault řízení přístupu k rovině dat. K řízení přístupu v síťové vrstvě můžete použít také vestavěnou bránu firewall Key Vault. Pokud chcete monitorovat přístup k Azure Key Vault, povolte Key Vault nastavení diagnostiky a odešlete protokoly do účtu Azure Storage nebo Log Analytics pracovního prostoru.

- [Zabezpečení přístupu k trezoru klíčů](security-overview.md)

- [Konfigurace Azure Key Vault bran firewall a virtuálních sítí](/azure/key-vault/key-vault-network-security)

- [Protokolování Azure Key Vault](/azure/key-vault/key-vault-logging)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: ke správě přístupu k prostředkům použijte službu Azure RBAC.

**Doprovodné** materiály: zabezpečený přístup k rovině správy a dat vašich Azure Key Vaultch instancí.

- [Zabezpečení přístupu k trezoru klíčů](security-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí řešení Azure Key Vault Analytics v Azure monitor Zkontrolujte protokoly událostí auditu Azure Key Vault.

- [Řešení Azure Key Vault Analytics v Azure Monitor](/azure/azure-monitor/insights/azure-key-vault)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: použijte výchozí hodnocení rizik (zabezpečené skóre) poskytované Azure Security Center.

- [Vylepšit vaše zabezpečené skóre v Azure Security Center](/azure/security-center/security-center-secure-score)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: k dotazování a zjišťování všech prostředků (včetně instancí Azure Key Vault) v rámci vašeho předplatného použijte Azure Resource Graph. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

- [Spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](../../governance/resource-graph/first-query-portal.md)

- [Získání předplatných, ke kterým má aktuální účet přístup](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Řízení přístupu na základě role Azure](../../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro Azure Key Vault prostředků, které dávají metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Azure Key Vaultch instancí a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalšího předplatného Azure](/azure/billing/billing-create-subscription)

- [Vytvoření skupin pro správu pro organizaci a správu prostředků](/azure/governance/management-groups/create)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: pomocí zásad Azure můžete nastavovat omezení pro typ prostředků, které se dají vytvořit v předplatných zákazníků, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Kromě toho můžete pomocí grafu prostředků Azure dotazovat nebo zjišťovat prostředky v rámci předplatných.

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)

- [Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](../../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí zásad Azure můžete nastavovat omezení pro typ prostředků, které se dají vytvořit v předplatných zákazníků, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Další informace najdete v následujících referenčních materiálech:

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)

- [Ukázky Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změnám prostředků v rámci prostředí s vysokým zabezpečením, jako jsou ty s konfigurací Key Vault.

- [Správa přístupu ke správě Azure pomocí podmíněného přístupu](../../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Doprovodné** materiály: použijte aliasy Azure Policy v oboru názvů "Microsoft. klíčů trezor" k vytvoření vlastních zásad pro auditování nebo vymáhání konfigurace vašich Azure Key Vaultch instancí. Můžete také použít předdefinované definice Azure Policy pro Azure Key Vault, například:

- Key Vault objekty by měly být obnovitelné

- Nasazení nastavení diagnostiky pro Key Vault k pracovnímu prostoru Log Analytics

- Měly by být povolené diagnostické protokoly v Key Vault.

- Key Vault by měl používat koncový bod služby virtuální sítě

- Nasazení nastavení diagnostiky pro Key Vault do centra událostí

- Použijte doporučení z Azure Security Center jako standardní hodnoty konfigurace pro vaše Azure Key Vault instance.

Další informace najdete v následujících referenčních materiálech:

- [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistují] pro vymáhání zabezpečených nastavení napříč prostředky s povolenými Azure Key Vault. 

- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../../governance/policy/tutorials/create-and-manage.md)

  
- [Pochopení Azure Policych efektů](../../governance/policy/concepts/effects.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: Pokud používáte vlastní definice Azure Policy pro prostředky s povoleným Azure Key Vault, používejte Azure Repos k bezpečnému ukládání a správě kódu.

- [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentace k Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: použijte aliasy Azure Policy v oboru názvů "Microsoft. klíčů trezor" k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: použijte Azure Security Center k provádění kontrol standardních hodnot vašich prostředků chráněných pomocí Azure Key Vault.

- [Jak opravit doporučení v Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace. Ujistěte se, že je povoleno Azure Key Vault obnovitelné odstranění.

- [Integrace se spravovanými identitami Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Vytvoření Key Vault](quick-create-portal.md)

- [Ověřování pro Key Vault](authentication.md)

- [Přiřazení zásady přístupu Key Vault](assign-access-policy-portal.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. webtrezor**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace.

  

- [Integrace se spravovanými identitami Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Vytvoření Key Vault](quick-create-portal.md)    

- [Ověření Key Vault](authentication.md)

- [Přiřazení zásady přístupu Key Vault](assign-access-policy-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.  
  
- [ Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure Key Vault), ale nespouští se u zákaznického obsahu.

Předem prohledejte veškerý obsah, který se nahrává nebo odesílá do nevýpočetních prostředků Azure, jako je například Azure Key Vault. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

- [Pochopení Microsoft antimalwaru pro Azure Cloud Services a Virtual Machines](../../security/fundamentals/antimalware.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: Zajistěte pravidelné automatizované zálohování Key Vault certifikátů, klíčů, spravovaných účtů úložiště a tajných kódů s následujícími příkazy PowerShellu:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

V případě potřeby můžete ukládat zálohy Key Vault v Azure Backup.

- [Postup zálohování certifikátů Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Postup zálohování klíčů Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Postup zálohování Key Vault spravovaných účtů úložiště](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Postup zálohování Key Vault tajných kódů](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Postup povolení Azure Backup](/azure/backup)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: proveďte zálohy certifikátů Key Vault, klíčů, spravovaných účtů úložiště a tajných kódů s následujícími příkazy PowerShellu:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

V případě potřeby můžete ukládat zálohy Key Vault v Azure Backup.

- [Postup zálohování certifikátů Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Postup zálohování klíčů Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Postup zálohování Key Vault spravovaných účtů úložiště](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Postup zálohování Key Vault tajných kódů](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Postup povolení Azure Backup](/azure/backup)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: pravidelně provádějte obnovení dat Key Vault certifikátů, klíčů, spravovaných účtů úložiště a tajných kódů s následujícími příkazy PowerShellu:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Další informace najdete v následujících referenčních materiálech:

- [Postup obnovení certifikátů Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Postup obnovení klíčů Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Postup obnovení Key Vault spravovaných účtů úložiště](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Postup obnovení tajných kódů Key Vault](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: Ujistěte se, že je pro Azure Key Vault povolené obnovitelné odstranění. Obnovitelné odstranění umožňuje obnovit odstraněné trezory klíčů a objekty trezoru, jako jsou klíče, tajné klíče a certifikáty. 

- [Použití obnovitelného odstranění Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. webtrezor**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení. Tyto procesy by měly mít fokus na ochranu citlivých systémů, jako jsou například pomocí Key Vault tajných klíčů.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)   

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak je Security Center ve vyhledávání nebo v metrikě, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění. Kromě toho jasně označte odběry (pro např. produkční, neprodukční) a vytvoření názvového systému, který umožňuje snadno identifikovat a kategorizovat prostředky Azure, zejména ty, které zpracovávají citlivá data, například Azure Key Vault tajných údajů.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou chránit vaše Azure Key Vaulté instance a související prostředky. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky s povoleným Azure Key Vault. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem.  Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel. 

 

- [Postup konfigurace průběžného exportu](../../security-center/continuous-export.md) 

  

- [Streamování upozornění do služby Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich chráněných prostředků Azure Key Vault. 

 

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../../security-center/workflow-automation.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="next-steps"></a>Další kroky

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
