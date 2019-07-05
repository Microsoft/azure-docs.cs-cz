---
title: 'Kurz: Offline migrace MongoDB API služby Azure Cosmos DB pro MongoDB pomocí Azure Database Migration Service | Dokumentace Microsoftu'
description: Zjistěte, jak migrovat z MongoDB místního rozhraní API služby Azure Cosmos DB pro MongoDB do offline režimu s využitím Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: 2ff5ebefbe379edda94dcf8ac066027398e2f3f4
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565541"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Kurz: Migrace MongoDB API služby Azure Cosmos DB pro MongoDB offline pomocí DMS

Azure Database Migration Service můžete použít k provedení offline migrace (jednorázová) databází z místní nebo cloudové instance MongoDB API služby Azure Cosmos DB pro MongoDB.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vytvoření instance služby Azure Database Migration Service.
> * Vytvořte projekt migrace s využitím Azure Database Migration Service.
> * Spuštění migrace
> * Monitorování migrace

V tomto kurzu migrujete datové sady v databáze MongoDB hostované ve virtuálním počítači Azure do Azure Cosmos DB přes rozhraní API pro MongoDB s využitím Azure Database Migration Service. Pokud nemáte zdroji MongoDB již nastaveny, najdete v článku [nainstalovat a nakonfigurovat MongoDB na virtuálním počítači s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* [Dokončit před migrací](../cosmos-db/mongodb-pre-migration.md) kroky, jako třeba odhadnout propustnost, volba klíče oddílu a zásady indexování.
* [Vytvoření rozhraní API služby Azure Cosmos DB pro účet MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Vytvoření služby Azure Virtual Network (VNet) pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení site-to-site k vašich zdrojových serverů s místními pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace o vytvoření virtuální sítě, najdete v článku [dokumentace k Virtual Network](https://docs.microsoft.com/azure/virtual-network/)a hlavně článků rychlý start s podrobný.

    > [!NOTE]
    > Při nastavení virtuální sítě, pokud používáte ExpressRoute se síť vytvoření partnerského vztahu Microsoftu, přidejte následující službu [koncové body](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) k podsíti, ve kterém se zřídí služby:
    >
    > * Koncový bod databázového cíl (například koncový bod SQL, koncového bodu služby Cosmos DB a tak dále)
    > * Koncový bod úložiště
    > * Koncový bod služby Service bus
    >
    > Tato konfigurace je nezbytná, protože služba Azure Database Migration Service nemá připojení k Internetu.

* Ujistěte se, že není pravidla skupiny zabezpečení sítě virtuální sítě (NSG) blokovat následující komunikační porty: 53, 443, 445, 9354 a 10000 20000. Další podrobnosti o filtrování provozu skupiny zabezpečení sítě Azure VNet najdete v článku [filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Otevřete bránu Windows firewall a povolit Azure Database Migration Service přístup k serveru MongoDB zdroj, který ve výchozím nastavení je TCP port 27017.
* Při použití zařízení brány firewall u zdrojových databází, budete muset přidat pravidla firewallu povolující Azure Database Migration Service přístup k zdrojových databází pro migraci.

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

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Azure Database Migration Service. 

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service přístup k instanci MongoDB zdrojového a cílového účtu služby Azure Cosmos DB.

    Další informace o tom, jak vytvořit síť VNet na portálu Azure portal najdete v článku [vytvořit virtuální síť pomocí webu Azure portal](https://aka.ms/DMSVnet).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledejte všechny instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na **Azure Database Migration Service** obrazovky, vyhledejte název služby Azure Database Migration Service instanci, kterou jste vytvořili a pak vyberte instanci.

3. Vyberte **+ Nový projekt migrace**.

4. Na **nový projekt migrace** obrazovky, zadejte název projektu, v **serveru typ zdroje** textového pole, vyberte **MongoDB**v **cílový typ serveru**  textového pole, vyberte **služby cosmos DB (rozhraní API MongoDB)** a potom **zvolte typ aktivity**vyberte **migrace Offline dat**. 

    ![Vytvořte projekt Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na **zdrojové podrobnosti** obrazovky, zadat podrobnosti připojení pro zdrojový server MongoDB.

    Existují tři režimy připojení ke zdroji:
   * **Standardní režim**, která přijímá použitím plně kvalifikovaného názvu domény nebo IP adresu a číslo portu přihlašovací údaje pro připojení.
   * **Režim připojovacího řetězce**, která přijímá řetězec připojení MongoDB, jak je popsáno v článku [formát připojovacího řetězce identifikátoru URI](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Data ze služby Azure storage**, která přijímá adresy URL SAS kontejneru objektů blob. Vyberte **objekt Blob obsahuje BSON výpisy** Pokud kontejner objektů blob se výpisy BSON vytvářených MongoDB [bsondump nástroj](https://docs.mongodb.com/manual/reference/program/bsondump/)a vyberte ho zrušit, pokud kontejner obsahuje soubory JSON.

     Pokud vyberete tuto možnost, ujistěte se, že připojovací řetězec účtu úložiště se zobrazí ve formátu:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Na základě typu výpis informací ve službě Azure storage, mějte také následující podrobnosti.

     * BSON výpisy dat v rámci kontejneru objektů blob musí být ve formátu bsondump tak, že datové soubory jsou umístěny do složky s názvem po obsahující databází ve formátu collection.bson. Soubory metadat (pokud existuje) by měly být pojmenovány ve formátu *kolekce*. metadata.json.

     * Pro výpisy JSON musí být umístěna souborů v kontejneru objektů blob do složky s názvem po obsahující databáze. V rámci každé složky databáze datové soubory musí být umístěn v podsložce s názvem "data" a s názvem ve formátu *kolekce*.json. Soubory metadat (pokud existuje) musí být umístěny v podsložce s názvem "metadat" a s názvem ve stejném formátu *kolekce*.json. Soubory metadat musí být ve stejném formátu jako vytvořený nástrojem bsondump MongoDB.

    > [!IMPORTANT]
    > Nedoporučuje se používat certifikát podepsaný svým držitelem na serveru pro mongodb. Nicméně, pokud používá, připojte se k serveru pomocí **režimu připojovacího řetězce** a zajistěte, aby váš připojovací řetězec ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

   V situacích, kdy není možný překlad názvů DNS, můžete použít také IP adresu.

   ![Zadání podrobností o zdroji](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na **podrobnosti o cíli migrace** obrazovky, zadat podrobnosti připojení pro cíl účtu Azure Cosmos DB, který je předem zřízené služby Azure Cosmos DB pro rozhraní API pro účet MongoDB, do kterého budete migrovat MongoDB data.

    ![Zadání podrobností o cíli](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Vyberte **Uložit**.

## <a name="map-to-target-databases"></a>Mapování na cílové databáze

1. Na **mapu, která bude cílová databáze** obrazovky, mapování zdrojové a cílové databáze pro migraci.

    Pokud cílová databáze obsahuje název stejné databáze jako zdrojové databáze, vybere Azure Database Migration Service ve výchozím nastavení cílové databázi.

    Pokud řetězec **vytvořit** se zobrazí vedle názvu databáze, znamená to, že služba Azure Database Migration Service se nepovedlo najít cílovou databázi a službu databáze se vytvoří za vás.

    V tomto okamžiku migrace, můžete [zřídit propustnost](https://docs.microsoft.com/azure/cosmos-db/set-throughput). Propustnost na úrovni databáze nebo jednotlivě pro každou kolekci můžete zřizovat v databázi Cosmos DB. Propustnost se měří v [jednotek žádostí](https://docs.microsoft.com/azure/cosmos-db/request-units) (ru). Další informace o [ceny služby Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapování na cílové databáze](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Vyberte **Uložit**.
3. Na **nastavení kolekce** obrazovky rozbalte kolekce výpisu a pak si projděte seznam kolekcí, které se budou migrovat.

    Azure Database Migration Service automaticky vybere všechny kolekce, které existují v instanci MongoDB zdroje, které neexistují v cílovém účtu služby Azure Cosmos DB. Pokud chcete přenést kolekce, které již obsahují data, musíte explicitně vyberte kolekce, v tomto okně.

    Můžete zadat množství RU, které chcete, aby kolekce k použití. Azure Database Migration Service navrhuje inteligentních výchozích hodnot na základě velikosti kolekce.

    > [!NOTE]
    > Použití několika instancí služby Azure Database Migration Service, v případě potřeby, jak zrychlit spuštění paralelní provádění migrace databáze a kolekce.

    Můžete také zadat klíč horizontálního dělení, abyste mohli využívat [dělení ve službě Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) pro optimální škálovatelnost. Nezapomeňte si přečíst [osvědčené postupy pro výběr klíče horizontálních oddílů nebo oddíl](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Výběr kolekce tabulek](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Vyberte **Uložit**.

5. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

    ![Shrnutí migrace](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **stav** aktivity je **Nezahájeno**.

    ![Stav aktivity](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorování migrace

* Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

   > [!NOTE]
   > Můžete vybrat aktivit zobrazíte podrobnosti databáze a kolekce – migraci metriky na úrovni.

    ![Stav aktivity dokončeno](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Ověření dat ve službě Cosmos DB

* Po dokončení migrace můžete zkontrolovat svůj účet služby Azure Cosmos DB k ověření, že se úspěšně migrovaly všechny kolekce.

    ![Stav aktivity dokončeno](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optimalizace po migraci

Po dokončení migrace dat uložených v databázi MongoDB API služby Azure Cosmos DB pro MongoDB můžete připojit ke službě Azure Cosmos DB a spravovat data. Můžete také provést další kroky po migraci optimalizace, jako je třeba optimalizace zásady indexování, aktualizovat výchozí úroveň konzistence nebo konfigurace globální distribuce pro váš účet Azure Cosmos DB. Další informace najdete v tématu [po migraci optimalizace](../cosmos-db/mongodb-post-migration.md) článku.

## <a name="additional-resources"></a>Další materiály

* [Informace o službě cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Další postup

* Přečtěte si pokyny k migraci další scénáře v Microsoftu [Průvodce migrací databází](https://datamigration.microsoft.com/).
