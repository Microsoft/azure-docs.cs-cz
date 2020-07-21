---
title: Azure Database for PostgreSQL – směrný plán zabezpečení s ochranou rozsahu pro Azure Security test
description: Azure Database for PostgreSQL – směrný plán zabezpečení s ochranou rozsahu poskytuje pokyny a prostředky pro implementaci doporučení zabezpečení, která jsou určená v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ca08ac30c5d8e925197a0df1d327bf9a0923b046
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531223"
---
# <a name="azure-database-for-postgresql---hyperscale-security-baseline-for-azure-security-benchmark"></a>Azure Database for PostgreSQL – směrný plán zabezpečení s ochranou rozsahu pro Azure Security test

Základní plán zabezpečení Azure pro Azure Database for PostgreSQL – škálovatelné řešení obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné**materiály: Brána firewall serveru Azure Database for PostgreSQL zabraňuje všem přístupům k uzlu koordinátora Citus (), dokud neurčíte, které počítače mají oprávnění. Brána firewall uděluje přístup k serveru na základě zdrojové IP adresy jednotlivých požadavků. Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

* [Jak nakonfigurovat pravidla firewallu v Azure Database for PostgreSQL – Citus (škálování)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-firewall-rules)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Doprovodné**materiály: nepoužitelné; Azure Database for PostgreSQL – Citus () nelze přidružit k virtuální síti, podsíti nebo skupině zabezpečení sítě.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné**materiály: nepoužitelné; koncové body, které používá Azure Database for PostgreSQL-Citus (škálování), jsou spravovány společností Microsoft. Zodpovídáte za všechny další ovládací prvky, které chcete nasadit do místních systémů.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné**materiály: nepoužitelné; Azure Database for PostgreSQL – Citus () nelze přidružit k virtuální síti, podsíti nebo skupině zabezpečení sítě.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné**materiály: nepoužitelné; koncové body, které používá Azure Database for PostgreSQL-Citus (škálování), jsou spravovány společností Microsoft. Zodpovídáte za všechny další ovládací prvky, které chcete nasadit do místních systémů.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné**materiály: nepoužitelné; Azure Database for PostgreSQL – Citus () nelze přidružit k virtuální síti, podsíti nebo skupině zabezpečení sítě.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě a síťové prostředky přidružené k vašim Azure Database for PostgreSQL instancím Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich Azure Database for PostgreSQL instancí.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Ukázky Azure Policy pro sítě](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Vytvoření Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné**materiály: nepoužitelné; Azure Database for PostgreSQL – Citus () nelze přidružit k virtuální síti, podsíti nebo skupině zabezpečení sítě.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Doprovodné**materiály: nepoužitelné; Azure Database for PostgreSQL – Citus () nelze přidružit k virtuální síti, podsíti nebo skupině zabezpečení sítě.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Společnost Microsoft udržuje zdroj času používaný pro prostředky Azure, jako například Azure Database for PostgreSQL-Citus) pro časová razítka v protokolech.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné**materiály: pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro vaše prostředky Azure.

Také ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení vygenerovaných pomocí Citus (škálování). V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty úložiště pro dlouhodobé a archivační úložiště. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo incidentu zabezpečení a řízení událostí zabezpečení (SIEM) třetí strany.

