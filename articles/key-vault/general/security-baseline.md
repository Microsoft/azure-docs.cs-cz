---
title: Standardní hodnoty zabezpečení Azure pro službu Key Vault
description: Standardní hodnoty zabezpečení Azure pro službu Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 48a822adc34019ca2f6a39aa70211cae7fbf6030
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289881"
---
# <a name="azure-security-baseline-for-key-vault"></a>Standardní hodnoty zabezpečení Azure pro službu Key Vault

Základní plán zabezpečení Azure pro Key Vault obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Pokyny** : Integrace Azure Key Vault s privátním odkazem Azure. 

Služba privátního propojení Azure umožňuje přístup ke službám Azure (například Azure Key Vault) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti.

Privátní koncový bod Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

Jak integrovat Key Vault s privátním odkazem na Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Doprovodné** materiály: použijte Azure Security Center a sledujte doporučení pro ochranu sítě, která vám pomůžou zabezpečit vaše Key Vault nakonfigurované prostředky v Azure. 

Další informace o zabezpečení sítě, které poskytuje Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny** : povolení Azure DDoS Protection Standard ve virtuálních sítích Azure přidružených k vašim Key Vault instancím pro ochranu před distribuovanými útoky DoS (Denial-of-Service). K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

 
Správa Azure DDoS Protection Standard pomocí Azure Portal: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Detekce hrozeb pro vrstvu služby Azure v Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Doprovodné** materiály: Azure Key Vault nepoužívá skupiny zabezpečení sítě (NSG) a protokoly toků pro Azure Key Vault nejsou zachyceny. Místo toho pomocí privátního odkazu Azure Zabezpečte své instance Azure Key Vault a povolte nastavení diagnostiky pro zaznamenávání metrik a událostí auditu.

Integrace Key Vault s privátním odkazem na Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service

Protokolování Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny** : Tento požadavek je možné splnit konfigurací rozšířené ochrany před internetovými útoky (ATP) pro Azure Key Vault. ATP poskytuje další vrstvu zabezpečení, která je součástí služby Security Intelligence. Tento nástroj detekuje potenciálně nebezpečné pokusy o přístup k účtům Azure Key Vault a jejich zneužití.

Když Azure Security Center detekuje aktivitu neobvyklé, zobrazí výstrahy. Také pošle e-mailem správce předplatného s podrobnostmi o podezřelé aktivitě a doporučeních k tomu, jak zjistit a opravit zjištěné hrozby.

Nastavte rozšířenou ochranu před internetovými útoky pro Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků, které potřebují přístup k instancím Azure Key Vault, použijte značky služeb Azure pro Azure Key Vault k definování řízení přístupu k síti u skupin zabezpečení sítě nebo na Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. ApiManagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Přehled značek služeb Azure: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny** : definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené k vašim Azure Key Vault instancí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. klíčů a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich Azure Key Vault. Můžete také využít integrované definice zásad související s Azure Key Vault, například:

Key Vault by měl používat koncový bod služby virtuální sítě

Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy ukázky:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Rychlý Start: definování a přiřazení podrobného plánu na portálu:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro prostředky, které se týkají zabezpečení sítě a toku provozu pro vaše Azure Key Vault instance pro poskytování metadat a logické organizace.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

Použití značek k uspořádání prostředků Azure:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny** : pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi Azure Key Vault instancemi. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

Zobrazit a načíst události protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Vytváření, zobrazování a správa výstrah protokolu aktivit pomocí Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Doprovodné** materiály: nepoužitelné; Společnost Microsoft udržuje zdroj času, který se používá pro prostředky Azure, například Azure Key Vault, pro časová razítka v protokolech.


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny** : ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení vygenerovaných Azure Key Vault. V rámci Azure Monitor můžete k dotazování a provádění analýz použít pracovní prostor Azure Log Analytics a používat účty Azure Storage k dlouhodobému a Archivačnímu úložišti. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

Protokolování Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Rychlý Start: jak na desku zapnout službu Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny** : povolení nastavení diagnostiky na vašich instancích Azure Key Vault pro přístup k protokolům auditu, zabezpečení a diagnostiky. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

Protokolování Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor pro pracovní prostor Log Analytics, který se používá k ukládání protokolů Azure Key Vault, nastavte dobu uchování podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

