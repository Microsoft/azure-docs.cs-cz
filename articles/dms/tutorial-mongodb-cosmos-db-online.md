---
title: 'Kurz: migrace MongoDB do online režimu Azure Cosmos DB API pro MongoDB'
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat z místního prostředí MongoDB do Azure Cosmos DB API pro MongoDB online pomocí Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 09/25/2019
ms.openlocfilehash: 09a568f7cd0b8efaed4ee5210dde4000ca472529
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546785"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Kurz: migrace MongoDB do rozhraní API služby Azure Cosmos DB pro MongoDB online pomocí DMS

Azure Database Migration Service můžete použít k provedení online (minimálního výpadku) migrace databází z místní nebo cloudové instance MongoDB do Azure Cosmos DB API pro MongoDB.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí Azure Database Migration Service.
> * Spuštění migrace
> * Monitorujte migraci.
> * Až budete připraveni, dokončete migraci.

V tomto kurzu migrujete datovou sadu v MongoDB hostovaném na virtuálním počítači Azure, abyste Azure Cosmos DB rozhraní API pro MongoDB s minimálními výpadky pomocí Azure Database Migration Service. Pokud ještě nemáte nastavený zdroj MongoDB, přečtěte si článek [instalace a konfigurace MongoDB na virtuálním počítači s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Použití Azure Database Migration Service k provedení online migrace vyžaduje vytvoření instance založené na cenové úrovni Premium.

> [!IMPORTANT]
> Pro optimální prostředí migrace doporučuje Microsoft vytvořit instanci Azure Database Migration Service ve stejné oblasti Azure jako cílová databáze. Přesun dat mezi oblastmi nebo geografickými oblastmi může zpomalit proces migrace.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje online migraci z MongoDB Azure Cosmos DB a rozhraní API pro MongoDB. Offline migrace najdete v článku [migrace MongoDB do Azure Cosmos DB rozhraní API pro MongoDB v režimu offline pomocí DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* [Dokončete kroky před migrací](../cosmos-db/mongodb-pre-migration.md) , například odhad propustnosti, výběr klíče oddílu a zásady indexování.
* [Vytvořte rozhraní API Azure Cosmos DB pro účet MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který zajišťuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Pokud při instalaci virtuální sítě používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, tyto [koncové body](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) služby:
    >
    > * Koncový bod cílové databáze (například koncový bod SQL, Cosmos DB koncový bod atd.)
    > * Koncový bod úložiště
    > * Koncový bod služby Service Bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.

* Zajistěte, aby pravidla skupiny zabezpečení sítě (NSG) ve virtuální síti neblokovala následující komunikační porty: 53, 443, 445, 9354 a 10000-20000. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Otevřete bránu Windows Firewall, abyste povolili Azure Database Migration Service přístup ke zdrojovému serveru MongoDB, který je ve výchozím nastavení port TCP 27017.
* Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla firewallu, která Azure Database Migration Service umožní přístup ke zdrojovým databázím pro migraci.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná** .

   ![Zobrazení předplatných na portálu](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků** .

    ![Zobrazení poskytovatelů prostředků](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat** .

    ![Registrace poskytovatele prostředků](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek** , vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service** .

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit** .

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci Azure Database Migration Service.

5. Vyberte existující virtuální síť nebo vytvořte novou.

   Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojové instanci MongoDB a cílovému Azure Cosmos DBmu účtu.

   Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](https://aka.ms/DMSVnet).

6. Vyberte SKU z cenové úrovně Premium.

    > [!NOTE]
    > Online migrace se podporují jenom v případě, že používáte úroveň Premium. Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby** , vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service** .

    ![Vyhledat všechny instance Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Na obrazovce **služby Azure Database Migration Services** vyhledejte název instance Azure Database Migration Service, kterou jste vytvořili, a pak vyberte instanci.

    Alternativně můžete zjistit instanci služby Azure Database Migration Service z podokna hledání v Azure Portal.

    ![Použití podokna hledání v Azure Portal](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Vyberte **+ Nový projekt migrace** .

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **typ zdrojového serveru** vyberte možnost **MongoDB** , v textovém poli **typ CÍLOVÉHO serveru** vyberte možnost **CosmosDB (MongoDB API)** a potom pro **možnost zvolit typ aktivity** vyberte možnost **migrace online dat [Preview]** .

    ![Vytvořit Database Migration Service projekt](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Vyberte **Uložit** a potom vyberte **vytvořit a spustit aktivitu** a vytvořte tak projekt a spusťte aktivitu migrace.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti o zdroji** zadejte podrobnosti o připojení pro zdrojový server MongoDB.

   > [!IMPORTANT]
   > Azure Database Migration Service nepodporuje Azure Cosmos DB jako zdroj.

    Existují tři režimy připojení ke zdroji:
   * **Standardní režim** , který přijímá plně kvalifikovaný název domény nebo IP adresu, číslo portu a přihlašovací údaje pro připojení.
   * **Režim připojovacího řetězce** , který přijímá připojovací řetězec MongoDB, jak je popsáno v článku [Formát identifikátoru URI připojovacího řetězce](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Data ze služby Azure Storage** , která přijímá adresu URL SAS kontejneru objektů BLOB. Pokud má kontejner objektů BLOB BSON výpisy paměti vytvořené [nástrojem MongoDB bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/), vyberte objekt blob, který **obsahuje bson** , a zrušte jeho výběr, pokud kontejner obsahuje soubory JSON.

     Pokud vyberete tuto možnost, ujistěte se, že se připojovací řetězec účtu úložiště zobrazuje ve formátu:

     ```
     https://blobnameurl/container?SASKEY
     ```

     V závislosti na informacích o výpisu typu v Azure Storage mějte na paměti následující podrobnosti.

     * V případě výpisů BSON musí být data v kontejneru objektů BLOB ve formátu bsondump, aby byly datové soubory umístěny do složek pojmenovaných po obsahujících databázích ve formátu Collection. bson. Soubory metadat (pokud existují) by měly být pojmenovány pomocí *kolekce* Format.metadata.js.

     * V případě výpisů JSON musí být soubory v kontejneru objektů BLOB umístěny do složek pojmenovaných po nadřazených databázích. V rámci každé složky databáze musí být datové soubory umístěny do podsložky s názvem "data" a pojmenovány pomocí formátu *Collection* . JSON. Soubory metadat (pokud existují) musí být umístěny do podsložky s názvem "metadata" a pojmenovány pomocí stejného formátu, *Collection* . JSON. Soubory metadat musí být ve stejném formátu, který vytvořil nástroj MongoDB bsondump Tool.

    > [!IMPORTANT]
    > Doporučuje se použít certifikát podepsaný svým držitelem na serveru Mongo. Pokud se ale používá, připojte se prosím k serveru pomocí **režimu připojovacího řetězce** a ujistěte se, že váš připojovací řetězec má "".
    >
    >```
    >&sslVerifyCertificate=false
    >```

    IP adresu můžete použít v situacích, kdy není možné překlad názvů DNS.

   ![Zadání podrobností o zdroji](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Vyberte **Uložit** .

   > [!NOTE]
   > Adresa zdrojového serveru musí být adresa primárního serveru, pokud je zdrojem sada repliky, a směrovač, pokud je zdrojem cluster horizontálně dělené MongoDB. Pro cluster horizontálně dělené MongoDB musí být Azure Database Migration Service schopný připojit se k jednotlivým horizontálních oddílů v clusteru, což může vyžadovat otevření brány firewall na dalších počítačích.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **Podrobnosti cíle migrace** zadejte podrobnosti o připojení pro cílový Azure Cosmos DB účet, což je předběžně ZŘÍZENÉ rozhraní API služby Azure Cosmos DB pro účet MongoDB, na který migrujete data MongoDB.

    ![Zadání podrobností o cíli](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Vyberte **Uložit** .

## <a name="map-to-target-databases"></a>Mapování na cílové databáze

1. Na obrazovce **mapování na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

   Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

   Pokud se řetězec **vytvoří** vedle názvu databáze, indikuje, že Azure Database Migration Service nebyla nalezena cílová databáze a služba vytvoří databázi za vás.

   Pokud chcete v této fázi migrace sdílet propustnost v databázi, zadejte propustnost RU. V Cosmos DB můžete zřídit propustnost buď na úrovni databáze, nebo jednotlivě pro každou kolekci. Propustnost se měří v [jednotkách žádosti](https://docs.microsoft.com/azure/cosmos-db/request-units) (ru). Přečtěte si další informace o [cenách Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Mapování na cílové databáze](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Vyberte **Uložit** .

3. Na obrazovce **nastavení kolekce** rozbalte seznam kolekce a potom zkontrolujte seznam kolekcí, které budou migrovány.

   Azure Database Migration Service automaticky vybere všechny kolekce, které existují ve zdrojové instanci MongoDB, které neexistují na cílovém Azure Cosmos DB účtu. Pokud chcete znovu migrovat kolekce, které už obsahují data, musíte na této obrazovce explicitně vybrat kolekce.

   Můžete zadat počet ru, které mají kolekce používat. Ve většině případů by měla stačit hodnota mezi 500 (1000 minimum pro kolekce horizontálně dělené) a 4000. Azure Database Migration Service navrhuje inteligentní výchozí hodnoty na základě velikosti kolekce.

    > [!NOTE]
    > Migraci a shromažďování databáze můžete provádět paralelně pomocí několika instancí Azure Database Migration Service v případě potřeby zrychlit spuštění.

   Můžete také zadat horizontálních oddílů klíč, který bude využívat [dělení v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) pro zajištění optimální škálovatelnosti. Nezapomeňte si projít  [osvědčené postupy pro výběr klíče horizontálních oddílů/partition](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Pokud nemáte klíč oddílu, můžete pro lepší propustnost vždy použít **_ID** jako klíč horizontálních oddílů.

   ![Vybrat tabulky kolekcí](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Vyberte **Uložit** .

5. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

    ![Souhrn migrace](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci** .

   Zobrazí se okno aktivita migrace a zobrazí se **stav** aktivity.

   ![Stav aktivity](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorování migrace

* Na obrazovce aktivita migrace vyberte **aktualizovat** , aby se zobrazení aktualizovalo, dokud se **stav** migrace nezobrazuje jako **opakované přehrání** .

   > [!NOTE]
   > Můžete vybrat aktivitu pro získání podrobností o metrikách migrace na úrovni databáze a kolekce.

   ![Opětovné přehrání stavu aktivity](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Ověřit data v Cosmos DB

1. Proveďte změny ve zdrojové databázi MongoDB.
2. Připojte se k COSMOS DB a ověřte, jestli se data replikují ze zdrojového serveru MongoDB.

    ![Snímek obrazovky, který ukazuje, kde můžete ověřit, že se data replikují.](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Dokončete migraci

* Až budou všechny dokumenty ze zdroje dostupné v cíli služby COSMOS DB, v místní nabídce aktivity migrace vyberte **Dokončit** a dokončete migraci.

    Tato akce dokončí přehrávání všech nedokončených změn a dokončí migraci.

    ![Snímek obrazovky zobrazující možnost nabídky dokončit](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Optimalizace po migraci

Po migraci dat uložených v databázi MongoDB Azure Cosmos DB k rozhraní API pro MongoDB se můžete připojit k Azure Cosmos DB a spravovat data. Můžete také provést další kroky optimalizace po migraci, jako je například optimalizace zásad indexování, aktualizace výchozí úrovně konzistence nebo konfigurace globální distribuce pro účet Azure Cosmos DB. Další informace najdete v článku věnovaném [optimalizaci po migraci](../cosmos-db/mongodb-post-migration.md) .

## <a name="additional-resources"></a>Další materiály

* [Informace o Cosmos DB službě](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Další kroky

* Projděte si pokyny k migraci dalších scénářů v [Průvodci migrací databáze](https://datamigration.microsoft.com/)společnosti Microsoft.
