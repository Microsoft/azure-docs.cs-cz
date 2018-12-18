---
title: 'Kurz: Offline migrace MongoDB do rozhraní API Mongodb služby Azure Cosmos DB pomocí Azure Database Migration Service | Dokumentace Microsoftu'
description: Zjistěte, jak migrovat z MongoDB v místním Azure Cosmos DB Mongo rozhraní API do offline režimu s využitím Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 521c083f498c21dae38183ea9ee90af5dd395b30
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538544"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-mongo-api-offline-using-dms"></a>Kurz: Migrace MongoDB API služby Azure Cosmos DB Mongo offline pomocí DMS
Azure Database Migration Service můžete použít k provedení offline migrace (jednorázová) databází z místní nebo cloudové instance MongoDB do rozhraní API Mongodb služby Azure Cosmos DB.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvoření projektu migrace pomocí služby Azure Database Migration Service
> * Spuštění migrace
> * Monitorování migrace

V tomto kurzu, migrujete **vás Wingtips** datové sady do databáze MongoDB hostované ve virtuálním počítači Azure k rozhraní API služby Azure Cosmos DB pro MongoDB s využitím Azure Database Migration Service. Pokud nemáte zdroji MongoDB již nastaveny, najdete v článku [nainstalovat a nakonfigurovat MongoDB na virtuálním počítači s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu je potřeba provést následující:
- [Vytvoření účtu služby Azure Cosmos DB API pro MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Vytvořte pro službu Azure Database Migration Service virtuální síť s použitím modelu nasazení Azure Resource Manager, který poskytuje možnosti připojení typu Site-to-Site k místním zdrojovým serverům prostřednictvím [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo sítě [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Ujistěte se, že vaše pravidla virtuální sítě (VNET) skupiny zabezpečení sítě Azure není blokovat následující komunikační porty: 53, 443, 9354, 445 a 12000. Další podrobnosti o filtrování provozu pomocí skupiny zabezpečení virtuální sítě Azure najdete v článku [Filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Otevřete bránu Windows firewall a povolit službu Azure Database Migration Service přístup k serveru MongoDB zdroj, který ve výchozím nastavení je TCP port 27017.
- Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration
1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.
 
    ![Zobrazení poskytovatelů prostředků](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.
 
    ![Registrace poskytovatele prostředků](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance
1.  Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.
 
    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Azure Database Migration Service. 

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru a cílové instanci služby Azure SQL Database.

    Další informace o vytvoření virtuální sítě na webu Azure Portal najdete v článku [Vytvoření virtuální sítě pomocí webu Azure Portal](https://aka.ms/DMSVnet).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    Pokud potřebujete pomoc při výběru správné úrovně služby Azure Database Migration Service, podívejte se na doporučení v blogovém příspěvku [tady](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace
Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.
 
      ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na obrazovce **Služby Azure Database Migration Service** vyhledejte název instance služby Azure Database Migration Service, kterou jste vytvořili, a vyberte ji.

3. Vyberte **+ Nový projekt migrace**.

4. Na **nový projekt migrace** obrazovky, zadejte název projektu, v **serveru typ zdroje** textového pole, vyberte **MongoDB**v **cílový typ serveru**  textového pole, vyberte **služby cosmos DB (rozhraní API MongoDB)** a potom **zvolte typ aktivity**vyberte **migrace Offline dat**. 

    ![Vytvořte projekt Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji
1. Na **zdrojové podrobnosti** obrazovky, zadat podrobnosti připojení pro zdrojový server MongoDB.
    
   Můžete také použít režim připojovacího řetězce a zadejte umístění pro kontejner úložiště souboru blogu, ve kterém jste zálohované dat kolekce, kterou chcete migrovat.

   > [!NOTE]
   > Azure Database Migration Service můžete také migrovat bson dokumentů nebo dokumentů json do kolekce rozhraní API Mongodb služby Azure Cosmos DB.
    
   V situacích, kdy není možný překlad názvů DNS, můžete použít také IP adresu.

   ![Zadání podrobností o zdroji](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli
1. Na **podrobnosti o cíli migrace** obrazovky, zadat podrobnosti připojení pro cíl účtu Azure Cosmos DB, který je předem zřízená účtu Azure Cosmos DB MongoDB, do kterého budete migrovat MongoDB data.

    ![Zadání podrobností o cíli](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Vyberte **Uložit**.

## <a name="map-to-target-databases"></a>Mapování na cílové databáze
1. Na **mapu, která bude cílová databáze** obrazovky, mapování zdrojové a cílové databáze pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    Pokud řetězec **vytvořit** se zobrazí vedle názvu databáze, znamená to, že Azure Database Migration Service se nepovedlo najít cílovou databázi a službu databáze se vytvoří za vás.

    V tomto okamžiku migrace, můžete [zřídit propustnost](https://docs.microsoft.com/azure/cosmos-db/set-throughput). Propustnost na úrovni databáze nebo jednotlivě pro každou kolekci můžete zřizovat v databázi Cosmos DB. Propustnost se měří v [jednotek žádostí](https://docs.microsoft.com/azure/cosmos-db/request-units) (ru). Další informace o [ceny služby Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapování na cílové databáze](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Vyberte **Uložit**.
3. Na **nastavení kolekce** obrazovky rozbalte kolekce výpisu a pak si projděte seznam kolekcí, které se budou migrovat.

    Všimněte si, že služba Azure Database Migration Service automaticky vybere všechny kolekce, které existují v instanci MongoDB zdroje, které neexistují v cílovém účtu služby Azure Cosmos DB. Pokud chcete přenést kolekce, které již obsahují data, musíte explicitně vyberte kolekce, v tomto okně.

    Můžete zadat množství RU, které chcete, aby kolekce k použití. Azure Database Migration Service navrhuje inteligentních výchozích hodnot na základě velikosti kolekce.

    Můžete také zadat klíč horizontálního dělení, abyste mohli využívat [dělení ve službě Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) pro optimální škálovatelnost. Nezapomeňte si přečíst [osvědčené postupy pro výběr klíče horizontálních oddílů nebo oddíl](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Výběr kolekce tabulek](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Vyberte **Uložit**.

5. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

    ![Shrnutí migrace](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace
- Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **stav** aktivity je **Nezahájeno**.

    ![Stav aktivity](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorování migrace
- Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

   > [!NOTE]
   > Můžete vybrat aktivit zobrazíte podrobnosti databáze a kolekce – migraci metriky na úrovni.

    ![Stav aktivity dokončeno](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Ověření dat ve službě Cosmos DB

- Po dokončení migrace můžete zkontrolovat, váš účet Azure Cosmos DB API pro MongoDB. Chcete-li ověřit, že se úspěšně migrovaly všechny kolekce.

    ![Stav aktivity dokončeno](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>Další materiály

 * [Informace o službě cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Další postup
- Přečtěte si pokyny k migraci další scénáře v Microsoftu [Průvodce migrací databází](https://datamigration.microsoft.com/).