---
title: Vytvoření prostředí Azure-SSIS integration runtime ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit prostředí Azure-SSIS integration runtime ve službě Azure Data Factory, abyste mohli nasadit a spouštění balíčků služby SSIS v Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 90f6841ddc2fe1c017dbfc7e9046fae4a0ceb097
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363805"
---
# <a name="create-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Vytvoření prostředí Azure-SSIS integration runtime ve službě Azure Data Factory
Tento článek popisuje kroky pro zřízení prostředí Azure-SSIS integration runtime ve službě Azure Data Factory. Následně můžete pomocí SQL Server Data Tools (SSDT) nebo aplikace SQL Server Management Studio (SSMS) v tomto modulu runtime v Azure nasazovat a spouštět balíčky SSIS (SQL Server Integration Services). 

Tento kurz [kurz: nasazení balíčků služby SQL Server Integration Services (SSIS) do Azure](tutorial-create-azure-ssis-runtime-portal.md) ukazuje, jak vytvořit s využitím Azure SQL Database k hostování katalogu SSIS prostředí Azure-SSIS Integration Runtime (IR). Tento článek dál navazuje na tento kurz a ukazuje, jak provádět následující akce: 

- Volitelně můžete použijte Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance jako databázový server k hostování katalogu SSIS (databázi SSISDB). Pokyny k výběru typu databázový server pro hostování služby SSISDB, naleznete v tématu [logický server porovnání SQL Database a SQL Database Managed Instance](create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). Předpokladem je budete muset připojit k prostředí Azure-SSIS IR k virtuální síti a konfigurace virtuální sítě oprávnění a nastavení podle potřeby. Zobrazit [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

- Volitelně můžete pomocí ověřování Azure Active Directory (AAD) vaší identity spravované služby Azure Data Factory pro prostředky Azure pro prostředí Azure-SSIS IR pro připojení k databázovému serveru. Předpokladem je, budete muset přidat vaše Data Factory MSI do skupiny AAD s oprávnění k přístupu k databázovému serveru, najdete v článku [ověřování AAD povolit pro Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

## <a name="overview"></a>Přehled
Tento článek ukazuje různé způsoby zřizování prostředí Azure-SSIS IR: 

- [Azure Portal](#azure-portal) 
- [Azure PowerShell](#azure-powershell) 
- [Šablona Azure Resource Manageru](#azure-resource-manager-template) 

Při vytváření prostředí Azure-SSIS IR služby Data Factory se připojí k Azure SQL Database a připraví databázi katalogu služby SSIS (SSISDB). Skript také nakonfiguruje oprávnění a nastavení virtuální sítě, pokud zadaný a připojí novou instanci prostředí Azure-SSIS IR k virtuální síti. 

Když zřizujete instanci Azure-SSIS IR, nainstaluje se také Azure Feature Pack for SSIS a Access Redistributable. Tyto komponenty nabízejí mimo připojení k datovým zdrojům podporovaným integrovanými komponentami navíc možnosti připojení k souborům aplikací Excel a Access a různým datovým zdrojům Azure. Můžete nainstalovat také další komponenty. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). 

## <a name="prerequisites"></a>Požadavky 
- **Předplatné Azure**. Pokud předplatné nemáte, můžete si vytvořit [bezplatný zkušební](http://azure.microsoft.com/pricing/free-trial/) účet. 

- **Logický server Azure SQL Database nebo spravované Instance**. Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Tento server hostuje databázi katalogu služby SSIS (SSISDB). Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration Runtime zapisovat protokoly spuštění do databáze SSISDB bez přecházení mezi oblastmi Azure. V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Seznam podporovaných cenové úrovně pro službu Azure SQL Database najdete v tématu [limity prostředků SQL Database](../sql-database/sql-database-resource-limits.md). 

    Ujistěte se, že logický server Azure SQL Database nebo spravované Instance ještě nemá katalog služby SSIS (databázi SSIDB). Zřízení Azure-SSIS IR nepodporuje používání existujícího katalogu služby SSIS. 

- **Classic nebo virtuální sítí Azure Resource Manageru (volitelné)**. Virtuální síť Azure musí mít, pokud platí alespoň jedna z následujících podmínek: 
    - Hostují databázi katalogu služby SSIS v Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance, která je ve virtuální síti. 
    - Z balíčků SSIS spuštěných v prostředí Azure SSIS Integration Runtime se chcete připojit k místním úložištím dat. 

- **Azure PowerShell**. Postupujte podle pokynů v [instalace a konfigurace Azure Powershellu](/powershell/azure/install-azurerm-ps), je-li spustit skript prostředí zřízení prostředí Azure-SSIS integration runtime spouštějícího balíčky SSIS v cloudu pomocí Powershellu. 

### <a name="region-support"></a>Oblasti podpory
Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:** [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).

Seznam oblastí Azure, ve kterých je v současnosti Azure-SSIS Integration Runtime, vyberte oblasti, které vás zajímají na následující stránce a potom rozbalte **Analytics** najít **prostředí SSIS Integration Runtime** : [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). ### porovnání SQL Database a Managed Instance

### <a name="compare-sql-database-logical-server-and-sql-database-managed-instance"></a>Porovnání logický server služby SQL Database a SQL Database Managed Instance

Následující tabulka porovnává určité funkce logický server SQL Database a SQL Database Managed Instance, jak souvisejí s nabídkou Azure SSIR IR:

| Funkce | Logický server služby SQL Database| SQL Database – spravovaná Instance |
|---------|--------------|------------------|
| **Plánování** | Agent systému SQL Server není k dispozici.<br/><br/>Zobrazit [naplánovat balíčku jako součást kanálu Azure Data Factory](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| Agent systému SQL Server je k dispozici. |
| **Ověřování** | Můžete vytvořit databázi pomocí uživatelského účtu databáze s omezením, který reprezentuje všechny uživatele Azure Active Directory v **dbmanager** role.<br/><br/>Zobrazit [povolení služby Azure AD pro službu Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Nelze vytvořit databázi s účtem uživatele databáze s omezením, který reprezentuje všechny uživatele Azure Active Directory než správce Azure AD. <br/><br/>Zobrazit [povolení služby Azure AD na spravované instanci Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Úroveň služeb** | Při vytváření prostředí Azure-SSIS IR pro službu SQL Database, můžete vybrat na úrovni služby pro databázi SSISDB. Existuje několik úrovní služeb. | Když vytvoříte na spravované instanci Azure-SSIS IR, nelze vybrat úroveň služby pro databázi SSISDB. Všechny databáze na stejné Managed Instance sdílet stejný prostředek přidělených této instanci. |
| **Virtuální síť** | Podporuje Azure Resource Manager a klasické virtuální sítě. | Podporuje pouze virtuální sítí Azure Resource Manageru. Virtuální síť je povinná.<br/><br/>Když se do programu Azure-SSIS IR do stejné virtuální síti jako Managed Instance, ujistěte se, že Azure-SSIS IR je v jiné podsíti, než Managed Instance. Když se do programu Azure-SSIS IR k jiné virtuální sítě, než Managed Instance, doporučujeme, abyste partnerský vztah virtuální sítě (což je omezený na stejné oblasti) nebo virtuální sítě pro připojení k virtuální síti. Zobrazit [vaši aplikaci do Azure SQL Database Managed Instance připojit](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuované transakce** | Transakce Microsoft distribuované transakce koordinátor (MSDTC) nejsou podporovány. Pokud vaše balíčky pomocí služby MSDTC koordinovat distribuované transakce, může být schopni implementovat dočasné řešení s použitím elastické transakce pro službu SQL Database. V tuto chvíli nemá SSIS integrovanou podporu pro elastické transakce. Elastické transakce používat v balíčku služby SSIS, budete muset psát vlastní kód technologie ADO.NET v rámci úlohy skriptu. Tento skript úkolu musí obsahovat začátek a konec transakce a všechny akce, které musejí nastat uvnitř transakce.<br/><br/>Další informace o kódování elastické transakce, naleznete v tématu [elastické transakce s Azure SQL Database](https://azure.microsoft.com/blog/elastic-database-transactions-with-azure-sql-database/). Další informace o elastické transakce naleznete v tématu [distribuované transakce v cloudových databázích](../sql-database/sql-database-elastic-transactions-overview.md). | Nepodporuje se. |
| | | |

## <a name="azure-portal"></a>portál Azure
V této části použijete na webu Azure portal, konkrétně Uživatelském rozhraní služby Data Factory k vytvoření Azure-SSIS IR. 

### <a name="create-a-data-factory"></a>Vytvoření datové továrny 
1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome. 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). 
1. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 

   ![Nový -> Datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Na stránce **Nová datová továrna** jako **název** zadejte **MyAzureSsisDataFactory**. 

   ![Stránka Nová datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Název datové továrny Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název datové továrny (například na vaše_jméno_MyAzureSsisDataFactory) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md). 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
1. Pro **Skupinu prostředků** proveďte jeden z následujících kroků: 

   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 

   Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md). 

1. Jako **verzi** vyberte **V2**. 
1. Vyberte **umístění** pro datovou továrnu. V seznamu se zobrazí pouze podporovaná umístění pro vytváření datových továren. 
1. Zaškrtněte **Připnout na řídicí panel**. 
1. Klikněte na možnost **Vytvořit**. 
1. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku. 

    ![Domovská stránka datové továrny](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure SSIS Integration Runtime 
1. Na stránce Začínáme klikněte na dlaždici **Konfigurace prostředí SSIS Integration Runtime**. 

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

    c. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. 

    d. Na **ověřování pomocí AAD...**  zaškrtávací políčko, vyberte metodu ověřování pro databázový server pro hostování služby SSISDB: SQL nebo Azure Active Directory (AAD) s Azure Data Factory se identita spravované pro prostředky Azure. Pokud ho budete kontrolovat, je potřeba přidat vaše Data Factory MSI do skupiny AAD s oprávnění k přístupu k databázovému serveru, naleznete v tématu [ověřování AAD povolit pro Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    e. Jako **Uživatelské jméno správce** zadejte uživatelské jméno ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

    f. Jako **Heslo správce** zadejte heslo ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

    g. Jako **Úroveň služby databáze katalogu** vyberte úroveň služby pro váš databázový server pro hostování databáze SSISDB: úroveň Basic, Standard nebo Premium nebo název elastického fondu. 

    h. Klikněte na **Test připojení**, a pokud proběhne úspěšně, klikněte na **Další**. 

1.  Na stránce **Upřesnit nastavení** proveďte následující kroky: 

    ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Jako **Maximální počet paralelních zpracování na uzel** vyberte maximální počet balíčků, které se můžou paralelně spustit v jednom uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty balíčků. Vyberte nízké číslo, pokud chcete použít více než jedno jádro ke spuštění jedné velké a náročné balíček, který je výpočetní/paměti-náročné. Pokud chcete spustit jeden nebo více malých a méně náročných balíčků v jednom jádru, vyberte vysoké číslo. 

    b. Jako **Identifikátor URI SAS kontejneru vlastního nastavení** volitelně zadejte identifikátor URI sdíleného přístupového podpisu (SAS) vašeho kontejneru Azure Storage Blob, ve kterém je uložený váš instalační skript a související soubory. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

1. Na **výběr virtuální sítě...**  zaškrtávací políčko, vyberte, jestli se chcete připojit k prostředí integration runtime k virtuální síti. Zkontroluje, pokud používáte Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance pro hostování služby SSISDB nebo vyžadují přístup k místním datům; To znamená, že máte místní zdroje/cíle dat v balíčků služby SSIS, naleznete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud ho budete kontrolovat, proveďte následující kroky: 

   ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Pro **předplatné**, vyberte předplatné Azure, která má virtuální sítě. 

    b. Pro **umístění**, vybrali stejného umístění vašeho prostředí integration runtime. 

    c. Pro **typ**, vyberte typ virtuální síť: Classic nebo Azure Resource Manageru. Doporučujeme vám, že vyberete virtuální síť Azure Resource Manageru, protože klasické virtuální sítě se brzy přestanou používat. 

    d. Pro **název virtuální sítě**, vyberte název virtuální sítě. Tato virtuální síť musí být stejné virtuální síti použít pro službu Azure SQL Database s virtuální sítě služby koncové body nebo spravované instanci k hostování databáze SSISDB a nebo připojené k vaší místní síti. 

    e. Pro **název podsítě**, vyberte název podsítě pro virtuální síť. To by měl být jiné podsíti, než jaký se používá pro Managed Instance pro hostování služby SSISDB. 

1. Klikněte na tlačítko **ověření virtuální sítě** a v případě úspěchu, klikněte na tlačítko **Dokončit** spusťte vytváření prostředí Azure-SSIS integration runtime. 

    > [!IMPORTANT]
    > - Tento proces trvá přibližně 20 až 30 minut
    > - Služba Data Factory se připojí k vaší službě Azure SQL Database a připraví databázi katalogu služby SSIS (SSISDB). Skript také nakonfiguruje oprávnění a nastavení virtuální sítě, pokud zadaný a připojí novou instanci prostředí Azure-SSIS IR k virtuální síti. 

1. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Kliknutím na **Aktualizovat** aktualizujte stav. 

   ![Stav vytváření](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Pomocí odkazů ve **akce** sloupec, který se zastavit nebo spustit, upravit nebo odstranit prostředí integration runtime. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené. 

   ![Azure SSIS IR – akce](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Prostředí Azure SSIS Integration Runtime na portálu
1. V uživatelském prostředí služby Azure Data Factory přepněte na kartu **Upravit**, klikněte na **Připojení** a pak přepněte zpět na kartu **Prostředí Integration Runtime**, kde se zobrazí existující prostředí Integration Runtime ve vaší datové továrně. 

   ![Zobrazení existujících IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Kliknutím na **Nový** vytvořte nové prostředí Azure-SSIS IR. 

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Pokud chcete vytvořit prostředí Azure-SSIS Integration Runtime, klikněte na **Nový**, jak je znázorněno na obrázku. 
1. V okně Instalace prostředí Integration Runtime vyberte **Migrovat metodou „lift and shift“ existující balíčky služby SSIS ke spuštění v Azure** a pak klikněte na **Další**. 

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zbývající kroky k nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="azure-powershell"></a>Azure PowerShell
V této části použijete Azure PowerShell k vytvoření Azure-SSIS IR.

### <a name="create-variables"></a>Vytvoření proměnných
Definujte proměnné, které se použijí ve skriptech v tomto kurzu:

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

### <a name="log-in-and-select-subscription"></a>Přihlášení a výběr předplatného
Přidejte následující kód skriptu pro přihlášení a výběr vašeho předplatného Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Ověření připojení k databázi
Přidejte následující skript pro ověření vašeho koncového bodu serveru Azure SQL Database. 

```powershell
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}
```

### <a name="configure-virtual-network"></a>Konfigurace virtuální sítě
Přidejte následující skript, který automaticky nakonfiguruje oprávnění a nastavení virtuální sítě, ke které se má vaše prostředí Azure SSIS Integration Runtime připojit.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte [skupinu prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Spuštěním následujícího příkazu vytvořte datovou továrnu.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Vytvoření prostředí Integration Runtime
Spusťte následující příkazy k vytvoření prostředí Azure-SSIS integration runtime spouštějícího balíčky SSIS v Azure. 

Pokud není databáze SQL Azure pomocí virtuální sítě služby koncové body nebo spravované Instance pro hostování služby SSISDB, ani vyžadují přístup k místním datům, můžete vynechat VNetId a podsítě nebo předat prázdné hodnoty pro ně. V opačném případě nemůže vynechat, nechte je a musíte předat platný hodnoty z konfigurace vaší virtuální sítě, naleznete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

Pokud používáte spravovanou instanci pro hostování služby SSISDB, můžete CatalogPricingTier parametr vynecháte nebo předat prázdnou hodnotu pro něj. Jinak, nelze ji vynechat a musíte předat platnou hodnotu ze seznamu na podporované cenové úrovně pro službu Azure SQL Database, najdete v článku [limity prostředků SQL Database](../sql-database/sql-database-resource-limits.md). 

Pokud používáte ověřování Azure Active Directory (AAD) pomocí služby Azure Data Factory se identita spravované pro prostředky Azure se připojit k databázovému serveru, můžete vynechat parametr CatalogAdminCredential, ale vaše Data Factory MSI je nutné přidat do skupiny AAD s přístupová oprávnění k databázovému serveru, najdete v článku [ověřování AAD povolit pro Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). V opačném případě nemůžete vynechat a musíte předat platný objekt vytvořený z vaší uživatelské jméno správce serveru a heslo pro ověřování SQL.

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

### <a name="start-integration-runtime"></a>Spuštění prostředí Integration Runtime
Spuštěním následujícího příkazu spusťte prostředí Azure SSIS Integration Runtime: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

Dokončení tohoto příkazu trvá **20 až 30 minut**. 

### <a name="full-script"></a>Celý skript
Tady je úplná skript, který vytvoří prostředí Azure-SSIS integration runtime. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
V této části pomocí šablony Azure Resource Manageru k vytvoření prostředí Azure-SSIS integration runtime. Tady je ukázkový názorný postup: 

1. Vytvořte soubor JSON s následující šablony Azure Resource Manageru. Hodnoty v ostrými závorkami (místo zástupné znaky) nahraďte vlastními hodnotami. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D4_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
    
1. Nasazení šablony Azure Resource Manageru, spusťte příkaz New-AzureRmResourceGroupDeployment, jak je znázorněno v následujícím příkladu, kde je ADFTutorialResourceGroup název vaší skupiny prostředků a ADFTutorialARM.json je soubor, který obsahuje JSON definice služby data factory a Azure-SSIS IR. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Tento příkaz vytvoří data factory a Azure-SSIS IR v ní, ale nespustí IR. 

1. Pokud chcete spustit prostředí Azure-SSIS IR, spusťte příkaz Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS
Teď použijte SQL Server Data Tools (SSDT) nebo aplikaci SQL Server Management Studio (SSMS) k nasazení vašich balíčků SSIS do Azure. Připojte se ke svému databázovému serveru, který hostuje katalog služby SSIS (SSISDB). Název databázového serveru je ve formátu: &lt;název serveru Azure SQL Database&gt;. database.windows.net nebo &lt;název spravované Instance. Předpona DNS&gt;. database.windows.net. Pokyny najdete v článku věnovaném [nasazení balíčků](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

## <a name="next-steps"></a>Další postup
Zobrazit další prostředí Azure-SSIS IR témata v této dokumentaci: 

- [Prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o prostředí integration runtime obecně včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 
