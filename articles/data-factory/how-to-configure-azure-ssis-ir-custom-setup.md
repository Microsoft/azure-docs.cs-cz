---
title: Přizpůsobení nastavení pro runtime integrace Azure-SSIS
description: Tento článek popisuje použití vlastního instalačního rozhraní pro modul Runtime integrace Azure-SSIS k instalaci dalších součástí nebo změně nastavení
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: ab2ba31d6b712bd3399bc8bf5b491337d462dac9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606204"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Přizpůsobení nastavení pro runtime integrace Azure-SSIS

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vlastní nastavení modulu Runtime integrace služby Azure-SQL Server Integration Services (Azure-SSIS IR) poskytuje rozhraní pro přidání vlastních kroků během instalace nebo rekonfigurace infračerveného zařízení Azure-SSIS. 

Pomocí vlastního nastavení můžete změnit výchozí provozní konfiguraci nebo prostředí například spustit další služby systému Windows, zachovat přístupová pověření pro sdílené složky nebo použít silnou kryptografii/bezpečnější síťový protokol (TLS 1.2). Nebo můžete nainstalovat další součásti, jako jsou sestavení, ovladače nebo rozšíření, na každém uzlu zařízení Azure-SSIS IR.

Vlastní nastavení na infračerveném počítači Azure-SSIS můžete provést dvěma způsoby: 
* **Express vlastní nastavení bez skriptu**: Spusťte některé běžné konfigurace systému a příkazy systému Windows nebo nainstalujte některé oblíbené nebo doporučené další součásti bez použití skriptů.
* **Standardní vlastní nastavení se skriptem**: Připravte skript a jeho přidružené soubory a nahrajte je všechny společně do kontejneru objektů blob ve vašem účtu úložiště Azure. Potom zadáte identifikátor uri (Uniform Access Signature) (SAS) pro váš kontejner při nastavte nebo překonfigurujete zařízení Azure-SSIS IR. Každý uzel zařízení Azure-SSIS IR pak stáhne skript a jeho přidružené soubory z kontejneru a spustí vlastní nastavení se zvýšenými oprávněními. Po dokončení vlastního nastavení každý uzel nahraje standardní výstup spuštění a další protokoly do kontejneru.

