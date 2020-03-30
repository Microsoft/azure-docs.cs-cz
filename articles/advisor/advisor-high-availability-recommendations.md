---
title: Zlepšení dostupnosti vaší aplikace pomocí Azure Advisoru
description: Azure Advisor slouží ke zlepšení vysoké dostupnosti nasazení Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 997681ed62fa9985e3122ece22565dbae0e65b53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443109"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Zlepšení dostupnosti vaší aplikace pomocí Azure Advisoru

Azure Advisor vám pomůže zajistit a zlepšit kontinuitu vašich důležitých podnikových aplikací. Doporučení pro vysokou dostupnost můžete získat od poradce na kartě **Vysoká dostupnost** na řídicím panelu Poradce.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Zajištění odolnosti proti chybám virtuálního počítače

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Poradce identifikuje virtuální počítače, které nejsou součástí skupiny dostupnosti a doporučuje jejich přesunutí do skupiny dostupnosti. Tato konfigurace zajišťuje, že během plánované nebo neplánované události údržby je k dispozici alespoň jeden virtuální počítač, který splňuje sla virtuálního počítače Azure. Můžete vytvořit sadu dostupnosti pro virtuální počítač nebo přidat virtuální počítač do existující skupiny dostupnosti.

> [!NOTE]
> Pokud se rozhodnete vytvořit sadu dostupnosti, musíte do ní přidat alespoň jeden další virtuální počítač. Doporučujeme seskupit dva nebo více virtuálních počítačů v sadě dostupnosti, abyste zajistili, že během výpadku bude k dispozici alespoň jeden počítač.

## <a name="ensure-availability-set-fault-tolerance"></a>Zajistěte odolnost proti chybám sady dostupnosti

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Advisor identifikuje skupiny dostupnosti, které obsahují jeden virtuální počítač, a doporučuje do něj přidat jeden nebo více virtuálních počítačů.Tato konfigurace zajišťuje, že během plánované nebo neplánované události údržby je k dispozici alespoň jeden virtuální počítač, který splňuje sla virtuálního počítače Azure.Můžete se rozhodnout vytvořit virtuální počítač nebo přidat existující virtuální počítač do skupiny dostupnosti.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Zvýšení spolehlivosti dat s využitím spravovaných disků

Virtuální počítače, které jsou v sadě dostupnosti s disky, které sdílejí účty úložiště nebo jednotky škálování úložiště nejsou odolné vůči selhání jednotky škálování jednoho úložiště během výpadků. Poradce identifikuje tyto skupiny dostupnosti a doporučí migraci na spravované disky Azure. Tím zajistíte, že disky různých virtuálních počítačů v množině dostupnosti jsou dostatečně izolované, aby se zabránilo jedinému bodu selhání. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Zajištění odolnosti proti chybám aplikační brány

Toto doporučení zajišťuje kontinuitu provozu důležitých aplikací, které jsou napájeny aplikačními bránami. Advisor identifikuje instance aplikační brány, které nejsou nakonfigurovány pro odolnost proti chybám, a navrhuje nápravné akce, které můžete provést. Advisor identifikuje střední nebo velké brány aplikace s jednou instancí a doporučuje přidat alespoň jednu další instanci. Také identifikuje malé aplikační brány s jednou nebo více instancemi a doporučuje migraci na střední nebo velké sku. Advisor doporučuje tyto akce k zajištění, že instance aplikační brány jsou nakonfigurovány tak, aby splňovaly aktuální požadavky sla pro tyto prostředky.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Ochrana dat virtuálního počítače před náhodným odstraněním

Nastavení zálohování virtuálních strojů zajišťuje dostupnost důležitých podnikových dat a nabízí ochranu před náhodným odstraněním nebo poškozením. Advisor identifikuje virtuální počítače, kde není povoleno zálohování, a doporučuje povolit zálohování. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ujistěte se, že máte přístup k cloudovým odborníkům Azure, když to potřebujete

