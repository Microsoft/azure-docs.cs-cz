---
title: Vytvoření Azure SSIS integrace runtime v Azure Data Factory | Microsoft Docs
description: Naučte se vytvářet modulu runtime integrace Azure SSIS v Azure Data Factory, takže můžete nasadit a spouštění balíčků SSIS v Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f33b24544373bc778f27ef3da18da8d62407a639
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751632"
---
# <a name="create-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>V Azure Data Factory vytvořit modul runtime integrace Azure SSIS
Tento článek popisuje kroky pro zřizování modulu runtime integrace Azure SSIS v Azure Data Factory. Potom můžete SQL Server Data Tools (SSDT) nebo SQL Server Management Studio (SSMS) k nasazení a spuštění balíčky integrační služby SSIS (SQL Server) v tento modul runtime v Azure. 

Tento kurz [kurz: nasadit balíčky SQL Server Integration Services (služby SSIS) do Azure](tutorial-create-azure-ssis-runtime-portal.md) ukazuje, jak vytvořit modul Runtime integrace Azure SSIS (IR) pomocí Azure SQL Database pro hostování katalogu služby SSIS. Tento článek se rozšíří na kurz a ukazuje, jak provádět následující akce: 

- Volitelně můžete použijte Azure SQL Database s virtuální sítě služby koncových bodů nebo spravované Instance (Preview) jako databázový server k hostování vaší služby SSIS katalogu (databáze SSISDB). Předpokladem je, budete muset připojit vaše IR Azure SSIS k virtuální síti a konfigurace virtuální sítě oprávnění a nastavení jako nezbytné, naleznete v [IR Azure SSIS připojit k virtuální síti](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

- Volitelně použijte ověřování Azure Active Directory (AAD) s vaší Azure Data Factory spravované služby Identity (MSI) pro Azure SSIS IR pro připojení k serveru databáze. Předpokladem je, budete muset přidat do skupiny služby AAD s oprávnění k přístupu k serveru databáze MSI vaše Data Factory najdete v tématu [AAD povolit ověřování pro Azure SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-introduction.md). 

## <a name="overview"></a>Přehled
Tento článek ukazuje různé způsoby zřizování Azure SSIS IR: 

- [Azure Portal](#azure-portal) 
- [Azure PowerShell](#azure-powershell) 
- [Šablona Azure Resource Manageru](#azure-resource-manager-template) 

Při vytváření služby Azure SSIS IR objekt pro vytváření dat se připojuje k vaší databázi SQL Azure Příprava databáze katalogu služby SSIS (SSISDB). Skript také konfiguruje nastavení pro virtuální síť a oprávnění, pokud zadaný a připojí novou instanci třídy runtime integrace Azure SSIS do virtuální sítě. 

Když zřizujete instanci Azure-SSIS IR, nainstaluje se také Azure Feature Pack for SSIS a Access Redistributable. Tyto komponenty nabízejí mimo připojení k datovým zdrojům podporovaným integrovanými komponentami navíc možnosti připojení k souborům aplikací Excel a Access a různým datovým zdrojům Azure. Můžete nainstalovat také další komponenty. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). 

## <a name="prerequisites"></a>Požadavky 
- **Předplatné Azure**. Pokud předplatné nemáte, můžete si vytvořit [bezplatný zkušební](http://azure.microsoft.com/pricing/free-trial/) účet. 

- **Azure SQL Database server/spravované Instance (Preview)**. Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Tento server hostuje databázi katalogu služby SSIS (SSISDB). Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration Runtime zapisovat protokoly spuštění do databáze SSISDB bez přecházení mezi oblastmi Azure. Podle serveru vybrané databáze, SSISDB lze vytvářet vaším jménem jako jednu databázi, součást fondu elastické databáze, nebo v instanci spravované (Preview) a přístupné ve veřejné síti nebo připojení k virtuální síti. Seznam podporovaných cenové úrovně pro databázi SQL Azure najdete v tématu [limitů prostředků databáze SQL](../sql-database/sql-database-resource-limits.md). 

    Ujistěte se, že server Azure SQL Database nebo spravované Instance (Preview) ještě nemá katalog služby SSIS (SSIDB databáze). Zřízení Azure-SSIS IR nepodporuje používání existujícího katalogu služby SSIS. 

- **Classic nebo virtuální sítě Azure Resource Manager (volitelné)**. Musí mít virtuální síť Azure, pokud platí aspoň jednu z následujících podmínek: 
    - Jsou hostiteli databáze služby SSIS katalogu v databázi SQL Azure s virtuální sítě služby koncových bodů nebo spravované Instance (Preview), který je uvnitř virtuální sítě. 
    - Z balíčků SSIS spuštěných v prostředí Azure SSIS Integration Runtime se chcete připojit k místním úložištím dat. 

- **Azure PowerShell**. Postupujte podle pokynů v [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps), je-li spustit skript do zřízení Azure SSIS integrace runtime, který spouští balíčky SSIS v cloudu pomocí prostředí PowerShell. 

### <a name="region-support"></a>Oblasti podpory
Vytvoříte objekt pro vytváření dat v následujících oblastech: Východ USA, Východ USA 2, jihovýchodní Asie a západní Evropa. 

Prostředí Azure SSIS IR můžete vytvořit v následujících oblastech: USA – východ, USA – východ 2, USA – střed, USA – západ 2, Severní Evropa, Západní Evropa, Velká Británie – jih a Austrálie – východ. 

### <a name="compare-sql-database-and-managed-instance-preview"></a>Porovnání SQL Database a spravované Instance (Preview)

Následující tabulka porovnává určité funkce SQL Database a spravované Instance (Preview), které se vztahují ke IR Azure SSIR:

| Funkce | SQL Database | MI |
|---------|--------------|------------------|
| **Plánování** | Agent systému SQL Server není k dispozici.<br/><br/>V tématu [naplánovat balíček jako součást kanál služby Azure Data Factory](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages.md#activity).| Agent systému SQL Server je k dispozici. |
| **Ověřování** | Můžete vytvořit databázi pomocí databáze s omezením uživatelského účtu, který reprezentuje všechny uživatele Azure Active Directory v **dbmanager** role.<br/><br/>V tématu [povolení služby Azure AD pro službu Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Nelze vytvořit databázi pomocí databáze s omezením uživatelského účtu, který reprezentuje všechny uživatele Azure Active Directory, než správce služby Azure AD. <br/><br/>V tématu [povolení služby Azure AD na spravované Instance databáze Azure SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Úroveň služeb** | Když vytvoříte IR Azure SSIS pro službu SQL Database, můžete vybrat úroveň služby pro SSISDB. Existuje více úrovně služeb. | Když vytvoříte Azure SSIS Reakcí na instanci spravované, nemůžete vybrat úroveň služby pro SSISDB. Všechny databáze na stejnou instanci spravované sdílet stejné prostředky přidělené do této instance. |
| **Virtuální síť** | Podporuje Azure Resource Manager i klasické virtuální sítě. | Podporuje pouze virtuální sítě Azure Resource Manager. Je potřeba virtuální síť.<br/><br/>Pokud připojíte vaši Azure SSIS IR ke stejné virtuální síti jako spravované Instance, ujistěte se, že IR Azure SSIS je v jiné podsíti, než spravované Instance. Pokud připojíte k jiné virtuální síti než spravované Instance IR Azure SSIS, doporučujeme virtuální sítě partnerského vztahu (která je omezená na stejné oblasti) nebo virtuální sítě k připojení virtuální sítě. V tématu [aplikaci připojit ke spravované Instance serveru Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuované transakce** | Podporované prostřednictvím elastické transakce. Transakce Microsoft Distributed Transaction Coordinator služba MSDTC () nejsou podporovány. Pokud vaše balíčky pomocí služby MSDTC koordinovat distribuované transakce, zvažte migraci elastické Transaction pro databázi SQL. Další informace najdete v tématu [distribuované transakce napříč databázemi cloudu](../sql-database/sql-database-elastic-transactions-overview.md). | Nepodporuje se. |
| | | |

## <a name="azure-portal"></a>Azure Portal
V této části použijete portál Azure, konkrétně Data Factory uživatelského rozhraní, k vytvoření služby Azure SSIS infračerveného signálu. 

### <a name="create-a-data-factory"></a>Vytvoření datové továrny 
1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome. 
2. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/). 
3. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 

   ![Nový -> Objekt pro vytváření dat](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

4. Na stránce **Nová datová továrna** jako **název** zadejte **MyAzureSsisDataFactory**. 

   ![Stránka Nová datová továrna](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název datové továrny (například na vaše_jméno_MyAzureSsisDataFactory) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md). 

   `Data factory name “MyAzureSsisDataFactory” is not available`

5. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
6. Pro **Skupinu prostředků** proveďte jeden z následujících kroků: 

   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 

   Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md). 

7. Jako **verzi** vyberte **V2 (Preview)**. 
8. Vyberte **umístění** pro objekt pro vytváření dat. V seznamu se zobrazí pouze podporovaná umístění pro vytváření datových továren. 
9. Zaškrtněte **Připnout na řídicí panel**. 
10. Klikněte na možnost **Vytvořit**. 
11. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

12. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku. 

    ![Domovská stránka objektu pro vytváření dat](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

13. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure SSIS Integration Runtime 
1. Na stránce Začínáme klikněte na dlaždici **Konfigurace prostředí SSIS Integration Runtime**. 

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Na stránce **Obecná nastavení** v okně **Instalace prostředí Integration Runtime** proveďte následující kroky: 

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Pro **název**, zadejte název vaší integrace modulu runtime. 

    b. Pro **popis**, zadejte popis vaše integrace modulu runtime. 

    c. Pro **umístění**, vyberte umístění vašeho integrace modulu runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění databázového serveru hostitele SSISDB. 

    d. Pro **velikost uzlu**, vyberte velikost uzlu v clusteru integrace modulu runtime. Zobrazí se pouze podporované uzlu velikosti. Vyberte velikost velké uzlu (rozšiřování škálování využívajících), pokud chcete spustit mnoho výpočetních či paměti – náročné balíčky. 

    e. Pro **číslo uzlu**, vyberte počet uzlů v clusteru integrace modulu runtime. Zobrazí se pouze podporované uzlu čísla. Velké cluster s mnoha uzly (škálování), vyberte, pokud chcete spustit mnoho balíčky současně. 

    f. Pro **vydání/licenční**, vyberte edici systému SQL Server nebo licenci pro vaše integrace modulu runtime: Standard nebo Enterprise. Enterprise, vyberte, pokud chcete používat funkce Rozšířené nebo premium na vaše integrace modulu runtime. 

    g. Pro **uložit peníze**, vyberte možnost Azure hybridní Benefit (AHB) pro vaše integrace modulu runtime: Ano nebo ne. Vyberte možnost Ano, pokud chcete, aby vlastní licenci na SQL Server pomocí programu Software Assurance, abyste mohli využívat výhod úsporu nákladů s hybridní použití. 

    h. Klikněte na **Další**. 

3. Na stránce **Nastavení SQL** proveďte následující kroky: 

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Pro **předplatné**, vyberte předplatné Azure, který má váš server databáze SSISDB hostitele. 

    b. Pro **umístění**, vyberte umístění vašeho serveru databáze SSISDB hostitele. Doporučujeme vybrat stejné umístění vaší integrace modulu runtime. 

    c. Pro **koncový bod serveru databáze katalogu**, vyberte koncový bod serveru databáze SSISDB hostitele. Podle serveru vybrané databáze, SSISDB lze vytvářet vaším jménem jako jednu databázi, součást fondu elastické databáze, nebo v instanci spravované (Preview) a přístupné ve veřejné síti nebo připojení k virtuální síti. 

    d. Na **ověřování pomocí AAD...**  zaškrtávací políčko, vyberte metodu ověřování na serveru databáze hostitele SSISDB: SQL nebo Azure Active Directory (AAD) s vaší Azure Data Factory spravované služby Identity (MSI). Pokud ji, budete muset přidat do skupiny služby AAD s oprávnění k přístupu k serveru databáze MSI vaše Data Factory najdete v tématu [AAD povolit ověřování pro Azure SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    e. Pro **uživatelské jméno správce**, zadejte uživatelské jméno ověřování SQL na serveru databáze SSISDB hostitele. 

    f. Pro **heslo správce**, zadejte heslo pro ověřování SQL na serveru databáze SSISDB hostitele. 

    g. Pro **vrstvy služeb databáze katalogu**, vyberte úroveň služby pro databázový server do hostitele SSISDB: úroveň Basic nebo Standard nebo Premium nebo název elastického fondu. 

    h. Klikněte na tlačítko **Test připojení** a pokud bylo úspěšné, klikněte na tlačítko **Další**. 

4.  Na **Upřesnit nastavení** proveďte následující kroky: 

    ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Pro **maximální paralelní spuštěních podle uzlu**, vyberte maximální počet balíčků, které mají spustit souběžně na každém uzlu v clusteru integrace modulu runtime. Podporuje jenom balíčku, který se zobrazí čísla. Vyberte nízké číslo, pokud chcete použít víc než jednoho jádra spusťte jeden velké nebo zobrazené – balíček, který je výpočetní či paměti-náročné. Vysoké číslo, vyberte, pokud chcete spustit jeden nebo více balíčků malá nebo šedé – v jediného jádra. 

    b. Pro **vlastní instalační program kontejneru SAS URI**volitelně zadejte sdíleného přístupového podpisu (SAS) identifikátor URI (Uniform Resource) vašeho kontejneru Azure Storage Blob, kde jsou uložené instalační skript a jeho přidružené soubory, najdete v části [Vlastní instalace pro Azure SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

5. Na **výběr virtuální sítě...**  zaškrtávací políčko, vyberte, zda se chcete připojit k virtuální síti vaše integrace modulu runtime. Zkontroluje, pokud používáte Azure SQL Database s virtuální sítě služby koncových bodů nebo spravované Instance (Preview) na hostitele SSISDB nebo vyžadují přístup k místním datům; To znamená mít místní zdroje nebo cíle dat ve vašich balíčcích SSIS najdete v tématu [IR Azure SSIS připojit k virtuální síti](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud ji, proveďte následující kroky: 

   ![Pokročilé nastavení s virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Pro **předplatné**, vyberte předplatné Azure, který má k virtuální síti. 

    b. Pro **umístění**, stejné umístění vaší běhového modulu integrace je vybrané. 

    c. Pro **typu**, vyberte typ virtuální sítě: Classic nebo Azure Resource Manager. Doporučujeme vybrat virtuální síť Azure Resource Manager od Classic bude brzy zastaralá virtuální sítě. 

    d. Pro **název virtuální sítě**, vyberte název virtuální sítě. Tato virtuální síť musí být stejné virtuální síti použít pro databázi SQL Azure s virtuální sítě služby koncových bodů nebo spravované Instance (Preview) pro hostování a SSISDB nebo jeden připojený k síti na pracovišti. 

    e. Pro **název podsítě**, vyberte název podsítě pro vaši virtuální síť. To by měl být jiné podsíti než ten, který používá pro spravované Instance (Preview) na hostitele SSISDB. 

6. Klikněte na tlačítko **ověření virtuální sítě** a pokud bylo úspěšné, klikněte na tlačítko **Dokončit** zahájíte vytváření vašeho runtime integrace Azure SSIS. 

    > [!IMPORTANT]
    > - Tento proces trvá přibližně 20-30 minut
    > - Služba Data Factory se připojí k vaší službě Azure SQL Database a připraví databázi katalogu služby SSIS (SSISDB). Skript také konfiguruje nastavení pro virtuální síť a oprávnění, pokud zadaný a připojí novou instanci třídy runtime integrace Azure SSIS do virtuální sítě. 

7. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Kliknutím na **Aktualizovat** aktualizujte stav. 

   ![Stav vytváření](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Použijte odkazy v části **akce** sloupec, který se zastavit a spustit, upravit nebo odstranit integrace modulu runtime. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené. 

   ![Azure SSIS IR – akce](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Prostředí Azure SSIS Integration Runtime na portálu
1. V uživatelském prostředí služby Azure Data Factory přepněte na kartu **Upravit**, klikněte na **Připojení** a pak přepněte zpět na kartu **Prostředí Integration Runtime**, kde se zobrazí existující prostředí Integration Runtime ve vaší datové továrně. 

   ![Finanční úřad stávajících zobrazení](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Kliknutím na **Nový** vytvořte nové prostředí Azure-SSIS IR. 

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Pokud chcete vytvořit prostředí Azure-SSIS Integration Runtime, klikněte na **Nový**, jak je znázorněno na obrázku. 
4. V okně Instalace prostředí Integration Runtime vyberte **Migrovat metodou „lift and shift“ existující balíčky služby SSIS ke spuštění v Azure** a pak klikněte na **Další**. 

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Zbývající kroky k nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="azure-powershell"></a>Azure PowerShell
V této části použijte prostředí Azure PowerShell k vytvoření služby Azure SSIS infračerveného signálu.

### <a name="create-variables"></a>Vytvoření proměnných
Definujte proměnné, které se použijí ve skriptech v tomto kurzu:

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"
```

### <a name="log-in-and-select-subscription"></a>Přihlášení a výběr předplatného
Přidejte následující kód skriptu se přihlaste a vyberte předplatné Azure: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Ověření připojení k databázi
Přidejte následující skript, který chcete ověřit váš koncový bod serveru Azure SQL Database. 

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
Spusťte následující příkazy k vytvoření modulu runtime integrace Azure SSIS, který běží v Azure balíčky SSIS. 

Pokud nepoužijete Azure SQL Database pomocí virtuální sítě služby koncových bodů nebo spravované Instance (Preview) hostitele SSISDB ani vyžadují přístup k místním datům, můžete vynechat parametry VNetId a podsíť nebo předat prázdné hodnoty pro ně. Jinak, nelze je vynechejte a musíte předat platné hodnoty z konfiguraci virtuální sítě najdete [IR Azure SSIS připojit k virtuální síti](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

Pokud používáte spravované Instance (Preview) na hostitele SSISDB, můžete vynechejte parametr CatalogPricingTier nebo pro ni předat prázdnou hodnotu. Jinak, nelze ji vynechat a musíte předat platnou hodnotu ze seznamu podporované cenové úrovně pro databázi SQL Azure, najdete v části [limitů prostředků databáze SQL](../sql-database/sql-database-resource-limits.md). 

Pokud ověřování Azure Active Directory (AAD) používáte pro připojení k serveru databáze s vaší Azure Data Factory spravované služby Identity (MSI), můžete vynechat CatalogAdminCredential parametr, ale vaše Data Factory MSI je nutné přidat do skupiny služby AAD s přístupem oprávnění k databázovému serveru, najdete v části [AAD povolit ověřování pro Azure SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Jinak nemůžete vynechat a musí projít platný objekt vytvořen z svoje uživatelské jméno správce serveru a heslo pro ověřování serveru SQL.

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
Zde je úplná skript, který vytvoří modulu runtime integrace Azure SSIS. 

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"

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
V této části použijete k vytvoření runtime integrace Azure SSIS šablony Azure Resource Manageru. Zde je ukázka návod: 

1. Vytvořte soubor JSON s následující šablony Azure Resource Manager. Nahraďte vlastními hodnotami hodnoty v lomené závorky (zástupného). 

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
    
2. Pokud chcete nasadit šablonu Azure Resource Manager, spusťte příkaz New-AzureRmResourceGroupDeployment, jak je znázorněno v následujícím příkladu, kde ADFTutorialResourceGroup je název vaší skupiny prostředků a C:\adfgetstarted je soubor, který obsahuje JSON definici objektu pro vytváření dat a Azure SSIS infračerveného signálu. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Tento příkaz vytvoří objekt pro vytváření dat a Azure SSIS reakcí na Incidenty v něm, ale nespustí infračerveného signálu. 

3. Spuštění vaší IR Azure SSIS, spusťte příkaz Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS
Teď použijte SQL Server Data Tools (SSDT) nebo aplikaci SQL Server Management Studio (SSMS) k nasazení vašich balíčků SSIS do Azure. Připojte k databázovému serveru, který je hostitelem katalogu služby SSIS (SSISDB). Název databáze serveru je ve formátu: &lt;název serveru Azure SQL Database&gt;. database.windows.net nebo &lt;název spravované Instance (Preview). Předpona DNS&gt;. database.windows.net. Pokyny najdete v článku věnovaném [nasazení balíčků](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

## <a name="next-steps"></a>Další postup
Najdete v dalších tématech IR Azure SSIS v této dokumentaci: 

- [Modul Runtime integrace Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o integraci runtimes obecně včetně infračerveného signálu Azure SSIS. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR a využívá databázi Azure SQL k hostování katalogu SSIS. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Ukazuje také postup horizontálního navýšení kapacity Azure-SSIS IR přidáním více uzlů. 
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek obsahuje koncepční informace o propojení vaší Azure SSIS Reakcí do virtuální sítě Azure. Poskytuje také kroky pro využití webu Azure Portal ke konfiguraci virtuální sítě, aby se k ní prostředí Azure-SSIS IR mohlo připojit. 
