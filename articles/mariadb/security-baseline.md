---
title: Základní hodnoty zabezpečení Azure pro Azure Database for MariaDB
description: Základní Azure Database for MariaDB zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/29/2021
ms.service: mariadb
ms.topic: conceptual
ms.custom: subject-security-benchmark
ms.openlocfilehash: fd169738c138d56932dbab87047b89e373b9c6ec
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227666"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Základní hodnoty zabezpečení Azure pro Azure Database for MariaDB

Tato základní hodnota zabezpečení platí pro Azure Database for MariaDB pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Azure Database for MariaDB. **Ovládací prvky** , které se nevztahují na Azure Database for MariaDB nebo pro které je odpovědnost od Microsoftu, jsou vyloučené.

Pokud chcete zjistit, jak Azure Database for MariaDB kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Azure Database for MariaDB Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Pokyny**: konfigurace privátního odkazu pro Azure Database for MariaDB se soukromými koncovými body. Private Link umožňuje připojení k různým službám PaaS v Azure přes privátní koncový bod. Azure Private Link v podstatě přináší služby Azure do vaší privátní virtuální sítě. Přenosy mezi vaší virtuální sítí a instancí MariaDB cestují páteřní síť Microsoftu.

Alternativně můžete použít koncové body služby Virtual Network k ochraně a omezení síťového přístupu k vašim Azure Database for MariaDB implementaci. Pravidla virtuální sítě jsou jednou funkcí zabezpečení brány firewall, která určuje, jestli Azure Database for MariaDB přijímá komunikaci, která se odesílají z konkrétních podsítí ve virtuálních sítích.

Azure Database for MariaDB můžete také zabezpečit pomocí pravidel brány firewall. Brána firewall serveru znemožní přístup k vašemu databázovému serveru, dokud neurčíte, které počítače mají oprávnění. Bránu firewall nakonfigurujete tak, že vytvoříte pravidla brány firewall určující rozsahy přípustných IP adres. Pravidla brány firewall můžete vytvořit na úrovni serveru.

- [Postup konfigurace privátního odkazu pro Azure Database for MariaDB](howto-configure-privatelink-portal.md)

- [Jak vytvořit a spravovat koncové body služby virtuální sítě a pravidla virtuální sítě na serveru Azure Database for MariaDB](howto-manage-vnet-portal.md)

- [Postup konfigurace Azure Database for MariaDB pravidel brány firewall](concepts-firewall-rules.md)

**Odpovědnost:** Zákazník

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových rozhraní

**Pokyny**: když je server Azure Database for MariaDB zabezpečený k privátnímu koncovému bodu, můžete virtuální počítače nasadit ve stejné virtuální síti. Ke snížení rizika exfiltrace dat můžete použít skupinu zabezpečení sítě (NSG). Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Postup konfigurace privátního odkazu pro Azure Database for MariaDB](howto-configure-privatelink-portal.md)

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: použití rozšířené ochrany před internetovými útoky pro Azure Database for MariaDB. Rozšířená ochrana před internetovými útoky detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Povolte DDoS Protection Standard ve virtuálních sítích přidružených k vašim Azure Database for MariaDB instancím, abyste se mohli chránit před útoky DDoS. K odepření komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrovanou analýzu hrozeb.

- [Jak nakonfigurovat rozšířenou ochranu před internetovými útoky pro Azure Database for MariaDB](howto-database-threat-protection-portal.md)

- [Jak nakonfigurovat DDoS Protection](/azure/virtual-network/manage-ddos-protection)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Pokyny**: když je server Azure Database for MariaDB zabezpečený k privátnímu koncovému bodu, můžete virtuální počítače nasadit ve stejné virtuální síti. Pak můžete nakonfigurovat skupinu zabezpečení sítě (NSG), aby se snížilo riziko exfiltrace dat. Povolte protokoly toku NSG a odešlete protokoly do účtu úložiště pro audit provozu. Protokoly toku NSG můžete také odesílat do pracovního prostoru Log Analytics a používat Analýza provozu k poskytování přehledů o toku přenosů ve vašem cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu a identifikovat aktivní body, identifikovat bezpečnostní hrozby, pochopit vzory toků provozu a označovat nesprávné konfigurace sítě.