Při spuštění úlohy kritické pro podnikání je důležité mít v případě potřeby přístup k technické podpoře. Advisor identifikuje potenciální důležitá obchodní předplatná, která nemají technickou podporu zahrnutou v plánu podpory, a doporučuje upgradovat na možnost, která zahrnuje technickou podporu.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Vytvořte výstrahy stavu služby Azure, které mají být upozorňovány, když se vás týkají problémy s Azure

Doporučujeme nastavit výstrahy stavu služby Azure, které mají být upozorňovány, když se vás týkají problémy se službou Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) je bezplatná služba, která poskytuje přizpůsobené pokyny a podporu, když se vás na vliv na problém služby Azure. Advisor identifikuje odběry, které nemají výstrahy nakonfigurována a doporučuje vytvořit jeden.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurace koncových bodů Traffic Manageru pro odolnost proti chybám

Traffic Manager profily s více než jeden koncový bod zkušenosti vyšší dostupnost, pokud některý daný koncový bod selže. Umístění koncových bodů v různých oblastech dále zvyšuje spolehlivost služeb. Advisor identifikuje profily Traffic Manger, kde je pouze jeden koncový bod a doporučuje přidat alespoň jeden další koncový bod v jiné oblasti.

Pokud jsou všechny koncové body v profilu traffic manageru, který je nakonfigurován pro směrování bezkontaktní komunikace, ve stejné oblasti, mohou uživatelé z jiných oblastí zaznamenat zpoždění připojení. Přidání nebo přesunutí koncového bodu do jiné oblasti zlepší celkový výkon a poskytne lepší dostupnost, pokud všechny koncové body v jedné oblasti nezdaří. Advisor identifikuje profily Traffic Manageru nakonfigurované pro bezkontaktní směrování, kde jsou všechny koncové body ve stejné oblasti. Doporučuje přidání nebo přesunutí koncového bodu do jiné oblasti Azure.

Pokud je profil Traffic Manageru nakonfigurován pro geografické směrování, je provoz směrován do koncových bodů na základě definovaných oblastí. Pokud oblast selže, neexistuje žádné předdefinované převzetí služeb při selhání. S koncovým bodem, kde je nakonfigurován o místní seskupení "Všechny (svět)" zabrání provozu a zlepšit dostupnost služeb. Advisor identifikuje profily Traffic Manageru nakonfigurované pro geografické směrování, kde neexistuje žádný koncový bod nakonfigurovaný tak, aby měl místní seskupení jako "Vše (svět)". Doporučuje změnit konfiguraci tak, aby koncový bod "Vše (svět).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Použití obnovitelného odstranění na účtu úložiště Azure k uložení a obnovení dat po náhodném přepsání nebo odstranění

Povolte [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) v účtu úložiště, aby odstraněné objekty BLOB přešly do obnovitelného odstraněného stavu, místo aby byly trvale odstraněny. Při přepsání dat se vygeneruje obnovitelně odstraněný snímek, do kterého se uloží stav přepsaných dat. Použití obnovitelného odstranění umožňuje obnovit, pokud dojde k náhodnému odstranění nebo přepsání. Advisor identifikuje účty Azure Storage, které nemají povolené obnovitelné odstranění, a navrhne, abyste ho povolili.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurace brány VPN na aktivní aktivní pro odolnost proti připojení

V konfiguraci aktivní aktivní obě instance brány VPN vytvoří tunely S2S VPN do místního zařízení VPN. Pokud dojde k plánované události údržby nebo neplánované události v jedné instanci brány, provoz se automaticky přepne do jiného aktivního tunelu IPsec. Azure Advisor identifikuje brány VPN, které nejsou nakonfigurované jako aktivní, a navrhne, abyste je nakonfigurovali pro vysokou dostupnost.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Použití produkčních bran VPN ke spuštění produkčních úloh

Azure Advisor zkontroluje všechny brány VPN, které jsou základní skladovou položkou, a místo toho doporučuje použít produkční skladovou položku. Základní skladová položka je určena pro účely vývoje a testování. Výrobní skum nabízejí vyšší počet tunelů, podporu Protokolu BGP, možnosti konfigurace aktivní a aktivní, vlastní zásady Protokolu Ipsec/IKE a vyšší stabilitu a dostupnost.

