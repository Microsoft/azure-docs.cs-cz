---
title: 'Kurz: Migrace serveru SQL Server offline do jediné databáze SQL'
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat z místního SQL Serveru do jedné databáze nebo ve sloučené databázi v Azure SQL Database offline pomocí služby Azure Database Migration Service.
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
ms.openlocfilehash: ff47246482bd0712ea4e741d44b12f2c6767380b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298919"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Kurz: Migrace SQL Serveru do jedné databáze nebo databáze s dražeb v Azure SQL Database offline pomocí DMS

Službu migrace databáze Azure můžete použít k migraci databází z místní instance SERVERU SQL Server do [azure sql database](https://docs.microsoft.com/azure/sql-database/). V tomto kurzu migrujete databázi **Adventureworks2012** obnovenou do místní instance SQL Serveru 2016 (nebo novější) do jedné databáze nebo sdružené databáze v Azure SQL Database pomocí služby Migrace databáze Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> - Posouzení místní databáze pomocí nástroje Data Migration Assistant
> - Migrace ukázkového schématu pomocí nástroje Data Migration Assistant
> - Vytvořte instanci služby Azure Database Migration Service.
> - Vytvořte projekt migrace pomocí služby Migrace databáze Azure.
> - Spuštění migrace
> - Monitorování migrace
> - Stažení sestavy migrace

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje offline migraci z SQL Serveru do jedné databáze nebo sdružené databáze v Azure SQL Database. Informace o online migraci najdete v tématu [Online migrace SQL Serveru do služby Azure SQL Database pomocí DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

- Stáhněte a nainstalujte [SQL Server 2016 nebo novější](https://www.microsoft.com/sql-server/sql-server-downloads).
- Povolte protokol TCP/IP, který se ve výchozím nastavení zakáže během instalace SQL Serveru Express, a to podle pokynů v článku [Povolení nebo zakázání síťového protokolu serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Vytvořte jednu (nebo sdruženou) databázi v Azure SQL Database, což uděláte podle podrobností v článku [Vytvoření jedné databáze v Azure SQL Database pomocí portálu Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Pokud používáte služby SQL Server Integration Services (SSIS) a chcete migrovat databázi katalogu pro vaše projekty/balíčky SSIS (SSISDB) z SQL Serveru do databáze Azure SQL, bude cílový SSISDB vytvořen a spravován automaticky vaším jménem při zřizování SSIS v Azure Data Factory (ADF). Další informace o migraci balíčků SSIS naleznete v článku [Migrace balíčků služby SQL Server Integration Services do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).
  
- Stáhněte a nainstalujte nástroj [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) verze 3.3 nebo novější.
- Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace o vytvoření virtuální sítě naleznete v [dokumentaci k virtuální síti](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlého startu s podrobnými podrobnostmi.

    > [!NOTE]
    > Pokud během instalace virtuální sítě používáte ExpressRoute s partnerským vztahem k síti společnosti Microsoft, přidejte do podsítě, ve které bude služba zřízena, následující [koncové body služby:](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
    >
    > - Cílový koncový bod databáze (například koncový bod SQL, koncový bod Cosmos DB a tak dále)
    > - Koncový bod úložiště
    > - Koncový bod sběrnice
    >
    > Tato konfigurace je nezbytná, protože služba Azure Database Migration Service nemá připojení k internetu.
    >
    >Pokud nemáte připojení mezi lokalitou k webu mezi místní sítí a Azure nebo pokud je omezená šířka pásma připojení mezi lokalitami, zvažte použití služby Migrace databáze Azure v hybridním režimu (preview). Hybridní režim využívá místního pracovníka migrace spolu s instancí služby Migrace databáze Azure spuštěnou v cloudu. Pokud chcete vytvořit instanci služby Migrace databáze Azure v hybridním režimu, přečtěte si článek [Vytvoření instance služby Migrace databáze Azure v hybridním režimu pomocí portálu Azure](https://aka.ms/dms-hybrid-create).

- Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě neblokují následující příchozí komunikační porty služby Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu sítě NSG ve virtuální síti Azure najdete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete bránu firewall systému Windows, abyste povolili službě Migrace databáze Azure přístup ke zdrojovému serveru SQL Server, který je ve výchozím nastavení portem TCP 1433.
- Pokud používáte více pojmenovaných instancí SQL Serveru pomocí dynamických portů, můžete povolit službu prohlížeče SQL a povolit přístup k portu UDP 1434 prostřednictvím bran firewall, aby se služba Migrace databáze Azure mohla připojit k pojmenované instanci ve zdroji. Server.
- Při použití zařízení brány firewall před zdrojovou databází, budete muset přidat pravidla brány firewall povolit Azure Database Migration Service pro přístup ke zdrojové databáze pro migraci.
- Vytvořte [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) IP na úrovni serveru pro server Azure SQL Database, které umožní přístup služby Azure Database Migration Service k cílovým databázím. Zadejte rozsah podsítí virtuální sítě používané pro službu Migrace databáze Azure.
- Ujistěte se, že přihlašovací údaje použité pro připojení ke zdrojové instanci SQL Serveru mají oprávnění [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Ujistěte se, že přihlašovací údaje použité pro připojení k cílové instanci služby Azure SQL Database mají oprávnění CONTROL DATABASE k cílovým databázím Azure SQL.

## <a name="assess-your-on-premises-database"></a>Posouzení místní databáze

Než budete moct migrovat data z místní instance SERVERU SQL Server do jedné databáze nebo sdružené databáze v Azure SQL Database, je třeba vyhodnotit databázi SQL Server pro všechny problémy s blokováním, které by mohly zabránit migraci. Proveďte posouzení místní databáze pomocí nástroje Data Migration Assistant verze 3.3 nebo novější a postupujte při tom podle kroků popsaných v článku [Posuzování migrace SQL Serveru](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem). Následuje souhrn požadovaných kroků:

1. V nástroji Data Migration Assistant vyberte ikonu Nový (+) a pak vyberte typ projektu **Posouzení**.
2. Zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server**, v textovém poli **Typ cílového serveru** vyberte **Azure SQL Database** a pak vyberte **Vytvořit** a vytvořte projekt.

    Při posuzování zdrojové databáze SQL Serveru migrující do jedné databáze nebo sdružené databáze v Azure SQL Database můžete zvolit jeden nebo oba následující typy sestav hodnocení:

   - Kontrola kompatibility databáze
   - Kontrola parity funkcí

    Ve výchozím nastavení jsou vybrané oba typy sestavy.

3. V nástroji Data Migration Assistant na obrazovce **Možnosti** vyberte **Další**.
4. Na obrazovce **Vybrat zdroje** v dialogovém okně **Připojit k serveru** zadejte podrobnosti o připojení k vašemu SQL Serveru a pak vyberte **Připojit**.
5. V dialogovém okně **Přidat zdroje** vyberte **AdventureWorks2012**, pak **Přidat** a pak vyberte **Spustit posouzení**.

    > [!NOTE]
    > Pokud používáte SSIS, DMA v současné době nepodporuje posouzení zdrojové SSISDB. Projekty/balíčky SSIS se však vyhodnotí nebo ověří, jakmile budou znovu nasazeny do cílové databáze SSISDB hostované službou Azure SQL Database. Další informace o migraci balíčků SSIS naleznete v článku [Migrace balíčků služby SQL Server Integration Services do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Po dokončení posouzení se zobrazí výsledky, jak je znázorněno na následujícím obrázku:

    ![Posouzení migrace dat](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    U jednotlivých databází nebo sdružených databází v Azure SQL Database identifikují hodnocení problémy s paritou funkcí a problémy s blokováním migrace pro nasazení do jedné databáze nebo sdružené databáze.

    - Kategorie **Parita funkcí SQL Serveru** poskytuje komplexní sadu doporučení, alternativní postupy, které jsou v Azure k dispozici, a postupy pro zmírnění problémů, které vám pomůžou naplánovat náročnost projektů migrace.
    - Kategorie **Problémy s kompatibilitou** identifikuje částečně podporované nebo nepodporované funkce, které odrážejí problémy, které můžou blokovat migraci místních databází SQL Serveru do služby Azure SQL Database. K dispozici jsou také doporučení, která vám pomůžou tyto problémy vyřešit.

6. Výběrem konkrétních možností zkontrolujte výsledky posouzení z hlediska problémů blokujících migraci a problémů s paritou funkcí.

## <a name="migrate-the-sample-schema"></a>Migrace ukázkového schématu

Až budete spokojeni s hodnocením a uspokojeni, že vybraná databáze je životaschopným kandidátem pro migraci do jedné databáze nebo sdružené databáze v Azure SQL Database, použijte DMA k migraci schématu do Azure SQL Database.

> [!NOTE]
> Před vytvořením projektu migrace v nástroji Data Migration Assistant se ujistěte, že už máte zřízenou databázi Azure SQL, jak je uvedeno v požadavcích. Pro účely tohoto kurzu se předpokládá, že je název služby Azure SQL Database **AdventureWorksAzure**, ale můžete zadat libovolný název.

> [!IMPORTANT]
> Pokud používáte SSIS, DMA aktuálně nepodporuje migraci zdrojového SSISDB, ale můžete znovu nasadit projekty nebo balíčky SSIS do cílové SSISDB hostované službou Azure SQL Database. Další informace o migraci balíčků SSIS naleznete v článku [Migrace balíčků služby SQL Server Integration Services do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Chcete-li migrovat schéma **AdventureWorks2012** do jedné databáze nebo do databáze Azure SQL Database s drahou refondovou databází, proveďte následující kroky:

1. V nástroji Data Migration Assistant vyberte ikonu Nový (+) a pak v části **Typ projektu** vyberte **Migrace**.
2. Zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server** a pak v textovém poli **Typ cílového serveru** vyberte **Azure SQL Database**.
3. V části **Rozsah migrace** vyberte **Pouze schéma**.

    Po provedení předchozích kroků by se mělo zobrazit rozhraní nástroje Data Migration Assistant, jak je znázorněno na následujícím obrázku:

    ![Vytvoření projektu nástroje Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Vyberte **Vytvořit** a vytvořte projekt.
5. V nástroji Data Migration Assistant zadejte podrobnosti o připojení ke zdrojovému SQL Serveru, vyberte **Připojit** a pak vyberte databázi **AdventureWorks2012**.

    ![Podrobnosti o připojení ke zdroji v nástroji Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Vyberte **Další**, v části **Připojit k cílovému serveru**zadejte podrobnosti o cílovém připojení pro Azure SQL Database, vyberte **Připojit**a pak vyberte databázi **AdventureWorksAzure,** kterou jste předem zřídili v Azure SQL Database.

    ![Podrobnosti o připojení k cíli v nástroji Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Vyberte **Další** a přejděte na obrazovku **Vybrat objekty**, na které můžete určit objekty schématu v databázi **AdventureWorks2012**, které je potřeba nasadit do služby Azure SQL Database.

    Ve výchozím nastavení jsou vybrané všechny objekty.

    ![Generování skriptů SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Výběrem možnosti **Vygenerovat skript SQL** vytvořte skripty SQL a pak zkontrolujte, jestli skripty neobsahují chyby.

    ![Skript schématu](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Vyberte **Nasadit schéma** a nasaďte schéma do služby Azure SQL Database. Po nasazení schématu zkontrolujte případné anomálie na cílovém serveru.

    ![Nasazení schématu](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k portálu Azure. Vyhledejte a vyberte **předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Migrace databáze Azure, a pak vyberte **zprostředkovatele prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Vyhledejte migraci a pak vyberte **Registrovat** **microsoft.datamigration**.

    ![Registrace poskytovatele prostředků](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance

1. V nabídce Portál Azure nebo na **domovské** stránce vyberte **Vytvořit prostředek**. Vyhledejte a vyberte **službu Migrace databáze Azure**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Migrace databáze Azure.

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje službě Migrace databáze Azure přístup ke zdrojovému serveru SQL Server a cílové instanci Azure SQL Database.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

6. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. V nabídce Portál Azure vyberte **Všechny služby**. Vyhledejte a vyberte **službu Azure Database Migration Services**.

     ![Vyhledání všech instancí služby Migrace databáze Azure](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Na obrazovce **Azure Database Migration Services** vyberte instanci služby Migrace databáze Azure, kterou jste vytvořili.

3. Vyberte **Nový projekt migrace**.

     ![Vyhledání instance služby Migrace databáze Azure](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server**, v textovém poli **Typ cílového serveru** vyberte **Azure SQL Database** a pak v části **Zvolte typ aktivity** vyberte **Offline migrace dat**.

    ![Vytvoření projektu Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Podrobnosti zdroje migrace** zadejte podrobnosti o připojení ke zdrojové instanci SQL Serveru.

    Jako název zdrojové instance SQL Serveru nezapomeňte použít plně kvalifikovaný název domény. V situacích, kdy není možný překlad názvů DNS, můžete použít také IP adresu.

2. Pokud jste na svém zdrojovém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení TLS, která jsou šifrována pomocí certifikátu podepsaného svým držitelem, neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Na protokol TLS byste neměli spoléhat pomocí certifikátů podepsaných svým držitelem v produkčním prostředí nebo na serverech, které jsou připojeny k Internetu.

   ![Podrobnosti zdroje](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Pokud používáte SSIS, DMS aktuálně nepodporuje migraci zdrojového SSISDB, ale můžete znovu nasadit projekty nebo balíčky SSIS do cílové HosisDB hostované službou Azure SQL Database. Další informace o migraci balíčků SSIS naleznete v článku [Migrace balíčků služby SQL Server Integration Services do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit** a pak na obrazovce **Podrobnosti cíle migrace** zadejte podrobnosti o připojení k cílovému serveru služby Azure SQL Database. Tím je předem zřízená služba Azure SQL Database, do které se pomocí nástroje Data Migration Assistant nasadilo schéma **AdventureWorks2012**.

    ![Výběr cíle](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Migrace databáze Azure vybere cílovou databázi ve výchozím nastavení.

    ![Mapování na cílové databáze](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Vyberte **Uložit**, na obrazovce **Vybrat tabulky** rozbalte seznam tabulek a zkontrolujte seznam ovlivněných polí.

    Služba Migrace databáze Azure automaticky vybere všechny prázdné zdrojové tabulky, které existují v cílové instanci Azure SQL Database. Pokud chcete znovu migrovat tabulky, které již obsahují data, musíte tabulky explicitně vybrat v tomto okně.

    ![Výběr tabulek](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Vyberte **Uložit** a na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace.

5. Rozbalením části **Možnost ověřování** zobrazte obrazovku **Vybrat možnost ověřování** a pak určete, jestli chcete u migrovaných databází ověřovat **Porovnání schématu**, **Konzistenci dat** nebo **Správnost dotazu**.

    ![Výběr možnosti ověřování](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. Vyberte **Uložit**, zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>Spuštění migrace

- Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Probíhající**.

    ![Stav aktivity](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

    ![Stav aktivity – Dokončeno](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Po dokončení migrace vyberte **Stáhnout sestavu** a získejte sestavu s výpisem podrobností souvisejících s procesem migrace.

3. Zkontrolujte cílové databáze na cílovém serveru služby Azure SQL Database.

### <a name="additional-resources"></a>Další zdroje

- Migrace SQL pomocí praktického testovacího prostředí [Služby migrace dat Azure.](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587)
- Informace o známých problémech a omezeních při provádění online migrací do Azure SQL Database najdete v článku [Známé problémy a řešení online migrací Azure SQL Database](known-issues-azure-sql-online.md).
- Informace o službě Migrace databáze Azure najdete v článku [Co je služba migrace databáze Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
- Informace o Azure SQL Database najdete v článku [Co je služba Azure SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
