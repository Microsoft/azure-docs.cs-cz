---
title: Používat službu Azure databáze migrace pro migraci systému SQL Server do Azure SQL Database | Microsoft Docs
description: Zjistěte, k migraci z místního serveru SQL do Azure SQL Database pomocí služby Azure databáze migrace.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/08/2018
ms.openlocfilehash: f64b2922818eddcab02f7d1c7b8f97671d92589e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850249"
---
# <a name="migrate-sql-server-to-azure-sql-database-using-dms"></a>Migrace systému SQL Server do Azure SQL Database pomocí DMS
Můžete používat službu Azure databáze migrace k migraci databáze z místní instance systému SQL Server na [Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/). V tomto kurzu, migrovat **Adventureworks2012** databáze obnovena do místní instance systému SQL Server 2016 (nebo novější) do Azure SQL Database pomocí služby Azure databáze migrace.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vyhodnocení místní databázi pomocí Pomocníka pro migraci dat.
> * Ukázka schéma migrujte pomocí Pomocníka pro migraci dat.
> * Vytvoření instance služby migrace databáze Azure.
> * Vytvoření projektu migrace pomocí služby Azure databáze migrace.
> * Spusťte migrace.
> * Monitorujte migraci.
> * Stáhněte si sestavu migraci.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu potřebujete:

