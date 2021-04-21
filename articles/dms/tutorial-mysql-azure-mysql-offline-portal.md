---
title: 'Kurz: migrace MySQL pro Azure Database for MySQL offline pomocí DMS'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět offline migraci z místního MySQL do Azure Database for MySQL pomocí Azure Database Migration Service.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 71363771359ffef0ff165bd4a6744d864ea1856c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819610"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>Kurz: migrace MySQL pro Azure Database for MySQL offline pomocí DMS

Azure Database Migration Service můžete použít k provedení jednorázové úplné migrace databáze z místní instance MySQL pro [Azure Database for MySQL](../mysql/index.yml) s možností migrace dat s vysokou rychlostí. V tomto kurzu provedeme migraci ukázkové databáze z místní instance MySQL 5,7 na Azure Database for MySQL (v 5.7) pomocí offline aktivity migrace v Azure Database Migration Service. I když články předpokládají, že se jedná o instanci databáze MySQL a že cíl je Azure Database for MySQL, dá se použít k migraci z jednoho Azure Database for MySQL na jiný, a to tak, že změníte název zdrojového serveru a přihlašovací údaje. Také je podporována migrace z nižší verze serverů MySQL (v 5.6 a vyšších) na vyšší verze.

> [!IMPORTANT]
> Pro online migrace můžete tuto novou nabídku použít spolu s [replikací dat](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication). Alternativně můžete použít Open Source nástroje, jako je [MyDumper/MyLoader](https://centminmod.com/mydumper.html) , při replikaci dat pro online migrace. 

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]


V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Migrujte schéma databáze pomocí nástroje mysqldump.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí Azure Database Migration Service.
> * Spuštění migrace
> * Monitorujte migraci.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Mít účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free).
* Máte místní databázi MySQL s verzí 5,7. Pokud ne, Stáhněte a nainstalujte [MySQL community edition](https://dev.mysql.com/downloads/mysql/) 5,7.
* [Vytvořte instanci ve službě Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Další informace o tom, jak připojit a vytvořit databázi pomocí aplikace Workbench, najdete v článku [použití MySQL Workbench k připojení a dotazování dat](../mysql/connect-workbench.md) . Verze Azure Database for MySQL by měla být větší nebo rovna verzi místního MySQL. MySQL 5,7 může například migrovat na Azure Database for MySQL 5,7 nebo na 8. 
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který zajišťuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](../virtual-network/index.yml)a zejména v článcích rychlý Start s podrobnými údaji.

    > [!NOTE]
    > Pokud při instalaci služby Virtual networkNet používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, následující [koncové body](../virtual-network/virtual-network-service-endpoints-overview.md) služby:
    >
    > * Koncový bod cílové databáze (například koncový bod SQL, Cosmos DB koncový bod atd.)
    > * Koncový bod úložiště
    > * Koncový bod služby Service Bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.

* Zajistěte, aby pravidla skupiny zabezpečení sítě virtuálních sítí neblokovala odchozí port 443 ServiceTag pro ServiceBus, Storage a AzureMonitor. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Otevřete bránu Windows Firewall, aby povolovala připojení z Virtual Network Azure Database Migration Service přístup ke zdrojovému serveru MySQL, který je ve výchozím nastavení port TCP 3306.
* Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla firewallu, která umožní připojení z Virtual Network Azure Database Migration Service pro přístup ke zdrojovým databázím pro migraci.
* Vytvoření [pravidla brány firewall](../azure-sql/database/firewall-configure.md) na úrovni serveru nebo [Konfigurace koncových bodů služby virtuální](../mysql/howto-manage-vnet-using-portal.md) sítě pro cílový Azure Database for MySQL pro povolení Virtual Network pro Azure Database Migration Service přístup k cílovým databázím.
* Zdrojová instance MySQL musí být na podporované edici MySQL Community Edition. Pokud chcete určit verzi instance MySQL, v nástroji MySQL nebo aplikaci MySQL Workbench spusťte následující příkaz:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL podporuje pouze tabulky InnoDB. Informace o převodu tabulek MyISAM na tabulky InnoDB najdete v článku [Převod tabulek z MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html).
* Uživatel musí mít oprávnění ke čtení dat ve zdrojové databázi.

## <a name="migrate-database-schema"></a>Migrace schématu databáze

Abychom přenesli všechny databázové objekty, jako jsou schémata tabulek, indexy a uložené procedury, musíme ze zdrojové databáze extrahovat schéma a použít ho v cílové databázi. K extrahování schématu můžete použít nástroj mysqldump s parametrem `--no-data`. K tomu potřebujete počítač, který se může připojit ke zdrojové databázi MySQL i k cílovému Azure Database for MySQL.

Pokud chcete schéma exportovat pomocí mysqldump, spusťte následující příkaz:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Například:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Chcete-li importovat schéma do cílového Azure Database for MySQL, spusťte následující příkaz:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Například:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Pokud máte ve schématu cizí klíče, bude úloha migrace zpracovávat paralelní datové zatížení během migrace. Při migraci schématu není nutné vyřadit cizí klíče.

Pokud máte v databázi triggery, vynutila integritu dat v cíli před úplnou migrací dat ze zdroje. Doporučením je zakázat aktivační události ve všech tabulkách v cíli během migrace a poté povolit triggery po dokončení migrace.

Spusťte následující skript v aplikaci MySQL Workbench v cílové databázi pro extrakci skriptu pro odkládací událost a přidání skriptu triggeru.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

V cílové databázi spusťte dotaz vygenerované aktivační události vyřazení (sloupec DropQuery) ve výsledku pro vyřazení aktivačních událostí. Dotaz přidat aktivační událost lze uložit, aby bylo možné použít dokončení migrace dat.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

Registrace poskytovatele prostředků se musí provádět v každém předplatném Azure jenom jednou. Bez registrace nebudete moci vytvořit instanci **Azure Database Migration Service**.

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-mysql-to-azure-mysql-offline-portal/01-dms-portal-select-subscription.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-mysql-to-azure-mysql-offline-portal/02-dms-portal-register-rp.png)

