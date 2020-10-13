---
title: Vylepšení spolehlivosti aplikace pomocí služby Advisor
description: Pomocí Azure Advisor můžete zajistit a zlepšit spolehlivost vašich důležitých nasazení v Azure.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 1e256d99f8d78ddff318f963dcb21e9b4537f110
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91405186"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Zlepšení spolehlivosti aplikace pomocí Azure Advisor

Azure Advisor vám pomůže zajistit a zlepšit kontinuitu důležitých podnikových aplikací. Doporučení pro spolehlivost můžete získat na kartě **spolehlivost** na řídicím panelu poradce.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Ověřte verzi image síťového virtuálního zařízení se kontrolním bodem

Poradce může zjistit, jestli virtuální počítač používá verzi image kontrolního bodu, u které se během operací obsluhy platforem ví, že ztratí síťové připojení. Doporučení poradce vám pomůže upgradovat na novější verzi image, která tento problém řeší. Tato kontrola zajistí kontinuitu podnikových aplikací díky lepšímu připojení k síti.

## <a name="ensure-application-gateway-fault-tolerance"></a>Zajištění odolnosti proti chybám služby Application Gateway

Toto doporučení zajišťuje kontinuitu podnikových aplikací pro klíčové aplikace, které využívají aplikační brány. Poradce identifikuje instance služby Application Gateway, které nejsou nakonfigurované pro odolnost proti chybám. Pak navrhne nápravné akce, které můžete provést. Advisor identifikuje střední nebo velké aplikační brány s jednou instancí a doporučuje přidat aspoň jednu další instanci. Identifikuje taky jednu instanci a malé aplikační brány s více instancemi a doporučuje je migrovat na střední nebo velké SKU. Poradce doporučuje tyto akce, aby bylo zajištěno, že instance služby Application Gateway jsou nakonfigurovány tak, aby splňovaly aktuální požadavky smlouvy SLA pro tyto prostředky.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Ochrana dat virtuálního počítače před náhodným odstraněním

Nastavení zálohování virtuálních počítačů zajišťuje dostupnost důležitých podnikových dat a nabízí ochranu před náhodným odstraněním nebo poškozením. Advisor identifikuje virtuální počítače, u kterých není zapnuté zálohování, a doporučuje povolení zálohování. 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>Ujistěte se, že máte přístup k expertům Azure, když ho potřebujete.

Když pracujete s nejdůležitějším obchodním zatížením, je důležité mít přístup k technické podpoře, až ji budete potřebovat. Advisor identifikuje potenciální obchodní předplatná, která nemají technickou podporu zahrnutou v jejich plánech podpory. Doporučuje se upgradovat na možnost, která zahrnuje technickou podporu.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Vytváření upozornění na Azure Service Health, která se mají upozornit, když vás budou problémy s Azure týkat

Doporučujeme, abyste nastavili výstrahy Azure Service Health, abyste byli informováni o tom, že se vás týkají problémy se službou Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) je bezplatná služba, která poskytuje individuální pokyny a podporu v případě, že potíže se službou Azure ovlivňují. Advisor identifikuje odběry, u kterých nejsou nakonfigurované výstrahy, a doporučuje je nakonfigurovat.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurace koncových bodů Traffic Manager pro odolnost

Azure Traffic Manager profily s více než jedním koncovým bodem se vyšší dostupností, pokud některý z těchto koncových bodů selhává. Umístění koncových bodů v různých oblastech dál vylepšuje spolehlivost služby. Poradce identifikuje profily Traffic Manageru, kde je jenom jeden koncový bod a doporučuje přidat aspoň jeden další koncový bod v jiné oblasti.

