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
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 58cd3882eab41934135670f6f99faf3d834c74d7
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43108964"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Zřízení prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory
Tento kurz obsahuje postup pro zřízení prostředí Azure-SSIS Integration Runtime (IR) ve službě Azure Data Factory pomocí webu Azure Portal. Následně můžete pomocí SQL Server Data Tools nebo aplikace SQL Server Management Studio v tomto modulu runtime v Azure nasazovat a spouštět balíčky SSIS (SQL Server Integration Services). Informace o konceptu prostředí Azure-SSIS IR najdete v [přehledu prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Zřízení prostředí Azure-SSIS Integration Runtime

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 
- **Server služby Azure SQL Database**. Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Azure Data Factory na tomto databázovém serveru vytvoří katalog služby SSIS (databázi SSISDB). Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration Runtime zapisovat do databáze SSISDB protokoly spuštění bez přecházení mezi oblastmi Azure. 
- V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednu databázi, součást elastického fondu nebo v MI (Preview) a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokud k hostování databáze SSISDB používáte službu Azure SQL Database s koncovými body služby virtuální sítě nebo MI (Preview) nebo pokud vyžadujete přístup k místním datům, musíte prostředí Azure-SSIS IR připojit k virtuální síti. Další informace najdete v tématu [Vytvoření prostředí Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
- Ujistěte se, že má databázový server povolené nastavení **Povolit přístup ke službám Azure**. Toto nastavení neplatí, pokud k hostování databáze SSISDB používáte službu Azure SQL Database s koncovými body služby virtuální sítě nebo MI (Preview). Další informace najdete v tématu [Zabezpečení databáze SQL Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si téma věnované rutině [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1). 
- Přidejte do seznamu IP adres v nastavení brány firewall databázového serveru IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md). 
- K databázovému serveru se můžete připojit prostřednictvím ověřování SQL s použitím přihlašovacích údajů správce serveru nebo prostřednictvím ověřování Azure Active Directory (AAD) s použitím identity spravované služby (MSI) Azure Data Factory (ADF).  U druhé možnosti je potřeba přidat MSI služby ADF do skupiny AAD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Vytvoření prostředí Azure-SSIS IR s ověřováním AAD](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 
- Ověřte si, že váš server Azure SQL Database nemá katalog služby SSIS (databázi SSIDB). Zřízení prostředí Azure-SSIS IR nepodporuje použití existujícího katalogu služby SSIS. 

> [!NOTE]
> - Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:** [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). 
> - Pokud chcete zobrazit seznam oblastí Azure, ve kterých je prostředí Azure-SSIS Integration Runtime aktuálně dostupné, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **SSIS Integration Runtime:** [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). 

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
1. Jako **Verze** vyberte **V2 (Preview)**. 
1. Jako **Umístění** vyberte umístění datové továrny. V seznamu se zobrazí pouze podporovaná umístění pro vytváření datových továren. 
1. Zaškrtněte **Připnout na řídicí panel**. 
1. Vyberte **Vytvořit**. 
1. Na řídicím panelu se zobrazí následující dlaždice se stavem **Nasazování datové továrny**: 

   ![Dlaždice Nasazování datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Po vytvoření se zobrazí stránka **Datová továrna**. 

   ![Domovská stránka datové továrny](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Výběrem dlaždice **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure-SSIS Integration Runtime

1. Na stránce **Začínáme** vyberte dlaždici **Konfigurace prostředí SSIS Integration Runtime**. 

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Na stránce **Obecná nastavení** v okně **Instalace prostředí Integration Runtime** proveďte následující kroky: 

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Jako **Název** zadejte název vašeho prostředí Integration Runtime. 

   b. Jako **Popis** zadejte popis vašeho prostředí Integration Runtime. 

   c. Jako **Umístění** vyberte umístění vašeho prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění, jako má váš databázový server pro hostování databáze SSISDB. 

   d. Jako **Velikost uzlu** vyberte velikost uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované velikosti uzlů. Pokud chcete spouštět mnoho balíčků náročných na výpočetní výkon nebo paměť, vyberte velkou velikost uzlu (vertikálně navyšte kapacitu). 

   e. Jako **Počet uzlů** vyberte počet uzlů ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty uzlů. Pokud chcete paralelně spouštět mnoho balíčků, vyberte velký cluster s mnoha uzly (horizontálně navyšte kapacitu). 

   f. Jako **Edice / licence** vyberte edici nebo licenci SQL Serveru pro vaše prostředí Integration Runtime: Standard nebo Enterprise. Vyberte Enterprise, pokud ve svém prostředí Integration Runtime chcete využít pokročilé nebo prémiové funkce. 

   g. V části **Úspora peněz** vyberte možnost Zvýhodněného hybridního využití Azure (AHB) pro vaše prostředí Integration Runtime: Ano nebo Ne. Vyberte Ano, pokud chcete použít vlastní licenci SQL Serveru se Software Assurance a využít tak úspory nákladů spojené s hybridním využitím. 

   h. Klikněte na **Další**. 

1. Na stránce **Nastavení SQL** proveďte následující kroky: 

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

   b. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime. 

   c. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako samostatnou databázi, součást elastického fondu nebo v MI (Preview) a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny pro výběr typu databázového serveru pro hostování SSISDB najdete ve [srovnání SQL Database a Spravované instance (Preview)](create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). Pokud k hostování databáze SSISDB vyberete službu Azure SQL Database s koncovými body služby virtuální sítě nebo Spravované instance (Preview) nebo pokud vyžadujete přístup k místním datům, musíte prostředí Azure-SSIS IR připojit k virtuální síti. Další informace najdete v tématu o [vytvoření prostředí Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. Pomocí zaškrtávacího políčka **Použít ověřování AAD...** vyberte metodu ověřování pro váš databázový server pro hostování databáze SSISDB: SQL nebo Azure Active Directory (AAD) s použitím identity spravované služby (MSI) služby Azure Data Factory (ADF). Pokud políčko zaškrtnete, je potřeba přidat MSI služby ADF do skupiny AAD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Vytvoření prostředí Azure-SSIS IR s ověřováním AAD](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Jako **Uživatelské jméno správce** zadejte uživatelské jméno ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

   f. Jako **Heslo správce** zadejte heslo ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

   g. Jako **Úroveň služby databáze katalogu** vyberte úroveň služby pro váš databázový server pro hostování databáze SSISDB: úroveň Basic, Standard nebo Premium nebo název elastického fondu. 

   h. Klikněte na **Test připojení**, a pokud proběhne úspěšně, klikněte na **Další**. 

1. Na stránce **Upřesnit nastavení** proveďte následující kroky: 

   ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Jako **Maximální počet paralelních zpracování na uzel** vyberte maximální počet balíčků, které se můžou paralelně spustit v jednom uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty balíčků. Pokud chcete použít více než jedno jádro ke spuštění jednoho velkého balíčku náročného na výpočetní výkon nebo paměť, vyberte nízké číslo. Pokud chcete spustit jeden nebo více malých a méně náročných balíčků v jednom jádru, vyberte vysoké číslo. 

   b. Jako **Identifikátor URI SAS kontejneru vlastního nastavení** volitelně zadejte identifikátor URI sdíleného přístupového podpisu (SAS) vašeho kontejneru Azure Storage Blob, ve kterém je uložený váš instalační skript a související soubory. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Pomocí zaškrtávacího políčka **Vybrat virtuální síť...** vyberte, jestli chcete své prostředí Integration Runtime připojit k virtuální síti. Políčko byste měli zaškrtnout, pokud k hostování databáze SSISDB používáte službu Azure SQL Database s koncovými body služby virtuální sítě nebo MI (Preview) nebo pokud vyžadujete přístup k místním datům. Další informace najdete v tématu [Vytvoření prostředí Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Kliknutím na **Dokončit** zahajte vytváření vašeho prostředí Integration Runtime. 

   > [!IMPORTANT]
   > Tento proces trvá přibližně 20 až 30 minut.
   >
   > Služba Data Factory se připojí k vašemu serveru služby Azure SQL Database a připraví katalog služby SSIS (databázi SSISDB). 
   > 
   > Když zřizujete instanci prostředí Azure-SSIS IR, nainstaluje se také Azure Feature Pack for SSIS a Access Redistributable. Tyto komponenty nabízejí mimo připojení k datovým zdrojům podporovaným integrovanými komponentami navíc možnosti připojení k souborům aplikací Excel a Access a různým datovým zdrojům Azure. Můžete nainstalovat také další komponenty. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). 

1. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

   ![Stav vytváření s tlačítkem Aktualizovat](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Pomocí odkazů ve sloupci **Akce** můžete prostředí Integration Runtime zastavit nebo spustit, upravit nebo odstranit. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené. 

   ![Odkazy ve sloupci Akce](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime

1. V uživatelském prostředí služby Azure Data Factory přepněte na kartu **Upravit**, vyberte **Připojení** a potom přepněte na kartu **Prostředí Integration Runtime**, kde se zobrazí existující prostředí Integration Runtime ve vaší datové továrně. 

   ![Výběr možností za účelem zobrazení stávajících prostředí IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Výběrem možnosti **Nové** vytvořte nové prostředí Azure-SSIS IR. 

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. V okně **Instalace prostředí Integration Runtime** vyberte možnost **Migrovat metodou „lift and shift“ existující balíčky služby SSIS ke spuštění v Azure** a pak vyberte tlačítko **Další**. 

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zbývající kroky nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS
Teď použijte SQL Server Data Tools (SSDT) nebo aplikaci SQL Server Management Studio (SSMS) k nasazení vašich balíčků SSIS do Azure. Připojte se ke svému serveru služby Azure SQL Database, který hostuje katalog služby SSIS (databázi SSISDB). Název serveru služby Azure SQL Database je ve formátu `<servername>.database.windows.net`. 

Projděte si následující články v dokumentaci ke službě SSIS: 

- [Nasazení, spuštění a monitorování balíčku SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Připojení ke katalogu služby SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Plánování spouštění balíčku v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Zřízení prostředí Azure-SSIS Integration Runtime

Pokud se chcete dozvědět o kopírování dat z místního prostředí do cloudu, přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
> [Kopírování dat v cloudu](tutorial-copy-data-portal.md)
