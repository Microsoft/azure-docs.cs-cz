---
title: Základní hodnoty zabezpečení Azure pro Azure DevTest Labs
description: Základní hodnoty zabezpečení Azure pro Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 4ae3886e1c47b1d6835dc348b474e3d9e82a8f41
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348819"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Základní hodnoty zabezpečení Azure pro Azure DevTest Labs

Základní plán zabezpečení Azure pro Azure DevTest Labs obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení.

Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení, jak můžete zabezpečit cloudová řešení v Azure.

Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v tématu [řízení zabezpečení: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času
**Doprovodné materiály:** Microsoft udržuje časové zdroje pro prostředky Azure. Můžete ale spravovat nastavení synchronizace času pro výpočetní prostředky.

V následujícím článku se dozvíte, jak nakonfigurovat synchronizaci času pro výpočetní prostředky Azure: [čas synchronizace virtuálních počítačů s Windows v Azure](../virtual-machines/windows/time-sync.md). 

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení
**Doprovodné materiály:** Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Protokoly aktivit poskytují přehled o operacích, které byly provedeny na vašich Azure DevTest Labs instancích na úrovni roviny správy. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) provedené na úrovni roviny správy pro instance DevTest Labs.

Další informace najdete v tématu [Vytvoření nastavení diagnostiky pro odesílání protokolů platforem a metrik do různých umístění](../azure-monitor/platform/diagnostic-settings.md).

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure
**Doprovodné materiály:** Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do Log Analytics pracovního prostoru, centra událostí Azure nebo účtu Azure Storage pro archivaci. Protokoly aktivit poskytují přehled o operacích, které byly provedeny na vašich Azure DevTest Labs instancích na úrovni roviny správy. Pomocí dat protokolu aktivit Azure můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) provedené na úrovni roviny správy pro instance DevTest Labs.

Další informace najdete v tématu [Vytvoření nastavení diagnostiky pro odesílání protokolů platforem a metrik do různých umístění](../azure-monitor/platform/diagnostic-settings.md).

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů
**Doprovodné materiály:** Virtuální počítače s Azure DevTest Labs jsou vytvořené a vlastněné zákazníkem. Je to tedy zodpovědnost vaší organizace za jeho monitorování. Pomocí Azure Security Center můžete monitorovat výpočetní operační systém. Data shromažďovaná Security Center z operačního systému zahrnují typ a verzi operačního systému, operační systém (protokoly událostí systému Windows), spuštěné procesy, název počítače, IP adresy a přihlášený uživatel. Agent Log Analytics také shromažďuje soubory s výpisem stavu systému.

Další informace najdete v následujících článcích: 