Pokud jsou všechny koncové body v profilu Traffic Manager, který je nakonfigurovaný pro směrování blízkosti, ve stejné oblasti, můžou se uživatelé z jiných oblastí setkat se zpožděními připojení. Přidáním nebo přesunutím koncového bodu do jiné oblasti dojde ke zvýšení celkového výkonu a zajištění lepší dostupnosti v případě selhání všech koncových bodů v jedné oblasti. Poradce identifikuje Traffic Manager profily nakonfigurované pro směrování blízkosti, kde jsou všechny koncové body ve stejné oblasti. Doporučuje přidat nebo přesunout koncový bod do jiné oblasti Azure.

Pokud je pro geografické směrování nakonfigurovaný profil Traffic Manager, provoz se směruje do koncových bodů v závislosti na definovaných oblastech. V případě selhání oblasti není k dispozici žádné předdefinované převzetí služeb při selhání. Pokud máte koncový bod, ve kterém je místní seskupení nakonfigurované na **vše (World)**, můžete se vyhnout zahození provozu a zlepšit dostupnost služby. Služba Advisor identifikuje profily Traffic Manager nakonfigurovaných pro geografické směrování, kde není nakonfigurován žádný koncový bod pro místní seskupení **(World)**. Doporučuje se změnit konfiguraci tak, aby byl koncový bod **vše (World)**.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Použití obnovitelného odstranění v účtu služby Azure Storage k uložení a obnovení dat po náhodném přepsání nebo odstranění

Povolte [obnovitelné odstranění](../storage/blobs/soft-delete-overview.md) na svém účtu úložiště, aby se odstraněné objekty blob přešly do neodstraněného stavu místo trvalého odstranění. Při přepsání dat se vygeneruje obnovitelně odstraněný snímek, do kterého se uloží stav přepsaných dat. Použití obnovitelného odstranění umožňuje obnovení z náhodného odstranění nebo přepsání. Advisor identifikuje účty služby Azure Storage, které nemají povolené obnovitelné odstranění, a navrhuje, abyste je povolili.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurace brány VPN na aktivní – aktivní pro odolnost připojení

V konfiguraci aktivní-aktivní obě instance služby VPN Gateway navážou tunely VPN S2S na vaše místní zařízení VPN. Když dojde k události plánované údržby nebo neplánované události na jednu instanci brány, provoz se automaticky přepne na ostatní aktivní tunelové propojení IPsec. Azure Advisor identifikuje brány VPN, které nejsou nakonfigurované jako aktivní – aktivní, a navrhuje, abyste je nakonfigurovali pro vysokou dostupnost.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Použití provozních bran sítě VPN ke spouštění produkčních úloh

Azure Advisor kontroluje všechny brány VPN, které používají základní SKU a doporučuje místo toho použít produkční skladovou jednotku. Základní skladová položka je navržena pro vývoj a testování. Produkční skladové položky nabídky:
- Další tunely. 
- Podpora protokolu BGP. 
- Možnosti konfigurace aktivní – aktivní 
- Vlastní zásady IPSec/IKE. 
- Vyšší stabilita a dostupnost.

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>Zajištění odolnosti proti chybám virtuálního počítače (dočasně zakázáno)

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Poradce identifikuje virtuální počítače, které nejsou součástí skupiny dostupnosti, a doporučuje je přesunout do jedné. Tato konfigurace zajišťuje, že během plánované nebo neplánované údržby je k dispozici aspoň jeden virtuální počítač a splňuje smlouvu SLA pro virtuální počítače Azure. Můžete zvolit vytvoření skupiny dostupnosti pro virtuální počítač nebo přidání virtuálního počítače do existující skupiny dostupnosti.

> [!NOTE]
> Pokud se rozhodnete vytvořit skupinu dostupnosti, musíte do ní přidat aspoň jeden virtuální počítač. Doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti, aby bylo zajištěno, že během výpadku bude k dispozici alespoň jeden počítač.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>Zajištění odolnosti proti chybám u sady dostupnosti (dočasně zakázáno)

