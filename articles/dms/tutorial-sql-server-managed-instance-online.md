---
title: 'Kurz: Azure Database Migration Service můžete provést online migraci SQL serveru do spravované instance Azure SQL Database | Dokumentace Microsoftu'
description: Zjistěte, jak provést online migrace z místního SQL serveru do spravované instance Azure SQL Database s využitím Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 02/08/2019
ms.openlocfilehash: 95eb5131cb953cdcb5f7c5cc255c80ae0b9e1ca8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997206"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-managed-instance-online-using-dms"></a>Kurz: Migrace SQL serveru do spravované instance Azure SQL Database pomocí DMS online

Azure Database Migration Service můžete použít k migraci databází z místní instance systému SQL Server do [Azure SQL Database managed instance](../sql-database/sql-database-managed-instance.md) s minimálními výpadky. Další metody, které mohou vyžadovat některé ruční práce, najdete v článku [migrace instance SQL serveru do služby Azure SQL Database managed instance](../sql-database/sql-database-managed-instance-migrate.md).

V tomto kurzu, migrujete **Adventureworks2012** databáze z místní instance SQL serveru do spravované instance Azure SQL Database s minimálními prostoji s využitím Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvoření instance služby Azure Database Migration Service
> - Vytvoření projektu migrace a spuštění online migrace pomocí služby Azure Database Migration Service
> - Monitorování migrace
> - Spuštění přímé migrace, jakmile budete připraveni

