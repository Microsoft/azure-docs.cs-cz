---
title: Přehled – Azure Database for MySQL – flexibilní Server
description: Přečtěte si o Azure Database for MySQL flexibilním serveru, službě relačních databází v cloudu Microsoftu na základě verze MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/21/2020
ms.openlocfilehash: f6ec6bced9c84e4e5b0f04cc32eebb438052bd6c
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348283"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL – flexibilní Server (Preview)

Azure Database for MySQL, kterou používá služba MySQL Community Edition, je k dispozici ve dvou režimech nasazení:
- Jeden server 
- Flexibilní server (Preview)

V tomto článku budeme poskytovat přehled a Úvod do základních konceptů flexibilního modelu nasazení serveru. Informace o tom, jak rozhodnout, jaká možnost nasazení je pro vaše zatížení vhodná, najdete v tématu [Volba správné možnosti serveru MySQL v Azure](./../select-right-deployment-type.md).

## <a name="overview"></a>Přehled

Azure Database for MySQL flexibilní Server je plně spravovaná databázová služba navržená tak, aby poskytovala přesnější kontrolu a flexibilitu nad funkcemi správy databáze a nastaveními konfigurace. Obecně platí, že služba poskytuje větší flexibilitu a přizpůsobení konfigurace serveru na základě požadavků uživatelů. Flexibilní architektura serveru umožňuje uživatelům, aby se mohli rozhodnout o vysoké dostupnosti v rámci jedné zóny dostupnosti a napříč několika zónami dostupnosti. Flexibilní servery také poskytují lepší ovládací prvky pro optimalizaci nákladů s možností zastavit nebo spustit server a rozzpůsobitelné skladové položky, které jsou ideální pro úlohy, které nepotřebují nepřetržitě plný výpočetní výkon. Služba aktuálně podporuje komunitní verze MySQL 5,7. Služba je momentálně ve verzi Preview, která je dnes k dispozici v nejrůznějších [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/).

Flexibilní servery jsou vhodné pro 
- Vývoj aplikací, který vyžaduje lepší kontrolu a přizpůsobení.
- Redundantní vysoká dostupnost zóny
- Spravovaná okna údržby