Změnit dobu uchovávání dat: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny** : analýza a sledování protokolů pro neobvyklé chování a pravidelné kontroly výsledků pro prostředky chráněné vaším Azure Key Vault. Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo jiného dodavatele SIEM. 

Rychlý Start: zprovoznění služby Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Začínáme s Log Analytics v Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Začínáme s dotazy protokolu v Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Doprovodné** materiály: v Azure Security Center povolte pro Key Vault rozšířenou ochranu před internetovými útoky (ATP). Povolte nastavení diagnostiky v Azure Key Vault a odešlete protokoly do pracovního prostoru Log Analytics. Připojte pracovní prostor Log Analytics do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení.

Rychlý Start: zprovoznění služby Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Správa a reakce na výstrahy zabezpečení v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Reakce na události s výstrahami Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: nepoužitelné; Azure Key Vault nezpracovává ani nevytváří protokoly související s malwarem.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: nepoužitelné; Azure Key Vault nezpracovává ani nevytváří protokoly související s DNS.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: udržování inventáře vašich Azure Active Directorych aplikací registrovaných i všech uživatelských účtů, které mají přístup k vašim Azure Key Vault klíčům, tajným klíčům a certifikátům. K dotazování a sjednocení Key Vault přístupu můžete použít buď Azure Portal, nebo PowerShell. Chcete-li zobrazit přístup v prostředí PowerShell, použijte následující příkaz:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Properties. AccessPolicies

Registrace aplikace s Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Zabezpečený přístup k trezoru klíčů:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: nepoužitelné; Azure Key Vault nemá koncept výchozích hesel, protože ověřování zajišťuje služba Active Directory a je zabezpečené pomocí řízení přístupu na základě role.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem použití vyhrazených účtů pro správu, které mají přístup k vašim instancím Azure Key Vault. Pomocí Azure Security Center správy identit a přístupu (aktuálně ve verzi Preview) můžete monitorovat počet aktivních účtů pro správu.

Monitorovat identitu a přístup (Preview):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Pokyny** : pomocí instančního objektu Azure ve spojení s AppID, TenantID a ClientSecret můžete hladce ověřit vaši aplikaci a načíst token, který se použije pro přístup k Azure Key Vault tajným klíčům.

Ověřování služba-služba pro Azure Key Vault pomocí rozhraní .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: povolte Azure Active Directory Multi-Factor Authentication a sledujte Azure Security Center doporučení pro správu identit a přístupu (aktuálně ve verzi Preview), která vám pomůžou chránit vaše prostředky s podporou centra událostí.

Plánování cloudového nasazení Azure Multi-Factor Authentication:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Monitorovat identitu a přístup (Preview):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny** : použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) se službou Azure Multi-Factor Authentication (MFA) nakonfigurovanou pro přihlášení a konfiguraci Key Vaultch povolených prostředků. 

Pracovní stanice privilegovaného přístupu: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Plánování cloudového nasazení Azure Multi-Factor Authentication: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Doprovodné** materiály: použijte Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy týkající se rizikového chování uživatelů. Pro další protokolování odešlete výstrahy Azure Security Center detekce rizik do Azure Monitor a nakonfigurujte vlastní výstrahy a oznámení pomocí skupin akcí.

Povolit rozšířenou ochranu před internetovými útoky (ATP) pro Azure Key Vault pro generování výstrah pro podezřelou aktivitu.

Nasadit Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Nastavte rozšířenou ochranu před internetovými útoky pro Azure Key Vault (Preview): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Výstrahy pro Azure Key Vault (Preview): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory detekce rizik: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Vytváření a Správa skupin akcí v Azure Portal: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny** : umožňuje nakonfigurovat podmínku umístění zásad podmíněného přístupu a spravovat pojmenovaná umístění. Pomocí pojmenovaných umístění můžete vytvořit logická seskupení rozsahů IP adres nebo zemí a oblastí. Přístup k citlivým prostředkům, jako jsou Key Vault tajné klíče, můžete omezit na vaše nakonfigurovaná pojmenovaná umístění.

Jaká je podmínka umístění v Azure Active Directory podmíněný přístup?: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: použití Azure Active Directory (Azure AD) jako centrálního ověřování a autorizačního systému pro prostředky Azure, jako je například Key Vault. To umožňuje řízení přístupu na základě role (RBAC) spravovat citlivé prostředky.

 

