---
title: Integrace cloudové slévárna s Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak může Cloud Foundry používat služby Azure k vylepšení podnikového prostředí.
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 04ef72f7ec70b370305395ae8de8180f4594b43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277347"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrace Cloud Foundry s Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) je platforma PaaS, která běží nad platformou IaaS poskytovatelů cloudu. Nabízí konzistentní možnosti nasazení aplikací napříč poskytovateli cloudu. Může se také integrovat s různými službami Azure s podnikovou dostupností, škálovatelností a úsporami nákladů.
Existuje [6 subsystémů Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), které lze flexibilně škálovat online, včetně: směrování, ověřování, správa životního cyklu aplikací, správa služeb, zasílání zpráv a monitorování. Pro každý subsystém můžete nakonfigurovat Cloud Foundry tak, aby používalkorespondenční službu Azure. 

![Cloud Foundry v architektuře integrace Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Vysoká dostupnost a škálovatelnost
### <a name="managed-disk"></a>Spravovaný disk
Bosh používá Azure CPI (Cloud Provider Interface) pro vytváření disků a odstranění rutiny. Ve výchozím nastavení se používají nespravované disky. Vyžaduje, aby zákazník ručně vytvořit účty úložiště, pak nakonfigurovat účty v souborech manifestu CF. Důvodem je omezení počtu disků na účet úložiště.
Nyní je k dispozici [spravovaný disk,](https://azure.microsoft.com/services/managed-disks/) nabízí spravované zabezpečené a spolehlivé diskové úložiště pro virtuální počítače. Zákazník již nemusí řešit účet úložiště pro stupnici a HA. Azure uspořádá disky automaticky. Ať už se jedná o nové nebo existující nasazení, Azure CPI bude zpracovávat vytvoření nebo migraci spravovaného disku během nasazení CF. Je podporován pcf 1.11. Můžete také prozkoumat pokyny spravovaného [disku](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) cloudové slévárny s otevřeným zdrojovým kódem. 
### <a name="availability-zone-"></a>Zóna dostupnosti *
Cloud Foundry je jako platforma nativní pro cloud navržena se [čtyřmi úrovněmi vysoké dostupnosti](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Zatímco první tři úrovně selhání softwaru mohou být zpracovány samotným systémem CF, odolnost proti chybám platformy zajišťují poskytovatelé cloudu. Klíčové komponenty CF by měly být chráněny pomocí řešení HA platformy poskytovatele cloudu. To zahrnuje GoRouters, Diego Brains, CF databáze a služby dlaždice. Ve výchozím nastavení [se sada dostupnosti Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) používá pro odolnost proti chybám mezi clustery v datovém centru.
Dobré nové je, [Azure Dostupnost zóna](https://docs.microsoft.com/azure/availability-zones/az-overview ) je uvolněna nyní, čímž odolnost proti chybám na další úroveň, s nízkou latencí redundance napříč datovými centry.
Zóna dostupnosti Azure dosahuje HA umístěním sady virtuálních počítačů do 2+ datových center, každá sada virtuálních počítačů je redundantní pro jiné sady. Pokud je jedna zóna mimo, ostatní sady jsou stále živé, izolované od katastrofy.
> [!NOTE] 
> Zóna dostupnosti Azure se zatím nenabízí všem oblastem, podívejte se na nejnovější [oznámení pro seznam podporovaných oblastí](https://docs.microsoft.com/azure/availability-zones/az-overview). Open Source Cloud Foundry najdete [v Azure Availability Zone, kde najdete pokyny](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone)pro open source Cloud Foundry .

## <a name="2-network-routing"></a>2. Síťové směrování
Ve výchozím nastavení se základní vyvyřič zatížení Azure používá pro příchozí požadavky CF API/aplikací a přeposílá je gorouterům. Cf komponenty jako Diego Brain, MySQL, ERT můžete také použít nástroje pro vyrovnávání zatížení k vyrovnání provozu pro HA. Azure také poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud hledáte ukončení TLS ("Snížení zátěže SSL") nebo na zpracování aplikační vrstvy požadavku HTTP/HTTPS, zvažte aplikační bránu. Pro vysokou dostupnost a škálovatelnost vyrovnávání zatížení na vrstvě 4 zvažte standardní vyvažovač evidenční stav zatížení.
### <a name="azure-application-gateway-"></a>Aplikační brána Azure *
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) nabízí různé možnosti vyrovnávání zatížení vrstvy 7, včetně snižování zátěže SSL, ssl od konce, brány firewall webových aplikací, spřažení relací na základě souborů cookie a další. Aplikační [bránu můžete konfigurovat ve slézavce cloudu s otevřeným zdrojovým kódem](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). U PCF zkontrolujte [poznámky k verzi PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) pro test POC.

### <a name="azure-standard-load-balancer-"></a>Standardní vyrovnávání zatížení Azure *
Azure Load Balancer je vyrovnávání zatížení vrstvy 4. Používá se k distribuci provozu mezi instance služeb v sadě s vyrovnáváním zatížení. Standardní verze poskytuje [pokročilé funkce](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) nad základní verzí. Například 1. Maximální limit back-endového fondu se zvýší ze 100 na 1000 virtuálních můe.  2. Koncové body nyní podporují více sad dostupnosti namísto jedné sady dostupnosti.  3. Další funkce, jako jsou porty HA, bohatší data monitorování a tak dále. Pokud přecházíte do zóny dostupnosti Azure, je vyžadován standardní vyrovnávání zatížení. Pro nové nasazení doporučujeme začít s Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. Autentizace 
[Cloud Foundry User Account and Authentication](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) je centrální služba správy identit pro CF a jeho různé komponenty. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je služba Microsoft u víceklientů, cloudových adresářů a identit. Ve výchozím nastavení se UAA používá pro ověřování slévárnou v cloudu. Jako upřesňující možnost UAA také podporuje Azure AD jako externí úložiště uživatelů. Uživatelé Azure AD mají přístup ke cloudové slétovce pomocí své identity LDAP bez účtu Cloud Foundry. Následujícím postupem [nakonfigurujte Azure AD pro ZAA v PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Ukládání dat pro cloudový slévárna Runtime systém
Cloud Foundry nabízí skvělou rozšiřitelnost pro použití Azure blobstore nebo Služby Azure MySQL/PostgreSQL pro úložiště systému runtime aplikací.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Blobstore Azure pro cloudový cloudový řadič blobstore
Cloud Controller blobstore je důležité úložiště dat pro buildpacks, droplety, balíčky a fondy prostředků. Ve výchozím nastavení se server systému nfs používá pro úložiště objektů blobstore cloudového řadiče. Chcete-li se vyhnout jedinému bodu selhání, použijte Azure Blob Storage jako externí úložiště. Podívejte se na [dokumentaci cloudové slévárny](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) pro pozadí a [možnosti v Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL jako cloudová slévárna elastická databáze běhu *
CF Elastic Runtime vyžaduje dvě hlavní systémové databáze:
#### <a name="ccdb"></a>CCDB 
Databáze cloudového řadiče.  Cloud Controller poskytuje koncovým bodům rozhraní REST API pro klienty pro přístup k systému. CCDB ukládá tabulky pro organizace, prostory, služby, role uživatelů a další pro řadič cloudu.
#### <a name="uaadb"></a>UAADB 
Databáze uživatelského účtu a ověřování. Ukládá data související s ověřováním uživatelů, například šifrovaná uživatelská jména a hesla.

Ve výchozím nastavení lze použít místní systémovou databázi (MySQL). Pro HA a škálování využijte služby MySQL spravované Azure morem nebo PostgreSQL. Zde je instrukce [umožňující Azure MySQL/PostgreSQL pro CCDB, UAADB a další systémové databáze s Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Zprostředkovatel otevřených služeb
Azure service broker nabízí konzistentní rozhraní pro správu přístupu aplikací ke službám Azure. Nový [projekt Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure) poskytuje jediný a jednoduchý způsob poskytování služeb aplikacím napříč Cloud Foundry, OpenShift a Kubernetes. Pokyny k nasazení na PCF najdete na [dlaždici Azure Open Service Broker for PCF.](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure)

## <a name="6-metrics-and-logging"></a>6. Metriky a protokolování
Tryska Azure Log Analytics je součást Cloud Foundry, která předává metriky z [loggregator firehotoru Cloud Foundry](https://docs.cloudfoundry.org/loggregator/architecture.html) do [protokolů Azure Monitor](https://azure.microsoft.com/services/log-analytics/). S tryskou můžete shromažďovat, zobrazovat a analyzovat metriky stavu a výkonu systému CF napříč několika nasazeními.
Kliknutím [sem](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) se dozvíte, jak nasadit trysku Azure Log Analytics do prostředí Open Source i Pivotal Cloud Foundry a pak získat přístup k datům z konzoly protokolů Azure Monitor. 
> [!NOTE]
> Z PCF 2.0 metriky stavu BOSH pro virtuální počítače jsou předávány loggregator Firehose ve výchozím nastavení a jsou integrovány do konzoly protokolů Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. Úspora nákladů
### <a name="cost-saving-for-devtest-environments"></a>Úspora nákladů pro vývoj/testování prostředí
#### <a name="b-series-"></a>Řada B: *
Zatímco řady VM F a D byly běžně doporučovány pro produkční prostředí Pivotal Cloud Foundry, nová "burstable" [řada B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) přináší nové možnosti. Roztržené virtuální počítače řady B jsou ideální pro úlohy, které nepotřebují plný výkon procesoru nepřetržitě, jako jsou webové servery, malé databáze a vývojová a testovací prostředí. Tyto úlohy mají obvykle sknutelné požadavky na výkon. Je to $0.012/hour (B1) ve srovnání s $0.05/hour (F1), podrobnosti najdete v úplném seznamu [velikostí virtuálních](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) zařízení a [cen.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 
#### <a name="managed-standard-disk"></a>Spravovaný standardní disk: 
Prémiové disky byly doporučeny pro spolehlivý výkon ve výrobě.  Se [spravovaným diskem](https://azure.microsoft.com/services/managed-disks/)může standardní úložiště také poskytovat podobnou spolehlivost s různým výkonem. Pro úlohy, které nejsou citlivé na výkon, jako je vývoj a testování nebo nekritické prostředí, spravované standardní disky nabízejí alternativní možnost s nižšími náklady.  
### <a name="cost-saving-in-general"></a>Úspora nákladů obecně 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Významná úspora nákladů virtuálního počítače s rezervacemi Azure: 
Dnes se všechny virtuální aplikace CF účtují pomocí cen "na vyžádání", i když prostředí obvykle zůstávají vzhůru neomezeně dlouho. Nyní můžete rezervovat kapacitu virtuálního počítače na období 1 nebo 3 let a získat slevy ve výši 45–65 %. Slevy se uplatňují ve fakturačním systému bez evidenčních změn ve vašem prostředí. Podrobnosti najdete v tématu [Jak fungují rezervace Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Spravovaný disk Premium s menšími velikostmi: 
Spravované disky podporují menší velikosti disků, například P4(32 GB) a P6(64 GB) pro prémiové i standardní disky. Pokud máte malé úlohy, můžete ušetřit náklady při migraci ze standardních disků premium na spravované disky premium.
#### <a name="use-azure-first-party-services"></a>Používejte služby Azure první strany: 
Využití služby první strany Azure sníží dlouhodobé náklady na správu, kromě HA a spolehlivosti uvedené ve výše uvedených částech. 

Pivotal spustila [malé stopy ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) pro zákazníky PCF, komponenty jsou co-umístěný do pouhých 4 virtuálních počítačů, běží až 2500 instancí aplikací. Zkušební verze je teď dostupná přes [Azure Market.](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)

## <a name="next-steps"></a>Další kroky
Funkce integrace Azure jsou k dispozici jako první s [Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), než bude k dispozici na Pivotal Cloud Foundry. Funkce označené * stále nejsou k dispozici prostřednictvím PCF. Integrace Cloud Foundry s Azure Stack není zahrnuta v tomto dokumentu buď.
Chcete-li podporu PCF na funkce označené *, nebo Cloud Foundry integrace s Azure Stack, obraťte se na pivotal a Správce účtu Microsoft pro nejnovější stav. 

