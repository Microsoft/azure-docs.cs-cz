---
title: Zlepšení dostupnosti aplikace pomocí Azure Advisor | Microsoft Docs
description: Pomocí Azure Advisor můžete zlepšit vysokou dostupnost nasazení Azure.
services: advisor
documentationcenter: NA
author: saket-ms
ms.author: sagupt
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: dd1b898adf4c4cdff45e05427757d90d5f80bf25
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145342"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Zlepšení dostupnosti aplikace pomocí Azure Advisor

Azure Advisor vám pomůže zajistit a zlepšit kontinuitu důležitých podnikových aplikací. Doporučení pro vysokou dostupnost na kartě **Vysoká dostupnost** na řídicím panelu služby Advisor můžete získat pomocí Advisoru.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Zajištění odolnosti proti chybám virtuálního počítače

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Poradce identifikuje virtuální počítače, které nejsou součástí skupiny dostupnosti, a doporučuje je přesunout do skupiny dostupnosti. Tato konfigurace zajišťuje, že během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač a splňuje smlouvu SLA pro virtuální počítače Azure. Můžete zvolit vytvoření skupiny dostupnosti pro virtuální počítač nebo přidání virtuálního počítače do existující skupiny dostupnosti.

> [!NOTE]
> Pokud se rozhodnete vytvořit skupinu dostupnosti, musíte do ní přidat aspoň jeden virtuální počítač. Doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti, aby bylo zajištěno, že během výpadku bude k dispozici alespoň jeden počítač.

## <a name="ensure-availability-set-fault-tolerance"></a>Zajištění odolnosti proti chybám u sady dostupnosti

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Poradce identifikuje skupiny dostupnosti, které obsahují jeden virtuální počítač a doporučuje do něj přidat jeden nebo více virtuálních počítačů. Tato konfigurace zajišťuje, že během plánované nebo neplánované události údržby je k dispozici aspoň jeden virtuální počítač a splňuje smlouvu SLA pro virtuální počítače Azure. Můžete se rozhodnout vytvořit virtuální počítač nebo přidat existující virtuální počítač do skupiny dostupnosti.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Zvýšení spolehlivosti dat s využitím spravovaných disků

Virtuální počítače, které jsou ve skupině dostupnosti s disky, které sdílejí buď účty úložiště nebo jednotky škálování úložiště, nejsou odolné vůči selhání jednotek škálování s jedním úložištěm během výpadků. Poradce tyto skupiny dostupnosti rozpozná a doporučí migraci do Azure Managed Disks. Tím se zajistí, že disky různých virtuálních počítačů ve skupině dostupnosti jsou dostatečně izolované, aby se předešlo jednomu bodu selhání. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Zajištění odolnosti proti chybám služby Application Gateway

Toto doporučení zajišťuje kontinuitu podnikových aplikací pro klíčové aplikace, které využívají aplikační brány. Advisor identifikuje instance služby Application Gateway, které nejsou nakonfigurované pro odolnost proti chybám, a navrhuje akce oprav, které můžete provést. Advisor identifikuje střední nebo velké aplikační brány s jednou instancí a doporučuje přidat aspoň jednu další instanci. Také identifikuje malé aplikační brány s jedním nebo více instancemi a doporučuje migraci na střední nebo velké SKU. Poradce doporučuje tyto akce, aby bylo zajištěno, že instance služby Application Gateway jsou nakonfigurovány tak, aby splňovaly aktuální požadavky smlouvy SLA pro tyto prostředky.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Ochrana dat virtuálního počítače před náhodným odstraněním

Nastavení zálohování virtuálních počítačů zajišťuje dostupnost důležitých podnikových dat a nabízí ochranu před náhodným odstraněním nebo poškozením. Poradce identifikuje virtuální počítače, u kterých není zapnuté zálohování, a doporučuje povolení zálohování. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ujistěte se, že máte přístup k odborníkům na cloud Azure, když ho potřebujete.

