---
title: Vytvoření prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory, abyste mohli nasadit a spouštění balíčků služby SSIS v Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 4339782304f1bc175f1066954f1050bc00f25005
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434236"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Vytvoření prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory
Tento článek popisuje kroky pro zřízení prostředí Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF). Potom můžete použít SQL Server Data Tools (SSDT) nebo SQL Server Management Studio (SSMS) k nasazení a spouštění balíčků SQL Server Integration Services (SSIS) v tomto modulu integration runtime v Azure. 

[Kurzu: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) ukazuje, jak vytvořit prostředí Azure-SSIS IR pomocí serveru Azure SQL Database a hostitele databázi katalogu služby SSIS (SSISDB). Tento článek dál navazuje na tento kurz a ukazuje, jak provádět následující akce: 

- Volitelně použijte server Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance pro hostování služby SSISDB. Pokyny k výběru typu databázový server pro hostování služby SSISDB, naleznete v tématu [serveru porovnání Azure SQL Database a Managed Instance](create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). Předpokladem je budete muset připojit k prostředí Azure-SSIS IR k virtuální síti a konfigurace virtuální sítě oprávnění a nastavení podle potřeby. Zobrazit [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

- Volitelně můžete pomocí ověřování Azure Active Directory (AAD) spravovanou identitu pro vaši ADF pro připojení k serveru databáze. Předpokladem je, budete muset přidat spravovanou identitu pro vaši ADF jako schopná vytvořit SSISDB ve vaší databázi Azure SQL server nebo spravované Instance uživatele databáze s omezením naleznete v tématu [ověřování AAD povolit pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

## <a name="overview"></a>Přehled
Tento článek ukazuje různé způsoby zřizování prostředí Azure-SSIS IR: 

- [Azure Portal](#azure-portal) 
- [Azure PowerShell](#azure-powershell) 
- [Šablona Azure Resource Manageru](#azure-resource-manager-template) 

Při vytváření prostředí Azure-SSIS IR, služba ADF se připojí k vaší databáze SQL Azure server nebo spravované Instance Příprava databáze SSISDB. Také nakonfiguruje oprávnění a nastavení pro virtuální síť, je-li zadán a připojí prostředí Azure-SSIS IR k virtuální síti. 

Když si zřídíte Azure-SSIS IR, nainstaluje se také Azure Feature Pack for SSIS a Access Redistributable. Tyto komponenty nabízejí připojení k souborům aplikace Excel a Access a různým datovým zdrojům Azure, kromě zdroje dat podporované metodou integrované komponenty. Můžete nainstalovat také další komponenty. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). 

## <a name="prerequisites"></a>Požadavky 
- **Předplatné Azure**. Pokud ještě nemáte předplatné, můžete vytvořit [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/) účtu. 

- **Azure server služby SQL Database nebo spravované Instance**. Pokud ještě nemáte databázový server, můžete můžete vytvořit na webu Azure portal před zahájením práce. Tento server bude hostovat službu SSISDB. Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí integration runtime. Tato konfigurace umožňuje prostředí integration runtime zapisovat protokoly spuštění do databáze SSISDB bez přecházení mezi oblastmi Azure. Na základě vybrané databáze serveru, SSISDB lze vytvořit vaším jménem jako izolovanou databázi, součástí elastického fondu, nebo v Managed Instance a dostupný ve veřejné síti nebo pomocí připojení k virtuální síti. Seznam podporovaných cenové úrovně pro službu Azure SQL Database najdete v tématu [limity prostředků SQL Database](../sql-database/sql-database-resource-limits.md). 

    Ujistěte se, že vaše databáze Azure SQL server nebo spravované Instance ještě nemá SSISDB. Zřízení prostředí Azure-SSIS IR nepodporuje použití existující databáze SSISDB. 

- **Azure Resource Manageru virtuální síť (volitelné)**. Musí mít virtuální síť Azure Resource Manageru, pokud platí alespoň jedna z následujících podmínek: 
    - Na serveru Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instanci, která je ve virtuální síti, jsou hostiteli databáze SSISDB. 
    - Chcete se připojit k datům v místním úložišti z balíčků SSIS spuštěných v Azure-SSIS IR. 

- **Azure PowerShell**. Postupujte podle pokynů [instalace a konfigurace Azure Powershellu](/powershell/azure/azurerm/install-azurerm-ps), pokud chcete spustit skript prostředí PowerShell ke zřízení prostředí Azure-SSIS IR. 

### <a name="region-support"></a>Oblasti podpory
Seznam oblastí Azure, ve kterých jsou aktuálně k dispozici, ADF a Azure-SSIS IR najdete v části [ADF + SSIS IR dostupnost podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

### <a name="compare-sql-database-logical-server-and-sql-database-managed-instance"></a>Porovnání logický server služby SQL Database a SQL Database Managed Instance

Následující tabulka porovnává určitých funkcí serveru Azure SQL Database a Managed Instance, jak souvisejí s nabídkou Azure SSIR reakcí na Incidenty:

| Funkce | Server služby Azure SQL Database| MI |
|---------|--------------|------------------|
| **Plánování** | Agent systému SQL Server není k dispozici.<br/><br/>Zobrazit [plánování spouštění balíčku v kanálu ADF](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Spravovaná Instance agenta je k dispozici. |
| **Ověřování** | Můžete vytvářet databáze SSISDB uživatele databáze s omezením představující všechny skupiny AAD s identitou, vaše ADF jako člen **db_owner** role.<br/><br/>Zobrazit [povolení služby Azure AD ověřování k vytvoření databáze SSISDB na serveru Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Můžete vytvořit službu SSISDB s uživatele databáze s omezením představující spravovanou identitu vaší ADF. <br/><br/>Zobrazit [povolení služby Azure AD ověřování k vytvoření databáze SSISDB v Azure SQL Database Managed Instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Úroveň služeb** | Při vytváření prostředí Azure-SSIS IR pomocí serveru Azure SQL Database, můžete vybrat na úrovni služby pro databázi SSISDB. Existuje více úrovní služeb. | Při vytváření prostředí Azure-SSIS IR pomocí Managed Instance, nelze vybrat úroveň služby pro databázi SSISDB. Všechny databáze ve spravované instanci sdílet stejný prostředek přidělených této instanci. |
| **Virtuální síť** | Podporuje pouze Azure Resource Manageru virtuální sítě pro prostředí Azure-SSIS IR mají spojit, když pomocí serveru Azure SQL Database s koncovými body služby virtuální sítě a vyžadují přístup k úložišti dat v místním. | Podporuje pouze Azure Resource Manageru virtuální sítě pro vaše prostředí Azure SSIS IR pro připojení. Vždy se vyžaduje virtuální síť.<br/><br/>Když se do programu Azure-SSIS IR do stejné virtuální síti jako Managed Instance, ujistěte se, že prostředí Azure-SSIS IR je v jiné podsíti, než Managed Instance. Když se do programu Azure-SSIS IR do jiné virtuální sítě, než Managed Instance, doporučujeme, abyste buď partnerský vztah virtuální sítě nebo virtuální sítě pro připojení k virtuální síti. Zobrazit [vaši aplikaci do Azure SQL Database Managed Instance připojit](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuované transakce** | Podporováno prostřednictvím elastické transakce. Transakce Microsoft distribuované transakce koordinátor (MSDTC) nejsou podporovány. Pokud balíčků služby SSIS pomocí příkaz MSDTC koordinovat distribuované transakce, zvažte přechod na elastické transakce pro službu Azure SQL Database. Další informace najdete v tématu [distribuované transakce v cloudových databázích](../sql-database/sql-database-elastic-transactions-overview.md). | Nepodporuje se. |
| | | |

## <a name="azure-portal"></a>portál Azure
V této části použijete Azure portal, konkrétně ADF uživatelské rozhraní (UI) nebo aplikace, chcete-li vytvořit prostředí Azure-SSIS IR. 

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

    f. Pro **Edition nebo licencí**, vyberte edici systému SQL Server/licenci pro prostředí integration runtime: Standard nebo Enterprise. Vyberte Enterprise, pokud ve svém prostředí Integration Runtime chcete využít pokročilé nebo prémiové funkce. 

    g. Pro **uložit peníze**, vyberte možnost Azure Hybrid Benefit (AHB) pro prostředí integration runtime: Ano nebo ne. Vyberte Ano, pokud chcete použít vlastní licenci SQL Serveru se Software Assurance a využít tak úspory nákladů spojené s hybridním využitím. 

    h. Klikněte na **Další**. 

1. Na stránce **Nastavení SQL** proveďte následující kroky: 

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

    b. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime. 

    c. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. 

    d. Na **ověřování pomocí AAD...**  zaškrtávací políčko, vyberte metodu ověřování pro databázový server pro hostování služby SSISDB: SQL nebo Azure Active Directory (AAD) s identitou pro služby Azure Data Factory. Pokud ho budete kontrolovat, je potřeba přidat spravovanou identitu pro vaši ADF do skupiny AAD s oprávnění k přístupu k databázovému serveru, najdete v článku [ověřování AAD povolit pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    e. Jako **Uživatelské jméno správce** zadejte uživatelské jméno ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

    f. Jako **Heslo správce** zadejte heslo ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

    g. Pro **úroveň služeb databáze katalogu**, vyberte úroveň služby pro databázový server pro hostování služby SSISDB: Úroveň Basic, Standard nebo Premium nebo název elastického fondu. 

    h. Klikněte na **Test připojení**, a pokud proběhne úspěšně, klikněte na **Další**. 

1.  Na stránce **Upřesnit nastavení** proveďte následující kroky: 

    ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Jako **Maximální počet paralelních zpracování na uzel** vyberte maximální počet balíčků, které se můžou paralelně spustit v jednom uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty balíčků. Vyberte nízké číslo, pokud chcete použít více než jedno jádro ke spuštění jedné velké a náročné balíček, který je výpočetní/paměti-náročné. Pokud chcete spustit jeden nebo více malých a méně náročných balíčků v jednom jádru, vyberte vysoké číslo. 

    b. Jako **Identifikátor URI SAS kontejneru vlastního nastavení** volitelně zadejte identifikátor URI sdíleného přístupového podpisu (SAS) vašeho kontejneru Azure Storage Blob, ve kterém je uložený váš instalační skript a související soubory. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

1. Na **výběr virtuální sítě...**  zaškrtávací políčko, vyberte, jestli se chcete připojit k prostředí integration runtime k virtuální síti. Zkontroluje, pokud používáte Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance pro hostování služby SSISDB nebo vyžadují přístup k místním datům; To znamená, že máte místní zdroje/cíle dat v balíčků služby SSIS, naleznete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud ho budete kontrolovat, proveďte následující kroky: 

   ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Pro **předplatné**, vyberte předplatné Azure, která má virtuální sítě. 

    b. Pro **umístění**, vybrali stejného umístění vašeho prostředí integration runtime. 

    c. Pro **typ**, vyberte typ virtuální sítě: Classic nebo Azure Resource Manageru. Doporučujeme vám, že vyberete virtuální síť Azure Resource Manageru, protože klasické virtuální sítě se brzy přestanou používat. 

    d. Pro **název virtuální sítě**, vyberte název virtuální sítě. Tato virtuální síť musí být stejné virtuální síti použít pro službu Azure SQL Database s virtuální sítě služby koncové body nebo spravované instanci k hostování databáze SSISDB a nebo připojené k vaší místní síti. 

    e. Pro **název podsítě**, vyberte název podsítě pro virtuální síť. To by měl být jiné podsíti, než jaký se používá pro Managed Instance pro hostování služby SSISDB. 

1. Klikněte na tlačítko **ověření virtuální sítě** a v případě úspěchu, klikněte na tlačítko **Dokončit** spusťte vytváření prostředí Azure-SSIS integration runtime. 

    > [!IMPORTANT]
    > - Tento proces trvá přibližně 20 až 30 minut
    > - Služba Data Factory se připojí k vaší službě Azure SQL Database a připraví databázi katalogu služby SSIS (SSISDB). Také nakonfiguruje oprávnění a nastavení virtuální sítě, pokud zadaná a připojí novou instanci prostředí Azure-SSIS IR k virtuální síti. 

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
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS" 
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

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
# Make sure to run this script against the subscription to which the virtual network belongs
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

Pokud není databáze SQL Azure pomocí virtuální sítě služby koncové body nebo spravované Instance pro hostování služby SSISDB, ani vyžadují přístup k místním datům, můžete vynechat VNetId a podsítě nebo předat prázdné hodnoty pro ně. V opačném případě nemůže vynechat, nechte je a musíte předat platný hodnoty z konfigurace vaší virtuální sítě, naleznete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

Pokud používáte spravovanou instanci pro hostování služby SSISDB, můžete CatalogPricingTier parametr vynecháte nebo předat prázdnou hodnotu pro něj. Jinak, nelze ji vynechat a musíte předat platnou hodnotu ze seznamu na podporované cenové úrovně pro službu Azure SQL Database, najdete v článku [limity prostředků SQL Database](../sql-database/sql-database-resource-limits.md). 

Pokud používáte ověřování Azure Active Directory (AAD) s identitou pro služby Azure Data Factory pro připojení k databázovému serveru, můžete vynechat parametr CatalogAdminCredential, ale je nutné přidat spravovanou identitu pro vaši ADF do skupiny AAD s přístupová oprávnění k databázovému serveru, najdete v článku [ověřování AAD povolit pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). V opačném případě nemůžete vynechat a musíte předat platný objekt vytvořený z vaší uživatelské jméno správce serveru a heslo pro ověřování SQL.

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
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
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS" 
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

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
# Make sure to run this script against the subscription to which the virtual network belongs
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
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
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
                            "nodeSize": "Standard_D8_v3",
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
Teď použijte SQL Server Data Tools (SSDT) nebo aplikaci SQL Server Management Studio (SSMS) k nasazení vašich balíčků SSIS do Azure. Připojte se ke svému databázovému serveru, který hostuje katalog služby SSIS (SSISDB). Název databázového serveru je ve formátu: &lt;Název serveru Azure SQL Database&gt;. database.windows.net nebo &lt;název spravované Instance. Předpona DNS&gt;. database.windows.net. Pokyny najdete v článku věnovaném [nasazení balíčků](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

## <a name="next-steps"></a>Další postup
Zobrazit další prostředí Azure-SSIS IR témata v této dokumentaci: 

- [Prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o prostředí integration runtime obecně včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o připojení Azure-SSIS IR k virtuální síti Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 
