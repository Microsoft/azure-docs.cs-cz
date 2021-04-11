---
title: 'Kurz: migrace MongoDB do offline režimu pro Azure Cosmos DB rozhraní API pro MongoDB'
titleSuffix: Azure Database Migration Service
description: Migrace z místního prostředí z MongoDB do Azure Cosmos DB API pro MongoDB v režimu offline pomocí Azure Database Migration Service.
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
ms.openlocfilehash: 8977bb90087d9d3d4962d7eda50903d97da93539
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443863"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Kurz: migrace MongoDB do rozhraní Azure Cosmos DB API pro MongoDB offline

Použijte Azure Database Migration Service k provedení offline, jednorázové migrace databází z místní nebo cloudové instance MongoDB do rozhraní Azure Cosmos DB API pro MongoDB.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí Azure Database Migration Service.
> * Spuštění migrace
> * Monitorujte migraci.

V tomto kurzu migrujete datovou sadu v MongoDB, která je hostovaná na virtuálním počítači Azure. Pomocí Azure Database Migration Service migrujete datovou sadu do rozhraní Azure Cosmos DB API pro MongoDB. Pokud už nemáte nastavený zdroj MongoDB, přečtěte si téma [instalace a konfigurace MongoDB na virtuálním počítači s Windows v Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* [Dokončete kroky před migrací](../cosmos-db/mongodb-pre-migration.md) , například odhad propustnosti a výběr klíče oddílu.
* [Vytvořte účet pro rozhraní Azure Cosmos DB API pro MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí Azure Resource Manager. Tento model nasazení zajišťuje připojení typu Site-to-site k vašim místním zdrojovým serverům pomocí [Azure ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Azure Virtual Network](../virtual-network/index.yml), zejména v článcích "rychlý Start" s podrobnými údaji.

    > [!NOTE]
    > Pokud při instalaci virtuální sítě používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, tyto [koncové body](../virtual-network/virtual-network-service-endpoints-overview.md) služby:
    >
    > * Koncový bod cílové databáze (například koncový bod SQL nebo koncový bod Azure Cosmos DB)
    > * Koncový bod úložiště
    > * Koncový bod služby Service Bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.

* Zajistěte, aby pravidla skupiny zabezpečení sítě (NSG) pro vaši virtuální síť neblokovala následující komunikační porty: 53, 443, 445, 9354 a 10000-20000. Další informace viz [Filtrování provozu sítě s použitím skupin zabezpečení sít](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Otevřete bránu Windows Firewall, abyste povolili Azure Database Migration Service přístup ke zdrojovému serveru MongoDB, který je ve výchozím nastavení port TCP 27017.
* Pokud používáte zařízení brány firewall před zdrojovou databází, může být nutné přidat pravidla brány firewall, aby Azure Database Migration Service mohl získat přístup ke zdrojové databázi pro migraci.

## <a name="configure-the-server-side-retry-feature"></a>Konfigurovat funkci opakování na straně serveru

Pokud migrujete z MongoDB na Azure Cosmos DB, můžete využívat možnosti zásad správného řízení prostředků. S těmito možnostmi můžete plně využívat vaše zřízené jednotky žádostí (RU/s). Azure Cosmos DB může v průběhu migrace omezit konkrétní Database Migration Service žádosti, pokud tento požadavek překročí požadavky na kontejner zřízený na kontejneru/s. Pak se musí tento požadavek opakovat.

Database Migration Service je schopen provádět opakované pokusy. Je důležité pochopit, že doba odezvy v síti mezi Database Migration Service a Azure Cosmos DB ovlivňuje celkovou dobu odezvy tohoto požadavku. Zvýšení doby odezvy pro omezené požadavky může zkrátit celkovou dobu potřebnou k migraci.

Funkce opakování na straně serveru Azure Cosmos DB umožňuje službě zachytit kódy chyb omezení a opakovat s mnohem nižší dobou odezvy, což výrazně vylepšuje dobu odezvy na žádosti.

Chcete-li použít operaci na straně serveru, vyberte na portálu Azure Cosmos DB možnost **funkce**  >  **Opakovat na straně serveru**.

![Snímek obrazovky, který ukazuje, kde najít funkci opakování na straně serveru.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Pokud je funkce zakázaná, vyberte **Povolit**.

![Snímek obrazovky, který ukazuje, jak povolit opakování na straně serveru](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Snímek obrazovky zobrazující odběry portálu](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Snímek obrazovky, který ukazuje poskytovatele prostředků.](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Snímek obrazovky, který ukazuje, jak zaregistrovat poskytovatele prostředků](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Snímek obrazovky znázorňuje Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Snímek obrazovky, který ukazuje, jak vytvořit instanci Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. V části **vytvořit migrační službu** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci Azure Database Migration Service. 

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojové instanci MongoDB a cílovému Azure Cosmos DBmu účtu.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v tématu [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Snímek obrazovky, který zobrazuje nastavení konfigurace pro instanci Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte v rámci Azure Portal a otevřete ji. Pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Snímek obrazovky, který ukazuje, jak najít všechny instance Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na obrazovce **služby Azure Database Migration Services** vyhledejte název instance Azure Database Migration Service, kterou jste vytvořili, a pak vyberte instanci.

3. Vyberte **+ Nový projekt migrace**.

4. V části **Nový projekt migrace** zadejte název projektu a v textovém poli **typ zdrojového serveru** vyberte možnost **MongoDB**. V textovém poli **typ cílového serveru** vyberte **COSMOSDB (MongoDB API)** a pak u možnosti **zvolit typ aktivity** vyberte **offline migrace dat**. 

    ![Snímek obrazovky, který zobrazuje možnosti projektu.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti o zdroji** zadejte podrobnosti o připojení pro zdrojový server MongoDB.

   > [!IMPORTANT]
   > Azure Database Migration Service nepodporuje Azure Cosmos DB jako zdroj.

    Existují tři režimy připojení ke zdroji:
   * **Standardní režim**, který přijímá plně kvalifikovaný název domény nebo IP adresu, číslo portu a přihlašovací údaje pro připojení.
   * **Režim připojovacího řetězce**, který přijímá připojovací řetězec MongoDB, jak je popsáno ve [formátu identifikátoru URI připojovacího řetězce](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Data ze služby Azure Storage**, která přijímá adresu URL SAS kontejneru objektů BLOB. Pokud má kontejner objektů BLOB BSON výpisy paměti vytvořené [nástrojem MongoDB bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/), vyberte **objekt BLOB obsahuje výpisy bson** . Tuto možnost nevybírejte, pokud kontejner obsahuje soubory JSON.

     Pokud vyberete tuto možnost, ujistěte se, že se připojovací řetězec účtu úložiště zobrazuje v následujícím formátu:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Tento připojovací řetězec SAS kontejneru objektů BLOB najdete v Průzkumníkovi Azure Storage. Vytvořením SAS pro příslušný kontejner získáte adresu URL v požadovaném formátu.
     
     Na základě informací o výpisu typu v Azure Storage mějte na paměti následující informace:

     * V případě výpisů BSON musí být data v kontejneru objektů BLOB ve formátu bsondump. Umístěte datové soubory do složek pojmenovaných po obsahujících databázích ve formátu *Collection. bson*. Pojmenujte všechny soubory metadat pomocí *collection.metadata.js* formátu.

     * V případě výpisů JSON musí být soubory v kontejneru objektů BLOB umístěny do složek pojmenovaných po nadřazených databázích. V rámci každé složky databáze musí být datové soubory umístěny do podsložky s názvem *data* a pojmenovány pomocí formátu *collection.js*. Uložte všechny soubory metadat do podsložky s názvem *metadata* a pojmenujte je pomocí stejného formátu *collection.js*. Soubory metadat musí být ve stejném formátu, který vytvořil nástroj MongoDB bsondump Tool.

    > [!IMPORTANT]
    > Na serveru MongoDB nedoporučujeme používat certifikát podepsaný svým držitelem. Pokud musíte použít jeden, připojte se k serveru pomocí režimu připojovacího řetězce a ujistěte se, že připojovací řetězec obsahuje uvozovky ("").
    >
    >```
    >&sslVerifyCertificate=false
    >```

   IP adresu můžete použít taky v situacích, kdy není možné překlad názvů DNS.

   ![Snímek obrazovky, který ukazuje zadání podrobností o zdroji.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **Podrobnosti cíle migrace** zadejte podrobnosti o připojení pro cílový Azure Cosmos DB účet. Tento účet představuje předem zřízené Azure Cosmos DB rozhraní API pro účet MongoDB, na který migrujete data MongoDB.

    ![Snímek obrazovky, který ukazuje zadání cílových podrobností.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Vyberte **Uložit**.

## <a name="map-to-target-databases"></a>Mapování na cílové databáze

1. Na obrazovce **mapování na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    Pokud **se** zobrazí vedle názvu databáze, znamená to, že Azure Database Migration Service nebyla nalezena cílová databáze a služba vytvoří databázi za vás.

    V tomto okamžiku migrace můžete [zřídit propustnost](../cosmos-db/set-throughput.md). V Azure Cosmos DB můžete zřídit propustnost buď na úrovni databáze, nebo jednotlivě pro každou kolekci. Propustnost se měří v [jednotkách žádostí](../cosmos-db/request-units.md). Přečtěte si další informace o [cenách Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Snímek obrazovky, který ukazuje mapování na cílové databáze.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Vyberte **Uložit**.
3. Na obrazovce **nastavení kolekce** rozbalte seznam kolekce a potom zkontrolujte seznam kolekcí, které budou migrovány.

    Azure Database Migration Service automaticky vybere všechny kolekce, které existují ve zdrojové instanci MongoDB, které v cílovém Azure Cosmos DB účtu neexistují. Pokud chcete znovu migrovat kolekce, které již obsahují data, je nutné explicitně vybrat kolekce v tomto podokně.

    Můžete zadat počet ru, které mají kolekce používat. Azure Database Migration Service navrhuje inteligentní výchozí hodnoty na základě velikosti kolekce.

    > [!NOTE]
    > Migraci a shromažďování databáze proveďte paralelně. V případě potřeby můžete použít více instancí Azure Database Migration Service ke zrychlení běhu.

    Můžete také zadat horizontálních oddílů klíč, který bude využívat [dělení v Azure Cosmos DB](../cosmos-db/partitioning-overview.md) pro zajištění optimální škálovatelnosti. Projděte si [osvědčené postupy pro výběr klíče horizontálních oddílů/partition](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Snímek obrazovky, který zobrazuje výběr tabulek kolekcí.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Vyberte **Uložit**.

5. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

    ![Snímek obrazovky zobrazující souhrn nigration](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

Vyberte **Spustit migraci**. Zobrazí se okno aktivita migrace a stav aktivity není **spuštěno**.

![Snímek obrazovky zobrazující stav aktivity](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorování migrace

Na obrazovce aktivita migrace vyberte **aktualizovat** , aby se zobrazení aktualizovalo, dokud se stav migrace nezobrazuje jako **dokončený**.

> [!NOTE]
> Můžete vybrat aktivitu pro získání podrobností o metrikách migrace na úrovni databáze a kolekce.

![ScrenShot zobrazující stav aktivity dokončeno.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Ověřit data v Azure Cosmos DB

Po dokončení migrace můžete zkontrolovat účet Azure Cosmos DB, abyste ověřili, že všechny kolekce byly úspěšně migrovány.

![Snímek obrazovky, který ukazuje, kde se má zkontrolovat váš Azure Cosmos DB účet, aby se ověřilo, že se úspěšně migrují všechny kolekce.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optimalizace po migraci

Po migraci dat uložených v databázi MongoDB do rozhraní API služby Azure Cosmos DB pro MongoDB se můžete připojit k Azure Cosmos DB a spravovat data. Můžete také provést další kroky optimalizace po migraci. To může zahrnovat optimalizaci zásad indexování, aktualizaci výchozí úrovně konzistence nebo konfiguraci globální distribuce pro účet Azure Cosmos DB. Další informace najdete v tématu [optimalizace po migraci](../cosmos-db/mongodb-post-migration.md).

## <a name="next-steps"></a>Další kroky

Projděte si pokyny k migraci dalších scénářů v [Průvodci migrací databáze Azure](https://datamigration.microsoft.com/).



