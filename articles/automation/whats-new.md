---
title: Co je nového v Azure Automation
description: Významné aktualizace Azure Automation každý měsíc aktualizovány.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 04/09/2021
ms.custom: references_regions
ms.openlocfilehash: f8b4d6965a8a1f046fd2459ce9fe5cce8ea45443
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531073"
---
# <a name="whats-new-in-azure-automation"></a>Co je nového v Azure Automation?

Azure Automation průběžně přijímá vylepšení. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb

Tato stránka se aktualizuje měsíčně, takže ji můžete pravidelně znovu navštěvovat.

## <a name="march-2021"></a>Březen 2021

### <a name="new-azure-automation-built-in-policies"></a>Nové Azure Automation předdefinované zásady

**Zadejte:** Nová funkce

Azure Automation přidal 5 nových předdefinovaných zásad:

- Účty služby Automation by měly zakázat přístup k veřejné síti,
- Azure Automation účty by měly používat klíče spravované zákazníkem k šifrování neaktivních dat
- Konfigurace Azure Automation účtů pro zakázání přístupu k veřejné síti
- Konfigurace připojení privátního koncového bodu u Azure Automation účtů
- Měla by být povolená připojení privátního koncového bodu na účtech Automation.

Další podrobnosti najdete v článku informace o [zásadách](./policy-reference.md) .

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Podpora pro automatizaci a konfiguraci stavu s deklarací GA v Jižní Indie

**Zadejte:** Nová funkce

Využijte možnosti automatizace procesů a konfigurace stavu v Jižní Indie. Další informace najdete v [oznámení](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/) .

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Podpora pro automatizaci a konfiguraci stavu s deklarací GA v Velká Británie – západ

**Zadejte:** Nová funkce