Můžete nainstalovat bezplatné, nelicencované komponenty a placené licencované komponenty s expresními a standardními vlastními nastaveními. Pokud jste nezávislý dodavatel softwaru (ISV), přečtěte [si informace o vývoji placených nebo licencovaných komponent pro zařízení Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Chcete-li těžit z budoucích vylepšení, doporučujeme použít v3 nebo novější řady uzlů pro Azure-SSIS IR s vlastním nastavením.

## <a name="current-limitations"></a>Aktuální omezení

Následující omezení platí pouze pro standardní vlastní nastavení:

- Pokud chcete použít *gacutil.exe* ve skriptu k instalaci sestavení v globální mezipaměti sestavení (GAC), musíte poskytnout *gacutil.exe* jako součást vlastního nastavení. Nebo můžete použít kopii, která je k dispozici v našem kontejneru *Public Preview,* popsané později v části "Pokyny".

- Pokud chcete odkazovat na podsložku ve skriptu, *msiexec.exe* nepodporuje `.\` zápis odkazovat na kořenovou složku. Použijte příkaz, `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` například `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`namísto .

- Sdílené složky pro správu nebo skryté sdílené síťové složky, které jsou automaticky vytvořeny systémem Windows, nejsou aktuálně podporovány v infračerveném počítači Azure-SSIS.

- Ovladač IBM iSeries Access ODBC není v infračerveném systému Azure-SSIS podporován. Při vlastním nastavení se mohou zobrazit chyby instalace. Pokud tak učiníte, požádejte o pomoc podporu IBM.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Chcete-li inindové zařízení Azure-SSIS inpřizpůsobit, potřebujete následující položky:

- [Předplatné Azure](https://azure.microsoft.com/)

- [Zřízení infračerveného zařízení Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Účet úložiště Azure](https://azure.microsoft.com/services/storage/). Není vyžadováno pro expresní vlastní nastavení. Pro standardní vlastní nastavení nahrajete a uložíte vlastní instalační skript a jeho přidružené soubory do kontejneru objektů blob. Vlastní proces instalace také nahraje protokoly spuštění do stejného kontejneru objektů blob.

## <a name="instructions"></a>Pokyny

1. Pokud chcete nastavit nebo překonfigurovat azure-SSIS IR pomocí PowerShellu, stáhněte a nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps). Pro expresní vlastní nastavení přejděte ke kroku 4.

1. Připravte vlastní instalační skript a jeho přidružené soubory (například soubory .bat, cmd, .exe, dll, .msi nebo PS1).

   * Musíte mít soubor skriptu s názvem *main.cmd*, což je vstupní bod vlastního nastavení.  
   * Chcete-li zajistit, že skript lze spustit tiše, doporučujeme nejprve otestovat v místním počítači.  
   * Pokud chcete, aby byly do kontejneru odeslány další protokoly generované jinými nástroji (například *msiexec.exe),* zadejte předdefinovanou proměnnou prostředí , `CUSTOM_SETUP_SCRIPT_LOG_DIR`jako složku protokolu ve skriptech (například *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Stáhněte, nainstalujte a otevřete [Azure Storage Explorer](https://storageexplorer.com/). Postupujte následovně:

   a. V části **(Místní a připojené)** klikněte pravým tlačítkem na **Účty úložiště**a pak vyberte **Připojit k úložišti Azure**.

      ![Připojení ke službě Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Vyberte **Použít název a klíč účtu úložiště a**pak vyberte **Další**.

      ![Použití názvu a klíče účtu úložiště](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Zadejte název a klíč účtu úložiště Azure, vyberte **Další**a pak vyberte **Připojit**.

      ![Zadejte název účtu úložiště a klíč](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. V části připojeného účtu úložiště Azure klikněte pravým tlačítkem myši na **kontejnery objektů blob**, vyberte **Vytvořit kontejner objektů blob**a pojmenujte nový kontejner.

      ![Vytvoření kontejneru objektů blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Vyberte nový kontejner a nahrajte vlastní instalační skript a jeho přidružené soubory. Ujistěte se, že jste nahráli *main.cmd* na nejvyšší úrovni kontejneru, ne v žádné složce. Také ujistěte se, že váš kontejner obsahuje pouze potřebné vlastní instalační soubory, takže jejich stažení do zařízení Azure-SSIS IR později nebude trvat dlouho. Maximální doba trvání vlastní instalace je aktuálně nastavena na 45 minut před časovým limitem. To zahrnuje čas stáhnout všechny soubory z kontejneru a nainstalovat je na Azure-SSIS IR. Pokud nastavení vyžaduje více času, zvyšte lístek podpory.

      ![Nahrání souborů do kontejneru objektů blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Klikněte pravým tlačítkem myši na kontejner a potom vyberte **získat sdílený přístupový podpis**.

      ![Získání sdíleného přístupového podpisu pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Vytvořte identifikátor URI SAS pro váš kontejner s dostatečně dlouhou dobou vypršení platnosti a s oprávněním pro čtení a zápis/seznam. Uri identifikátor SAS potřebujete ke stažení a spuštění vlastního instalačního skriptu a přidružených souborů vždy, když je libovolný uzel zařízení Azure-SSIS IR znovu zobrazen nebo restartován. K nahrání protokolů spuštění instalace potřebujete oprávnění k zápisu.

      > [!IMPORTANT]
      > Ujistěte se, že platnost identifikátoru URI SAS nevyprší a že prostředky vlastní instalace jsou vždy k dispozici po celou dobu životnosti zařízení Azure-SSIS IR, od vytvoření až po odstranění, zejména pokud během tohoto období pravidelně zastavujete a spouštěte infračervený přenos Azure-SSIS.

      ![Generovat sdílený přístupový podpis pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Zkopírujte a uložte identifikátor URI sas kontejneru.

      ![Kopírování a ukládání sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Když nastavíte nebo překonfigurujete inzid azure-SSIS pomocí uzdu datové továrny, můžete přidat nebo odebrat vlastní nastavení zaškrtnutím **políčka Přizpůsobit prostředí integrace Azure-SSIS s dalšími konfiguracemi systému nebo instalacemi součástí** v části **Upřesnit nastavení** v podokně **nastavení prostředí integrace.** 

   Pokud chcete přidat standardní vlastní nastavení, zadejte identifikátor URI SAS kontejneru do pole **Vlastní instalační kontejner SAS URI.** 
   
   Pokud chcete přidat expresní vlastní nastavení, vyberte **Nový,** chcete-li otevřít podokno **Přidat expresní vlastní nastavení,** a pak vrozeném typ v rozevíracím seznamu **Typ vlastního nastavení Express:**

   * Pokud vyberete typ **příkazu Spustit příkaz cmdkey,** můžete zachovat přihlašovací údaje pro sdílené složky nebo sdílené složky Azure Files v zařízení Azure-SSIS IR zadáním cílového názvu počítače nebo názvu domény, názvu účtu nebo uživatelského jména a klíče účtu nebo hesla v polích **/Add**, **User**a **/Pass.** Je to podobné jako spuštění příkazu [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) systému Windows v místním počítači.
   
   * Pokud vyberete typ **proměnné Přidat prostředí,** můžete přidat proměnné prostředí systému Windows pro použití v balíčcích, které běží na Azure-SSIS IR zadáním názvu a hodnoty proměnné prostředí do polí **Název proměnné** a **Hodnota proměnné.** Je to podobné jako spuštění příkazu [Nastavit](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) systémwindows v místním počítači.

   * Pokud vyberete typ **licencované součásti Instalovat,** můžete vybrat integrovanou komponentu od našich partnerů pro vlastní dodavatele softwaru v rozevíracím seznamu **Název součásti:**

     * Pokud vyberete komponentu **SentryOne Task Factory,** můžete nainstalovat sadu komponent [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) ze sentryone na azure-SSIS IR zadáním licenčního klíče produktu, který jste od nich zakoupili v poli **s klíči licence.** Aktuální integrovaná verze je **2019.4.3**.

     * Pokud zvolíte **OH22 je HEDDA. IO** komponenty, můžete nainstalovat [HEDDA. Komponenta](https://hedda.io/ssis-component/) kvality/čištění iO dat z oh22 na zařízení Azure-SSIS IR po zakoupení jejich služby. Současná integrovaná verze je **1.0.13**.

     * Pokud vyberete **komponentu SQLPhonetics.NET oh22,** můžete nainstalovat [komponentu SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) kvality a párování dat z oh22 do zařízení Azure-SSIS IR zadáním licenčního klíče produktu, který jste od nich zakoupili, do složky **s klíči licence.** Současná integrovaná verze je **1.0.43**.

     * Pokud vyberete komponentu **KingswaySoft pro integraci SSIS Toolkit,** můžete nainstalovat sadu konektorů [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) pro aplikace CRM/ERP/marketing/collaboration, jako je Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud atd. **License key** Aktuální integrovaná verze je **2019.2**.

     * Pokud vyberete komponentu **KingswaySoft SSIS Productivity Pack,** můžete nainstalovat sadu komponent [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) z KingswaySoft na azure-SSIS IR zadáním licenčního klíče produktu, který jste od nich zakoupili v krabici **s licenčním klíčem.** Aktuální integrovaná verze je **10.0**.

     * Pokud vyberete komponentu **Xtract IS společnosti Theobald Software,** můžete nainstalovat sadu konektorů [Xtract IS](https://theobald-software.com/en/xtract-is/) pro systém SAP (ERP, S/4HANA, BW) z theobald software na vašem Azure-SSIS IR přetažením & vynecháním/nahráním licenčního souboru produktu, který jste od nich zakoupili, do pole **licenčního souboru.** Současná integrovaná verze je **6.1.1.3**.

   Přidaná expresní vlastní nastavení se zobrazí v části **Upřesnit nastavení.** Chcete-li je odebrat, zaškrtněte jejich políčka a pak vyberte **Odstranit**.

   ![Upřesňující nastavení s vlastními nastaveními](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Když nastavíte nebo překonfigurujete infračervený přenos Azure-SSIS pomocí `Set-AzDataFactoryV2IntegrationRuntime` prostředí PowerShell, můžete přidat nebo odebrat vlastní nastavení spuštěním rutiny před spuštěním infračerveného serveru Azure-SSIS.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
   
   Po dokončení standardní vlastní nastavení a spuštění Azure-SSIS IR, můžete najít standardní výstup *main.cmd* a další spuštění protokoly ve složce *main.cmd.log* vašeho kontejneru úložiště.

1. Chcete-li zobrazit některé ukázky standardních vlastních nastavení, připojte se k našemu kontejneru Public Preview pomocí Průzkumníka úložiště Azure.

   a. V části **(Místní a připojené)** klikněte pravým tlačítkem myši na **Účty úložiště**, vyberte **Připojit k úložišti Azure**, vyberte Použít **připojovací řetězec nebo identifikátor URI se sdíleným přístupovým podpisem**a pak vyberte **Další**.

      ![Připojení k úložišti Azure pomocí sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Vyberte **Použít identifikátor URI SAS** a do pole **IDENTIFIKÁTOR URI** zadejte následující identifikátor URI sas:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Poskytnutí sdíleného přístupového podpisu pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Vyberte **Další**a pak vyberte **Připojit**.

   d. V levém podokně vyberte připojený kontejner **publicpreview** a poklepejte na složku *CustomSetupScript.* V této složce jsou následující položky:

      * *Ukázková* složka, která obsahuje vlastní nastavení pro instalaci základní úlohy na každý uzel zařízení Azure-SSIS IR. Úkol nedělá nic jiného než spánek po dobu několika sekund. Složka také obsahuje složku *gacutil,* jejíž celý obsah (*gacutil.exe*, *gacutil.exe.config*a *1033\gacutlrc.dll*) lze zkopírovat tak, jak je do kontejneru.

      * Složka *UserScenarios,* která obsahuje několik vlastních ukázek nastavení z reálných uživatelských scénářů.

        ![Obsah kontejneru public preview](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Poklepáním na složku *UserScenarios* vyhledejte následující položky:

      * Složka *rozhraní .NET FRAMEWORK 3.5,* která obsahuje vlastní instalační skript *(main.cmd)* pro instalaci starší verze rozhraní .NET Framework, která může být vyžadována pro vlastní součásti v každém uzlu zařízení Azure-SSIS IR.

      * Složka *BCP,* která obsahuje vlastní instalační skript *(main.cmd*) pro instalaci nástrojů příkazového řádku serveru SQL Server (*MsSqlCmdLnUtils.msi*), včetně programu hromadného kopírování (*bcp*), na každý uzel induI Azure-SSIS.

      * Složka *EXCEL,* která obsahuje vlastní instalační skript *(main.cmd)* pro instalaci sestavení a knihoven jazyka C#, které můžete použít v úlohách skriptu k dynamickému čtení a zápisu souborů aplikace Excel v každém uzlu zařízení Azure-SSIS IR. 
      
        Nejprve si stáhněte [*soubor ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) a [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)a pak je všechny nahrajte společně s *souborem main.cmd* do kontejneru. Případně pokud chcete použít jenom standardní Excel Connection Manager, excelový zdroj a cíl Excelu, požadovaný přístup redistributable je již předinstalován na Azure-SSIS IR, takže nepotřebujete žádné vlastní nastavení.
      
      * Složka *MYSQL ODBC,* která obsahuje vlastní instalační skript *(main.cmd)* pro instalaci ovladačů MYSQL ODBC na každý uzel infračerveného zařízení Azure-SSIS. Toto nastavení umožňuje připojit se k serveru MySQL pomocí správce připojení ROZHRANÍ ODBC, zdroje a cíle. 
     
        Nejprve [si stáhněte nejnovější 64bitové a 32bitové verze instalačních modulů ovladačů MySQL ODBC](https://dev.mysql.com/downloads/connector/odbc/) (například *mysql-connector-odbc-8.0.13-winx64.msi* a *mysql-connector-odbc-8.0.13-win32.msi)* a pak je všechny nahrajte společně s *main.cmd* do kontejneru.

      * Složka *ORACLE ENTERPRISE,* která obsahuje vlastní instalační skript *(main.cmd)* a tichý instalační konfigurační soubor *(client.rsp)* pro instalaci konektorů Oracle a ovladače OCI do každého uzlu zařízení Azure-SSIS IR Enterprise Edition. Toto nastavení umožňuje připojit se k serveru Oracle pomocí nástroje Oracle Connection Manager, zdroje a cíle. 
      
        Nejprve si stáhněte microsoft konektory v5.0 pro oracle (*AttunitySIsOraAdaptersSetup.msi* a *AttunitySSISOraAdaptersSetup64.msi*) ze [služby Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) a nejnovějšího klienta Oracle (například *winx64_12102_client.zip*) od [společnosti Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)a poté je všechny nahrajte společně s *main.cmd* a *client.rsp* do kontejneru. Pokud používáte tns pro připojení k Oracle, musíte také stáhnout *tnsnames.ora*, upravit a nahrát do kontejneru, takže jej lze zkopírovat do instalační složky Oracle během instalace.

      * Složka *ORACLE STANDARD ADO.NET,* která obsahuje vlastní instalační skript *(main.cmd)* pro instalaci ovladače Oracle ODP.NET na každý uzel zařízení Azure-SSIS IR. Toto nastavení umožňuje připojit se k serveru Oracle pomocí ADO.NET programu Connection Manager, zdroj a cíl. 
      
        Nejprve [si stáhněte nejnovější ovladač Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (například *ODP.NET_Managed_ODAC122cR1.zip*) a pak jej nahrajte společně s *souborem main.cmd* do kontejneru.
       
      * Složka *ORACLE STANDARD ODBC,* která obsahuje vlastní instalační skript *(main.cmd)* pro instalaci ovladače ORACLE ODBC a konfiguraci názvu zdroje dat (DSN) na každém uzlu zařízení Azure-SSIS IR. Toto nastavení umožňuje připojit se k serveru Oracle pomocí správce připojení ODBC, zdroj a cíl nebo správce připojení power query a zdroj se zdrojem dat ODBC. 
      
        Nejprve si stáhněte nejnovější balíček Oracle Instant Client (Základní balíček nebo základní balíček Lite) a balíček ODBC a poté je všechny nahrajte společně s *souborem main.cmd* do kontejneru:
        * [Stáhnout 64bitové balíčky](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Základní balíček: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Základní lite balíček: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; BALÍČEK ODBC: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Stáhněte si 32bitové balíčky](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Základní balíček: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Základní lite balíček: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; Balíček ODBC: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Složka *ORACLE STANDARD OLEDB,* která obsahuje vlastní instalační skript *(main.cmd)* pro instalaci ovladače Oracle OLEDB na každý uzel zařízení Azure-SSIS IR. Toto nastavení umožňuje připojit se k serveru Oracle pomocí nástroje OLEDB Connection Manager, zdroje a cíle. 
     
        Nejprve [si stáhněte nejnovější ovladač Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) (například *ODAC122010Xcopy_x64.zip*) a pak jej nahrajte společně s *souborem main.cmd* do kontejneru.

      * Složka *POSTGRESQL ODBC,* která obsahuje vlastní instalační skript *(main.cmd)* pro instalaci ovladačů PostgreSQL ODBC na každý uzel zařízení Azure-SSIS IR. Toto nastavení umožňuje připojit se k serveru PostgreSQL pomocí správce připojení ODBC, zdroje a cíle. 
     
        Nejprve [si stáhněte nejnovější 64bitové a 32bitové verze instalačních modulů ovladače PostgreSQL ODBC](https://www.postgresql.org/ftp/odbc/versions/msi/) (například *psqlodbc_x64.msi* a *psqlodbc_x86.msi)* a pak je všechny nahrajte společně s *main.cmd* do kontejneru.

      * Složka *SAP BW,* která obsahuje vlastní instalační skript *(main.cmd)* pro instalaci sestavení konektoru SAP .NET (*librfc32.dll)* na každém uzlu vašeho Azure-SSIS IR Enterprise Edition. Toto nastavení umožňuje použít SAP Business Warehouse (BW) Connection Manager, zdroj a cíl pro připojení k serveru SAP BW. 
      
        Nejprve nahrajte 64bitovou nebo 32bitovou verzi *librfc32.dll* z instalační složky SAP spolu s *main.cmd* do kontejneru. Skript pak zkopíruje sestavení SAP do složky *%windir%\SysWow64* nebo *%windir%\System32* během instalace.

      * Složka *STORAGE,* která obsahuje vlastní instalační skript *(main.cmd)* pro instalaci Azure PowerShellu do každého uzlu indu Urzoru Azure-SSIS. Toto nastavení umožňuje nasadit a spustit balíčky SSIS, které [spouštějí skripty PowerShellu, aby manipulovaly s vaším účtem úložiště Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Zkopírujte *main.cmd*, ukázku *AzurePowerShell.msi* (nebo použijte nejnovější verzi) a *storage.ps1* do kontejneru. Použijte *PowerShell.dtsx* jako šablonu pro vaše balíčky. Šablona balíčku kombinuje [úlohu stahování objektů Blob Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), která stáhne *soubor storage.ps1* jako upravitelný skript prostředí PowerShell, a [úlohu procesu spuštění](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), která spustí skript v každém uzlu.

      * Složka *TERADATA,* která obsahuje vlastní instalační skript (*main.cmd*), přidružený soubor (*install.cmd*) a instalační balíčky (*.msi*). Tyto soubory nainstalují konektory Teradata, rozhraní API Teradata Parallel Transporter (TPT) a ovladač ODBC na každém uzlu vašeho zařízení Azure-SSIS IR Enterprise Edition. Toto nastavení umožňuje použít Teradata Connection Manager, zdroj a cíl pro připojení k serveru Teradata. 
      
        Nejprve [si stáhněte soubor Zip Teradata Tools and Utilities 15.x](http://partnerintelligence.teradata.com) (například *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) a pak jej nahrajte společně s výše uvedenými soubory *CMD* a *.msi* do kontejneru.

      * Složka *TLS 1.2,* která obsahuje vlastní instalační skript *(main.cmd)* pro použití silné kryptografie/bezpečnějšího síťového protokolu (TLS 1.2) a zakázání starších verzí Protokolu SSL/TLS na každém uzlu zařízení Azure-SSIS IR.

      * Složka *ZULU OPENJDK,* která obsahuje vlastní instalační skript *(main.cmd)* a soubor PowerShell *(install_openjdk.ps1)* pro instalaci sady Zulu OpenJDK do každého uzlu infračerveného serveru Azure-SSIS. Toto nastavení umožňuje používat Azure Data Lake Store a flexibilní file konektory ke zpracování ORC a parketových souborů. Další informace najdete [v tématu Azure Feature Pack for Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Nejprve [si stáhněte nejnovější Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (například *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) a pak jej nahrajte společně s *main.cmd* a *install_openjdk.ps1* do kontejneru.

        ![Složky ve složce scénáře uživatelů](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Chcete-li vyzkoušet tyto vlastní ukázky nastavení, zkopírujte obsah z vybrané složky do kontejneru.
   
      Když nastavíte nebo překonfigurujete infračervený přenos Azure-SSIS pomocí uznaného datové **továrny, zaškrtněte políčko Přizpůsobit prostředí integrace Azure-SSIS s dalšími konfiguracemi/instalacemi součástí** v části **Upřesnit nastavení** a pak zadejte identifikátor URI SAS kontejneru do pole Identifikátor URI vlastního **instalačního kontejneru.**
   
      Když nastavíte nebo překonfigurujete infračervený `Set-AzDataFactoryV2IntegrationRuntime` přenos Azure-SSIS pomocí prostředí PowerShell, `SetupScriptContainerSasUri` spusťte rutinu s identifikátorem URI s as vašeho kontejneru jako hodnotou parametru.

## <a name="next-steps"></a>Další kroky

- [Nastavení enterprise edice runtime integrace Azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Vývoj placených nebo licencovaných vlastních komponent pro prostředí Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
