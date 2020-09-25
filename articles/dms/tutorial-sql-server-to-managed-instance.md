---
title: 'Kurz: migrace SQL Server do spravované instance SQL'
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat z SQL Server do spravované instance Azure SQL pomocí Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: defe7cb8ec727ac358789368f0897639fa109b16
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308601"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>Kurz: migrace SQL Server do spravované instance Azure SQL do offline režimu pomocí DMS

Pomocí Azure Database Migration Service můžete migrovat databáze z instance SQL Server do [spravované instance Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Další metody, které mohou vyžadovat určité ruční úsilí, najdete v článku [migrace instance SQL Server do spravované instance SQL](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

V tomto kurzu migrujete databázi **Adventureworks2012** z místní instance SQL Server do spravované instance SQL pomocí Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> - Vytvořte instanci služby Azure Database Migration Service.
> - Vytvořte projekt migrace pomocí Azure Database Migration Service.
> - Spuštění migrace
> - Monitorujte migraci.
> - Stažení sestavy migrace

> [!IMPORTANT]
> Pro offline migrace z SQL Server do spravované instance SQL může Azure Database Migration Service vytvořit záložní soubory. Alternativně můžete v síťové sdílené složce SMB zadat nejnovější úplnou zálohu databáze, kterou bude služba používat k migraci vašich databází. Nepřipojujte více záloh do jediného záložního média. proveďte každou zálohu samostatného záložního souboru. Všimněte si, že můžete použít také komprimované zálohy, abyste snížili pravděpodobnost výskytu potenciálních problémů s migrací velkých záloh.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje offline migraci z SQL Server do spravované instance SQL. Online migrace najdete v článku [migrace SQL Server do spravované instance SQL online pomocí DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

- Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který umožňuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Naučte se síťové topologie pro migrace spravovaných instancí SQL pomocí Azure Database Migration Service](https://aka.ms/dmsnetworkformi). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlý Start s podrobnými údaji.

    > [!NOTE]
    > Pokud při instalaci virtuální sítě používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, tyto [koncové body](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) služby:
    > - Koncový bod cílové databáze (například koncový bod SQL, Cosmos DB koncový bod atd.)
    > - Koncový bod úložiště
    > - Koncový bod služby Service Bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.

- Zajistěte, aby pravidla skupiny zabezpečení sítě virtuálních sítí neblokovala následující příchozí komunikační porty Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Nakonfigurujte bránu [Windows Firewall pro přístup ke zdrojovému databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete bránu Windows Firewall, čímž povolíte Azure Database Migration Service přístup ke zdrojovému SQL Server, který je ve výchozím nastavení port TCP 1433.
- Pokud spouštíte více instancí s názvem SQL Server s použitím dynamických portů, možná budete chtít povolit službu SQL Browser a povolit přístup k portu UDP 1434 přes brány firewall, aby se Azure Database Migration Service mohl připojit k pojmenované instanci na zdrojovém serveru.
- Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla firewallu, která Azure Database Migration Service umožní přístup ke zdrojovým databázím pro migraci, stejně jako souborům přes SMB port 445.
- Vytvořte spravovanou instanci SQL podle podrobných pokynů v článku [Vytvoření spravované instance SQL v Azure Portal](https://aka.ms/sqldbmi).
- Zajistěte, aby se přihlašovací jména používaná k připojení zdrojového SQL Server a cílovou instanci SQL Managed instance používala jako členové role serveru sysadmin.

    >[!NOTE]
    >Ve výchozím nastavení Azure Database Migration Service podporuje pouze migraci přihlašovacích údajů SQL. Můžete ale povolit možnost migrovat přihlášení Windows pomocí:
    >
    >- Zajistěte, aby cílová instance SQL Managed instance měla oprávnění ke čtení AAD, které je možné nakonfigurovat prostřednictvím Azure Portal uživatelem s rolí **Správce společnosti**nebo **globální správce**.
    >- Konfigurace instance Azure Database Migration Service, aby se povolily migrace přihlášení uživatelů nebo skupin systému Windows, které se nastaví přes Azure Portal, na stránce konfigurace. Po povolení tohoto nastavení restartujte službu, aby se změny projevily.
    >
    > Po restartování služby se zobrazí přihlášení uživatele nebo skupiny systému Windows v seznamu přihlášení, která jsou k dispozici pro migraci. U všech přihlášení uživatelů nebo skupin systému Windows, které migrujete, budete vyzváni k zadání přidruženého názvu domény. Uživatelské účty služby (účet s názvem domény NT AUTHORITY) a virtuální uživatelské účty (název účtu s názvem domény NT) nejsou podporovány.

- Vytvořte sdílenou síťovou složku, kterou může Azure Database Migration Service použít k zálohování zdrojové databáze.
- Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
- Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Azure Database Migration Service zosobňuje přihlašovací údaje uživatele k nahrání záložních souborů do kontejneru Azure Storage pro operaci obnovení.
- Vytvořte kontejner objektů blob a načtěte jeho identifikátor URI SAS pomocí postupu v článku [Správa prostředků služby Azure Blob Storage pomocí Průzkumníka služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Při vytváření identifikátoru URI SAS nezapomeňte v okně zásad vybrat všechna oprávnění (čtení, zápis, odstranění, výpis). Tato podrobnosti poskytuje Azure Database Migration Service s přístupem k vašemu kontejneru účtu úložiště pro nahrávání záložních souborů používaných pro migraci databází do spravované instance SQL.

    > [!NOTE]
    > Při konfiguraci nastavení účtu úložiště během kroku [Konfigurace nastavení migrace](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance#configure-migration-settings) Azure Database Migration Service nepodporuje použití tokenu SAS na úrovni účtu.
    
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. V Azure Portal vyberte + **vytvořit prostředek**, vyhledejte **Azure Database Migration Service**a v rozevíracím seznamu vyberte **Azure Database Migration Service** .

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo ji vytvořte.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojovému SQL Server a cílové spravované instanci SQL.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [síťové topologie pro migrace spravované instance SQL Azure pomocí Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Vytvoření služby DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledat všechny instance Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a pak vyberte instanci.

3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **typ zdrojového serveru** vyberte možnost **SQL Server**, v textovém poli **typ CÍLOVÉHO serveru** vyberte možnost **spravovaná instance Azure SQL**a pak pro **zvolit typ aktivity**vyberte možnost **migrace offline dat**.

   ![Vytvoření projektu DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Vyberte **Vytvořit** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení TLS zašifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Neměli byste spoléhat na protokol TLS pomocí certifikátů podepsaných držitelem v produkčním prostředí nebo na serverech, které jsou připojené k Internetu.

   ![Podrobnosti zdroje](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Vyberte **Uložit**.

4. Na obrazovce **Vybrat zdrojové databáze** vyberte databázi **Adventureworks2012** určenou k migraci.

   ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Pokud používáte služba SSIS (SQL Server Integration Services) (SSIS), služba DMS v současné době nepodporuje migraci databáze katalogu pro vaše projekty SSIS a balíčky (SSISDB) z SQL Server na spravovanou instanci SQL. Můžete ale zřídit SSIS v Azure Data Factory (ADF) a znovu nasadit SSIS projekty/balíčky do cílového SSISDBu hostovaného službou SQL Managed instance. Další informace o migraci balíčků SSIS najdete v článku migrace balíčků [služba SSIS (SQL Server Integration Services) do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **Podrobnosti cíle migrace** zadejte podrobnosti o připojení pro cíl, což je předem zřízená spravovaná instance SQL, na kterou migrujete databázi **AdventureWorks2012** .

    Pokud jste ještě nezřídili spravovanou instanci SQL, vyberte [odkaz](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) , který vám pomůžete zřídit instanci. I nadále můžete pokračovat v vytváření projektu a poté, když je SQL Managed instance připravena, vraťte se do tohoto konkrétního projektu a spusťte migraci.

    ![Výběr cíle](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Vyberte **Uložit**.

## <a name="select-source-databases"></a>Výběr zdrojových databází

1. Na obrazovce **Vybrat zdrojové databáze** vyberte zdrojovou databázi, kterou chcete migrovat.

    ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Vyberte **Uložit**.

## <a name="select-logins"></a>Výběr přihlašovacích jmen

1. Na obrazovce **Vybrat přihlašovací jména** vyberte přihlašovací jména, která chcete migrovat.

    >[!NOTE]
    >Ve výchozím nastavení Azure Database Migration Service podporuje pouze migraci přihlašovacích údajů SQL. Pokud chcete povolit podporu pro migraci přihlašovacích údajů systému Windows, přečtěte si část **požadavky** tohoto kurzu.

    ![Výběr přihlašovacích jmen](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Vyberte **Uložit**.

## <a name="configure-migration-settings"></a>Konfigurace nastavení migrace

1. Na obrazovce **Konfigurace nastavení migrace** zadejte následující podrobnosti:

    | | |
    |--------|---------|
    |**Zvolte možnost zálohování zdroje** | Zvolte možnost **Poskytnu nejnovější záložní soubory**, pokud už máte k dispozici soubory úplné zálohy, které může DMS použít k migraci databáze. Zvolte možnost **Umožním, aby služba Azure Database Migration Service vytvářela soubory zálohy**, pokud chcete, aby služba DMS nejprve provedla úplné zálohování zdrojové databáze a tuto zálohu pak použila k migraci. |
    |**Sdílení síťového umístění** | Místní sdílená síťová složka SMB, do které Azure Database Migration Service může přebírat zdrojové zálohy databáze. Účet služby, který spouští zdrojovou instanci SQL Serveru, musí mít oprávnění k zápisu do této sdílené síťové složky. Zadejte plně kvalifikovaný název domény nebo IP adresy serveru ve sdílené síťové složce, například \\\název_serveru.název_domény.com\záložní_složka nebo \\\IP_adressa\záložní_složka.|
    |**Uživatelské jméno** | Ujistěte se, že má uživatel Windows oprávnění Úplné řízení ke sdílené síťové složce, kterou jste určili dříve. Azure Database Migration Service zosobní přihlašovací údaje uživatele, aby nahrála záložní soubory do Azure Storage kontejneru pro operaci obnovení. Pokud jsou pro migraci vybrané databáze s povoleným transparentním šifrováním dat, výše uvedený uživatel Windows musí být předdefinovaným účtem správce a pro službu Azure Database Migration Service musí být zakázaný [nástroj Řízení uživatelských účtů](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview), aby bylo možné nahrávat a odstraňovat soubory certifikátů. |
    |**Heslo** | Heslo pro tohoto uživatele. |
    |**Nastavení účtu úložiště** | Identifikátor URI SAS, který poskytuje Azure Database Migration Service s přístupem k kontejneru účtu úložiště, do kterého služba odesílá záložní soubory a která se používá pro migraci databází do spravované instance SQL. [Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Tento identifikátor URI SAS musí být pro kontejner objektů blob, nikoli pro účet úložiště.|
    |**Nastavení transparentního šifrování dat** | Pokud migrujete zdrojové databáze s povoleným transparentní šifrování dat (TDE), budete muset mít oprávnění k zápisu do cílové spravované instance SQL.  Vyberte předplatné, ve kterém je spravovaná instance SQL zřízená z rozevírací nabídky.  V rozevírací nabídce vyberte cílovou **spravovanou instanci Azure SQL Database**. |

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

  Zobrazí se okno aktivita migrace a stav aktivity **čeká na vyřízení**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace výběrem **Aktualizovat** aktualizujte zobrazení.

   ![Probíhající aktivita migrace](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Kategorie databází a přihlašovacích jmen můžete dále rozbalit a monitorovat stav migrace příslušných objektů serveru.

   ![Probíhající aktivita migrace](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Po dokončení migrace vyberte **Stáhnout sestavu** a získejte sestavu s výpisem podrobností souvisejících s procesem migrace.

3. Ověřte, jestli je cílová databáze v cílovém prostředí spravované instance SQL.

## <a name="next-steps"></a>Další kroky

- Kurz, ve kterém se dozvíte, jak migrovat databázi do spravované instance SQL pomocí příkazu T-SQL Restore, najdete v tématu [obnovení zálohy do spravované instance SQL pomocí příkazu RESTORE](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Informace o spravované instanci SQL najdete v tématu [co je Managed instance SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
- Informace o připojení aplikací ke službě Managed instance SQL najdete v tématu věnovaném [připojení aplikací](../azure-sql/managed-instance/connect-application-instance.md).
