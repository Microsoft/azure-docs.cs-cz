---
title: 'Kurz: Pomocí Azure Database Migration Service online migraci MongoDB API služby Azure Cosmos DB pro MongoDB | Dokumentace Microsoftu'
description: Zjistěte, jak migrovat místních MongoDB API služby Azure Cosmos DB pro MongoDB online s využitím Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 02/12/2019
ms.openlocfilehash: 95286b7a63471ee07f76276d8b4b63ca5f2aecce
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212835"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms-preview"></a>Kurz: Migrace MongoDB API služby Azure Cosmos DB pro MongoDB online pomocí DMS (Preview)
Azure Database Migration Service můžete použít k provedení migrace databází do online (minimálními prostoji) z místní nebo cloudové instance MongoDB API služby Azure Cosmos DB pro MongoDB.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvoření projektu migrace pomocí služby Azure Database Migration Service
> * Spuštění migrace
> * Monitorování migrace
> * Až budete připravení dokončete migraci.

V tomto kurzu migrujete datové sady v databáze MongoDB hostované ve virtuálním počítači Azure do Azure Cosmos DB přes rozhraní API pro MongoDB s minimálními prostoji s využitím Azure Database Migration Service. Pokud nemáte zdroji MongoDB již nastaveny, najdete v článku [nainstalovat a nakonfigurovat MongoDB na virtuálním počítači s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Použití Azure Database Migration Service online migrace vyžaduje vytvoření instance založené na cenovou úroveň Premium.

> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi nebo zeměpisných oblastech mohou zpomalit proces migrace.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje online migrace z MongoDB API služby Azure Cosmos DB pro MongoDB. Offline migrace, najdete v části [migraci MongoDB API služby Azure Cosmos DB pro MongoDB offline pomocí DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu je potřeba provést následující:
- [Vytvoření rozhraní API služby Azure Cosmos DB pro účet MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Vytvoření Azure Virtual Network (VNET) pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení site-to-site k vašich zdrojových serverů s místními pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Ujistěte se, že pravidla skupiny zabezpečení sítě VNET není blokovat následující komunikační porty: 53, 443, 9354, 445 a 12000. Další podrobnosti o filtrování provozu pomocí skupiny zabezpečení virtuální sítě Azure najdete v článku [Filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Změňte zdrojový server brány firewall umožňující Azure Database Migration Service přístup k serveru MongoDB zdroj, který ve výchozím nastavení je TCP port 27017.
- Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration
1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)
       
2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.
 
    ![Zobrazení poskytovatelů prostředků](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)
    
3.  Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.
 
    ![Registrace poskytovatele prostředků](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance
1.  Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2.  Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.
 
    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3.  Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Azure Database Migration Service. 

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service přístup k instanci MongoDB zdrojového a cílového účtu služby Azure Cosmos DB.

    Další informace o vytvoření virtuální sítě na webu Azure Portal najdete v článku [Vytvoření virtuální sítě pomocí webu Azure Portal](https://aka.ms/DMSVnet).

6. Vyberte SKU cenové úrovně Premium.

    > [!NOTE]
    > Online migrace jsou podporované jenom při použití na úrovni Premium. Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    Pokud potřebujete pomoc s výběrem správné úrovně služby Azure Database Migration Service, podívejte se na doporučení v blogovém příspěvku [tady](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7.  Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace
Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.
 
    ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Na obrazovce **Služby Azure Database Migration Service** vyhledejte název instance služby Azure Database Migration Service, kterou jste vytvořili, a vyberte ji.

    Alternativně můžete zjistit, instance služby Azure Database Migration z podokna vyhledávání na webu Azure portal.

    ![Použití pokokna vyhledávání na webu Azure Portal](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Vyberte **+ Nový projekt migrace**.

4. Na **nový projekt migrace** obrazovky, zadejte název projektu, v **serveru typ zdroje** textového pole, vyberte **MongoDB**v **cílový typ serveru**  textového pole, vyberte **služby cosmos DB (rozhraní API MongoDB)** a potom **zvolte typ aktivity**vyberte **migrace Online data [preview]**.

    ![Vytvořte projekt Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5.  Vyberte **Uložit**a pak vyberte **vytvoření a spuštění aktivity** vytvoření projektu a spuštění aktivity migrace.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji
1. Na **zdrojové podrobnosti** obrazovky, zadat podrobnosti připojení pro zdrojový server MongoDB.

    Existují tři režimy připojení ke zdroji:
       * **Standardní režim**, která přijímá použitím plně kvalifikovaného názvu domény nebo IP adresu a číslo portu přihlašovací údaje pro připojení.
       * **Režim připojovacího řetězce**, která přijímá řetězec připojení MongoDB, jak je popsáno v článku [formát připojovacího řetězce identifikátoru URI](https://docs.mongodb.com/manual/reference/connection-string/).
       * **Data ze služby Azure storage**, která přijímá adresy URL SAS kontejneru objektů blob. Vyberte **objekt Blob obsahuje BSON výpisy** Pokud kontejner objektů blob se výpisy BSON vytvářených MongoDB [bsondump nástroj](https://docs.mongodb.com/manual/reference/program/bsondump/)a vyberte ho zrušit, pokud kontejner obsahuje soubory JSON.

    Můžete použít IP adresu pro situace, ve které DNS není možné překlad.

   ![Zadání podrobností o zdroji](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Vyberte **Uložit**.

   > [!NOTE]
   > Zdrojová adresa serveru by měla být adresa primární, pokud je zdroj sady replik a směrovač Pokud zdrojem je horizontálně dělené clusterem MongoDB. Pro cluster s horizontálně dělené MongoDB Azure Database Migration Service musí být schopný se připojit k jednotlivých horizontálních oddílů v clusteru, což může vyžadovat otevření brány firewall na více počítačích.          

## <a name="specify-target-details"></a>Zadání podrobností o cíli
1. Na **podrobnosti o cíli migrace** obrazovky, zadat podrobnosti připojení pro cíl účtu Azure Cosmos DB, který je předem zřízené služby Azure Cosmos DB pro rozhraní API pro účet MongoDB, do kterého budete migrovat MongoDB data.

    ![Zadání podrobností o cíli](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Vyberte **Uložit**.

## <a name="map-to-target-databases"></a>Mapování na cílové databáze
1. Na **mapu, která bude cílová databáze** obrazovky, mapování zdrojové a cílové databáze pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    Pokud řetězec **vytvořit** se zobrazí vedle názvu databáze, znamená to, že Azure Database Migration Service se nepovedlo najít cílovou databázi a službu databáze se vytvoří za vás.

    V tomto okamžiku migrace, pro potřeby propustnosti sdílené složky v databázi zadejte propustnost RU. Propustnost na úrovni databáze nebo jednotlivě pro každou kolekci můžete zřizovat v databázi Cosmos DB. Propustnost se měří v [jednotek žádostí](https://docs.microsoft.com/azure/cosmos-db/request-units) (ru). Další informace o [ceny služby Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapování na cílové databáze](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Vyberte **Uložit**.

3. Na **nastavení kolekce** obrazovky rozbalte kolekce výpisu a pak si projděte seznam kolekcí, které se budou migrovat.

    Všimněte si, že služba Azure Database Migration Service automaticky vybere všechny kolekce, které existují v instanci MongoDB zdroje, které neexistují v cílovém účtu služby Azure Cosmos DB. Pokud chcete přenést kolekce, které již obsahují data, musíte explicitně vybrat kolekce na této obrazovce.

    Můžete zadat počet jednotek RU, požadované kolekce k použití. Ve většině případů by mělo stačit hodnotu 500 (minimálně 1 000 pro horizontálně dělené kolekce) až 4000. Azure Database Migration Service navrhuje inteligentních výchozích hodnot na základě velikosti kolekce.

    Můžete také zadat klíč horizontálního dělení, abyste mohli využívat [dělení ve službě Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) pro optimální škálovatelnost. Nezapomeňte si přečíst [osvědčené postupy pro výběr klíče horizontálních oddílů nebo oddíl](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Pokud nemáte klíč oddílu, můžete vždy použít **_id** jako klíč horizontálního oddílu pro vyšší propustnost.

    ![Výběr kolekce tabulek](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Vyberte **Uložit**.

5. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

    ![Shrnutí migrace](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Spuštění migrace
- Vyberte **Spustit migraci**.

   Zobrazí se okno aktivity migrace a **stav** aktivity se zobrazí.

   ![Stav aktivity](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorování migrace
- Na obrazovce aktivita migrace, vyberte **aktualizovat** aktualizovat zobrazení, dokud **stav** migrace ukazovat **Replaying**.

   > [!NOTE]
   > Můžete vybrat aktivit zobrazíte podrobnosti databáze a kolekce – migraci metriky na úrovni.

   ![Přehrání stav aktivity](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Ověření dat ve službě Cosmos DB

1. Změny zdrojové databáze MongoDB.
2. Připojení ke službě COSMOS DB k ověření, pokud data se replikují ze zdrojového serveru MongoDB.

    ![Přehrání stav aktivity](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)
 
## <a name="complete-the-migration"></a>Dokončení migrace

* Po dokončení všechny dokumenty ze zdroje jsou k dispozici na cílovém COSMOS DB, vyberte **Dokončit** místní nabídce aktivitu migrace k dokončení migrace.

    Tato akce dokončit přehráním všechny neuložené změny a dokončete migraci.

    ![Přehrání stav aktivity](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="additional-resources"></a>Další materiály

 * [Informace o službě cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Další postup
- Přečtěte si pokyny k migraci další scénáře v Microsoftu [Průvodce migrací databází](https://datamigration.microsoft.com/).