- [Jak shromažďovat protokoly interního hostitele virtuálních počítačů Azure pomocí Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Pochopení Azure Security Center shromažďování dat](../security-center/security-center-enable-data-collection.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení
**_Doprovodné materiály:_* V Azure Monitor nastavte dobu uchování protokolu pro pracovní prostory Log Analytics přidružené k vašim Azure DevTest Labs instancí podle předpisů pro dodržování předpisů vaší organizace.

Další informace najdete v následujícím článku: [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) .

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly
**Doprovodné materiály:** Povolte nastavení diagnostiky protokolu aktivit Azure a odešlete protokoly do pracovního prostoru Log Analytics. Spouštějte dotazy v Log Analytics pro hledání podmínek, identifikujte trendy, analyzujte vzorce a poskytněte spoustu dalších přehledů na základě dat protokolů aktivit, které se můžou shromažďovat pro Azure DevTest Labs.

Další informace najdete v následujících článcích:

- [Postup povolení nastavení diagnostiky pro protokol aktivit Azure](../azure-monitor/platform/diagnostic-settings.md)
- [Jak shromažďovat a analyzovat protokoly aktivit Azure v pracovním prostoru Log Analytics v Azure Monitor](../azure-monitor/platform/activity-log.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: povolení výstrah pro aktivitu neobvyklé
**Doprovodné materiály:** Pracovní prostor Azure Log Analytics slouží k monitorování a upozorňování na aktivity neobvyklé v protokolech zabezpečení a událostech souvisejících s vaší Azure DevTest Labs.

Další informace najdete v následujícím článku: [jak upozornit na data protokolu Log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru
**Doprovodné materiály:** Nelze použít. Azure DevTest Labs nezpracovává ani nevytváří protokoly související s malwarem.

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS
**Doprovodné materiály:** Nelze použít. Azure DevTest Labs nezpracovává nebo nevytváří protokoly související se službou DNS.

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku
**Doprovodné materiály:** Azure DevTest Labs vytvoří výpočetní počítače Azure, které jsou vlastněné a spravované zákazníkem. K zaznamenání události vytvoření procesu a pole použijte Microsoft Monitoring Agent na všech podporovaných virtuálních počítačích Azure s Windows `CommandLine` . U podporovaných virtuálních počítačů se systémem Azure Linux můžete ručně nakonfigurovat protokolování konzoly na bázi jednotlivých uzlů a pomocí protokolu syslog ukládat data. K prohlížení protokolů a spouštění dotazů na protokolovaná data z virtuálních počítačů Azure taky použijte pracovní prostor Azure Monitor Log Analytics.

- [Shromažďování dat v Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Spuštění vlastních dotazů v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
- [Zdroje dat Syslogu ve službě Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

## <a name="identity-and-access-control"></a>Identita a řízení přístupu
*Další informace najdete v tématu [řízení zabezpečení: identita a Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu
**Doprovodné materiály:** Azure Active Directory (Azure AD) mají předdefinované role, které se musí explicitně přiřadit a jsou Queryable. Pomocí modulu Azure AD PowerShell můžete spouštět dotazy ad hoc a zjišťovat účty, které jsou členy skupin pro správu.

- [Jak získat roli adresáře ve službě Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [Jak načíst členy role adresáře v Azure AD pomocí PowerShellu](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Role Azure DevTest Labs](devtest-lab-add-devtest-user.md)  

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné
**Doprovodné materiály:** Azure Active Directory (Azure AD) nemá koncept výchozích hesel. Další prostředky Azure, které vyžadují heslo, vynutí vytvoření hesla s požadavky na složitost a minimální délkou hesla, která se liší v závislosti na službě. Zodpovídáte za aplikace třetích stran a služby Marketplace, které mohou používat výchozí hesla.

DevTest Labs nemá koncept výchozích hesel. 

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.
**Doprovodné materiály:** Vytvořte standardní operační postupy kolem použití vyhrazených účtů pro správu. Pomocí Azure Security Center správy identit a přístupu můžete monitorovat počet účtů pro správu.

Kromě toho můžete použít doporučení z Azure Security Center nebo integrovaných zásad Azure, jako je například:

- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

- [Použití Azure Security Center k monitorování identity a přístupu (Preview)](../security-center/security-center-identity-access.md)  
- [Jak používat Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Role Azure DevTest Labs](devtest-lab-add-devtest-user.md)  

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory
**Doprovodné materiály:** DevTest Labs používá službu Azure AD pro správu identit. Tyto dvě klíčové aspekty zvažte, když uživatelům udělíte přístup k prostředí založenému na DevTest Labs:

- **Správa prostředků:** Poskytuje přístup k Azure Portal ke správě prostředků (vytváření virtuálních počítačů, vytváření prostředí, spouštění, zastavování, restartování, odstraňování a použití artefaktů atd.). Správa prostředků se provádí v Azure pomocí řízení přístupu na základě role Azure (RBAC). Role přiřadíte uživatelům a nastavíte oprávnění na úrovni prostředků a přístupu.
- **Virtuální počítače (na úrovni sítě)**: ve výchozí konfiguraci virtuální počítače používají účet místního správce. Pokud je k dispozici doména (Azure AD Domain Services, místní doména nebo cloudová doména), můžete počítače připojit k doméně. Uživatelé pak můžou pomocí artefaktu připojení k doméně připojit k počítačům své identity založené na doméně. 

- [Referenční architektura pro DevTest Labs](devtest-lab-reference-architecture.md#architecture)
- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory
**Doprovodné materiály:** Povolte Azure Active Directory (AD) Multi-Factor Authentication (MFA) a sledujte Azure Security Center doporučení pro správu identit a přístupu.

- [Povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)  
- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování Azure Security Center:** _ Ano

_ *Zodpovědnost:** zákazník


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy
**Doprovodné materiály:** Použití pracovních stanic s privilegovaným přístupem (privilegovaným přístupem) s MFA nakonfigurovaným pro přihlášení a konfiguraci prostředků Azure.

- [Další informace o pracovních stanicích s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/privileged-access-workstations)  
- [Povolení vícefaktorového ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)  

**Monitorování Azure Security Center:** NENÍ K DISPOZICI

**Zodpovědnost:** Zákazníka

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: protokolování a upozornění na podezřelou aktivitu z účtů pro správu
**Doprovodné materiály:** Sestavy zabezpečení Azure Active Directory (Azure AD) můžete použít pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)  
- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)  

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění
**Doprovodné materiály:** Pomocí pojmenovaných umístění podmíněného přístupu povolíte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.

- [Postup konfigurace pojmenovaných umístění v Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory
**Doprovodné materiály:** Jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.

- [Vytvoření a konfigurace instance Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů
**Doprovodné materiály:** Azure Active Directory (Azure AD) poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. Pomocí kontrol přístupu k identitám Azure můžete také efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé.

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/overview-reports.md)  
- [Jak používat recenze Azure identity Access](../active-directory/governance/access-reviews-overview.md)  

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: monitorování pokusů o přístup k deaktivovaným účtům
**Doprovodné materiály:** Máte přístup k Azure Active Directory (Azure AD) přihlášení k aktivitám, auditům a zdrojům protokolu událostí, které vám umožní integrovat s jakýmkoli nástrojem/Monitoring (Security Information and Event Management) (SIEM).

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro Azure Active Directory uživatelských účtů a odesláním protokolů auditu a protokolů přihlášení do pracovního prostoru Log Analytics. Výstrahy můžete konfigurovat v rámci Log Analytics pracovního prostoru.

- [Integrace protokolů aktivit Azure do služby Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu
**Doprovodné materiály:** Pomocí funkcí Azure Active Directory (Azure AD) pro rizika a ochranu identity můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů.

- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)  
- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.
**Doprovodné materiály:** Customer Lockbox aktuálně není pro Azure DevTest Labs podporovaná.

- [Seznam podporovaných služeb Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

## <a name="data-protection"></a>Ochrana dat
*Další informace najdete v tématu [řízení zabezpečení: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací
**Doprovodné materiály:** Pomocí značek můžete posloužit ke sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.
**Doprovodné materiály:** Implementujte samostatné odběry nebo skupiny pro správu pro vývoj, testování a produkci. Instance Azure DevTest Labs by se měly oddělit pomocí virtuální sítě/podsítě a vhodně se označit příznakem. 

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)
- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)
- [Jak nakonfigurovat virtuální síť pro DevTest Labs](devtest-lab-configure-vnet.md)
- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)
- [Vytváření a používání značek pro DevTest Labs](devtest-lab-add-tag.md)

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací
**Doprovodné materiály:** Ještě není k dispozici; pro Azure DevTest Labs ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací.

Společnost Microsoft spravuje základní infrastrukturu pro Azure DevTest Labs a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Sdíleného

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu
**Doprovodné materiály:** Azure DevTest Labs vyžaduje ve výchozím nastavení komunikaci šifrovanou pomocí protokolu TLS. V tuto chvíli se podporuje TLS verze 1,2. Pokud vaše Klientská knihovna nebo nástroj nepodporuje protokol TLS, je možné povolit nešifrovaná připojení pomocí Azure Portal nebo rozhraní API pro správu. V takových případech, kde nejsou šifrovaná připojení možná, se doporučuje umístit testovací prostředí a klientské aplikace do virtuální sítě.

[Vysvětlení šifrování ve scénáři přenosu pro DevTest Labs](https://techcommunity.microsoft.com/t5/azure-developer-community-blog/azure-devtest-labs-enforcing-tls-1-2-starting-may-01-2020/ba-p/1236279)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Sdíleného

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.
**Doprovodné materiály:** Pro Azure DevTest Labs ještě nejsou dostupné funkce pro identifikaci, klasifikaci a ochranu před únikem informací. Instance značek obsahující citlivé údaje jako takové a implementují řešení třetích stran, pokud je to nutné pro účely dodržování předpisů.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center:** Momentálně není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: k řízení přístupu k prostředkům použijte službu Azure RBAC.
**Doprovodné materiály:** Pro řízení přístupu k testovacím prostředím v Azure DevTest Labs použijte řízení přístupu na základě role Azure (Azure RBAC).

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/role-assignments-portal.md)
- [Pochopení rolí v DevTest Labs](devtest-lab-add-devtest-user.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu
**Doprovodné materiály:** Pokud je to nutné pro dodržování předpisů u výpočetních prostředků vytvořených jako součást DevTest Labs, implementujte nástroj třetí strany, například řešení ochrany před únikem informací na základě automatizovaného hostitele, abyste vynutili řízení přístupu k datům i v případě, že se data zkopírují mimo systém.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Nelze použít

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění
**Doprovodné materiály:** Azure DevTest Labs ukládá následující zákaznická data:

- [Výsledky artefaktů](add-artifact-vm.md) , které zahrnují protokoly nasazení a rozšíření vygenerované při použití artefaktů
- [Dokumenty receptury](devtest-lab-manage-formulas.md) , které se používají k vytvoření virtuálních počítačů ze vzorců
- Operační systém a datové disky pro virtuální počítače testovacího prostředí 

Výsledky artefaktů a dokumenty vzorců se odesílají do účtu Azure Storage, který je vytvořený jako součást každého nasazení testovacího prostředí. Data v Azure Storage jsou šifrována a dešifrována transparentně pomocí 256 šifrování AES, je k dispozici jedna z nejúčinnějších šifrovacích šifr a je kompatibilní se standardem FIPS 140-2. Šifrování Azure Storage nelze zakázat. Pro šifrování účtu úložiště můžete spoléhat na klíče spravované Microsoftem, nebo můžete spravovat šifrování pomocí vlastních klíčů. Další informace najdete v tématu [šifrování pro účet úložiště testovacího prostředí](encrypt-storage.md).

Ve výchozím nastavení jsou všechny operační systémy a datové disky testovacího prostředí šifrovány pomocí klíče spravovaného platformou. Všechny spravované disky, snímky, image a data zapsaná na stávající spravované disky se automaticky zašifrují bez použití klíčů spravovaných platformou. Jako vlastník testovacího prostředí můžete nakonfigurovat disky s operačním systémem testovacího prostředí pro šifrování pomocí spravovaného klíče zákazníka. Šifrování pomocí spravovaného klíče zákazníka pro datové disky testovacího prostředí se v současné době nedají konfigurovat prostřednictvím samotného testovacího prostředí. Správce předplatného ale může nakonfigurovat toto nastavení pro disky testovacího prostředí v rámci předplatného teď. Další informace najdete v tématu [šifrování disků s operačním systémem laboratoře DevTest Labs pomocí zákaznických spravovaných klíčů](encrypt-disks-customer-managed-keys.md).

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Sdíleného

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure
**Doprovodné materiály:** Pomocí Azure Monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny vznikne na instance DevTest Labs a další důležité nebo související prostředky.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)
- [Vytvoření upozornění pro události protokolu aktivit DevTest Labs](create-alerts.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka



## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení
*Další informace najdete v tématu [řízení zabezpečení: Správa ohrožení](../security/benchmarks/security-control-vulnerability-management.md)zabezpečení.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení
**Doprovodné materiály:** Při zabezpečení instancí Azure DevTest Labs a souvisejících prostředků použijte doporučení od Azure Security Center.

Microsoft provádí správu ohrožení zabezpečení na podkladových zdrojích, které podporují Azure DevTest Labs.

- [Pochopení Azure Security Center doporučení](../security-center/recommendations-reference.md) 

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Sdíleného

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému
**Doprovodné materiály:** Pomocí Azure Update Management zajistěte, aby byly na virtuálních počítačích s Windows a Linux hostovaných v rámci DevTest Labs nainstalované nejnovější aktualizace zabezpečení. U virtuálních počítačů s Windows ověřte, že je povolená možnost web Windows Update a že se nastaví automatické aktualizace. Toto nastavení není aktuálně k dispozici pro konfiguraci prostřednictvím DevTest Labs, ale správce testovacího prostředí/Správce předplatného může nakonfigurovat toto nastavení u základních výpočetních virtuálních počítačů ve svém předplatném. 

- [Postup konfigurace Update Management pro virtuální počítače v Azure](../automation/update-management/overview.md)
- [Porozumění zásadám zabezpečení Azure monitorovaným Security Center](../security-center/policy-reference.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: nasazení automatizovaného řešení pro správu oprav softwaru třetí strany
**_Doprovodné materiály:_* _ jako správce testovacího prostředí můžete použít [artefakty DevTest Labs](add-artifact-vm.md) k automatizaci aktualizací vlastních imagí testovacího prostředí, včetně oprav zabezpečení a dalších aktualizací. 

Přečtěte si další informace o [objektu pro vytváření imagí DevTest Labs](image-factory-create.md), což je řešení pro konfiguraci s kódováním, které vytváří a distribuuje image automaticky v pravidelných intervalech se všemi požadovanými konfiguracemi. 

Jako správce předplatného můžete také pomocí řešení Azure Update Management spravovat aktualizace a opravy pro virtuální počítače DevTest Labs. Update Management spoléhá na místně nakonfigurované úložiště aktualizací, které opraví podporované systémy Windows. Nástroje, jako je System Center Updates Publisher (Updates Publisher), umožňují publikovat vlastní aktualizace do Windows Server Update Services (WSUS). Tento scénář umožňuje Update Management opravit počítače, které používají Configuration Manager jako úložiště aktualizací se softwarem třetích stran.

- [Řešení Update Management v Azure](../automation/update-management/overview.md)
- [Správa aktualizací a oprav pro virtuální počítače](../automation/update-management/overview.md)

_ *Azure Security Center monitorování:** nelze použít

**Zodpovědnost:** Zákazníka

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back
**Doprovodné materiály:** Exportovat výsledky kontroly v konzistentních intervalech a porovnat výsledky a ověřit, zda byly chyby zabezpečení opraveny. Při použití doporučení správy ohrožení zabezpečení navrhovaného Azure Security Center se může zákazník na portálu vybraného řešení překlopit a zobrazit historická data kontroly.

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení
**Doprovodné materiály:** Použijte výchozí hodnocení rizik (zabezpečené skóre) poskytované Azure Security Center.

- [Pochopení Azure Security Center zabezpečeného skóre](../security-center/secure-score-security-controls.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv
*Další informace najdete v tématu [řízení zabezpečení: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků
**Doprovodné materiály:** Azure Resource Graph slouží k dotazování a zjišťování všech prostředků (včetně prostředků DevTest Labs) v rámci vašich předplatných. Ujistěte se, že máte ve svém tenantovi příslušná oprávnění (pro čtení) a můžete v rámci předplatných vytvořit výčet všech předplatných a prostředků Azure.

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)
- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu
**Doprovodné materiály:** Použijte značky pro prostředky Azure poskytující metadata k logickému uspořádání v závislosti na taxonomii.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)
- [Konfigurace značek pro DevTest Labs](devtest-lab-add-tag.md)

**Monitorování Azure Security Center:** Není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure
**Doprovodné materiály:** Používejte označení, skupiny pro správu a samostatné odběry, a v případě potřeby samostatné laboratoře, k organizování a sledování laboratorních cvičení a prostředků souvisejících s testovacím prostředím. Proveďte pravidelné sjednocení inventáře a zajistěte rychlou odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)
- [Postup vytvoření Skupiny pro správu](../governance/management-groups/create-management-group-portal.md)
- [Postup vytvoření testovacího prostředí pomocí DevTest Labs](devtest-lab-create-lab.md)
- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)
- [Postup konfigurace značek pro testovací prostředí](devtest-lab-add-tag.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure
**Doprovodné materiály:** Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb organizace. Jako správce předplatného můžete také použít adaptivní řízení aplikací, což je funkce Azure Security Center, která vám umožní definovat sadu aplikací, které se můžou spouštět v konfigurovaných skupinách testovacích počítačů. Tato funkce je k dispozici pro Azure i mimo Azure Windows (všechny verze, Classic nebo Azure Resource Manager) a počítače se systémem Linux.

- [Jak povolit Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)
 
**Monitorování Azure Security Center:** Nerelevantní **odpovědnost:** zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure
**Doprovodné materiály:** Pomocí služby Azure Policy můžete nastavovat omezení pro typ prostředků, které se dají vytvořit v předplatných zákazníků, a to pomocí těchto integrovaných definic zásad:

- Žádné povolené typy prostředků
- Povolené typy prostředků

K dotazování a zjišťování prostředků v rámci předplatných také použijte graf Azure Resource. Může pomáhat v prostředích s vysokým zabezpečením, jako jsou ty, které využívají účty úložiště.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.
**Doprovodné materiály:** Azure Automation poskytuje úplnou kontrolu nad nasazením, provozem a vyřazením úloh a prostředků z provozu. Jako správce předplatného můžete pomocí inventáře virtuálních počítačů Azure automatizovat shromažďování informací o veškerém softwaru na virtuálních počítačích DevTest Labs v rámci vašeho předplatného. Z Azure Portal jsou k dispozici vlastnosti pro název softwaru, verze, Vydavatel a čas aktualizace. Aby bylo možné získat přístup k datu instalace a dalším informacím, musí zákazník vyžadovat diagnostiku na úrovni hosta a přenést protokoly událostí systému Windows do Log Analytics pracovního prostoru.

Kromě použití Change Tracking ke sledování softwarových aplikací, adaptivní řízení aplikací v Azure Security Center pomocí strojového učení můžete analyzovat aplikace běžící na vašich počítačích a vytvořit z tohoto inteligentního seznamu povolený seznam. Tato funkce značně zjednodušuje proces konfigurace a správy zásad seznamu povolených aplikací a umožňuje vyhnout se nechtěnému softwaru ve vašem prostředí. Můžete nakonfigurovat režim auditování nebo režim vymáhání. Režim auditování Audituje pouze aktivitu na chráněných virtuálních počítačích. Režim vynutilosti vynutil pravidla a zajišťuje, aby aplikace, které nejsou povolené ke spuštění, byly blokované. 

- [Seznámení se službou Azure Automation](../automation/automation-intro.md)
- [Jak povolit inventář virtuálních počítačů Azure](../automation/automation-tutorial-installed-software.md)
- [Principy adaptivních řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace
**Doprovodné materiály:** Azure Automation poskytuje úplnou kontrolu nad nasazením, provozem a vyřazením úloh a prostředků z provozu. Jako správce předplatného můžete použít Change Tracking k identifikaci veškerého softwaru nainstalovaného na virtuálních počítačích hostovaných v DevTest Labs. Můžete implementovat vlastní proces nebo použít konfiguraci Azure Automation stav pro odebrání neautorizovaného softwaru.

- [Seznámení se službou Azure Automation](../automation/automation-intro.md)
- [Sledování změn ve vašem prostředí pomocí Change Tracking řešení](../automation/change-tracking/overview.md)
- [Přehled konfigurace stavu Azure Automation](../automation/automation-dsc-overview.md)

**Monitorování Azure Security Center:** Není k dispozici

**Zodpovědnost:** Zákazníka

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.
**Doprovodné materiály:** Jako správce předplatného můžete použít Azure Security Center Adaptivní řízení aplikací, abyste zajistili, že se spustí jenom autorizovaný software, a veškerý neautorizovaný software se zablokuje spouštění na virtuálních počítačích Azure hostovaných v DevTest Labs.

- [Jak používat Azure Security Center Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.
**Doprovodné materiály:** Pomocí služby Azure Policy můžete nastavovat omezení pro typ prostředků, které se dají vytvořit v předplatných zákazníků, a to pomocí těchto integrovaných definic zásad: 

- Žádné povolené typy prostředků
- Povolené typy prostředků

Viz následující články: 
- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../governance/policy/samples/index.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů
**Doprovodné materiály:** Adaptivní řízení aplikací je inteligentní, automatizované a ucelené řešení z Azure Security Center, které vám pomůže určit, které aplikace se můžou spouštět na počítačích Azure a mimo Azure (Windows a Linux) hostované v DevTest Labs. Poznámka: abyste mohli nakonfigurovat toto nastavení pro základní výpočetní prostředky hostované v DevTest Labs, musíte být správcem předplatného. Implementujte řešení třetích stran, pokud toto nastavení nesplňuje požadavek vaší organizace.

- [Jak používat Azure Security Center Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager
**Doprovodné materiály:** Pomocí podmíněného přístupu Azure můžete omezit schopnost uživatelů komunikovat s Azure Resource Manager konfigurací **blokování přístupu*** * pro aplikaci **Microsoft Azure Management** .

- [Postup konfigurace podmíněného přístupu pro blokování přístupu k Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.
**Doprovodné materiály:** V závislosti na typu skriptů můžete pomocí konfigurací specifických pro operační systém nebo prostředků třetích stran omezit schopnost uživatelů spouštět skripty v rámci virtuálních počítačů hostovaných v DevTest Labs. Můžete také použít Azure Security Center Adaptivní řízení aplikací, abyste zajistili, že se spustí jenom autorizovaný software, a veškerý neoprávněný software se zablokuje spouštění na podkladových virtuálních počítačích Azure.

- [Řízení spouštění skriptu PowerShellu v prostředích Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [Jak používat Azure Security Center Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování Azure Security Center:** Není k dispozici

**Zodpovědnost:** Zákazníka


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem
**Doprovodné materiály:** Vysoce rizikové aplikace nasazené ve vašem prostředí Azure můžou být izolované pomocí virtuální sítě, podsítě, předplatných, skupin pro správu a tak dále. a dostatečně zabezpečená buď s Azure Firewall, firewallem webových aplikací (WAF) nebo skupinou zabezpečení sítě (NSG).

- [Konfigurace služby Virtual Network pro DevTest Labs](devtest-lab-configure-vnet.md)
- [Přehled Azure Firewall](../firewall/overview.md)
- [Přehled Firewallu webových aplikací](../web-application-firewall/overview.md)
- [Přehled zabezpečení sítě](../virtual-network/network-security-groups-overview.md)
- [Přehled služby Azure Virtual Network]()
- [Uspořádání prostředků s využitím skupin pro správu Azure](../governance/management-groups/overview.md)
- [Průvodce rozhodováním ohledně předplatného](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitorování Azure Security Center:** Není k dispozici

**Zodpovědnost:** Zákazníka

## <a name="secure-configuration"></a>Zabezpečená konfigurace
**Další informace najdete v tématu řízení zabezpečení: zabezpečená konfigurace.**

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure
**Doprovodné materiály:** Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace prostředků Azure vytvořených v rámci DevTest Labs. Můžete také použít předdefinované definice Azure Policy.

Azure Resource Manager taky umožňuje exportovat šablonu do JavaScript Object Notation (JSON), která by se měla zkontrolovat, aby se zajistilo, že konfigurace splňují nebo překračují požadavky zabezpečení vaší organizace.

Pro vaše prostředky Azure můžete také použít doporučení z Azure Security Center jako standardní hodnoty konfigurace zabezpečení.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)
- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)
- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)
- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému
**Doprovodné materiály:** Použijte Azure Security Center doporučení k údržbě konfigurací zabezpečení u všech základních výpočetních prostředků vytvořených jako součást laboratoře DevTest Labs. Kromě toho můžete k vytvoření konfigurace zabezpečení operačního systému vyžadovaného vaší organizací použít vlastní image operačního systému nebo konfigurace stavu Azure Automation nebo artefakty DevTest Labs.

- [Jak monitorovat Azure Security Center doporučení](../security-center/security-center-recommendations.md)
- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)
- [Přehled konfigurace stavu Azure Automation](../automation/automation-dsc-overview.md)
- [Nahrání virtuálního pevného disku a jeho použití k vytvoření nových virtuálních počítačů s Windows v Azure](../virtual-machines/windows/upload-generalized-managed.md)
- [Vytvoření virtuálního počítače se systémem Linux z vlastního disku pomocí Azure CLI](../virtual-machines/linux/upload-vhd.md)
- [Vytváření a distribuce vlastních imagí do několika DevTest Labs](image-factory-save-distribute-custom-images.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Nelze použít

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure
**Doprovodné materiály:** **Pokud neexistují** pravidla pro vymáhání zabezpečených nastavení napříč prostředky Azure vytvořenými jako součást DevTest Labs, použijte Azure Policy **Odepřít** a nasadit. Můžete také použít šablony Azure Resource Manager k údržbě konfigurace zabezpečení vašich prostředků Azure, které vaše organizace vyžaduje.

- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)
- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)
- [Přehled šablon Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému
**Doprovodné materiály:** Dodržujte doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení vašich základních výpočetních prostředků Azure vytvořených v rámci testovacího prostředí. Kromě toho můžete použít šablony Azure Resource Manager, vlastní image operačního systému nebo konfiguraci stavu Azure Automation, abyste zachovali konfiguraci zabezpečení operačního systému, který vyžaduje vaše organizace. Můžete také použít řešení pro vytváření imagí, což je řešení konfigurace se stejným kódem, které sestaví a distribuuje image automaticky v pravidelných intervalech se všemi požadovanými konfiguracemi.

Azure Marketplace image virtuálních počítačů publikované Microsoftem jsou taky spravované a udržované Microsoftem.

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)
- [Přehled konfigurace stavu Azure Automation](../automation/automation-dsc-overview.md)
- [Ukázkový skript pro nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)
- [Postup vytvoření objektu pro vytváření imagí v DevTest Labs](image-factory-create.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Sdíleného

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti
**Doprovodné materiály:** Využijte Azure DevOps k bezpečnému ukládání a správě kódu, jako jsou vlastní zásady Azure, Azure Resource Manager šablony a požadované konfigurační skripty stavu. Pokud chcete získat přístup k prostředkům, které spravujete v Azure DevOps, můžete udělit nebo odepřít oprávnění konkrétním uživatelům, vestavěným skupinám zabezpečení nebo skupinám definovaným v Azure Active Directory (Azure AD), pokud jsou integrované s Azure DevOps.

- [Kurz k Azure Repos Git](/azure/devops/repos/git/gitworkflow)
- [O oprávněních a skupinách](/azure/devops/organizations/security/about-permissions?tabs=preview-page&view=azure-devops)
- [Integrace mezi Azure DevTest Labs a pracovním postupem Azure DevOps](devtest-lab-dev-ops.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému
**Doprovodné materiály:** Pokud používáte vlastní image, použijte řízení přístupu na základě role Azure (Azure RBAC), abyste zajistili přístup k imagí jenom autorizovaným uživatelům. Pomocí Galerie sdílených imagí můžete sdílet své image s konkrétními laboratořemi, které ji potřebují. V případě imagí kontejnerů je uložte do Azure Container Registry a pomocí Azure RBAC zajistěte, aby k nim měli přístup jenom autorizovaní uživatelé.

- [Pochopení Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)
- [Konfigurace Galerie sdílených imagí pro DevTest Labs](configure-shared-image-gallery.md)
- [Vysvětlení služby Azure RBAC pro Container Registry](../container-registry/container-registry-roles.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: nasazení nástrojů pro správu konfigurace systému
**Doprovodné materiály:** Definování a implementace standardních konfigurací zabezpečení pro prostředky Azure pomocí Azure Policy. Pomocí aliasů Azure Policy můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace sítě vašich prostředků Azure vytvořených v DevTest Labs. Můžete také využít integrované definice zásad související s vašimi konkrétními prostředky. Kromě toho můžete k nasazení změn konfigurace použít Azure Automation.

- [Konfigurace a Správa Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Jak používat aliasy](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: nasaďte nástroje pro správu konfigurace systému pro operační systémy.
**Doprovodné materiály:** Konfigurace stavu Azure Automation je služba správy konfigurace pro požadované uzly Konfigurace stavu (DSC) v jakémkoli cloudu nebo v místním datacentru. Můžete snadno připojit počítače, přiřazovat je k deklarativním konfiguracím a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který jste zadali. Můžete také napsat vlastní artefakt, který se dá nainstalovat na každý testovací počítač, abyste měli jistotu, že dodržují zásady organizace. 

- [Připojování počítačů pro správu podle konfigurace stavu Azure Automation](../automation/automation-dsc-onboarding.md)
- [Vytváření vlastních artefaktů pro virtuální počítače DevTest Labs](devtest-lab-artifact-author.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Implementujte automatizované monitorování konfigurace pro služby Azure.
**Doprovodné materiály:** Použijte Azure Security Center k provádění kontrol standardních hodnot vašich prostředků Azure vytvořených v rámci DevTest Labs. Kromě toho použijte Azure Policy k upozornění a auditování konfigurace prostředků Azure.

- [Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)
 
**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy
**Doprovodné materiály:** Pomocí Azure Security Center můžete provádět kontroly základní hodnoty pro kontejnery a nastavení Docker.

- [Porozumění doporučení týkajících se kontejnerů Azure Security Center](../security-center/container-security.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure
**Doprovodné materiály:** K zjednodušení a zabezpečení správy tajných kódů pro cloudové aplikace můžete použít Identita spravované služby ve spojení s Azure Key Vault.

- [Konfigurace spravované identity pro nasazení Azure Resource Manager prostředí v DevTest Labs](use-managed-identities-environments.md)
- [Konfigurace spravované identity pro nasazení virtuálních počítačů v DevTest Labs](enable-managed-identities-lab-vms.md)
- [Vytvoření trezoru klíčů](../key-vault/general/quick-create-portal.md)
- [Ověření Key Vault](../key-vault/general/authentication.md)
- [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity
**Doprovodné materiály:** Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů ve vašem kódu.

- [Konfigurace spravované identity pro nasazení Azure Resource Manager prostředí v DevTest Labs](use-managed-identities-environments.md)
- [Konfigurace spravované identity pro nasazení virtuálních počítačů v DevTest Labs](enable-managed-identities-lab-vms.md)
 
**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů
**Doprovodné materiály:** Implementujte kontrolu přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Skener přihlašovacích údajů taky bude povzbudit přesunutí zjištěných přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault.

- Jak nastavit skener přihlašovacích údajů

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka


## <a name="malware-defense"></a>Obrana před malwarem
*Další informace najdete v tématu řízení zabezpečení: obrana proti malwaru.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: použití centrálně spravovaného malwarového softwaru
**Doprovodné materiály:** Využijte Microsoft Antimalware pro Azure Cloud Services a Virtual Machines nepřetržitě monitorujte a ochraňujte své prostředky. Pro Linux použijte antimalwarové řešení třetích stran. K detekci malwaru nahraného do účtů úložiště taky můžete použít detekci hrozeb Azure Security Center pro datové služby.

- Jak nakonfigurovat Microsoft Antimalware pro Cloud Services a Virtual Machines
- Ochrana před hrozbami v Azure Security Center

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE
**Doprovodné materiály:** Microsoft Antimalware je povolený na podkladovém hostiteli, který podporuje služby Azure (například Azure App Service hostované v testovacím prostředí), ale neběží na vašem obsahu.
Předem Prohledejte všechny soubory nahrané do nevýpočetních prostředků Azure, jako jsou App Service, Data Lake Storage, Blob Storage a tak dále.

K detekci malwaru nahraného do účtů úložiště použijte detekci hrozeb Azure Security Center pro datové služby.

- Pochopení Microsoft antimalwaru pro Azure Cloud Services a Virtual Machines
- Vysvětlení detekce hrozeb Azure Security Center pro datové služby

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Nelze použít


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované.
**Doprovodné materiály:** Po nasazení bude Microsoft Antimalware pro Azure ve výchozím nastavení automaticky instalovat nejnovější signatury, platformy a aktualizace stroje. Použijte doporučení v Azure Security Center: "COMPUTE & aplikace", abyste zajistili aktuálnost všech koncových bodů pro základní výpočetní prostředky DevTest Labs s nejnovějšími podpisy. OPERAČNÍ systém Windows je možné dále chránit pomocí dalšího zabezpečení a omezit tak riziko virů nebo malwarových útoků pomocí služby Microsoft Defender Advanced Threat Protection, která se integruje s Azure Security Center.

- Jak nasadit Microsoft Antimalware pro Azure Cloud Services a Virtual Machines
- Rozšířená ochrana před internetovými útoky v programu Microsoft Defender

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

## <a name="data-recovery"></a>Obnovení dat
*Další informace najdete v tématu [řízení zabezpečení – obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování
**Doprovodné materiály:** V současné době Azure DevTest Labs nepodporuje zálohování virtuálních počítačů a snímky. Můžete ale povolit a nakonfigurovat Azure Backup na podkladových virtuálních počítačích Azure hostovaných v DevTest Labs. A můžete také nakonfigurovat požadovanou četnost a dobu uchování pro automatické zálohování, pokud máte odpovídající přístup k základním výpočetním prostředkům. 

- [Přehled zálohování virtuálních počítačů Azure](../backup/backup-azure-vms-introduction.md)
- [Zálohování virtuálního počítače Azure z nastavení virtuálního počítače](../backup/backup-azure-vms-first-look-arm.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.
**Doprovodné materiály:** V současné době Azure DevTest Labs nepodporuje zálohování virtuálních počítačů a snímky. Můžete ale vytvořit snímky z vašich základních virtuálních počítačů Azure hostovaných v DevTest Labs nebo spravované disky připojené k těmto instancím pomocí prostředí PowerShell nebo rozhraní REST API, pokud máte odpovídající přístup k základním výpočetním prostředkům. Můžete také zálohovat libovolné klíče spravované zákazníkem v rámci Azure Key Vault.

Povolte Azure Backup na cílových virtuálních počítačích Azure a požadovanou četnost a dobu uchování. Zahrnuje kompletní zálohu stavu systému. Pokud používáte Azure Disk Encryption, zálohování virtuálních počítačů Azure automaticky zpracovává zálohu klíčů spravovaných zákazníkem.

- [Zálohování virtuálních počítačů Azure, které používají šifrování](../backup/backup-azure-vms-encryption.md)
- [Přehled zálohování virtuálních počítačů Azure](../backup/backup-azure-vms-introduction.md)
- [Přehled zálohování virtuálních počítačů Azure](../backup/backup-azure-vms-introduction.md)
- [Postup zálohování klíčů Key Vault v Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem
**Doprovodné materiály:** Zajistěte, aby možnost pravidelně prováděla obnovování obsahu v rámci Azure Backup. V případě potřeby otestujte obnovení obsahu do izolované virtuální sítě nebo předplatného. Také testovací obnovení zálohovaných klíčů spravovaných zákazníkem.

Pokud používáte Azure Disk Encryption, můžete virtuální počítač Azure obnovit pomocí klíčů pro šifrování disku. Při použití šifrování disku můžete virtuální počítač Azure obnovit pomocí klíčů pro šifrování disku.

- [Zálohování virtuálních počítačů Azure, které používají šifrování](../backup/backup-azure-vms-encryption.md)
- [Postup obnovení souborů ze zálohy virtuálního počítače Azure](../backup/backup-azure-restore-files-from-vm.md)
- [Postup obnovení klíčů trezoru klíčů v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [Jak zálohovat a obnovit zašifrovaný virtuální počítač](../backup/backup-azure-vms-encryption.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem
**Doprovodné materiály:** Když zálohujete spravované disky pomocí Azure Backup, virtuální počítače se zašifrují v klidovém stavu pomocí Šifrování služby Storage (SSE). Azure Backup taky můžou zálohovat virtuální počítače Azure, které jsou zašifrované pomocí Azure Disk Encryption. Azure Disk Encryption se integruje s šifrovacími klíči BitLockeru (BEKs), které jsou v trezoru klíčů zabezpečené jako tajné klíče. Azure Disk Encryption se taky integruje s klíči šifrovacího klíče Azure Key Vault (KEK). Povolí Soft-Delete v Key Vault k ochraně klíčů proti náhodnému nebo škodlivému odstranění.

- [Obnovitelné odstranění pro virtuální počítače](../backup/soft-delete-virtual-machines.md)
- [Přehled Azure Key Vault-Soft-odstranění](../key-vault/general/soft-delete-overview.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

## <a name="incident-response"></a>Reakce na incidenty
*Další informace najdete v tématu [řízení zabezpečení: reakce na incidenty](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty
**Doprovodné materiály:** Vytvořte si Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků a fází zpracování a správy incidentů z detekce až po kontrolu incidentu.

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [Pomoc při tvorbě vlastního plánu odpovědí na incidenty najdete v příručce pro zpracování incidentů v počítači s NIST.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit
**Doprovodné materiály:** Azure Security Center přiřadí každému upozornění závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se Security Center ve vyhledávání, nebo na analýze, která se používá k vystavení výstrahy, a také na úrovni spolehlivosti, u které došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Kromě toho jasně označte odběry (pro např. Výroba, nevýrobní zakázka pomocí značek a vytvoření názvového systému pro zřetelné identifikaci a kategorizaci prostředků Azure, zejména těch, která zpracovávají citlivá data. Je vaší zodpovědností určit prioritu nápravy upozornění v závislosti na důležitosti prostředků Azure a prostředí, ve kterém k incidentu došlo.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)
- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center:** Ano

**Zodpovědnost:** Zákazníka

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení
**Doprovodné materiály:** Provádějte cvičení a otestujte možnosti reakce na incidenty v pravidelných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a podle potřeby upravte plán.

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení
**Doprovodné materiály:** Kontaktní údaje incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba MSRC (Microsoft Security Response Center) zjistí, že vaše data jsou přístupná ze strany, která není protiprávní nebo oprávněná. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.
**Doprovodné materiály:** Vyexportujte výstrahy a doporučení Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)
- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Zákazníka

#### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení
**Doprovodné materiály:** Použijte funkci automatizace pracovních postupů v Azure Security Center k automatickému spouštění odpovědí prostřednictvím "Logic Apps" pro výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

- [Jak nakonfigurovat automatizaci pracovních postupů a Logic Apps](../security-center/workflow-automation.md)
 
Monitorování Azure Security Center: * * * * nejde použít.

**Zodpovědnost:** Zákazníka


## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises
*Další informace najdete v tématu řízení zabezpečení: [testy průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech důležitých zjištění zabezpečení do 60 dnů.
**Doprovodné materiály:** Využijte pravidla zapojení Microsoftu, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a provádění etického testování průniku a penetračního testování živých webů proti cloudové infrastruktuře, službám a aplikacím spravovaným Microsoftem.

- [Pravidla testování průniku pro zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Červený tým Microsoftu v cloudu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center:** Nelze použít

**Zodpovědnost:** Sdíleného

## <a name="next-steps"></a>Další kroky
Přečtěte si následující článek:

- [Výstrahy zabezpečení pro prostředí v Azure DevTest Labs](environment-security-alerts.md)