Využijte možnosti automatizace procesů a konfigurace stavu v Velká Británie – západ. Další informace najdete v tématu [oznámení](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Podpora pro automatizaci a konfiguraci stavu s deklarací GA v Spojené arabské emiráty Central

**Zadejte:** Nová funkce

Využijte možnosti automatizace procesů a konfigurace stavu v Spojené arabské emiráty Central. Další informace najdete v [oznámení](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/) .

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2--norway-west-and-france-south"></a>Podpora pro automatizaci a konfiguraci stavu dostupná v Austrálii – střed 2, Norsko – západ a Francie – jih

**Zadejte:** Nová funkce

Další informace na stránce zaregistrování [dat](https://azure.microsoft.com/global-infrastructure/data-residency/) získáte výběrem geografické oblasti pro každou oblast.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Přidaly se nové skripty pro instalaci hybridního pracovního procesu v systému Windows a Linux.

**Zadejte:** Nová funkce

Do Azure Automationého [úložiště GitHub](https://github.com/azureautomation) byly přidány dva nové skripty, které řeší jeden z klíčových scénářů Azure Automation nastavení Hybrid Runbook Worker na počítači s Windows nebo Linux. Skript vytvoří nový virtuální počítač nebo použije stávající. v případě potřeby vytvoří pracovní prostor Log Analytics, nainstaluje agenta Log Analytics pro Windows nebo agenta Log Analytics pro Linux a zaregistruje počítač do Log Analytics pracovního prostoru. Skript Windows se jmenuje **vytvořit Automation Windows HybridWorker** a skript Linux **vytvoří Automation Linux HybridWorker**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Vyvolat Runbook pomocí Webhooku šablony Azure Resource Manager

**Zadejte:** Nová funkce

Další podrobnosti najdete v tématu [použití Webhooku ze šablony ARM](./automation-webhooks.md#use-a-webhook-from-an-arm-template) .

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure Update Management teď podporuje CentOS 8. x, Red Hat Enterprise Linux Server 8. x a SUSE Linux Enterprise Server 15.

**Zadejte:** Nová funkce

Další podrobnosti najdete v [úplném seznamu](./update-management/overview.md#supported-operating-systems) podporovaných operačních systémů Linux.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Podpora zaregistrování dat ve vaší oblasti pro oblast Brazílie – jih a Jižní Východní Asie 

**Zadejte:** Nová funkce

Ve všech oblastech s výjimkou Brazílie – jih a jihovýchodní Asie se Azure Automation data ukládají v jiné oblasti (v spárované oblasti Azure), která zajišťuje provozní kontinuitu a zotavení po havárii (BCDR). Pro oblasti Brazílie a jihovýchodní Asie teď ukládáme Azure Automation data ve stejné oblasti, abyste se dokryli požadavky na umístění dat pro tyto oblasti. Další podrobnosti najdete [v tématu geografická replikace v Azure Automation](./automation-managing-data.md#geo-replication-in-azure-automation) .

## <a name="february-2021"></a>Únor 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Podpora pro automatizaci a konfiguraci stavu deklarovaný GA v oblasti Japonsko – západ

**Zadejte:** Nová funkce

Dostupnost konfigurace účtu a stavu služby Automation v oblasti Japonsko – západ Další informace najdete v tématu [oznámení](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Zavedení vlastního Azure Policy dodržování předpisů pro vymáhání spuštění sady Runbook v Hybrid Worker

**Zadejte:** Nová funkce

Pomocí nového pravidla dodržování předpisů Azure Policy můžete vytvořit úlohy, Webhooky a plány úloh, které se spustí pouze v Hybrid Workerch skupinách.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Update Management dostupnost v oblastech Východní USA, Francie, střední a Severní Evropa

**Zadejte:** Nová funkce

Funkce automatizace Update Management je k dispozici v oblastech Východní USA, Francie – střed a Severní Evropa. Aktualizace dokumentace, která odráží tuto změnu, najdete v tématu [mapování podporovaných oblastí](how-to/region-mappings.md) .

## <a name="january-2021"></a>Leden 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Podpora pro automatizaci a konfiguraci stavu s deklarací GA v Švýcarsko – západ

**Zadejte:** Nová funkce

Dostupnost účtu Automation a konfigurace stavu v oblasti Švýcarsko – západ. Pro další informace si přečtěte [oznámení](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Přidání skriptu Python 3 pro import modulu s několika závislostmi

**Zadejte:** Nová funkce

Skript je k dispozici ke stažení z našeho [úložiště GitHub](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py). 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Podpora rolí Hybrid Runbook Worker pro CentOS 8. x/RHEL 8. x/SLES 15

**Textový.** Nová funkce

Funkce Hybrid Runbook Worker podporuje distribuce CentOS 8. x, REHL 8. x a SLES 15 jenom pro automatizaci procesů na hybridních pracovních procesech Runbooku. V části [podporované operační systémy](automation-linux-hrw-install.md#supported-linux-operating-systems) najdete aktualizace dokumentace, aby odrážely tyto změny.

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>Dostupnost Update Management a Change Tracking v oblastech Austrálie – východ, Východní Asie, Západní USA a Střed USA

**Zadejte:** Nová funkce

Účet služby Automation, Change Tracking a inventář a Update Management jsou k dispozici v oblastech Austrálie – východ, Východní Asie, Západní USA a Střed USA. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Představená verze Public Preview sad Runbook Python 3 v cloudu pro státní správu USA

**Zadejte:** Nová funkce Azure Automation zavádí podporu cloudového a hybridního spuštění Runbooku v Pythonu ve verzi Public Preview v oblastech cloudu USA. Další informace najdete v [oznámení](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Automation Runbooky přesunuté z centra skriptů TechNet do GitHubu

**Zadejte:** Plánování změn

Centrum skriptů TechNet se vyřadí z provozu a všechny Runbooky hostované v galerii runbooků se přesunuly do naší [organizace služby Automation](https://github.com/azureautomation)na webu GitHub. Další informace najdete v tématu [Azure Automation Runbooky se přesunou do GitHubu](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="december-2020"></a>Prosinec 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation a Update Management soukromý odkaz GA

**Zadejte:** Nová funkce

Podpora Azure Automation a Update Management byla oznámena jako GA pro cloudy Azure Global a vládních institucí. Podpora privátního propojení Azure Automation k zabezpečení spouštění sady Runbook v roli hybridního pracovního procesu pomocí Update Management k opravě počítačů, vyvolání sady Runbook prostřednictvím Webhooku a použití služby konfigurace stavu k uchování stížnosti počítačů. Další informace najdete v tématu [Podpora pro Azure Automation privátních odkazů](https://azure.microsoft.com/updates/azure-automation-private-link) .

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation klasifikované jako přístupnost-C s certifikací při přístupnosti

**Zadejte:** Nová funkce

Funkce usnadnění pro produkty Microsoftu, které mají organizacím, můžou řešit globální požadavky na přístupnost. Na stránce [oznámení blogu](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) vyhledejte **Azure Automation** , abyste si přečetli sestavu dodržování přístupnosti pro službu Automation.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Podpora pro automatizaci a konfiguraci stavu GA v Spojené arabské emiráty sever

**Zadejte:** Nová funkce

Dostupnost účtu Automation a konfigurace stavu v oblasti Spojené arabské emiráty sever. Další informace najdete v tématu [oznámení](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Podpora pro automatizaci a konfiguraci stavu GA v Německo – středozápad

**Zadejte:** Nová funkce

Dostupnost konfigurace účtu a stavu služby Automation v oblasti Německo – západ Další informace najdete v tématu [oznámení](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>Podpora DSC pro Oracle 6 a 7

**Zadejte:** Nová funkce

Správa počítačů s Oracle Linux 6 a 7 s konfigurací stavu automatizace V článku [Podporované distribuce pro Linux](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) najdete aktualizace dokumentace, aby odrážely tyto změny.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Public Preview pro Runbooky python3 ve službě Automation

**Zadejte:** Nová funkce

Azure Automation teď podporuje službu Python 3 Cloud a hybridní spuštění Runbooku ve verzi Public Preview ve všech oblastech v globálním cloudu Azure. Další podrobnosti najdete v článku [oznámení] (( https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) .

## <a name="november-2020"></a>Listopad 2020

### <a name="dsc-support-for-ubuntu-1804"></a>Podpora DSC pro Ubuntu 18,04

**Zadejte:** Nová funkce

V části [podporované Linux distribuce](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) najdete aktualizace dokumentace, která odráží tyto změny.

## <a name="october-2020"></a>Říjen 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Podpora pro automatizaci a konfiguraci stavu GA v Švýcarsko – sever

**Zadejte:** Nová funkce

Dostupnost účtu Automation a konfigurace stavu v Švýcarsko – sever. Další informace najdete v tématu [oznámení](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Podpora pro automatizaci a konfiguraci stavu GA v oblasti Brazílie – jih – východ

**Zadejte:** Nová funkce

Dostupnost konfigurace účtu a stavu služby Automation v oblasti Brazílie – jih Další informace najdete v tématu [oznámení](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Update Management dostupnost v Střed USA – jih

**Zadejte:** Nová funkce

Mapování Azure Automation oblasti bylo aktualizováno na podporu Update Management funkcí v Střed USA – jih oblasti. V části [podporované mapování oblastí](how-to/region-mappings.md#supported-mappings) najdete aktualizace dokumentace, aby odrážela tuto změnu.

## <a name="september-2020"></a>Září 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Aktualizace sady Runbook Start/Stop VMs during off-hours pro použití Azure AZ modules

**Zadejte:** Nová funkce

Sady Runbook spustit/zastavit byly aktualizovány tak, aby používaly moduly AZ modules místo Azure Resource Managerch modulů. V tématu Přehled [Start/Stop VMS during off-hours](automation-solution-vm-management.md) najdete informace o aktualizacích dokumentace, aby odrážely tyto změny.

## <a name="august-2020"></a>Srpen 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Publikovalo se rozšíření DSC pro podporu ARC Azure.

**Zadejte:** Nová funkce

Konfigurace stavu Azure Automation slouží k centrálnímu ukládání konfigurací a údržbě požadovaného stavu hybridních připojených počítačů, které jsou povolené prostřednictvím rozšíření virtuálních počítačů DSC serverů s podporou Azure ARC. Další informace najdete v tématu [Přehled rozšíření virtuálních počítačů s povolenými ARC](../azure-arc/servers/manage-vm-extensions.md)pro čtení.

### <a name="july-2020"></a>Červenec 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Představená Public Preview podpora privátních odkazů ve službě Automation

**Zadejte:** Nová funkce

Použití privátního odkazu Azure k bezpečnému propojení virtuálních sítí s Azure Automation pomocí privátních koncových bodů. Pro další informace si přečtěte [oznámení](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Podpora Hybrid Runbook Worker pro Windows Server 2008 R2

**Zadejte:** Nová funkce

Automatizace Hybrid Runbook Worker podporuje operační systém Windows Server 2008 R2. V části [podporované operační systémy](automation-windows-hrw-install.md#supported-windows-operating-system) najdete aktualizace dokumentace, aby odrážely tyto změny.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Podpora Update Management pro Windows Server 2008 R2

**Zadejte:** Nová funkce

Update Management podporuje vyhodnocení a opravy operačního systému Windows Server 2008 R2. V části [podporované operační systémy](update-management/overview.md#clients) najdete aktualizace dokumentace, aby odrážely tyto změny.

### <a name="automation-diagnostic-logs-schema-update"></a>Aktualizace schématu diagnostických protokolů pro automatizaci

**Zadejte:** Nová funkce

Změna schématu Azure Automation dat protokolu ve službě Log Analytics. Další informace najdete v tématu [přeposlání Azure Automation dat úloh do protokolů Azure monitor](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse podporuje Update Management Automation.

**Zadejte:** Nová funkce

Azure Lighthouse umožňuje delegovanou správu prostředků s Update Management pro poskytovatele služeb a zákazníky. Další informace si můžete přečíst [zde](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Červen 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automatizace a Update Management dostupnost v oblasti US Gov – Arizona

**Zadejte:** Nová funkce

Účet služby Automation a Update Management jsou k dispozici v US Gov – Arizona. Další informace najdete v tématu [oznámení](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Aktualizace skriptu Hybrid Runbook Workerho zprovoznění pro použití AZ modules

**Zadejte:** Nová funkce

Sada New-OnPremiseHybridWorker Runbook byla aktualizována tak, aby podporovala AZ Modules. Další informace najdete v balíčku v [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Update Management dostupnost v Čína – východ 2

**Zadejte:** Nová funkce

Mapování Azure Automation oblasti bylo aktualizováno na podporu Update Management funkcí v Čína – východ 2 oblasti. V části [podporované mapování oblastí](how-to/region-mappings.md#supported-mappings) najdete aktualizace dokumentace, aby odrážela tuto změnu.

## <a name="may-2020"></a>Květen 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Aktualizované záznamy DNS služby Automation z oblastí specifických pro konkrétní adresy URL pro účet Automation

**Zadejte:** Nová funkce

Azure Automation záznamů DNS byly aktualizovány pro podporu privátních odkazů. Pro další informace si přečtěte [oznámení](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Přidání možnosti, která ve výchozím nastavení udržuje Runbooky Automation a skripty DSC šifrované

**Zadejte:** Nová funkce

Kromě zlepšení zabezpečení prostředků jsou taky zašifrované Runbooky a skripty DSC, aby se zvýšila Azure Automation zabezpečení.

## <a name="april-2020"></a>Duben 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Vyřazení úlohy sledovacího procesu Automation

**Zadejte:** Plánování změn

Azure Logic Apps je teď doporučený a podporovaný způsob, jak monitorovat události, naplánovat opakující se úlohy a aktivovat akce. Funkce úlohy sledovacího procesu nebudou mít žádné další investice. Další informace najdete v tématu [plánování a spouštění opakujících se automatizovaných úloh pomocí Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

## <a name="march-2020"></a>Březen 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Podpora výpočtů úrovně dopadu 5 (IL5) v cloudu Azure pro komerční a státní správu

**Textový**

Hybrid Runbook Worker Azure Automation lze v Azure Government použít k podpoře úloh na úrovni dopadu 5. Další informace najdete v naší [dokumentaci](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5).

## <a name="february-2020"></a>Únor 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Zavádí se podpora značek služeb virtuální sítě Azure.

**Zadejte:** Nová funkce

Podpora automatizace značek služeb povoluje nebo zakazuje provoz služby Automation pro podmnožinu scénářů. Další informace najdete v [dokumentaci](automation-hybrid-runbook-worker.md#service-tags).

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Povolit podporu TLS 1,2 pro službu Azure Automation

**Zadejte:** Plánování změn

Azure Automation plně podporuje TLS 1,2 a všechna volání klientů (prostřednictvím webhooků, uzlů DSC a hybridního pracovního procesu). TLS 1,1 a TLS 1,0 jsou stále podporované kvůli zpětné kompatibilitě se staršími klienty, dokud se zákazníci nestandardizací a plně migrují na TLS 1,2.

## <a name="january-2020"></a>Leden 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Představený Public Preview klíčů spravovaných zákazníkem pro Azure Automation

**Zadejte:** Nová funkce

Zákazníci můžou spravovat a zabezpečit šifrování Azure Automationch prostředků pomocí vlastních spravovaných klíčů. Další informace najdete v tématu [použití klíčů spravovaných zákazníkem](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Vyřazení rozhraní REST API služby Azure Service Management (ASM) pro Azure Automation

**Zadejte:** Vyřazení

Rozhraní REST API pro službu Azure Service Management (ASM) pro Azure Automation budou Vyřazená a už se nepodporují po 30. lednu 2020. Další informace najdete v [oznámení](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Další kroky

Pokud se chcete přispívat k dokumentaci Azure Automation, přečtěte si [příručku pro přispěvatele docs](/contribute/).
