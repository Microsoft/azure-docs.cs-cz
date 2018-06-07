---
title: Jak Cloud Foundry integruje se službou Azure | Microsoft Docs
description: Popisuje, jak může utlize Foundry cloudu Azure services pro zajištění lepších možností Enterprice
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
ms.openlocfilehash: 1f4afbe1849210c55c392d014449224f2fe97b04
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654817"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrovat Azure Cloud Foundry

[Cloud Foundry](https://docs.cloudfoundry.org/) je platforma PaaS systémem nad poskytovatelů cloudu IaaS platformy. Nabízí možnosti nasazení aplikace konzistentní napříč poskytovatelů cloudu. Kromě toho můžete také integraci s různými službami Azure, s enterprise úrovni HA, škálovatelnosti a finanční úspoře.
Existují [6 subsystémy cloudu Foundry](https://docs.cloudfoundry.org/concepts/architecture/), který může být flexibilně škálování online, včetně: směrování, ověřování, Správa životního cyklu aplikací, Správa služby zasílání zpráv a monitorování. Pro každou subsystémů můžete nakonfigurovat Cloud Foundry využívat zpravodaj služby Azure. 

![Cloud Foundry na architektuře integrace se službou Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Vysoké dostupnosti a škálovatelnosti
### <a name="managed-disk"></a>Spravovaný Disk
Bosh využívá Azure ISP (Cloud poskytovatele rozhraní) pro disk vytváření a odstraňování rutiny. Ve výchozím nastavení, nespravované disky se používají. To vyžaduje zákazník ručně vytvořit účty úložiště, pak konfigurovat účty v souborech CR manifestu. To je kvůli omezení počtu disků na účet úložiště.
Nyní [spravované disku](https://azure.microsoft.com/services/managed-disks/) je k dispozici, nabízí spravovaných disků na zabezpečený a spolehlivý úložiště pro virtuální počítače. Jak nakládat s účtem úložiště pro škálování a HA už nemusí zákazníka. Azure automaticky uspořádá disky. Jestli je nový nebo existující nasazení, bude zpracovávat ISP Azure vytvoření či migrace spravovaného disku během CR nasazení. Je podporována s PCF 1.11. Můžete si také prostudovat Foundry cloudu open-source [doporučené spravované disku](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) pro referenci. 
### <a name="availability-zone-"></a>Dostupnost zóny *
Jako platformu nativní cloudové aplikace, je určená Foundry cloudu s [čtyři úroveň zajištění vysoké dostupnosti](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Během první tři úrovně chyby softwaru může zpracovávat vlastní CR systém, platformu odolnost proti chybám zajišťuje poskytovatelů cloudu. Klíčové komponenty CR by měly být chráněné pomocí poskytovatele cloudové platformy HA řešení. To zahrnuje GoRouters, Diego mozky, dlaždice CR služeb a databázi. Ve výchozím nastavení [sadu dostupnosti Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) se používá pro odolnost proti chybám mezi clustery v datovém centru.
Je dobré nové, [Azure dostupnost zóny](https://docs.microsoft.com/azure/availability-zones/az-overview ) vydání teď přináší odolnost proti chybám další úroveň, s nízkou latencí redundance napříč datovými centry.
Azure dostupnost zóny dosahuje HA umístění sadu virtuálních počítačů do 2 + datových center, každou sadu virtuálních počítačů jsou redundantní pro jiné skupiny. Jednu zónu je vypnutý, jiné sady jsou stále aktivní, izolovaně od po havárii.
> [!NOTE] 
> Dostupnost zóny Azure není ještě nabízí ke všem oblastem, zkontrolujte nejnovější [oznámení pro seznam podporovaných oblastí](https://docs.microsoft.com/azure/availability-zones/az-overview). Otevřete zdrojový Cloud Foundry zkontrolujte [zóny dostupnosti Azure Cloud Foundry pokyny s otevřeným zdrojem](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Směrování sítě
Ve výchozím nastavení nástroj pro vyrovnávání zatížení Azure základní slouží pro příchozí požadavky CR/aplikace API a předává je Gorouters. CR komponenty, jako jsou Diego mozku, MySQL, Vložit můžete také použít nástroje pro vyrovnávání zatížení k vyrovnávání přenosů pro HA. Kromě toho Azure poskytuje sadu plně spravovaná řešení vyrovnávání zatížení. Pokud hledáte pro ukončení protokolu TLS ("přesměrování zpracování SSL") nebo za zpracování vrstvy aplikace žádosti protokolu HTTP nebo HTTPS, zvažte Application Gateway. Pro vysokou dostupnost a škálovatelnost rozložení zátěže na vrstvy 4 zvažte standardní vyrovnávání zátěže.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Služba Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) nabízí různé vrstvy 7 možnosti vyrovnávání zatížení, včetně SSL snižování zátěže, konce protokolu SSL, brány Firewall webových aplikací, spřažení relace na základě souborů cookie a další. Můžete [konfigurace aplikační brány v otevřít zdrojový Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). PCF, zkontrolujte [poznámky k verzi PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) pro testování Koncepce test.

### <a name="azure-standard-load-balancer-"></a>Nástroje pro vyrovnávání zatížení Azure standardní *
Azure Vyrovnávání zatížení je nástroj pro vyrovnávání zatížení vrstvy 4. Slouží k distribuci přenosů mezi instancemi služeb v sadě s vyrovnáváním zatížení. Obsahuje standardní verzi [pokročilé funkce](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) na základní verzi. Například 1. Maximální limit fondu back-end je vyvolána od 100 do 1 000 virtuálních počítačů.  2. Koncové body teď podporují několik skupin dostupnosti místo sady dostupnosti. jeden.  3. Další funkce, jako porty HA, data širší monitorování atd. Pokud přesouváte do zóny dostupnosti Azure, je třeba standardní vyrovnávání zátěže. Pro nové nasazení doporučujeme vám umožní začít s Azure Standard pro vyrovnávání zatížení. 

## <a name="3-authentication"></a>3. Authentication 
[Cloud Foundry uživatelský účet a ověřování](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) je služba správy centrální identity CR a jeho různé komponenty. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) je společnosti Microsoft víceklientské, cloudového adresáře a identity management service. Ve výchozím nastavení UAA slouží k ověřování cloudové Foundry. Jako o upřesňující možnost UAA také podporuje Azure AD jako úložiště externí uživatele. Azure AD mohou uživatelé Foundry cloudu pomocí svou identitu LDAP, bez účtu Foundry cloudu. Postupujte podle těchto kroků [konfigurovat Azure AD pro UAA v PCF](http://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Úložiště dat pro Cloud Foundry Runtime System
Cloud Foundry nabízí skvělý rozšiřitelnost používat úložiště objektů BLOB Azure nebo Azure MySQL/PostgreSQL služeb pro úložiště systému za běhu aplikace.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Úložiště objektů Azure blob pro Cloud Foundry cloudu řadič úložiště objektů BLOB
Úložiště objektů BLOB Kontroleru cloudu je důležitá data úložiště pro buildpacks, droplety, balíčky a fondů zdrojů. Ve výchozím nastavení systém souborů NFS server slouží k cloudu řadič úložiště objektů BLOB. Abyste se vyhnuli jediný bod selhání, používejte jako externí úložiště Azure Blob Storage. Podívejte se na [dokumentace cloudu Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) pozadí, a [možnosti v hrají cloudu Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>Spustit čas databáze MySQL/PostgreSQL jako cloudu Foundry Elastická *
Elastické Runtime CR vyžaduje dva hlavní systémové databáze:
#### <a name="ccdb"></a>CCDB 
Databázi Kontroleru cloudu.  Adaptér cloudu poskytuje koncové body REST API pro klienty pro přístup k systému. CCDB ukládá tabulky pro orgs, mezery, služby, rolí uživatelů a více kontroleru cloudu.
#### <a name="uaadb"></a>UAADB 
Databáze pro uživatelský účet a ověřování. Ukládá ověřování uživatelů, související data, například zašifrovaná uživatelská jména a hesla.

Ve výchozím nastavení můžete použít místní systémovou databázi (MySQL). Pro HA a škálování, využít Azure spravované MySQL nebo služby PostgreSQL. Tady je pokyn z [povolení Azure MySQL/PostgreSQL CCDB, UAADB a jiné systémové databáze s otevřít zdrojový Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Otevřete Service Broker
Zprostředkovatele služby Azure nabízí jednotné rozhraní pro správu aplikace přístup ke službám Azure. Nové [otevřete služby Service Broker pro projektu Azure](https://github.com/Azure/open-service-broker-azure) poskytuje jeden a jednoduchý způsob, jak poskytovat služby k aplikacím přes Cloud Foundry, OpenShift a Kubernetes. Najdete v článku [Azure otevřete Service Broker pro dlaždici PCF](https://network.pivotal.io/products/azure-open-service-broker-pcf/) pokyny pro nasazení na PCF.

## <a name="6-metrics-and-logging"></a>6. Protokolování a metriky
Trysek Azure Log Analytics je komponenta Foundry cloudu, který předává metriky z [Foundry cloudu loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) k [Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/). S trysek můžete shromažďovat, zobrazit a analyzovat vaše CR systému stavu a výkonu metriky napříč více nasazení.
Klikněte na tlačítko [sem](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) se dozvíte, jak nasadit trysek Azure Log Analytics Open Source a otáčení Foundry cloudové prostředí, a poté přístup k datům z konzoly Azure Log Analytics OMS. 
> [!NOTE]
> Z PCF 2.0 BOSH stavu metriky pro virtuální počítače se předávají do Loggregator Firehose ve výchozím nastavení a jsou integrované do konzoly Azure Log Analytics OMS.

## <a name="7-cost-saving"></a>7. Úspora nákladů
### <a name="cost-saving-for-devtest-environments"></a>Náklady na ukládání pro prostředí pro vývoj/testování
#### <a name="b-series-"></a>B-Series: *
Když řady F a virtuálních počítačů D byly běžně doporučoval pro produkční prostředí hrají cloudu Foundry nové "burstable" [B-series](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) přináší nové možnosti. B-series burstable virtuální počítače jsou ideální pro úlohy, které nemají potřebovat úplný výkon procesoru nepřetržitě, jako jsou webové servery, malé databáze a vývoj a testovací prostředí. Tyto úlohy mají obvykle burstable výkonu požadavky. Je $0,012 za hodinu (B1) ve srovnání s $ hodnotu 0,05 za hodinu (F1) najdete úplný seznam [velikosti virtuálních počítačů](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes-general) a [ceny](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) podrobnosti. 
#### <a name="managed-standard-disk"></a>Spravované – standardní Disk: 
Prémiové disky se doporučuje pro spolehlivý výkon v produkčním prostředí.  S [spravované disku](https://azure.microsoft.com/services/managed-disks/), standardního úložiště může také poskytnout podobné spolehlivost, výkon jiný. Pro úlohy, která není náročné na výkon, jako jsou pro vývoj/testování nebo nekritické prostředí spravované standardní disky nabízejí alternativní možnost s nižší náklady.  
### <a name="cost-saving-in-general"></a>Obecně úspora nákladů 
#### <a name="significant-vm-cost-saving-with-reserved-instances"></a>Uložení s vyhrazenou instancí náklady na významné virtuální počítače: 
Dnes, které všechny virtuální počítače CR fakturují pomocí "na vyžádání" ceny, i když prostředí obvykle zůstat až po neomezenou dobu. Nyní můžete záložní kapacita virtuálního počítače na termín 1 nebo rok 3 a získáte slevy 45 65 %. Slevy jsou použity v fakturačních systémů bez změny pro vaše prostředí. Podrobnosti najdete v tématu [funguje jak vyhrazené instance](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Spravovaný Disk Premium s menší velikostí: 
Spravované disky podporu menší velikost disku, například P4(32 GB) a P6(64 GB) pro premium a standardní disky. Pokud máte malé úlohy, můžete uložit náklady při migraci z standardní prémiové disky spravované prémiové disky.
#### <a name="utilizing-azure-first-party-services"></a>Použití služby Azure první strany: 
Využití výhod první strany služby Azure bude nižší dlouhodobé správy náklady, kromě HA a spolehlivost zmíněné v části. 

Spuštění hrají má [malé nároky vložit](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) pro zákazníky PCF komponenty společném umístění do právě 4 spuštěné virtuální počítače, až 2 500 instancí aplikace. Zkušební verze je nyní k dispozici prostřednictvím [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Další kroky
Funkce integrace se službou Azure jsou k dispozici nejprve [otevřít zdrojový Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), než je k dispozici na hrají Foundry cloudu. Funkce označené * ještě nejsou k dispozici prostřednictvím PCF. Integrace Foundry cloudu s Azure zásobníku není buď zahrnuté v tomto dokumentu.
Pro podporu PCF na funkcích označené *, nebo cloudové Foundry integraci s Azure zásobníku, kontaktujte vašeho account manažera Pivotal a společnosti Microsoft pro poslední stav. 

