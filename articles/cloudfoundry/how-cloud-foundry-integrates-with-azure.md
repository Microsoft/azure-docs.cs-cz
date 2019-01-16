---
title: Jak se integruje s Azure Cloud Foundry | Dokumentace Microsoftu
description: Popisuje, jak využít služby Azure a zlepšit tak prostředí Enterprice Cloud Foundry
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 908b7e40c0509d7034b86985ac0775635726a6b9
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329799"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrace Cloud Foundry s Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) je spuštěn na platformě poskytovatelů cloudu IaaS platformě PaaS. Nabízí komfortem při nasazování aplikace konzistentní napříč poskytovatelů cloudových služeb. Kromě toho můžete také integrovat s různými službami Azure, podnikové vysokou dostupnost, škálovatelnost a úspor nákladů.
Existují [6 subsystémy Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), který může být flexibilně škálování online, včetně: Směrování, ověřování, Správa životního cyklu aplikací, správa, monitorování a zasílání zpráv. Pro každou subsystémů můžete nakonfigurovat Cloud Foundry využívat zpravodaj služby Azure. 

![Cloud Foundry v Azure integrace architektury](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Vysoká dostupnost a škálovatelnost
### <a name="managed-disk"></a>Spravovaný Disk
Bosh využívá Azure ISP (rozhraní poskytovatele cloudu) pro vytvoření disku a odstraňování rutiny. Ve výchozím nastavení se používají nespravované disky. Zákazník ručně vytvářet účty úložiště a pak nakonfigurujte účty v souborech manifestu CF vyžaduje. Toto je z důvodu omezení počtu disků v účtu úložiště.
Nyní [spravovaného disku](https://azure.microsoft.com/services/managed-disks/) nabídkami spravovaného disku bezpečné a spolehlivé úložiště pro virtuální počítače, je k dispozici. Zákazník nemusí se účet úložiště pro škálování a vysokou dostupnost. Azure automaticky uspořádá disky. Ať už nové nebo stávající nasazení, bude zpracovávat ISP Azure vytvoření či migrace spravovaných disků při nasazení aplikace CF. Jsou podporovány pro PCF 1.11. Můžete si taky prostudovat Cloud Foundry open source [spravovaného disku pokyny](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) pro referenci. 
### <a name="availability-zone-"></a>Zóna dostupnosti *
Jako platforma pro aplikace nativní pro cloud, Cloud Foundry je navržená s [čtyři úroveň vysoké dostupnosti](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Při první tři úrovně chyby softwaru mohou být zpracovány CF samotný systém, platformu odolnost proti chybám poskytuje poskytovatelů cloudových služeb. Klíčové komponenty CF by měly být chráněny pomocí platformy poskytovatele cloudových řešení s vysokou DOSTUPNOSTÍ. To zahrnuje GoRouters, mozky Diegu, CF databázi a službu dlaždice. Ve výchozím nastavení [dostupnosti Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) se používá pro odolnost proti chybám mezi clustery v datovém centru.
Je dobré nové [zóny dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview ) vydání nyní přináší odolnost proti chybám na další úroveň s nízkou latencí redundance napříč datovými centry.
Zóny dostupnosti Azure HA dosahuje tak, že sada virtuálních počítačů do více než 2 datových center, každá sada virtuálních počítačů jsou redundantní do jiné sady. Jednu zónu je vypnutý, ostatní sady jsou ještě živá, izolovaně od po havárii.
> [!NOTE] 
> Zóny dostupnosti Azure není ještě dostupná do všech oblastí, zkontrolujte nejnovější [oznámení pro seznam podporovaných oblastí](https://docs.microsoft.com/azure/availability-zones/az-overview). Otevřete zdrojový Cloud Foundry, zkontrolujte [zóny dostupnosti Azure Cloud Foundry pokyny opensourcových](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Směrováním v síti
Ve výchozím nastavení se používá Azure load balancer úrovně basic pro příchozí požadavky CF/aplikace API a předává je Gorouters. CF komponenty, jako jsou Brain Diegu, MySQL, ERT můžete také použít nástroj pro vyrovnávání zatížení pro provoz pro vysokou dostupnost. Azure navíc poskytuje sadu plně spravované řešení vyrovnávání zatížení. Pokud chcete pro ukončení protokolu TLS ("přesměrování zpracování SSL") nebo za zpracování vrstvy aplikace požadavku HTTP/HTTPS, vezměte v úvahu Application Gateway. Pro vysokou dostupnost a škálovatelnost zátěže na vrstvy 4 vezměte v úvahu load balanceru úrovně standard.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) nabízí různé možnosti, včetně snižování zátěže SSL, kompletního SSL, Firewall webových aplikací, spřažení relace na základě souborů cookie a další vyrovnávání zatížení vrstvy 7. Je možné [konfigurace aplikační brány v otevřít zdrojový Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). PCF, zkontrolujte [zpráva k vydání verze PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) POC testu.

### <a name="azure-standard-load-balancer-"></a>Azure Load balancer úrovně Standard *
Nástroj Azure Load Balancer je nástroj pro vyrovnávání zatížení vrstvy 4. Používá se za účelem distribuce provozu mezi instance služeb v sadě s vyrovnáváním zatížení. Standardní verze nabízí [pokročilé funkce](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) nad základní verze. Příklad 1. Maximální limit fondu back-end je vyvolávána s cílem 1 000 virtuálních počítačů ze 100.  2. Koncové body nyní podporují více skupinám dostupnosti místo jedné skupiny dostupnosti.  3. Další funkce, jako jsou porty s vysokou DOSTUPNOSTÍ, bohatší data monitorování, atd. Přesunutí do zóny dostupnosti Azure load balancer úrovně standard je povinné. Pro nové nasazení doporučujeme začít s Azure Load balancer úrovně Standard. 

## <a name="3-authentication"></a>3. Authentication 
[Cloud Foundry uživatelský účet a ověřování](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) je služba pro správu identit centrální pro CF a jeho různé součásti. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je služba Microsoftu pro více tenantů, cloudového adresáře a identity management. Ve výchozím nastavení se UAA používá k ověřování Cloud Foundry. Jako upřesňující možnosti UAA také podporuje Azure AD jako úložiště externí uživatele. Uživatelé Azure AD můžete přistupovat pomocí svoji identitu LDAP, bez účtu Cloud Foundry Cloud Foundry. Postupujte podle těchto kroků [konfigurace služby Azure AD pro UAA v PCF](http://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Úložiště dat pro Cloud Foundry běhu systému
Cloud Foundry nabízí skvělé rozšiřitelnosti pro použití úložiště objektů BLOB Azure nebo služby Azure MySQL nebo PostgreSQL pro aplikace modulu runtime systému úložiště.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Úložiště Azure objektů blob pro Cloud Foundry cloudu řadič úložiště objektů BLOB
Kontroler cloudové úložiště objektů blob je důležitá data úložiště pro buildpacks, droplety, balíčky a fondy zdrojů. Ve výchozím nastavení se používá server systému souborů NFS pro kontroler cloudové úložiště objektů BLOB. Abyste se vyhnuli jediným bodem selhání, použijte jako externí úložiště Azure Blob Storage. Podívejte se [dokumentace ke Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) pozadí, a [možnosti v Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL nebo PostgreSQL jako elastický Cloud Foundry provozovat čas databázi *
Elastické Runtime CF vyžaduje dvě důležité systémy databáze:
#### <a name="ccdb"></a>CCDB 
Kontroler cloudové databáze.  Adaptér cloud poskytuje koncových bodů rozhraní REST API pro klienty pro přístup k systému. CCDB ukládá tabulek pro organizace, mezery, služby, role uživatelů a více pro kontroler cloudu.
#### <a name="uaadb"></a>UAADB 
Databáze pro uživatelský účet a ověřování. Ukládají se ověřování uživatelů související data, například šifrovaná uživatelská jména a hesla.

Ve výchozím nastavení můžete použít místní systémovou databázi (MySQL). Pro vysokou dostupnost a škálování, využívají Azure spravované MySQL nebo PostgreSQL služby. Tady je podle pokynů z [povolení Azure MySQL nebo PostgreSQL pro CCDB, UAADB a jiné systémové databáze s otevřít zdrojový Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Technologie Open Service Broker
Služby Azure service broker nabízí jednotné rozhraní pro správu aplikace přístup ke službám Azure. Nové [Open Service Broker for Azure projektu](https://github.com/Azure/open-service-broker-azure) poskytuje jednotné a jednoduchý způsob, jak poskytovat služby do aplikací přes Cloud Foundry, OpenShift a Kubernetes. Zobrazit [Azure Open Service Broker pro dlaždici PCF](https://network.pivotal.io/products/azure-open-service-broker-pcf/) pokyny nasazení PCF.

## <a name="6-metrics-and-logging"></a>6. Metriky a protokolování
Azure Log Analytics Nozzle je komponenta Cloud Foundry, která předává metriky z [Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) k [Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/). Nozzle můžete shromažďovat, zobrazit a analyzovat metriky stavu a výkonu systému CF mezi několika nasazeními.
Klikněte na tlačítko [tady](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) se dozvíte, jak nasadit Azure Log Analytics Nozzle pro Open Source a Pivotal Cloud Foundry prostředí a pak přístup k datům z konzoly Azure Log Analytics. 
> [!NOTE]
> Z verze PCF 2.0 BOSH metrik stavu pro virtuální počítače se předávají do Loggregator Firehose ve výchozím nastavení a jsou integrované do konzoly Azure Log Analytics.

## <a name="7-cost-saving"></a>7. Úspory nákladů
### <a name="cost-saving-for-devtest-environments"></a>Pro prostředí pro vývoj/testování pro úsporu nákladů
#### <a name="b-series-"></a>Řady B-Series: *
Zatímco řady F a virtuálních počítačů D byly běžně doporučuje pro produkční prostředí Pivotal Cloud Foundry nové "zvládáním výkonových špiček" [řady B-series](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) přináší nové možnosti. Zvládáním výkonových špiček virtuální počítače řady B-series jsou ideální pro úlohy, které nejsou nutné plný výkon procesoru nepřetržitě, jako jsou webové servery, malé databáze a vývoj a testovací prostředí. Tyto úlohy mají obvykle požadavky na výkon zvládáním výkonových špiček. Je 0,012 $za hodinu (B1) ve srovnání s 0,05 USD za hodinu (F1), podívejte se seznam všech [velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) a [ceny](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) podrobnosti. 
#### <a name="managed-standard-disk"></a>Spravovaných disků úrovně Standard: 
Disky Premium se doporučuje pro zajištění spolehlivého výkonu v produkčním prostředí.  S [spravovaného disku](https://azure.microsoft.com/services/managed-disks/), služba storage úrovně standard může také poskytovat podobné spolehlivost, jiné výkonové. Pro úlohy, které nejsou závislé na výkonu, jako je vývoj a testování nebo méně náročné prostředí spravované disky úrovně standard nabízí alternativní možnost s nižšími náklady.  
### <a name="cost-saving-in-general"></a>Obecně úspory nákladů 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Významné VM náklady na ukládání s Azure rezervace: 
Dnes, všechny virtuální počítače CF se účtuje pomocí "on-demand" cen, i když v prostředí obvykle zůstat po neomezenou dobu. Teď můžete rezervovat kapacitu virtuálních počítačů na 1 nebo 3 roky období a získejte slevy 45 – 65 %. Slevy fakturačního systému beze změny do svého prostředí. Podrobnosti najdete v tématu [jak Azure funguje rezervace](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Spravovaný Disk úrovně Premium s menší velikostí: 
Spravované disky podporu menší velikosti disků, třeba P4(32 GB) a P6(64 GB) pro disky standard i premium. Pokud máte malé úlohy, ušetříte náklady při migraci ze standardní prémiové disky na spravované prémiové disky.
#### <a name="utilizing-azure-first-party-services"></a>Využívání služeb Azure první strany: 
Využití výhod Azure služba Microsoftu sníží dlouhodobé správy nákladů, kromě vysokou dostupnost a spolehlivost v uvedených částech. 

Má Pivotalu spustili [malé nároky na místo ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) pro zákazníky PCF komponenty jsou umístěné do jenom 4 virtuální počítače spuštěné instance až 2500 aplikací. Zkušební verze je teď k dispozici prostřednictvím [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Další kroky
Funkce integrace služby Azure jsou dostupné s nejprve [otevřít zdrojový Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), než bude k dispozici v Pivotal Cloud Foundry. Funkce označené * stále nejsou k dispozici prostřednictvím PCF. Cloud Foundry integrace s Azure Stack není buď zahrnuté v tomto dokumentu.
Pro podporu PCF na funkce označené *, nebo Cloud Foundry integrace s Azure Stackem, kontaktujte vašeho account manažera společnosti Pivotal a Microsoft pro nejnovější stav. 