- [Jak povolit protokoly toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: použití rozšířené ochrany před internetovými útoky pro Azure Database for MariaDB. Rozšířená ochrana před internetovými útoky detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

- [Jak nakonfigurovat rozšířenou ochranu před internetovými útoky pro Azure Database for MariaDB](howto-database-threat-protection-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků, které potřebují přístup k instancím Azure Database for MariaDB, použijte značky služby virtuální sítě a definujte řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. SQL. WestUs) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo zamítnout přenos pro odpovídající službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Azure Database for MariaDB používá značku služby Microsoft. SQL.

- [Další informace o použití značek služeb](../virtual-network/service-tags-overview.md)

- [Pochopení použití značek služby pro Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro nastavení sítě a síťové prostředky přidružené k vašim Azure Database for MariaDB instancím Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. DBforMariaDB a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich Azure Database for MariaDBch instancí. Můžete také využít integrované definice zásad týkající se sítě nebo vašich Azure Database for MariaDB instancí, například:

- Měla by být povolená DDoS Protection Standard.

- Privátní koncový bod by měl být povolený pro MariaDB servery.

- Server MariaDB by měl používat koncový bod služby virtuální sítě.

Referenční dokumentace:

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ukázky Azure Policy pro sítě](../governance/policy/samples/built-in-policies.md)

- [Vytvoření Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro prostředky, které se týkají zabezpečení sítě a toku provozu pro instance MariaDB k poskytování metadat a logické organizace.

Použijte některou z předdefinovaných Azure Policy definic souvisejících s označováním, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky.

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledávat nebo provádět akce s prostředky na základě jejich značek.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s vašimi Azure Database for MariaDB instancemi. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](/azure/azure-monitor/platform/activity-log-view)

- [Vytváření výstrah v Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: povolení nastavení diagnostiky a protokolů serveru a příjem protokolů pro agregaci dat zabezpečení generovaných vašimi Azure Database for MariaDB instancemi. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé nebo archivní úložiště. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany.

- [Konfigurace a přístup k protokolům serveru pro Azure Database for MariaDB](concepts-server-logs.md)

- [Postup konfigurace a přístup k protokolům auditu pro Azure Database for MariaDB](howto-configure-audit-logs-portal.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení nastavení diagnostiky na vašich instancích Azure Database for MariaDB pro přístup k protokolům auditu, zabezpečení a diagnostiky. Ujistěte se, že jste konkrétně povolili protokol auditu MariaDB. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky. Můžete také povolit nastavení diagnostiky protokolů aktivit Azure a odesílat protokoly do stejného Log Analytics pracovního prostoru nebo účtu úložiště.

- [Konfigurace a přístup k protokolům serveru pro Azure Database for MariaDB](concepts-server-logs.md)

- [Postup konfigurace a přístup k protokolům auditu pro Azure Database for MariaDB](howto-configure-audit-logs-portal.md)

- [Jak nakonfigurovat nastavení diagnostiky pro protokol aktivit Azure](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v rámci Azure monitor pro pracovní prostor Log Analytics, který se používá k ukládání protokolů Azure Database for MariaDB, nastavte dobu uchování podle předpisů pro dodržování předpisů vaší organizace. Používejte účty Azure Storage pro dlouhodobé a archivační úložiště.

- [Postup nastavení parametrů uchovávání protokolů pro Log Analytics pracovní prostory](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Ukládání protokolů prostředků v Azure Storagem účtu](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů z instancí MariaDB pro chování neobvyklé. Pomocí Log Analytics pracovního prostoru Azure Monitor můžete prohlížet protokoly a provádět dotazy na data protokolu. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo jiného dodavatele SIEM.

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Další informace o pracovním prostoru Log Analytics](/azure/azure-monitor/log-query/get-started-portal)

- [Jak provádět vlastní dotazy v Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: povolení rozšířené ochrany před internetovými útoky pro MariaDB. Rozšířená ochrana před internetovými útoky pro Azure Database for MariaDB detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Kromě toho můžete povolit protokoly serveru a nastavení diagnostiky pro MariaDB a odesílat protokoly do pracovního prostoru Log Analytics. Připojte pracovní prostor Log Analytics do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení.

- [Jak povolit rozšířenou ochranu před internetovými útoky pro MariaDB](howto-database-threat-protection-portal.md)

- [Konfigurace a přístup k protokolům serveru pro MariDB](concepts-server-logs.md)

- [Konfigurace a přístup k protokolům auditu pro MariaDB](howto-configure-audit-logs-portal.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: udržování inventáře uživatelských účtů, které mají přístup správce k rovině správy (Azure Portal/Azure Resource Manager) vašich instancí MariaDB. Kromě toho udržujte inventarizaci účtů pro správu, které mají přístup k rovině dat instancí MariaDB. (Při vytváření serveru MariaDB zadáte přihlašovací údaje pro uživatele správce. Tento správce se dá použít k vytvoření dalších uživatelů MariaDB.)

- [Pochopení správy přístupu pro MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-security#access-management)

- [Principy předdefinovaných rolí Azure pro předplatná Azure](../role-based-access-control/built-in-roles.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: Azure Active Directory (Azure AD) nemá koncept výchozích hesel.

Při vytváření samotného prostředku MariaDB vynutí Azure vytvořit administrativního uživatele se silným heslem. Po vytvoření instance MariaDB můžete použít první účet správce serveru, který jste vytvořili, a vytvořit tak další uživatele a udělit jim přístup pro správu. Při vytváření těchto účtů se ujistěte, že pro každý účet nakonfigurujete jiné silné heslo.

- [Vytvoření dalších účtů pro MariaDB](howto-create-users.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem použití vyhrazených účtů pro správu, které mají přístup k instancím MariaDB. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

- [Pochopení Azure Security Center identity a přístupu](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: použijte Azure Active Directory jednotné přihlašování (SSO).

**Doprovodné** materiály: přístup k rovině dat na MariaDB se řídí identitami uloženými v databázi nástroje a nepodporuje jednotné přihlašování. Přístup k řídicí rovině pro MariaDB je k dispozici prostřednictvím REST API a podporuje jednotné přihlašování. Pro ověření nastavte hlavičku autorizace pro vaše požadavky na JSON Web Token, které získáte z Azure Active Directory (Azure AD).

- [Pochopení Azure Database for MariaDB REST API](/rest/api/mariadb/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte vícefaktorové ověřování Azure Active Directory (Azure AD) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Používejte zabezpečené pracovní stanice spravované Azure pro úlohy správy

**Doprovodné** materiály: Používejte privilegovaným přístupem (Privileged Access Workstations) s nakonfigurovaným vícefaktorového ověřováním, které je nakonfigurované pro přihlášení k prostředkům Azure a jejich konfiguraci. 

- [Další informace o pracovních stanicích s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Postup povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: povolení rozšířené ochrany před internetovými útoky pro MariaDB pro generování výstrah pro podezřelou aktivitu.

Kromě toho můžete použít Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí zjišťování rizik Azure AD můžete zobrazit výstrahy a sestavy týkající se rizikového chování uživatelů.

- [Nastavení rozšířené ochrany před internetovými útoky pro MariaDB](howto-database-threat-protection-portal.md)

- [Postup nasazení Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Vysvětlení zjišťování rizik Azure AD](/azure/active-directory/reports-monitoring/concept-risk-events)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí, abyste omezili přístup k prostředkům Azure, jako je MariaDB.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Ověřování Azure AD se nedá použít pro přímý přístup k rovině dat MariaDB, ale k řízení účtů správce MariaDB se můžou použít přihlašovací údaje Azure AD pro správu na úrovni roviny správy (např. Azure Portal).

- [Postup aktualizace hesla správce pro MariaDB](https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Přečtěte si protokoly Azure Active Directory (Azure AD), které vám pomůžou zjistit zastaralé účty, které můžou obsahovat i s MariaDB rolemi pro správu. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím, které se dají použít pro přístup k MariaDB a přiřazení rolí. Přístup uživatelů by se měl pravidelně kontrolovat, například každých 90 dní, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](/azure/active-directory/reports-monitoring/)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: Povolte nastavení diagnostiky pro MariaDB a Azure Active Directory (Azure AD) a odešlete všechny protokoly do pracovního prostoru Log Analytics. Nakonfigurujte požadovaná upozornění (například neúspěšné pokusy o ověření) v rámci Log Analytics pracovního prostoru.

- [Konfigurace a přístup k protokolům serveru pro MariaDB](concepts-server-logs.md)

- [Konfigurace a přístup k protokolům auditu pro MariaDB](howto-configure-audit-logs-portal.md)

- [Integrace protokolů aktivit Azure do služby Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: výstraha při odchylce chování při přihlašování k účtu

**Doprovodné** materiály: povolení rozšířené ochrany před internetovými útoky pro MariaDB. Rozšířená ochrana před internetovými útoky pro Azure Database for MariaDB detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Pomocí funkcí Azure Active Directory (Azure AD) pro ochranu identit a detekce rizik můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce. Můžete povolit automatizované odezvy prostřednictvím služby Azure Sentinel, abyste mohli implementovat reakce na zabezpečení vaší organizace.

- [Jak povolit rozšířenou ochranu před internetovými útoky pro MariaDB](howto-database-threat-protection-portal.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Zobrazení rizikových přihlášení Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: pomocí značek pomáhají při sledování Azure Database for MariaDBch instancí nebo souvisejících prostředků, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a produkci. Pomocí kombinace privátních odkazů, koncových bodů služby a/nebo MariaDB pravidel brány firewall můžete izolovat a omezit síťový přístup k instancím MariaDB.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Postup konfigurace privátního odkazu pro Azure Database for MariaDB](concepts-data-access-security-private-link.md)

- [Postup konfigurace koncových bodů služby pro Azure Database for MariaDB](howto-manage-vnet-portal.md)

- [Jak nakonfigurovat pravidla firewallu pro Azure Database for MariaDB](concepts-firewall-rules.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: při použití virtuálních počítačů Azure k přístupu k instancím MariaDB můžete využít privátní propojení, konfigurace sítě MariaDB, skupiny zabezpečení sítě a značky služeb, aby se zmírnila možnost datových exfiltrace.

Microsoft spravuje základní infrastrukturu pro MariaDB a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Jak zmírnit exfiltrace dat pro Azure Database for MariaDB](concepts-data-access-security-private-link.md)

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Azure Database for MariaDB podporuje připojení serveru Azure Database for MariaDB k klientským aplikacím pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL). Vynucování připojení TLS mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "muž" v prostředním případě šifrováním datového proudu mezi serverem a vaší aplikací. V Azure Portal zajistěte, aby bylo pro všechny vaše instance MariaDB povoleno **vynutilo připojení SSL** .

- [Konfigurace šifrování při přenosu pro MariaDB](howto-configure-ssl.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Database for MariaDB. Implementujte řešení třetích stran, pokud je to potřeba pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte řízení přístupu na základě role

**Pokyny**: pomocí řízení přístupu na základě role Azure (Azure RBAC) můžete spravovat přístup k databázi Azure pro rovinu správy MariaDB (Azure Portal/Azure Resource Manager). Pro přístup k rovině dat (v samotné databázi) použijte dotazy SQL k vytvoření uživatelů a konfiguraci oprávnění uživatele.

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Jak nakonfigurovat přístup uživatelů pomocí SQL pro MariaDB](howto-create-users.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Azure Database for MariaDB a dalších důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](/azure/azure-monitor/platform/alerts-activity-log)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: aktuálně není k dispozici; Azure Security Center zatím nepodporuje posouzení ohrožení zabezpečení pro Azure Database for MariaDB Server.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat a zjišťovat všechny prostředky (včetně Azure Database for MariaDB serveru). Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a že máte v rámci předplatných také výčet všech předplatných Azure i prostředků.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro Azure Database for MariaDB Server a další související prostředky, které dávají metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Azure Database for MariaDB serveru a souvisejících prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](/azure/billing/billing-create-subscription)

- [Postup vytvoření Skupiny pro správu](/azure/governance/management-groups/create)

- [Vytváření a používání značek](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky a Azure jako celek.

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

K dotazování a zjišťování prostředků v rámci předplatných navíc použijte graf prostředků Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků

- Povolené typy prostředků

Další informace najdete v následujících referenčních materiálech:

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změnám prostředků v prostředí s vysokým zabezpečením, jako je Azure Database for MariaDB Server obsahující citlivé údaje.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Azure Database for MariaDB instance pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. DBforMariaDB můžete vytvořit vlastní zásady, které budou auditovat nebo vymáhat konfiguraci sítě vašich Azure Database for MariaDB serverů. Můžete také využít integrované definice zásad, které souvisejí s vašimi servery Azure Database for MariaDB, například:

- Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.

Další informace najdete v následujících referenčních materiálech:

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

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

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy pro servery Azure Database for MariaDB a související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [Dokumentace k Azure Repos](/azure/devops/repos/)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. DBforMariaDB můžete vytvořit vlastní zásady pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. DBforMariaDB můžete vytvořit vlastní zásady pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro vaše Azure Database for MariaDB instance a související prostředky použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje].

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: u Azure Virtual Machines nebo webových aplikací běžících na Azure App Service se používá pro přístup k serverům Azure Database for MariaDB použijte identita spravované služby ve spojení s Azure Key Vault ke zjednodušení a zabezpečení správy tajných kódů serveru Azure Database for MariaDB. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Vytvoření Key Vault](../key-vault/general/quick-create-portal.md)

- [Ověření Key Vault](../key-vault/general/authentication.md)

- [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Pokyny**: Azure Database for MariaDB Server v současné době nepodporuje ověřování Azure Active Directory (Azure AD) pro přístup k databázím. Při vytváření Azure Database for MariaDBho serveru poskytnete přihlašovací údaje pro uživatele správce. Tento správce se dá použít k vytvoření dalších uživatelů MariaDB.

V případě Azure Virtual Machines nebo webových aplikací běžících na Azure App Service se používá pro přístup k serveru Azure Database for MariaDB použijte spravované identity ve spojení s Azure Key Vault k ukládání a načítání přihlašovacích údajů pro Azure Database for MariaDB Server. Ujistěte se, že je povolené Key Vault obnovitelné odstranění.

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

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

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure Database for MariaDB Server), ale neběží na zákaznickém obsahu.

Předem prohledejte veškerý obsah, který se nahrává do nevýpočetních prostředků Azure, například App Service, Data Lake Storage, Blob Storage, Azure Database for MariaDB Server atd. Společnost Microsoft nemá přístup k vašim datům v těchto instancích.

**Odpovědnost:** Sdílené

**Monitorování Azure Security Center**: žádné

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: Azure Database for MariaDB přebírá úplné a rozdílové zálohy a zálohování protokolů transakcí.  Azure Database for MariaDB automaticky vytvoří zálohy serveru a uloží je v uživatelsky nakonfigurovaném místně redundantním nebo geograficky redundantním úložišti. Zálohy lze použít k obnovení serveru do určitého bodu v čase. Zálohování a obnovení jsou základní součástí jakékoli strategie kontinuity podnikových procesů, protože chrání data před náhodným poškozením nebo odstraněním.  Výchozí doba uchovávání záloh je sedm dní. Volitelně je můžete nakonfigurovat až 35 dní. Všechny zálohy se šifrují s využitím 256bitového šifrování AES.

- [Principy zálohování pro MariaDB](concepts-backup.md)

- [Principy počáteční konfigurace MariaDB](tutorial-design-database-using-portal.md)

**Odpovědnost:** Sdílené

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: Azure Database for MariaDB automaticky vytvoří zálohy serveru a uloží je v uživatelsky nakonfigurovaném místně redundantním nebo geograficky redundantním úložišti. Zálohy lze použít k obnovení serveru do určitého bodu v čase.  Zálohování a obnovení jsou základní součástí jakékoli strategie kontinuity podnikových procesů, protože chrání data před náhodným poškozením nebo odstraněním.

Pokud používáte Key Vault pro šifrování dat na straně klienta pro data uložená na vašem serveru MariaDB, zajistěte pravidelné automatizované zálohování vašich klíčů.

- [Principy zálohování pro MariaDB](concepts-backup.md)

- [Postup zálohování klíčů Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Odpovědnost:** Sdílené

**Azure Security Center Monitoring**: [Srovnávací test zabezpečení Azure](/azure/governance/policy/samples/azure-security-benchmark) je výchozí iniciativou pro Security Center a je základem pro [doporučení Security Center](/azure/security-center/security-center-recommendations). Definice Azure Policy související s tímto ovládacím prvkem jsou automaticky povoleny Security Center. Výstrahy související s tímto ovládacím prvkem můžou pro související služby vyžadovat plán [Azure Defender](/azure/security-center/azure-defender) .

**Azure Policy předdefinované definice – Microsoft. DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Doprovodné** materiály: v Azure Database for MariaDB proveďte obnovení ze zálohy původního serveru pro pravidelné testování záloh. K dispozici jsou dva typy obnovení: obnovení k bodu v čase a geografické obnovení. Obnovení k určitému bodu v čase je dostupné s možností redundance zálohy a vytvoří nový server ve stejné oblasti jako původní server. Geografické obnovení je k dispozici pouze v případě, že jste server nakonfigurovali pro geograficky redundantní úložiště a máte možnost obnovit server do jiné oblasti.

Odhadovaná doba obnovení závisí na několika faktorech, včetně velikostí databází, velikosti transakčního protokolu, šířky pásma sítě a celkového počtu databází obnovování ve stejné oblasti ve stejnou dobu. Doba obnovení je obvykle méně než 12 hodin.

- [Principy zálohování a obnovení v Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-backup#restore)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: Azure Database for MariaDB přebírá úplné a rozdílové zálohy a zálohování protokolů transakcí. Tyto zálohy umožňují obnovit server k jakémukoli časovému okamžiku v rámci nakonfigurované doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Volitelně je můžete nakonfigurovat až 35 dní. Všechny zálohy se šifrují s využitím 256bitového šifrování AES.

- [Principy zálohování a obnovení v Azure Database for MariaDB](concepts-backup.md)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak je Security Center ve vyhledávání nebo v metrikě, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete jasně označit odběry pomocí značek a vytvořit názvový systém, který bude jasně identifikovat a kategorizovat prostředky Azure, zejména ty, které zpracovávají citlivá data. Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Odpovědnost:** Zákazník

**Monitorování Azure Security Center**: žádné

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

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

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.
    

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

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](/azure/security/benchmarks/overview).
- Další informace o [základních úrovních zabezpečení Azure](/azure/security/benchmarks/security-baselines-overview)
