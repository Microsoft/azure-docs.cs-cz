---
title: Základní hodnoty zabezpečení Azure pro Site Recovery
description: Základní Site Recovery zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf91c0a6e533acb326fe815c3e3c1088c959a603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576724"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Základní hodnoty zabezpečení Azure pro Site Recovery

Tato základní hodnota zabezpečení platí pro Site Recovery pokynů od [zabezpečení Azure Security test 1,0](../security/benchmarks/overview-v1.md) . Azure Security Benchmark poskytuje doporučení, jak můžete zabezpečit svá cloudová řešení v Azure. Obsah je seskupen podle **řízení zabezpečení** definovaného srovnávacím testem zabezpečení Azure a souvisejících pokynů, které se vztahují na Site Recovery. **Ovládací prvky** , které se nevztahují k Site Recovery byly vyloučeny. 

Pokud chcete zjistit, jak Site Recovery kompletně mapuje srovnávací test zabezpečení Azure, přečtěte si [celý soubor mapování standardních hodnot Site Recovery Security](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v tématu [Azure Security Benchmark: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: Microsoft Azure Site Recovery nepodporuje nasazení do služby Azure Virtual Network. Nakonfigurujte Site Recovery službu pomocí privátního koncového bodu Azure pro vymáhání zabezpečené komunikace v síti.

- [Podpora privátních odkazů Azure Site Recovery](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: Služba Site Recovery podporuje značky služeb, které zákazníkům umožňují otevřít provoz jenom pro konkrétní služby a porty. Zákazníci musí ve své bráně firewall nebo skupině zabezpečení sítě umožňovat označení služby AzureSiteRecovery, aby mohl odchozí přístup k Site Recovery službě.

- [Odchozí připojení pomocí značek služeb](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky prostředků pro skupiny zabezpečení sítě a další prostředky, které se týkají zabezpečení sítě a toku provozu. Pro jednotlivá pravidla skupiny zabezpečení sítě použijte pole Popis k dokumentaci pravidel, která umožňují provoz do a ze sítě. 

Zahrňte všechny předdefinované Azure Policy definice týkající se označování, jako je "vyžadovat značku a její hodnotu", aby se zajistilo, že všechny prostředky budou vytvořeny pomocí značek, a upozorní vás na stávající neoznačené prostředky. 

Pomocí Azure PowerShell nebo Azure CLI můžete vyhledat nebo provádět akce s prostředky na základě jejich značek. 

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md) 

- [Postup vytvoření Virtual Network Azure](../virtual-network/quick-create-portal.md) 

- [Postup filtrování síťového provozu pomocí pravidel skupiny zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Doprovodné** materiály: Sledujte všechny změny konfigurace síťových prostředků, které se vztahují ke službě Site Recovery pomocí protokolů aktivit Azure. V Azure Monitor můžete vytvářet výstrahy, které vám upozorní na změnu důležitých Site Recovery síťových prostředků.

- [Zobrazení a načtení událostí protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Vytváření, zobrazování a správa výstrah protokolu aktivit pomocí Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure pro protokolování auditu a odešlete protokoly do pracovního prostoru Log Analytics, Azure Storage účtu nebo centra událostí Azure pro archivaci.

Data protokolu aktivit Azure použijte k určení "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) provedené na vašich prostředcích Azure.

Ingestování Site Recovery protokolů v Azure Monitor k agregaci generovaných dat zabezpečení. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty úložiště pro dlouhodobé nebo archivaci úložiště. Můžete také povolit a začlenit data do Azure Sentinel nebo řešení pro řízení událostí a zabezpečení SIEM (Event Management) jiného výrobce.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Monitorování Site Recovery s využitím protokolů Azure Monitoru](monitor-log-analytics.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure pro protokolování auditu a odešlete protokoly do Log Analytics pracovního prostoru, Azure Storage účtu nebo do centra událostí Azure pro účely archivace. 

Data protokolu aktivit Azure použijte k určení "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) provedené na vašich prostředcích Azure.

Ingestování Site Recovery protokolů pomocí Azure Monitor k agregaci generovaných dat zabezpečení. V rámci Azure Monitor Používejte pracovní prostory Log Analytics k dotazování a provádění analýz a k dlouhodobému a Archivačnímu úložišti používejte účty úložiště. Povolte a zapněte data pro Azure Sentinel nebo řešení incidentu zabezpečení a řízení událostí (SIEM) jiného výrobce.

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Monitorování Site Recovery s využitím protokolů Azure Monitoru](monitor-log-analytics.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Pokyny**: Nastavte dobu uchování protokolu pro Log Analytics pracovní prostory přidružené k vašim trezorům Azure Recovery Services pomocí Azure monitor podle předpisů pro dodržování předpisů vaší organizace. 

- [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Doprovodné** materiály: Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do pracovního prostoru Log Analytics. 

Provádějte dotazy v Log Analytics k hledání podmínek, identifikujte trendy, analyzujte vzorce a poznatky o datech protokolu aktivit shromažďovaných z trezorů Recovery Services.

- [Monitorování Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/essentials/activity-log.md)

- [Jak shromažďovat a analyzovat protokoly aktivit Azure v pracovním prostoru Log Analytics v Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: monitorování počítačů replikovaných pomocí Azure Site Recovery pomocí protokolů Azure Monitor a Log Analytics. Pomocí Log Analytics v rámci Azure Monitor můžete zapisovat a testovat dotazy protokolů a interaktivně analyzovat data protokolu. Azure Monitor shromažďuje protokoly aktivit a prostředků spolu s dalšími daty monitorování. 

Vizualizujte a Dotazujte výsledky protokolu a nakonfigurujte výstrahy tak, aby na základě monitorovaných dat probraly akce. Nastavení výstrah v pracovním prostoru Log Analytics do Azure Sentinel, protože poskytuje řešení pro automatizované odpovědi na orchestraci zabezpečení (společnosti). To umožňuje automatizovaná řešení, jako je playbooky, vytvářet a používat k nápravě problémů se zabezpečením. Vytvářejte vlastní výstrahy protokolu v pracovním prostoru Log Analytics pomocí Azure Monitor. 

- [Monitorování Site Recovery](site-recovery-monitor-and-troubleshoot.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Vytváření, zobrazování a správa výstrah protokolu pomocí Azure Monitor](../azure-monitor/alerts/alerts-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: ve výchozím nastavení nejsou přiřazeny žádné role. Je potřeba je explicitně přiřadit na základě obchodních potřeb. Jakékoli přiřazení rolí se dá zkontrolovat pomocí PowerShell CLI nebo Azure Active Directory (Azure AD) a zjistit účty, které jsou členy skupin pro správu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Doprovodné** materiály: vytvořte standardní operační postupy kolem používání vyhrazených účtů pro správu. Pomocí funkcí správy identit a přístupu Security Center můžete monitorovat počet účtů pro správu.

Kromě toho, abyste mohli sledovat vyhrazené účty pro správu, použijte doporučení z Security Center nebo integrovaných zásad Azure, jako jsou: 

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník. 
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného. 

- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

Vytvořte proces, ve kterém můžete sledovat identitu a řízení přístupu pro účty pro správu a pravidelně je kontrolovat.

- [Použití Azure Security Center k monitorování identity a přístupu](../security-center/security-center-identity-access.md)

- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Pokyny**: pomocí registrace aplikací Azure s instančním objektem načtěte token, který se použije k interakci s Recovery Servicesmi trezory prostřednictvím volání rozhraní API.

- [Volání rozhraní Azure REST API](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Postup registrace klientské aplikace (instančního objektu) ve službě Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Informace o rozhraní API pro Azure Recovery Services](/rest/api/recoveryservices)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: Povolte službu Azure AD, vícefaktorové ověřování a postupujte podle doporučení Security Center identity a přístupu. 
- [Plánování nasazení Multi-Factor Authentication služby Azure AD](../active-directory/authentication/howto-mfa-getstarted.md)

- [Monitorování identity a přístupu](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Doprovodné** materiály: Použijte zabezpečenou pracovní stanici spravovanou Azure (označovanou také jako pracovní stanice s privilegovaným přístupem (privilegovaným přístupem)) se službou Azure vícefaktorového ověřování pro úlohy správy a k provádění privilegovaných akcí na site Recoverych prostředcích.

- [Pracovní stanice s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Plánování nasazení cloudové služby Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití funkce Privileged Identity Management služby Azure AD pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě.
Podívejte se na výstrahy a sestavy týkající se rizikového chování uživatelů pomocí funkce zjišťování rizik Azure AD.

- [Postup nasazení Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Vysvětlení zjišťování rizik Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup k Azure Portal jenom z konkrétních logických skupin rozsahů IP adres, oblastí nebo zemí.
- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: použití Azure AD jako centrálního ověřování a autorizačního systému pro Site Recovery. Azure AD chrání data pomocí silného šifrování dat v klidovém provozu, při přenosu a také solí, hodnot hash a bezpečně ukládá přihlašovací údaje uživatele. 

- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Pokyny**: pomocí protokolů služby Azure AD můžete zjistit zastaralé účty. 

Efektivně spravujte členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí s využitím kontrol identit a přístupů služby Azure AD. 

Vytvořte proces, který pravidelně kontroluje přístup uživatelů, aby bylo zajištěno, že budou mít přístup jenom uživatelé s dokončenými recenzemi přístupu. 

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure AD pro Site Recovery prostředky. Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data a také soli, hodnoty hash a bezpečně ukládá přihlašovací údaje uživatele.

Máte přístup ke zdrojům aktivit přihlašování, auditu a rizikových událostí služby Azure AD, které vám umožní jejich integraci s ověřováním Azure Sentinel nebo jakýmkoli SIEM nebo monitorovacím nástrojem dostupným v Azure Marketplace.

Tento proces můžete ještě víc zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Postup zprovoznění služby Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Pokyny**: jako centrální ověřování a systém autorizací pro vaše Recovery Services trezory použijte službu Azure AD. 

Využití funkcí ochrany identit Azure AD pro detekci chování přihlášení k účtu a konfigurace automatizovaných odpovědí na zjištěné podezřelé akce, které se týkají identit uživatelů. Také ingestujte data do Azure Sentinel pro další šetření.

- [Jak zobrazit rizikové přihlášení ke službě Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [Azure Security Benchmark: ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Doprovodné** materiály: implementace samostatných předplatných nebo skupin pro správu pro vývoj, testování a provozní trezory Recovery Services. Oddělení prostředků pomocí virtuální sítě nebo podsítě, správné označení a zabezpečené skupinou zabezpečení sítě nebo Azure Firewall. 

Vypněte virtuální počítače, které ukládají nebo zpracovávají citlivá data, pokud se nepoužívají. K provedení tohoto opakovaného procesu Implementujte zásady a postupy. 

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Přehled Site Recovery](site-recovery-overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny**: pomocí privátního odkazu nebo privátního koncového bodu, skupin zabezpečení sítě a značek služeb můžete zmírnit případné příležitosti pro exfiltrace dat z virtuálních počítačů s povoleným Site Recovery.

Společnost Microsoft spravuje základní platformu, kterou používá Site Recovery a který zpracovává veškerý obsah zákazníka jako citlivý a ochrana před ztrátou a únikem dat zákazníků. Společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat, aby se zajistilo zabezpečení zákaznických dat v Azure. 

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

- [Replikace virtuálních počítačů s privátními koncovými body Azure](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Replikace virtuálních počítačů pomocí značek služby Azure Site Recovery](azure-to-azure-about-networking.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Pokyny**: Site Recovery používá zabezpečený kanál https šifrovaný pomocí standard AES (Advanced Encryption Standard) (AES 256), ze serverů Azure pro úlohy do služeb Site Recovery služby hostované v Recovery Services trezoru.

Aktuální verze TLS podporované pro Site Recovery jsou TLS 1,0, TLS 1,1, TLS 1,2 v oblastech, které byly na konci 2019 živé. Protokol TLS 1.2 je jedinou podporovanou verzí TLS pro všechny nové oblasti.

- [Principy šifrování při přenosu pro Azure Site Recovery](physical-azure-set-up-source.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Site Recovery. 

Pro účely dodržování předpisů implementujte řešení třetí strany podle potřeby.

Společnost Microsoft spravuje základní platformu, kterou používá Site Recovery a který považuje veškerý obsah zákazníka za citlivý a chrání před ztrátou a únikem dat zákazníků. Implementoval a udržuje sadu robustních ovládacích prvků ochrany dat a možností, které zajistí, aby zákaznická data v Azure zůstala zabezpečená. 

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: ke správě přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: pomocí řízení přístupu na základě role Azure (Azure RBAC) můžete spravovat přístup k datům a prostředkům souvisejícím s site Recoverymi prostředky. 

Oddělte pracovní cla pomocí Azure RBAC a udělte jim odpovídající přístup. Pro řízení operací správy Site Recovery použijte předdefinované role Site Recovery.

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Ke správě Azure Site Recovery použijte Role-Based Access Control](site-recovery-role-based-linked-access-control.md)

**Monitorování služby Azure Security Center:** Momentálně není k dispozici

**Odpovědnost:** Zákazník

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: Povolte dvojité šifrování s klíči spravovanými platformou a zákazníkem. Tato funkce je k dispozici v Site Recovery. 

Site Recovery podporuje místní šifrování pro data. Pro úlohy Azure IaaS jsou data zašifrovaná – v klidovém formátu pomocí Šifrování služby Storage (SSE). 

K šifrovacímu klíči má přístup jenom zákazník při použití trezoru Recovery Services šifrovaných pomocí klíče spravovaného zákazníkem. Společnost Microsoft nikdy neuchovává kopii, nemá přístup k tomuto klíči a nešifruje data přenesená z primárního do umístění zotavení po havárii v jakémkoli bodě. 

- [Podpora spravovaných klíčů Customer pro Azure Site Recovery](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Sdílené

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Pokyny**: pomocí Azure monitor s protokoly aktivit Azure můžete vytvářet výstrahy, když se změny projeví u kritických prostředků. Tyto prostředky můžou zahrnovat produkční instance Recovery Servicesch trezorů, prostředků služby Site Recovery a souvisejících prostředků.
- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/alerts/alerts-activity-log.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu můžete v rámci svých předplatných dotazovat nebo zjišťovat všechny prostředky, včetně trezorů Recovery Services. Zajistěte, aby ve vašem tenantovi byla příslušná oprávnění ke čtení a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použijte značky pro Recovery Services trezory a další související prostředky, které používá Site Recovery s metadaty k jejich logickému uspořádání do taxonomie.
- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování Site Recovery (Recovery Services trezory) a dalších souvisejících prostředků. 

Kromě toho použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, a to pomocí těchto integrovaných definic zásad: 

- Žádné povolené typy prostředků
- Povolené typy prostředků

Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Pokyny**: Vytvoření inventáře schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky na základě požadavků organizace zákazníka.

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad: 

- Žádné povolené typy prostředků 
- Povolené typy prostředků

Pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky v rámci předplatných.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

- Žádné povolené typy prostředků 
- Povolené typy prostředků

Pochopení způsobu, jakým se v Azure vytvářejí a spravují zásady, je důležité pro zajištění souladu s firemními standardy a smlouvami o úrovni služeb.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací "blokování přístupu" pro aplikaci Microsoft Azure Management. To může zabránit vytváření a změnám prostředků v rámci vysoce zabezpečeného prostředí.

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro Recovery Services trezor pomocí Azure Policy. 

Pomocí aliasů Azure Policy v oboru názvů Microsoft. RecoveryServices můžete vytvořit vlastní zásady pro audit nebo vynutili konfiguraci Recovery Services úložiště Site Recovery služby.
- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [odepřít] a [nasazení, pokud neexistují] efekty pro vymáhání zabezpečených nastavení napříč prostředky Azure.
- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Doprovodné** materiály: vyberte Azure Repos pro bezpečné uložení a správu kódu, pokud používáte vlastní definice Azure Policy pro Recovery Services trezory a související prostředky.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. RecoveryServices, abyste vytvořili vlastní zásady pro upozornění, audit a prosazování konfigurace systému. 

Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Doprovodné** materiály: použijte předdefinované definice Azure Policy a také Azure Policy aliasy v oboru názvů Microsoft. RecoveryServices, abyste vytvořili vlastní zásady pro upozornění, audit a prosazování konfigurace systému. 

Použijte Azure Policy [audit], [Deny] a [nasazení, pokud neexistuje] efekty pro automatické vymáhání konfigurací pro prostředky Azure.
- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Pokyny**: zákazník by měl spravovat Site Recovery tajných kódů integrovaných s trezorem klíčů Azure a zároveň povolit zotavení po havárii pro virtuální počítače s podporou Azure Disk Encryption. 

- [Vytvoření trezoru klíčů](../key-vault/general/quick-create-portal.md)

- [Ověření v trezoru klíčů](../key-vault/general/authentication.md)

- [Přiřazení zásad přístupu trezoru klíčů](../key-vault/general/assign-access-policy-portal.md)

- [Jak povolit DR pro virtuální počítače s podporou Azure Disk Encryption pomocí Site Recovery](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: Site Recovery podporuje jenom identitu spravovanou systémem, kde zákazník může povolit identitu spravované systémem v trezoru Recovery Services. Stejná metodologie se vztahuje na prostředky, které se používají v nabídce zotavení po havárii k definování hranice přístupu. 

Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. 

Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů v kódu.

- [Integrace se spravovanými identitami Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [Postup povolení identity spravované systémem v trezoru Recovery Services](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Site Recovery), ale neběží na vašem obsahu. Předem Prohledejte všechny soubory nahrané do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage a Blob Storage.

K detekci malwaru nahraného do účtů úložiště použijte detekci hrozeb Security Center pro datové služby.

- [Pochopení Microsoft antimalwaru pro Azure Cloud Services a Virtual Machines](../security/fundamentals/antimalware.md)

- [Vysvětlení detekce hrozeb Azure Security Center pro datové služby](../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Doprovodné** materiály: Site Recovery interně používá Azure Storage účet k údržbě stavu řešení zotavení po havárii, jak je nakonfigurovali zákazníci na svých úlohách.

Všechny prostředky úložiště, které používá metadata služby Site Recovery Services s konfigurací typu: geograficky redundantní úložiště s přístupem pro čtení (RA-GRS). Účty úložiště typu nad GRS (například RAGRS, RAG-ZRS) replikují vaše data do sekundární oblasti (stovky kilometrů od primárního umístění zdrojových dat), aby se pro zákazníky mohla během výpadků dál obsluhovat zotavení po havárii.

Nejedná se o obor zákazníka a Site Recovery se tým postará o interní oddělení IT. Zákazník může zálohovat Key Vault klíče v Azure.

- [Postup zálohování klíčů trezoru klíčů v Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny**: pravidelné testování obnovení zálohovaných klíčů spravovaných zákazníkem.

- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: data jsou zašifrovaná – při použití šifrování služby Storage (SSE) s Virtual Machines založenou na infrastruktuře jako služby (IaaS) Azure. Povolit obnovitelné odstranění v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

- [Jak povolit obnovitelné odstranění v Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v tématu [Azure Security Benchmark: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny:** Sestavte pro vaši organizaci průvodce reakcemi na incidenty. 

Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování incidentů nebo správy z detekce po kontrolu incidentu.

- [Postup konfigurace automatizace pracovních postupů v rámci služby Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Zákazník může také využít příručku pro zpracování incidentů zabezpečení počítače v NIST, aby mohl pomoci při vytváření vlastního plánu reakce na incidenty.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Určete prioritu, které výstrahy by se měly prozkoumat jako první, na základě výstrahy s přiřazeným Security Center. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Označení předplatných jasně (například produkční, neprodukční) a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Přečtěte si průvodce publikováním v NIST – příručka pro testování, školení a cvičení programů pro plány a možnosti IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům zákazníka přistupovala protiprávní nebo neoprávněná strana. 

Vytvořte proces pro kontrolu incidentů, vystavení výskytu a zajištění vyřešení problémů.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Doprovodné** materiály: vyexportujte výstrahy a doporučení Security Center pomocí funkce průběžného exportu. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. 

Pomocí konektoru Security Center Data můžete v případě potřeby streamovat výstrahy do Azure Sentinel.
- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování služby Azure Security Center:** Nelze použít

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovního postupu v Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" na výstrahy a doporučení zabezpečení.
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

- Další informace najdete v článku [Přehled Azure Security Benchmark v2](../security/benchmarks/overview.md).
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)