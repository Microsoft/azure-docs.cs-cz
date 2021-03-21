---
title: 'Kurz: migrace MongoDB do offline režimu pro Azure Cosmos DB rozhraní API pro MongoDB'
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat z místního prostředí MongoDB na rozhraní Azure Cosmos DB API pro MongoDB v režimu offline pomocí Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: b669870537ffb58d9ae7e8a5c65276d310ba6a7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722020"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Kurz: migrace MongoDB k rozhraní API Azure Cosmos DB pro MongoDB v režimu offline pomocí DMS

Pomocí Azure Database Migration Service můžete provést offline (jednorázovou) migraci databází z místní nebo cloudové instance služby MongoDB a Azure Cosmos DB rozhraní API pro MongoDB.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí Azure Database Migration Service.
> * Spuštění migrace
> * Monitorujte migraci.

V tomto kurzu migrujete datovou sadu v MongoDB hostovaném na virtuálním počítači Azure, abyste mohli rozhraní API pro MongoDB Azure Cosmos DB pomocí Azure Database Migration Service. Pokud ještě nemáte nastavený zdroj MongoDB, přečtěte si článek [instalace a konfigurace MongoDB na virtuálním počítači s Windows v Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu je potřeba provést následující:

* [Dokončete kroky před migrací](../cosmos-db/mongodb-pre-migration.md) , například odhad propustnosti, výběr klíče oddílu a zásady indexování.
* [Vytvořte rozhraní API Azure Cosmos DB pro účet MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který zajišťuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](../virtual-network/index.yml)a zejména v článcích rychlý Start s podrobnými údaji.

    > [!NOTE]
    > Pokud při instalaci virtuální sítě používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, tyto [koncové body](../virtual-network/virtual-network-service-endpoints-overview.md) služby:
    >
    > * Koncový bod cílové databáze (například koncový bod SQL, Cosmos DB koncový bod atd.)
    > * Koncový bod úložiště
    > * Koncový bod služby Service Bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.

* Zajistěte, aby pravidla skupiny zabezpečení sítě (NSG) ve virtuální síti neblokovala následující komunikační porty: 53, 443, 445, 9354 a 10000-20000. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Otevřete bránu Windows Firewall, abyste povolili Azure Database Migration Service přístup ke zdrojovému serveru MongoDB, který je ve výchozím nastavení port TCP 27017.
* Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla firewallu, která Azure Database Migration Service umožní přístup ke zdrojovým databázím pro migraci.

## <a name="configure-azure-cosmos-db-server-side-retries-for-efficient-migration"></a>Konfigurace Azure Cosmos DBch opakování na straně serveru pro efektivní migraci

Zákazníci, kteří migrují z Azure Cosmos DB MongoDB, přináší výhody funkcí zásad správného řízení prostředků, což zaručuje schopnost plně využít vaše zřízené zvýšení propustnosti/s. Azure Cosmos DB může v průběhu migrace omezit daný požadavek na službu migrace dat, pokud požadavek překročí zřízené požadavky na kontejner/s. pak se musí tento požadavek opakovat. Služba migrace dat je schopná provádět opakované pokusy, ale čas odezvy v rámci směrování sítě mezi službou migrace dat a Azure Cosmos DB dopad na celkovou dobu odezvy tohoto požadavku. Zvýšení doby odezvy pro omezené požadavky může zkrátit celkovou dobu potřebnou k migraci. Funkce *opakování na straně serveru* Azure Cosmos DB umožňuje službě zachytit kódy chyb omezení a opakovat s mnohem nižší dobou odezvy, což výrazně vylepšuje dobu odezvy na žádosti.

Funkci opakování na straně serveru můžete najít v okně *funkce* portálu Azure Cosmos DB.

![MongoDB SSR – funkce](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

A pokud je *zakázaný*, doporučujeme ho povolit, jak vidíte níže.

![Povolení SSR pro MongoDB](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci Azure Database Migration Service. 

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojové instanci MongoDB a cílovému Azure Cosmos DBmu účtu.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledat všechny instance Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na obrazovce **služby Azure Database Migration Services** vyhledejte název instance Azure Database Migration Service, kterou jste vytvořili, a pak vyberte instanci.

3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **typ zdrojového serveru** vyberte možnost **MongoDB**, v textovém poli **typ CÍLOVÉHO serveru** vyberte možnost **CosmosDB (MongoDB API)** a potom pro **zvolit typ aktivity** vyberte možnost **migrace offline dat**. 

    ![Vytvořit Database Migration Service projekt](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti o zdroji** zadejte podrobnosti o připojení pro zdrojový server MongoDB.

   > [!IMPORTANT]
   > Azure Database Migration Service nepodporuje Azure Cosmos DB jako zdroj.

    Existují tři režimy připojení ke zdroji:
   * **Standardní režim**, který přijímá plně kvalifikovaný název domény nebo IP adresu, číslo portu a přihlašovací údaje pro připojení.
   * **Režim připojovacího řetězce**, který přijímá připojovací řetězec MongoDB, jak je popsáno v článku [Formát identifikátoru URI připojovacího řetězce](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Data ze služby Azure Storage**, která přijímá adresu URL SAS kontejneru objektů BLOB. Pokud má kontejner objektů BLOB BSON výpisy paměti vytvořené [nástrojem MongoDB bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/), vyberte objekt blob, který **obsahuje bson** , a zrušte jeho výběr, pokud kontejner obsahuje soubory JSON.

     Pokud vyberete tuto možnost, ujistěte se, že se připojovací řetězec účtu úložiště zobrazuje ve formátu:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Tento připojovací řetězec SAS kontejneru objektů BLOB najdete v Azure Storage Exploreru. Vytvořením SAS pro příslušný kontejner získáte adresu URL ve výše požadovaném formátu.
     
     V závislosti na informacích o výpisu typu v Azure Storage mějte na paměti následující podrobnosti.

     * V případě výpisů BSON musí být data v kontejneru objektů BLOB ve formátu bsondump, aby byly datové soubory umístěny do složek pojmenovaných po obsahujících databázích ve formátu Collection. bson. Soubory metadat (pokud existují) by měly být pojmenovány pomocí *kolekce* Format.metadata.js.

     * V případě výpisů JSON musí být soubory v kontejneru objektů BLOB umístěny do složek pojmenovaných po nadřazených databázích. V rámci každé složky databáze musí být datové soubory umístěny do podsložky s názvem "data" a pojmenovány pomocí formátu *Collection*. JSON. Soubory metadat (pokud existují) musí být umístěny do podsložky s názvem "metadata" a pojmenovány pomocí stejného formátu, *Collection*. JSON. Soubory metadat musí být ve stejném formátu, který vytvořil nástroj MongoDB bsondump Tool.

    > [!IMPORTANT]
    > Doporučuje se použít certifikát podepsaný svým držitelem na serveru Mongo. Pokud se ale používá, připojte se prosím k serveru pomocí **režimu připojovacího řetězce** a ujistěte se, že váš připojovací řetězec má "".
    >
    >```
    >&sslVerifyCertificate=false
    >```

   V situacích, kdy není možný překlad názvů DNS, můžete použít také IP adresu.

   ![Zadání podrobností o zdroji](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **Podrobnosti cíle migrace** zadejte podrobnosti o připojení pro cílový Azure Cosmos DB účet, což je předběžně ZŘÍZENÉ rozhraní API služby Azure Cosmos DB pro účet MongoDB, na který migrujete data MongoDB.

    ![Zadání podrobností o cíli](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Vyberte **Uložit**.

## <a name="map-to-target-databases"></a>Mapování na cílové databáze

1. Na obrazovce **mapování na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    Pokud se řetězec **vytvoří** vedle názvu databáze, indikuje, že Azure Database Migration Service nebyla nalezena cílová databáze a služba vytvoří databázi za vás.

    V tomto okamžiku migrace můžete [zřídit propustnost](../cosmos-db/set-throughput.md). V Cosmos DB můžete zřídit propustnost buď na úrovni databáze, nebo jednotlivě pro každou kolekci. Propustnost se měří v [jednotkách žádosti](../cosmos-db/request-units.md) (ru). Přečtěte si další informace o [cenách Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapování na cílové databáze](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Vyberte **Uložit**.
3. Na obrazovce **nastavení kolekce** rozbalte seznam kolekce a potom zkontrolujte seznam kolekcí, které budou migrovány.

    Azure Database Migration Service automaticky vybere všechny kolekce, které existují ve zdrojové instanci MongoDB, které neexistují na cílovém Azure Cosmos DB účtu. Pokud chcete znovu migrovat kolekce, které už obsahují data, musíte v tomto okně explicitně vybrat kolekce.

    Můžete zadat množství ru, které mají kolekce používat. Azure Database Migration Service navrhuje inteligentní výchozí hodnoty na základě velikosti kolekce.

    > [!NOTE]
    > Migraci a shromažďování databáze můžete provádět paralelně pomocí několika instancí Azure Database Migration Service v případě potřeby zrychlit spuštění.

    Můžete také zadat horizontálních oddílů klíč, který bude využívat [dělení v Azure Cosmos DB](../cosmos-db/partitioning-overview.md) pro zajištění optimální škálovatelnosti. Nezapomeňte si projít  [osvědčené postupy pro výběr klíče horizontálních oddílů/partition](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Vybrat tabulky kolekcí](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Vyberte **Uložit**.

5. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

    ![Souhrn migrace](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

    Zobrazí se okno aktivita migrace a **stav** aktivity není **spuštěno**.

    ![Stav aktivity](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorování migrace

* Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

   > [!NOTE]
   > Můžete vybrat aktivitu pro získání podrobností o metrikách migrace na úrovni databáze a kolekce.

    ![Stav aktivity je dokončený.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Ověřit data v Cosmos DB

* Po dokončení migrace můžete zkontrolovat účet Azure Cosmos DB, abyste ověřili, že všechny kolekce byly úspěšně migrovány.

    ![Snímek obrazovky, který ukazuje, kde se má zkontrolovat váš Azure Cosmos DB účet, aby se ověřilo, že se úspěšně migrují všechny kolekce.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optimalizace po migraci

Po migraci dat uložených v databázi MongoDB Azure Cosmos DB k rozhraní API pro MongoDB se můžete připojit k Azure Cosmos DB a spravovat data. Můžete také provést další kroky optimalizace po migraci, jako je například optimalizace zásad indexování, aktualizace výchozí úrovně konzistence nebo konfigurace globální distribuce pro účet Azure Cosmos DB. Další informace najdete v článku věnovaném [optimalizaci po migraci](../cosmos-db/mongodb-post-migration.md) .

## <a name="additional-resources"></a>Další zdroje informací

* [Informace o Cosmos DB službě](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Další kroky

* Projděte si pokyny k migraci dalších scénářů v [Průvodci migrací databáze](https://datamigration.microsoft.com/)společnosti Microsoft.