![Flexibilní koncepční diagram serveru](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="high-availability-within-and-across-availability-zones"></a>Vysoká dostupnost v rámci zón dostupnosti i napříč nimi

Model nasazení flexibilního serveru je navržený tak, aby podporoval vysokou dostupnost v rámci jedné zóny dostupnosti a napříč několika zónami dostupnosti. Architektura odděluje výpočetní výkon a úložiště. Databázový stroj běží na virtuálním počítači, zatímco datové soubory se nacházejí v úložišti Azure. Úložiště uchovává tři místně redundantní synchronní kopie databázových souborů, které zajišťují odolnost dat neustále. 

Pokud dojde v rámci jedné zóny dostupnosti k výpadku serveru kvůli plánovaným nebo neplánovaným událostem, služba udržuje vysokou dostupnost serverů pomocí následujícího automatizované procedury:

1. Zřizuje se nový virtuální počítač pro výpočetní výkon.
2. Úložiště s datovými soubory je namapované na nový virtuální počítač.
3. Databázový stroj MySQL se do nového virtuálního počítače přepne do online režimu.
4. Klientské aplikace se mohou znovu připojit, jakmile je server připraven přijmout připojení.
   
:::image type="content" source="media/overview/2-flexible-server-architecture.png" alt-text="Koncepční diagram vysoké dostupnosti jedné zóny"::: 

Pokud je nakonfigurovaná vysoká dostupnost zóny, služba zřídí a udržuje aktivní pohotovostní server napříč zónou dostupnosti v rámci stejné oblasti Azure. Změny dat na zdrojovém serveru se synchronně replikují na pohotovostní server, aby se zajistila nulová ztráta dat. Pokud se po aktivaci plánované nebo neplánované události převzetí služeb při selhání spustí záložní server s vysokou dostupností, bude pohotovostní server okamžitě online a bude k dispozici pro zpracování příchozích transakcí. Typický časový rozsah pro převzetí služeb při selhání je od 60-120 sekund. Díky tomu může služba podporovat vysokou dostupnost a poskytovat lepší odolnost proti chybám jedné zóny dostupnosti v dané oblasti Azure. 

Další podrobnosti najdete v tématu [Koncepty vysoké dostupnosti](concepts-high-availability.md) .

:::image type="content" source="media/overview/3-flexible-server-overview-zone-redundant-ha.png" alt-text="Koncepční diagram redundantní vysoké dostupnosti zóny"::: 

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatizované opravy pomocí spravovaného časového období údržby

Služba provádí automatizované opravy základního hardwaru, operačního systému a databázového stroje. Opravy zahrnují zabezpečení a aktualizace softwaru. Pro modul MySQL jsou jako součást plánované verze údržby zahrnuté i upgrady podverze. Uživatelé můžou nakonfigurovat plán oprav tak, aby byl spravovaný systémem, nebo definovat vlastní plán. Během plánu údržby se oprava použije a server může po dokončení aktualizace vyžadovat restart jako součást procesu oprav. Díky vlastnímu plánu můžou uživatelé provádět předvídatelné cykly oprav a zvolit časové období údržby s minimálním dopadem na firmu. Obecně platí, že služba dodržuje plán měsíčního vydání jako součást průběžné integrace a vydání.

Další podrobnosti najdete v tématu [naplánovaná údržba](concepts-maintenance.md) . 

## <a name="automatic-backups"></a>Automatické zálohování

Flexibilní serverová služba automaticky vytvoří zálohy serveru a uloží je v uživatelsky nakonfigurovaném místně redundantním nebo geograficky redundantním úložišti. Zálohy můžete použít k obnovení serveru k jakémukoli časovému okamžiku v rámci doby uchovávání záloh. Výchozí doba uchovávání záloh je sedm dní. Doba uchovávání může být volitelně nakonfigurovaná na 35 dní. Všechny zálohy se šifrují s využitím 256bitového šifrování AES. 

Další informace najdete v tématu [Koncepty zálohování](concepts-backup-restore.md) .

## <a name="network-isolation"></a>Izolace sítě

Máte dvě možnosti sítě, které se připojí k vašemu Azure Database for MySQL flexibilnímu serveru. Možnosti jsou **privátní přístup (Integration VNET)** a **veřejný přístup (povolených IP adres)**. 

* **Privátní přístup (Integration VNET)** – flexibilní Server můžete nasadit do [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuální sítě Azure poskytují soukromou a zabezpečenou síťovou komunikaci. Prostředky ve virtuální síti můžou komunikovat prostřednictvím privátních IP adres.

   Vyberte možnost integrace virtuální sítě, pokud chcete následující funkce:
   * Připojení z prostředků Azure ve stejné virtuální síti k flexibilnímu serveru pomocí privátních IP adres
   * Pomocí VPN nebo ExpressRoute se připojte z jiných prostředků než Azure k flexibilnímu serveru.
   * Žádný veřejný koncový bod

* **Veřejný přístup (povolené IP adresy)** – flexibilní Server můžete nasadit pomocí veřejného koncového bodu. Veřejný koncový bod je veřejně přeložitelný adresa DNS. Fráze "povolené IP adresy" odkazuje na rozsah IP adres, které se rozhodnete udělit oprávnění k přístupu k serveru. Tato oprávnění se nazývají **pravidla brány firewall**.

Další informace najdete v tématu [Koncepty sítě](concepts-networking.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Úprava výkonu a škálování během několika sekund

Flexibilní serverová služba je k dispozici ve třech úrovních SKU: shluky, Pro obecné účely a paměťově optimalizované. Vícevrstvá vrstva je nejvhodnější pro vývoj s nízkými náklady a pro úlohy s nízkou úrovní souběžnosti, které nepotřebují nepřetržitě plný výpočet kapacity. Pro obecné účely a paměť optimalizované pro produkční úlohy, které vyžadují vysokou souběžnost, škálování a předvídatelný výkon, jsou lépe vhodné. Svou první aplikaci můžete vytvořit na malé databázi po dobu několika dolarů měsíčně a pak hladce upravit škálování tak, aby splňovalo požadavky vašeho řešení. Škálování úložiště je online a podporuje automatické zvětšování úložiště. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které využíváte. 

Další informace najdete v tématu [Koncepty služby COMPUTE and Storage](concepts-compute-storage.md) .

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>Horizontální navýšení kapacity pro čtení a zatížení až na 10 replik pro čtení

MySQL je jedním z oblíbených databázových modulů pro provozování webových a mobilních aplikací v internetovém měřítku. Spousta našich zákazníků ji používá pro svoje online vzdělávací služby, služby streamování videí, digitální platební řešení, platformy elektronického obchodování, herní služby, diskusní portály, státní správu a weby zdravotnictví. Tyto služby jsou nutné k obsluze a škálování při zvyšování provozu webové nebo mobilní aplikace.

Na straně aplikace je aplikace obvykle vyvinutá v jazyce Java nebo php a migrována do provozu na [Azure Virtual Machine Scale Sets](../../virtual-machine-scale-sets/overview.md)   nebo v [Azure App Services](../../app-service/overview.md)   nebo je kontejnerem pro spuštění ve [službě Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md). Pomocí sady škálování virtuálních počítačů, App Service nebo AKS jako základní infrastruktury, se škálování aplikace zjednodušuje okamžitým zřizováním nových virtuálních počítačů a replikací bezstavových komponent aplikací do zařízení do systému stravování až po požadavky, ale databáze ukončí kritické body jako centralizované stavové součásti.

Funkce replika čtení umožňuje replikovat data z Azure Database for MySQL flexibilního serveru do serveru jen pro čtení. Můžete replikovat ze zdrojového serveru do až **10 replik**. Repliky se asynchronně aktualizují pomocí [technologie replikace nativního binárního protokolu (binlog)](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)modulu MySQL. Řešení proxy serveru pro vyrovnávání zatížení, jako je [ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) , můžete využít k bezproblémovému škálování úlohy aplikace na čtení replik bez jakýchkoli nákladů refaktoringu aplikací. 

Další informace najdete v tématu věnovaném [konceptům repliky pro čtení](concepts-read-replicas.md) . 


## <a name="stopstart-server-to-optimize-cost"></a>Zastavení/spuštění serveru za účelem optimalizace nákladů

Flexibilní serverová služba umožňuje zastavit a spustit server na vyžádání, aby se optimalizoval náklady. Fakturace na výpočetní úrovni se okamžitě zastaví, když se Server zastaví. To vám může způsobit výrazné úspory nákladů během vývoje, testování a předvídatelných produkčních úloh, které jsou časově závislé. Server zůstane v zastaveném stavu po dobu sedmi dnů, pokud se znovu nespustí dříve. 

Další informace najdete v tématu [Koncepty serveru](concept-servers.md) . 

## <a name="enterprise-grade-security-and-privacy"></a>Zabezpečení a ochrana osobních údajů na podnikové úrovni

Flexibilní serverová služba používá šifrovací modul ověřený standardem FIPS 140-2 pro šifrování úložiště neaktivních dat. Data, včetně záloh a dočasných souborů vytvořených při spouštění dotazů, jsou zašifrovaná. Služba používá algoritmus AES 256-bit, který je součástí šifrování úložiště Azure, a klíče můžou být spravované systémem (výchozí). 

Služba šifruje přenášená data pomocí Transport Layer Security, který je ve výchozím nastavení vynutil. Flexibilní servery podporují jenom šifrovaná připojení pomocí TLS 1,2) a všechna příchozí připojení s TLS 1,0 a TLS 1,1 budou odepřená. 

Další informace najdete v tématu [použití šifrovaných připojení k flexibilním serverům](https://docs.mongodb.com/manual/tutorial/configure-ssl) .

Flexibilní servery umožňují úplný privátní přístup k serverům pomocí integrace [virtuální sítě Azure](../../virtual-network/virtual-networks-overview.md) . Servery ve službě Azure Virtual Network je možné oslovit a propojit jenom pomocí privátních IP adres. S integrací virtuální sítě se zamítl veřejný přístup a servery se nedají kontaktovat pomocí veřejných koncových bodů. 

Další informace najdete v tématu [Koncepty sítě](concepts-networking.md) .


## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Flexibilní serverová služba je vybavená integrovanými funkcemi pro monitorování výkonu a upozorňování. Všechny metriky Azure mají četnost jednosměrných minut a každá metrika poskytuje historii 30 dnů. Výstrahy můžete nakonfigurovat pro metriky. Služba zpřístupňuje metriky hostitelského serveru pro monitorování využití prostředků a umožňuje konfigurovat protokoly pomalých dotazů. Pomocí těchto nástrojů můžete rychle optimalizovat vaše úlohy a nakonfigurovat server tak, aby co nejlépe vyzpůsobil výkon. 

Další informace najdete v tématu věnovaném [konceptům monitorování](concepts-monitoring.md) .

## <a name="migration"></a>Migrace

Služba spouští komunitní verze MySQL. To umožňuje úplnou kompatibilitu aplikací a vyžaduje minimální náklady na Refaktoring pro migraci existující aplikace vyvinuté v modulu MySQL na jednu serverovou službu. Migraci na jeden server lze provést pomocí jedné z následujících možností:

- **Vypsat a obnovit** – pro offline migrace, kde můžou uživatelé získat nějaké výpadky, vypsat a obnovit pomocí nástrojů komunity, jako je mysqldump/mydumper, může poskytnout nejrychlejší způsob migrace. Podrobnosti najdete v tématu migrace pomocí výpisu paměti a obnovení. 
- **Azure Database Migration Service** – pro bezproblémové a zjednodušené migrace na jediný server s minimálními výpadky je možné využít [Azure Database Migration Service](../../dms/tutorial-mysql-azure-mysql-online.md) . 

## <a name="azure-regions"></a>Oblast Azure

Jednou z výhod spouštění úloh v Azure je globální dosah. Flexibilní Server pro Azure Database for MySQL je k dispozici v současnosti v následujících oblastech Azure:

- West Europe
- Severní Evropa
- Spojené království – jih
- USA – východ 2
- Západní USA 2
- Střední USA
- East US
- Střední Kanada
- Southeast Asia
- Jižní Korea – střed
- Japan East
- Austrálie – východ

Pracujeme na přidávání nových serverů brzy.

## <a name="contacts"></a>Kontakty
V případě jakýchkoli otázek nebo návrhů, které byste mohli mít Azure Database for MySQL flexibilní Server, pošlete e-mail týmu Azure Database for MySQL ([ @Ask Azure DB pro MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Tato e-mailová adresa není alias technické podpory.

Můžete také využít tyto komunikační kanály:

- Pro podporu Azure [vytvořte lístek podpory na webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Další kroky
Teď, když jste si přečetli Úvod do Azure Database for MySQL režimu nasazení na jeden server, jste připraveni:

- Vytvořte svůj první server. 
  - [Vytvoření Azure Database for MySQL flexibilního serveru pomocí Azure Portal](quickstart-create-server-portal.md)
  - [Vytvoření Azure Database for MySQL flexibilního serveru pomocí Azure CLI](quickstart-create-server-cli.md)
  - [Správa Azure Database for MySQL flexibilního serveru pomocí Azure CLI](how-to-manage-server-portal.md)

- Sestavte svou první aplikaci pomocí preferovaného jazyka:
  - [Python](connect-python.md)
  - [Php](connect-php.md)
