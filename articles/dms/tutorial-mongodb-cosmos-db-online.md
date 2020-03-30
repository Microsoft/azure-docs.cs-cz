---
title: 'Kurz: Migrace MongoDB online do rozhraní API Azure Cosmos DB pro MongoDB'
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat z místního MongoDB do rozhraní API Azure Cosmos DB pro MongoDB online pomocí služby Migrace databáze Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 66375d83dca4edef17919e3b493d5e45be37cc40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255623"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Kurz: Migrace MongoDB do rozhraní API Azure Cosmos DB pro MongoDB online pomocí DMS

Službu migrace databáze Azure můžete použít k provedení online (minimální prostoje) migrace databází z místní nebo cloudové instance MongoDB do rozhraní API Azure Cosmos DB pro MongoDB.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí služby Migrace databáze Azure.
> * Spuštění migrace
> * Monitorování migrace
> * Až budete připraveni, dokončete migraci.

V tomto kurzu migrujete datovou sadu v MongoDB hostované ve virtuálním počítači Azure do rozhraní API Azure Cosmos DB pro MongoDB s minimálními prostoji pomocí služby Migrace databáze Azure. Pokud ještě nemáte nastavený zdroj MongoDB, přečtěte si článek [Instalace a konfigurace MongoDB na virtuálním počítači s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Použití služby Migrace databáze Azure k provedení migrace online vyžaduje vytvoření instance založené na cenové úrovni Premium.

> [!IMPORTANT]
> Pro optimální prostředí migrace Microsoft doporučuje vytvořit instanci služby Migrace databáze Azure ve stejné oblasti Azure jako cílová databáze. Přesouvání dat mezi oblastmi nebo zeměpisnými oblastmi může proces migrace zpomalit.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje online migraci z MongoDB do rozhraní API Azure Cosmos DB pro MongoDB. Offline migraci najdete v [tématu Migrace MongoDB do rozhraní API Azure Cosmos DB pro MongoDB offline pomocí DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* [Dokončete kroky před migrací,](../cosmos-db/mongodb-pre-migration.md) jako je odhad propustnosti, výběr klíče oddílu a zásady indexování.
* [Vytvořte rozhraní API Azure Cosmos DB pro účet MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Pokud během instalace virtuální sítě používáte ExpressRoute s partnerským vztahem k síti společnosti Microsoft, přidejte do podsítě, ve které bude služba zřízena, následující [koncové body služby:](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
    >
    > * Cílový koncový bod databáze (například koncový bod SQL, koncový bod Cosmos DB a tak dále)
    > * Koncový bod úložiště
    > * Koncový bod sběrnice
    >
    > Tato konfigurace je nezbytná, protože služba Azure Database Migration Service nemá připojení k internetu.

* Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě (NSG) neblokují následující komunikační porty: 53, 443, 445, 9354 a 10000-20000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Otevřete bránu firewall systému Windows, abyste povolili službě Azure Database Migration Service přístup ke zdrojovému serveru MongoDB, který je ve výchozím nastavení portTCP 27017.
* Při použití zařízení brány firewall před zdrojovou databází, budete muset přidat pravidla brány firewall povolit Azure Database Migration Service pro přístup ke zdrojové databáze pro migraci.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Migrace databáze Azure, a pak vyberte **zprostředkovatele prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Migrace databáze Azure.

5. Vyberte existující virtuální síť nebo vytvořte novou.

   Virtuální síť poskytuje službě Migrace databáze Azure přístup ke zdrojové instanci MongoDB a cílovému účtu Azure Cosmos DB.

   Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

6. Vyberte skladovou položku z cenové úrovně Premium.

    > [!NOTE]
    > Migrace online jsou podporovány pouze při použití úrovně Premium. Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Migrace databáze Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Na obrazovce **Azure Database Migration Services** vyhledejte název instance služby Migrace databáze Azure, kterou jste vytvořili, a vyberte instanci.

    Alternativně můžete zjistit instanci služby Migrace databáze Azure z podokna vyhledávání na webu Azure Portal.

    ![Použití podokna Hledání na webu Azure Portal](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli Typ **zdrojového serveru** vyberte **MongoDB**, v textovém poli **Typ cílového serveru,** vyberte **CosmosDB (Rozhraní API MongoDB)** a pak pro **Zvolit typ aktivity**vyberte **Online migraci dat [preview]**.

    ![Vytvořit projekt služby migrace databáze](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Vyberte **Uložit**a pak vyberte **Vytvořit a spustit aktivitu,** abyste vytvořili projekt a spusťte aktivitu migrace.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje** zadejte podrobnosti o připojení pro zdrojový server MongoDB.

   > [!IMPORTANT]
   > Služba migrace databáze Azure nepodporuje Azure Cosmos DB jako zdroj.

    Ke zdroji se lze připojit třemi režimy:
   * **Standardní režim**, který přijímá plně kvalifikovaný název domény nebo ADRESU IP, číslo portu a přihlašovací údaje připojení.
   * **Režim připojovacího řetězce**, který přijímá připojovací řetězec MongoDB, jak je popsáno v článku [Formát identifikátoru URI připojovacího řetězce](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Data z úložiště Azure**, která přijímá adresu URL SAS kontejneru objektů blob. Vyberte **objekt blob obsahuje výpisy BSON,** pokud kontejner objektů blob obsahuje výpisy BSON vytvořené [nástrojem bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/)MongoDB , a zrušte výběr, pokud kontejner obsahuje soubory JSON.

     Pokud vyberete tuto možnost, ujistěte se, že se ve formátu zobrazí připojovací řetězec účtu úložiště:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Také na základě informací o výpisu typu ve službě Azure Storage mějte na paměti následující podrobnosti.

     * Pro bson výpisy, data v kontejneru objektů blob musí být ve formátu bsondump, tak, aby datové soubory jsou umístěny do složek pojmenovaný ch obsahující databáze ve formátu collection.bson. Soubory metadat (pokud existuje) by měly být pojmenovány pomocí *kolekce*formátu .metadata.json.

     * Pro výpisy JSON soubory v kontejneru objektů blob musí být umístěny do složek pojmenovaných po obsahující databáze. V rámci každé složky databáze musí být datové soubory umístěny do podsložky s názvem "data" a pojmenovány pomocí *kolekce*formátu json. Soubory metadat (pokud existuje) musí být umístěny v podsložce s názvem "metadata" a pojmenované ve stejném formátu, *kolekce*.json. Soubory metadat musí být ve stejném formátu jako vytvořené nástrojem bsondump MongoDB.

    > [!IMPORTANT]
    > Nedoporučuje se používat certifikát podepsaný svým držitelem na serveru mongo. Pokud je však použit, připojte se k serveru pomocí **režimu připojovacího řetězce** a ujistěte se, že připojovací řetězec má ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

    Adresu IP můžete použít pro situace, ve kterých není možné překlad názvů DNS.

   ![Zadání podrobností o zdroji](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Vyberte **Uložit**.

   > [!NOTE]
   > Adresa zdrojového serveru by měla být adresou primární, pokud je zdrojem sada replik, a směrovač, pokud je zdrojem cluster MongoDB s rozdílovými rozdíly. Pro oborové MongoDB clusteru služby Azure Database Migration Service musí být možné se připojit k jednotlivým úlomky v clusteru, což může vyžadovat otevření brány firewall na více počítačích.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **podrobnosti cíle migrace** zadejte podrobnosti o připojení pro cílový účet Azure Cosmos DB, což je předem zřízené rozhraní API Azure Cosmos DB pro účet MongoDB, do kterého migrujete data MongoDB.

    ![Zadání podrobností o cíli](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Vyberte **Uložit**.

## <a name="map-to-target-databases"></a>Mapování na cílové databáze

1. Na obrazovce **Mapa k cílovým databázím** namapujte zdrojovou a cílovou databázi pro migraci.

   Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Migrace databáze Azure vybere cílovou databázi ve výchozím nastavení.

   Pokud se vedle názvu databáze zobrazí řetězec **Create,** znamená to, že služba migrace databáze Azure nenalezla cílovou databázi a služba databázi vytvoří za vás.

   V tomto okamžiku migrace, pokud chcete sdílet propustnost v databázi, zadejte propustnost RU. V Cosmos DB, můžete zřídit propustnost buď na úrovni databáze nebo jednotlivě pro každou kolekci. Propustnost se měří v [jednotkách požadavků](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU). Další informace o [cenách Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Mapování na cílové databáze](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Vyberte **Uložit**.

3. Na obrazovce **nastavení Kolekce** rozbalte výpis kolekcí a zkontrolujte seznam kolekcí, které budou migrovány.

   Služba migrace databáze Azure automaticky vybere všechny kolekce, které existují ve zdrojové instanci MongoDB, které neexistují na cílovém účtu Azure Cosmos DB. Pokud chcete znovu emigrovat kolekce, které již obsahují data, musíte explicitně vybrat kolekce na této obrazovce.

   Můžete zadat počet ru, které chcete použít kolekce. Ve většině případů by měla stačit hodnota mezi 500 (1000 minimum pro oddílové kolekce) a 4000. Služba migrace databáze Azure navrhuje inteligentní výchozí hodnoty na základě velikosti kolekce.

    > [!NOTE]
    > V případě potřeby proveďte migraci a shromažďování databáze souběžně s použitím více instancí služby Migrace databáze Azure, abyste urychlili spuštění.

   Můžete také zadat klíč horizontálního oddílu, který můžete využít [možnosti dělení v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) pro optimální škálovatelnost. Zkontrolujte osvědčené [postupy pro výběr klíče súlot/oddíl](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Pokud nemáte klíč oddílu, můžete vždy použít **_id** jako klíč střepu pro lepší propustnost.

   ![Výběr tabulek kolekcí](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Vyberte **Uložit**.

5. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

    ![Souhrn migrace](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

   Zobrazí se okno aktivity migrace a zobrazí se **stav** aktivity.

   ![Stav aktivity](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorování migrace

* Na obrazovce aktivity migrace vyberte **Aktualizovat,** chcete-li aktualizovat zobrazení, dokud se **stav** migrace nezobrazí jako **Přehrání**.

   > [!NOTE]
   > Můžete vybrat aktivitu získat podrobnosti o metriky migrace na úrovni databáze a kolekce.

   ![Přehrání stavu aktivity](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Ověření dat v Cosmos DB

1. Proveďte změny ve zdrojové databázi MongoDB.
2. Připojte se k COSMOS DB a ověřte, zda jsou data replikována ze zdrojového serveru MongoDB.

    ![Přehrání stavu aktivity](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Dokončení migrace

* Po všechny dokumenty ze zdroje jsou k dispozici na cíl COSMOS DB, vyberte **Dokončit** z místní nabídky aktivity migrace k dokončení migrace.

    Tato akce dokončí přehrání všech čekajících změn a dokončí migraci.

    ![Přehrání stavu aktivity](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Optimalizace po migraci

Po migraci dat uložených v databázi MongoDB do rozhraní API Azure Cosmos DB pro MongoDB se můžete připojit k Azure Cosmos DB a spravovat data. Můžete také provést další kroky po migraci, jako je optimalizace zásad indexování, aktualizace výchozí úrovně konzistence nebo konfigurace globální distribuce pro váš účet Azure Cosmos DB. Další informace naleznete v článku [optimalizace po migraci.](../cosmos-db/mongodb-post-migration.md)

## <a name="additional-resources"></a>Další zdroje

* [Informace o službě Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Další kroky

* Přečtěte si pokyny k migraci pro další scénáře v [Příručce pro migraci databáze](https://datamigration.microsoft.com/)společnosti Microsoft .
