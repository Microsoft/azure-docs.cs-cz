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
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b3b180fe4b465f3e0c7de888043326fd1a43cf23
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009654"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Zřízení prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory

V tomto kurzu najdete postup, jak pomocí Azure Portal zřídit Integration Runtime (IR) služba SSIS (SQL Server Integration Services) (SSIS) v Azure Data Factory (ADF). Azure-SSIS IR podporuje spouštění balíčků nasazených do katalogu SSIS (SSISDB) hostovaných pomocí Azure SQL Database serveru/spravované instance (model nasazení projektu) a ty, které jsou nasazené do systémů souborů/sdílené složky/soubory Azure (model nasazení balíčku). Po zřízení Azure-SSIS IR pak můžete použít známé nástroje, jako je například nástroj SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) `dtinstall`a nástroje příkazového řádku, / `dtutil` / `dtexec`jako je například, na Nasaďte a spusťte balíčky v Azure. Informace o konceptu prostředí Azure-SSIS IR najdete v [přehledu prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Zřízení prostředí Azure-SSIS Integration Runtime

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- **Server Azure SQL Database (volitelné)** . Pokud ještě nemáte databázový server, vytvořte ho v Azure Portal před tím, než začnete. ADF pak na tomto databázovém serveru vytvoří SSISDB. Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration runtime zapisovat protokoly spouštění do SSISDB bez přechodu do oblastí Azure. 
    - V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB naleznete v tématu [Compare Azure SQL Database Single Database/elastický fond/Managed instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Pokud používáte Azure SQL Database Server s koncovými body služby virtuální sítě/spravovanou instancí s privátním koncovým bodem pro hostování SSISDB nebo vyžadujete přístup k místním datům, aniž byste museli konfigurovat místní prostředí IR, musíte se připojit k Azure-SSIS IR k virtuální síti, viz [vytvoření Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Ujistěte se, že má databázový server povolené nastavení **Povolit přístup ke službám Azure**. Tato možnost se nevztahuje na použití serveru Azure SQL Database s koncovými body služby virtuální sítě/spravovanou instancí s privátním koncovým bodem pro hostování SSISDB. Další informace najdete v tématu [Zabezpečení databáze Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si článek [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Přidejte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače, do seznamu IP adres klienta v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - K databázovému serveru se můžete připojit pomocí ověřování SQL s přihlašovacími údaji správce serveru nebo pomocí ověřování Azure Active Directory (AAD) se spravovanou identitou pro ADF. U druhé možnosti je potřeba přidat spravovanou identitu služby ADF do skupiny AAD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Vytvoření prostředí Azure-SSIS IR s ověřováním AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Ověřte, že váš databázový server ještě nemá SSISDB. Zřizování Azure-SSIS IR nepodporuje používání existující SSISDB.

> [!NOTE]
> - Seznam oblastí Azure, ve kterých jsou v současnosti k dispozici ADF a Azure-SSIS IR, najdete v článku [o dostupnosti ADF + SSIS IR v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

Pokud chcete vytvořit ADF prostřednictvím Azure Portal, postupujte podle podrobných pokynů v tématu [Vytvoření ADF pomocí uživatelského rozhraní](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) a vyberte **Připnout na řídicí panel** , abyste po jeho vytvoření povolili rychlý přístup. 

Po vytvoření ADF otevřete jeho stránku Přehled na Azure Portal a kliknutím na dlaždici pro **monitorování autora &** na samostatné kartě spusťte svou **úvodní** stránku, kde můžete pokračovat v vytváření Azure-SSIS IR.   

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Z přehledu Data Factory

1. Na stránce **Začínáme** klikněte na dlaždici **Konfigurovat SSIS Integration runtime** . 

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

   f. V případě **edice nebo licence**vyberte SQL Server Edition/licence pro prostředí Integration Runtime: Standard nebo Enterprise. Vyberte Enterprise, pokud ve svém prostředí Integration Runtime chcete využít pokročilé nebo prémiové funkce. 

   g. Pro možnost **Uložit peníze**vyberte možnost zvýhodněné hybridní využití Azure (AHB) pro prostředí Integration Runtime: Ano nebo ne. Vyberte Ano, pokud chcete použít vlastní licenci SQL Serveru se Software Assurance a využít tak úspory nákladů spojené s hybridním využitím. 

   h. Klikněte na **Další**. 

1. Na stránce **Nastavení SQL** proveďte následující kroky: 

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. V části **vytvořit katalog SSIS...** vyberte model nasazení, ve kterém se mají balíčky spouštět v Azure-SSIS IR: Model nasazení projektu, kde jsou balíčky nasazeny do SSISDB hostovaného databázovým serverem nebo modelem nasazení balíčku, kde jsou balíčky nasazeny do vašich systémů souborů/sdílených složek nebo souborů Azure. Pokud ho zkontrolujete, budete muset přenést svůj vlastní databázový server do hostitele SSISDB, který vytvoříme a spravujeme vaším jménem.
   
   b. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

   c. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime.

   d. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB naleznete v tématu [Compare Azure SQL Database Single Database/elastický fond/Managed instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Pokud vyberete Azure SQL Database Server s použitím koncových bodů služby virtuální sítě/spravované instance s privátním koncovým bodem pro hostování SSISDB nebo vyžadujete přístup k místním datům, aniž byste museli nakonfigurovat prostředí IR v místním prostředí, musíte se připojit k Azure-SSIS IR k virtuální síti. , přečtěte si téma [vytvoření Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. V části **použít ověřování AAD...** vyberte metodu ověřování pro databázový server pro hostování SSISDB: Ověřování SQL nebo ověřování AAD se spravovanou identitou pro váš ADF. Pokud ho zkontrolujete, budete muset přidat spravovanou identitu pro svůj ADF do skupiny AAD s přístupovými oprávněními k vašemu databázovému serveru. Další informace najdete v tématu věnovaném [vytvoření Azure-SSIS IR s ověřováním AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. Jako **Uživatelské jméno správce** zadejte uživatelské jméno ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

   g. Jako **Heslo správce** zadejte heslo ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

   h. V poli **úroveň služby databáze katalogu**vyberte úroveň služby pro váš databázový server pro hostování SSISDB: Úroveň Basic/Standard/Premium nebo název elastického fondu.

   i. Klikněte na **Test připojení**, a pokud proběhne úspěšně, klikněte na **Další**. 

1. Na stránce **Upřesnit nastavení** proveďte následující kroky: 

   ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Jako **Maximální počet paralelních zpracování na uzel** vyberte maximální počet balíčků, které se můžou paralelně spustit v jednom uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty balíčků. Pokud chcete použít více než jedno jádro ke spuštění jednoho velkého balíčku náročného na výpočetní výkon nebo paměť, vyberte nízké číslo. Pokud chcete spustit jeden nebo více malých a méně náročných balíčků v jednom jádru, vyberte vysoké číslo. 

   b. Jako **Identifikátor URI SAS kontejneru vlastního nastavení** volitelně zadejte identifikátor URI sdíleného přístupového podpisu (SAS) vašeho kontejneru Azure Storage Blob, ve kterém je uložený váš instalační skript a související soubory. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Pomocí zaškrtávacího políčka **Vybrat virtuální síť...** vyberte, jestli chcete své prostředí Integration Runtime připojit k virtuální síti. Měli byste je kontrolovat, pokud používáte Azure SQL Database Server s koncovými body služby virtuální sítě/spravovanou instancí s privátním koncovým bodem pro hostování SSISDB nebo vyžadovat přístup k místním datům bez konfigurace místního prostředí IR. Další informace najdete [v tématu vytvoření Azure-SSIS IR v virtuální síť](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. V nastavení v místním prostředí **...** vyberte, jestli chcete nakonfigurovat prostředí IR v místním prostředí jako proxy pro váš Azure-SSIS IR. Další informace najdete v tématu [nastavení prostředí IR pro místní hostování jako proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

1. Klikněte na **Další**. 

1. Na stránce **Souhrn** Zkontrolujte všechna nastavení zřizování, zajistěte záložku doporučené odkazy na dokumentaci a kliknutím na tlačítko **Dokončit** spusťte vytváření prostředí Integration runtime. 

   > [!NOTE]
   > S výjimkou času vlastní instalace by tento proces měl být dokončen do 5 minut.
   >
   > Pokud používáte SSISDB, služba ADF se připojí k vašemu databázovému serveru a připraví SSISDB. 
   > 
   > Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují kromě zdrojů dat, které už jsou podporované integrovanými součástmi, možnosti připojení k souborům Excelu a Accessu a různým zdrojům dat Azure. Můžete také nainstalovat další součásti, viz [vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

   ![Stav vytváření s tlačítkem Aktualizovat](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Pomocí odkazů ve sloupci **Akce** můžete prostředí Integration Runtime zastavit nebo spustit, upravit nebo odstranit. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené. 

   ![Odkazy ve sloupci Akce](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte SSISDB, můžete do něj nasadit balíčky a spouštět je v Azure-SSIS IR pomocí nástrojů SSDT/SSMS, které se připojují k vašemu databázovému serveru prostřednictvím koncového bodu serveru. U Azure SQL Database serveru/spravované instance s veřejným koncovým bodem je `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`formát koncového bodu serveru v uvedeném pořadí. Pokud SSISDB nepoužíváte, můžete balíčky nasadit do systémů souborů/sdílených složek nebo souborů Azure a spouštět je v `dtinstall` Azure-SSIS IR pomocí / `dtutil` / `dtexec` nástrojů příkazového řádku. Další informace najdete v tématu [nasazení balíčků SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). V obou případech můžete také spouštět nasazené balíčky na Azure-SSIS IR pomocí aktivity spustit balíček SSIS v kanálech ADF, viz téma [vyvolání spuštění balíčku SSIS jako aktivity první třídy ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

Viz také následující články z dokumentace ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Plánování spouštění balíčků v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
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