## <a name="repair-invalid-log-alert-rules"></a>Oprava neplatných pravidel upozornění protokolu

Azure Advisor zjistí pravidla výstrah, která mají neplatné dotazy zadané v jejich části podmínky. Pravidla upozornění protokolu se vytváří ve službě Azure Monitor a slouží ke spouštění analytických dotazů v určených intervalech. Výsledky dotazu určují, jestli je potřeba aktivovat upozornění. Analytické dotazy se v průběhu času můžou stát neplatnými kvůli změnám v odkazovaných prostředcích, tabulkách nebo příkazech. Poradce vám doporučí opravit dotaz v pravidle výstrahy, abyste zabránili jeho automatickému zakázání a zajistili monitorování pokrytí vašich prostředků v Azure. [Další informace o řešení pravidel upozornění](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Konfigurace konzistentního režimu indexování v kolekci Cosmos DB

Kontejnery Azure Cosmos DB nakonfigurované s režimem opožděného indexování může mít vliv na čerstvost výsledků dotazu. Poradce detekuje kontejnery nakonfigurované tímto způsobem a doporučuje přepnutí do konzistentního režimu. [Další informace o zásadách indexování v Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Konfigurace klíče oddílu pro kontejnery Azure Cosmos DB

Azure Advisor identifikuje nerozdělené kolekce Azure Cosmos DB, které se blíží k jejich kvótě zřízeného úložiště. Doporučuje migraci těchto kolekcí do nových kolekcí s definicí klíče oddílu tak, aby mohly být automaticky škálovány službou. [Další informace o výběru klíče oddílu](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Upgrade sady Azure Cosmos DB .NET SDK na nejnovější verzi z NuGetu

Azure Advisor identifikuje účty Azure Cosmos DB, které používají staré verze sady .NET SDK, a doporučí upgrade na nejnovější verzi od Nugetu pro nejnovější opravy, vylepšení výkonu a nové funkce. [Další informace o službě Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Upgrade sady Azure Cosmos DB Java SDK na nejnovější verzi z Mavenu

Azure Advisor identifikuje účty Azure Cosmos DB, které používají staré verze sady Java SDK, a doporučí upgradovat na nejnovější verzi od společnosti Maven pro nejnovější opravy, vylepšení výkonu a nové funkce. [Další informace o sdk Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Upgrade konektoru Azure Cosmos DB Spark na nejnovější verzi z Mavenu

Azure Advisor identifikuje účty Azure Cosmos DB, které používají staré verze konektoru Cosmos DB Spark, a doporučí upgrade na nejnovější verzi od mavenu pro nejnovější opravy, vylepšení výkonu a nové funkce. [Další informace o konektoru Cosmos DB Spark](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Povolení replikace virtuálních počítačů
Virtuální počítače, které nemají povolenou replikaci do jiné oblasti, nejsou odolné vůči regionálním výpadkům. Replikace virtuálních počítačů snižuje jakýkoli nepříznivý obchodní dopad během výpadku oblasti Azure. Poradce zjistí virtuální počítače, které nemají povolenou replikaci, a doporučí povolení replikace, aby v případě výpadku mohli rychle vyvolat virtuální počítače ve vzdálené oblasti Azure. [Další informace o replikaci virtuálních strojů](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Jak získat přístup k doporučením pro vysokou dostupnost v advisor

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete [poradce](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu Poradce klikněte na kartu **Vysoká dostupnost.**

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních poradců naleznete v tématu:
* [Úvod do Azure Advisoru](advisor-overview.md)
* [Začínáme s poradcem](advisor-get-started.md)
* [Doporučení nákladů poradce](advisor-cost-recommendations.md)
* [Doporučení pro výkon poradce](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení poradce](advisor-security-recommendations.md)
* [Doporučení poradce pro provozní excelenci](advisor-operational-excellence-recommendations.md)