Rychlý Start: vytvoření nového tenanta v Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Pokyny** : Projděte si protokoly Azure Active Directory (Azure AD), které vám pomůžou zjistit zastaralé účty s Azure Key Vault administrativní role. Navíc můžete pomocí kontrol přístupu Azure AD efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím, které se dají použít pro přístup k Azure Key Vault a přiřazení rolí. Přístup uživatelů by se měl pravidelně kontrolovat, například každých 90 dní, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

Azure Active Directory sestavy a monitorovací dokumentace:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Co jsou kontroly přístupu Azure AD?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Doprovodné** materiály: povolení nastavení diagnostiky pro Azure Key Vault a Azure Active Directory a posílání všech protokolů do log Analyticsho pracovního prostoru. Nakonfigurujte požadovaná upozornění (například pokusy o přístup k zakázaným tajným klíčům) v rámci Log Analytics.

Integrace protokolů služby Azure AD s protokoly Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrace ze starého řešení Key Vault: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Pokyny** : pomocí funkcí Azure Active Directory ochrany identit a detekce rizik můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se vašich Azure Key Vault chráněných prostředků. Pomocí služby Azure Sentinel byste měli povolit automatizované odezvy pro implementaci reakcí zabezpečení vaší organizace. 

Sestava rizikových přihlášení na portálu Azure Active Directory: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Postupy: konfigurace a povolení zásad rizik: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak připojit Azure Sentinel:  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: nepoužitelné; Customer Lockbox se pro Azure Key Vault nepodporuje.

Podporované služby a scénáře ve všeobecné dostupnosti: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: pomocí značek pomáhají při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace na Azure Key Vault povolené prostředky. 

Použití značek k uspořádání prostředků Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny** : pomocí koncových bodů služby virtuální sítě, které jsou nakonfigurovány k omezení přístupu ke konkrétním podsítím, můžete zabezpečit přístup k Azure Key Vault.

Po uplatnění pravidel brány firewall můžete provádět operace Azure Key Vault roviny dat pouze v případě, že požadavek pochází z povolených podsítí nebo rozsahů IP adres. To platí také pro Azure Key Vault přístup v Azure Portal. I když můžete z Azure Portal přejít k trezoru klíčů, možná nebudete moct zobrazit seznam klíčů, tajných kódů ani certifikátů, pokud klientský počítač není na seznamu povolených. To má vliv také na Azure Key Vault pro výběr a další služby Azure. Možná budete moct zobrazit seznamy trezorů klíčů, ale ne seznam klíčů, pokud pravidla firewallu brání vašemu klientskému počítači.

Nakonfigurovat Azure Key Vault brány firewall a virtuální sítě: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Koncové body služby virtuální sítě pro Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: všechna data uložená v Azure Key Vault se považují za citlivá. Pro řízení přístupu k Azure Key Vault tajným klíčům použijte Azure Key Vault řízení přístupu k rovině dat. K řízení přístupu v síťové vrstvě můžete použít také vestavěnou bránu firewall Key Vault. Pokud chcete monitorovat přístup k Azure Key Vault, povolte Key Vault nastavení diagnostiky a odešlete protokoly do účtu Azure Storage nebo Log Analytics pracovního prostoru.

Zabezpečený přístup k trezoru klíčů: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Nakonfigurovat Azure Key Vault brány firewall a virtuální sítě: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Protokolování Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: veškerý provoz Azure Key Vault pro ověřování, správu a přístup k rovině dat je zašifrovaný a projde přes protokol https: port 443. (Pro seznam CRL bude ale občas možné provozovat přenosy HTTP [port 80].) Azure Key Vault nadále povoluje ingestování dat TLS 1,1 a TLS 1,0. Data mohou být omezena na TLS 1,2 prostřednictvím konfigurace na straně klienta.



Přístup k Azure Key Vault za bránou firewall: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: nepoužitelné; všechna data v Azure Key Vault (tajné klíče, klíče a certifikáty) se považují za citlivá.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Doprovodné** materiály: zabezpečený přístup k rovině správy a dat vašich Azure Key Vaultch instancí.

Zabezpečený přístup k trezoru klíčů:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: Společnost Microsoft spravuje základní infrastrukturu pro Azure Key Vault a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

Co je Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Ochrana zákaznických dat Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: všechny spravované objekty (klíče, certifikáty a tajné klíče) jsou v klidovém stavu zašifrované Azure Key Vault.

Podpůrná dokumentace:

- [Model šifrování a tabulka správy klíčů](../../security/fundamentals/encryption-atrest.md)


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny** : pomocí řešení Azure Key Vault Analytics v Azure monitor Zkontrolujte protokoly událostí auditu Azure Key Vault.

Řešení Azure Key Vault Analytics v Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Pokyny** : Společnost Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Azure Key Vault.


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: není k dispozici; Microsoft provádí správu oprav pro základní systémy, které podporují Key Vault.

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Pokyny** : Společnost Microsoft provádí správu ohrožení zabezpečení v základních systémech, které podporují Key Vault.

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: použijte výchozí hodnocení rizik (zabezpečené skóre) poskytované Azure Security Center.

Vylepšete Vaše zabezpečené skóre v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny** : k dotazování a zjišťování všech prostředků (včetně instancí Azure Key Vault) v rámci vašeho předplatného použijte Azure Resource Graph. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Získání předplatných, ke kterým má aktuální účet přístup:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Co je řízení přístupu na základě role v Azure (Azure RBAC)?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro Azure Key Vault prostředků, které dávají metadata k logickému uspořádání do taxonomie.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Azure Key Vaultch instancí a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Vytvořte další předplatné Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Vytvoření skupin pro správu pro organizaci a správu prostředků:

https://docs.microsoft.com/azure/governance/management-groups/create

Použití značek k uspořádání prostředků Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: udržování inventáře schválených prostředků Azure a softwarových titulů

**Doprovodné** materiály: definování seznamu schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: pomocí zásad Azure můžete nastavovat omezení pro typ prostředků, které se dají vytvořit v předplatných zákazníků, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Kromě toho můžete pomocí grafu prostředků Azure dotazovat nebo zjišťovat prostředky v rámci předplatných.

Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro Azure jako celek a také pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: pomocí zásad Azure můžete nastavovat omezení pro typ prostředků, které se dají vytvořit v předplatných zákazníků, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy ukázky: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: Omezte schopnost uživatelů pracovat se AzureResources Managerem prostřednictvím skriptů.

**Pokyny** : pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat s Azure Resource Manager (ARM) tak, že pro aplikaci Microsoft Azure Management nakonfigurujete "blokování přístupu". To může zabránit vytváření a změnám prostředků v rámci prostředí s vysokým zabezpečením, jako jsou ty s konfigurací Key Vault.

Správa přístupu ke správě Azure pomocí podmíněného přístupu:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Doprovodné** materiály: použijte aliasy Azure Policy v oboru názvů "Microsoft. klíčů trezor" k vytvoření vlastních zásad pro auditování nebo vymáhání konfigurace vašich Azure Key Vaultch instancí. Můžete také použít předdefinované definice Azure Policy pro Azure Key Vault, například:

Key Vault objekty by měly být obnovitelné

Nasazení nastavení diagnostiky pro Key Vault k pracovnímu prostoru Log Analytics

Měly by být povolené diagnostické protokoly v Key Vault.

Key Vault by měl používat koncový bod služby virtuální sítě

Nasazení nastavení diagnostiky pro Key Vault do centra událostí

Použijte doporučení z Azure Security Center jako standardní hodnoty konfigurace pro vaše Azure Key Vault instance.

Jak zobrazit dostupné aliasy Azure Policy:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistují] pro vymáhání zabezpečených nastavení napříč prostředky s povolenými Azure Key Vault. 

Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Pochopení Azure Policych účinků: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny** : Pokud používáte vlastní definice Azure Policy pro prostředky s povoleným Azure Key Vault, používejte Azure Repos k bezpečnému ukládání a správě kódu.

Jak ukládat kód v Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure Repos dokumentaci: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Pokyny** : použijte aliasy Azure Policy v oboru názvů "Microsoft. klíčů trezor" k vytváření vlastních zásad pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Pokyny** : použití Azure Security Center k provádění kontrol standardních hodnot vašich prostředků chráněných Azure Key Vault 

  

Postup nápravy doporučení v Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Tento srovnávací test je určený pro výpočetní prostředky.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace. Ujistěte se, že je povoleno Azure Key Vault obnovitelné odstranění.

Jak integrovat se spravovanými identitami Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Postup vytvoření Key Vault:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Ověření Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Jak přiřadit zásadu přístupu Key Vault:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: použití identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů pro vaše cloudové aplikace. 

  

* [Integrace se spravovanými identitami Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Vytvoření Key Vault](quick-create-portal.md)

* [Ověření Key Vault](authentication.md)