Pokud chcete zajistit redundanci pro vaši aplikaci, doporučujeme seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Poradce identifikuje skupiny dostupnosti, které obsahují jeden virtuální počítač a doporučuje do něj přidat jeden nebo více virtuálních počítačů.Tato konfigurace zajišťuje, že během plánované nebo neplánované údržby je k dispozici aspoň jeden virtuální počítač a splňuje smlouvu SLA pro virtuální počítače Azure.Můžete se rozhodnout vytvořit virtuální počítač nebo přidat existující virtuální počítač do skupiny dostupnosti.  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>Použití spravovaných disků ke zlepšení spolehlivosti dat (dočasně zakázáno)

Virtuální počítače, které jsou ve skupině dostupnosti s disky, které sdílejí buď účty úložiště nebo jednotky škálování úložiště, nejsou odolné vůči selháním jednotek škálování v jednom úložišti během výpadků. Advisor tyto skupiny dostupnosti identifikuje a doporučuje migraci na Azure Managed disks. Tato migrace zajistí, že disky virtuálních počítačů ve skupině dostupnosti jsou dostatečně izolované, aby se předešlo jednomu bodu selhání. 

## <a name="repair-invalid-log-alert-rules"></a>Opravit neplatná pravidla upozornění protokolu

Azure Advisor detekuje pravidla upozornění protokolu, která mají v oddílu podmínky zadány neplatné dotazy. Azure Monitor pravidla upozornění protokolů spouštějí dotazy v zadaném četnosti a výstrahy požáru na základě výsledků. Dotazy se můžou v průběhu času stát neplatnými kvůli změnám v odkazovaných prostředcích, tabulkách nebo příkazech. Poradce doporučuje opravy pro dotazy na výstrahy, aby nedocházelo k automatickému zakázání pravidel a zajistili monitorování pokrytí. Další informace najdete v tématu [řešení potíží s pravidly výstrah](../azure-monitor/platform/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid) .

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Konfigurace konzistentního režimu indexování v kolekci Azure Cosmos DB

