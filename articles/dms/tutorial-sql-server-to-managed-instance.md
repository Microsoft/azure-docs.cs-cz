---
title: 'Kurz: Migrace instance spravované sql serverem do sql spravované instance'
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat z místního SQL Serveru do spravované instance Azure SQL Database pomocí služby Migrace databáze Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 416be7de4b3cef4fb6e1bcfd09d934937f8c96d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297726"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Kurz: Migrace SQL Serveru do instance spravované databází Azure SQL database offline pomocí DMS

Službu migrace databáze Azure můžete použít k migraci databází z místní instance serveru SQL Server do [instance spravované službou Azure SQL Database](../sql-database/sql-database-managed-instance.md). Další metody, které mohou vyžadovat určité ruční úsilí, naleznete v článku [migrace instance instance SQL Server do spravované instance Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

V tomto kurzu migrujete databázi **Adventureworks2012** z místní instance SERVERU SQL Server do instance spravované databáze SQL pomocí služby Migrace databáze Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> - Vytvořte instanci služby Azure Database Migration Service.
> - Vytvořte projekt migrace pomocí služby Migrace databáze Azure.
> - Spuštění migrace
> - Monitorování migrace
> - Stažení sestavy migrace

> [!IMPORTANT]
> Pro offline migrace z SQL Serveru do instance spravované databází SQL database může služba Migrace databáze Azure vytvořit záložní soubory za vás. Alternativně můžete poskytnout nejnovější úplnou zálohu databáze ve sdílené síťové složce SMB, kterou bude služba používat k migraci databází. Nepřipobujte více záloh do jednoho zálohovacího média. vezměte každou zálohu na samostatný záložní soubor. Všimněte si, že můžete použít komprimované zálohy také snížit pravděpodobnost výskytu potenciální problémy s migrací velkých záloh.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje offline migraci z SQL Serveru do spravované instance sql database. Online migraci najdete v [tématu Migrace SQL Serveru do instance spravované databází Azure SQL database online pomocí DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

- Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Naučte se síťové topologie pro migrace spravovaných instancí Azure SQL Database pomocí služby Migrace databáze Azure](https://aka.ms/dmsnetworkformi). Další informace o vytvoření virtuální sítě naleznete v [dokumentaci k virtuální síti](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlého startu s podrobnými podrobnostmi.

    > [!NOTE]
    > Pokud během instalace virtuální sítě používáte ExpressRoute s partnerským vztahem k síti společnosti Microsoft, přidejte do podsítě, ve které bude služba zřízena, následující [koncové body služby:](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
    > - Cílový koncový bod databáze (například koncový bod SQL, koncový bod Cosmos DB a tak dále)
    > - Koncový bod úložiště
    > - Koncový bod sběrnice
    >
    > Tato konfigurace je nezbytná, protože služba Azure Database Migration Service nemá připojení k internetu.

- Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě neblokují následující příchozí komunikační porty služby Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Nakonfigurujte bránu [Windows Firewall pro přístup ke zdrojovému databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete bránu Windows Firewall, abyste povolili službě Migrace databáze Azure přístup ke zdrojovému serveru SQL Server, který je ve výchozím nastavení portem TCP 1433.
- Pokud používáte více pojmenovaných instancí SQL Serveru pomocí dynamických portů, můžete povolit službu prohlížeče SQL a povolit přístup k portu UDP 1434 prostřednictvím bran firewall, aby se služba Migrace databáze Azure mohla připojit k pojmenované instanci ve zdroji. Server.
- Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla brány firewall, která umožní službě Migrace databáze Azure přístup ke zdrojové databázi pro migraci, stejně jako soubory přes port SMB 445.
- Vytvořte spravovanou instanci Azure SQL Database podle podrobností v článku [Vytvoření instance spravované azure SQL database na webu Azure Portal](https://aka.ms/sqldbmi).
- Ujistěte se, že přihlášení používaná k připojení zdrojového serveru SQL Server a cílové spravované instance jsou členy role serveru sysadmin.

    >[!NOTE]
    >Ve výchozím nastavení služba Migrace databáze Azure podporuje jenom migrace přihlášení SQL. Můžete však povolit možnost migrace přihlášení systému Windows:
    >
    >- Zajištění, že cílová instance spravované databáze SQL database má přístup pro čtení AAD, který může nakonfigurovat prostřednictvím portálu Azure uživatelem s rolí **Správce společnosti**nebo **globálního správce.**
    >- Konfigurace instance služby Migrace databáze Azure tak, aby umožňovala migrace přihlášení uživatelů a skupin pro Windows, která se nastaví na webu Azure Portal, na stránce Konfigurace. Po povolení tohoto nastavení restartujte službu, aby se změny projevily.
    >
    > Po restartování služby se přihlášení uživatelů nebo skupin systému Windows zobrazí v seznamu přihlášení, která jsou k dispozici pro migraci. U všech přihlášení uživatelů nebo skupin systému Windows, které migrujete, budete vyzváni k zadání přidruženého názvu domény. Uživatelské účty služeb (účet s názvem domény NT AUTHORITY) a virtuální uživatelské účty (název účtu s názvem domény NT SERVICE) nejsou podporovány.

- Vytvořte sdílenou síťovou složku, kterou může služba Azure Database Migration Service použít k zálohování zdrojové databáze.
- Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
- Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Služba Migrace databáze Azure zosobňuje přihlašovací údaje uživatele k nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
- Vytvořte kontejner objektů blob a načtěte jeho identifikátor URI SAS pomocí postupu v článku [Správa prostředků služby Azure Blob Storage pomocí Průzkumníka služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Při vytváření identifikátoru URI SAS nezapomeňte v okně zásad vybrat všechna oprávnění (čtení, zápis, odstranění, výpis). Tento detail poskytuje službě Migrace databáze Azure přístup ke kontejneru vašeho účtu úložiště pro nahrávání záložních souborů používaných pro migraci databází do spravované instance Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Migrace databáze Azure, a pak vyberte **zprostředkovatele prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte + **Vytvořit prostředek**, vyhledejte **službu Migrace databáze Azure**a v rozevíracím seznamu vyberte **službu Migrace databáze Azure.**

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo ji vytvořte.

    Virtuální síť poskytuje službě Migrace databáze Azure přístup ke zdrojovému serveru SQL Server a cílové instanci spravované službou Azure SQL Database.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [Síťové topologií pro migrace spravovaných instancí Azure SQL DB pomocí služby Migrace databáze Azure](https://aka.ms/dmsnetworkformi).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Vytvoření služby DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Migrace databáze Azure](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a vyberte instanci.

3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server**, v textovém poli **Typ cílového serveru** vyberte **Spravovaná instance Azure SQL Database** a pak v části **Zvolte typ aktivity** vyberte **Offline migrace dat**.

   ![Vytvoření projektu DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Vyberte **Vytvořit** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení TLS, která jsou šifrována pomocí certifikátu podepsaného svým držitelem, neposkytuje silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na protokol TLS byste neměli spoléhat pomocí certifikátů podepsaných svým držitelem v produkčním prostředí nebo na serverech, které jsou připojeny k Internetu.

   ![Podrobnosti zdroje](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Vyberte **Uložit**.

4. Na obrazovce **Vybrat zdrojové databáze** vyberte databázi **Adventureworks2012** určenou k migraci.

   ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Pokud používáte SQL Server Integration Services (SSIS), DMS aktuálně nepodporuje migraci databáze katalogu pro vaše projekty SSIS projekty/balíčky (SSISDB) z SQL Server do Azure SQL Database spravované instance. Můžete ale zřídit SSIS v Azure Data Factory (ADF) a znovu nasadit projekty/balíčky SSIS do cílové sisdb hostované spravovanou instancí Azure SQL Database. Další informace o migraci balíčků SSIS naleznete v článku [Migrace balíčků služby SQL Server Integration Services do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **podrobnosti cíle migrace** zadejte podrobnosti o připojení pro cíl, což je předem zřízená instance spravované službou Azure SQL Database, do které migrujete databázi **AdventureWorks2012.**

    Pokud jste ještě nezřízené instance spravované databáze SQL, vyberte [odkaz,](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) který vám pomůže zřídit instanci. Stále můžete pokračovat s vytvářením projektu a pak, když je připravená instance spravované Azure SQL Database, vrátíte se k tomuto konkrétnímu projektu a spusťte migraci.

    ![Výběr cíle](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Vyberte **Uložit**.

## <a name="select-source-databases"></a>Výběr zdrojových databází

1. Na obrazovce **Vybrat zdrojové databáze** vyberte zdrojovou databázi, kterou chcete migrovat.

    ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Vyberte **Uložit**.

## <a name="select-logins"></a>Výběr přihlašovacích jmen

1. Na obrazovce **Vybrat přihlašovací jména** vyberte přihlašovací jména, která chcete migrovat.

    >[!NOTE]
    >Ve výchozím nastavení služba Migrace databáze Azure podporuje jenom migrace přihlášení SQL. Chcete-li povolit podporu pro migraci přihlášení systému Windows, naleznete **požadavky** části tohoto kurzu.

    ![Výběr přihlašovacích jmen](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Vyberte **Uložit**.

## <a name="configure-migration-settings"></a>Konfigurace nastavení migrace

1. Na obrazovce **Konfigurace nastavení migrace** zadejte následující podrobnosti:

    | | |
    |--------|---------|
    |**Zvolte možnost zálohování zdroje** | Zvolte možnost **Poskytnu nejnovější záložní soubory**, pokud už máte k dispozici soubory úplné zálohy, které může DMS použít k migraci databáze. Zvolte možnost **Umožním, aby služba Azure Database Migration Service vytvářela soubory zálohy**, pokud chcete, aby služba DMS nejprve provedla úplné zálohování zdrojové databáze a tuto zálohu pak použila k migraci. |
    |**Sdílení síťového umístění** | Místní sdílená síť SMB, do které může služba Azure Database Migration Service převést zálohy zdrojové databáze. Účet služby, který spouští zdrojovou instanci SQL Serveru, musí mít oprávnění k zápisu do této sdílené síťové složky. Zadejte plně kvalifikovaný název domény nebo IP adresy serveru ve sdílené síťové složce, například \\\název_serveru.název_domény.com\záložní_složka nebo \\\IP_adressa\záložní_složka.|
    |**Uživatelské jméno** | Ujistěte se, že má uživatel Windows oprávnění Úplné řízení ke sdílené síťové složce, kterou jste určili dříve. Služba Migrace databáze Azure zosobní přihlašovací údaje uživatele k nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení. Pokud jsou pro migraci vybrané databáze s povoleným transparentním šifrováním dat, výše uvedený uživatel Windows musí být předdefinovaným účtem správce a pro službu Azure Database Migration Service musí být zakázaný [nástroj Řízení uživatelských účtů](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview), aby bylo možné nahrávat a odstraňovat soubory certifikátů. |
    |**Heslo** | Heslo pro tohoto uživatele. |
    |**Nastavení účtu úložiště** | Identifikátor URI SAS, který poskytuje službě Migrace databáze Azure přístup ke kontejneru vašeho účtu úložiště, do kterého služba nahrává záložní soubory a který se používá pro migraci databází do spravované instance Azure SQL Database. [Zjistěte, jak získat identifikátor URI SAS pro kontejner objektů blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Nastavení transparentního šifrování dat** | Pokud migrujete zdrojové databáze s povoleným transparentním šifrováním dat (TDE), musíte mít oprávnění k zápisu na cílovou instanci spravované databází Azure SQL Database.  V rozevírací nabídce vyberte předplatné, ve kterém byla zřízena instance spravované službou Azure SQL Database.  V rozevírací nabídce vyberte cílovou **spravovanou instanci Azure SQL Database**. |

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

  Zobrazí se okno aktivity migrace a stav aktivity je **Čeká na vyřízení**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace výběrem **Aktualizovat** aktualizujte zobrazení.

   ![Probíhající aktivita migrace](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Kategorie databází a přihlašovacích jmen můžete dále rozbalit a monitorovat stav migrace příslušných objektů serveru.

   ![Probíhající aktivita migrace](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Po dokončení migrace vyberte **Stáhnout sestavu** a získejte sestavu s výpisem podrobností souvisejících s procesem migrace.

3. Ověřte, zda cílová databáze v cílovém prostředí spravované databáze Azure SQL Database spravuje instance.

## <a name="next-steps"></a>Další kroky

- Kurz, který ukazuje, jak migrovat databázi do spravované instance pomocí příkazu T-SQL RESTORE, naleznete v [tématu Obnovení zálohy na spravovanou instanci pomocí příkazu obnovení](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Informace o spravované instanci naleznete v [tématu Co je spravovaná instance](../sql-database/sql-database-managed-instance.md).
- Informace o připojení aplikací ke spravované instanci najdete v tématu [Připojení aplikací](../sql-database/sql-database-managed-instance-connect-app.md).
