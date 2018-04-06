---
title: Nasazení balíčků SSIS do Azure | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak nasadit balíčky SSIS do Azure a vytvořit prostředí Azure SSIS Integration Runtime pomocí služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: hero-article
ms.date: 01/29/2018
ms.author: douglasl
ms.openlocfilehash: aca9f822bf3fd3b26e554240a4fee2474b89143d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>Nasazení balíčků SSIS (SQL Server Integration Services) do Azure
Tento kurz obsahuje postup pro zřízení prostředí Azure-SSIS Integration Runtime (IR) ve službě Azure Data Factory pomocí webu Azure Portal. Následně můžete pomocí SQL Server Data Tools nebo aplikace SQL Server Management Studio do tohoto modulu runtime v Azure nasadit balíčky SSIS (SQL Server Integration Services). Informace o konceptu prostředí Azure-SSIS IR najdete v [přehledu prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Zřízení prostředí Azure-SSIS Integration Runtime

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, která je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 
- **Server služby Azure SQL Database**. Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Azure Data Factory na tomto databázovém serveru vytvoří katalog služby SSIS (databázi SSISDB). Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration Runtime zapisovat do databáze SSISDB protokoly spuštění bez přecházení mezi oblastmi Azure. 
- Ujistěte se, že má databázový server povolené nastavení **Povolit přístup ke službám Azure**. Další informace najdete v tématu [Zabezpečení databáze SQL Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si téma věnované rutině [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1).
- Přidejte do seznamu IP adres v nastavení brány firewall databázového serveru IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
- Ověřte si, že váš server Azure SQL Database nemá katalog služby SSIS (databázi SSIDB). Zřízení prostředí Azure-SSIS IR nepodporuje použití existujícího katalogu služby SSIS.

> [!NOTE]
> - Datovou továrnu verze 2 můžete vytvořit v následujících oblastech: USA – východ, USA – východ 2, Jihovýchodní Asie a Západní Evropa. 
> - Prostředí Azure SSIS IR můžete vytvořit v následujících oblastech: USA – východ, USA – východ 2, USA – střed, Severní Evropa, Západní Evropa a Austrálie – východ. 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
2. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).    
3. Vyberte **Nový** v nabídce vlevo, vyberte **Data a analýzy** a pak vyberte **Datová továrna**. 
   
   ![Výběr datové továrny v podokně Nový](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na stránce **Nová datová továrna** zadejte do pole **Název** text **MyAzureSsisDataFactory**. 
      
   ![Stránka Nová datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny (například na **&lt;vaše_jméno_&gt;MyAzureSsisDataFactory**) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
5. V části **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a ze seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
   Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
6. Jako **Verze** vyberte **V2 (Preview)**.
7. Jako **Umístění** vyberte umístění datové továrny. V seznamu se zobrazí pouze podporovaná umístění pro vytváření datových továren.
8. Zaškrtněte **Připnout na řídicí panel**.     
9. Vyberte **Vytvořit**.
10. Na řídicím panelu se zobrazí následující dlaždice se stavem **Nasazování datové továrny**: 

   ![Dlaždice Nasazování datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. Po vytvoření se zobrazí stránka **Datová továrna**.
   
   ![Domovská stránka datové továrny](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. Výběrem dlaždice **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure-SSIS Integration Runtime

1. Na stránce **Začínáme** vyberte dlaždici **Konfigurace prostředí SSIS Integration Runtime**. 

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. Na stránce **Obecná nastavení** v okně **Instalace prostředí Integration Runtime** proveďte následující kroky: 

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Do pole **Název** zadejte název prostředí Integration Runtime.

   b. V poli **Umístění** vyberte umístění prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění.

   c. V poli **Velikost uzlu** vyberte velikost uzlu, která se má nastavit pro modul runtime služby SSIS.

   d. Do pole **Počet uzlů** zadejte počet uzlů v clusteru.
   
   e. Vyberte **Next** (Další). 
3. Na stránce **Nastavení SQL** proveďte následující kroky: 

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. V poli **Předplatné** určete předplatné Azure, které obsahuje databázový server Azure.

   b. V poli **Koncový bod databázového serveru katalogu** vyberte svůj databázový server Azure.

   c. Do pole **Uživatelské jméno správce** zadejte uživatelské jméno správce.

   d. Do pole **Heslo správce** zadejte heslo správce.

   e. V poli **Úroveň služeb databáze katalogu** vyberte úroveň služeb databáze SSISDB. Výchozí hodnota je **Základní**.

   f. Vyberte **Next** (Další). 
4. Na stránce **Upřesnit nastavení** vyberte hodnotu v poli **Maximální počet paralelních zpracování na uzel**.   

   ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Tento krok je *volitelný*. Pokud máte virtuální síť (vytvořenou pomocí klasického modelu nasazení nebo prostřednictvím Azure Resource Manageru), ke které chcete prostředí Integration Runtime připojit, zaškrtněte políčko **Vybrat virtuální síť, ke které se prostředí Azure SSIS Integration Runtime připojí, a povolit službám Azure konfigurovat oprávnění a nastavení virtuální sítě**. Potom proveďte následující kroky: 

   ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. V poli **Předplatné** určete předplatné, které obsahuje virtuální síť.

   b. V poli **Název virtuální sítě** vyberte název virtuální sítě.

   c. V poli **Název podsítě** vyberte název podsítě virtuální sítě. 
6. Výběrem možnosti **Dokončit** spusťte vytváření prostředí Azure-SSIS Integration Runtime. 

   > [!IMPORTANT]
   > Tento proces trvá přibližně 20 minut.
   >
   > Služba Data Factory se připojí k vaší službě Azure SQL Database a připraví katalog služby SSIS (databázi SSISDB). Skript nakonfiguruje také oprávnění a nastavení virtuální sítě, pokud jste ji vybrali. Kromě toho také k této virtuální síti připojí novou instanci prostředí Azure-SSIS Integration Runtime.
   > 
   > Když zřizujete instanci prostředí Azure-SSIS IR, nainstaluje se také Azure Feature Pack for SSIS a Access Redistributable. Tyto komponenty nabízejí mimo připojení k datovým zdrojům podporovaným integrovanými komponentami navíc možnosti připojení k souborům aplikací Excel a Access a různým datovým zdrojům Azure. V tuto chvíli nemůžete instalovat komponenty třetích stran pro službu SSIS. (Toto omezení se týká i komponent třetích stran od Microsoftu, jako jsou komponenty Oracle a Teradata od společnosti Attunity a komponenty SAP BI).

7. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

   ![Stav vytváření s tlačítkem Aktualizovat](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Pomocí odkazů ve sloupci **Akce** můžete prostředí Integration Runtime zastavit nebo spustit, upravit nebo odstranit. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené. 

   ![Odkazy ve sloupci Akce](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime

1. V uživatelském prostředí služby Azure Data Factory přepněte na kartu **Upravit**, vyberte **Připojení** a potom přepněte na kartu **Prostředí Integration Runtime**, kde se zobrazí existující prostředí Integration Runtime ve vaší datové továrně. 
   ![Výběr možností za účelem zobrazení stávajících prostředí IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. Výběrem možnosti **Nové** vytvořte nové prostředí Azure-SSIS IR. 

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. V okně **Instalace prostředí Integration Runtime** vyberte možnost **Migrovat metodou „lift and shift“ existující balíčky služby SSIS ke spuštění v Azure** a pak vyberte tlačítko **Další**.

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Zbývající kroky nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

 
## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS
Teď pomocí sady SQL Server Data Tools nebo aplikace SQL Server Management Studio nasaďte své balíčky SSIS do Azure. Připojte se k databázovému serveru Azure, který hostuje katalog služby SSIS (databázi SSISDB). Název databázového serveru Azure je ve formátu `<servername>.database.windows.net` (pro službu Azure SQL Database). 

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
