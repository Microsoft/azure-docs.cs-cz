---
title: Základní plán zabezpečení Azure pro zálohování
description: Základní plán zabezpečení Azure pro zálohování
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e71beb4e4b5d23dcd1cffa1f60462d782d37db2e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572188"
---
# <a name="azure-security-baseline-for-backup"></a>Základní plán zabezpečení Azure pro zálohování

Základní plán zabezpečení Azure pro zálohování obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [řízení zabezpečení: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Chraňte prostředky pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

**Doprovodné** materiály: nepoužitelné; k trezoru Recovery Services nelze přidružit virtuální síť, podsíť ani skupinu zabezpečení sítě. Při zálohování virtuálního počítače Azure se data přenáší přes páteřní síť Azure. Při zálohování z místního počítače se v Azure vytvoří šifrované tunelové propojení s konkrétním koncovým bodem a přihlašovací údaje se používají k předšifrování dat před jejich odesláním pomocí šifrovaného tunelu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuální sítě, podsítí a síťových karet.

**Doprovodné** materiály: nepoužitelné; k trezoru Recovery Services nelze přidružit virtuální síť, podsíť ani skupinu zabezpečení sítě. Při zálohování virtuálního počítače Azure se data přenáší přes páteřní síť Azure. Při zálohování z místního počítače se v Azure vytvoří šifrované tunelové propojení s konkrétním koncovým bodem a přihlašovací údaje se používají k předšifrování dat před jejich odesláním pomocí šifrovaného tunelu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Doprovodné** materiály: koncové body, které používá Azure Backup (včetně agenta Microsoft Azure Recovery Services), jsou spravovány společností Microsoft. Zodpovídáte za všechny další ovládací prvky, které chcete nasadit do místních systémů.

- [Vysvětlení sítě a podpory přístupu pro agenta MARS](./backup-support-matrix-mars-agent.md#networking-and-access-support)

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: záznam síťových paketů a protokolů toků

**Doprovodné** materiály: nepoužitelné; k trezoru Recovery Services nelze přidružit virtuální síť, podsíť ani skupinu zabezpečení sítě. Při zálohování virtuálního počítače Azure se data přenáší přes páteřní síť Azure. Při zálohování z místních počítačů se v Azure vytvoří šifrované tunelové propojení s konkrétním koncovým bodem a přihlašovací údaje se používají k předšifrování dat před jejich odesláním pomocí šifrovaného tunelu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Doprovodné** materiály: koncové body, které používá Azure Backup (včetně agenta Microsoft Azure Recovery Services), jsou spravovány společností Microsoft. Zodpovídáte za všechny další ovládací prvky, které chcete nasadit do místních systémů.

- [Vysvětlení sítě a podpory přístupu pro agenta MARS](./backup-support-matrix-mars-agent.md#networking-and-access-support)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: Pokud používáte agenta Mars na virtuálním počítači Azure, použijte na NSG nebo Azure firewall značku služby AzureBackup a umožněte odchozí přístup Azure Backup.

- [Zálohování databází SQL Server ve virtuálních počítačích Azure](./backup-sql-server-database-azure-vms.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Doprovodné** materiály: nepoužitelné; koncové body, které používá Azure Backup (včetně agenta Microsoft Azure Recovery Services), jsou spravovány společností Microsoft.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Pokyny**: Pokud používáte agenta Mars na virtuálním počítači Azure, přidružte tento virtuální počítač ke skupině zabezpečení sítě, použijte popis k určení obchodních potřeb pro pravidlo.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: Pokud používáte agenta Mars na virtuálním počítači Azure, který je chráněný NSG nebo Azure firewall, použijte k monitorování konfigurace NSG nebo brány firewall protokol aktivit Azure. Můžete vytvářet výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny těchto prostředků.

- [Zobrazení a načtení událostí protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Vytváření, zobrazování a správa výstrah protokolu aktivit pomocí Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Doprovodné** materiály: nepoužitelné; Společnost Microsoft udržuje zdroj času, který se používá pro prostředky Azure, například Azure Backup, pro časová razítka v protokolech.

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro vaše prostředky Azure.

Také ingestování protokolů prostřednictvím Azure Monitor k agregaci dat zabezpečení vygenerovaných Azure Backup. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty úložiště pro dlouhodobé a archivační úložiště. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo incidentu zabezpečení a řízení událostí zabezpečení (SIEM) třetí strany.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Použití nastavení diagnostiky pro pro úložiště Recovery Services](./backup-azure-diagnostic-events.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: pro protokolování auditu řídicích rovin povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) prováděné na úrovni ovládacího prvku pro vaše prostředky Azure.

Kromě toho Azure Backup odesílá diagnostické události, které je možné shromažďovat a používat pro účely analýzy, upozorňování a vytváření sestav. Nastavení diagnostiky pro Recovery Services trezor můžete nakonfigurovat pomocí Azure Portal. K účtu úložiště, centru událostí nebo pracovnímu prostoru Log Analytics můžete odeslat jednu nebo více diagnostických událostí.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Použití nastavení diagnostiky pro pro úložiště Recovery Services](./backup-azure-diagnostic-events.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování protokolu pro pracovní prostory Log Analytics přidružené k vašim trezorům Azure Recovery Services podle předpisů pro dodržování předpisů vaší organizace.

- [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: Azure Backup poskytuje integrované funkce monitorování a upozorňování v trezoru Recovery Services. Tyto možnosti jsou k dispozici bez jakékoli další infrastruktury pro správu. Můžete také zvýšit měřítko monitorování a vytváření sestav pomocí Azure Monitor.

Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do pracovního prostoru Log Analytics. Můžete provádět dotazy v Log Analytics k hledání podmínek, identifikaci trendů, analýze vzorů a poskytování mnoha dalších přehledů na základě dat protokolu aktivit, která se mohla shromažďovat pro Recovery Services trezory.

- [Monitorování úloh Azure Backup](./backup-azure-monitoring-built-in-monitor.md)

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Jak shromažďovat a analyzovat protokoly aktivit Azure v pracovním prostoru Log Analytics v Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé

**Doprovodné** materiály: Azure Backup poskytuje integrované funkce monitorování a upozorňování v trezoru Recovery Services. Tyto možnosti jsou k dispozici bez jakékoli další infrastruktury pro správu. Můžete také zvýšit měřítko monitorování a vytváření sestav pomocí Azure Monitor.

Výstrahy jsou primárně ve scénářích, kdy se uživatelům pošle oznámení, aby mohli provádět příslušné akce. Část výstrahy zálohování zobrazuje výstrahy vygenerované službou Azure Backup. Tyto výstrahy definuje služba a nemůžete vytvářet vlastní výstrahy.

Můžete taky připojit Log Analytics pracovní prostor ke službě Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje vytvořit playbooky (automatizovaná řešení) a použít je k nápravě problémů zabezpečení. Kromě toho můžete pomocí Azure Monitor vytvořit vlastní výstrahy protokolu v pracovním prostoru Log Analytics.

- [Monitorování úloh Azure Backup](./backup-azure-monitoring-built-in-monitor.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Vytváření, zobrazování a správa výstrah protokolu pomocí Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: nepoužitelné; Azure Backup nezpracovává ani nevytváří protokoly související s malwarem.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: nepoužitelné; Azure Backup nezpracovává ani nevytváří protokoly související se službou DNS.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v tématu [řízení zabezpečení: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: Azure Active Directory (AD) mají předdefinované role, které se musí explicitně přiřadit a které jsou Queryable. Pomocí modulu Azure AD PowerShell můžete provádět ad hoc dotazy a zjišťovat účty, které jsou členy skupin pro správu.

Podpůrná dokumentace:

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Doprovodné** materiály: Azure AD nemá koncept výchozích hesel. Další prostředky Azure, které vyžadují heslo, vynutí vytvoření hesla s požadavky na složitost a minimální délkou hesla, která se liší v závislosti na službě. Zodpovídáte za aplikace třetích stran a služby Marketplace, které mohou používat výchozí hesla.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho, abyste mohli sledovat vyhrazené účty pro správu, můžete použít doporučení z Azure Security Center nebo předdefinovaných zásad Azure, jako je: k předplatnému by měli být přiřazeni více vlastníků, jejichž účty mají oprávnění vlastníka, a proto byste měli z předplatného odebrat oprávnění vlastníka.

- [Použití Azure Security Center k monitorování identity a přístupu (Preview)](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Pokyny**: použijte registraci aplikace Azure (instanční objekt) k získání tokenu, který se dá použít k interakci s Recovery Servicesmi trezory prostřednictvím volání rozhraní API.

- [Volání rozhraní Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Postup registrace klientské aplikace (instančního objektu) ve službě Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informace o rozhraní API pro Azure Recovery Services](/rest/api/recoveryservices/)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Pokyny**: když v Azure Backup provedete kritické operace, je nutné zadat bezpečnostní kód PIN, který je k dispozici v Azure Portal. Povolením Multi-Factor Authentication služby Azure AD přidáte vrstvu zabezpečení. K Azure Portal mají přístup jenom autorizovaní uživatelé s platnými přihlašovacími údaji Azure a ověřené z druhého zařízení.

- [Multi-Factor Authentication v Azure Backup](./backup-azure-security-feature.md)

- [Plánování nasazení cloudové služby Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: použití pracovní stanice s privilegovaným přístupem (privilegovaným přístupem) se službou Azure AD Multi-Factor Authentication (MFA) nakonfigurovanou pro přihlášení k a konfiguraci vašich prostředků s povoleným Azure Backupm.

- [Pracovní stanice s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Plánování nasazení cloudové služby Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu

**Pokyny**: pomocí Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) můžete generovat protokoly a výstrahy, když dojde k podezřelé nebo nebezpečné aktivitě v prostředí.

Navíc můžete pomocí zjišťování rizik Azure AD zobrazovat výstrahy a sestavy týkající se rizikového chování uživatelů.

- [Postup nasazení Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Vysvětlení zjišťování rizik Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup k Azure Portal jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Doprovodné** materiály: jako centrální ověřování a systém autorizací pro vaše Azure Backup instance použijte Azure Active Directory (AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Jak nakonfigurovat Azure Backup pro použití přihlášení Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure Active Directory (AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Navíc můžete pomocí kontrol přístupu Azure identity efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům

**Doprovodné** materiály: jako centrální ověřování a systém autorizací pro vaše Azure Backup instance použijte Azure Active Directory (AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

Máte přístup ke zdrojům přihlašovacích aktivit, auditování a rizikových protokolů událostí Azure AD, které vám umožní integrovat s ověřováním Azure Sentinel nebo SIEM třetí strany.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy protokolu můžete nakonfigurovat v rámci Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Postup zprovoznění služby Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Doprovodné** materiály: jako centrální ověřování a systém autorizací pro vaše trezory Recovery Services použijte Azure Active Directory (AD). V případě odchylky chování přihlášení k účtu na rovině ovládacího prvku (Azure Portal) použijte funkce Azure AD Identity Protection a detekce rizik ke konfiguraci automatizovaných odpovědí na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.

- [Jak nakonfigurovat Azure Backup pro použití přihlášení Azure AD](../app-service/configure-authentication-provider-aad.md)

- [Jak zobrazit rizikové přihlášení ke službě Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: aktuálně není k dispozici; Customer Lockbox ještě není pro Azure Backup podporovaná.

- [Seznam služeb podporovaných Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: při zálohování virtuálních počítačů Azure IaaS poskytuje Azure Backup nezávislé a izolované zálohy pro ochranu před náhodným zničením původních dat. Zálohy jsou uloženy v trezoru služby Recovery Services s integrovanou správou bodů obnovení.

Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a produkční úložiště Recovery Services. Prostředky by měly být oddělené podle virtuální sítě a podsítě, musí se vhodně označit a zabezpečit pomocí NSG nebo Azure Firewall. Prostředky, které ukládají nebo zpracovávají citlivá data, by měly být dostatečně izolované. Pokud Virtual Machines ukládáte nebo zpracováváte citlivá data, implementujte zásady a postupy pro jejich vypnutí, pokud se nepoužívají.

Podpůrná dokumentace:

- [Přehled Azure Backup](./backup-overview.md)

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Doprovodné** materiály: aktuálně není k dispozici; pro Azure Backup ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Backup a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Sdílené

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: provoz zálohování ze serverů do trezoru Recovery Services se přenáší přes zabezpečené připojení HTTPS a zašifruje se pomocí standard AES (Advanced Encryption Standard) (AES) 256, když je uložený v trezoru.

- [Vysvětlení šifrování v klidovém Azure Backup](./backup-encryption.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: aktuálně není k dispozici; pro Azure Backup ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací.

Společnost Microsoft spravuje základní infrastrukturu pro Azure Backup a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost**: aktuálně není k dispozici.

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: řízení přístupu na základě role v Azure (Azure RBAC) umožňuje jemně odstupňovanou správu přístupu pro Azure. Pomocí Azure RBAC můžete oddělit povinnosti v rámci týmu a udělit uživatelům jenom takový přístup, který potřebují k výkonu své práce.

Azure Backup poskytuje tři předdefinované role pro řízení operací správy zálohování: Přispěvatel zálohování, operátor zálohování a čtečka zálohování. Předdefinované role zálohování můžete mapovat na různé akce správy zálohování.

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Použití řízení přístupu na základě role Azure ke správě Azure Backup bodů obnovení](./backup-rbac-rs-vault.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Společnost Microsoft spravuje základní infrastrukturu pro Azure Backup a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Ochrana zákaznických dat Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Zodpovědnost**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: Azure Backup podporuje šifrování pro data v klidovém umístění. V případě místního zálohování se šifrování v klidovém stavu zajišťuje pomocí hesla, které zadáte při zálohování do Azure. Pro cloudové úlohy jsou data zašifrovaná – při použití Šifrování služby Storage (SSE). Microsoft nikdy nedešifruje zálohovaná data.

Při zálohování s agentem MARS nebo pomocí Recovery Services trezoru zašifrovaného s klíčem spravovaným zákazníkem je k dispozici pouze přístup k šifrovacímu klíči. Společnost Microsoft nikdy neudržuje kopii a nemá přístup k tomuto klíči. Pokud je klíč nesprávně umístěn, Microsoft nemůže obnovit zálohovaná data.

- [Vysvětlení šifrování v klidovém umístění pro Azure Backup](./backup-encryption.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet upozornění na to, kdy změny probíhají v produkčních trezorech Azure Recovery Services i v jiných důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: ještě není k dispozici; posouzení ohrožení zabezpečení v Azure Security Center ještě není k dispozici pro Azure Backup.

Nadřazená platforma byla zkontrolována a opravena společností Microsoft. Přečtěte si bezpečnostní mechanismy, které jsou k dispozici pro Azure Backup snížení zabezpečení souvisejícího s konfigurací služby.

- [Porozumění ovládacím prvkům zabezpečení dostupným pro Azure Backup]()

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: aktuálně není k dispozici; Konfigurace zabezpečení pro Azure Backup ještě nejsou v Azure Security Center podporované.

- [Seznam Azure Security Center podporovaných služeb PaaS Services](../security-center/features-paas.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

**Pokyny**: pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly).  Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků Azure. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v předplatných zákazníka pomocí následujících integrovaných definic zásad: nepovolené typy prostředků povolené typy prostředků.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: udržování inventáře schválených prostředků Azure a softwarových titulů

**Pokyny**: definování schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit ve vašich předplatných.

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad: nepovolené typy prostředků povolené typy prostředků

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Omezení schopnosti uživatelů pracovat s Azure Resource Manager prostřednictvím skriptů</div>

**Pokyny**: Nakonfigurujte podmíněný přístup Azure tak, aby uživatelé mohli komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [řízení zabezpečení: zabezpečená konfigurace](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Recovery Services trezor pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. RecoveryServices můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace trezorů Recovery Services.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: Pokud používáte vlastní definice Azure Policy, použijte k bezpečnému ukládání a správě kódu službu Azure DevOps nebo Azure Repos.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow)

- [Dokumentace k Azure Repos](/azure/devops/repos/index)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. RecoveryServices, abyste vytvořili vlastní zásady pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. RecoveryServices, abyste vytvořili vlastní zásady pro upozornění, audit a prosazování konfigurace systému. K automatickému vymáhání konfigurací pro prostředky Azure použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje].

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Pokyny**: při nastavování agenta Mars Uložte šifrovací heslo v rámci Azure Key Vault.

- [Vytvoření Key Vault](../key-vault/secrets/quick-create-portal.md)

* [Ověření Key Vault](../key-vault/general/authentication.md)

* [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: nepoužitelné; Spravované identity se pro Azure Backup nepodporují.

- [Služby, které podporují spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [řízení zabezpečení: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro výpočetní prostředky. Microsoft Anti-malware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Backup), ale neběží na zákaznickém obsahu.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure Backup), ale neběží na vašem obsahu.

Předem Prohledejte všechny soubory nahrané do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage a Blob Storage.

K detekci malwaru nahraného do účtů úložiště použijte detekci hrozeb Azure Security Center pro datové služby.

- [Pochopení Microsoft antimalwaru pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

- [Vysvětlení detekce hrozeb Azure Security Center pro datové služby](../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro prostředky, které se zálohují, a neAzure Backup sám sebe.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost**: netýká se

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

**Doprovodné** materiály: místně redundantní úložiště (LRS) replikuje vaše data třikrát (vytváří tři kopie dat) v jednotce škálování úložiště v datacentru. Všechny kopie dat existují ve stejné oblasti. LRS je cenová možnost pro ochranu vašich dat před selháním místního hardwaru. Geograficky redundantní úložiště (GRS) je výchozí a doporučená možnost replikace. GRS replikuje vaše data do sekundární oblasti (vzdálené stovky kilometrů od primárního umístění zdrojových dat). GRS náklady jsou větší než LRS, ale GRS poskytuje vyšší úroveň trvanlivosti vašich dat, i když dojde k oblastnímu výpadku.

Zálohujte spravované klíče zákazníka v rámci Azure Key Vault.

- [Přehled Azure Backup](./backup-overview.md)

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Pochopení šifrování v Azure Backup](./backup-encryption.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: Ověřte všechny zálohy včetně spravovaných klíčů zákazníků.

**Pokyny**: testování obnovení zálohovaných spravovaných klíčů zákazníků.

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

**Doprovodné** materiály: pro místní zálohování je k dispozici šifrování v klidovém formátu pomocí hesla, které zadáte při zálohování do Azure. Pro virtuální počítače Azure jsou neaktivní uložená data zašifrovaná pomocí Šifrování služby Storage (SSE). Můžete povolit obnovitelné odstranění v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

- [Jak povolit obnovitelné odstranění v Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. Zajistěte, aby existovaly písemné plány reakcí na incidenty, které definují všechny role pracovníků, a také fáze zpracování nebo správy incidentů od jejich detekce až po přezkoumání po jejich ukončení.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [K podpoře při vytváření vlastního plánu odpovědí na incidenty můžete také využít příručku pro zpracování incidentů v počítači s NIST.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si téma publikace NIST: Průvodce testováním, školením a cvičením programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana.  Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

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

*Další informace najdete v tématu [řízení zabezpečení: testy průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech důležitých zjištění zabezpečení do 60 dnů.

**Doprovodné** materiály: [podle pravidel Microsoft Engagement zajistěte, aby testy průniku nebyly v rozporu s zásadami Microsoftu](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) .

- [V této části najdete další informace o strategii Microsoftu a provádění testování v rámci červeného seskupování a testování průniku na cloudové infrastruktuře, služby a aplikace spravované Microsoftem.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [plánech zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)