- Stáhněte a nainstalujte [systému SQL Server 2016 nebo novější](https://www.microsoft.com/sql-server/sql-server-downloads) (všechny edice).
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
Než bude možné migrovat data z místní instance systému SQL Server do Azure SQL Database, budete muset vyhodnocení databázi systému SQL Server pro libovolným omezujícím problémům, které by mohly bránit migrace. Pomocí Pomocníka pro migraci dat v3.3 nebo novější, postupujte podle kroků popsaných v článku [provádění na posouzení migrace systému SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) k dokončení místní databáze hodnocení. Následuje souhrn požadované kroky:
1.  V Pomocníka pro migraci dat, vyberte ikonu nový (+) a pak vyberte **Assessment** typ projektu.
2.  Zadejte název projektu, ve **serveru typ zdroje** textového pole, vyberte **systému SQL Server**v **cíle typ serveru** textového pole, vyberte **Azure SQL Database**a potom vyberte **vytvořit** a vytvořte tak projekt.

    Když jsou hodnocením zdrojové databáze systému SQL Server migrace do Azure SQL Database, můžete jedno nebo obě následující typy sestav vyhodnocení:
    - Zkontrolovat kompatibilitu databáze
    - Zkontrolovat paritu funkcí

    Ve výchozím nastavení jsou vybrány oba typy sestav.

3.  V Pomocníka pro migraci dat na **možnosti** obrazovku, vyberte **Další**.
4.  Na **vyberte zdroje** obrazovce **připojit k serveru** dialogové okno, zadejte podrobnosti připojení k systému SQL Server a pak vyberte **Connect**.
5.  V **přidejte zdroje** dialogové okno, vyberte **AdventureWorks2012**, vyberte **přidat**a potom vyberte **spustit Assessment**.

    Po dokončení hodnocení ve výsledcích se zobrazí, jak je znázorněno na následujícím obrázku:

    ![Posouzení migrace dat](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Pro databázi SQL Azure hodnocení identifikovat problémy parita funkce a blokující problémy s migrací.

    - **Parity funkcí systému SQL Server** kategorie poskytuje komplexní sadu doporučení, Alternativní přístupy k dispozici ve službě Azure a zmírnění kroky, které vám pomohou naplánovat úsilí do vašich projektů migrace.
    - **Problémy s kompatibilitou** kategorie identifikuje částečně podporované nebo nepodporované funkce, které odráží kompatibility potíže, které by mohly blokovat migrace místní databáze SQL serveru do Azure SQL Database. Doporučení jsou k dispozici také můžete vyřešit tyto problémy.

6.  Zkontrolujte výsledky hodnocení pro blokující problémy s migrací a problémy parita funkce výběrem konkrétních možností.

## <a name="migrate-the-sample-schema"></a>Migrace schéma ukázka
Po celý hodnocení a uspokojit, že vybraná databáze je vhodným kandidátem pro migraci do Azure SQL Database pomocí Pomocníka pro migraci dat k migraci schématu do Azure SQL Database.

> [!NOTE]
> Před vytvořením projektu migrace v Pomocníkovi pro migraci dat, ujistěte se, jak je uvedeno v požadavky mají už zřízené Azure SQL database. Pro účely tohoto kurzu, je název databáze SQL Azure považuje za **AdventureWorksAzure**, ale můžete zadat jakýkoli název, který chcete.

K migraci **AdventureWorks2012** schématu do Azure SQL Database, proveďte následující kroky:

1.  V Pomocníka pro migraci dat, vyberte ikonu nový (+) a potom v části **typ projektu**, vyberte **migrace**.
2.  Zadejte název projektu, ve **serveru typ zdroje** textového pole, vyberte **systému SQL Server**a potom v **cíle typ serveru** textového pole, vyberte **Azure SQL Databáze**.
3.  V části **migrace oboru**, vyberte **Schema only**.

    Po provedení předchozích kroků, by měla vypadat rozhraní Pomocník pro migraci dat, jak je znázorněno na následujícím obrázku:
    
    ![Vytvoření projektu pomocníka migrace dat](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

4.  Vyberte **vytvořit** a vytvořte tak projekt.
5.  V Pomocníka pro migraci dat zadat podrobnosti připojení zdroje pro systém SQL Server, vyberte **připojit**a pak vyberte **AdventureWorks2012** databáze.

    ![Podrobnosti připojení pomocníka zdroje dat migrace](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)

6.  Vyberte **Další**v části **připojit k cílovému serveru**, zadat podrobnosti připojení cíl pro databázi Azure SQL, vyberte **Connect**a pak vyberte **AdventureWorksAzure** databáze měl předem zřizovat v databázi Azure SQL.

    ![Podrobnosti připojení pomocníka cíl migrace dat](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)

7.  Vyberte **Další** pro přechod **vybrat objekty** obrazovky, ve kterém můžete zadat objekty schématu v **AdventureWorks2012** databázi, která je potřeba nasadit do Azure Databáze SQL.

    Ve výchozím nastavení jsou vybrány všechny objekty.

    ![Generování skriptů SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)

8.  Vyberte **skript SQL generovat** vytvářet skripty SQL a poté zkontrolovat skripty pro všechny chyby.

    ![Skript schématu](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)

9.  Vyberte **nasazení schématu** pro nasazení schématu do Azure SQL Database a potom po nasazení schématu zkontrolujte cílový server pro anomálie.

    ![Nasazení schématu](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration
1. Přihlaste se k portálu Azure, vyberte **všechny služby**a potom vyberte **odběry**.
 
   ![Zobrazit portálu odběrů](media\tutorial-sql-server-to-azure-sql\portal-select-subscription1.png)
       
2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.
 
    ![Zobrazit zprostředkovatelé prostředků](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)
    
3.  Vyhledávání pro migraci a potom napravo od **Microsoft.DataMigration**, vyberte **zaregistrovat**.
 
    ![Registrace poskytovatele prostředků](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance
1.  Na portálu Azure vyberte + **vytvořit prostředek**, vyhledejte službu migrace databáze Azure a pak vyberte **služba migrace databáze Azure** z rozevíracího seznamu.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)

2.  Na **služba migrace databáze Azure** obrazovku, vyberte **vytvořit**.
 
    ![Vytvoření instance služby migrace databáze Azure](media\tutorial-sql-server-to-azure-sql\dms-create1.png)
  
3.  Na **vytvořit službu migrace** obrazovky, zadejte název pro tuto službu, předplatné a skupiny nový nebo existující prostředek.

4. Vyberte existující virtuální síť (VNET) nebo vytvořte novou.

    Virtuální sítě poskytuje služba migrace databáze Azure přístup k systému SQL Server zdrojové a cílové instance databáze SQL Azure.

    Další informace o tom, jak vytvořit síť VNET na portálu Azure, najdete v článku [vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

5. Vyberte cenovou úroveň.

    Další informace o náklady a cenových úrovní najdete v tématu [stránce s cenami](https://aka.ms/dms-pricing).

    Pokud potřebujete pomoc při výběru správné úrovně služba migrace databáze Azure, podívejte se na doporučení účtování [zde](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Konfigurace nastavení instance služby migrace databáze Azure](media\tutorial-sql-server-to-azure-sql\dms-settings1.png)

6.  Vyberte **vytvořit** vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace
Po vytvoření služby najít na portálu Azure, otevřete ho a pak vytvořte nový projekt migrace.

1. Na portálu Azure vyberte **všechny služby**, vyhledejte službu migrace databáze Azure a pak vyberte **služby migrace databáze Azure**.
 
      ![Vyhledejte všechny instance služby Azure databáze migrace](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. Na **služby migrace databáze Azure** si vyhledávání pro název služby migrace databáze Azure instanci, kterou jste vytvořili a pak vyberte instanci.
 
     ![Najít instanci služby Azure databáze migrace](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Vyberte + **nový projekt migrace**.
4. Na **nový projekt migrace** obrazovky, zadejte název projektu, ve **serveru typ zdroje** textového pole, vyberte **systému SQL Server**a potom v **cíl Typ serveru** textového pole, vyberte **Azure SQL Database**.

    ![Vytvoření projektu služby migrace databáze](media\tutorial-sql-server-to-azure-sql\dms-create-project1.png)

5.  Vyberte **vytvořit** a vytvořte tak projekt.

## <a name="specify-source-details"></a>Zadejte podrobnosti zdroje
1. Na **zdroje podrobnosti** obrazovky, zadat podrobnosti připojení pro instanci systému SQL Server zdrojové.
 
    Nezapomeňte použít plně kvalifikované domény název (FQDN) pro název instance systému SQL Server zdroj. Můžete taky IP adresu pro situace, ve službě DNS, které není možné překlad názvů.

2. Pokud jste nenainstalovali důvěryhodný certifikát na zdrojovém serveru, vyberte **certifikátu serveru důvěryhodnosti** zaškrtávací políčko.

    Když není nainstalován důvěryhodný certifikát, SQL Server vygeneruje certifikát podepsaný svým držitelem při spuštění instance. Tento certifikát se používá k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení SSL, které jsou encyopted používá certifikát podepsaný svým držitelem neposkytuje silné zabezpečení. Jsou náchylné k útokům man-in-the-middle. Neměli byste tedy spoléhat na certifikáty podepsané svým držitelem v produkčním prostředí pomocí SSL nebo na serverech, které jsou připojené k Internetu.

   ![Podrobnosti zdroje](media\tutorial-sql-server-to-azure-sql\dms-source-details1.png)
  
2. Vyberte **Uložit**a pak vyberte **AdventureWorks2012** databáze pro migraci.

    ![Vyberte zdroj DB](media\tutorial-sql-server-to-azure-sql\dms-select-source-db1.png)

## <a name="specify-target-details"></a>Zadejte podrobnosti cíl
1. Vyberte **Uložit**a pak na **cíle podrobnosti** obrazovky, zadat podrobnosti připojení pro cílový Server databází SQL Azure, který je předem zřízená Azure SQL Database na kterou **AdventureWorks2012** schématu byla nasazená pomocí Pomocníka pro migraci dat.

    ![Vyberte cíl](media\tutorial-sql-server-to-azure-sql\dms-select-target1.png)

2. Vyberte **Uložit** k uložení projektu.

3. Na **souhrn projektu** obrazovce zkontrolujte a zkontrolujte podrobnosti o přidružený k projektu migrace.

    ![Souhrn DMS](media\tutorial-sql-server-to-azure-sql\dms-summary1.png)

4. Vyberte **Uložit**.

## <a name="run-the-migration"></a>Spustit migrace
1.  Vyberte nedávno uložený projekt, vyberte + **nová aktivita**a potom vyberte **spuštění migrace**.

    ![Nová aktivita](media\tutorial-sql-server-to-azure-sql\dms-new-activity1.png)

2.  Po zobrazení výzvy zadejte přihlašovací údaje pro zdrojové a cílové servery a potom vyberte **Uložit**.

3.  Na **mapu, která bude cílovým databázím** obrazovky, mapy zdroji a cílová databáze pro migraci.

    Pokud je cílová databáze obsahuje název stejné databáze jako zdrojové databáze, vybere Azure DMS cílová databáze ve výchozím nastavení.

    ![Mapovat na cílové databáze](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity1.png)

4. Vyberte **Uložit**na **vyberte tabulky** obrazovky, rozbalte tabulku se seznamem a poté zkontrolovat seznam ovlivněných polí.

    Všimněte si, že služba migrace databáze Azure automaticky vybere všechny prázdné zdrojové tabulky, které existují v cílové instance databáze SQL Azure. Pokud chcete znovu přenést tabulek, které již obsahují data, je nutné explicitně vybrat tabulky v tomto okně.

    ![Výběr tabulek](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity1.png)

5.  Vyberte **Uložit**na **migrace Souhrn** obrazovce **název aktivity** textové pole, zadejte název aktivity migrace.

6. Rozbalte **možnost ověření** části zobrazíte **zvolte možnost ověření** obrazovky, určete, zda chcete ověřit migrovaných databází pro **schématu porovnání**, **Konzistenci dat**, a **dotaz správnost**.
    
    ![Vyberte možnost ověřování](media\tutorial-sql-server-to-azure-sql\dms-configuration1.png)

6.  Vyberte **Uložit**, zkontrolujte souhrn zajistit, aby odpovídaly zdrojové a cílové podrobnosti, co jste dřív zadali.

    ![Souhrn migrace](media\tutorial-sql-server-to-azure-sql\dms-run-migration1.png)

7.  Vyberte **spuštění migrace**.

    Zobrazí se okno aktivity migrace a **stav** aktivity je **čekající**.

    ![Stav aktivity](media\tutorial-sql-server-to-azure-sql\dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorování migrace
1. Na obrazovce aktivity migrace vyberte **aktualizovat** aktualizovat zobrazení až **stav** migrace zobrazuje jako **dokončeno**.

    ![Stav aktivity dokončena](media\tutorial-sql-server-to-azure-sql\dms-completed-activity1.png)

2. Po dokončení migrace, vyberte **stáhnout sestavu** získat sestavu se seznamem podrobnosti přidružené k procesu migrace.

3. Zkontrolujte cílové databáze na cílovém serveru Azure SQL Database.

### <a name="additional-resources"></a>Další zdroje informací:

 * [Migrace SQL pomocí Azure Data migrace služby (DMS)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) praktické cvičení.