## <a name="create-a-database-migration-service-instance"></a>Vytvoření instance Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte cenovou úroveň a přejděte na obrazovku sítě. Možnost offline migrace je dostupná jak v cenové úrovni úrovně Standard, tak i Premium.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení Azure Database Migration Service Basic](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. Vyberte ze seznamu existující virtuální síť nebo zadejte název nové virtuální sítě, která se má vytvořit. Přejděte na obrazovku revize + vytvořit. Volitelně můžete přidat značky ke službě pomocí obrazovky značky.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojovému SQL Server a cílové instanci Azure SQL Database.

    ![Konfigurace nastavení Azure Database Migration Service sítě](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

6. Zkontrolujte konfigurace a vyberte **vytvořit** a vytvořte službu.
    
    ![Vytvoření Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.  

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledat všechny instance Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Z výsledků hledání vyberte instanci migrační služby a vyberte + **Nový projekt migrace**.
    
    ![Vytvoření nového projektu migrace](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. Na obrazovce **Nový projekt migrace** zadejte název projektu, v poli Výběr **typu zdrojového serveru** vyberte **MySQL**, v poli Výběr **typu cílového serveru** vyberte položku **Azure Database for MySQL** a v poli Výběr **typu aktivity migrace** vyberte možnost **migrace dat \[ verze Preview \]**. Vyberte **Vytvořit a spustit aktivitu**.

    ![Vytvoření projektu Database Migration Service](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Alternativně můžete zvolit **vytvořit projekt pouze** k vytvoření projektu migrace a provést migraci později.

## <a name="configure-migration-project"></a>Konfigurace projektu migrace

1. Na obrazovce **Vybrat zdroj** zadejte podrobnosti připojení zdrojové instance MySQL a vyberte **Další: vybrat cílovou>>**

    ![Přidat obrazovku podrobností zdroje](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. Na obrazovce **vybrat cíl** zadejte podrobnosti o připojení pro cílovou instanci Azure Database for MySQL a vyberte **Další: Vyberte databáze>>**

    ![Přidat obrazovku s podrobnostmi o cíli](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. Na obrazovce **Vybrat databáze** namapujte zdrojovou a cílovou databázi pro migraci a vyberte **Další: konfigurace nastavení migrace>>**. Pokud chcete zdroj nastavit jako jen pro čtení, můžete vybrat možnost **vytvořit zdrojový server** jen pro čtení, ale buďte opatrní, protože se jedná o nastavení na úrovni serveru. Pokud je tato možnost vybrána, nastaví celý server jen pro čtení, nikoli jenom pro vybrané databáze.
    
    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.
    ![Obrazovka s podrobnostmi o databázi](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > I když v tomto kroku můžete vybrat více databází, existují ale omezení počtu a četnosti migrace databáze tímto způsobem, protože každá databáze bude sdílet výpočetní výkon. S výchozí konfigurací SKU úrovně Premium se Každá úloha migrace pokusí migrovat dvě tabulky paralelně. Tyto tabulky mohou být ze všech vybraných databází. Pokud to není dost rychle, můžete aktivity migrace databáze rozdělit do různých úloh migrace a škálovat napříč několika službami. Také platí omezení 10 instancí Azure Database Migration Service pro každé předplatné na oblast.
    > Podrobnější kontrolu propustnosti a paralelismu migrace najdete v článku [PowerShellu: spuštění offline migrace z databáze MySQL pro Azure Database for MySQL pomocí DMS.](./migrate-mysql-to-azure-mysql-powershell.md)

4. Na obrazovce **Konfigurovat nastavení migrace** vyberte tabulky, které mají být součástí migrace, a vyberte **další: Souhrn>>**. Pokud mají cílové tabulky nějaká data, ve výchozím nastavení nejsou vybrané, ale můžete je explicitně vybrat a před zahájením migrace se zkrátí.

    ![Obrazovka vybrat tabulky](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. Na obrazovce **Souhrn** zadejte do textového pole **název aktivity** název aktivity migrace a zkontrolujte souhrn, abyste se ujistili, že se podrobnosti o zdroji a cíli shodují s tím, co jste předtím zadali.

    ![Shrnutí projektu migrace](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. Vyberte **Spustit migraci**. Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Inicializace**. Když se spustí migrace tabulky, **stav** se změní na **spuštěno** .
 
     ![Spuštění migrace](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivita migrace vyberte **aktualizovat** , aby se zobrazily informace o průběhu počtu dokončených tabulek. 

2. Můžete kliknout na název databáze na obrazovce aktivita a zobrazit stav každé tabulky při jejich migraci. Vyberte **aktualizovat** , aby se zobrazila aktualizace. 

     ![Monitorování migrace](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>Dokončete migraci

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

    ![Dokončení migrace](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>Aktivity po migraci

Migrace přímou migraci při offline migraci je proces závislý na aplikaci, který je pro tento dokument mimo rozsah, ale jsou předepsané následující aktivity po migraci:

1. Vytvořte přihlašovací údaje, role a oprávnění podle požadavků aplikace.
2. Znovu vytvořte všechny aktivační události v cílové databázi jako extrahované během kroku před migrací.
3. Proveďte správnosti testování aplikace proti cílové databázi pro certifikaci migrace. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete Database Migration Service nadále používat, můžete službu odstranit pomocí následujících kroků:

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledat všechny instance DMS](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. Z výsledků hledání vyberte instanci migrační služby a vyberte **Odstranit službu**.
    
    ![Odstranění migrační služby](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. V potvrzovacím dialogovém okně zadejte název služby do textového pole **Zadejte název služby pro migraci databáze** a vyberte **Odstranit** .

    ![Potvrdit odstranění migrační služby](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>Další kroky

* Informace o známých problémech a omezeních při provádění migrace pomocí DMS najdete v článku [běžné problémy – Azure Database Migration Service](./known-issues-troubleshooting-dms.md).
* Problémy s připojením zdrojové databáze při používání DMS najdete v článku problémy při [připojování zdrojových databází](./known-issues-troubleshooting-dms-source-connectivity.md).
* Informace o Azure Database Migration Service najdete v článku [co je Azure Database Migration Service?](./dms-overview.md).
* Informace o Azure Database for MySQL najdete v článku [co je Azure Database for MySQL?](../mysql/overview.md).
* Pokyny k používání DMS přes PowerShell najdete v článku [PowerShellu: spuštění offline migrace z databáze MySQL pro Azure Database for MySQL pomocí DMS.](./migrate-mysql-to-azure-mysql-powershell.md)