* [Postup přiřazení zásady přístupu Key Vault](assign-access-policy-portal.md)

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny** : implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.  
  
 Jak nastavit skener přihlašovacích údajů: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Microsoft zpracovává anti-malware pro základní platformu.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny** : ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure Key Vault), ale nespouští se u zákaznického obsahu.

Předem prohledejte veškerý obsah, který se nahrává nebo odesílá do nevýpočetních prostředků Azure, jako je například Azure Key Vault. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

Pochopení ochrany proti malwaru Microsoftu pro Azure Cloud Services a Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Microsoft zpracovává anti-malware pro základní platformu.


**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: Zajistěte pravidelné automatizované zálohování Key Vault certifikátů, klíčů, spravovaných účtů úložiště a tajných kódů s následujícími příkazy PowerShellu:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

V případě potřeby můžete ukládat zálohy Key Vault v Azure Backup.

Postup zálohování certifikátů Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Postup zálohování klíčů Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Postup zálohování Key Vault spravovaných účtů úložiště: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Postup při zálohování Key Vault tajných kódů: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Postup povolení Azure Backup: https://docs.microsoft.com/azure/backup



**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Doprovodné** materiály: proveďte zálohy certifikátů Key Vault, klíčů, spravovaných účtů úložiště a tajných kódů s následujícími příkazy PowerShellu:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

V případě potřeby můžete ukládat zálohy Key Vault v Azure Backup.

Postup zálohování certifikátů Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Postup zálohování klíčů Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Postup zálohování Key Vault spravovaných účtů úložiště: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Postup při zálohování Key Vault tajných kódů: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Postup povolení Azure Backup: https://docs.microsoft.com/azure/backup



**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Doprovodné** materiály: pravidelně provádějte obnovení dat Key Vault certifikátů, klíčů, spravovaných účtů úložiště a tajných kódů s následujícími příkazy PowerShellu:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Postup obnovení Key Vaultch certifikátů:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Postup obnovení klíčů Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Postup obnovení Key Vault spravovaných účtů úložiště: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Postup obnovení Key Vault tajných kódů: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Pokyny** : Ujistěte se, že je pro Azure Key Vault povoleno obnovitelné odstranění. Obnovitelné odstranění umožňuje obnovit odstraněné trezory klíčů a objekty trezoru, jako jsou klíče, tajné klíče a certifikáty. 

Použití obnovitelného odstranění Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny** : Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech. Tyto procesy by měly mít fokus na ochranu citlivých systémů, jako jsou například pomocí Key Vault tajných klíčů.

Postup konfigurace automatizace pracovních postupů v rámci Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie centra Microsoft Security Response Center:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění. Kromě toho jasně označte odběry (pro např. produkční, neprodukční) a vytvoření názvového systému, který umožňuje snadno identifikovat a kategorizovat prostředky Azure, zejména ty, které zpracovávají citlivá data, například Azure Key Vault tajných údajů.


**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou chránit vaše Azure Key Vaulté instance a související prostředky. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

Přečtěte si téma publikace NIST: Průvodce pro testování, školení a cvičení programů pro plány a možnosti IT: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center** : nelze použít

**Zodpovědnost** : zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

Jak nastavit Azure Security Center kontakt zabezpečení:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Azure Security Center** : Ano

**Zodpovědnost** : zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky s povoleným Azure Key Vault. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem.  Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel. 

 

Postup konfigurace průběžného exportu: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Jak streamovat výstrahy do Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich chráněných prostředků Azure Key Vault. 

 

Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Azure Security Center** : aktuálně není k dispozici.

**Zodpovědnost** : zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech důležitých zjištění zabezpečení do 60 dnů.

**Pokyny** : neprovádíte přímé testování perem u Azure Key Vault služby, ale doporučujeme, abyste otestovali prostředky Azure, které používají Key Vault k zajištění zabezpečení tajných kódů.

Abyste zajistili, že testy průniku nebudou mít porušení zásad Microsoftu, budete muset postupovat podle pravidel Microsoft Engagement:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

V této části najdete další informace o strategii a provádění testování na základě červeného týmu a testování průniku na cloudové infrastruktuře, služby a aplikace spravované Microsoftem: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center** : nelze použít

**Odpovědnost** : sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../../security/benchmarks/overview.md)
- Další informace o [plánech zabezpečení Azure](../../security/benchmarks/security-baselines-overview.md)