Konfigurace Azure Cosmos DB kontejnerů pomocí režimu opožděného indexování může ovlivnit aktuálnost výsledků dotazu. Advisor detekuje kontejnery konfigurované tímto způsobem a doporučuje přepnout do konzistentního režimu. [Přečtěte si další informace o indexování zásad v Azure Cosmos DB.](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Konfigurace klíče oddílu pro kontejnery Azure Cosmos DB

Azure Advisor identifikuje Azure Cosmos DB nedělené kolekce, které se blíží své zřízené kvótě úložiště. Doporučuje se migrovat tyto kolekce do nových kolekcí s definicí klíče oddílu, aby je bylo možné automaticky škálovat službou. [Přečtěte si další informace o volbě klíče oddílu.](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Upgrade Azure Cosmos DB .NET SDK na nejnovější verzi z NuGet

Azure Advisor identifikuje Azure Cosmos DB účty, které používají starší verze sady .NET SDK. Doporučuje se upgradovat na nejnovější verzi nástroje NuGet, kde najdete nejnovější opravy, vylepšení výkonu a možnosti funkcí. [Přečtěte si další informace o Azure Cosmos DB .NET SDK.](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Upgrade sady Azure Cosmos DB Java SDK na nejnovější verzi z Mavenu

Azure Advisor identifikuje účty Azure Cosmos DB, které používají starší verze sady Java SDK. Doporučuje se upgradovat na nejnovější verzi z Maven, kde najdete nejnovější opravy, vylepšení výkonu a možnosti funkcí. [Přečtěte si další informace o Azure Cosmos DB Java SDK.](https://aka.ms/cosmosdb/sql-api-sdk-async-java)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Upgradovat Azure Cosmos DB Spark Connector na nejnovější verzi z Maven

Azure Advisor identifikuje účty Azure Cosmos DB, které používají starší verze konektoru Azure Cosmos DB Spark. Doporučuje se upgradovat na nejnovější verzi z Maven, kde najdete nejnovější opravy, vylepšení výkonu a možnosti funkcí. [Přečtěte si další informace o konektoru Azure Cosmos DB Spark.](https://aka.ms/cosmosdb/spark-connector)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>Zvažte přechod na Kafka 2,1 ve službě HDInsight 4,0.

Od 1. července 2020 nebudete moct vytvářet nové clustery Kafka pomocí Kafka 1,1 v Azure HDInsight 4,0. Stávající clustery budou fungovat beze změny, jen bez podpory Microsoftu. Zvažte přechod do Kafka 2,1 v HDInsight 4,0 do 30. června 2020, aby se předešlo potenciálnímu přerušení systému/podpory.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>Zvažte možnost upgradovat starší verze Sparku v clusterech HDInsight Spark.

Od 1. července 2020 nebudete moci vytvářet nové clustery Spark pomocí Spark 2,1 nebo 2,2 ve službě HDInsight 3,6. Pomocí Spark 2,3 ve službě HDInsight 4,0 nebudete moct vytvářet nové clustery Spark. Stávající clustery budou fungovat beze změny, jen bez podpory Microsoftu. 

## <a name="enable-virtual-machine-replication"></a>Povolení replikace virtuálních počítačů
Virtuální počítače, které nemají povolenou replikaci do jiné oblasti, nejsou odolné vůči nemožnostem regionálních výpadků. Replikace virtuálních počítačů redukuje nepříznivý dopad na firmu během výpadků v oblasti Azure. Advisor detekuje virtuální počítače, u kterých není replikace povolená, a doporučuje ji povolit. Pokud povolíte replikaci, můžete v případě výpadku rychle vytvořit virtuální počítače ve vzdálené oblasti Azure. [Přečtěte si další informace o replikaci virtuálních počítačů.](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>Upgrade na nejnovější verzi agenta Azure Connected Machine
[Agent připojeného počítače Azure](https://docs.microsoft.com/azure/azure-arc/servers/manage-agent) se pravidelně aktualizuje s opravami chyb, vylepšeními stability a novými funkcemi. Zjistili jsme prostředky, které nefungují na nejnovější verzi agenta Machine agent, a toto doporučení Advisoru vám pomůže upgradovat agenta na nejnovější verzi, aby se dosáhlo nejlepšího prostředí Azure ARC.

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>Zajištění integrity webu nepřepisováním názvu hostitele
Poradce doporučujeme, abyste se vyhnuli přepsání názvu hostitele při konfiguraci Application Gateway. Pokud pro front-end služby Application Gateway budete mít jinou doménu než pro přístup k back-endu, může dojít k poškození souborů cookie nebo adres URL pro přesměrování. Upozorňujeme, že to nemusí platit ve všech situacích a že určité kategorie back-endů (například rozhraní REST API) jsou na to obecně méně citlivé. Ujistěte se, že je back-end schopný si s tím poradit, nebo aktualizujte konfiguraci služby Application Gateway tak, aby se název hostitele pro back-end nemusel přepisovat. Při použití s App Service připojte k webové aplikaci vlastní název domény a vyhněte se použití *názvu hostitele. azurewebsites.NET směrem k back-endu.* [Přečtěte si další informace o vlastní doméně](https://aka.ms/appgw-advisor-usecustomdomain).

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Jak získat přístup k doporučením vysoké dostupnosti v Advisoru

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak otevřete [Poradce](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu Advisor vyberte kartu **Vysoká dostupnost** .

## <a name="next-steps"></a>Další kroky

Další informace o doporučeních služby Advisor najdete v těchto tématech:
* [Úvod do Advisoru](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Skóre Advisoru](azure-advisor-score.md)
* [Doporučení Advisoru týkající se cen](advisor-cost-recommendations.md)
* [Doporučení pro výkon Advisoru](advisor-performance-recommendations.md)
* [Doporučení pro zabezpečení Advisoru](advisor-security-recommendations.md)
* [Doporučení provozní kvality Advisoru](advisor-operational-excellence-recommendations.md)
