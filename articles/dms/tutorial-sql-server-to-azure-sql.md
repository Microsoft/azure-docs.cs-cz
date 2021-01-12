---
title: 'Kurz: migrace SQL Server offline do jediné databáze SQL'
titleSuffix: Azure Database Migration Service
description: Naučte se migrovat z SQL Server Azure SQL Database offline pomocí Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: b1a732350c69d366458af6e388102e1f67395abf
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120569"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>Kurz: Offline migrace SQL Serveru do služby Azure SQL Database pomocí DMS

Pomocí Azure Database Migration Service můžete migrovat databáze z instance SQL Server na [Azure SQL Database](/azure/sql-database/). V tomto kurzu migrujete databázi [Adventureworks2016](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=ssms#download-backup-files) obnovenou do místní instance SQL Server 2016 (nebo novější) do izolované databáze nebo databáze ve fondu v Azure SQL Database pomocí Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> - Vyhodnoťte a vyhodnoťte místní databázi pro všechny blokující problémy pomocí Data Migration Assistant.
> - K migraci ukázkového schématu databáze použijte Data Migration Assistant. 
> - Zaregistrujte poskytovatele prostředků Azure datamigration.
> - Vytvořte instanci služby Azure Database Migration Service.
> - Vytvořte projekt migrace pomocí Azure Database Migration Service.
> - Spuštění migrace
> - Monitorujte migraci.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje offline migraci z SQL Server do databáze v Azure SQL Database. Informace o online migraci najdete v tématu [Online migrace SQL Serveru do služby Azure SQL Database pomocí DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

- Stáhněte a nainstalujte [SQL Server 2016 nebo novější](https://www.microsoft.com/sql-server/sql-server-downloads).
- Povolte protokol TCP/IP, který se ve výchozím nastavení zakáže během instalace SQL Serveru Express, a to podle pokynů v článku [Povolení nebo zakázání síťového protokolu serveru](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Vytvořte v Azure SQL Database databázi, kterou provedete podle podrobných informací uvedených v článku [Vytvoření databáze v Azure SQL Database pomocí Azure Portal](../azure-sql/database/single-database-create-quickstart.md). Pro účely tohoto kurzu se předpokládá, že je název služby Azure SQL Database **AdventureWorksAzure**, ale můžete zadat libovolný název.

    > [!NOTE]
    > Pokud používáte služba SSIS (SQL Server Integration Services) (SSIS) a chcete migrovat databázi katalogu pro projekty SSIS a balíčky (SSISDB) z SQL Server na Azure SQL Database, cílový SSISDB se při zřizování SSIS v Azure Data Factory (ADF) automaticky vytvoří a bude spravovat vaším jménem. Další informace o migraci balíčků SSIS najdete v článku migrace balíčků [služba SSIS (SQL Server Integration Services) do Azure](./how-to-migrate-ssis-packages.md).
  
- Stáhněte a nainstalujte nejnovější verzi [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který umožňuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](../virtual-network/index.yml)a zejména v článcích rychlý Start s podrobnými údaji.

    > [!NOTE]
    > Pokud při instalaci virtuální sítě používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, tyto [koncové body](../virtual-network/virtual-network-service-endpoints-overview.md) služby:
    >
    > - Koncový bod cílové databáze (například koncový bod SQL, Cosmos DB koncový bod atd.)
    > - Koncový bod úložiště
    > - Koncový bod služby Service Bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.
    >
    >Pokud nemáte připojení Site-to-site mezi místní sítí a Azure nebo pokud dojde k omezené šířce pásma připojení mezi lokalitami, zvažte použití Azure Database Migration Service v hybridním režimu (Preview). Hybridní režim využívá místní pracovní proces migrace společně s instancí Azure Database Migration Service spuštěnou v cloudu. Chcete-li vytvořit instanci Azure Database Migration Service v hybridním režimu, přečtěte si článek [vytvoření instance Azure Database Migration Service v hybridním režimu pomocí Azure Portal](./quickstart-create-data-migration-service-hybrid-portal.md).

- Zajistěte, aby odchozí pravidla zabezpečení skupiny zabezpečení sítě virtuální sítě neblokovala následující komunikační porty vyžadované pro Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další informace o filtrování provozu NSG ve službě Azure Virtual Network najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete bránu Windows Firewall, čímž povolíte Azure Database Migration Service přístup ke zdrojovému SQL Server, který je ve výchozím nastavení port TCP 1433. Pokud vaše výchozí instance naslouchá na nějakém jiném portu, přidejte ji do brány firewall.
- Pokud spouštíte více instancí s názvem SQL Server s použitím dynamických portů, možná budete chtít povolit službu SQL Browser a povolit přístup k portu UDP 1434 přes brány firewall, aby se Azure Database Migration Service mohl připojit k pojmenované instanci na zdrojovém serveru.
- Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla firewallu, která Azure Database Migration Service umožní přístup ke zdrojovým databázím pro migraci.
- Vytvořte [pravidlo brány firewall](../azure-sql/database/firewall-configure.md) protokolu IP na úrovni serveru pro Azure SQL Database, které povolí Azure Database Migration Service přístup k cílovým databázím. Zadejte rozsah podsítě virtuální sítě, která se používá pro Azure Database Migration Service.
- Ujistěte se, že přihlašovací údaje použité pro připojení ke zdrojové instanci SQL Serveru mají oprávnění [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Zajistěte, aby pověření používaná k připojení k cílové instanci Azure SQL Database měla oprávnění [řízení databáze](/sql/t-sql/statements/grant-database-permissions-transact-sql) pro cílové databáze.

## <a name="assess-your-on-premises-database"></a>Posouzení místní databáze

Než budete moct migrovat data z SQL Server instance do izolované databáze nebo databáze ve fondu v Azure SQL Database, musíte vyhodnotit SQL Server databázi pro všechny blokující problémy, které by mohly bránit migraci. Pomocí Data Migration Assistant postupujte podle kroků popsaných v článku [provedení vyhodnocení SQL Server migrace](/sql/dma/dma-assesssqlonprem) k dokončení posouzení místní databáze. Následuje souhrn požadovaných kroků:

1. V nástroji Data Migration Assistant vyberte ikonu Nový (+) a pak vyberte typ projektu **Posouzení**.
2. Zadejte název projektu. V rozevíracím seznamu **typ posouzení** vyberte možnost **databázový stroj**, v textovém poli **typ zdrojového serveru** vyberte možnost **SQL Server**, v textovém poli **typ cílového serveru** vyberte možnost **Azure SQL Database** a potom vyberte **vytvořit** a vytvořte projekt.

    Při posuzování zdrojové SQL Server databáze migrace do izolované databáze nebo databáze ve fondu v Azure SQL Database můžete zvolit jeden z následujících typů sestav hodnocení:

   - Kontrola kompatibility databáze
   - Kontrola parity funkcí

    Ve výchozím nastavení jsou vybrané oba typy sestavy.

3. V nástroji Data Migration Assistant na obrazovce **Možnosti** vyberte **Další**.
4. Na obrazovce **Vybrat zdroje** v dialogovém okně **Připojit k serveru** zadejte podrobnosti o připojení k vašemu SQL Serveru a pak vyberte **Připojit**.
5. V dialogovém okně **Přidat zdroje** vyberte možnost **Adventureworks2016**, vyberte možnost **Přidat** a potom vyberte možnost **Spustit posouzení**.

    > [!NOTE]
    > Pokud používáte SSIS, DMA aktuálně nepodporuje posouzení zdrojového SSISDB. Projekty nebo balíčky SSIS se ale posuzují nebo ověřují, protože se znovu nasazují do cílového SSISDB hostovaného v Azure SQL Database. Další informace o migraci balíčků SSIS najdete v článku migrace balíčků [služba SSIS (SQL Server Integration Services) do Azure](./how-to-migrate-ssis-packages.md).

    Po dokončení posouzení se zobrazí výsledky, jak je znázorněno na následujícím obrázku:

    ![Posouzení migrace dat](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Pro databáze v Azure SQL Database posouzení identifikují problémy s paritou funkcí a blokující problémy při nasazení do izolované databáze nebo databáze ve fondu.

    - Kategorie **Parita funkcí SQL Serveru** poskytuje komplexní sadu doporučení, alternativní postupy, které jsou v Azure k dispozici, a postupy pro zmírnění problémů, které vám pomůžou naplánovat náročnost projektů migrace.
    - Kategorie **problémy s kompatibilitou** identifikuje částečně podporované nebo nepodporované funkce, které odráží problémy s kompatibilitou, které by mohly blokovat migraci SQL Server databází do Azure SQL Database. K dispozici jsou také doporučení, která vám pomůžou tyto problémy vyřešit.

6. Výběrem konkrétních možností zkontrolujte výsledky posouzení z hlediska problémů blokujících migraci a problémů s paritou funkcí.

## <a name="migrate-the-sample-schema"></a>Migrace ukázkového schématu

Až budete spokojení s posouzením a spokojeni s tím, že vybraná databáze je vhodným kandidátem na migraci do izolované databáze nebo databáze ve fondu v Azure SQL Database, použijte k migraci schématu do Azure SQL Database možnost DMA.

> [!NOTE]
> Před vytvořením projektu migrace v Data Migration Assistant Ujistěte se, že jste už zřídili databázi v Azure, jak je uvedeno v části požadavky. 

> [!IMPORTANT]
> Pokud používáte SSIS, DMA v současné době nepodporuje migraci zdrojového SSISDB, ale můžete znovu nasadit své projekty a balíčky SSIS do cílového SSISDB hostovaného Azure SQL Database. Další informace o migraci balíčků SSIS najdete v článku migrace balíčků [služba SSIS (SQL Server Integration Services) do Azure](./how-to-migrate-ssis-packages.md).

Chcete-li migrovat schéma **Adventureworks2016** do izolované databáze nebo Azure SQL Database ve fondu, proveďte následující kroky:

1. V nástroji Data Migration Assistant vyberte ikonu Nový (+) a pak v části **Typ projektu** vyberte **Migrace**.
2. Zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server** a pak v textovém poli **Typ cílového serveru** vyberte **Azure SQL Database**.
3. V části **Rozsah migrace** vyberte **Pouze schéma**.

    Po provedení předchozích kroků by se mělo zobrazit rozhraní nástroje Data Migration Assistant, jak je znázorněno na následujícím obrázku:

    ![Vytvoření projektu nástroje Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Vyberte **Vytvořit** a vytvořte projekt.
5. V Data Migration Assistant zadejte podrobnosti o zdrojovém připojení pro vaši SQL Server, vyberte **připojit** a pak vyberte databázi **Adventureworks2016** .

    ![Podrobnosti o připojení ke zdroji v nástroji Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Vyberte **Další**, v části **připojit k cílovému serveru** zadejte podrobnosti o cílovém připojení pro Azure SQL Database, vyberte **připojit** a pak vyberte databázi **AdventureWorksAzure** , kterou jste předem zřídili v Azure SQL Database.

    ![Podrobnosti o připojení k cíli v nástroji Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Kliknutím na tlačítko **Další** přejdete na obrazovku **Vybrat objekty** , na které můžete zadat objekty schématu v databázi **Adventureworks2016** , která musí být nasazená do Azure SQL Database.

    Ve výchozím nastavení jsou vybrané všechny objekty.

    ![Generování skriptů SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Výběrem možnosti **Vygenerovat skript SQL** vytvořte skripty SQL a pak zkontrolujte, jestli skripty neobsahují chyby.

    ![Skript schématu](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Vyberte **Nasadit schéma** a nasaďte schéma do služby Azure SQL Database. Po nasazení schématu zkontrolujte případné anomálie na cílovém serveru.

    ![Nasazení schématu](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k portálu Azure. Vyhledejte a vyberte **odběry**.

   ![Zobrazení předplatných na portálu](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Vyhledejte migraci a pak vyberte **zaregistrovat** pro **migraci Microsoft.** data.

    ![Registrace poskytovatele prostředků](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Vytvoření instance

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**. Vyhledejte a vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. Na obrazovce základy **Vytvoření služby Migration Service** :

     - Vyberte předplatné.
     - Vytvořte novou skupinu prostředků nebo vyberte některou z existujících.
     - Zadejte název instance Azure Database Migration Service.
     - Vyberte umístění, ve kterém chcete vytvořit instanci Azure Database Migration Service.
     - Jako režim služby vyberte **Azure** .
     - Vyberte cenovou úroveň. Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení základy instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

     - Až skončíte, vyberte **Další: Sítě**.

4. Na obrazovce **vytvoření sítě služby Migration Service** :

    - Vyberte existující virtuální síť nebo vytvořte novou. Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojovému SQL Server a cílové instanci Azure SQL Database. Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

    ![Konfigurace nastavení sítě Azure Database Migration Service instance](media/tutorial-sql-server-to-azure-sql/dms-settings3.png)

    - Vyberte možnost **zkontrolovat + vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. V nabídce Azure Portal vyberte **všechny služby**. Vyhledejte a vyberte **služby Azure Database Migration Services**.

     ![Vyhledat všechny instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Na obrazovce **služby Azure Database Migration Services** vyberte instanci Azure Database Migration Service, kterou jste vytvořili.

3. Vyberte **Nový projekt migrace**.

     ![Vyhledání vaší instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **SQL Server**, v textovém poli **Typ cílového serveru** vyberte **Azure SQL Database** a pak v části **Zvolte typ aktivity** vyberte **Offline migrace dat**.

    ![Vytvoření projektu Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Vybrat zdroj** zadejte podrobnosti o připojení pro zdrojovou instanci SQL Server.

    Jako název zdrojové instance SQL Serveru nezapomeňte použít plně kvalifikovaný název domény. V situacích, kdy není možný překlad názvů DNS, můžete použít také IP adresu.

2. Pokud jste na svém zdrojovém serveru nenainstalovali důvěryhodný certifikát, zaškrtněte políčko **Důvěřovat certifikátu serveru**.

    Pokud není nainstalovaný důvěryhodný certifikát, SQL Server při spuštění instance vygeneruje certifikát podepsaný svým držitelem. Tento certifikát slouží k šifrování přihlašovacích údajů pro připojení klientů.

    > [!CAUTION]
    > Připojení TLS zašifrovaná pomocí certifikátu podepsaného svým držitelem neposkytují silné zabezpečení. Jsou náchylná na útoky, kdy se útočníci vydávají za prostředníky. Neměli byste spoléhat na protokol TLS pomocí certifikátů podepsaných držitelem v produkčním prostředí nebo na serverech, které jsou připojené k Internetu.

    > [!IMPORTANT]
    > Pokud používáte SSIS, služba DMS v současné době nepodporuje migraci zdrojového SSISDB, ale můžete znovu nasadit své projekty a balíčky SSIS do cílového SSISDB hostovaného Azure SQL Database. Další informace o migraci balíčků SSIS najdete v článku migrace balíčků [služba SSIS (SQL Server Integration Services) do Azure](./how-to-migrate-ssis-packages.md).

   ![Podrobnosti zdroje](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

3. Vyberte **Další: vybrat cíl**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **vybrat cíl** zadejte podrobnosti o připojení pro cílový Azure SQL Database, což je předem zřízené Azure SQL Database, na které bylo schéma **Adventureworks2016** nasazeno pomocí Data Migration Assistant.

    ![Výběr cíle](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Vyberte možnost **Další: namapovat na cílovou** databázi, namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    ![Mapování na cílové databáze](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Vyberte **Další: nastavení migrace konfigurace**, rozbalte seznam tabulka a pak zkontrolujte seznam ovlivněných polí.

    Azure Database Migration Service automaticky vybere všechny prázdné zdrojové tabulky, které existují na cílové Azure SQL Database instanci. Pokud chcete znovu migrovat tabulky, které již obsahují data, musíte tabulky explicitně vybrat v tomto okně.

    ![Výběr tabulek](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Vyberte **Další: Souhrn**, zkontrolujte konfiguraci migrace a v textovém poli **název aktivity** zadejte název aktivity migrace.

    ![Výběr možnosti ověřování](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

## <a name="run-the-migration"></a>Spuštění migrace

- Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Probíhající**.

    ![Stav aktivity](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

    ![Stav aktivity – Dokončeno](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Ověřte cílové databáze na cílovém **Azure SQL Database**.

### <a name="additional-resources"></a>Další zdroje informací

- Migrace SQL pomocí praktického testovacího prostředí [služby Azure Data Migration Service](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) .
- Informace o známých problémech a omezeních při provádění online migrací pro Azure SQL Database najdete v článku [známé problémy a řešení pro Azure SQL Database online migrace](known-issues-azure-sql-online.md).
- Informace o Azure Database Migration Service najdete v článku [co je Azure Database Migration Service?](./dms-overview.md).
- Informace o Azure SQL Database najdete v článku [co je služba Azure SQL Database?](../azure-sql/database/sql-database-paas-overview.md).