* [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Metriky v měřítku (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-monitoring)

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné**materiály: Citus (škálování) poskytuje metriky pro každý uzel ve skupině serverů. Metriky poskytují přehled o chování podpůrných prostředků. Každá metrika je vygenerována s jednou minutovou frekvencí a má až 30 dnů od historie.

Pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu úložiště Azure pro archivaci. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro vaše prostředky Azure.

Také ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení vygenerovaných pomocí Citus (škálování). V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty úložiště pro dlouhodobé a archivační úložiště. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo incidentu zabezpečení a řízení událostí zabezpečení (SIEM) třetí strany.

* [Metriky v měřítku (Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-monitoring)

* [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné**materiály: v rámci Azure monitor pro pracovní prostor Log Analytics, který se používá k ukládání protokolů Citus), nastavte dobu uchování podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

* [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Ukládání protokolů prostředků v Azure Storagem účtu](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů z instancí Citus (neobvyklé) pro chování. Pomocí Log Analytics Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Další informace o Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Jak provádět vlastní dotazy v Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné**materiály: můžete povolit nastavení diagnostiky pro Citus a odesílat protokoly do pracovního prostoru Log Analytics. Můžete nakonfigurovat a přijmout výstrahu na základě metrik monitorování pro vaše služby Azure. Pomocí Log Analytics Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

Připojte pracovní prostor Log Analytics do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení.

* [Metriky v měřítku (Citus)](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-alert-on-metric)

* [Jak nakonfigurovat nastavení diagnostiky pro protokol aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné**materiály: nepoužitelné; Azure Database for PostgreSQL – Citus (škálovatelné) nezpracovává ani nevytváří protokoly související s malwarem.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné**materiály: nepoužitelné; Azure Database for PostgreSQL nezpracovává ani nevytváří protokoly související se službou DNS.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a řízení přístupu](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné**materiály: udržování inventáře uživatelských účtů, které mají přístup pro správu k rovině řízení (např. Azure Portal) instancí Citus (osobní škálování). Kromě toho udržujte inventarizaci účtů pro správu, které mají přístup k rovině dat (v samotné databázi) vašich instancí Citus.

Citus () nepodporuje integrované řízení přístupu na základě rolí, ale můžete vytvořit vlastní role na základě konkrétních operací poskytovatele prostředků.

Kromě toho modul PostgreSQL používá role k řízení přístupu k databázovým objektům a nově vytvořená skupina serverů Citus () obsahuje několik rolí předem definovaných. Chcete-li upravit uživatelská oprávnění, použijte standardní příkazy PostgreSQL pomocí nástroje, jako je například PgAdmin nebo psql.

* [Porozumění vlastním rolím pro předplatné Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)

* [Principy Azure Database for PostgreSQL operací poskytovatele prostředků](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql)

* [Pochopení správy přístupu pro Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-security#access-management])

* [Vytváření uživatelů v Azure Database for PostgreSQL – Citus (škálování)](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

* [Jak se připojit k PostgreSQL – škálovatelné (Citus) s využitím psql](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné**materiály: Azure AD nemá koncept výchozích hesel. Další prostředky Azure, které vyžadují heslo, vynutí vytvoření hesla s požadavky na složitost a minimální délkou hesla, která se liší v závislosti na službě. Zodpovídáte za aplikace třetích stran a služby Marketplace, které mohou používat výchozí hesla.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné**materiály: vytvořte standardní operační postupy kolem použití vyhrazených účtů pro správu, které se používají pro přístup k instancím Citus (vlastní škálování). Účty správců pro správu prostředků Azure jsou vázané na Azure Active Directory, existují také účty správců místního serveru, které existují ve skupině serverů Citus () pro správu oprávnění přístupu k databázi. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu v rámci Azure Active Directory.

* [Pochopení Azure Security Center identity a přístupu](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Vytváření uživatelů v Azure Database for PostgreSQL – Citus (škálování)](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné**materiály: přihlášení do Azure Database for PostgreSQL-Citus (škálování) se podporuje jak pomocí uživatelského jména nebo hesla nakonfigurovaného přímo v databázi, tak i pomocí identity Azure Active Directory (AD) a s využitím tokenu Azure AD pro připojení. Při použití tokenu Azure AD se podporují různé metody, jako je například uživatel Azure AD, skupina Azure AD nebo aplikace Azure AD připojující se k databázi.

* [Použití Azure Active Directory k ověřování pomocí Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné**materiály: povolte Azure Active Directory Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu. Při použití tokenů Azure AD pro přihlášení k vaší databázi vám to umožní vyžadovat vícefaktorové ověřování pro přihlášení k databázi.

* [Jak povolit vícefaktorové ověřování v Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Použití Azure Active Directory k ověřování pomocí Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

* [Jak monitorovat identitu a přístup v rámci Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s nakonfigurovaným Multi-Factor Authentication (MFA), které jsou nakonfigurovány pro přihlášení a konfiguraci prostředků Azure.

* [Další informace o pracovních stanicích s privilegovaným přístupem](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Jak povolit vícefaktorové ověřování v Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="37-alert-on-account-login-behavior-deviation"></a>3,7: upozornění na odchylku chování přihlášení k účtu

**Pokyny**: pomocí Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) můžete generovat protokoly a výstrahy, když dojde k podezřelé nebo nebezpečné aktivitě v prostředí.

Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy týkající se rizikového chování uživatelů.

* [Postup nasazení Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Vysvětlení zjišťování rizik Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu umožněte portálu a Azure Resource Manager přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

* [Postup konfigurace pojmenovaných umístění v Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné**materiály: jako centrální ověřování a systém autorizací použijte Azure Active Directory (AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Přihlašovací údaje Azure AD se můžou používat taky pro správu na úrovni roviny správy (například Azure Portal) k řízení účtů správce PostgreSQL.

* [Použití Azure Active Directory k ověřování pomocí Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné**materiály: Projděte si a slučte přístup pro uživatele, kteří mají přístup k místní databázi, a také prostřednictvím Azure Active Directory.

Přečtěte si protokoly Azure Active Directory (AD), které vám pomůžou zjistit zastaralé účty, které můžou obsahovat role pro správu s Citus. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím, které se dají použít pro přístup k Citus a přiřazení rolí. Přístup uživatelů by se měl pravidelně kontrolovat, například každých 90 dní, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

* [Kontrola uživatelů PostgreSQL a přiřazených rolí](https://www.postgresql.org/docs/current/database-roles.html)

* [Pochopení sestav Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Jak používat recenze Azure identity Access](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné**materiály: v rámci Azure Active Directory (AD) máte přístup ke zdrojům přihlašovacích aktivit Azure AD, auditu a rizikovým protokolům událostí, které vám umožní integraci s jakýmkoli nástrojem Siem/monitoring.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro Azure Active Directory uživatelských účtů a odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

* [Jak integrovat protokoly aktivit Azure do Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné**materiály: použití funkcí ochrany identit a detekce rizik Azure Active Directory ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce na úrovni Azure Active Directory (AD). Můžete povolit automatizované odezvy prostřednictvím služby Azure Sentinel, abyste mohli implementovat reakce na zabezpečení vaší organizace.

K dalšímu zkoumání můžete také ingestovat protokoly do Azure Sentinel.

* [Přehled Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

* [Jak zobrazit rizikové přihlašování Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [Jak připojit Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné**materiály: aktuálně není k dispozici; Customer Lockbox ještě není podporován pro Citus (Scale).

* [Seznam podporovaných služeb Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: aktuálně není k dispozici.

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné**materiály: použití značek k usnadnění sledování instancí Citus () nebo souvisejících prostředků, které ukládají nebo zpracovávají citlivé informace.

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. K izolaci a omezení síťového přístupu k instancím Azure Database for PostgreSQL použijte kombinaci rolí pro správu a pravidel brány firewall.

* [Vytvoření dalších předplatných Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Postup vytvoření Skupiny pro správu](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Principy pravidel brány firewall v Azure Database for PostgreSQL – Citus (škálování)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-firewall-rules)

* [Porozumění rolím v Citus (Scale)](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné**materiály: Společnost Microsoft spravuje základní infrastrukturu pro Azure Database for PostgreSQL Citus a implementuje striktní ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

* [Pochopení ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: klientská aplikace připojení k uzlu koordinátora Citus (Transport Layer Security) vyžaduje zabezpečení TLS 1,2. Vynucování připojení TLS mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "prostředník-in-the-middle" tím, že šifruje datový proud mezi serverem a vaší aplikací.

Pro všechny Azure Database for PostgreSQL servery zřízené prostřednictvím Azure Portal je vynucování připojení TLS ve výchozím nastavení povolené.

V některých případech aplikace třetích stran vyžadují k zabezpečenému připojení soubor místního certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (. cer).

* [Postup konfigurace TLS v Azure Database for PostgreSQL – Citus (škálování)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-ssl-connection-security)

* [Aplikace, které vyžadují ověření certifikátu pro připojení TLS](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-ssl-connection-security)

**Monitorování Azure Security Center**: Ano

**Odpovědnost**: sdílená

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné**materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro škálování (Citus).

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Pochopení ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Doprovodné**materiály: použití řízení přístupu na základě role (RBAC) v Azure k řízení přístupu k rovině ovládacího prvku Citus (škálování na úrovni) (např. Azure Portal). RBAC nemá vliv na uživatelská oprávnění v rámci databáze.

Pokud chcete upravit uživatelská oprávnění na úrovni databáze, použijte standardní příkazy PostgreSQL pomocí nástroje, jako je PgAdmin nebo psql.

* [Jak nakonfigurovat RBAC v Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

* [Jak nakonfigurovat přístup uživatelů pomocí SQL pro Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-hyperscale-create-users)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft spravuje základní infrastrukturu pro Citus a implementuje striktní ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

* [Pochopení ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné**materiály: alespoň jednou denně Azure Database for PostgreSQL Citus () provádí zálohování snímků datových souborů a transakčního protokolu databáze. Zálohy umožňují obnovit server do libovolného bodu v čase v rámci doby uchování. (Doba uchování je aktuálně 35 dní pro všechny clustery.) Všechny zálohy se šifrují pomocí šifrování AES 256-bit. Nabídka PostgreSQL (Citus) používá pro šifrování klíče spravované Microsoftem.

* [Vysvětlení šifrování pro zálohy Azure PostgreSQL-(Citus)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají do produkčních instancí Citus a dalších důležitých nebo souvisejících prostředků.

* [Vytvoření upozornění pro události protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](/azure/security/benchmarks/security-control-vulnerability-management)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné**materiály: aktuálně není k dispozici; Azure Security Center ještě nepodporuje posouzení ohrožení zabezpečení pro Azure Database for PostgreSQL-Citus (škálování).

* [Pokrytí funkcí pro služby Azure PaaS v Azure Security Center](https://docs.microsoft.com/azure/security-center/features-paas)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: aktuálně není k dispozici.

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné**materiály: pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Pochopení ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizovaného řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné**materiály: pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Pochopení ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné**materiály: pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

* [Pochopení ochrany zákaznických dat v Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Pokyny**: Microsoft provádí správu ohrožení zabezpečení u základních systémů, které podporují Azure Database for PostgreSQL – Citus (škálování). V případě ohrožení zabezpečení souvisejícího s konfigurací vašeho databázového clusteru a souvisejících prostředků použijte program pro běžné hodnocení rizik (například běžný systém vyhodnocování chyb zabezpečení) nebo výchozí hodnocení rizika poskytovaná skenovacím nástrojem třetí strany.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se můžete dotazovat a zjišťovat všechny prostředky (včetně instancí Citus) v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

* [Jak vytvářet dotazy pomocí Azure graphu](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Jak zobrazit vaše předplatná Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Pochopení Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné**materiály: použití značek na instance Citus () a další související prostředky, které dávají metadata k logickému uspořádání do taxonomie.

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné**materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k uspořádání a sledování instancí Citus () a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

* [Vytvoření dalších předplatných Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Postup vytvoření Skupiny pro správu](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Vytváření a používání značek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné**materiály: pomocí Azure Policy můžete klást omezení typů prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

Kromě toho můžete pomocí grafu prostředků Azure dotazovat nebo zjišťovat prostředky v rámci předplatných.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak vytvářet dotazy pomocí Azure graphu](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné**materiály: pomocí Azure Policy můžete klást omezení typů prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

Kromě toho můžete pomocí grafu prostředků Azure dotazovat nebo zjišťovat prostředky v rámci předplatných.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak vytvářet dotazy pomocí Azure graphu](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky a Azure jako celek.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné**materiály: pomocí Azure Policy můžete klást omezení typů prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad:
- Žádné povolené typy prostředků
- Povolené typy prostředků

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změnám prostředků v rámci prostředí s vysokým zabezpečením, jako jsou například instance Citus () obsahující citlivé údaje.

* [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro instance Citus () s Azure Policy. Pomocí Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich Azure Database for PostgreSQL instancí.

Azure Resource Manager taky umožňuje exportovat šablonu do JavaScript Object Notation (JSON), která by se měla zkontrolovat, aby se zajistilo, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

* [Jak zobrazit dostupné aliasy Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Export jednoho a více prostředků do šablony v Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Pokyny**: použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure. Kromě toho můžete použít šablony Azure Resource Manager k údržbě konfigurace zabezpečení vašich prostředků Azure, které vaše organizace vyžaduje.

* [Pochopení Azure Policych efektů](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [Vytváření a Správa zásad pro vymáhání dodržování předpisů](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Přehled šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: Pokud používáte vlastní definice zásad Azure pro instance Citus a související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

* [Jak v Azure DevOps ukládat kód](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentace k Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: použijte zásady Azure [Deny] a [nasazení, pokud neexistuje], abyste vynutili zabezpečená nastavení napříč prostředky Azure. Kromě toho můžete použít šablony Azure Resource Manager k údržbě konfigurace zabezpečení vašich prostředků Azure, které vaše organizace vyžaduje.

* [Pochopení Azure Policych efektů](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [Vytváření a Správa zásad pro vymáhání dodržování předpisů](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Přehled šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. DBforPostgreSQL můžete vytvořit vlastní zásady pro upozornění, audit a prosazování konfigurace systému. Použijte zásady Azure [audit], [Deny] a [nasazení, pokud neexistuje] k automatickému vymáhání konfigurací pro Azure Database for PostgreSQL instance a související prostředky.

* [Konfigurace a Správa Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné**materiály: nepoužitelné; Heslo správce databáze můžete bezpečně spravovat pomocí Azure Key Vault. Přiřaďte patřičná oprávnění vymezená pro tento Key Vault tajný klíč omezující přístup k vašemu heslu databáze. Vyhněte se ukládání připojovacích řetězců nebo databázových tajných kódů v úložištích kódu.

* [Jak se připojit k databázi PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal#connect-to-the-database-using-psql)

* [Vytvoření Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [Jak zajistit Key Vault ověřování pomocí spravované identity](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: Azure Database for PostgreSQL Server podporuje ověřování Azure Active Directory pro přístup k databázím. Při vytváření Azure Database for PostgreSQLho serveru poskytnete přihlašovací údaje pro uživatele správce. Tento správce se dá použít k vytvoření dalších uživatelů databáze.

V případě Azure Virtual Machines nebo webových aplikací běžících na Azure App Service se používá pro přístup k serveru Azure Database for PostgreSQL použijte Identita spravované služby ve spojení s Azure Key Vault ukládat a načítat přihlašovací údaje pro Azure Database for PostgreSQL Server. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou v Azure Active Directory (AD). Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

* [Postup konfigurace spravovaných identit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

* [Integrace se spravovanými identitami Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Odpovědnost**: aktuálně není k dispozici.

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

* [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service), ale neběží na zákaznickém obsahu.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure – například Citus (škálovatelné), ale neběží na zákaznickém obsahu.

Předem prohledejte veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL atd. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné**materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure – například Citus (škálovatelné), ale neběží na zákaznickém obsahu.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné**materiály: Azure Database for PostgreSQL – Citus (škálování) automaticky vytváří zálohy každého uzlu a ukládá je do místně redundantního úložiště. Zálohy se dají použít k obnovení clusteru Citus (Your Scale) do určeného času.

* [Postup zálohování a obnovení v Azure Database for PostgreSQL – Citus (škálování)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné**materiály: alespoň jednou denně Azure Database for PostgreSQL pořizování snímků datových souborů a protokolu transakcí databáze. Zálohy umožňují obnovit server do libovolného bodu v čase v rámci doby uchování. Doba uchovávání dat je v současnosti 35 dní pro všechny clustery. Všechny zálohy se šifrují pomocí šifrování AES 256-bit.

V oblastech Azure, které podporují zóny dostupnosti, se záložní snímky ukládají ve třech zónách dostupnosti. Pokud je aspoň jedna zóna dostupnosti online, cluster Citus () je obnovitelné.

* [Postup zálohování a obnovení v Azure Database for PostgreSQL – Citus (škálování)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny**: v Azure Database for PostgreSQL obnovení clusteru Citus () vytvoří nový cluster z původních záloh uzlů. Cluster můžete obnovit do libovolného bodu v čase během posledních 35 dnů. Proces obnovení vytvoří nový cluster ve stejné oblasti Azure, předplatném a skupině prostředků jako původní. Nová konfigurace clusteru se shoduje s původní konfigurací clusteru: stejný počet uzlů, počet virtuální jádra, velikost úložiště a role uživatele.

Nastavení brány firewall a parametry serveru PostgreSQL nejsou zachovány z původní skupiny serverů; resetují se na výchozí hodnoty. Brána firewall zabrání všem připojením. Tato nastavení budete muset po obnovení upravit ručně.

* [Postup zálohování a obnovení v Azure Database for PostgreSQL – Citus (škálování)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Pokyny**: odstraněné clustery Citus) se nedají obnovit. Pokud cluster odstraníte, odstraní se všechny uzly patřící do clusteru a nebude možné je obnovit. K ochraně prostředků clusteru po nasazení po náhodném odstranění nebo neočekávaných změnách můžou správci využít zámky pro správu.

* [Postup zálohování a obnovení v Azure Database for PostgreSQL – Citus (škálování)](https://docs.microsoft.com/azure/postgresql/concepts-hyperscale-backup)

**Monitorování Azure Security Center**: aktuálně není k dispozici.

**Zodpovědnost**: zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: Vytvoření Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů z detekce až po přezkoumání po jednotlivých událostech.

* [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné**materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné**materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

* [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné**materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

* [Jak nastavit kontakt zabezpečení Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitorování Azure Security Center**: Ano

**Zodpovědnost**: zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné**materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat ověřovací data výstrah.

* [Postup konfigurace průběžného exportu](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Jak streamovat výstrahy do Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné**materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.

* [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Pokyny**: * [použijte pravidla zapojení Microsoftu a ujistěte se, že testy průniku nejsou v rozporu s zásadami Microsoftu](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) .

* [V této části najdete další informace o strategii Microsoftu a provádění testování v rámci červeného seskupování a testování průniku na cloudové infrastruktuře, služby a aplikace spravované Microsoftem.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: sdílená

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](/azure/security/benchmarks/overview)
- Další informace o [plánech zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
