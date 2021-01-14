---
title: Základní hodnoty zabezpečení Azure pro Azure Machine Learning
description: Základní Azure Machine Learning zabezpečení poskytuje postupy a prostředky pro implementaci doporučení zabezpečení uvedených v srovnávacím testu zabezpečení Azure.
author: msmbaldwin
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 380aa826030df14b4b88b86ac2962c988796c0f9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202636"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Základní hodnoty zabezpečení Azure pro Azure Machine Learning

Základní plán zabezpečení Azure pro Microsoft Azure Machine Learning obsahuje doporučení, která vám pomůžou vylepšit stav zabezpečení vašeho nasazení. Základní hodnota této služby se vykreslí z [bezpečnostního testu Azure Security 1,0](../security/benchmarks/overview.md), který poskytuje doporučení k zabezpečení cloudových řešení v Azure s využitím našich osvědčených postupů. Další informace najdete v tématu [Přehled standardních hodnot zabezpečení Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Zabezpečení sítě

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: zabezpečení sítě](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Ochrana prostředků Azure v rámci virtuálních sítí

**Doprovodné** materiály: Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky. Výpočetní prostředky (cíle výpočtů) se používají ke školení a nasazení modelů. Tyto výpočetní cíle můžete vytvořit ve virtuální síti. Například můžete pomocí výpočetní instance Azure Virtual Machine Learning vytvořit model a potom model nasadit do služby Azure Kubernetes Service (AKS). Životní cyklus strojového učení můžete zabezpečit tak, že izolujete Azure Machine Learning školení a odvozování úloh v rámci služby Azure Virtual Network.

Azure Firewall lze použít k řízení přístupu k pracovnímu prostoru Azure Machine Learning a k veřejnému Internetu.

- [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md)

- [Použít pracovní prostor za Azure Firewall Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: Sledujte a protokolujte konfiguraci a provoz virtuálních sítí, podsítí a síťových karet

**Doprovodné** materiály: Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky. Přiřaďte skupiny zabezpečení sítě k sítím, které jsou vytvořeny jako nasazení Machine Learning. 

Povolte protokolování toku skupin zabezpečení sítě a odešlete protokoly do účtu Azure Storage k auditování. Protokoly toku můžete také odeslat do pracovního prostoru Log Analytics a potom pomocí Analýza provozu poskytnout přehled o vzorech přenosu v cloudu Azure. Mezi výhody Analýza provozu patří schopnost vizualizovat síťovou aktivitu, identifikovat aktivní body a bezpečnostní hrozby, pochopit vzorce toku přenosů a odhalit nesprávné konfigurace sítě.

- [Jak povolit protokoly toku skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Postup povolení a použití Analýza provozu](../network-watcher/traffic-analytics.md)

- [Pochopení zabezpečení sítě, které poskytuje Azure Security Center](../security-center/security-center-network-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="13-protect-critical-web-applications"></a>1,3: Chraňte kritické webové aplikace

**Pokyny**: protokol HTTPS můžete povolit pro zabezpečenou komunikaci s webovými službami nasazenými pomocí Azure Machine Learning. Webové služby se nasazují v Azure Kubernetes Services (AKS) nebo Azure Container Instances (ACI) a zabezpečují data odeslaná klienty. Můžete také použít privátní IP adresu s AKS a omezit tak vyhodnocování, aby přístup k webové službě měli jenom klienti, kteří jsou za virtuální sítí.

- [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)

- [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: zakažte komunikaci se známými škodlivými IP adresami.

**Pokyny**: ve virtuálních sítích, které jsou přidružené k vaší instanci Machine Learning, povolte DDoS Protection Standard, abyste se mohli chránit před distribuovanými útoky DoS (DDoS). K detekci komunikace se známými škodlivými nebo nepoužívanými internetovými IP adresami použijte Azure Security Center integrované zjišťování hrozeb.

Nasaďte Azure Firewall na všech hranicích sítě organizace s povoleným filtrováním na základě hrozeb a nakonfigurovanými na "výstrahy a zamítnutí" pro škodlivý síťový provoz.

- [Jak nakonfigurovat DDoS Protection](../ddos-protection/manage-ddos-protection.md)

- [Použít pracovní prostor za Azure Firewall Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [Další informace o Azure Security Center detekci hrozeb](../security-center/azure-defender.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="15-record-network-packets"></a>1,5: zaznamenání síťových paketů

**Doprovodné** materiály: u všech virtuálních počítačů se správným rozšířením nainstalovaným ve službě Azure Machine Learning Services můžete povolit Network Watcher zachytávání paketů a prozkoumat aktivity neobvyklé. 

- [Postup vytvoření instance Network Watcher](../network-watcher/network-watcher-create.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: nasazení systémů ochrany před internetovými útoky/systémy prevence vniknutí (ID/IP adresy)

**Pokyny**: nasazení řešení brány firewall podle vašeho výběru na základě hranic sítě vaší organizace za účelem detekce a blokování škodlivého provozu.

Vyberte nabídku z Azure Marketplace, která podporuje funkce ID/IP adres s možnostmi kontroly zatížení.  Pokud kontrola datové části není požadavkem, je možné použít Azure Firewall analýzy hrozeb. Azure Firewall filtrování založené na základě logiky hrozeb slouží k upozorňování a zablokování provozu do a ze známých škodlivých IP adres a domén. Zdrojem těchto IP adres a domén je kanál analýzy hrozeb Microsoftu.

- [Postup nasazení Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Postup konfigurace výstrah pomocí Azure Firewall](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="17-manage-traffic-to-web-applications"></a>1,7: Správa provozu do webových aplikací

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: Minimalizujte složitost a administrativní režii pravidel zabezpečení sítě

**Doprovodné** materiály: u prostředků, které potřebují přístup k vašemu Azure Machine Learning účtu, použijte značky služby Virtual Network a definujte řízení přístupu k síti u skupin zabezpečení sítě nebo Azure firewall. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (například AzureMachineLearning) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny.

Služba Azure Machine Learning documentuje seznam značek služeb pro výpočetní cíle ve virtuální síti, která pomáhá minimalizovat složitost, můžete ji použít jako pokyny v rámci správy sítě.

- [Další informace o použití značek služeb](../virtual-network/service-tags-overview.md)

- [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: Udržujte standardní konfigurace zabezpečení pro síťová zařízení.

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro síťové prostředky přidružené k vašim oborům názvů Azure Machine Learning pomocí Azure Policy. Pomocí aliasů Azure Policy v oborech názvů Microsoft. MachineLearning a Microsoft. Network můžete vytvářet vlastní zásady pro auditování nebo vymáhání konfigurace sítě vašich Machine Learning oborů názvů. 

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="110-document-traffic-configuration-rules"></a>1,10: pravidla pro konfiguraci provozu dokumentu

**Doprovodné** materiály: používejte značky pro síťové prostředky přidružené k vašemu nasazení Azure Machine Learning, aby je bylo možné logicky uspořádat podle taxonomie.

Pro prostředek ve vaší virtuální síti Azure Machine Learning, který podporuje pole Popis, ho použijte k dokumentaci pravidel, která umožňují provoz do/ze sítě.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: pomocí automatizovaných nástrojů monitorujte konfigurace síťových prostředků a zjišťují změny.

**Pokyny**: pomocí protokolu aktivit Azure můžete monitorovat konfigurace síťových prostředků a zjišťovat změny síťových prostředků, které souvisejí s Azure Machine Learning. Vytvoří výstrahy v rámci Azure Monitor, které se aktivují, když budou provedeny změny v kritických síťových prostředcích.

- [Jak zobrazit a načíst události protokolu aktivit Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Vytváření výstrah v Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: protokolování a monitorování](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Použijte schválené zdroje synchronizace času

**Pokyny**: Společnost Microsoft udržuje zdroj času používaný pro prostředky Azure, například Azure Machine Learning pro časová razítka v protokolech.

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurace centrální správy protokolů zabezpečení

**Pokyny**: ingestování protokolů prostřednictvím Azure monitor k agregaci dat zabezpečení vygenerovaných Azure Machine Learning. V Azure Monitor použijte pracovní prostory Log Analytics k dotazování a provádění analýz a používejte účty Azure Storage pro dlouhodobé a archivační úložiště. Alternativně můžete povolit a začlenit data do Azure Sentinel nebo incidentu zabezpečení a řízení událostí zabezpečení (SIEM) třetí strany.

- [Postup konfigurace diagnostických protokolů pro Azure Machine Learning](monitor-azure-machine-learning.md#configuration)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: povolení protokolování auditu pro prostředky Azure

**Pokyny**: povolení nastavení diagnostiky v prostředcích Azure pro přístup k protokolům auditu, zabezpečení a diagnostiky. Protokoly aktivit, které jsou automaticky k dispozici, zahrnují zdroj událostí, datum, uživatele, časové razítko, zdrojové adresy, cílové adresy a další užitečné prvky.

Pro účely zabezpečení a dodržování předpisů můžete také korelovat protokoly operací Machine Learning služby.

- [Jak shromažďovat protokoly a metriky platforem pomocí Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Principy protokolování a různých typů protokolů v Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Povolit přihlašování Azure Machine Learning](./how-to-track-experiments.md)

- [Azure Machine Learning monitorování](monitor-azure-machine-learning.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: shromáždění protokolů zabezpečení z operačních systémů

**Doprovodné** materiály: Pokud je výpočetní prostředek vlastněn Microsoftem, zodpovídá za jeho shromažďování a sledování. 

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. Pro všechny výpočetní prostředky, které vlastní vaše organizace, použijte Azure Security Center k monitorování operačního systému. 

- [Jak shromažďovat protokoly interního hostitele virtuálních počítačů Azure pomocí Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

- [Pochopení Azure Security Center shromažďování dat](../security-center/security-center-enable-data-collection.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurace uchovávání úložiště protokolu zabezpečení

**Doprovodné** materiály: v Azure monitor nastavte dobu uchování protokolu pro Log Analytics pracovní prostory, které jsou přidružené k vašim Azure Machine Learning instancí podle předpisů pro dodržování předpisů vaší organizace.

- [Postup nastavení parametrů uchovávání protokolů](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="26-monitor-and-review-logs"></a>2,6: Sledujte a kontrolujte protokoly

**Pokyny**: analýza a sledování protokolů pro neobvyklé chování a pravidelné prohlížení výsledků z Azure Machine Learning. Pomocí Azure Monitor a pracovního prostoru Log Analytics můžete prohlížet protokoly a provádět dotazy na data protokolu.

Případně můžete povolit a začlenit data do Azure Sentinel nebo SIEM třetí strany. 

- [Postup provádění dotazů pro Azure Machine Learning v pracovních prostorech Log Analytics](monitor-azure-machine-learning.md#analyzing-log-data)

- [Povolit přihlašování Azure Machine Learning](./how-to-track-experiments.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Začínáme s Log Analytics dotazy](../azure-monitor/log-query/log-analytics-tutorial.md)

- [Jak provádět vlastní dotazy v Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: povolení výstrah pro aktivity neobvyklé

**Doprovodné** materiály: v Azure monitor Nakonfigurujte protokoly týkající se Azure Machine Learning v protokolu aktivit a Machine Learning nastavení diagnostiky k odesílání protokolů do log Analyticsho pracovního prostoru, který se má dotazovat nebo do účtu úložiště pro dlouhodobé archivace úložiště. Pomocí Log Analytics pracovního prostoru můžete vytvářet výstrahy pro aktivitu neobvyklé nalezené v protokolech zabezpečení a událostech.

Alternativně můžete povolit a začlenit data do Azure Sentinel.

- [Další informace o výstrahách Azure Machine Learning](monitor-azure-machine-learning.md#alerts)

- [Upozornění na data protokolu Log Analytics pracovního prostoru](../azure-monitor/learn/tutorial-response.md)

- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizace protokolování proti malwaru

**Doprovodné** materiály: Pokud je výpočetní prostředek vlastněný společností Microsoft, zodpovídá za to, že společnost Microsoft je zodpovědná za nasazení Azure Machine Learning služby proti malwaru. 

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků vlastněných vaší organizací povolte shromažďování antimalwarových událostí pro Microsoft Antimalware pro Azure Cloud Services a Virtual Machines.

- [Jak nakonfigurovat Microsoft Antimalware pro virtuální počítač](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Jak nakonfigurovat rozšíření Microsoft Antimalware pro Cloud Services](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Pochopení antimalwarového programu Microsoftu](../security/fundamentals/antimalware.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

### <a name="29-enable-dns-query-logging"></a>2,9: povolení protokolování dotazů DNS

**Doprovodné** materiály: nepoužitelné; Azure Machine Learning nezpracovává ani nevytváří protokoly související se službou DNS.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="210-enable-command-line-audit-logging"></a>2,10: povolení protokolování auditu příkazového řádku

**Doprovodné** materiály: Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. Pro výpočetní prostředky vlastněné vaší organizací použijte Azure Security Center k povolení monitorování protokolu událostí zabezpečení pro virtuální počítače Azure. Azure Security Center zřídí agenta Log Analytics na všech podporovaných virtuálních počítačích Azure a všechny nově vytvořené, pokud je povolené Automatické zřizování. Nebo můžete agenta nainstalovat ručně. Agent umožňuje událost vytvoření procesu 4688 a pole CommandLine v události 4688. Nové procesy vytvořené na virtuálním počítači jsou zaznamenané protokolem událostí a monitorují služby zjišťování Security Center.

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

## <a name="identity-and-access-control"></a>Identita a řízení přístupu

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: identita a řízení přístupu](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: udržování inventáře účtů pro správu

**Doprovodné** materiály: kartu Správa identit a přístupu k prostředku v Azure Portal můžete použít ke konfiguraci řízení přístupu na základě role Azure (Azure RBAC) a údržbě inventáře na Azure Machine Learningch prostředcích. Role se aplikují na uživatele, skupiny, instanční objekty a spravované identity ve službě Active Directory. Pro jednotlivce a skupiny můžete použít předdefinované role nebo vlastní role.

Azure Machine Learning poskytuje předdefinované role pro běžné scénáře správy v Azure Machine Learning. Jednotlivec, který má profil v Azure Active Directory (Azure AD), může přiřadit tyto role uživatelům, skupinám, instančním objektům nebo spravovaným identitám udělit nebo odepřít přístup k prostředkům a operacím s prostředky Azure Machine Learning.

K vyhledání účtů, které jsou členy skupin pro správu, můžete také použít modul Azure AD PowerShell k provádění dotazů ad hoc.

- [Pochopení řízení přístupu na základě role v Azure v Azure Machine Learning](how-to-assign-roles.md)

- [Postup získání role adresáře v Azure Active Directory pomocí prostředí PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="32-change-default-passwords-where-applicable"></a>3,2: Změna výchozích hesel tam, kde je to možné

**Pokyny**: Správa přístupu k prostředkům Machine Learning je řízena prostřednictvím služby Azure Active Directory (Azure AD). Azure AD nemá koncept výchozích hesel.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: použijte vyhrazené účty pro správu.

**Pokyny**: Azure Machine Learning při vytvoření nového pracovního prostoru dokončí tři výchozí role a vytvoří standardní operační postupy kolem použití účtů vlastníka.

Můžete také povolit přístup za běhu k účtům pro správu pomocí Azure AD Privileged Identity Management a Azure Resource Manager. 

- [Další informace Machine Learning výchozích rolích](how-to-assign-roles.md#default-roles)

- [Další informace o Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: použijte jednotné přihlašování (SSO) s Azure Active Directory

**Doprovodné** materiály: Machine Learning je integrována s Azure Active Directory, použijte Azure Active Directory jednotné přihlašování (SSO) místo konfigurace jednotlivých samostatných přihlašovacích údajů pro každou službu. Použijte Azure Security Center doporučení pro identitu a přístup.  

- [Vysvětlení jednotného přihlašování pomocí Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Používejte vícefaktorové ověřování pro veškerý přístup založený na Azure Active Directory

**Doprovodné** materiály: povolte Azure Active Directory Multi-Factor Authentication a sledujte Azure Security Center doporučení pro identitu a přístup.

- [Jak povolit vícefaktorové ověřování v Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Jak monitorovat identitu a přístup v rámci Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Používejte vyhrazené počítače (privilegovaný přístup k pracovní stanici) pro všechny úlohy správy

**Pokyny**: Použijte zabezpečenou pracovní stanici spravovanou v Azure (také známou jako pracovní stanice s privilegovaným přístupem nebo privilegovaným přístupem) pro úlohy správy, které vyžadují zvýšená oprávnění.

- [Pochopení zabezpečení pracovních stanic spravovaných Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Jak povolit Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: protokolovat a upozornit na podezřelé aktivity z účtů pro správu

**Doprovodné** materiály: použití sestav a monitorování zabezpečení Azure Active Directory k detekci, kdy dojde k podezřelé nebo nebezpečné aktivitě v prostředí. Pomocí Azure Security Center můžete monitorovat aktivitu identity a přístupu.

- [Identifikace uživatelů Azure AD označených příznakem rizikové aktivity](../active-directory/identity-protection/overview-identity-protection.md)

- [Monitorování identit a aktivit přístupu uživatelů ve službě Azure Security Center](../security-center/security-center-identity-access.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: Správa prostředků Azure pouze ze schválených umístění

**Pokyny**: pomocí pojmenovaných umístění Azure AD povolte přístup jenom z konkrétních logických skupin rozsahů IP adres nebo zemí nebo oblastí.
 
 
 
- [Jak nakonfigurovat pojmenovaná umístění služby Azure AD](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="39-use-azure-active-directory"></a>3,9: použijte Azure Active Directory

**Pokyny**: jako centrální ověřování a systém autorizací použijte Azure Active Directory (Azure AD). Azure AD chrání data pomocí silného šifrování pro neaktivní a tranzitní data. Azure AD také nasolete, hodnoty hash a bezpečně ukládají přihlašovací údaje uživatele.
 
Přístup k rolím může být v Azure omezený na více úrovní. V případě Machine Learning se role dají spravovat na úrovni pracovního prostoru, například máte přístup vlastníka k pracovnímu prostoru, ale nemáte oprávnění vlastníka k této skupině prostředků, která tento pracovní prostor obsahuje. Tato funkce poskytuje podrobnější řízení přístupu k samostatným rolím v rámci stejné skupiny prostředků. 

- [Správa přístupu k pracovnímu prostoru služby Azure Machine Learning](how-to-assign-roles.md) 
 
- [Jak vytvořit a nakonfigurovat instanci Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: pravidelně kontrolovat a sjednotit přístup uživatelů

**Doprovodné** materiály: Azure AD poskytuje protokoly, které vám pomůžou zjistit zastaralé účty. K efektivní správě členství ve skupinách, přístupu k podnikovým aplikacím a přiřazování rolí můžete navíc použít kontroly identity a přístupu v Azure AD. Přístup uživatelů se dá pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní uživatelé. 
 
Použijte Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pro generování protokolů a výstrah v případě, že v prostředí dojde k podezřelé nebo nebezpečné aktivitě.

- [Pochopení sestav Azure AD](../active-directory/reports-monitoring/index.yml)

- [Používání kontrol přístupu a identit Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Nasazení Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: sledování pokusů o přístup k deaktivovaným přihlašovacím údajům

**Doprovodné** materiály: máte přístup ke zdrojům přihlašovacích aktivit, auditem a rizikovým protokolům událostí Azure AD, které vám umožní integraci s jakýmkoli nástrojem Siem/monitoring.

Tento proces můžete zjednodušit vytvořením nastavení diagnostiky pro uživatelské účty Azure AD a odesláním protokolů auditu a protokolů přihlášení do Log Analytics pracovního prostoru. Požadované výstrahy můžete nakonfigurovat v pracovním prostoru Log Analytics.
 
 
- [Jak integrovat protokoly aktivit Azure pomocí Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: upozornění na odchylku chování přihlášení k účtu

**Pokyny**: pomocí Azure AD identity Protectionch funkcí můžete nakonfigurovat automatizované odezvy na zjištěné podezřelé akce týkající se identit uživatelů. Můžete také ingestovat data do služby Azure Sentinel pro další šetření.
 
- [Zobrazení rizikových přihlášení Azure AD](../active-directory/identity-protection/overview-identity-protection.md)
 
- [Jak nakonfigurovat a povolit zásady rizik ochrany identity](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
 
- [Jak připojit Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: Poskytněte Microsoftu přístup k relevantním zákaznickým datům během scénářů podpory.

**Doprovodné** materiály: nepoužitelné; Služba Azure Machine Learning nepodporuje bezpečnostní modul zákazníka.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="data-protection"></a>Ochrana dat

*Další informace najdete v tématu [srovnávací testy zabezpečení Azure: Ochrana dat](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: Udržujte inventář citlivých informací

**Doprovodné** materiály: používejte značky, které vám pomůžou při sledování prostředků Azure, které ukládají nebo zpracovávají citlivé informace.
 
- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: izolujte systémy, které ukládají nebo zpracovávají citlivé informace.

**Pokyny**: Implementujte izolaci pomocí samostatných předplatných a skupin pro správu pro jednotlivé domény zabezpečení, jako je například typ prostředí a úroveň citlivosti dat. Můžete omezit úroveň přístupu k prostředkům Azure, které vaše aplikace a podniková prostředí vyžadují. Přístup k prostředkům Azure můžete řídit prostřednictvím Azure RBAC.
 
- [Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)

- [Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)
 
- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Sledujte a zablokujte neoprávněný přenos citlivých informací

**Pokyny**: použití řešení třetí strany z Azure Marketplace v hraničních sítích pro monitorování neoprávněného přenosu citlivých informací a zablokování takových přenosů při upozornění na odborníky na zabezpečení informací. 

Pro základní platformu spravovanou Microsoftem platí, že Microsoft považuje veškerý obsah zákazníků za citlivý a zajišťuje ochranu před ztrátou a vystavením zákaznických dat. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat. 

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: šifrování všech citlivých informací během přenosu

**Doprovodné** materiály: webové služby nasazené prostřednictvím Azure Machine Learning podporují pouze protokol TLS verze 1,2, který vynutil šifrování dat při přenosu.

- [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: k identifikaci citlivých dat použijte aktivní nástroj zjišťování.

**Doprovodné** materiály: funkce pro identifikaci, klasifikaci a ochranu před únikem informací ještě nejsou k dispozici pro Azure Machine Learning. Pokud je to nutné pro účely dodržování předpisů, implementujte řešení třetí strany.

Pro základní platformu, která je spravovaná Microsoftem, Microsoft považuje veškerý obsah zákazníka za citlivý a vede na skvělé délky, aby se zabránilo ochraně před ztrátou a únikem informací a riziky zákazníků. Aby se zajistilo zabezpečení zákaznických dat v Azure, společnost Microsoft implementovala a udržuje sadu robustních ovládacích prvků a možností ochrany dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: ke správě přístupu k prostředkům použijte službu Azure RBAC.

**Pokyny**: Azure Machine Learning podporuje použití Azure Active Directory (Azure AD) k autorizaci požadavků na Machine Learning prostředky. Pomocí Azure AD můžete použít řízení přístupu na základě role Azure (Azure RBAC) k udělení oprávnění objektu zabezpečení, který může být uživatel nebo instančního objektu aplikace.

- [Správa přístupu k pracovnímu prostoru služby Azure Machine Learning](how-to-assign-roles.md)
- [Použití Azure RBAC pro autorizaci Kubernetes](../aks/manage-azure-rbac.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: použití prevence ztráty dat na základě hostitele k vymáhání řízení přístupu

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

Společnost Microsoft spravuje základní infrastrukturu pro Machine Learning a implementuje přísné ovládací prvky, které zabrání ztrátě nebo expozici zákaznických dat.

- [Principy ochrany zákaznických dat v Azure](../security/fundamentals/protection-customer-data.md)

**Monitorování Azure Security Center**: nelze použít

**Zodpovědnost**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: šifrování citlivých informací v klidovém umístění

**Doprovodné** materiály: Azure Machine Learning ukládá snímky, výstup a protokoly v účtu služby Azure Blob Storage, který je svázán s pracovním prostorem Azure Machine Learning a vaším předplatným. Všechna data uložená v úložišti objektů BLOB v Azure jsou v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem. Data uložená v úložišti objektů BLOB v Azure můžete také šifrovat pomocí vlastních klíčů ve službě Machine Learning Service. 

- [Azure Machine Learning šifrování dat v klidovém umístění](concept-enterprise-security.md#encryption-at-rest)

- [Principy šifrování neaktivních uložených dat v Azure](../security/fundamentals/encryption-atrest.md)

- [Postup konfigurace šifrovacích klíčů spravovaných zákazníkem](../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: protokolovat a upozornit na změny kritických prostředků Azure

**Doprovodné** materiály: pomocí Azure monitor s protokolem aktivit Azure můžete vytvářet výstrahy pro případy, kdy změny probíhají v produkčních instancích Azure Machine Learning a dalších důležitých nebo souvisejících prostředcích.

- [Vytvoření upozornění pro události protokolu aktivit Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

*Další informace najdete v článku [Srovnávací test zabezpečení Azure: Správa ohrožení zabezpečení](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

**Doprovodné** materiály: Pokud je výpočetní prostředek vlastněn Microsoftem, zodpovídá za správu ohrožení zabezpečení Azure Machine Learning služby Microsoft. 

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků, které vlastní vaše organizace, postupujte podle doporučení Azure Security Center pro provedení posouzení ohrožení zabezpečení na virtuálních počítačích Azure, imagí kontejneru a SQL serverech.

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: nasazení automatizovaného řešení pro správu oprav operačního systému

**Doprovodné** materiály: Pokud je výpočetní prostředek vlastněn Microsoftem, zodpovídá za správu oprav služby Azure Machine Learning. 

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U všech výpočetních prostředků, které vlastní vaše organizace, použijte Azure Automation Update Management a ujistěte se, že na virtuálních počítačích s Windows a Linuxem jsou nainstalované nejnovější aktualizace zabezpečení. U virtuálních počítačů s Windows ověřte, že je povolená možnost web Windows Update a že se nastaví automatické aktualizace.

- [Jak nakonfigurovat Update Management pro virtuální počítače v Azure](../automation/update-management/overview.md)

- [Porozumění zásadám zabezpečení Azure monitorovaným Security Center](../security-center/policy-reference.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: nasazení automatizované řešení pro správu oprav pro softwarové tituly třetích stran

**Doprovodné** materiály: Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků, které vlastní vaše organizace, použijte řešení pro správu oprav třetí strany. Zákazníci, kteří už používají Configuration Manager ve svém prostředí, můžou používat taky System Center Updates Publisher, aby jim mohli publikovat vlastní aktualizace do služby Windows Server Update Service. To umožňuje Update Management opravit počítače, které používají Configuration Manager jako úložiště aktualizací se softwarem třetích stran.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: porovnání kontrol zabezpečení back-to-back

**Doprovodné** materiály: Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků, které vlastní vaše organizace, použijte doporučení od Azure Security Center pro provedení posouzení ohrožení zabezpečení na virtuálních počítačích Azure, imagí kontejneru a SQL serverech. Exportovat výsledky kontroly v konzistentních intervalech a porovnat výsledky s předchozími kontrolami a ověřit tak, že chyby zabezpečení byly opraveny. Pokud používáte doporučení ke správě ohrožení zabezpečení, kterou navrhla Azure Security Center, můžete se na portál vybraného řešení překlopit a zobrazit historická data kontroly.

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: použijte proces hodnocení rizik k určení priorit nápravy zjištěných ohrožení zabezpečení

**Doprovodné** materiály: nepoužitelné; Tyto zásady jsou určené pro výpočetní prostředky.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="inventory-and-asset-management"></a>Správa inventáře a aktiv

*Další informace najdete v tématu [testování výkonnosti Azure Security: inventář a Správa prostředků](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

**Pokyny**: pomocí Azure Resource graphu se v předplatných dotázatte na prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.).  Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v předplatných mohli vytvořit výčet všech předplatných Azure i prostředků.

I když se klasické prostředky Azure dají zjistit pomocí Průzkumníka Azure Resource graphu, důrazně se doporučuje vytvářet a používat Azure Resource Manager prostředky.

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

**Doprovodné** materiály: použití značek pro prostředky Azure a přidání metadat k logickému uspořádání podle taxonomie.

- [Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

**Doprovodné** materiály: Používejte označení, skupiny pro správu a samostatné odběry tam, kde je to vhodné, k uspořádání a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.
 
 
 
- [ Vytvoření dalších předplatných Azure](../cost-management-billing/manage/create-subscription.md)
 
 
 
- [ Vytvoření skupin pro správu](../governance/management-groups/create-management-group-portal.md)
 
 
 
- [ Vytváření a používání značek](../azure-resource-manager/management/tag-resources.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

**Doprovodné** materiály: Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb vaší organizace.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

* Žádné povolené typy prostředků
* Povolené typy prostředků

K dotazování a zjišťování prostředků v rámci předplatných navíc použijte graf prostředků Azure.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

**Doprovodné** materiály: Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků vlastněných vaší organizací použijte Azure Automation Change Tracking a inventář k automatizaci shromažďování informací o inventáři z virtuálních počítačů s Windows a Linux. Z Azure Portal je k dispozici název softwaru, verze, Vydavatel a čas aktualizace. Chcete-li získat datum instalace softwaru a další informace, povolte diagnostiku na úrovni hosta a nasměrujte protokoly událostí systému Windows tak, aby Log Analytics pracovní prostor.

- [Jak povolit shromažďování Azure Automation inventáře pro virtuální počítač](../automation/automation-tutorial-installed-software.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

**Doprovodné** materiály: Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků, které vlastní vaše organizace, použijte monitorování integrity souborů (FIM) Azure Security Center k identifikaci veškerého softwaru nainstalovaného na virtuálních počítačích. Další možností, kterou je možné použít místo nebo ve spojení s produktem FIM, je Azure Automation Change Tracking a inventáře za účelem shromažďování inventáře z virtuálních počítačů se systémy Linux a Windows. 

Můžete implementovat vlastní proces odebrání neautorizovaného softwaru. K identifikaci neschváleného softwaru můžete také použít řešení třetí strany.

Pokud už je nepotřebujete, odeberte prostředky Azure.

- [Jak používat monitorování integrity souborů](../security-center/security-center-file-integrity-monitoring.md)

- [Pochopení Azure Automation Change Tracking a inventáře](../automation/change-tracking/overview.md)

- [Jak povolit inventář virtuálních počítačů Azure](../automation/automation-tutorial-installed-software.md)

- [Odstranění prostředku a skupiny prostředků Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

**Doprovodné** materiály: Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků, které vlastní vaše organizace, použijte Azure Security Center Adaptivní řízení aplikací, abyste zajistili, že se spustí jenom autorizovaný software, a veškerý neoprávněný software se zablokuje spouštění na Azure Virtual Machines.

- [Jak používat Azure Security Center Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Sdílené

### <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

**Doprovodné** materiály: použijte Azure Policy k omezení typu prostředků, které se dají vytvořit v zákaznických předplatných, pomocí následujících integrovaných definic zásad:

* Žádné povolené typy prostředků
* Povolené typy prostředků

Kromě toho se pomocí grafu prostředků Azure Dotazujte na prostředky v předplatných a vyhledejte je.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../governance/resource-graph/first-query-portal.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

**Doprovodné** materiály: Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. Pro všechny výpočetní prostředky, které vlastní vaše organizace, použijte Azure Security Center Adaptivní řízení aplikací k určení typů souborů, na které se pravidlo může nebo nemusí vztahovat.

Implementujte řešení třetí strany, pokud adaptivní ovládací prvky aplikace nesplňují požadavek.

- [Jak používat Azure Security Center Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

**Pokyny**: pomocí podmíněného přístupu Azure AD omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že nakonfigurujete "blokování přístupu" pro aplikaci Microsoft Azure Management.
 
- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../role-based-access-control/conditional-access-azure-management.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

**Doprovodné** materiály: Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků, které vlastní vaše organizace, v závislosti na typu skriptů můžete pomocí konfigurací specifických pro operační systém nebo prostředků třetích stran omezit schopnost uživatelů spouštět skripty ve výpočetních prostředcích Azure.  Můžete také použít Azure Security Center Adaptivní řízení aplikací, abyste zajistili, že se spustí jenom autorizovaný software, a veškerý neautorizovaný software bude zablokovaný na Azure Virtual Machines.

- [Řízení spouštění skriptu PowerShellu v prostředích Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Jak používat Azure Security Center Adaptivní řízení aplikací](../security-center/security-center-adaptive-application.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

**Doprovodné** materiály: nepoužitelné; Toto doporučení je určené pro webové aplikace běžící na Azure App Service nebo výpočetních prostředcích.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

## <a name="secure-configuration"></a>Zabezpečená konfigurace

*Další informace najdete v tématu [Konfigurace zabezpečení Azure Security test: zabezpečení](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Vytvoření zabezpečených konfigurací pro všechny prostředky Azure

**Pokyny**: definování a implementace standardních konfigurací zabezpečení pro službu Azure Machine Learning pomocí Azure Policy. Pomocí aliasů Azure Policy v oboru názvů Microsoft. MachineLearning můžete vytvářet vlastní zásady pro auditování nebo prosazování konfigurace služeb Azure Machine Learning.

Azure Resource Manager má možnost Exportovat šablonu do JavaScript Object Notation (JSON), která by měla být přezkoumána, aby konfigurace splňovala požadavky na zabezpečení vaší organizace.

Můžete také použít doporučení z Azure Security Center jako standardní hodnoty konfigurace pro prostředky Azure.

Azure Machine Learning plně podporuje úložiště Git pro sledování práce; úložiště můžete klonovat přímo do sdíleného systému souborů pracovních prostorů, použít Git na místní pracovní stanici a zajistit, aby se v rámci vašeho Machine Learning prostředí používaly zabezpečené konfigurace pro prostředky kódu.

- [Jak zobrazit dostupné aliasy Azure Policy](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)

- [Export jednoho a více prostředků do šablony v Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: Vytvoření zabezpečených konfigurací operačního systému

**Doprovodné** materiály: Pokud je výpočetní prostředek vlastněný společností Microsoft, zodpovídá za to, že společnost Microsoft odpovídá za zabezpečené konfigurace operačního systému služby Azure Machine Learning.

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků, které vlastní vaše organizace, použijte Azure Security Center doporučení k údržbě konfigurací zabezpečení ve všech výpočetních prostředcích.  Kromě toho můžete k vytvoření konfigurace zabezpečení operačního systému vyžadovaného vaší organizací použít vlastní image operačního systému nebo konfiguraci stavu Azure Automation.

- [Jak monitorovat Azure Security Center doporučení](../security-center/security-center-recommendations.md)

- [Doporučení k zabezpečení – Referenční příručka](../security-center/recommendations-reference.md)

- [Přehled konfigurace stavu Azure Automation](../automation/automation-dsc-overview.md)

- [Nahrání virtuálního pevného disku a jeho použití k vytvoření nových virtuálních počítačů s Windows v Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Vytvoření virtuálního počítače se systémem Linux z vlastního disku pomocí Azure CLI](../virtual-machines/linux/upload-vhd.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: udržování zabezpečených konfigurací prostředků Azure

**Doprovodné** materiály: použijte Azure Policy [Deny] a [Deploy, pokud neexistuje] pro vymáhání zabezpečených nastavení napříč prostředky Azure. Kromě toho můžete použít šablony Azure Resource Manager k údržbě konfigurace zabezpečení vašich prostředků Azure, které vaše organizace vyžaduje. 
 
 
 
- [Pochopení Azure Policych efektů](../governance/policy/concepts/effects.md)
 
- [Vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md)
 
- [ Přehled šablon Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: udržování zabezpečených konfigurací operačního systému

**Doprovodné** materiály: Pokud je výpočetní prostředek vlastněný společností Microsoft, zodpovídá za to, že společnost Microsoft odpovídá za zabezpečené konfigurace operačního systému služby Azure Machine Learning.

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. V případě výpočetních prostředků, které vlastní vaše organizace, použijte doporučení od Azure Security Center při provádění posouzení ohrožení zabezpečení ve výpočetních prostředcích Azure.  Kromě toho můžete použít šablony Azure Resource Manager, vlastní image operačního systému nebo konfiguraci stavu Azure Automation, abyste zachovali konfiguraci zabezpečení operačního systému, který vyžaduje vaše organizace.   Šablony virtuálních počítačů Microsoft v kombinaci s konfigurací stavu Azure Automation můžou pomoct při plnění a údržbě požadavků na zabezpečení. 

Všimněte si, že Azure Marketplace image virtuálních počítačů publikované Microsoftem jsou spravované a udržované společností Microsoft. 

- [Implementace doporučení pro posouzení ohrožení zabezpečení Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md)

- [Jak vytvořit virtuální počítač Azure ze šablony ARM](../virtual-machines/windows/ps-template.md)

- [Přehled konfigurace stavu Azure Automation](../automation/automation-dsc-overview.md)

- [Vytvoření virtuálního počítače s Windows v Azure Portal ](../virtual-machines/windows/quick-create-portal.md)

- [Informace o tom, jak stáhnout šablonu virtuálního počítače](../virtual-machines/windows/download-template.md)

- [Ukázkový skript pro nahrání virtuálního pevného disku do Azure a vytvoření nového virtuálního počítače](../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Konfigurace prostředků Azure v zabezpečeném úložišti

**Pokyny**: Pokud používáte vlastní definice Azure Policy pro Machine Learning nebo související prostředky, použijte Azure Repos k bezpečnému ukládání a správě kódu.

Azure Machine Learning plně podporuje úložiště Git pro sledování práce; úložiště můžete klonovat přímo do sdíleného systému souborů pracovních prostorů, použít Git na místní pracovní stanici a zajistit, aby se v rámci vašeho Machine Learning prostředí používaly zabezpečené konfigurace pro prostředky kódu.

- [Jak v Azure DevOps ukládat kód](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Dokumentace k Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpečné uložení vlastních imagí operačního systému

**Doprovodné** materiály: Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků, které vlastní vaše organizace, použijte řízení přístupu na základě role Azure (Azure RBAC), abyste zajistili, že k vašim vlastním imagí mají přístup jenom autorizovaní uživatelé. Pomocí Galerie sdílených imagí Azure můžete své image sdílet s různými uživateli, instančními objekty nebo skupinami služby Azure AD v rámci vaší organizace. Uložte image kontejneru do Azure Container Registry a pomocí Azure RBAC zajistěte, aby měli přístup jenom autorizovaní uživatelé.

- [Pochopení Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Vysvětlení služby Azure RBAC pro Container Registry](../container-registry/container-registry-roles.md)

- [Jak nakonfigurovat službu Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Přehled Galerie sdílených imagí](../virtual-machines/windows/shared-image-galleries.md)

- [Použití Azure RBAC pro autorizaci Kubernetes](../aks/manage-azure-rbac.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost**: netýká se

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: nasazení nástrojů pro správu konfigurace pro prostředky Azure

**Pokyny**: pomocí aliasů Azure Policy v oboru názvů Microsoft. MachineLearning můžete vytvořit vlastní zásady pro upozornění, audit a prosazování konfigurace systému. Dále můžete vyvinout proces a kanál pro správu výjimek zásad.

- [Jak nakonfigurovat a spravovat Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Jak používat aliasy](../governance/policy/concepts/definition-structure.md#aliases)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: nasazení nástrojů pro správu konfigurace pro operační systémy

**Doprovodné** materiály: Pokud je výpočetní prostředek vlastněn Microsoftem, zodpovídá za to, že společnost Microsoft je zodpovědná za nasazení zabezpečené konfigurace služby Azure Machine Learning.

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků, které vlastní vaše organizace, použijte konfiguraci Azure Automation stav pro uzly pro konfiguraci požadovaného stavu (DSC) v jakémkoli cloudu nebo v místním datovém centru. Můžete snadno připojit počítače, přiřazovat je k deklarativním konfiguracím a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který jste zadali. 

- [Jak povolit konfiguraci stavu Azure Automation](../automation/automation-dsc-onboarding.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: Implementujte automatizované monitorování konfigurace pro prostředky Azure.

**Pokyny**: použijte Azure Security Center k provádění kontrol standardních hodnot vašich prostředků Azure. Kromě toho použijte Azure Policy k upozornění a auditování konfigurace prostředků Azure.
 
 
 
- [ Jak opravit doporučení v Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Implementujte automatizované monitorování konfigurace pro operační systémy

**Doprovodné** materiály: Pokud je výpočetní prostředek vlastněn společností Microsoft, zodpovídá vám za automatizované monitorování konfigurace služby Azure Machine Learning.

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků, které vlastní vaše organizace, použijte Azure Security Center výpočetní &amp; aplikace a použijte doporučení pro virtuální počítače a servery a kontejnery.

- [Porozumění doporučení týkajících se kontejnerů Azure Security Center](../security-center/container-security.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

### <a name="711-manage-azure-secrets-securely"></a>7,11: zabezpečená Správa tajných kódů Azure

**Doprovodné** materiály: Používejte spravované identity společně s Azure Key Vault k zjednodušení správy tajných kódů pro vaše cloudové aplikace.

Azure Machine Learning podporuje šifrování úložiště dat pomocí klíčů spravovaných zákazníkem, je třeba spravovat střídání klíčů a odvolávat požadavky na zabezpečení a dodržování předpisů v organizaci. 

Pomocí Azure Key Vault můžete předávat tajné kódy do vzdáleného spouštění namísto nezašifrovaného textu ve školicích skriptech.

- [Azure Machine Learning klíčů spravovaných zákazníkem](concept-enterprise-security.md#azure-blob-storage)

- [Použití tajných kódů přihlašovacích údajů v Azure Machine Learningch školicích běhů](how-to-use-secrets-in-runs.md)

- [Jak používat spravované identity pro prostředky Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Vytvoření Key Vault](../key-vault/general/quick-create-portal.md)

- [Ověření Key Vault](../key-vault/general/authentication.md)

- [Postup přiřazení zásady přístupu Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpečně a automaticky spravujte identity

**Doprovodné** materiály: Azure Machine Learning podporuje jak předdefinované role, tak i možnost vytvářet vlastní role. Spravované identity použijte k poskytování služeb Azure s automaticky spravovanou identitou ve službě Azure AD. Spravované identity vám umožňují ověřit jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault bez jakýchkoli přihlašovacích údajů v kódu.

 
- [Správa přístupu k pracovnímu prostoru služby Azure Machine Learning](how-to-assign-roles.md)

- [Postup konfigurace spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: Eliminujte nezamýšlenou expozici přihlašovacích údajů

**Pokyny**: implementace skeneru přihlašovacích údajů pro identifikaci přihlašovacích údajů v rámci kódu. Credential Scanner bude také pobízet k přesunutí odhalených přihlašovacích údajů do bezpečnějších umístění, jako je Azure Key Vault. 

- [Jak nastavit skener přihlašovacích údajů](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="malware-defense"></a>Obrana před malwarem

*Další informace najdete v tématu [Srovnávací test zabezpečení Azure: obrana proti malwaru](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Používejte centrálně spravovaný antimalwarový software

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure Machine Learning), ale nespouští se u zákaznického obsahu.

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. U výpočetních prostředků vlastněných vaší organizací použijte Microsoft Antimalware pro Azure a průběžně monitorujte a ochraňujte své prostředky. Pro Linux použijte antimalwarové řešení třetích stran. K detekci malwaru nahraného do účtů úložiště taky můžete použít detekci hrozeb Azure Security Center pro datové služby.

- [Jak nakonfigurovat Microsoft Antimalware pro Azure](../security/fundamentals/antimalware.md)

- [Ochrana před hrozbami v Azure Security Center](../security-center/azure-defender.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: předběžná kontrola souborů, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

**Pokyny**: ochrana proti malwaru od Microsoftu je povolená na podkladovém hostiteli, který podporuje služby Azure (například Azure Machine Learning), ale neběží na zákaznickém obsahu.

Je vaše zodpovědnost za to, že se veškerý obsah nahrává do prostředků Azure, které nejsou ve výpočetním prostředí, předem. Microsoft nemůže získat přístup k zákaznickým datům, a proto nemůže za vás provádět kontrolu proti malwarovým kontrolám zákaznického obsahu.

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: Ujistěte se, že antimalwarový software a signatury jsou aktualizované

**Pokyny**: antimalwarový software společnosti Microsoft je povolen a udržován pro základního hostitele, který podporuje služby Azure (například Azure Machine Learning), ale neběží na zákaznickém obsahu.

Azure Machine Learning má různou podporu napříč různými výpočetními prostředky a dokonce i s vašimi vlastními výpočetními prostředky. Pro všechny výpočetní prostředky, které vlastní vaše organizace, použijte doporučení v Azure Security Centerch výpočetních aplikacích, &amp; abyste zajistili aktuálnost všech koncových bodů s nejnovějšími podpisy. Pro Linux použijte antimalwarové řešení třetích stran.

- [Jak nasadit Microsoft Antimalware pro Azure](../security/fundamentals/antimalware.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

## <a name="data-recovery"></a>Obnovení dat

*Další informace najdete v článku [srovnávací testy zabezpečení Azure: obnovení dat](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zajištění pravidelného automatického zálohování

**Pokyny**: Správa obnovování dat ve službě Machine Learning je prostřednictvím správy dat v připojených úložištích dat. Nezapomeňte postupovat podle pokynů pro obnovení dat pro připojená úložiště, která zálohují data na zásady organizace zákazníka.

- [Postup obnovení souborů ze zálohy virtuálního počítače Azure](../backup/backup-azure-restore-files-from-vm.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: proveďte kompletní systémové zálohy a zálohujte všechny klíče spravované zákazníkem.

**Pokyny**: zálohování dat ve službě Machine Learning je prostřednictvím správy dat v připojených úložištích dat. Povolte Azure Backup pro virtuální počítače a nakonfigurujte požadovanou frekvenci a dobu uchování. Zálohujte klíče spravované zákazníkem v Azure Key Vault.

- [Postup obnovení souborů ze zálohy virtuálního počítače Azure](../backup/backup-azure-restore-files-from-vm.md)
- [Postup obnovení klíčů Key Vault v Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: ověření všech záloh včetně klíčů spravovaných zákazníkem

**Pokyny**: ověření zálohování dat ve službě Machine Learning je prostřednictvím správy dat v připojených úložištích dat. Pravidelně provádějte obnovování obsahu v Azure Backup. Ujistěte se, že můžete obnovit zálohovaných klíčů spravovaných zákazníkem.

- [Postup obnovení souborů ze zálohy virtuálního počítače Azure](../backup/backup-azure-restore-files-from-vm.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: Zajistěte ochranu záloh a klíčů spravovaných zákazníkem

**Doprovodné** materiály: pro místní zálohování se šifrování v klidovém prostředí poskytuje pomocí hesla, které zadáte při zálohování do Azure. Použijte řízení přístupu na základě role Azure k ochraně záloh a klíčů spravovaných zákazníkem. 

Povolí v Key Vault ochranu před náhodným odstraněním a vyprázdněním, aby se chránily klíče proti náhodnému nebo škodlivému odstranění. Pokud se pro ukládání záloh používá Azure Storage, povolte obnovitelné odstranění, abyste mohli data ukládat a obnovovat při odstraňování objektů BLOB nebo snímků objektů BLOB.
 
 
- [Pochopení Azure RBAC](../role-based-access-control/overview.md)

- [Jak povolit obnovitelné odstranění a ochranu před vymazáním v Key Vaultu](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Obnovitelné odstranění pro Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="incident-response"></a>Reakce na incidenty

*Další informace najdete v odpovědi na [incidenty Azure Security test:](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: Vytvoření Průvodce odpověďmi na incidenty

**Pokyny**: vývoj Průvodce odpověďmi na incidenty pro vaši organizaci. Zajistěte, aby existovaly písemné plány odpovědí na incidenty, které definují všechny role pracovníků, a také fáze zpracování incidentů a správu, které se mají zjišťovat, na kontrolu po jednotlivých událostech. 

- [Pokyny k vytvoření vlastního procesu reakce na incidenty zabezpečení](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomie centra Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Pomoc při tvorbě vlastního plánu odpovědí na incidenty najdete v příručce pro zpracování incidentů v počítači s NIST.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: vytvoření bodování incidentu a postupu stanovení priorit

**Doprovodné** materiály: Azure Security Center přiřadí každému upozornění závažnost závažnosti, které vám pomůžou určit, které výstrahy by se měly prozkoumat jako první. Závažnost je založena na tom, jak se nachází Security Center ve vyhledávání nebo v analytickém formátu, který vydává výstrahu, a také na úrovni spolehlivosti, u kterých došlo k škodlivému záměru za aktivitu, která vedla k upozornění.

Navíc můžete označit odběry pomocí značek a vytvořit systém pojmenování k identifikaci a kategorizaci prostředků Azure, zejména těch, které zpracovávají citlivá data. Je vaše zodpovědnost za to, že je možné určit prioritu nápravy výstrah na základě závažnosti prostředků a prostředí Azure, ve kterých došlo k incidentu.

- [Výstrahy zabezpečení ve službě Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Používání značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorování služby Azure Security Center:** Ano

**Odpovědnost:** Zákazník

### <a name="103-test-security-response-procedures"></a>10,3: testovací postupy pro odpověď zabezpečení

**Doprovodné** materiály: proveďte cvičení k otestování funkcí reakce na incidenty na běžných tempo, které vám pomůžou ochránit vaše prostředky Azure. Identifikujte slabá místa a mezery a pak podle potřeby upravte plán odpovědí.

- [Publikování v NIST – průvodce pro testování, školení a cvičení programů pro plány a možnosti IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: zadání podrobností o kontaktu incidentu zabezpečení a konfigurace oznámení o výstrahách pro incidenty zabezpečení

**Doprovodné** materiály: kontaktní informace incidentu zabezpečení bude společnost Microsoft používat ke kontaktování v případě, že služba Microsoft Security Response Center (MSRC) zjistí, že k datům došlo nezákonní nebo neoprávněná osoba. Projděte si incidenty, abyste měli jistotu, že jsou vyřešené problémy.

- [Jak nastavit kontakt zabezpečení pro Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: zahrňte výstrahy zabezpečení do systému reakce na incidenty.

**Pokyny**: vyexportujte výstrahy a doporučení pro Azure Security Center pomocí funkce průběžného exportu, které vám pomůžou identifikovat rizika pro prostředky Azure. Průběžný export umožňuje exportovat výstrahy a doporučení buď ručně, nebo nepřetržitě, průběžným způsobem. Pomocí konektoru Azure Security Center Data můžete streamovat výstrahy do Azure Sentinel.

- [Postup konfigurace průběžného exportu](../security-center/continuous-export.md)

- [Streamování upozornění do služby Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizujte reakci na výstrahy zabezpečení

**Doprovodné** materiály: použití funkce automatizace pracovních postupů Azure Security Center k automatickému spouštění odpovědí na výstrahy zabezpečení a doporučení k ochraně vašich prostředků Azure.

- [Jak nakonfigurovat automatizaci pracovního postupu v Security Center](../security-center/workflow-automation.md)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Zákazník

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrační testy a tzv. red team exercises

*Další informace najdete v tématu [testy zabezpečení Azure – testování průniku a cvičení červeného týmu](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: proveďte pravidelné testování průniku vašich prostředků Azure a zajistěte nápravu všech kritických poznatků zabezpečení.

**Doprovodné** materiály: postupujte podle pravidel pro testování průniku Microsoft Cloud, abyste zajistili, že testy průniku nejsou v rozporu s zásadami Microsoftu. Využijte strategii Microsoftu a spuštění červeného týmu a testování průniku na živém webu na cloudové infrastruktuře, službách a aplikacích spravovaný Microsoftem.

- [Pravidla zapojení testování průniku](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Červený tým cloudových služeb Microsoftu](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorování Azure Security Center**: nelze použít

**Odpovědnost:** Sdílené

## <a name="next-steps"></a>Další kroky

- Zobrazit [Srovnávací test zabezpečení Azure](../security/benchmarks/overview.md)
- Další informace o [základních úrovních zabezpečení Azure](../security/benchmarks/security-baselines-overview.md)