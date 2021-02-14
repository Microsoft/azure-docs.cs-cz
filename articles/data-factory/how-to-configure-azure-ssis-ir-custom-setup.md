---
title: Přizpůsobení nastavení pro Azure-SSIS Integration Runtime
description: Tento článek popisuje, jak použít vlastní rozhraní pro instalaci pro Azure-SSIS Integration Runtime k instalaci dalších součástí nebo změně nastavení.
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: b1b3a34ac495936ed92e29353a41efb8c462768f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387729"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Přizpůsobení nastavení pro Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pomocí vlastních nastavení můžete v Azure Data Factory (ADF) služba SSIS (SQL Server Integration Services) přizpůsobit Integration Runtime SSIS (IR). Umožňují přidat vlastní kroky během zřizování nebo opětovné konfigurace Azure-SSIS IR. 

Pomocí vlastních nastavení můžete změnit výchozí konfiguraci operačního systému nebo prostředí vašeho Azure-SSIS IR. Chcete-li například spustit další služby systému Windows, zachovat přihlašovací údaje pro sdílení souborů nebo použít silné šifrování/bezpečnější síťový protokol (TLS 1,2). Případně můžete do každého uzlu Azure-SSIS IR nainstalovat další komponenty, jako jsou například sestavení, ovladače nebo rozšíření. Můžou se jednat o vlastní, Open Source nebo komponenty třetích stran. Další informace o předdefinovaných/předinstalovaných součástech najdete v tématu [vestavěné/předinstalované komponenty na Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).

Vlastní nastavení můžete provést na svém Azure-SSIS IR jedním ze dvou způsobů: 
* **Standardní vlastní nastavení pomocí skriptu**: Připravte skript a jeho přidružené soubory a nahrajte je dohromady do kontejneru objektů BLOB v účtu úložiště Azure. Když nastavíte nebo znovu nakonfigurujete Azure-SSIS IR, zadáte identifikátor URI (Uniform Resource Identifier) sdíleného přístupového podpisu (SAS) pro váš kontejner. Každý uzel vaší Azure-SSIS IR pak stáhne skript a jeho přidružené soubory z vašeho kontejneru a spustí vlastní instalaci se zvýšenými oprávněními. Po dokončení vlastní instalace každý uzel nahraje standardní výstup spuštění a další protokoly do vašeho kontejneru.
* **Expresní vlastní nastavení bez skriptu**: Spusťte některé běžné konfigurace systému a příkazy systému Windows nebo nainstalujte některé oblíbené nebo Doporučené dodatečné součásti bez použití skriptů.

