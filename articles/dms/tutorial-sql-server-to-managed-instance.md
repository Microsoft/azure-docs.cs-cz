---
title: Migrace do spravované instance Azure SQL Database pomocí DMS | Microsoft Docs
description: Zjistěte, jak pomocí služby Azure Database Migration Service provést migraci z místního SQL Serveru do spravované instance Azure SQL Database.
services: dms
author: edmacauley
ms.author: jtoland
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: dbf71b1fcc15743f4670c4072921f1a167a90e97
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887298"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-offline-using-dms"></a>Offline migrace SQL Serveru do spravované instance Azure SQL Database pomocí DMS
Pomocí služby Azure Database Migration Service můžete migrovat databáze z místní instance SQL Serveru do [spravované instance Azure SQL Database](../sql-database/sql-database-managed-instance.md). Další metody, které můžou vyžadovat určitou ruční práci, najdete v článku [Migrace instance SQL Serveru do spravované instance Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Projekty migrace z SQL Serveru do spravované instance Azure SQL Database jsou ve verzi Preview a vztahují se na ně [Dodatečné podmínky používání verzí Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto kurzu pomocí služby Azure Database Migration Service provedete migraci databáze **Adventureworks2012** z místní instance SQL Serveru do spravované instance Azure SQL Database.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvoření projektu migrace pomocí služby Azure Database Migration Service
> * Spuštění migrace
> * Monitorování migrace
> * Stažení sestavy migrace

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu je potřeba provést následující:

- Vytvořte pro službu Azure Database Migration Service virtuální síť s použitím modelu nasazení Azure Resource Manager, který poskytuje možnosti připojení typu Site-to-Site k místním zdrojovým serverům prostřednictvím [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo sítě [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Seznamte se s topologiemi sítí pro migrace spravovaných instancí Azure SQL Database pomocí služby Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Zajistěte, aby pravidla skupiny zabezpečení virtuální sítě Azure neblokovala následující komunikační porty: 443, 53, 9354, 445 a 12000. Další podrobnosti o filtrování provozu pomocí skupiny zabezpečení virtuální sítě Azure najdete v článku [Filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Nakonfigurujte bránu [Windows Firewall pro přístup ke zdrojovému databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
- Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
- Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace a také k souborům přes port SMB 445.
- Vytvořte spravovanou instanci Azure SQL Database podle podrobných pokynů v článku [Vytvoření spravované instance Azure SQL Database na webu Azure Portal](https://aka.ms/sqldbmi).
- Ujistěte se, že přihlašovací jména použitá pro připojení ke zdrojovému SQL Serveru a cílové spravované instanci patří členům role serveru sysadmin.
- Vytvořte sdílenou síťovou složku, kterou může Azure Database Migration Service použít k zálohování zdrojové databáze.
- Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
- Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Služba Azure Database Migration Service zosobní přihlašovací údaje uživatele za účelem nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
- Vytvořte kontejner objektů blob a načtěte jeho identifikátor URI SAS pomocí postupu v článku [Správa prostředků služby Azure Blob Storage pomocí Průzkumníka služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Při vytváření identifikátoru URI SAS nezapomeňte v okně zásad vybrat všechna oprávnění (čtení, zápis, odstranění, výpis). Tento detail poskytne službě Azure Database Migration Service přístup k vašemu kontejneru účtu úložiště pro nahrání záložních souborů, které se použijí k migraci databází do spravované instance Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte **Azure Database Migration Service** a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4.  Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo vytvořte novou.
 
    Virtuální síť poskytuje službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru a cílové spravované instanci Azure SQL Database.

    Další informace o vytvoření virtuální sítě na webu Azure Portal najdete v článku [Vytvoření virtuální sítě pomocí webu Azure Portal](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [Topologie sítí pro migrace spravovaných instancí Azure SQL Database pomocí služby Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).
   
    ![Vytvoření služby DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service2.png)

7.  Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance\dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a vyberte ji.
 
3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server**, v textovém poli **Typ cílového serveru** vyberte **Spravovaná instance Azure SQL Database** a pak v části **Zvolte typ aktivity** vyberte **Offline migrace dat**.

   ![Vytvoření projektu DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project2.png)

5. Vyberte **Vytvořit** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení SSL šifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na připojení SSL s použitím certifikátů podepsaných svým držitelem byste neměli spoléhat v produkčním prostředí ani na serverech připojených k internetu.

   ![Podrobnosti zdroje](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Vyberte **Uložit**.

4. Na obrazovce **Vybrat zdrojové databáze** vyberte databázi **Adventureworks2012** určenou k migraci.

   ![Výběr zdrojových databází](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1.  Na obrazovce **Podrobnosti cíle migrace** zadejte podrobnosti o připojení k cíli. Tím je předem zřízená spravovaná instance Azure SQL Database, do které migrujete databázi **AdventureWorks2012**.

    Pokud jste ještě nezřídili spravovanou instanci Azure SQL Database, vyberte **Ne**. Zobrazí se odkaz na nápovědu ke zřízení instance. Můžete pokračovat ve vytváření projektu a jakmile bude spravovaná instance Azure SQL Database připravená, můžete se k tomuto projektu vrátit a spustit migraci.   
 
       ![Výběr cíle](media\tutorial-sql-server-to-managed-instance\dms-target-details2.png)

2.  Vyberte **Uložit**.

## <a name="select-source-databases"></a>Výběr zdrojových databází

1. Na obrazovce **Vybrat zdrojové databáze** vyberte zdrojovou databázi, kterou chcete migrovat.

    ![Výběr zdrojových databází](media\tutorial-sql-server-to-managed-instance\select-source-databases.png)

2. Vyberte **Uložit**.

## <a name="select-logins"></a>Výběr přihlašovacích jmen
 
1. Na obrazovce **Vybrat přihlašovací jména** vyberte přihlašovací jména, která chcete migrovat.

    >[!NOTE]
    >Tato verze podporuje pouze migraci přihlašovacích jmen SQL.

    ![Výběr přihlašovacích jmen](media\tutorial-sql-server-to-managed-instance\select-logins.png)

2. Vyberte **Uložit**.
 
## <a name="configure-migration-settings"></a>Konfigurace nastavení migrace
 
1. Na obrazovce **Konfigurace nastavení migrace** zadejte následující podrobnosti:

    | | |
    |--------|---------|
    |**Zvolte možnost zálohování zdroje** | Zvolte možnost **Poskytnu nejnovější záložní soubory**, pokud už máte k dispozici soubory úplné zálohy, které může DMS použít k migraci databáze. Zvolte možnost **Umožním, aby služba Azure Database Migration Service vytvářela soubory zálohy**, pokud chcete, aby služba DMS nejprve provedla úplné zálohování zdrojové databáze a tuto zálohu pak použila k migraci. |
    |**Sdílení síťového umístění** | Místní sdílená síťová složka SMB, do které může Azure Database Migration Service ukládat zálohy zdrojové databáze. Účet služby, který spouští zdrojovou instanci SQL Serveru, musí mít oprávnění k zápisu do této sdílené síťové složky. Zadejte plně kvalifikovaný název domény nebo IP adresy serveru ve sdílené síťové složce, například \\\název_serveru.název_domény.com\záložní_složka nebo \\\IP_adressa\záložní_složka.|
    |**Uživatelské jméno** | Ujistěte se, že má uživatel Windows oprávnění Úplné řízení ke sdílené síťové složce, kterou jste určili dříve. Služba Azure Database Migration Service zosobní přihlašovací údaje uživatele za účelem nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení. Pokud jsou pro migraci vybrané databáze s povoleným transparentním šifrováním dat, výše uvedený uživatel Windows musí být předdefinovaným účtem správce a pro službu Azure Database Migration Service musí být zakázaný [nástroj Řízení uživatelských účtů](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview), aby bylo možné nahrávat a odstraňovat soubory certifikátů. |
    |**Heslo** | Heslo pro tohoto uživatele. |
    |**Nastavení účtu úložiště** | Identifikátor URI SAS, který službě Azure Database Migration Service poskytne přístup k vašemu kontejneru účtu úložiště, do kterého služba nahraje záložní soubory a který se použije k migraci databází do spravované instance Azure SQL Database. [Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Nastavení transparentního šifrování dat** | Pokud migrujete zdrojové databáze s povoleným transparentním šifrováním dat, musíte mít oprávnění k zápisu do cílové spravované instance Azure SQL Database.  V rozevírací nabídce vyberte předplatné, ve kterém je spravovaná instance Azure SQL Database zřízená.  V rozevírací nabídce vyberte cílovou spravovanou instanci Azure SQL Database. |
    
    ![Konfigurace nastavení migrace](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings3.png)

2. Vyberte **Uložit**.
 
## <a name="review-the-migration-summary"></a>Kontrola shrnutí migrace

1. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

2. Rozbalením části **Možnost ověřování** zobrazte obrazovku **Vybrat možnost ověřování**, kde určete, jestli chcete u migrovaných databází ověřovat správnost dotazu, a pak vyberte **Uložit**.

3. Zkontrolujte a ověřte podrobnosti související s projektem migrace.
 
    ![Shrnutí projektu migrace](media\tutorial-sql-server-to-managed-instance\dms-project-summary2.png)

4.  Vyberte **Uložit**.   

## <a name="run-the-migration"></a>Spuštění migrace

- Vyberte **Spustit migraci**.

  Zobrazí se okno aktivity migrace a stav aktivity bude **Probíhající**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace výběrem **Aktualizovat** aktualizujte zobrazení.
 
   ![Probíhající aktivita migrace](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration1.png)

    Kategorie databází a přihlašovacích jmen můžete dále rozbalit a monitorovat stav migrace příslušných objektů serveru.

   ![Probíhající aktivita migrace](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration-extend.png)

2. Po dokončení migrace vyberte **Stáhnout sestavu** a získejte sestavu s výpisem podrobností souvisejících s procesem migrace.
 
3. Ověřte, že se cílová databáze nachází v cílovém prostředí spravované instance Azure SQL Database.

## <a name="next-steps"></a>Další kroky

- V tématu [Obnovení zálohy do spravované instance pomocí příkazu restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md) najdete kurz ukazující migraci databáze do spravované instance pomocí příkazu T-SQL RESTORE.
- Informace o spravované instanci najdete v tématu [Co je spravovaná instance](../sql-database/sql-database-managed-instance.md).
- Informace o připojování aplikací ke spravované instanci najdete v tématu věnovaném [připojení aplikací](../sql-database/sql-database-managed-instance-connect-app.md).