---
title: Používat službu Azure databáze migrace pro migraci systému SQL Server do Azure SQL Database | Microsoft Docs
description: Zjistěte, k migraci z místního serveru SQL do Azure SQL pomocí služby Azure databáze migrace.
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/29/2018
ms.openlocfilehash: 48f1b3715f300fea7bfc0590e6d2e6c6622e83aa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>Migrace systému SQL Server do databáze Azure SQL
Službu Azure databáze migrace můžete použít k migraci databáze z místní instance systému SQL Server do Azure SQL Database. V tomto kurzu, migrovat **Adventureworks2012** databáze obnovena do místní instance systému SQL Server 2016 (nebo vyšší) do Azure SQL Database pomocí služby Azure databáze migrace.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vyhodnocení místní databázi pomocí Pomocníka pro migraci dat.
> * Ukázka schéma migrujte pomocí Pomocníka pro migraci dat.
> * Vytvoření instance služby migrace databáze Azure.
> * Vytvoření projektu migrace pomocí služby Azure databáze migrace.
> * Spusťte migrace.
> * Monitorujte migraci.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu potřebujete:

- Stáhněte a nainstalujte [SQL Server 2016 nebo novější](https://www.microsoft.com/sql-server/sql-server-downloads) (všechny edice).
- Povolte protokol TCP/IP, který je zakázaný ve výchozím nastavení během instalace systému SQL Server Express, pomocí pokynů v článku [povolit nebo zakázat síťový protokol serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Vytvoření instance Azure SQL Database instance, kterou provedete podle následujících podrobností v článku [vytvoření Azure SQL database na portálu Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Stáhněte a nainstalujte [Data migrace pomocníka](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 nebo novější.
- Vytvoření virtuální sítě pro službu Azure databáze migrace pomocí modelu nasazení Azure Resource Manager, které poskytuje připojení site-to-site k vaší místní zdrojové servery pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Ujistěte se, že vaše Azure virtuální síť (VNET) skupinu zabezpečení sítě pravidla proveďte bloku následující komunikace porty 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu NSG virtuální síť Azure, najdete v článku [filtrování provozu sítě přenosů se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurace vaší [brány Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete vaší brány Windows firewall a povolit službu Azure databáze migrace k přístupu ke zdroji systému SQL Server, který ve výchozím nastavení je TCP port 1433.
- Pokud používáte více pojmenované instance systému SQL Server pomocí dynamické porty, budete pravděpodobně chtít povolit službu SQL Browser a povolení přístupu k portu UDP 1434 přes vaší brány firewall tak, aby služba migrace databáze Azure může připojit k pojmenovaná instance na svůj zdroj Server.
- Pokud používáte zařízení brány firewall před vaší zdrojové databáze, musíte přidat pravidla firewallu povolující službu Azure databáze migrace pro přístup k databází zdroje pro migraci.
- Vytvořte úrovni serveru [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) pro server Azure SQL Database, aby mohly služba migrace databáze Azure přístup k cílovým databázím. Zadejte rozsah podsíť virtuální sítě používaný pro službu Azure databáze migrace.
- Zkontrolujte, zda pověření používaná k připojení k instanci systému SQL Server zdrojové [ovládacího PRVKU serveru](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) oprávnění.
- Ujistěte se, že pověření použitá pro připojení k cílové instance Azure SQL Database mít oprávnění CONTROL DATABASE na cílovým databázím Azure SQL.

## <a name="assess-your-on-premises-database"></a>Vyhodnocení místní databázi
Než bude možné migrovat data z místní instance systému SQL Server do Azure SQL Database, budete muset vyhodnocení databázi systému SQL Server pro libovolným omezujícím problémům, které by mohly bránit migrace. Pomocník pro migraci dat v3.3 nebo novější, postupujte podle kroků popsaných v článku [provádění na posouzení migrace systému SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) k dokončení místní databáze hodnocení. Následuje souhrn požadované kroky:
1.  V Pomocníka pro migraci dat, vyberte ikonu nový (+) a pak vyberte **Assessment** typ projektu.
2.  Zadejte název projektu, ve **serveru typ zdroje** textového pole, vyberte **systému SQL Server**a potom v **cíle typ serveru** textového pole, vyberte **Azure SQL Databáze**.
3.  Vyberte **vytvořit** a vytvořte tak projekt.

    Když jsou hodnocením zdrojové databáze systému SQL Server migrace do Azure SQL Database, můžete jedno nebo obě následující typy sestav vyhodnocení:
    - Kontrola kompatibility databáze
    - Zkontrolujte parity funkcí

    Ve výchozím nastavení jsou vybrány oba typy sestav.
4.  V Pomocníka pro migraci dat na **možnosti** obrazovku, vyberte **Další**.
5.  Na **vyberte zdroje** obrazovce **připojit k serveru** dialogové okno, zadejte podrobnosti připojení k systému SQL Server a pak vyberte **Connect**.
6.  V **přidejte zdroje** dialogové okno, vyberte **AdventureWorks2012**, vyberte **přidat**a potom vyberte **spustit Assessment**.

    Po dokončení hodnocení ve výsledcích se zobrazí, jak je znázorněno na následujícím obrázku:

    ![Posouzení migrace dat](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Pro databázi SQL Azure hodnocení identifikovat blokující problémy s migrací a problémy parita funkce.

7.  Zkontrolujte výsledky hodnocení pro blokující problémy s migrací a problémy parita funkce výběrem konkrétních možností.
    - **Parity funkcí systému SQL Server** kategorie poskytuje komplexní sadu doporučení, Alternativní přístupy k dispozici ve službě Azure a zmírnění kroky, které vám pomohou naplánovat úsilí do vašich projektů migrace.
    - **Problémy s kompatibilitou** kategorie identifikuje částečně podporované nebo nepodporované funkce, které odráží kompatibility potíže, které by mohly blokovat migrace místní databáze SQL serveru do Azure SQL Database. Doporučení jsou k dispozici také můžete vyřešit tyto problémy.


## <a name="migrate-the-sample-schema"></a>Migrace schéma ukázka
Po celý hodnocení a uspokojit, že vybraná databáze je vhodným kandidátem pro migraci do Azure SQL Database pomocí Pomocníka pro migraci dat k migraci schématu do Azure SQL Database.

> [!NOTE]
> Před vytvořením projektu migrace v Pomocníkovi pro migraci dat, ujistěte se, jak je uvedeno v požadavky mají už zřízené Azure SQL database. Pro účely tohoto kurzu, je název databáze SQL Azure považuje za **AdventureWorksAzure**, ale nechcete-li ji můžete pojmenovat jinak.

K migraci **AdventureWorks2012** schématu do Azure SQL Database, proveďte následující kroky:

1.  V Pomocníka pro migraci dat, vyberte ikonu nový (+) a potom v části **typ projektu**, vyberte **migrace**.
3.  Zadejte název projektu, ve **serveru typ zdroje** textového pole, vyberte **systému SQL Server**a potom v **cíle typ serveru** textového pole, vyberte **Azure SQL Databáze**.
4.  V části **migrace oboru**, vyberte **Schema only**.

    Po provedení předchozích kroků, by měla vypadat rozhraní Pomocník pro migraci dat, jak je znázorněno na následujícím obrázku:
    
    ![Vytvoření projektu pomocníka migrace dat](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  Vyberte **vytvořit** a vytvořte tak projekt.
6.  V Pomocníka pro migraci dat zadat podrobnosti připojení zdroje pro systém SQL Server, vyberte **připojit**a pak vyberte **AdventureWorks2012** databáze.

    ![Podrobnosti připojení pomocníka zdroje dat migrace](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  Vyberte **Další**v části **připojit k cílovému serveru**, zadat podrobnosti připojení cíl pro databázi Azure SQL, vyberte **Connect**a pak vyberte **AdventureWorksAzure** databáze měl předem zřizovat v databázi Azure SQL.

    ![Podrobnosti připojení pomocníka cíl migrace dat](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  Vyberte **Další** pro přechod **vybrat objekty** obrazovky, ve kterém můžete zadat objekty schématu v **AdventureWorks2012** databázi, která je potřeba nasadit do Azure Databáze SQL.

    Ve výchozím nastavení jsou vybrány všechny objekty.

    ![Generování skriptů SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  Vyberte **skript SQL generovat** vytvářet skripty SQL a poté zkontrolovat skripty pro všechny chyby.

    ![Skript schématu](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. Vyberte **nasazení schématu** pro nasazení schématu do Azure SQL Database a potom po nasazení schématu zkontrolujte cílový server pro anomálie.

    ![Nasazení schématu](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration
1. Přihlaste se k portálu Azure, vyberte **všechny služby**a potom vyberte **odběry**.
 
   ![Zobrazit portálu odběrů](media\tutorial-sql-server-to-azure-sql\portal-select-subscription.png)
       
2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.
 
    ![Zobrazit zprostředkovatelé prostředků](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)    
3.  Vyhledávání pro migraci a potom napravo od **Microsoft.DataMigration**, vyberte **zaregistrovat**.
 
    ![Registrace poskytovatele prostředků](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    


## <a name="create-an-instance"></a>Vytvoření instance
1.  Na portálu Azure vyberte **+ vytvořit prostředek**, vyhledejte službu migrace databáze Azure a pak vyberte **služba migrace databáze Azure** z rozevíracího seznamu.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  Na **služba migrace databáze Azure (preview)** obrazovku, vyberte **vytvořit**.
 
    ![Vytvoření instance služby migrace databáze Azure](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  Na **služba migrace databáze** obrazovky, zadejte název pro tuto službu, předplatné, virtuální síť a cenovou úroveň.

    Další informace o náklady a cenových úrovní najdete v tématu [stránce s cenami](https://aka.ms/dms-pricing).

     ![Konfigurace nastavení instance služby migrace databáze Azure](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  Vyberte **vytvořit** vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace
Po vytvoření služby najít na portálu Azure a pak vytvořte projekt migrace.
1. Na portálu Azure vyberte **všechny služby**, vyhledejte službu migrace databáze Azure a pak vyberte **služby migrace databáze Azure**.
 
      ![Vyhledejte všechny instance služby Azure databáze migrace](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. Na **služby migrace databáze Azure** si vyhledejte název Azure DMS instanci, kterou jste vytvořili a pak vyberte instanci.
 
     ![Najít instanci služby Azure databáze migrace](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Vyberte **+ nový projekt migrace**.
4. Na **nový projekt migrace** obrazovky, zadejte název projektu, ve **serveru typ zdroje** textového pole, vyberte **systému SQL Server**a potom v **cíl Typ serveru** textového pole, vyberte **Azure SQL Database**.

    ![Vytvoření projektu služby migrace databáze](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  Vyberte **vytvořit** a vytvořte tak projekt.


## <a name="specify-source-details"></a>Zadejte podrobnosti zdroje
1. Na **zdroje podrobnosti** obrazovky, zadat podrobnosti připojení ke zdroji systému SQL Server.

    ![Vyberte zdroj](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. Vyberte **Uložit**a pak vyberte **AdventureWorks2012** databáze pro migraci.

    ![Vyberte zdroj DB](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>Zadejte podrobnosti cíl
1. Vyberte **Uložit**a pak na **cíle podrobnosti** obrazovky, zadat podrobnosti připojení pro cíl, který je předem zřízená Azure SQL Database na kterou **AdventureWorks2012**  schématu byla nasazená pomocí Pomocníka pro migraci dat.

    ![Vyberte cíl](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. Vyberte **Uložit** k uložení projektu.
3. Na **souhrn migrace** obrazovce zkontrolujte a zkontrolujte podrobnosti o přidružený k projektu migrace.

    ![Souhrn DMS](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. Vyberte **Uložit**.

## <a name="run-the-migration"></a>Spustit migrace
1.  Vyberte nedávno uložený projekt, vyberte **+ nová aktivita**a potom vyberte **spustit migraci dat**.

    ![Nová aktivita](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  Po zobrazení výzvy zadejte přihlašovací údaje pro zdrojové a cílové servery a potom vyberte **Uložit**.
3.  Na **mapu, která bude cílovým databázím** obrazovky, mapy zdroji a cílová databáze pro migraci.

    Pokud je cílová databáze obsahuje název stejné databáze jako zdrojové databáze, vybere Azure DMS cílová databáze ve výchozím nastavení.

    ![Mapovat k cílovým databázím](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. Vyberte **Uložit**na **vyberte tabulky** obrazovky, rozbalte seznam tabulky a projděte si seznam ovlivněných pole.

    ![Výběr tabulek](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  Vyberte **Uložit**na **migrace Souhrn** obrazovce **název aktivity** textové pole, zadejte název aktivity migrace.

    Na této obrazovce můžete také rozšířit **zvolte možnost ověření** obrazovky, který můžete použít k určení migrovaných databázi pro ověření:
    - Schéma
    - Konzistence dat
    - Správnost dotazu a výkonu

    ![Zvolte možnost ověření](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  Vyberte **Uložit**, zkontrolujte souhrn zajistit, aby odpovídaly zdrojové a cílové podrobnosti, co jste dřív zadali.

    ![Souhrn migrace](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  Vyberte **spuštění migrace** spuštění aktivity migrace, a potom vyberte **aktualizovat** můžete zkontrolovat aktuální stav.

    ![Stav aktivity](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorování migrace
1. Vyberte aktivitu migrace, můžete zkontrolovat stav aktivity.
2. Po dokončení migrace, ověřte cílová databáze Azure SQL.

    ![Dokončené](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
