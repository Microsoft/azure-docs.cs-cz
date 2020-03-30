---
title: 'Kurz: Migrace serveru SQL Server online do instance spravované SQL'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z místního SQL Serveru do spravované instance Azure SQL Database pomocí služby Migrace databáze Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 236c68b3c26049073d3e6e942ce2a6be8b7f4fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298903"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Kurz: Migrace SQL Serveru do instance spravované databází Azure SQL Database online pomocí DMS

Pomocí služby Migrace databáze Azure můžete použít k migraci databází z místní instance SERVERU SQL Server do [instance spravované službou Azure SQL Database](../sql-database/sql-database-managed-instance.md) s minimálními prostoji. Další metody, které mohou vyžadovat určité ruční úsilí, naleznete v článku [migrace instance instance SQL Server do spravované instance Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

V tomto kurzu migrujete databázi **Adventureworks2012** z místní instance SQL Serveru do instance spravované databází SQL s minimálními prostoji pomocí služby Migrace databáze Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace a začněte migraci online pomocí služby Migrace databáze Azure.
> * Monitorování migrace
> * Proveďte přechod migrace, až budete připraveni.

> [!IMPORTANT]
> Pro online migrace z SQL Server do instance spravované sql database pomocí služby Migrace databáze Azure, musíte poskytnout úplné zálohování databáze a následné zálohy protokolů ve sdílené síťové složce SMB, kterou může služba použít k migraci databází. Služba Migrace databáze Azure neinicializuje žádné zálohy a místo toho používá existující zálohy, které už můžete mít jako součást plánu zotavení po havárii, pro migraci.
> Ujistěte se, že budete mít [zálohy pomocí možnosti With CHECKSUM](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Také se ujistěte, že není připojit více záloh (tj. úplné a t-log) do jednoho záložního média; vezměte každou zálohu na samostatný záložní soubor. Nakonec můžete použít komprimované zálohy ke snížení pravděpodobnosti výskytu potenciálních problémů spojených s migrací velkých záloh.

> [!NOTE]
> Použití služby Migrace databáze Azure k provedení migrace online vyžaduje vytvoření instance založené na cenové úrovni Premium.

> [!IMPORTANT]
> Pro optimální prostředí migrace Microsoft doporučuje vytvořit instanci služby Migrace databáze Azure ve stejné oblasti Azure jako cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

> [!IMPORTANT]
> Zkraťte dobu trvání procesu migrace online co nejvíce, abyste minimalizovali riziko přerušení způsobeného rekonfigurací instance nebo plánovanou údržbou. V případě takové události bude proces migrace zahájen od začátku. V případě plánované údržby je doba odkladu 36 hodin před restartováním procesu migrace.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje online migraci z SQL Serveru do spravované instance sql database. Informace o offline migraci najdete [v tématu Migrace serveru SQL Server do instance spravované databází SQL v offline pomocí služby DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Naučte se síťové topologie pro migrace spravovaných instancí Azure SQL Database pomocí služby Migrace databáze Azure](https://aka.ms/dmsnetworkformi). Další informace o vytvoření virtuální sítě naleznete v [dokumentaci k virtuální síti](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlého startu s podrobnými podrobnostmi.

    > [!NOTE]
    > Pokud během instalace virtuální sítě používáte ExpressRoute s partnerským vztahem k síti společnosti Microsoft, přidejte do podsítě, ve které bude služba zřízena, následující [koncové body služby:](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
    >
    > * Cílový koncový bod databáze (například koncový bod SQL, koncový bod Cosmos DB a tak dále)
    > * Koncový bod úložiště
    > * Koncový bod sběrnice
    >
    > Tato konfigurace je nezbytná, protože služba Azure Database Migration Service nemá připojení k internetu.
    >
    >Pokud nemáte připojení mezi lokalitou k webu mezi místní sítí a Azure nebo pokud je omezená šířka pásma připojení mezi lokalitami, zvažte použití služby Migrace databáze Azure v hybridním režimu (preview). Hybridní režim využívá místního pracovníka migrace spolu s instancí služby Migrace databáze Azure spuštěnou v cloudu. Pokud chcete vytvořit instanci služby Migrace databáze Azure v hybridním režimu, přečtěte si článek [Vytvoření instance služby Migrace databáze Azure v hybridním režimu pomocí portálu Azure](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > Pokud jde o účet úložiště používaný jako součást migrace, musíte buď:
    > * Zvolte, zda chcete povolit přístup k účtu úložiště celé síti.
    > * Zapněte [delegování podsítě](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) v podsíti MI a aktualizujte pravidla brány firewall účtu úložiště, abyste tuto podsíť povolili.

* Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě neblokují následující příchozí komunikační porty služby Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Nakonfigurujte bránu [Windows Firewall pro přístup ke zdrojovému databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu Windows Firewall, abyste povolili službě Migrace databáze Azure přístup ke zdrojovému serveru SQL Server, který je ve výchozím nastavení portem TCP 1433.
* Pokud používáte více pojmenovaných instancí SQL Serveru pomocí dynamických portů, můžete povolit službu prohlížeče SQL a povolit přístup k portu UDP 1434 prostřednictvím bran firewall, aby se služba Migrace databáze Azure mohla připojit k pojmenované instanci ve zdroji. Server.
* Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla brány firewall, která umožní službě Migrace databáze Azure přístup ke zdrojové databázi pro migraci, stejně jako soubory přes port SMB 445.
* Vytvořte spravovanou instanci sql database podle podrobností v článku [Vytvoření spravované instance Azure SQL Database na webu Azure Portal](https://aka.ms/sqldbmi).
* Ujistěte se, že přihlašovací jména použitá pro připojení ke zdrojovému SQL Serveru a cílové spravované instanci patří členům role serveru sysadmin.
* Zadejte sdílenou síťovou složku SMB, která obsahuje všechny záložní soubory databáze úplné databáze a následné záložní soubory protokolu transakcí, které může služba Azure Database Migration Service použít pro migraci databáze.
* Ujistěte se, že účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, má oprávnění k zápisu do sdílené síťové složky, kterou jste vytvořili, a že účet počítače pro zdrojový server má k této sdílené složce přístup pro čtení i zápis.
* Poznamenejte si uživatele Windows (a jeho heslo) s oprávněním Úplné řízení ke sdílené síťové složce, kterou jste vytvořili dříve. Služba Migrace databáze Azure zosobňuje přihlašovací údaje uživatele k nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení.
* Vytvořte ID aplikace Azure Active Directory, které generuje klíč ID aplikace, který může služba Azure Database Migration Service použít k připojení k cílové instanci spravované databází Azure a kontejneru úložiště Azure. Další informace najdete v článku [Vytvoření aplikace Azure Active Directory a instančního objektu s přístupem k prostředkům pomocí portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > Služba migrace databáze Azure vyžaduje oprávnění přispěvatele k předplatnému pro zadané ID aplikace. Případně můžete vytvořit vlastní role, které udělují konkrétní oprávnění, která vyžaduje služba Migrace databáze Azure. Podrobné pokyny k používání vlastních rolí naleznete v článku [Vlastní role pro migrace online instancí spravované službou SQL Server to SQL Database Database](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Vytvořte nebo si poznamenejte **úroveň výkonu Standard** účtu úložiště Azure, do kterého může služba DMS nahrát soubory záloh databází a který může použít k migraci databází.  Ujistěte se, že jste vytvořili účet úložiště Azure ve stejné oblasti, kde se vytváří instance služby Migrace databáze Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

    ![Zobrazení předplatných na portálu](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Migrace databáze Azure, a pak vyberte **zprostředkovatele prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte + **Vytvořit prostředek**, vyhledejte **službu Migrace databáze Azure**a v rozevíracím seznamu vyberte **službu Migrace databáze Azure.**

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete instanci DMS vytvořit.

5. Vyberte existující virtuální síť nebo ji vytvořte.

    Virtuální síť poskytuje službě Migrace databáze Azure přístup ke zdrojovému serveru SQL Server a cílové instanci spravované databází SQL.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

    Další podrobnosti najdete v článku [Síťové topologie pro migrace spravovaných instancí Azure SQL Database pomocí služby Migrace databáze Azure](https://aka.ms/dmsnetworkformi).

6. Vyberte skladovou položku z cenové úrovně Premium.

    > [!NOTE]
    > Migrace online jsou podporovány pouze při použití úrovně Premium.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Vytvoření služby DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření instance služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Migrace databáze Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Na obrazovce **Azure Database Migration Service** vyhledejte název instance, kterou jste vytvořili, a vyberte instanci.

3. Vyberte **+ Nový projekt migrace**.

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli Typ **zdrojového serveru** vyberte sql **server**, v textovém poli **Typ cílového serveru** vyberte Azure SQL Database Managed **Instance**a potom pro zvolit **typ aktivity**vyberte **Online migrace dat**.

   ![Vytvoření projektu služby migrace databáze Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojovému SQL Serveru.

2. Pokud jste na svém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení TLS, která jsou šifrována pomocí certifikátu podepsaného svým držitelem, neposkytuje silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na protokol TLS byste neměli spoléhat pomocí certifikátů podepsaných svým držitelem v produkčním prostředí nebo na serverech, které jsou připojeny k Internetu.

   ![Podrobnosti zdroje](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Vyberte **Uložit**.

4. Na obrazovce **Vybrat zdrojové databáze** vyberte databázi **Adventureworks2012** určenou k migraci.

   ![Výběr zdrojových databází](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Pokud používáte SQL Server Integration Services (SSIS), DMS aktuálně nepodporuje migraci databáze katalogu pro vaše projekty SSIS projekty/balíčky (SSISDB) z SQL Server do Azure SQL Database spravované instance. Můžete ale zřídit SSIS v Azure Data Factory (ADF) a znovu nasadit projekty/balíčky SSIS do cílové sisdb hostované spravovanou instancí Azure SQL Database. Další informace o migraci balíčků SSIS naleznete v článku [Migrace balíčků služby SQL Server Integration Services do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **podrobnosti cíle migrace** zadejte **ID aplikace** a **klíč,** který instanci DMS můžete použít pro připojení k cílové instanci instance spravované služby Azure SQL Database a účtu úložiště Azure.

    Další informace najdete v článku [Vytvoření aplikace Azure Active Directory a instančního objektu s přístupem k prostředkům pomocí portálu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Vyberte **předplatný obsahující** cílovou instanci spravované instance Azure SQL Database a pak vyberte cílovou instanci.

    Pokud jste ještě nezřídili spravovanou instanci Azure SQL Database, vyberte [odkaz,](https://aka.ms/SQLDBMI) který vám pomůže zřídit instanci. Když je připravená instance spravované databází Azure SQL Database, vraťte se k tomuto konkrétnímu projektu a proveďte migraci.

3. Poskytněte **uživateli SQL** a **heslu** připojení k instanci spravované službou Azure SQL Database.

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
    |**Sdílená složka SMB v síťovém umístění** | Místní sdílená síť Oválná síť Nebo Sdílená síť Azure, která obsahuje záložní soubory úplné databáze a záložní soubory protokolu transakcí, které může služba Azure Database Migration Service použít pro migraci. Účet služby, ve kterém je spuštěná zdrojová instance SQL Serveru, musí pro tuto sdílenou síťovou složku mít oprávnění ke čtení i zápisu. Zadejte plně kvalifikovaný název domény nebo IP adresy serveru ve sdílené síťové složce, například \\\název_serveru.název_domény.com\záložní_složka nebo \\\IP_adressa\záložní_složka. Pro zvýšení výkonu doporučujeme použít samostatnou složku pro každou databázi, která má být migrována. Cestu ke sdílení souboru na úrovni databáze můžete zadat pomocí možnosti **Upřesnit nastavení.** |
    |**Uživatelské jméno** | Ujistěte se, že má uživatel Windows oprávnění Úplné řízení ke sdílené síťové složce, kterou jste určili dříve. Služba Migrace databáze Azure zosobní přihlašovací údaje uživatele k nahrání záložních souborů do kontejneru úložiště Azure pro operaci obnovení. Pokud používáte sdílenou složku Azure, použijte jako uživatelské jméno název účtu úložiště s azure\. |
    |**Heslo** | Heslo pro tohoto uživatele. Pokud používáte sdílenou složku Azure, použijte jako heslo klíč účtu úložiště. |
    |**Předplatné účtu úložiště Azure** | Vyberte předplatné obsahující účet úložiště Azure. |
    |**Účet úložiště Azure** | Vyberte účet úložiště Azure, do kterého může DMS nahrát soubory záloh ze sdílené síťové složky SMB a který může použít k migraci databází.  Pro zajištění optimálního výkonu nahrávání souborů doporučujeme vybrat účet úložiště ve stejné oblasti, jako je služba DMS. |

    ![Konfigurace nastavení migrace](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Pokud služba migrace databáze Azure zobrazuje chybu "Systémová chyba 53" nebo "Systémová chyba 57", příčinou může být neschopnost služby Migrace databáze Azure pro přístup ke sdílené složce Azure. Pokud narazíte na některou z těchto chyb, udělte přístup k účtu úložiště z virtuální sítě pomocí pokynů [zde](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Pokud je povolena funkce kontroly zpětné smyčky a zdrojový sql server a sdílená složka jsou ve stejném počítači, pak zdroj nebude mít přístup k souborům zajícpomocí FQDN. Chcete-li tento problém vyřešit, zakažte funkci kontroly zpětné smyčky pomocí pokynů [zde](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

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

Po obnovení úplné zálohy databáze na cílové instanci spravované instance SQL Database je databáze k dispozici pro provedení přechodu migrace.

1. Jakmile budete připraveni dokončit online migraci databází, vyberte **Spustit přímou migraci**.

2. Zastavte veškerý příchozí provoz do zdrojových databází.

3. Proveďte [zálohování protokolu poškozené databáze], zpřístupněte soubor zálohy ve sdílené síťové složce SMB a pak počkejte na obnovení této konečné zálohy transakčních protokolů.

    V tuto chvíli se zobrazí hodnota **Probíhající změny** nastavená na 0.

4. Vyberte **Potvrdit** a pak **Použít**.

    ![Příprava na dokončení přímé migrace](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Když se zobrazí stav migrace databáze **Dokončeno**, připojte aplikace k nové cílové instanci spravované instance Azure SQL Database.

    ![Dokončení přímé migrace](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Další kroky

* Kurz, který ukazuje, jak migrovat databázi do spravované instance pomocí příkazu T-SQL RESTORE, naleznete v [tématu Obnovení zálohy na spravovanou instanci pomocí příkazu obnovení](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Informace o spravované instanci naleznete v [tématu Co je spravovaná instance](../sql-database/sql-database-managed-instance.md).
* Informace o připojení aplikací ke spravované instanci najdete v tématu [Připojení aplikací](../sql-database/sql-database-managed-instance-connect-app.md).
