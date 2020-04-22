---
title: Standardní hodnoty zabezpečení Azure pro službu Key Vault
description: Standardní hodnoty zabezpečení Azure pro službu Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 6e660c1244dd5566fbfb45a6da37d39294354ccb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756985"
---
# <a name="azure-security-baseline-for-key-vault"></a>Standardní hodnoty zabezpečení Azure pro službu Key Vault

Směrný plán zabezpečení Azure pro trezor klíčů obsahuje doporučení, která vám pomohou zlepšit stav zabezpečení vašeho nasazení.

Směrný plán pro tuto službu vychází z [azure security benchmark verze 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure s našimi pokyny pro osvědčené postupy.

Další informace naleznete v [tématu Přehled směrného plánu zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace naleznete v [tématu Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

**Navádění:** Integrace Azure Key Vault s Azure Private Link. 

Služba Azure Private Link Service umožňuje přístup ke službám Azure Services (například Azure Key Vault) a službám hostovaným u zákazníků a partnerů Azure přes privátní koncový bod ve vaší virtuální síti.

Azure Private Endpoint je síťové rozhraní, které vás soukromě a bezpečně propojuje se službou využívající Azure Private Link. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě, efektivně uvedení služby do virtuální sítě. Veškerý provoz na službu lze směrovat přes soukromý koncový bod, takže nejsou potřeba žádné brány, zařízení NAT, připojení ExpressRoute nebo VPN nebo veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, což vám nejvyšší úroveň rozlišovací schopnost v řízení přístupu.

Jak integrovat trezor klíčů s Privátním propojením Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Sledování a protokolování konfigurace a provozu sítí, podsítí a síťových rozhraní

**Pokyny:** Pomocí Azure Security Center a postupujte podle doporučení ochrany sítě, abyste pomohli zabezpečit prostředky konfigurované trezorem klíčů v Azure. 

Další informace o zabezpečení sítě poskytované službou Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="13-protect-critical-web-applications"></a>1.3: Ochrana kritických webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Odepřít komunikaci se známými škodlivými IP adresami

**Pokyny:** Povolte Azure DDoS Protection Standard na virtuálních sítích Azure přidružené k instancím trezoru klíčů pro ochranu proti distribuovaným útokům s cílem odepření služby. Pomocí integrované analýzy hrozeb Centra zabezpečení Azure můžete odepřít komunikaci se známými škodlivými nebo nepoužívanými internetovými IP adresami.

 
Správa Azure DDoS Protection Standard pomocí portálu Azure:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Zjišťování hrozeb pro vrstvu služeb Azure v Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Záznam síťových paketů a protokolů toku

**Navádění**: Azure Key Vault nepoužívá skupiny zabezpečení sítě (NSG) a protokoly toku pro Azure Key Vault nejsou zachyceny. Místo toho použijte Azure Private Link k zabezpečení instancí Azure Key Vault a povolit nastavení diagnostiky pro záznam metrik a události auditu.

Integrace trezoru klíčů s privátním propojením Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service

Protokolování trezoru klíčů Azure:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Nasazení síťových systémů detekce vniknutí/prevence vniknutí (IDS/IPS)

**Pokyny:** Tento požadavek lze splnit konfigurací rozšířené ochrany před hrozbami (ATP) pro Azure Key Vault. Atp poskytuje další vrstvu inteligence zabezpečení. Tento nástroj detekuje potenciálně škodlivé pokusy o přístup nebo zneužití účtů Azure Key Vault.

Když Azure Security Center zjistí neobvyklé aktivity, zobrazí výstrahy. Také e-maily správce předplatného s podrobnostmi o podezřelé aktivity a doporučení, jak prozkoumat a napravit identifikované hrozby.

Nastavte pokročilou ochranu před hrozbami pro Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Správa návštěvnosti webových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimalizace složitosti a administrativní režie pravidel zabezpečení sítě

**Pokyny:** Pro prostředky, které potřebují přístup k vašim instancím Azure Key Vault, použijte značky služeb Azure pro Azure Key Vault k definování ovládacích prvků přístupu k síti ve skupinách zabezpečení sítě nebo azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. apimanagement) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo jako adresy změnit.

Přehled značek služeb Azure:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Udržovat standardní konfigurace zabezpečení pro síťová zařízení

**Pokyny:** Definujte a implementujte standardní konfigurace zabezpečení pro síťové prostředky přidružené k instancím Azure Key Vault s Azure Policy. Pomocí aliasů zásad Azure v oborech názvů Microsoft.KeyVault a Microsoft.Network vytvořte vlastní zásady pro auditování nebo vynucení síťové konfigurace instancí trezoru klíčů Azure. Můžete také využít předdefinované definice zásad související s Azure Key Vault, například:

Trezor klíčů by měl používat koncový bod služby virtuální sítě.

Kurz: Vytvářejte a spravujte zásady pro vynucení dodržování předpisů:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ukázky zásad Azure:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Úvodní příručka: Definujte a přiřaďte podrobný plán na portálu:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentovat pravidla konfigurace provozu

**Pokyny:** Pomocí značek pro prostředky související se zabezpečením sítě a tokem provozu pro instance Azure Key Vault poskytují metadata a logické organizace.

Pomocí některé z předdefinovaných definic zásad Azure souvisejících s označováním, jako je například Vyžadovat značku a její hodnotu, můžete zajistit, aby se všechny prostředky vytvořily pomocí značek, a upozornit vás na existující neoznačené prostředky.

Azure PowerShell nebo Azure CLI můžete použít k vyvyhledávání nebo provádění akcí na prostředky na základě jejich značek.

K uspořádání prostředků Azure použijte značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Použití automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

**Pokyny:** Pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků souvisejících s instancemi trezoru klíčů Azure. Vytvořte výstrahy v rámci Azure Monitor, který se aktivuje, když dojde ke změnám kritických síťových prostředků.

Zobrazení a načtení událostí protokolu aktivit Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Vytvářejte, zobrazujte a spravujte výstrahy protokolu aktivit pomocí Azure Monitoru:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace naleznete [v tématu Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Použití schválených zdrojů synchronizace času

**Pokyny**: Nepoužije se; Microsoft udržuje zdroj času používaný pro prostředky Azure, jako je azure key vault, pro časová razítka v protokolech.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurace správy centrálního protokolu zabezpečení

**Pokyny:** Ingestování protokolů prostřednictvím Azure Monitor agregovat data zabezpečení generované Azure Key Vault. V rámci Azure Monitoru můžete pomocí pracovního prostoru Azure Log Analytics dotazovat a provádět analýzy a používat účty úložiště Azure pro dlouhodobé úložiště a archivní úložiště. Případně můžete povolit a na palubě data do Azure Sentinelu nebo siem třetí strany. 

Protokolování trezoru klíčů Azure:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Úvodní příručka: Jak napalubě Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Povolení protokolování auditování prostředků Azure

**Pokyny:** Povolte nastavení diagnostiky v instanci Azure Key Vault pro přístup k protokolům auditování, zabezpečení a diagnostiky. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

Protokolování trezoru klíčů Azure:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Shromažďování protokolů zabezpečení z operačních systémů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny:** V rámci Azure Monitor, pro pracovní prostor Log Analytics slouží k uložení protokolů Azure Key Vault, nastavte dobu uchování v souladu s předpisy vaší organizace dodržování předpisů. Účty úložiště Azure používejte pro dlouhodobé nebo archivní úložiště.

Změna doby uchovávání dat:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="26-monitor-and-review-logs"></a>2.6: Sledování a kontrola protokolů

**Pokyny:** Analyzujte a monitorujte protokoly neobvyklé chování a pravidelně kontrolujte výsledky prostředků chráněných úložištěm klíčů Azure. Pomocí pracovního prostoru Služby Log Analytics služby Azure Monitor můžete kontrolovat protokoly a provádět dotazy na data protokolu. Případně můžete povolit a na palubě data azure sentinelu nebo třetí strany SIEM. 

Úvodní příručka: Na palubě Azure Sentinelu:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Začínáme s Analýzou protokolů ve službě Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Začínáme s dotazy protokolů v Azure Monitoru:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Povolit upozornění na anomální aktivitu

**Pokyny:** V Centru zabezpečení Azure povolte pokročilou ochranu před hrozbami (ATP) pro trezor klíčů. Povolte diagnostická nastavení v trezoru klíčů Azure a odesílejte protokoly do pracovního prostoru Analýzy protokolů. Založte pracovní prostor Analýzy protokolů do Azure Sentinelu, protože poskytuje řešení automatické odpovědi orchestrace zabezpečení (SOAR). To umožňuje vytvářet a používat playbooky (automatizovaná řešení) k nápravě problémů se zabezpečením.

Úvodní příručka: Na palubě Azure Sentinelu:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Správa výstrah zabezpečení a reakce na ně v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Reagujte na události pomocí výstrah azure monitoru:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizovat protokolování antimalwaru

**Pokyny**: Nepoužije se; Azure Key Vault nezpracovává ani nevytváří protokoly související s antimalwarem.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="29-enable-dns-query-logging"></a>2.9: Povolení protokolování dotazů DNS

**Pokyny**: Nepoužije se; Azure Key Vault nezpracovává ani nevytváří protokoly související se službou DNS.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="210-enable-command-line-audit-logging"></a>2.10: Povolení protokolování auditu příkazového řádku

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace naleznete [v tématu Zabezpečení: Identita a řízení přístupu](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Vedení soupisu administrativních účtů

**Pokyny:** Udržujte inventář aplikací registrovaných službou Azure Active Directory a také všechny uživatelské účty, které mají přístup k vašim klíčům, tajným klíčům a certifikátům služby Azure Key Vault. Můžete použít buď portál Azure nebo PowerShell k dotazování a odsouhlasení přístupu trezoru klíčů. Chcete-li zobrazit přístup v PowerShellu, použijte následující příkaz:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Vlastnosti.Zásady přístupu

Registrace aplikace pomocí služby Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Bezpečný přístup do trezoru klíčů:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Změna výchozích hesel, pokud je to možné

**Pokyny**: Nepoužije se; Azure Key Vault nemá koncept výchozích hesel, protože ověřování poskytuje služba Active Directory a zabezpečuje se řízením přístupu na základě rolí.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Použití vyhrazených administrativních účtů

**Pokyny:** Vytvořte standardní operační postupy týkající se použití vyhrazených účtů pro správu, které mají přístup k instancím azure key vault. Pomocí správy identit a přístupu Centra zabezpečení Azure (aktuálně ve verzi Preview) můžete sledovat počet aktivních účtů pro správu.

Sledování identity a přístupu (náhled):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Použití jednotného přihlašování (SSO) s Azure Active Directory

**Pokyny**: Použijte instanční objekt Služby Azure ve spojení s AppId, TenantID a ClientSecret, chcete-li bezproblémově ověřit vaši aplikaci a načíst token, který se použije pro přístup k tajným klíčům Azure Vault.

Ověřování služby azure key vault pomocí rozhraní .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Použití vícefaktorového ověřování pro veškerý přístup založený na službě Azure Active Directory

**Pokyny:** Povolte vícefaktorové ověřování Azure Active Directory a postupujte podle doporučení Centra identit a přístupu Azure Security Center (aktuálně ve verzi Preview), abyste pomohli chránit prostředky centra událostí.

Plánování cloudového nasazení Azure Multi-Factor Authentication:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Sledování identity a přístupu (náhled):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Pro všechny administrativní úkoly používejte vyhrazené počítače (pracovní stanice s privilegovaným přístupem)

**Pokyny:** Použijte pracovní stanici s privilegovaným přístupem (PAW) s Azure Multi-Factor Authentication (MFA) nakonfigurovanou pro přihlášení a konfiguraci prostředků s povoleným trezorem klíčů. 

Pracovní stanice s privilegovaným přístupem:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Plánování cloudového nasazení Azure Multi-Factor Authentication:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokolování a upozornění na podezřelou aktivitu z administrativních účtů

**Pokyny:** Použití Azure Active Directory (AAD) Privileged Identity Management (PIM) pro generování protokolů a výstrah, když dojde k podezřelé nebo nebezpečné aktivity v prostředí. Pomocí detekce rizik AAD můžete zobrazit výstrahy a sestavy o rizikovém chování uživatelů. Další protokolování můžete zasílat výstrahy Azure Security Center pro zjišťování rizik do Azure Monitoru a konfigurovat vlastní výstrahy/oznámení pomocí skupin akcí.

Povolte pokročilou ochranu před hrozbami (ATP) pro Azure Key Vault a vygenerujte výstrahy pro podezřelou aktivitu.

Nasazení správy privilegovaných identit Azure AD (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Nastavení pokročilé ochrany před hrozbami pro Azure Key Vault (preview):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Výstrahy pro trezor klíčů Azure (náhled):https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Detekce rizik služby Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Vytváření a správa skupin akcí na webu Azure Portal:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Správa prostředků Azure z jenom schválených umístění

**Pokyny**: Nakonfigurujte podmínku umístění zásad podmíněného přístupu a spravujte pojmenovaná umístění. S pojmenovanými umístěními můžete vytvořit logická seskupení rozsahů IP adres nebo zemí a oblastí. Přístup k citlivým prostředkům, jako jsou tajné klíče trezoru klíčů, můžete omezit na nakonfigurovaná pojmenovaná umístění.

Jaká je podmínka umístění v podmíněném přístupu služby Azure Active Directory?:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="39-use-azure-active-directory"></a>3.9: Použití služby Azure Active Directory

**Pokyny:** Použijte Azure Active Directory (AAD) jako centrální ověřovací a autorizační systém pro prostředky Azure, jako je trezor klíčů. To umožňuje řízení přístupu na základě rolí (RBAC) spravovat citlivé prostředky.

 

Úvodní příručka: Vytvoření nového klienta ve službě Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Pravidelně kontrolujte a slaďujte přístup uživatelů

**Pokyny:** Zkontrolujte protokoly služby Azure Active Directory (AAD), které vám pomohou zjistit zastaralé účty s rolemi správy Azure Key Vault. Kromě toho použijte kontroly přístupu AAD efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím, které lze použít pro přístup k Azure Key Vault a přiřazení rolí. Přístup uživatelů by měl být pravidelně kontrolován, například každých 90 dní, aby se zajistilo, že pouze ti praví uživatelé mají trvalý přístup.

Sestavy služby Azure Active Directory a dokumentace monitorování:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Co jsou kontroly přístupu k Azure AD?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Sledování pokusů o přístup k deaktivovaným účtům

**Pokyny:** Povolte diagnostická nastavení pro Azure Key Vault a Azure Active Directory a odesílat všechny protokoly do pracovního prostoru Analýzy protokolů. Nakonfigurujte požadované výstrahy (například pokusy o přístup k zakázaným tajným kódům) v rámci analýzy protokolů.

Integrace protokolů Azure AD s protokoly Azure Monitoru:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrace ze starého řešení trezoru klíčů:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Upozornění na odchylku chování přihlášení k účtu

**Pokyny:** Pomocí funkcí ochrany identity azure active directory a detekce rizik nakonfigurujte automatické odpovědi na zjištěné podezřelé akce související s prostředky chráněnými úložištěm klíčů Azure. Měli byste povolit automatické odpovědi prostřednictvím Azure Sentinelu k implementaci odpovědí na zabezpečení vaší organizace. 

Sestava rizikových přihlášení na portálu Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Postup: Konfigurace a povolení zásad rizik:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Jak napalubě Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Poskytněte společnosti Microsoft přístup k relevantním zákaznickým datům během scénářů podpory

**Pokyny**: Nepoužije se; Customer Lockbox není podporován pro Azure Key Vault.

Podporované služby a scénáře v obecné dostupnosti:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="data-protection"></a>Ochrana dat

*Další informace naleznete [v tématu Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Udržovat soupis citlivých informací

**Pokyny:** Pomocí značek můžete při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace o prostředcích s povoleným úložištěm klíčů Azure. 

K uspořádání prostředků Azure použijte značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Izolovat systémy ukládající nebo zpracovávající citlivé informace

**Pokyny:** Můžete zabezpečit přístup k Azure Key Vault pomocí koncových bodů služby virtuální sítě nakonfigurovaných k omezení přístupu k určitým podsítím.

Po nastavení pravidel brány firewall můžete provádět operace datové roviny služby Azure Key Vault pouze v případě, že váš požadavek pochází z povolených podsítí nebo rozsahů IP adres. To platí také pro přístup k Azure Key Vault na webu Azure Portal. I když můžete přejít do trezoru klíčů z portálu Azure, nemusí být možné seznam klíčů, tajných kódů nebo certifikátů, pokud váš klientský počítač není v seznamu povolených. To má vliv také na výběr úložiště klíčů Azure a další služby Azure. Je možné, že se zobrazí seznamy trezorů klíčů, ale nikoli klíče seznamu, pokud pravidla brány firewall brání vašemu počítači v klientském počítači.

Konfigurace bran azure key vault a virtuálních sítí:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Koncové body služby Virtuální sítě pro Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Sledování a blokování neoprávněného přenosu citlivých informací

**Pokyny:** Všechna data uložená v úložišti klíčů Azure vault je považovánza citlivý. Pomocí ovládacích prvků přístupu k datové rovině úložiště klíčů Azure vault můžete řídit přístup k tajným klíčům služby Azure Key Vault. K řízení přístupu v síťové vrstvě můžete také použít vestavěnou bránu firewall trezoru klíčů. Chcete-li monitorovat přístup k úložišti klíčů Azure, povolte nastavení diagnostiky trezoru klíčů a odesílejte protokoly do pracovního prostoru úložiště Azure nebo do pracovního prostoru Log Analytics.

Bezpečný přístup do trezoru klíčů:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Konfigurace bran azure key vault a virtuálních sítí:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Protokolování trezoru klíčů Azure:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Šifrování všech citlivých informací při přepravě

**Naváděcí**pokyny: Veškerý provoz do azure key vault pro ověřování, správu a přístup k rovině dat, je šifrovaný a přejde přes HTTPS: port 443. (Příležitostně však bude pro CRL existovat přenoshttp [port 80].) 

Přístup k úložišti klíčů Azure za bránou firewall:https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

**Pokyny**: Nepoužije se; všechna data v rámci služby Azure Key Vault (tajné klíče, klíče a certifikáty) se považují za citlivé.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Použití Azure RBAC k řízení přístupu k prostředkům

**Navádění:** Zabezpečte přístup ke správě a datové rovině instancí azure key vault.

Bezpečný přístup do trezoru klíčů:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Použití ochrany před ztrátou dat na hostiteli k vynucení kontroly přístupu

**Pokyny**: Microsoft spravuje základní infrastrukturu pro Azure Key Vault a zavedlpřísné ovládací prvky, aby se zabránilo ztrátě nebo vystavení dat zákazníkům.

Co je Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Ochrana zákaznických dat Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Šifrování citlivých informací v klidovém stavu

**Pokyny:** Všechny spravované objekty (klíč, certifikáty a tajné klíče) jsou šifrovány v klidovém stavu v trezoru klíčů Azure.

Dokumentací:

- [Model šifrování a tabulka správy klíčů](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokolování a upozorňování na změny kritických prostředků Azure

**Pokyny:** Pomocí řešení Azure Key Vault Analytics v Azure Monitoru zkontrolujte protokoly událostí auditování azure key vault.

Řešení Azure Key Vault Analytics v Azure Monitoru:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace naleznete v [tématu Security Control: Vulnerability Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Spuštění automatizovaných nástrojů pro vyhledávání chyb zabezpečení

**Pokyny:** Společnost Microsoft provádí správu chyb zabezpečení v základních systémech, které podporují azure key vault.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Nasazení řešení pro správu automatických oprav operačního systému

**Pokyny:** Není k onomké Společnost Microsoft provádí správu oprav v základních systémech, které podporují trezor klíčů.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Nasazení automatizovaného řešení pro správu softwarových oprav od jiných výrobců

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Porovnání prohledávačů ohrožení zabezpečení zády k zadní straně

**Pokyny:** Společnost Microsoft provádí správu chyb zabezpečení v základních systémech, které podporují trezor klíčů.

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Použití procesu hodnocení rizik a upřednostnění nápravy zjištěných zranitelných míst

**Pokyny:** Použijte výchozí hodnocení rizik (Zabezpečené skóre) poskytované Centrem zabezpečení Azure.

Vylepšete své skóre zabezpečení v Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace naleznete v [tématu Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Použití Azure Asset Discovery

**Pokyny:** Pomocí Azure Resource Graph dotaz a zjistit všechny prostředky (včetně instancí Azure Key Vault) v rámci předplatného. Ujistěte se, že máte příslušná (číst) oprávnění ve vašem tenantovi a jsou schopni vytvořit výčet všech předplatných Azure, stejně jako prostředky v rámci vašich předplatných.

Úvodní příručka: Spusťte první dotaz na graf prostředků pomocí Průzkumníka prostředků Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Získejte odběry, ke kterým má přístup běžný účet.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Co je řízení přístupu na základě role (RBAC) u prostředků Azure?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="62-maintain-asset-metadata"></a>6.2: Udržování metadat datových zdrojů

**Pokyny:** Použití značek pro prostředky Azure Key Vault poskytuje metadata logicky uspořádat do taxonomie.

Jak vytvořit a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Odstranění neautorizovaných prostředků Azure

**Pokyny:** Použijte značkování, skupiny pro správu a samostatné předplatná, kde je to vhodné, k uspořádání a sledování instancí Azure Key Vault a souvisejících prostředků. Pravidelně odsouhlasete zásoby a zajistěte, aby byly z předplatného včas odstraněny neoprávněné prostředky.

Vytvořte další předplatné Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Vytvořte skupiny pro správu pro organizaci a správu prostředků:

https://docs.microsoft.com/azure/governance/management-groups/create

K uspořádání prostředků Azure použijte značky:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržovat soupis schválených prostředků Azure a softwarových titulů

**Pokyny:** Definování seznamu schválených prostředků Azure a schváleného softwaru pro vaše výpočetní prostředky

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorování neschválených prostředků Azure

**Pokyny:** Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků

- Povolené typy prostředků

Kromě toho použijte Azure Resource Graph k dotazování/zjišťování prostředků v rámci předplatného.

Kurz: Vytvářejte a spravujte zásady pro vynucení dodržování předpisů:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Úvodní příručka: Spusťte první dotaz na graf prostředků pomocí Průzkumníka prostředků Azure:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Sledování neschválených softwarových aplikací v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků Azure a softwarových aplikací

**Pokyny**: Nepoužije se; Toto doporučení je určené pro Azure jako celek i výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="68-use-only-approved-applications"></a>6.8: Používejte pouze schválené aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="69-use-only-approved-azure-services"></a>6.9: Používejte jenom schválené služby Azure

**Pokyny:** Pomocí zásad Azure můžete omezit typ prostředků, které lze vytvořit v předplatných zákazníků pomocí následujících předdefinovaných definic zásad:

- Nepovolené typy prostředků

- Povolené typy prostředků

Kurz: Vytvářejte a spravujte zásady pro vynucení dodržování předpisů:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ukázky zásad Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Omezte možnost uživatelů komunikovat se Správcem azurezdrojů pomocí skriptů

**Pokyny:** Pomocí podmíněného přístupu Azure omezte možnost uživatelů pracovat se Správcem prostředků Azure (ARM) konfigurací "Blokovat přístup" pro aplikaci "Správa Microsoft Azure". To může zabránit vytváření a změny prostředků v prostředí s vysokým zabezpečením, jako jsou ty s konfigurací trezoru klíčů.

Správa přístupu ke správě Azure pomocí podmíněného přístupu:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Omezení možnosti uživatelů spouštět skripty v rámci výpočetních prostředků

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fyzicky nebo logicky oddělit vysoce rizikové aplikace

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro webové aplikace spuštěné ve službě Azure App Service nebo výpočetních prostředcích.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace naleznete v [tématu Zabezpečení: Zabezpečená konfigurace](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny:** Pomocí aliasů zásad Azure v oboru názvů "Microsoft.KeyVault" můžete vytvořit vlastní zásady pro auditování nebo vynucení konfigurace instancí azure key vault. Můžete také použít integrované definice zásad Azure pro Azure Key Vault, jako jsou:

Objekty trezoru klíčů by měly být obnovitelné

Nasazení diagnostických nastavení pro trezor klíčů do pracovního prostoru Log Analytics

Diagnostické protokoly v trezoru klíčů by měly být povoleny

Trezor klíčů by měl používat koncový bod služby virtuální sítě.

Nasazení diagnostických nastavení trezoru klíčů do centra událostí

Doporučení z Azure Security Center použijte jako směrný plán zabezpečené konfigurace pro instance azure key vaultu.

Jak zobrazit dostupné aliasy zásad Azure:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Kurz: Vytvářejte a spravujte zásady pro vynucení dodržování předpisů:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Vytvoření bezpečných konfigurací operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Udržování zabezpečených konfigurací prostředků Azure

**Pokyny:** Pomocí zásad Azure [odepřít] a [nasadit, pokud neexistují] vynutit zabezpečené nastavení v rámci prostředků s povolenou službou Azure Key Vault. 

Kurz: Vytvářejte a spravujte zásady pro vynucení dodržování předpisů:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Seznamte se s efekty zásad Azure: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Udržovat bezpečné konfigurace operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Konfigurace prostředků Azure bezpečně ukládá

**Pokyny:** Pokud používáte vlastní definice zásad Azure pro prostředky s povoleným úložištěm klíčů Azure, použijte Azure Repos k bezpečnému ukládání a správě kódu.

Jak ukládat kód v Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Dokumentace k azure repos: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpečné ukládání vlastních bitových kopií operačního systému

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Nasazení nástrojů pro správu konfigurace systému

**Pokyny**: Pomocí aliasů zásad Azure v oboru názvů Microsoft.KeyVault vytvořte vlastní zásady pro výstrahu, auditování a vynucení konfigurací systému. Kromě toho vytvořte proces a kanál pro správu výjimek zásad.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Nasazení nástrojů pro správu konfigurace systému pro operační systémy

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementace automatického monitorování konfigurace pro služby Azure

**Pokyny:** Pomocí Centra zabezpečení Azure můžete provádět prohledávací plány prostředků chráněných úložištěm klíčů Azure 

  

Jak napravit doporučení v Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementace automatizovaného monitorování konfigurace operačních systémů

**Pokyny**: Nepoužije se; tento benchmark je určen pro výpočetní prostředky.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpečná správa tajných kódů Azure

**Pokyny:** Pomocí identity spravované služby ve spojení s Azure Key Vault zjednodušit a zabezpečit správu tajných služeb pro vaše cloudové aplikace. Ujistěte se, že azure key vault obnovitelné odstranění je povolena.

Jak integrovat s Azure Spravované identity:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak vytvořit trezor klíčů:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zajistit ověřování trezoru klíčů se spravovanou identitou: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpečná a automatická správa identit

**Pokyny:** Pomocí identity spravované služby ve spojení s Azure Key Vault zjednodušit a zabezpečit správu tajných služeb pro vaše cloudové aplikace. 

  

Jak integrovat s Azure Spravované identity: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Jak vytvořit trezor klíčů: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

Jak zajistit ověřování trezoru klíčů se spravovanou identitou:  
https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminujte nezamýšlenou expozici pověření

**Pokyny**: Implementujte skener pověření k identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů také podpoří přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je azure key vault.  
  
 Jak nastavit skener pověření:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace naleznete v [tématu Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Používejte centrálně spravovaný antimalwarový software

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky. Společnost Microsoft zpracovává antimalwarovou platformu.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

**Pokyny:** Microsoft anti-malware je povolena na podkladovém hostiteli, který podporuje služby Azure (například Azure Key Vault), ale neběží na obsah zákazníka.

Předem proskente veškerý obsah, který se nahrává nebo odesílá do nevýpočetních prostředků Azure, jako je Azure Key Vault. Společnost Microsoft nemá v těchto případech přístup k vašim datům.

Seznamte se s antimalwarovým programem Microsoft pro cloudové služby Azure a virtuální počítače:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Zajistěte aktualizaci antimalwarového softwaru a podpisů

**Pokyny**: Nepoužije se; Toto doporučení je určeno pro výpočetní prostředky. Společnost Microsoft zpracovává antimalwarovou platformu.


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Nepoužije se

## <a name="data-recovery"></a>Obnovení dat

*Další informace naleznete [v tématu Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zajistěte pravidelné automatické zálohování

**Pokyny**: Pomocí následujících příkazů prostředí PowerShell zajistěte pravidelné automatické zálohování certifikátů, klíčů, účtů spravovaného úložiště a tajných kódů úložiště:

- Backup-AzKeyVaultCertificate

- Záložní-AzKeyVaultKey

- Účet Backup-AzKeyVaultManagedStorageAccount

- Zálohování-AzKeyVaultSecret

Volitelně můžete ukládat zálohy trezoru klíčů v rámci služby Azure Backup.

Jak zálohovat certifikáty trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Jak zálohovat klíče trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

How to backup Key Vault Managed Storage Accounts:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Jak zálohovat tajné kódy trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Jak povolit Azure Backup:https://docs.microsoft.com/azure/backup



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

**Pokyny**: Provádějte zálohy certifikátů trezoru klíčů, klíčů, účtů spravovaného úložiště a tajných kódů pomocí následujících příkazů prostředí PowerShell:

- Backup-AzKeyVaultCertificate

- Záložní-AzKeyVaultKey

- Účet Backup-AzKeyVaultManagedStorageAccount

- Zálohování-AzKeyVaultSecret

Volitelně můžete ukládat zálohy trezoru klíčů v rámci služby Azure Backup.

Jak zálohovat certifikáty trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Jak zálohovat klíče trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

How to backup Key Vault Managed Storage Accounts:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Jak zálohovat tajné kódy trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Jak povolit Azure Backup:https://docs.microsoft.com/azure/backup



**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny:** Pravidelně provádějte obnovení dat certifikátů trezoru klíčů, klíčů, účtů spravovaného úložiště a tajných kódů pomocí následujících příkazů prostředí PowerShell:

- Obnovit azKeyVaultCertificate

- Obnovit klíč AzKeyVault

- Obnovení účtu AzKeyVaultManagedStorageAccount

- Obnovit-AzKeyVaultSecret

Jak obnovit certifikáty trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Jak obnovit klíče trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

How to restore Key Vault Managed Storage Accounts:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Jak obnovit tajné klíče trezoru klíčů:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zajištění ochrany záloh a klíčů spravovaných zákazníkem

**Pokyny:** Ujistěte se, že je povoleno obnovitelné odstranění pro Azure Key Vault. Obnovitelné odstranění umožňuje obnovení odstraněných trezorů klíčů a objektů úschovny, jako jsou klíče, tajné klíče a certifikáty. 

Jak používat azure key vault je obnovitelné odstranění: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace naleznete [v tématu Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Vytvoření průvodce odezvou na incidenty

**Pokyny:** Vytvořte průvodce odezvou na incidenty pro vaši organizaci. Ujistěte se, že existují písemné plány reakce na incidenty, které definují všechny role personálu, stejně jako fáze zpracování incidentů / správy od detekce až po kontrolu po incidentu. Tyto procesy by se měly zaměřit na ochranu citlivých systémů, například na systémy používající tajné klíče trezoru klíčů.

Jak nakonfigurovat automatizaci pracovních postupů v Centru zabezpečení Azure: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Pokyny pro vytváření vlastního procesu reakce na bezpečnostní incidenty:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie incidentu v centru Microsoft Security Response Center:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Zákazník může také využít nist počítačové bezpečnosti Incident Handling Guide na pomoc při vytváření vlastního plánu reakce na incidenty: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Vytvoření postupu hodnocení incidentů a stanovení priorit

**Pokyny**: Centrum zabezpečení přiřazuje každé výstraze závažnost, která vám pomůže určit prioritu, které výstrahy by měly být nejprve prošetřeny. Závažnost je založena na tom, jak sebevědomý Security Center je v hledání nebo analytice slouží k vydání výstrahy, stejně jako úroveň spolehlivosti, že tam byl škodlivý záměr za aktivitu, která vedla k upozornění. Navíc jasně označit odběry (pro ex. produkční, non-prod) a vytvořit systém pojmenování pro jasně identifikovat a kategorizovat prostředky Azure, zejména ty, které zpracovávají citlivá data, jako jsou tajné kódy Azure Key Vault.


**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="103-test-security-response-procedures"></a>10.3: Zkušební postupy odezvy zabezpečení

**Pokyny:** Provádění cvičení k testování možností reakce na incidenty vašich systémů na pravidelné kadence pomoci chránit vaše instance Azure Key Vault a související prostředky. Identifikujte slabá místa a mezery a podle potřeby plán revidujte.

Podívejte se na publikaci NIST: Průvodce testovacími, školicími a cvičebními programy pro plány a schopnosti IT: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Poskytněte kontaktní údaje bezpečnostních incidentů a nakonfigurujte upozornění na bezpečnostní incidenty

**Pokyny**: Kontaktní informace o bezpečnostních incidentech společnost Microsoft použije k tomu, aby vás kontaktovala, pokud centrum MSRC (Microsoft Security Response Center) zjistí, že k vašim datům získala přístup nezákonná nebo neoprávněná strana.  Zkontrolujte incidenty po faktu, abyste zajistili, že budou problémy vyřešeny.

Jak nastavit kontakt zabezpečení Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitorování Centra zabezpečení Azure:** Ano

**Odpovědnost**: Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Začleňte výstrahy zabezpečení do systému reakce na incidenty

**Pokyny:** Exportujte výstrahy a doporučení Centra zabezpečení Azure pomocí funkce nepřetržitého exportu, která vám pomůže identifikovat rizika pro prostředky s povoleným úložištěm klíčů Azure. Nepřetržitý export umožňuje exportovat výstrahy a doporučení buď ručně, nebo průběžně.  Datový konektor Azure Security Center můžete použít k streamování výstrah do Azure Sentinelu. 

 

Jak nakonfigurovat nepřetržitý export: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Jak streamovat výstrahy do Azure Sentinelu: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizace reakce na výstrahy zabezpečení

**Pokyny:** Pomocí funkce Automatizace pracovních postupů v Centru zabezpečení Azure můžete automaticky aktivovat odpovědi prostřednictvím "Logic Apps" na výstrahy zabezpečení a doporučení k ochraně prostředků chráněných úložištěm klíčů Azure. 

 

Konfigurace automatizace pracovních postupů a aplikací logiky: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitorování Centra zabezpečení Azure**: Momentálně není k dispozici

**Odpovědnost**: Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace naleznete [v tématu Bezpečnostní kontrola: Penetrační testy a cvičení červeného týmu](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Proveďte pravidelné penetrační testování prostředků Azure a zajistěte nápravu všech kritických zjištění zabezpečení do 60 dnů

**Pokyny:** Není provádět testování pera na azure key vault služby přímo, ale doporučuje se otestovat prostředky Azure, které používají key vault k zajištění zabezpečení tajných kódů.

Budete se muset řídit pravidly společnosti Microsoft, abyste zajistili, že penetrační testy nebudou v rozporu se zásadami společnosti Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Další informace o strategii společnosti Microsoft a provádění červených týmů a testování průniku živých webů proti cloudové infrastruktuře, službám a aplikacím spravovanému společností Microsoft naleznete zde: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitorování Azure Security Center**: Nelze použít

**Odpovědnost**: Sdíleno

## <a name="next-steps"></a>Další kroky

- Podívejte se na [srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Další informace o [plánech zásad zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
