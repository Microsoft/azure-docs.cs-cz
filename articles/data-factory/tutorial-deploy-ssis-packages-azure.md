---
title: Zřízení prostředí Azure-SSIS Integration Runtime | Microsoft Docs
description: Zjistěte, jak zřídit prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory, abyste v Azure mohli nasazovat a spouštět balíčky SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bd2c055d2f7f1e90918cb160cfdebfe88f7f8ea4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484791"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Zřízení prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory

Tento kurz obsahuje postup pro zřízení Azure – integrační služby SSIS (SQL Server) Integration Runtime (IR) v Azure Data Factory (ADF) pomocí webu Azure portal. Prostředí Azure-SSIS IR podporuje spouštění balíčků, které jsou nasazené do katalogu služby SSIS (SSISDB) hostitelem serveru Azure SQL Database nebo spravované Instance (Model nasazení projektu) a nasazené do systémů soubor soubory sdílené složky Azure (Model nasazení balíčku). Po zřízení Azure-SSIS IR, pak můžete používat známé nástroje, jako je například SQL Server Data Tools (SSDT) nebo SQL Server Management Studio (SSMS) a příkazového řádku nástroje, jako například `dtinstall` / `dtutil` / `dtexec`do nasazení a spouštění balíčků služby v Azure. Informace o konceptu prostředí Azure-SSIS IR najdete v [přehledu prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Zřízení prostředí Azure-SSIS Integration Runtime

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- **Server Azure SQL Database (volitelné)** . Pokud ještě nemáte databázový server, vytvořte na webu Azure Portal před zahájením práce. ADF pak vytvoří SSISDB na tomto databázovém serveru. Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí integration runtime zapisovat protokoly spuštění do databáze SSISDB bez přecházení mezi oblastmi Azure. 
    - V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázový server pro hostování služby SSISDB, naleznete v tématu [porovnání Azure SQL Database jeden databáze nebo elastického fondu nebo spravované Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Pokud používáte server Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance ve virtuální síti k hostování databáze SSISDB nebo vyžadují přístup k místním datům, musíte připojit k prostředí Azure-SSIS IR k virtuální síti najdete v tématu [vytvořit prostředí Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Ujistěte se, že má databázový server povolené nastavení **Povolit přístup ke službám Azure**. Tento parametr nelze použít, pokud používáte server Azure SQL Database pomocí virtuální sítě služby koncové body nebo spravované Instance ve virtuální síti pro hostování služby SSISDB. Další informace najdete v tématu [Zabezpečení databáze Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Toto nastavení povolit pomocí prostředí PowerShell, najdete v článku [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Přidáte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače seznamu IP adres v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Můžete připojit k databázovému serveru pomocí ověřování SQL pomocí svých přihlašovacích údajů správce serveru nebo ověřování Azure Active Directory (AAD) pomocí spravované identity pro vaše ADF. U druhé možnosti je potřeba přidat spravovanou identitu služby ADF do skupiny AAD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Vytvoření prostředí Azure-SSIS IR s ověřováním AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Potvrďte, že databázový server nemá žádné SSISDB již. Zřízení prostředí Azure-SSIS IR nepodporuje použití existující databáze SSISDB.

> [!NOTE]
> - Seznam oblastí Azure, ve kterých jsou aktuálně k dispozici ADF a Azure-SSIS IR najdete v tématu [ADF + SSIS IR dostupnost podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome. 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). 
1. Vyberte **Nový** v nabídce vlevo, vyberte **Data a analýzy** a pak vyberte **Datová továrna**. 

   ![Výběr datové továrny v podokně Nový](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Na stránce **Nová datová továrna** zadejte do pole **Název** text **MyAzureSsisDataFactory**. 

   ![Stránka Nová datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Název datové továrny Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny (například na **&lt;vaše_jméno_&gt;MyAzureSsisDataFactory**) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md). 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
1. V části **Skupina prostředků** proveďte jeden z následujících kroků: 

   - Vyberte **Použít existující** a ze seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 

   Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md). 
1. Jako **Verze** vyberte **V2 (Preview)** . 
1. Jako **Umístění** vyberte umístění datové továrny. V seznamu se zobrazí pouze podporovaná umístění pro vytváření datových továren. 
1. Zaškrtněte **Připnout na řídicí panel**. 
1. Vyberte **Vytvořit**. 
1. Na řídicím panelu se zobrazí následující dlaždice se stavem **Nasazování datové továrny**: 

   ![Dlaždice Nasazování datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Po vytvoření se zobrazí stránka **Datová továrna**. 

   ![Domovská stránka datové továrny](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Výběrem dlaždice **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory. 

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Přehled služby Data Factory

1. Na stránce **Začínáme** vyberte dlaždici **Konfigurace prostředí SSIS Integration Runtime**. 

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Zbývající kroky nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Z uživatelského rozhraní pro vytváření obsahu

1. V uživatelském prostředí služby Azure Data Factory přepněte na kartu **Upravit**, vyberte **Připojení** a potom přepněte na kartu **Prostředí Integration Runtime**, kde se zobrazí existující prostředí Integration Runtime ve vaší datové továrně. 

   ![Výběr možností za účelem zobrazení stávajících prostředí IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Výběrem možnosti **Nové** vytvořte nové prostředí Azure-SSIS IR. 

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. V okně **Instalace prostředí Integration Runtime** vyberte možnost **Migrovat metodou „lift and shift“ existující balíčky služby SSIS ke spuštění v Azure** a pak vyberte tlačítko **Další**. 

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zbývající kroky nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure-SSIS Integration Runtime

1. Na stránce **Obecná nastavení** v okně **Instalace prostředí Integration Runtime** proveďte následující kroky: 

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Jako **Název** zadejte název vašeho prostředí Integration Runtime. 

   b. Jako **Popis** zadejte popis vašeho prostředí Integration Runtime. 

   c. Jako **Umístění** vyberte umístění vašeho prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění, jako má váš databázový server pro hostování databáze SSISDB. 

   d. Jako **Velikost uzlu** vyberte velikost uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované velikosti uzlů. Pokud chcete spouštět mnoho balíčků náročných na výpočetní výkon nebo paměť, vyberte velkou velikost uzlu (vertikálně navyšte kapacitu). 

   e. Jako **Počet uzlů** vyberte počet uzlů ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty uzlů. Pokud chcete paralelně spouštět mnoho balíčků, vyberte velký cluster s mnoha uzly (horizontálně navyšte kapacitu). 

   f. Pro **Edition nebo licencí**, vyberte edici systému SQL Server/licenci pro prostředí integration runtime: Standard nebo Enterprise. Vyberte Enterprise, pokud ve svém prostředí Integration Runtime chcete využít pokročilé nebo prémiové funkce. 

   g. Pro **uložit peníze**, vyberte možnost Azure Hybrid Benefit (AHB) pro prostředí integration runtime: Ano nebo ne. Vyberte Ano, pokud chcete použít vlastní licenci SQL Serveru se Software Assurance a využít tak úspory nákladů spojené s hybridním využitím. 

   h. Klikněte na **Další**. 

1. Na stránce **Nastavení SQL** proveďte následující kroky: 

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Na **katalogu služby SSIS vytvořit...**  zaškrtávací políčko, vyberte model nasazení pro balíčky ke spuštění v prostředí Azure-SSIS IR: Model nasazení projektu, ve které jsou nasazené balíčky do databází SSISDB hostovanou na serveru databáze nebo v modelu nasazení balíčku, ve které jsou nasazené balíčky do souboru systému/souboru sdílené složky/Azure soubory. Pokud ho budete kontrolovat, je potřeba přenést databázový server pro hostování služby SSISDB, vytvoříme ji a budeme spravovat za vás.
   
   b. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

   c. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime. 

   d. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázový server pro hostování služby SSISDB, naleznete v tématu [porovnání Azure SQL Database jeden databáze nebo elastického fondu nebo spravované Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Pokud vyberete serveru Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance ve virtuální síti k hostování databáze SSISDB nebo vyžadují přístup k místním datům, je potřeba připojit k prostředí Azure-SSIS IR k virtuální síti, najdete v článku [vytvořit prostředí Azure SSIS Prostředí IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Na **ověřování pomocí AAD...**  zaškrtávací políčko, vyberte metodu ověřování pro databázový server pro hostování služby SSISDB: Ověřování SQL nebo ověřování AAD pomocí spravované identity pro vaše ADF. Pokud ho budete kontrolovat, je potřeba přidat spravovanou identitu pro vaši ADF do skupiny AAD pomocí oprávnění pro přístup k vašemu databázovému serveru, najdete v článku [vytvořit prostředí Azure-SSIS IR s ověřováním AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. Jako **Uživatelské jméno správce** zadejte uživatelské jméno ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

   g. Jako **Heslo správce** zadejte heslo ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

   h. Pro **úroveň služeb databáze katalogu**, vyberte úroveň služby pro databázový server pro hostování služby SSISDB: Úroveň Basic, Standard nebo Premium nebo název elastického fondu. 

   i. Klikněte na **Test připojení**, a pokud proběhne úspěšně, klikněte na **Další**. 

1. Na stránce **Upřesnit nastavení** proveďte následující kroky: 

   ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Jako **Maximální počet paralelních zpracování na uzel** vyberte maximální počet balíčků, které se můžou paralelně spustit v jednom uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty balíčků. Pokud chcete použít více než jedno jádro ke spuštění jednoho velkého balíčku náročného na výpočetní výkon nebo paměť, vyberte nízké číslo. Pokud chcete spustit jeden nebo více malých a méně náročných balíčků v jednom jádru, vyberte vysoké číslo. 

   b. Jako **Identifikátor URI SAS kontejneru vlastního nastavení** volitelně zadejte identifikátor URI sdíleného přístupového podpisu (SAS) vašeho kontejneru Azure Storage Blob, ve kterém je uložený váš instalační skript a související soubory. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Pomocí zaškrtávacího políčka **Vybrat virtuální síť...** vyberte, jestli chcete své prostředí Integration Runtime připojit k virtuální síti. Měli byste zkontrolovat, je-li použít server Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance ve virtuální síti pro hostování služby SSISDB, nebo vyžadují přístup k místním datům, najdete v článku [vytvořit prostředí Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Kliknutím na **Dokončit** zahajte vytváření vašeho prostředí Integration Runtime. 

   > [!NOTE]
   > S výjimkou kdykoli vlastní nastavení tento proces by se měl dokončit během 5 minut.
   >
   > Pokud používáte službu SSISDB, služba ADF se připojí k vašemu databázovému serveru Příprava databáze SSISDB. 
   > 
   > Když si zřídíte Azure-SSIS IR, jsou nainstalovány také Azure Feature Pack for SSIS a Access Redistributable. Tyto komponenty nabízejí připojení k souborům aplikace Excel a Access a různým datovým zdrojům Azure, spolu se zdroji dat, již podporuje integrované komponenty. Můžete taky nainstalovat další komponenty, naleznete v tématu [vlastní nastavení pro prostředí Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

   ![Stav vytváření s tlačítkem Aktualizovat](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Pomocí odkazů ve sloupci **Akce** můžete prostředí Integration Runtime zastavit nebo spustit, upravit nebo odstranit. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené. 

   ![Odkazy ve sloupci Akce](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte službu SSISDB, můžete nasadit své balíčky do ní a spouštět v prostředí Azure-SSIS IR pomocí Nástroje SSDT/SSMS, připojující se ke svému databázovému serveru prostřednictvím její koncový bod serveru. Pro server/spravované instance Azure SQL Database s veřejným koncovým bodem, formát koncový bod serveru je `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`v uvedeném pořadí. Pokud nepoužijete SSISDB, můžete nasadit své balíčky do souboru systémy nebo k souboru sdílené složky Azure soubory a spouštět v prostředí Azure-SSIS IR pomocí `dtinstall` / `dtutil` / `dtexec` nástroje příkazového řádku. Další informace najdete v tématu [balíčků služby SSIS nasazovat](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). V obou případech se může také spouštění balíčků služby nasazené v prostředí Azure-SSIS IR pomocí aktivity spustit balíčků služby SSIS v kanálech ADF, naleznete v tématu [spouštění jako první třídy aktivity ADF balíčků služby SSIS vyvolat](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

Viz také následující články v dokumentaci ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčků služby SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Připojit do databáze SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Plánování spouštění balíčku v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Zřízení prostředí Azure-SSIS Integration Runtime
> * Nasazení balíčků SSIS

Informace o přizpůsobení prostředí Azure-SSIS Integration Runtime najdete v následujícím článku: 

> [!div class="nextstepaction"]
> [Přizpůsobení prostředí Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)