Při používání důležitých podnikových úloh je důležité mít v případě potřeby přístup k technické podpoře. Advisor identifikuje potenciální podniková předplatná, která neobsahují technickou podporu zahrnutou do plánu podpory a doporučuje upgradovat na možnost, která zahrnuje technickou podporu.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Vytváření upozornění na Azure Service Health, která se mají upozornit, když se na vás bude týkat problémy Azure

Doporučujeme, abyste nastavili výstrahy Azure Service Health, aby byly oznamovány, když se vás týkají problémy se službou Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) je bezplatná služba, která poskytuje individuální pokyny a podporu v případě, že máte vliv na problém se službou Azure. Advisor identifikuje odběry, u kterých nejsou nakonfigurované výstrahy, a doporučuje je vytvořit.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurace koncových bodů Traffic Manager pro odolnost

Traffic Manager profily s více než jedním koncovým bodem se vyšší dostupností, pokud některý z těchto koncových bodů selhává. Umístění koncových bodů v různých oblastech dál vylepšuje spolehlivost služby. Poradce identifikuje profily Traffic Manageru, kde je pouze jeden koncový bod a doporučuje přidat alespoň jeden další koncový bod do jiné oblasti.

Pokud jsou všechny koncové body v profilu Traffic Manager, který je nakonfigurovaný pro směrování blízkosti, ve stejné oblasti, můžou uživatelé z jiných oblastí zaznamenat zpoždění připojení. Přidáním nebo přesunutím koncového bodu do jiné oblasti dojde ke zvýšení celkového výkonu a zajištění lepší dostupnosti v případě selhání všech koncových bodů v jedné oblasti. Poradce identifikuje Traffic Manager profily nakonfigurované pro směrování blízkosti, kde jsou všechny koncové body ve stejné oblasti. Doporučuje přidat nebo přesunout koncový bod do jiné oblasti Azure.

Pokud je pro geografické směrování nakonfigurovaný profil Traffic Manager, provoz se směruje do koncových bodů v závislosti na definovaných oblastech. V případě selhání oblasti není k dispozici žádné předdefinované převzetí služeb při selhání. Pokud máte koncový bod, ve kterém je místní seskupení nakonfigurované na "vše (World)", zabrání se tím zahození provozu a vylepšuje dostupnost služby. Poradce identifikuje Traffic Manager profily nakonfigurované pro geografické směrování, u kterých není žádný koncový bod nakonfigurovaný tak, aby měl regionální seskupení jako "vše (svět)". Doporučuje se změnit konfiguraci tak, aby byl koncový bod "All (World).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Použití obnovitelného odstranění na účtu Azure Storage k uložení a obnovení dat po náhodném přepsání nebo odstranění

Povolte [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) na svém účtu úložiště, aby se odstraněné objekty blob přešly do neodstraněného stavu místo trvalého odstranění. Při přepsání dat se vygeneruje obnovitelně odstraněný snímek, do kterého se uloží stav přepsaných dat. Použití obnovitelného odstranění umožňuje obnovení, pokud dojde k náhodnému odstranění nebo přepsání. Advisor identifikuje účty Azure Storage, které nemají povolené obnovitelné odstranění, a navrhuje je povolit.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurace brány VPN na aktivní – aktivní pro odolnost připojení

V konfiguraci aktivní-aktivní vytvoří obě instance brány VPN tunely S2S VPN na vaše místní zařízení VPN. Když dojde k události plánované údržby nebo neplánované události na jednu instanci brány, provoz se přepne na ostatní aktivní tunelové propojení IPsec automaticky. Azure Advisor označí brány VPN, které nejsou nakonfigurované jako aktivní – aktivní, a navrhne, abyste je nakonfigurovali pro zajištění vysoké dostupnosti.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Použití provozních bran sítě VPN ke spouštění produkčních úloh

