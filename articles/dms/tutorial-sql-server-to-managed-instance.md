---
title: 'Kurz: Migrace na spravované instanci Azure SQL Database pomocí DMS | Dokumentace Microsoftu'
description: Zjistěte, jak migrovat z místního SQL serveru do spravované instance Azure SQL Database s využitím Azure Database Migration Service.
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
ms.openlocfilehash: b1c635e5b4374b0369a0cf3d1cbff6d8087085a6
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990236"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-managed-instance-offline-using-dms"></a>Kurz: Migrace SQL serveru do spravované instance Azure SQL Database pomocí DMS do offline režimu

Azure Database Migration Service můžete použít k migraci databází z místní instance systému SQL Server do [Azure SQL Database managed instance](../sql-database/sql-database-managed-instance.md). Další metody, které mohou vyžadovat některé ruční práce, najdete v článku [migrace instance SQL serveru do služby Azure SQL Database managed instance](../sql-database/sql-database-managed-instance-migrate.md).

V tomto kurzu, migrujete **Adventureworks2012** databáze z místní instance SQL serveru do spravované instance Azure SQL Database s využitím Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Vytvoření instance služby Azure Database Migration Service
> - Vytvoření projektu migrace pomocí služby Azure Database Migration Service
> - Spuštění migrace
> - Monitorování migrace
> - Stažení sestavy migrace

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje při offline migraci z SQL serveru do spravované instance Azure SQL Database. Online migrace, najdete v části [migrace systému SQL Server do služby Azure SQL Database managed instance pomocí DMS online](tutorial-sql-server-managed-instance-online.md).

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
- Vytvořte sdílenou síťovou složku, kterou může Azure Database Migration Service použít k zálohování zdrojové databáze.
- Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
- Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Služba Azure Database Migration Service zosobní přihlašovací údaje uživatele za účelem nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
- Vytvořte kontejner objektů blob a načtěte jeho identifikátor URI SAS pomocí postupu v článku [Správa prostředků služby Azure Blob Storage pomocí Průzkumníka služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Při vytváření identifikátoru URI SAS nezapomeňte v okně zásad vybrat všechna oprávnění (čtení, zápis, odstranění, výpis). Těchto podrobných informací poskytuje Azure Database Migration Service přístup ke kontejneru účtu úložiště pro nahrávání záložní soubory použít k migraci databází do Azure SQL Database managed instance.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)        

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte **Azure Database Migration Service** a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service přístup k systému SQL Server zdrojové a cílové spravované instance Azure SQL Database.

    Další informace o vytvoření virtuální sítě na webu Azure Portal najdete v článku [Vytvoření virtuální sítě pomocí webu Azure Portal](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [síťové topologie pro Azure SQL DB managed instance migrace pomocí Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Vytvoření služby DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a vyberte ji.

3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server**, v textovém poli **Typ cílového serveru** vyberte **Spravovaná instance Azure SQL Database** a pak v části **Zvolte typ aktivity** vyberte **Offline migrace dat**.

   ![Vytvoření projektu DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Vyberte **Vytvořit** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení SSL šifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na připojení SSL s použitím certifikátů podepsaných svým držitelem byste neměli spoléhat v produkčním prostředí ani na serverech připojených k internetu.

   ![Podrobnosti zdroje](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Vyberte **Uložit**.

4. Na obrazovce **Vybrat zdrojové databáze** vyberte databázi **Adventureworks2012** určenou k migraci.

   ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Pokud používáte SQL Server Integration Services (SSIS), DMS aktuálně nepodporují migraci databáze katalogu projektů/balíčků SSIS (SSISDB) z SQL serveru do spravované instance Azure SQL Database. Můžete však zajistit služby SSIS v Azure Data Factory (ADF) a opětovné nasazení vašich projektů/balíčků služby SSIS do cílového umístění databází SSISDB hostovanou ve spravované instanci Azure SQL Database. Další informace o migraci balíčků služby SSIS, najdete v článku [balíčků migrovat SQL Server Integration Services do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na **podrobnosti o cíli migrace** obrazovky, zadat podrobnosti připojení pro cíl, který je předem zřízená spravované instance Azure SQL Database do kterého migrujete **AdventureWorks2012**databáze.

    Pokud jste ještě nezřídili spravované instance Azure SQL Database, vyberte **ne** pro odkaz na nápovědu můžete zřídit instanci. Můžete dál pokračujte ve vytváření projektu a, když Azure SQL Database managed instance je připravena, vraťte se do tohoto konkrétního projektu k provedení migrace.

       ![Select Target](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Vyberte **Uložit**.

## <a name="select-source-databases"></a>Výběr zdrojových databází

1. Na obrazovce **Vybrat zdrojové databáze** vyberte zdrojovou databázi, kterou chcete migrovat.

    ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Vyberte **Uložit**.

## <a name="select-logins"></a>Výběr přihlašovacích jmen

1. Na obrazovce **Vybrat přihlašovací jména** vyberte přihlašovací jména, která chcete migrovat.

    >[!NOTE]
    >Tato verze podporuje pouze migraci přihlašovacích jmen SQL.

    ![Výběr přihlašovacích jmen](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Vyberte **Uložit**.

## <a name="configure-migration-settings"></a>Konfigurace nastavení migrace

1. Na obrazovce **Konfigurace nastavení migrace** zadejte následující podrobnosti:

    | | |
    |--------|---------|
    |**Zvolte možnost zálohování zdroje** | Zvolte možnost **Poskytnu nejnovější záložní soubory**, pokud už máte k dispozici soubory úplné zálohy, které může DMS použít k migraci databáze. Zvolte možnost **Umožním, aby služba Azure Database Migration Service vytvářela soubory zálohy**, pokud chcete, aby služba DMS nejprve provedla úplné zálohování zdrojové databáze a tuto zálohu pak použila k migraci. |
    |**Sdílení síťového umístění** | Místní sdílená síťová složka SMB, do které může Azure Database Migration Service ukládat zálohy zdrojové databáze. Účet služby, který spouští zdrojovou instanci SQL Serveru, musí mít oprávnění k zápisu do této sdílené síťové složky. Zadejte plně kvalifikovaný název domény nebo IP adresy serveru ve sdílené síťové složce, například \\\název_serveru.název_domény.com\záložní_složka nebo \\\IP_adressa\záložní_složka.|
    |**Uživatelské jméno** | Ujistěte se, že má uživatel Windows oprávnění Úplné řízení ke sdílené síťové složce, kterou jste určili dříve. Služba Azure Database Migration Service zosobní přihlašovací údaje uživatele za účelem nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení. Pokud jsou pro migraci vybrané databáze s povoleným transparentním šifrováním dat, výše uvedený uživatel Windows musí být předdefinovaným účtem správce a pro službu Azure Database Migration Service musí být zakázaný [nástroj Řízení uživatelských účtů](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview), aby bylo možné nahrávat a odstraňovat soubory certifikátů. |
    |**Heslo** | Heslo pro tohoto uživatele. |
    |**Nastavení účtu úložiště** | Identifikátor URI SAS, který poskytuje Azure Database Migration Service přístup k vaší kontejner účtu úložiště, do kterého služba nahraje zálohování souborů a, který se používá k migraci databází do spravované instance Azure SQL Database. [Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Nastavení transparentního šifrování dat** | Pokud provádíte migraci zdrojové databáze s transparentní šifrování dat (TDE) povolena, musíte mít oprávnění k zápisu na cíl spravované instance Azure SQL Database.  Vyberte předplatné, ve kterém Azure SQL Database managed instance zřízené z rozevírací nabídky.  V rozevírací nabídce vyberte cílovou **spravovanou instanci Azure SQL Database**. |

    ![Konfigurace nastavení migrace](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Vyberte **Uložit**.

## <a name="review-the-migration-summary"></a>Kontrola shrnutí migrace

1. Na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

2. Rozbalením části **Možnost ověřování** zobrazte obrazovku **Vybrat možnost ověřování**, kde určete, jestli chcete u migrovaných databází ověřovat správnost dotazu, a pak vyberte **Uložit**.

3. Zkontrolujte a ověřte podrobnosti související s projektem migrace.

    ![Shrnutí projektu migrace](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Vyberte **Uložit**.

## <a name="run-the-migration"></a>Spuštění migrace

- Vyberte **Spustit migraci**.

  Zobrazí se okno aktivity migrace a stav aktivity bude **Probíhající**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace výběrem **Aktualizovat** aktualizujte zobrazení.
 
   ![Probíhající aktivita migrace](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Kategorie databází a přihlašovacích jmen můžete dále rozbalit a monitorovat stav migrace příslušných objektů serveru.

   ![Probíhající aktivita migrace](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Po dokončení migrace vyberte **Stáhnout sestavu** a získejte sestavu s výpisem podrobností souvisejících s procesem migrace.

3. Ověřte, že cílová databáze na cílovou službu Azure SQL Database managed instance prostředí.

## <a name="next-steps"></a>Další postup

- Kurz ukazuje, jak migrovat databázi do managed instance pomocí příkazu T-SQL RESTORE, najdete v tématu [obnovení zálohy do spravované instance pomocí příkazu restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Informace o spravované instance najdete v tématu [co je managed instance](../sql-database/sql-database-managed-instance.md).
- Informace o připojování aplikací k managed instance najdete v tématu [připojit aplikace](../sql-database/sql-database-managed-instance-connect-app.md).