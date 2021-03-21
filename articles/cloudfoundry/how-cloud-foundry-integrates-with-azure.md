---
title: Jak se Cloud Foundry integruje s Azure | Microsoft Docs
description: Popisuje, jak Cloud Foundry možné využívat služby Azure ke zlepšení podnikového prostředí.
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: ff2a6618b60ff2cfa5faa74c905e140466a14359
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563315"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrace Cloud Foundry s Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) je PaaS platforma, která běží na IaaS platformě poskytovatelů cloudu. Nabízí konzistentní prostředí pro nasazení aplikací napříč poskytovateli cloudu. Může se také integrovat s různými službami Azure s vysokou dostupností, škálovatelností a úsporou nákladů na podnikové úrovni.
Existují [6 subsystémů Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), které je možné pružně škálovat online, včetně: směrování, ověřování, Správa životního cyklu aplikací, Správa služeb, zasílání zpráv a monitorování. Pro každý z těchto subsystémů můžete nakonfigurovat Cloud Foundry, aby používaly korespondenční službu Azure. 

![Cloud Foundry v architektuře Integrace Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. vysoká dostupnost a škálovatelnost
### <a name="managed-disk"></a>Spravovaný disk
Bosh používá Azure CPI (rozhraní poskytovatele cloudu) pro vytváření a odstraňování rutin na disku. Ve výchozím nastavení se používají nespravované disky. Vyžaduje, aby zákazník vytvořil účty úložiště ručně, a pak nakonfiguruje účty v souborech manifestu CF. Důvodem je omezení počtu disků na účet úložiště.
K dispozici je teď [spravovaný disk](https://azure.microsoft.com/services/managed-disks/) , který nabízí spravované zabezpečené a spolehlivé diskové úložiště pro virtuální počítače. Zákazník už nebude muset řešit účet úložiště pro škálování a HA. Azure uspořádá disky automaticky. Bez ohledu na to, jestli se jedná o nové nebo existující nasazení, Azure CPI zpracuje vytváření nebo migraci spravovaného disku během nasazování CF. Podporuje se s PCF 1,11. Můžete také prozkoumat dokumentaci Open Source Cloud Foundry [Managed disks](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) pro referenci. 
### <a name="availability-zone-"></a>Zóna dostupnosti *
Jako platforma pro nativní cloudové platformy je Cloud Foundry navržena se [čtyřmi úrovněmi vysoké dostupnosti](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Zatímco první tři úrovně chyb softwaru mohou být zpracovány samotným systémem CF, odolnost proti chybám platformy je poskytována poskytovateli cloudu. Komponenty Key CF by měly být chráněné řešením HA pro platformu poskytovatele cloudu. Patří sem GoRouters, Diegu Mozeks, CF Database a Service dlaždice. Ve výchozím nastavení se [Skupina dostupnosti Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) používá pro odolnost proti chybám mezi clustery v datovém centru.
Dobrým novým je, že se teď uvolní [zóna dostupnosti Azure](../availability-zones/az-overview.md) , která přináší odolnost proti chybám na vyšší úroveň s nízkou latencí napříč datovými centry.
Zóna dostupnosti Azure dosahuje HA tím, že do dvou a datových center umístí sadu virtuálních počítačů. Každá sada virtuálních počítačů je redundantní pro jiné sady. Pokud je jedna zóna mimo provoz, ostatní sady jsou stále živé, izolované od havárie.
> [!NOTE] 
> Zóna dostupnosti Azure ještě není nabízená všem oblastem, podívejte se na nejnovější [oznámení v seznamu podporovaných oblastí](../availability-zones/az-overview.md). V případě Open Source Cloud Foundry ověřte v [oblasti dostupnosti v Azure informace o open source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. síťové směrování
Ve výchozím nastavení se nástroj pro vyrovnávání zatížení Azure Basic používá pro příchozí požadavky na CF API/aplikace a předává je do Gorouters. Komponenty CF, jako je Diegu mozek, MySQL, ERT, mohou také použít nástroj pro vyrovnávání zatížení k vyvážení provozu pro HA. Azure také poskytuje sadu plně spravovaných řešení vyrovnávání zatížení. Pokud hledáte zpracování pomocí protokolu TLS/SSL ("snižování zátěže SSL") nebo na požadavky HTTP/HTTPS, zvažte Application Gateway. Pro zajištění vysoké dostupnosti a škálovatelnosti vyrovnávání zatížení ve vrstvě 4 zvažte standardní nástroj pro vyrovnávání zatížení.
### <a name="azure-application-gateway-"></a>Application Gateway Azure *
[Azure Application Gateway](../application-gateway/overview.md) nabízí různé možnosti vyrovnávání zatížení vrstvy 7, včetně snižování zátěže SSL, koncového a koncového protokolu TLS, brány firewall webových aplikací, spřažení relací na základě souborů cookie a dalších. Application Gateway můžete [nakonfigurovat v Cloud Foundry Open Source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Pro PCF si Projděte zpráva k  [vydání verze PCF 2,1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) pro test ověření koncepce.

### <a name="azure-standard-load-balancer-"></a>Standard Load Balancer Azure *
Azure Load Balancer je nástroj pro vyrovnávání zatížení vrstvy 4. Slouží k distribuci provozu mezi instancemi služeb v sadě s vyrovnáváním zatížení. Standardní verze poskytuje [Pokročilé funkce](../load-balancer/load-balancer-overview.md) nad základní verzí. Například 1. Maximální limit back-end fondu je vyvolán z 100 na 1000 virtuálních počítačů.  2. Koncové body teď podporují více skupin dostupnosti místo jedné skupiny dostupnosti.  3. Další funkce, jako jsou porty HA, rozsáhlejší monitorovací data atd. Pokud přesouváte do zóny dostupnosti Azure, vyžaduje se standardní nástroj pro vyrovnávání zatížení. Pro nové nasazení doporučujeme začít s Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. ověřování 
[Cloud Foundry uživatelský účet a ověřování](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) je služba správy centrální identity pro CF a její různé komponenty. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) je víceklientské cloudové služby a služba pro správu identit od Microsoftu. Ve výchozím nastavení se pro Cloud Foundry ověřování používá UAA. Jako pokročilá možnost podporuje i Azure AD úložiště externích uživatelů. Uživatelé Azure AD mají přístup k Cloud Foundry pomocí své identity LDAP bez účtu Cloud Foundry. Pomocí těchto kroků můžete [nakonfigurovat Azure AD pro UAA v PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. úložiště dat pro systém Cloud Foundry runtime
Cloud Foundry nabízí skvělou rozšiřitelnost pro používání Azure SAS nebo Azure MySQL/PostgreSQL Services pro úložiště systémových aplikací modulu runtime.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure SAS pro Cloud Foundry SAS Cloud Controller
Cloud Controller SAS je důležité úložiště dat pro buildpacks, droplety, balíčky a fondy zdrojů. Ve výchozím nastavení se server NFS používá pro SAS adaptéru cloudu. Aby nedocházelo k jednomu bodu selhání, použijte Azure Blob Storage jako externí úložiště. Projděte si [dokumentaci k Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) pro pozadí a [Možnosti v Cloud Foundry pivoted](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>Databáze MySQL/PostgreSQL jako Cloud Foundry databáze elastického běhu *
Elastický modul runtime pro CF vyžaduje dvě hlavní systémové databáze:
#### <a name="ccdb"></a>CCDB 
Databáze řadiče cloudu.  Cloud Controller poskytuje REST API koncovým bodům pro klienty pro přístup k systému. CCDB ukládá tabulky pro organizace, prostory, služby, role uživatelů a další pro cloudový adaptér.
#### <a name="uaadb"></a>UAADB 
Databáze pro uživatelský účet a ověřování. Ukládá data související s ověřováním uživatele, například šifrovaná uživatelská jména a hesla.

Ve výchozím nastavení je možné použít databázi MySQL (Local System Database). V případě HA a škálování použijte Azure Managed MySQL nebo PostgreSQL Services. Tady je pokyn pro [Povolení Azure MySQL/PostgreSQL pro CCDB, UAADB a další systémové databáze s Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Otevřete Service Broker
Azure Service Broker nabízí konzistentní rozhraní pro správu přístupu aplikací ke službám Azure. Nový [otevřený Service Broker pro Azure Project](https://github.com/Azure/open-service-broker-azure) nabízí jediný a jednoduchý způsob poskytování služeb aplikacím v rámci Cloud Foundry, OpenShift a Kubernetes. Pokyny k nasazení v PCF najdete na [dlaždici Azure Open Service Broker pro PCF](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) .

## <a name="6-metrics-and-logging"></a>6. metriky a protokolování
Trysek služby Azure Log Analytics je Cloud Foundry komponenta, která předá metriky z [Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) do [protokolů Azure monitor](https://azure.microsoft.com/services/log-analytics/). Pomocí této trysky můžete shromažďovat, zobrazovat a analyzovat stav systému CF a metriky výkonu napříč různými nasazeními.
Kliknutím [sem](./cloudfoundry-oms-nozzle.md) se dozvíte, jak nasadit trysku služby Azure Log Analytics do Open Source a pivoted Cloud Foundry prostředí a pak přistupovat k datům z konzoly protokolů Azure monitor. 
> [!NOTE]
> Z PCF 2,0 jsou metriky stavu BOSH pro virtuální počítače předávány ve výchozím nastavení Loggregator firehose a jsou integrovány do konzoly Azure Monitor protokoly.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. úspora nákladů
### <a name="cost-saving-for-devtest-environments"></a>Úspora nákladů pro prostředí pro vývoj a testování
#### <a name="b-series-"></a>Řady B-Series: *
I když byly pro Pivoting Cloud Foundry produkčního prostředí běžně doporučovány série virtuálních počítačů F a D, nová možnost "s možností rozšíření [B-Series](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) " přináší nové možnosti. Virtuální počítače s podporou řady B-Series jsou ideální pro úlohy, které nepotřebují průběžný výkon procesoru, jako jsou webové servery, malé databáze a vývojové a testovací prostředí. Tyto úlohy mají typicky požadavky na výkon při roztržení. Ve srovnání s $0,05/hod (F1) je ve srovnání se $0.012/hod. Další informace najdete v úplném seznamu velikostí a [cen](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) [virtuálních počítačů](../virtual-machines/sizes-general.md) . 
#### <a name="managed-standard-disk"></a>Spravovaný standardní disk: 
Pro zajištění spolehlivého výkonu v produkčním prostředí byly doporučeny disky Premium.  Se [spravovaným diskem](https://azure.microsoft.com/services/managed-disks/)může standard Storage také poskytovat podobnou spolehlivost s jiným výkonem. Pro úlohy, které nejsou citlivé na výkon, jako je vývoj a testování nebo méně důležité prostředí, nabízejí spravované standardní disky alternativní možnost s nižšími náklady.  
### <a name="cost-saving-in-general"></a>Obecně se ukládají náklady. 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Významné náklady na virtuální počítače se ukládají pomocí rezervací Azure: 
V dnešní době se všechny virtuální počítače CF účtují pomocí cen na vyžádání, a to i v případě, že prostředí obvykle zůstanou neomezeně. Nyní můžete rezervovat kapacitu virtuálního počítače na 1 nebo 3 roky a získat slevy 45-65%. Slevy se používají v systému fakturace, a to bez jakýchkoli změn v prostředí. Podrobnosti najdete v tématu [Jak funguje rezervace Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Spravovaný disk úrovně Premium s menšími velikostmi: 
Spravované disky podporují menší velikosti disků, například P4 (32 GB) a P6 (64 GB) pro disky úrovně Premium i Standard. Pokud máte malé úlohy, můžete při migraci z disků Standard Premium na spravované disky Premium ušetřit náklady.
#### <a name="use-azure-first-party-services"></a>Používejte služby Azure First stran: 
Využití služby Azure First večírek snižuje náklady na dlouhodobou správu, kromě HA a spolehlivosti uvedených v předchozích částech. 

Na pivotu bylo zahájeno [malé nároky ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) pro zákazníky PCF. komponenty jsou společně umístěny do pouhých 4 virtuálních počítačů a jsou spuštěny až 2500 instancí aplikací. Zkušební verze je teď k dispozici prostřednictvím [místa na trhu Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Další kroky
Funkce Integrace Azure jsou nejdřív k dispozici s [Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), než budou k dispozici na pivoted Cloud Foundry. Funkce označené symbolem * stále nejsou k dispozici prostřednictvím PCF. Cloud Foundry integrace s Azure Stack není v tomto dokumentu zahrnutá ani jedna z nich.
Pokud chcete PCF podporu pro funkce označené * nebo Cloud Foundry integrace s Azure Stack, obraťte se na Pivoted and účet Microsoft Manager, kde najdete nejnovější stav.