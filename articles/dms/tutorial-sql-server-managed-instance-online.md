---
title: Provedení online migrace SQL Serveru do spravované instance Azure SQL Database pomocí služby Azure Database Migration Service | Microsoft Docs
description: Zjistěte, jak pomocí služby Azure Database Migration Service provést online migraci z místního SQL Serveru do spravované instance Azure SQL Database.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 4d46a438708e2e72d625f5104293b4605f7b096d
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946075"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-online-using-dms"></a>Online migrace SQL Serveru do spravované instance Azure SQL Database pomocí DMS
Pomocí služby Azure Database Migration Service můžete migrovat databáze z místní instance SQL Serveru do [spravované instance Azure SQL Database](../sql-database/sql-database-managed-instance.md) s minimálními výpadky. Další metody, které můžou vyžadovat určitou ruční práci, najdete v článku [Migrace instance SQL Serveru do spravované instance Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

>[!IMPORTANT]
>Projekty online migrace z SQL Serveru do spravované instance Azure SQL Database jsou ve verzi Preview a vztahují se na ně [Dodatečné podmínky používání verzí Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto kurzu pomocí služby Azure Database Migration Service provedete migraci databáze **Adventureworks2012** z místní instance SQL Serveru do spravované instance Azure SQL Database s minimálními výpadky.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvoření projektu migrace a spuštění online migrace pomocí služby Azure Database Migration Service
> * Monitorování migrace
> * Spuštění přímé migrace, jakmile budete připraveni

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje online migraci z SQL Serveru do spravované instance Azure SQL Database. Informace o offline migraci najdete v tématu [Offline migrace SQL Serveru do spravované instance Azure SQL Database pomocí DMS](tutorial-sql-server-to-managed-instance.md).

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
- Zadejte sdílenou složku SMB obsahující soubory úplných záloh všech vašich databází a soubory následných záloh transakčních protokolů, které může služba Azure Database Migration Service použít k migraci databází.
- Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
- Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Služba Azure Database Migration Service zosobní přihlašovací údaje uživatele za účelem nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
- Vytvořte aplikaci Azure Active Directory, která vygeneruje klíč ID aplikace, pomocí kterého se služba DMS může připojit k cílové spravované instanci databáze Azure a kontejneru úložiště Azure. Další informace najdete v článku [Vytvoření aplikace Azure Active Directory a instančního objektu s přístupem k prostředkům pomocí portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
- Vytvořte nebo si poznamenejte **úroveň výkonu Standard** účtu úložiště Azure, do kterého může služba DMS nahrát soubory záloh databází a který může použít k migraci databází.  Nezapomeňte účet úložiště Azure vytvořit ve stejné oblasti jako službu DMS.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media\tutorial-sql-server-to-managed-instance-online\portal-select-subscriptions.png)        
2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media\tutorial-sql-server-to-managed-instance-online\portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media\tutorial-sql-server-to-managed-instance-online\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte **Azure Database Migration Service** a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance-online\portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance-online\dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4.  Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo vytvořte novou.
 
    Virtuální síť poskytuje službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru a cílové spravované instanci Azure SQL Database.

    Další informace o vytvoření virtuální sítě na webu Azure Portal najdete v článku [Vytvoření virtuální sítě pomocí webu Azure Portal](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [Topologie sítí pro migrace spravovaných instancí Azure SQL Database pomocí služby Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Vyberte skladovou položku z cenové úrovně Pro důležité obchodní informace (Preview).

    > [!NOTE]
    > Online migrace se podporují pouze při použití úrovně Pro důležité obchodní informace (Preview). 
   
    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).
   
    ![Vytvoření služby DMS](media\tutorial-sql-server-to-managed-instance-online\dms-create-service3.png)

7.  Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Azure Database Migration Service](media\tutorial-sql-server-to-managed-instance-online\dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a vyberte ji.
 
3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server**, v textovém poli **Typ cílového serveru** vyberte **Spravovaná instance Azure SQL Database** a pak v části **Zvolte typ aktivity** vyberte **Online migrace dat (Preview)**.

   ![Vytvoření projektu DMS](media\tutorial-sql-server-to-managed-instance-online\dms-create-project3.png)

5. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení SSL šifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na připojení SSL s použitím certifikátů podepsaných svým držitelem byste neměli spoléhat v produkčním prostředí ani na serverech připojených k internetu.

   ![Podrobnosti zdroje](media\tutorial-sql-server-to-managed-instance-online\dms-source-details2.png)

3. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1.  Na obrazovce **Podrobnosti cíle migrace** zadejte **ID aplikace** a **Klíč**, pomocí kterých se instance DMS může připojit k cílové spravované instanci Azure SQL Database a účtu úložiště Azure.

    Další informace najdete v článku [Vytvoření aplikace Azure Active Directory a instančního objektu s přístupem k prostředkům pomocí portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
    
2. Vyberte **Předplatné** obsahující cílovou spravovanou instanci Azure SQL Database a pak vyberte cílovou instanci.

    Pokud jste ještě nezřídili spravovanou instanci Azure SQL Database, vyberte [odkaz](https://aka.ms/SQLDBMI) na nápovědu ke zřízení instance. Jakmile bude spravovaná instance Azure SQL Database připravená, vraťte se k tomuto projektu a spusťte migraci.

3. Zadejte **uživatele SQL** a **heslo** pro připojení k cílové spravované instanci Azure SQL Database.

       ![Select Target](media\tutorial-sql-server-to-managed-instance-online\dms-target-details3.png)

2.  Vyberte **Uložit**.

## <a name="select-source-databases"></a>Výběr zdrojových databází

1. Na obrazovce **Vybrat zdrojové databáze** vyberte zdrojovou databázi, kterou chcete migrovat.

    ![Výběr zdrojových databází](media\tutorial-sql-server-to-managed-instance-online\dms-select-source-databases2.png)

2. Vyberte **Uložit**.

## <a name="configure-migration-settings"></a>Konfigurace nastavení migrace
 
1. Na obrazovce **Konfigurace nastavení migrace** zadejte následující podrobnosti:

    | | |
    |--------|---------|
    |**Sdílená složka SMB v síťovém umístění** | Místní sdílená síťová složka SMB obsahující soubory úplných záloh databází a soubory záloh transakčních protokolů, které může služba Azure Database Migration Service použít k migraci. Účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, musí pro tuto sdílenou síťovou složku mít oprávnění ke čtení i zápisu. Zadejte plně kvalifikovaný název domény nebo IP adresy serveru ve sdílené síťové složce, například \\\název_serveru.název_domény.com\záložní_složka nebo \\\IP_adressa\záložní_složka.|
    |**Uživatelské jméno** | Ujistěte se, že má uživatel Windows oprávnění Úplné řízení ke sdílené síťové složce, kterou jste určili dříve. Služba Azure Database Migration Service zosobní přihlašovací údaje uživatele za účelem nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení. |
    |**Heslo** | Heslo pro tohoto uživatele. |
    |**Předplatné účtu úložiště Azure** | Vyberte předplatné obsahující účet úložiště Azure. |
    |**Účet úložiště Azure** | Vyberte účet úložiště Azure, do kterého může DMS nahrát soubory záloh ze sdílené síťové složky SMB a který může použít k migraci databází.  Pro zajištění optimálního výkonu nahrávání souborů doporučujeme vybrat účet úložiště ve stejné oblasti, jako je služba DMS. |
    
    ![Konfigurace nastavení migrace](media\tutorial-sql-server-to-managed-instance-online\dms-configure-migration-settings4.png)

2. Vyberte **Uložit**.
 
## <a name="review-the-migration-summary"></a>Kontrola shrnutí migrace

1. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

2. Zkontrolujte a ověřte podrobnosti související s projektem migrace.
 
    ![Shrnutí projektu migrace](media\tutorial-sql-server-to-managed-instance-online\dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Spuštění a monitorování migrace

1. Vyberte **Spustit migraci**.

2. Na obrazovce aktivity migrace výběrem **Aktualizovat** aktualizujte zobrazení.
 
   ![Probíhající aktivita migrace](media\tutorial-sql-server-to-managed-instance-online\dms-monitor-migration2.png)

    Kategorie databází a přihlašovacích jmen můžete dále rozbalit a monitorovat stav migrace příslušných objektů serveru.

   ![Probíhající aktivita migrace](media\tutorial-sql-server-to-managed-instance-online\dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Provedení přímé migrace

Po obnovení úplné zálohy databáze v cílové spravované instanci Azure SQL Database bude databáze k dispozici pro provedení přímé migrace.

1.  Jakmile budete připraveni dokončit online migraci databází, vyberte **Spustit přímou migraci**.

2.  Zastavte veškerý příchozí provoz do zdrojových databází.

3.  Proveďte [zálohování protokolu poškozené databáze], zpřístupněte soubor zálohy ve sdílené síťové složce SMB a pak počkejte na obnovení této konečné zálohy transakčních protokolů.

    V tuto chvíli se zobrazí hodnota **Probíhající změny** nastavená na 0.

4.  Vyberte **Potvrdit** a pak **Použít**.

    ![Příprava na dokončení přímé migrace](media\tutorial-sql-server-to-managed-instance-online\dms-complete-cutover.png)

5.  Když se stav migrace databází změní na **Dokončeno**, připojte své aplikace k nové cílové spravované instanci Azure SQL Database.

    ![Dokončení přímé migrace](media\tutorial-sql-server-to-managed-instance-online\dms-cutover-complete.png)

## <a name="next-steps"></a>Další kroky

- V tématu [Obnovení zálohy do spravované instance pomocí příkazu restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md) najdete kurz ukazující migraci databáze do spravované instance pomocí příkazu T-SQL RESTORE.
- Informace o spravované instanci najdete v tématu [Co je spravovaná instance](../sql-database/sql-database-managed-instance.md).
- Informace o připojování aplikací ke spravované instanci najdete v tématu věnovaném [připojení aplikací](../sql-database/sql-database-managed-instance-connect-app.md).