> [!NOTE]
> Použití Azure Database Migration Service online migrace vyžaduje vytvoření instance založené na cenovou úroveň Premium.
> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje online migraci z SQL serveru do spravované instance Azure SQL Database. Offline migrace, najdete v části [migrace systému SQL Server do služby Azure SQL Database managed instance pomocí DMS offline](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

- Vytvořte pro službu Azure Database Migration Service virtuální síť s použitím modelu nasazení Azure Resource Manager, který poskytuje možnosti připojení typu Site-to-Site k místním zdrojovým serverům prostřednictvím [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo sítě [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Zjistěte síťové topologie pro migrace Azure SQL Database managed instance pomocí Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Zajistěte, aby pravidla skupiny zabezpečení virtuální sítě Azure neblokovala následující komunikační porty: 443, 53, 9354, 445 a 12000. Další podrobnosti o filtrování provozu pomocí skupiny zabezpečení virtuální sítě Azure najdete v článku [Filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Nakonfigurujte bránu [Windows Firewall pro přístup ke zdrojovému databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému SQL Serveru, který ve výchozím nastavení probíhá přes port TCP 1433.
- Pokud provozujete několik pojmenovaných instancí SQL Serveru s využitím dynamických portů, možná budete chtít povolit službu SQL Browser a přístup k portu UDP 1434 přes vaše brány firewall, aby se služba Azure Database Migration Service mohla připojit k pojmenované instanci na vašem zdrojovém serveru.
- Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace a také k souborům přes port SMB 445.
- Vytvoření spravované instance Azure SQL Database pomocí následujících podrobností v článku [vytvořit spravovanou instanci Azure SQL Database na webu Azure Portal](https://aka.ms/sqldbmi).
- Zajistěte, aby byly přihlašovací údaje používat k připojení zdrojového systému SQL Server a cílit na spravované instanci členové role serveru sysadmin.
- Zadejte sdílenou složku SMB obsahující soubory úplných záloh všech vašich databází a soubory následných záloh transakčních protokolů, které může služba Azure Database Migration Service použít k migraci databází.
- Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
- Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Služba Azure Database Migration Service zosobní přihlašovací údaje uživatele za účelem nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
- Vytvořte Azure Active Directory aplikace ID, které vygeneruje klíč ID aplikace, služby DMS můžete použít k připojení k cílové databázi Azure managed instance a kontejner úložiště Azure. Další informace najdete v článku [Vytvoření aplikace Azure Active Directory a instančního objektu s přístupem k prostředkům pomocí portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
- Vytvořte nebo si poznamenejte **úroveň výkonu Standard** účtu úložiště Azure, do kterého může služba DMS nahrát soubory záloh databází a který může použít k migraci databází.  Nezapomeňte účet úložiště Azure vytvořit ve stejné oblasti jako službu DMS.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)        
2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte **Azure Database Migration Service** a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service přístup k systému SQL Server zdrojové a cílové spravované instance Azure SQL Database.

    Další informace o vytvoření virtuální sítě na webu Azure Portal najdete v článku [Vytvoření virtuální sítě pomocí webu Azure Portal](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [síťové topologie pro Azure SQL Database managed instance migrace pomocí Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Vyberte SKU cenové úrovně Premium.

    > [!NOTE]
    > Online migrace jsou podporované jenom při použití na úrovni Premium. 

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Vytvoření služby DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a vyberte ji.

3. Vyberte **+ Nový projekt migrace**.

4. Na **nový projekt migrace** obrazovky, zadejte název projektu, v **serveru typ zdroje** textového pole, vyberte **systému SQL Server**v **cílového serveru typ** textového pole, vyberte **Azure SQL Database Managed Instance**a potom **zvolte typ aktivity**vyberte **Online data migrace** .

   ![Vytvoření projektu DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení SSL šifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na připojení SSL s použitím certifikátů podepsaných svým držitelem byste neměli spoléhat v produkčním prostředí ani na serverech připojených k internetu.

   ![Podrobnosti zdroje](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Vyberte **Uložit**.

4. Na obrazovce **Vybrat zdrojové databáze** vyberte databázi **Adventureworks2012** určenou k migraci.

   ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Pokud používáte SQL Server Integration Services (SSIS), DMS aktuálně nepodporují migraci databáze katalogu projektů/balíčků SSIS (SSISDB) z SQL serveru do spravované instance Azure SQL Database. Můžete však zajistit služby SSIS v Azure Data Factory (ADF) a opětovné nasazení vašich projektů/balíčků služby SSIS do cílového umístění databází SSISDB hostovanou ve spravované instanci Azure SQL Database. Další informace o migraci balíčků služby SSIS, najdete v článku [balíčků migrovat SQL Server Integration Services do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na **podrobnosti o cíli migrace** obrazovky, zadejte **ID aplikace** a **klíč** , DMS instance můžete použít k připojení k cílové instanci Azure SQL Database spravovaná instance a účet úložiště Azure.

    Další informace najdete v článku [Vytvoření aplikace Azure Active Directory a instančního objektu s přístupem k prostředkům pomocí portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Vyberte **předplatné** obsahující cílovou instanci Azure SQL Database managed instance a potom vyberte cílovou instanci.

    Pokud jste ještě nezřídili spravované instance Azure SQL Database, vyberte [odkaz](https://aka.ms/SQLDBMI) můžete zřídit instanci. Pokud Azure SQL Database managed instance je připraven, vraťte se do tohoto konkrétního projektu k provedení migrace.

3. Zadejte **uživatel SQL** a **heslo** pro připojení ke službě Azure SQL Database managed instance.

    ![Výběr cíle](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Vyberte **Uložit**.

## <a name="select-source-databases"></a>Výběr zdrojových databází

1. Na obrazovce **Vybrat zdrojové databáze** vyberte zdrojovou databázi, kterou chcete migrovat.

    ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

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

    ![Konfigurace nastavení migrace](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

2. Vyberte **Uložit**.

## <a name="review-the-migration-summary"></a>Kontrola shrnutí migrace

1. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

2. Zkontrolujte a ověřte podrobnosti související s projektem migrace.

    ![Shrnutí projektu migrace](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Spuštění a monitorování migrace

1. Vyberte **Spustit migraci**.

2. Na obrazovce aktivity migrace výběrem **Aktualizovat** aktualizujte zobrazení.

   ![Probíhající aktivita migrace](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Kategorie databází a přihlašovacích jmen můžete dále rozbalit a monitorovat stav migrace příslušných objektů serveru.

   ![Probíhající aktivita migrace](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Provedení přímé migrace

Po obnovení úplná záloha databáze v cílové instanci spravované instance Azure SQL Database je k dispozici k provedení migrace přímou migraci databáze.

1. Jakmile budete připraveni dokončit online migraci databází, vyberte **Spustit přímou migraci**.

2. Zastavte veškerý příchozí provoz do zdrojových databází.

3. Proveďte [zálohování protokolu poškozené databáze], zpřístupněte soubor zálohy ve sdílené síťové složce SMB a pak počkejte na obnovení této konečné zálohy transakčních protokolů.

    V tuto chvíli se zobrazí hodnota **Probíhající změny** nastavená na 0.

4. Vyberte **Potvrdit** a pak **Použít**.

    ![Příprava na dokončení přímé migrace](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Když se zobrazí stav migrace databáze **dokončeno**, připojení aplikací k na novou cílovou instanci spravované instance Azure SQL Database.

    ![Dokončení přímé migrace](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Další postup

- Kurz ukazuje, jak migrovat databázi do managed instance pomocí příkazu T-SQL RESTORE, najdete v tématu [obnovení zálohy do spravované instance pomocí příkazu restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Informace o spravované instance najdete v tématu [co je managed instance](../sql-database/sql-database-managed-instance.md).
- Informace o připojování aplikací k managed instance najdete v tématu [připojit aplikace](../sql-database/sql-database-managed-instance-connect-app.md).