Můžete nainstalovat bezplatné (nelicencované) a placené (licencované) komponenty s vlastními nastaveními Standard a Express. Pokud jste nezávislý výrobce softwaru (ISV), přečtěte si téma [vývoj placených nebo licencovaných komponent pro Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Pro výhody budoucích vylepšení doporučujeme pro vaši Azure-SSIS IR použít pro vlastní instalaci řadu uzlů V3 nebo novější.

## <a name="current-limitations"></a>Aktuální omezení

Následující omezení platí pouze pro standardní vlastní nastavení:

- Pokud chcete použít *gacutil.exe* ve vašem skriptu k instalaci sestavení v globální mezipaměti sestavení (GAC), je nutné zadat *gacutil.exe* jako součást vlastního nastavení. Případně můžete použít kopii, která je k dispozici ve *vzorové* složce našeho kontejneru *Public Preview* , v níže uvedené standardní části s ukázkami pro **vlastní nastavení** .

- Pokud chcete odkazovat na podsložku ve skriptu, *msiexec.exe* nepodporuje `.\` zápis pro odkaz na kořenovou složku. Použijte příkaz, například `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` místo `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` .

- Sdílené složky pro správu nebo skryté sdílené síťové složky, které jsou automaticky vytvořeny systémem Windows, nejsou aktuálně podporovány v Azure-SSIS IR.

- Ovladač IBM iSeries Access ODBC není podporován na Azure-SSIS IR. Při vlastní instalaci se může zobrazit chyba instalace. Pokud to uděláte, požádejte o pomoc podporu společnosti IBM.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K přizpůsobení Azure-SSIS IR budete potřebovat následující položky:

- [Předplatné Azure](https://azure.microsoft.com/)

- [Zřízení Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md)

- [Účet služby Azure Storage](https://azure.microsoft.com/services/storage/). Nepožaduje se pro expresní vlastní nastavení. Pro standardní vlastní nastavení nahráváte a ukládáte vlastní instalační skript a jeho přidružené soubory do kontejneru objektů BLOB. Vlastní proces instalace také nahraje své protokoly spuštění do stejného kontejneru objektů BLOB.

## <a name="instructions"></a>Pokyny

Můžete zřídit nebo znovu nakonfigurovat Azure-SSIS IR vlastními nastaveními v uživatelském rozhraní ADF. Pokud chcete stejný postup provést pomocí PowerShellu, Stáhněte a nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Standardní vlastní nastavení

Pokud chcete zřídit nebo znovu nakonfigurovat Azure-SSIS IR se standardními vlastními nastaveními v uživatelském rozhraní ADF, proveďte následující kroky.

1. Připravte vlastní instalační skript a jeho přidružené soubory (například. bat,. cmd,. exe,. dll,. msi nebo. ps1).

   * Musíte mít soubor skriptu s názvem *Main. cmd*, který je vstupním bodem vlastní instalace.  
   * Chcete-li zajistit, aby se skript mohl spouštět tiše, měli byste ho nejdřív otestovat na svém místním počítači.  
   * Pokud chcete, aby byly do kontejneru nahrány další protokoly vygenerované jinými nástroji (například *msiexec.exe*), zadejte předdefinovanou proměnnou prostředí, `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako složku protokolu ve skriptech (například *msiexec/i xxx.msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ Install. log*).

1. Stáhněte, nainstalujte a otevřete [Průzkumník služby Azure Storage](https://storageexplorer.com/).

   a. V části **(místní a připojené)** klikněte pravým tlačítkem na **účty úložiště** a pak vyberte **připojit k Azure Storage**.

      ![Připojení ke službě Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Vyberte **použít název a klíč účtu úložiště** a pak vyberte **Další**.

      ![Použití názvu a klíče účtu úložiště](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Zadejte název a klíč účtu služby Azure Storage, klikněte na tlačítko **Další** a pak vyberte **připojit**.

      ![Zadejte název a klíč účtu úložiště.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. V rámci připojeného účtu úložiště Azure klikněte pravým tlačítkem na **kontejnery objektů BLOB**, vyberte **vytvořit kontejner objektů BLOB** a pojmenujte nový kontejner.

      ![Vytvoření kontejneru objektů blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Vyberte nový kontejner a nahrajte vlastní instalační skript a jeho přidružené soubory. Ujistěte se, že jste nahráli *Main. cmd* na nejvyšší úrovni kontejneru, a ne do žádné složky. Váš kontejner by měl obsahovat jenom nezbytné vlastní instalační soubory, aby je bylo možné stáhnout do Azure-SSIS IR později nebude trvat dlouhou dobu. Maximální doba vlastní instalace je v současnosti nastavená na 45 minut před vypršením časového limitu. To zahrnuje čas ke stažení všech souborů z vašeho kontejneru a jejich instalaci na Azure-SSIS IR. Pokud instalace vyžaduje více času, vyvolejte lístek podpory.

      ![Nahrání souborů do kontejneru objektů BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Klikněte pravým tlačítkem na kontejner a pak vyberte **získat sdílený přístupový podpis**.

      ![Získání sdíleného přístupového podpisu pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   například Vytvořte identifikátor URI SAS pro váš kontejner s dostatečně dlouhou dobou vypršení platnosti a s oprávněním pro čtení/zápis/seznam. Identifikátor URI SAS potřebujete ke stažení a spuštění vlastního instalačního skriptu a jeho přidružených souborů. K tomu dochází vždy, když se kterýkoli uzel Azure-SSIS IR obnoví nebo restartuje. Také potřebujete oprávnění k zápisu pro nahrání protokolů spuštění instalace.

      > [!IMPORTANT]
      > Zajistěte vypršení platnosti identifikátoru URI SAS a vlastní prostředky instalace budou vždy k dispozici během celého životního cyklu Azure-SSIS IR, od vytvoření po odstranění, zejména v případě, že během této doby pravidelně zastavíte a zahájíte Azure-SSIS IR.

      ![Generování sdíleného přístupového podpisu pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Zkopírujte a uložte identifikátor URI SAS vašeho kontejneru.

      ![Zkopírování a uložení sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Zaškrtněte políčko **přizpůsobit Azure-SSIS Integration runtime pomocí dalších systémových konfigurací/instalací součástí** na stránce **Upřesnit nastavení** v podokně **instalace prostředí Integration runtime** . Potom do textového pole **identifikátor URI SAS kontejneru vlastní instalace** zadejte identifikátor URI SAS vašeho kontejneru.

   ![Rozšířená nastavení s vlastními nastaveními](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

Po dokončení standardní vlastní instalace a spuštění Azure-SSIS IR můžete najít všechny vlastní protokoly instalace ve složce *Main. cmd. log* vašeho kontejneru. Zahrnují standardní výstup *Main. cmd* a dalších protokolů spuštění.

### <a name="express-custom-setup"></a>Expresní vlastní instalace

Pokud chcete zřídit nebo znovu nakonfigurovat Azure-SSIS IR pomocí expresního vlastního nastavení v uživatelském rozhraní ADF, proveďte následující kroky.

1. Zaškrtněte políčko **přizpůsobit Azure-SSIS Integration runtime pomocí dalších systémových konfigurací/instalací součástí** na stránce **Upřesnit nastavení** v podokně **instalace prostředí Integration runtime** . 

1. Výběrem **nové** otevřete podokno **Přidat vlastní nastavení Express** a potom vyberte typ v rozevíracím seznamu **typ vlastní instalace Express** . V současnosti nabízíme příkaz pro okamžité vlastní nastavení pro spuštěný příkaz cmdkey, přidávání proměnných prostředí, instalaci Azure PowerShell a instalaci licencovaných komponent.

#### <a name="running-cmdkey-command"></a>Spuštění příkazu cmdkey

Pokud pro vlastní instalaci Express vyberete typ **příkazu Spustit cmdkey** , můžete na svém Azure-SSIS IR spustit příkaz Windows cmdkey. Provedete to tak, že do textových polí **/Add**, **/User** a **/Pass** zadáte název svého cílového počítače nebo název domény, uživatelské jméno nebo název účtu a heslo nebo klíč účtu. To vám umožní zachovat přihlašovací údaje pro přístup k serverům SQL, sdíleným složkám nebo souborům Azure na vašich Azure-SSIS IR. Například pro přístup k souborům Azure můžete zadat `YourAzureStorageAccountName.file.core.windows.net` , `azure\YourAzureStorageAccountName` a `YourAzureStorageAccountKey` pro **/Add**, **/User** a **/Pass**, v uvedeném pořadí. To se podobá spuštění příkazu Windows [cmdkey](/windows-server/administration/windows-commands/cmdkey) na místním počítači. Pro nyní je podporována pouze jedna expresní vlastní instalace pro spuštění příkazu cmdkey. Chcete-li spustit více příkazů cmdkey, použijte raději standardní vlastní instalaci.

#### <a name="adding-environment-variables"></a>Přidávání proměnných prostředí

Pokud pro vlastní instalaci Express vyberete typ **proměnné prostředí** , můžete na svém Azure-SSIS IR přidat proměnnou prostředí Windows. Provedete to tak, že zadáte název proměnné prostředí a hodnotu do textových polí **název** proměnné a **hodnota proměnné** v uvedeném pořadí. To vám umožní použít proměnnou prostředí v balíčcích, které běží na Azure-SSIS IR, například v komponentách skriptu nebo úlohách. To se podobá spuštění příkazu Windows [set](/windows-server/administration/windows-commands/set_1) na místním počítači.

#### <a name="installing-azure-powershell"></a>Instalace Azure PowerShell

Pokud pro vlastní instalaci Express vyberete typ **instalace Azure PowerShell** , můžete do Azure-SSIS IR nainstalovat modul AZ Module prostředí PowerShell. Uděláte to tak, že v [seznamu podporovaných šablon](https://www.powershellgallery.com/packages/az)zadáte příkaz AZ Module verze Number (x. y. z). To vám umožní spouštět Azure PowerShell rutiny nebo skripty v balíčcích pro správu prostředků Azure, například [Azure Analysis Services (AAS)](../analysis-services/analysis-services-powershell.md).

#### <a name="installing-licensed-components"></a>Instalace licencovaných komponent

Pokud vyberete možnost **instalovat licencovaný typ součásti** pro aplikaci Express Custom, pak můžete vybrat integrovanou součást z našich partnerů ISV v rozevíracím seznamu **název součásti** :

   * Pokud vyberete součást **Task Factory pro SentryOne** , můžete na Azure-SSIS IR nainstalovat sadu funkcí [Factory úloh](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) z SentryOne. Provedete to tak, že do textového pole **licenční klíč** zakoupíte předem zakoupený licenční kód produktu. Aktuální integrovaná verze je **2020.1.3**.

   * Pokud vyberete **OH22'S HEDDA. V/** v součást, můžete nainstalovat [HEDDA. Vstupně-výstupní operace](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) s daty/čisticí součást z oh22 na vašich Azure-SSIS IR. K tomu je potřeba nejdřív zakoupit službu. Aktuální integrovaná verze je **1.0.14**.

   * Pokud vyberete komponentu **oh22's SQLPhonetics.NET** , můžete nainstalovat komponentu [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) Data Quality/Matching z oh22 na svém Azure-SSIS IR. Provedete to tak, že do textového pole **licenční klíč** zakoupíte předem zakoupený licenční kód produktu. Aktuální integrovaná verze je **1.0.45**.

   * Pokud vyberete komponentu **KingswaySoft Integration Toolkit pro SSIS** , můžete nainstalovat konektory sady [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) pro aplikace CRM/ERP/marketing a spolupráci, jako je například Microsoft Dynamics/SharePoint/Project Server, marketingový Cloud Oracle nebo Salesforce, atd. z KingswaySoft na Azure-SSIS IR. Provedete to tak, že do textového pole **licenční klíč** zakoupíte předem zakoupený licenční kód produktu. Aktuální integrovaná verze je **2020,1**.

   * Pokud vyberete komponentu **SSIS produktivního balíku KingswaySoft** , můžete nainstalovat sadu funkcí pro [SSIS produktivní](https://www.kingswaysoft.com/products/ssis-productivity-pack) sadu komponent z KingswaySoft na své Azure-SSIS IR. Provedete to tak, že do textového pole **licenční klíč** zakoupíte předem zakoupený licenční kód produktu. Aktuální integrovaná verze je **20,1**.

   * Pokud vyberete **Xtrahovat software Theobald** , můžete nainstalovat [xtrahovat je](https://theobald-software.com/en/xtract-is/) sada konektorů pro systémy SAP (ERP, s/4HANA, černobílý) od Theobald softwaru na vašich Azure-SSIS IR. Provedete to tak, že přetáhnete & přetáhněte nebo nahrajte soubor licence produktu, který jste si z nich koupili, do vstupního pole **soubor s licencí** . Aktuální integrovaná verze je **6.1.1.3**.

   * Pokud vyberete součást **integrační služby AecorSoft** , můžete [nainstalovat sadu](https://www.aecorsoft.com/en/products/integrationservice) konektorů konektorů pro systémy SAP a Salesforce z AecorSoft na své Azure-SSIS IR. Provedete to tak, že do textového pole **licenční klíč** zakoupíte předem zakoupený licenční kód produktu. Aktuální integrovaná verze je **3.0.00**.

   * Vyberete-li **standardní součást balíčku CDATA SSIS** , můžete nainstalovat sadu [SSIS Standard](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) pro nejoblíbenější součásti z CDATA, například konektory služby Microsoft SharePoint, na Azure-SSIS IR. Provedete to tak, že do textového pole **licenční klíč** zakoupíte předem zakoupený licenční kód produktu. Aktuální integrovaná verze je **19,7354**.

   * Pokud vyberete součást **balíčku CDATA Extended Package SSIS** , můžete na svém Azure-SSIS IR nainstalovat sadu [SSIS Extended Package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) pro všechny komponenty z CDATA, například Microsoft Dynamics 365 Business Central Connectors a další komponenty v jejich **standardním balíčku SSIS**. Provedete to tak, že do textového pole **licenční klíč** zakoupíte předem zakoupený licenční kód produktu. Aktuální integrovaná verze je **19,7354**. Vzhledem k velké velikosti, aby nedocházelo k vypršení časového limitu instalace, zajistěte, aby měl váš Azure-SSIS IR aspoň 4 jádra procesoru na jeden uzel.

Přidaná expresní vlastní nastavení se zobrazí na stránce **Upřesnit nastavení** . Pokud je chcete odebrat, zaškrtněte příslušná políčka a potom vyberte **Odstranit**.

### <a name="azure-powershell"></a>Azure PowerShell

Při zřizování nebo překonfigurování Azure-SSIS IR vlastními nastaveními pomocí Azure PowerShell proveďte následující kroky.

1. Pokud je vaše Azure-SSIS IR už spuštěná nebo spuštěná, zastavte ho jako první.

1. Pak můžete přidat nebo odebrat vlastní nastavení spuštěním `Set-AzDataFactoryV2IntegrationRuntime` rutiny před zahájením Azure-SSIS IR.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>Ukázky standardních vlastních nastavení

Pokud chcete zobrazit a znovu použít některé ukázky standardních vlastních nastavení, proveďte následující kroky.

1. Připojte se k našemu Public Preview kontejneru pomocí Průzkumník služby Azure Storage.

   a. V části **(místní a připojená)** klikněte pravým tlačítkem na **účty úložiště**, vyberte **připojit k Azure Storage**, vyberte **Použít připojovací řetězec nebo identifikátor URI sdíleného přístupového podpisu** a pak vyberte **Další**.

      ![Připojení ke službě Azure Storage pomocí sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Vyberte **použít identifikátor URI SAS** a potom do textového pole **URI** zadejte následující identifikátor URI SAS:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Zadejte sdílený přístupový podpis pro kontejner.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Vyberte **Další** a pak vyberte **připojit**.

   d. V levém podokně vyberte připojený kontejner **publicpreview** a dvakrát klikněte na složku *CustomSetupScript* . V této složce jsou následující položky:

      * *Ukázková* složka, která obsahuje vlastní instalaci pro instalaci základní úlohy do každého uzlu vašeho Azure-SSIS IR. Úkol neprovede žádnou akci, ale během několika sekund režim spánku. Složka obsahuje taky složku *Gacutil* , jejíž celý obsah (*gacutil.exe*, *gacutil.exe.config* a *1033\gacutlrc.dll*) se dá zkopírovat jako do vašeho kontejneru.

      * Složka *UserScenarios* , která obsahuje několik vlastních ukázek instalace ze scénářů reálných uživatelů. Pokud chcete do svého Azure-SSIS IR nainstalovat více ukázek, můžete zkombinovat vlastní instalační soubory skriptu (*Main. cmd*) do jednoho a nahrát ho do kontejneru pomocí všech přidružených souborů.

        ![Obsah kontejneru verze Public Preview](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Dvojím kliknutím na složku *UserScenarios* vyhledejte následující položky:

      * Složka *rozhraní .NET FRAMEWORK 3,5* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci starší verze .NET Framework do každého uzlu Azure-SSIS IR. Tato verze může být vyžadována některými vlastními součástmi.

      * Složka *BCP* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci SQL Server nástrojů příkazového řádku (*MsSqlCmdLnUtils.msi*) do každého uzlu Azure-SSIS IR. Jedním z těchto nástrojů je program hromadného kopírování (*BCP*).

      * Složka *přípon DNS* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro připojení vlastní přípony DNS (například *test.com*) k libovolnému nekvalifikovanému názvu domény s názvem bez přípony a jejich převeďte na plně kvalifikovaný název domény (FQDN), než je použijete v dotazech DNS z vašeho Azure-SSIS IR.

      * Složka *aplikace Excel* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci některých sestavení a knihoven v jazyce C# do každého uzlu Azure-SSIS IR. Můžete je použít v úlohách skriptu k dynamickému čtení a psaní excelových souborů. 
      
        Nejdřív stáhněte [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) a [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)a pak je nahrajte dohromady pomocí *Main. cmd* do kontejneru. Případně, pokud chcete pouze používat standardní konektory aplikace Excel (Správce připojení, zdroj a cíl), je již v Azure-SSIS IR předinstalována aplikace Access Redistributable, která je obsahuje, takže nepotřebujete žádnou vlastní instalaci.
      
      * Složka *MySQL ODBC* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci ovladačů MySQL odbc do každého uzlu Azure-SSIS IR. Tato instalace vám umožní připojit se k serveru MySQL pomocí konektorů rozhraní ODBC (Správce připojení, zdroje a cíle). 
     
        Nejdřív [Stáhněte nejnovější verze 64 a 32 instalačních programů ovladačů MySQL ODBC](https://dev.mysql.com/downloads/connector/odbc/) (například *mysql-connector-odbc-8.0.13-winx64.msi* a *mysql-connector-odbc-8.0.13-win32.msi*) a pak je nahrajte společně s *Main. cmd* do kontejneru.

      * Složka *Oracle Enterprise* , která obsahuje skript vlastního nastavení (*Main. cmd*) a konfigurační soubor bezobslužné instalace (*Client. rsp*) pro instalaci konektoru Oracle a ovladače OCI do každého uzlu vaší Azure-SSIS IR Enterprise Edition. Tato instalace vám umožní připojit se k serveru Oracle pomocí Správce připojení Oracle, zdroje a cíle. 
      
        Nejdřív Stáhněte konektory Microsoft Connectors v 5.0 pro Oracle (*AttunitySSISOraAdaptersSetup.msi* a *AttunitySSISOraAdaptersSetup64.msi*) z [webu Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) a nejnovějšího klienta Oracle (například *winx64_12102_client.zip*) od [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html). Dále je nahrajte do svého kontejneru společně s *Main. cmd* a *Client. rsp* . Pokud k připojení k Oracle používáte TNS, musíte si také stáhnout *souboru Tnsnames. Ora*, upravit ho a nahrát ho do svého kontejneru. Tímto způsobem je lze během instalace zkopírovat do instalační složky Oracle.

      * *Standardní složka ADO.NET Oracle* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci ovladače Oracle ODP.NET do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní připojit se k serveru Oracle pomocí Správce připojení ADO.NET, zdroje a cíle. 
      
        Nejdřív [Stáhněte nejnovější ovladač Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (například *ODP.NET_Managed_ODAC122cR1.zip*) a pak ho nahrajte společně s *Main. cmd* do kontejneru.
       
      * *Standard Oracle* , který obsahuje skript pro vlastní nastavení (*Main. cmd*), který nainstaluje ovladač Oracle ODBC do každého uzlu vašeho Azure-SSIS IR. Skript také nakonfiguruje název zdroje dat (DSN). Tato instalace umožňuje použít Správce připojení ODBC, zdroj a cíl nebo Power Query Správce připojení a zdroj s typem zdroje dat ODBC pro připojení k serveru Oracle. 
      
        Nejdřív Stáhněte nejnovější verzi pro okamžitého klienta Oracle (základní balíček nebo základní balíček verze Lite) a balíček ODBC a nahrajte ho dohromady pomocí *Main. cmd* do svého kontejneru:
        * [Stáhnout 64 – bitové balíčky](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (základní balíček: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Základní balíček verze Lite: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; Balíček ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Stáhnout 32 – bitové balíčky](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (základní balíček: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Základní balíček verze Lite: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Balíček ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * *Standardní složka OLEDB Oracle* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci ovladače Oracle OLEDB do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní připojit se k serveru Oracle pomocí Správce připojení OLEDB, zdroje a cíle. 
     
        Nejdřív [Stáhněte nejnovější ovladač Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (například *ODAC122010Xcopy_x64.zip*) a pak ho nahrajte společně s *Main. cmd* do kontejneru.

      * *POSTGRESQL složka ODBC* , která obsahuje skript vlastního nastavení (*Main. cmd*), který nainstaluje ovladače POSTGRESQL ODBC do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní připojit se k PostgreSQL serveru pomocí Správce připojení ODBC, zdroje a cíle. 
     
        Nejdřív [Stáhněte nejnovější 64 a 32 PostgreSQL instalačních ovladačů ovladačů ODBC](https://www.postgresql.org/ftp/odbc/versions/msi/) (například *psqlodbc_x64.msi* a *psqlodbc_x86.msi*) a pak je nahrajte dohromady pomocí *Main. cmd* do kontejneru.

      * *SAP BW* složky, která obsahuje skript vlastního nastavení (*Main. cmd*) pro instalaci sestavení SAP .NET Connector (*librfc32.dll*) na každý uzel vaší Azure-SSIS IR Enterprise Edition. Tato instalace vám umožní připojit se k serveru SAP BW pomocí SAP BW Správce připojení, zdroj a cíl. 
      
        Nejdřív nahrajte 64 nebo 32 verzi *librfc32.dll* z instalační složky SAP společně s *Main. cmd* do svého kontejneru. Skript pak zkopíruje sestavení SAP do složky *%windir%\syswow64* nebo *%windir%\System32* během instalace.

      * Složka *úložiště* , která obsahuje skript vlastního nastavení (*Main. cmd*), který se má nainstalovat Azure PowerShell v každém uzlu Azure-SSIS IR. Tato instalace umožňuje nasadit a spustit balíčky SSIS, které spouští [rutiny a skripty Azure PowerShell pro správu Azure Storage](../storage/blobs/storage-quickstart-blobs-powershell.md). 
      
        Zkopírujte *Main. cmd*, vzorový *AzurePowerShell.msi* (nebo použijte nejnovější verzi) a *storage.ps1* do svého kontejneru. Jako šablonu pro balíčky použijte *PowerShell. dtsx* . Šablona balíčku kombinuje [úlohu stažení objektu BLOB v Azure](/sql/integration-services/control-flow/azure-blob-download-task), která stáhne upravitelný skript PowerShellu (*storage.ps1*) a [úlohu spuštění procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), která spustí skript na každém uzlu.

      * Složka *Teradata* , která obsahuje skript vlastního nastavení (*Main. cmd*), přidružený soubor (*install. cmd*) a instalační balíčky (*. msi*). Tyto soubory instalují konektory Teradata, rozhraní API Teradata Parallel Transporter (TPT) a ovladač ODBC na každém uzlu vaší Azure-SSIS IR Enterprise Edition. Tato instalace vám umožní připojit se k serveru Teradata pomocí Správce připojení Teradata, zdroj a cíl. 
      
        Nejprve [si stáhněte soubor zip Tools a Utilities 15. x](http://partnerintelligence.teradata.com) (například  *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) a pak ho nahrajte společně s dříve uvedenými soubory *. cmd* a *. msi* do kontejneru.

      * Složka *TLS 1,2* , která obsahuje skript vlastního nastavení (*Main. cmd*) pro použití silné kryptografie a bezpečnějšího síťového protokolu (TLS 1,2) na každém uzlu vašeho Azure-SSIS IR. Skript také zakáže starší verze protokolu SSL/TLS.

      * Složka *Zulu OPENJDK* , která obsahuje skript vlastního nastavení (*Main. cmd*) a soubor PowerShellu (*install_openjdk.ps1*) pro instalaci Zulu OPENJDK do každého uzlu Azure-SSIS IR. Tato instalace umožňuje použít Azure Data Lake Store a flexibilní konektory souborů ke zpracování souborů ORC a Parquet. Další informace najdete v tématu [Azure Feature Pack pro integrační služby](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java). 
      
        Nejdřív [Stáhněte nejnovější Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (například *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) a pak ho nahrajte společně s *Main. cmd* a *install_openjdk.ps1* do svého kontejneru.

        ![Složky ve složce scénáře pro uživatele](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Chcete-li znovu použít tyto standardní ukázky pro vlastní nastavení, zkopírujte obsah vybrané složky do kontejneru.

1. Při zřizování nebo překonfigurování Azure-SSIS IR v uživatelském rozhraní ADF zaškrtněte políčko **přizpůsobit Azure-SSIS Integration runtime pomocí dalších systémových konfigurací/instalací součástí** na stránce **Upřesnit nastavení** v podokně **instalace prostředí Integration runtime** . Potom do textového pole **identifikátor URI SAS kontejneru vlastní instalace** zadejte identifikátor URI SAS vašeho kontejneru.
   
1. Když zřídíte nebo překonfigurujete Azure-SSIS IR pomocí Azure PowerShell, zastavte ho, pokud už je spuštěný nebo spuštěný, spusťte `Set-AzDataFactoryV2IntegrationRuntime` rutinu s identifikátorem URI SAS vašeho kontejneru jako hodnotu `SetupScriptContainerSasUri` parametru a pak spusťte Azure-SSIS IR.

1. Po dokončení standardní vlastní instalace a spuštění Azure-SSIS IR můžete najít všechny vlastní protokoly instalace ve složce *Main. cmd. log* vašeho kontejneru. Zahrnují standardní výstup *Main. cmd* a dalších protokolů spuštění.

## <a name="next-steps"></a>Další kroky

- [Nastavte edici Enterprise Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Vývoj placených nebo licencovaných komponent pro Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