Azure Advisor zkontroluje všechny brány VPN, které jsou základní jednotkou SKU, a doporučí místo toho použití produkční skladové položky (SKU). Základní skladová položka je navržena pro účely vývoje a testování. Produkční skladové položky nabízejí větší počet tunelových propojení, podporu protokolu BGP, možnosti konfigurace aktivní – aktivní, vlastní zásady IPSec/IKE a vyšší stabilitu a dostupnost.

## <a name="repair-invalid-log-alert-rules"></a>Opravit neplatná pravidla upozornění protokolu

Azure Advisor zjistí pravidla upozornění, která mají v oddílu podmínky zadány neplatné dotazy. Pravidla upozornění protokolu se vytváří ve službě Azure Monitor a slouží ke spouštění analytických dotazů v určených intervalech. Výsledky dotazu určují, jestli je potřeba aktivovat upozornění. Analytické dotazy se v průběhu času můžou stát neplatnými kvůli změnám v odkazovaných prostředcích, tabulkách nebo příkazech. Poradce doporučujeme, abyste v pravidle výstrahy opravili dotaz, abyste zabránili jeho automatickému zakázání a zajistili sledování pokrytí vašich prostředků v Azure. [Další informace o řešení potíží s pravidly upozornění](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Konfigurace konzistentního režimu indexování v kolekci Cosmos DB

Kontejnery Azure Cosmos DB nakonfigurované s opožděným indexováním můžou mít dopad na aktuálnost výsledků dotazů. Advisor vyhledá kontejnery nakonfigurované tímto způsobem a doporučí přepnutí do konzistentního režimu. [Další informace o indexování zásad v Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Konfigurace klíče oddílu pro kontejnery Azure Cosmos DB

Azure Advisor určí Azure Cosmos DB nedělené kolekce, které se blíží své zřízené kvótě úložiště. Doporučuje se migrovat tyto kolekce do nových kolekcí s definicí klíče oddílu tak, aby je bylo možné automaticky škálovat službou. [Další informace o volbě klíče oddílu](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Upgrade sady Azure Cosmos DB .NET SDK na nejnovější verzi z NuGetu

Azure Advisor určí Azure Cosmos DB účty, které používají starší verze sady .NET SDK, a doporučuje upgradovat na nejnovější verzi z NuGet, kde najdete nejnovější opravy, vylepšení výkonu a nové funkce funkcí. [Další informace o sadě Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Upgrade sady Azure Cosmos DB Java SDK na nejnovější verzi z Mavenu

Azure Advisor určí Azure Cosmos DB účty, které používají starší verze sady Java SDK, a doporučuje upgradovat na nejnovější verzi z Maven, kde najdete nejnovější opravy, vylepšení výkonu a nové funkce funkcí. [Další informace o Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Upgrade konektoru Azure Cosmos DB Spark na nejnovější verzi z Mavenu

Azure Advisor určí Azure Cosmos DB účty, které používají starší verze konektoru pro Cosmos DB Spark, a doporučí upgrade na nejnovější verzi z Maven na nejnovější opravy, vylepšení výkonu a nové funkce funkcí. [Další informace o konektoru Cosmos DB Spark](https://aka.ms/cosmosdb/spark-connector)

## <a name="enable-virtual-machine-replication"></a>Povolení replikace virtuálních počítačů
Virtuální počítače, které nemají povolenou replikaci do jiné oblasti, nejsou odolné vůči nemožnostem regionálních výpadků. Replikace virtuálních počítačů zkracuje dopad na nepříznivý dopad na firmu v době výpadku oblasti Azure. Poradce zjistí virtuální počítače, u kterých není povolená replikace, a doporučí povolení replikace, aby v případě výpadku mohli své virtuální počítače rychle zprovoznit ve vzdálené oblasti Azure. [Další informace o replikaci virtuálních počítačů](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Jak získat přístup k doporučením vysoké dostupnosti v Advisoru

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak otevřete [Poradce](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu Advisor klikněte na kartu **Vysoká dostupnost** .

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních služby Advisor najdete v těchto tématech:
* [Úvod do Azure Advisor](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Doporučení pro náklady na poradce](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
