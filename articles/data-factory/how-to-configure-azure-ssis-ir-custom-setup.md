---
title: Přizpůsobení nastavení pro prostředí Azure-SSIS integration runtime | Dokumentace Microsoftu
description: Tento článek popisuje způsob použití rozhraní vlastní nastavení pro prostředí Azure-SSIS integration runtime k instalaci dalších komponent nebo změnit nastavení
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2edaea1cfb02b250b27c47d58b6c1d1ef6501480
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420264"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Přizpůsobení nastavení pro prostředí Azure-SSIS integration runtime

Vlastní instalační program rozhraní pro prostředí Azure-SSIS Integration Runtime poskytuje rozhraní přidat vlastní kroky nastavení během zřizování nebo opětovnou konfiguraci vašeho prostředí Azure-SSIS IR. Vlastní instalace vám umožňuje změnit výchozí konfigurace nebo prostředí (například spustit další služby Windows nebo zachovat přístupové přihlašovací údaje pro sdílené složky), nebo nainstalovat další součásti (například sestavení, ovladače nebo rozšíření) na každém uzlu Azure-SSIS IR.

Vlastní nastavení nakonfigurujete tak, že příprava skriptu a jeho přidružené soubory a nahráváme je do kontejneru objektů blob v účtu úložiště Azure. Zadejte sdíleného přístupového podpisu (SAS) identifikátor URI (Uniform Resource) pro váš kontejner při zřízení nebo změnit konfiguraci vašeho prostředí Azure-SSIS IR. Každý uzel prostředí Azure-SSIS IR pak stáhne skript a jeho přidružené soubory z vašeho kontejneru a spustí vlastní nastavení se zvýšenými oprávněními. Po dokončení instalace vlastní se každý uzel standardní výstup spouštění a další protokoly nahraje do kontejneru.

Můžete nainstalovat zdarma nebo nelicencovaného součásti i součásti placené nebo s licencí. Pokud jste nezávislý dodavatel softwaru, přečtěte si téma [k vývoji placené nebo licencované komponenty pro prostředí Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).


## <a name="current-limitations"></a>Aktuální omezení

-   Pokud chcete použít `gacutil.exe` pro instalaci sestavení v globální mezipaměti sestavení (GAC), budete muset zadat `gacutil.exe` jako součást vlastní instalaci nebo použití kopie v kontejneru ve verzi Public Preview.

-   Pokud chcete odkazovat na podsložku ve skriptu, `msiexec.exe` nepodporuje `.\` notation tak, aby odkazovaly kořenovou složku. Použijte příkaz podobný `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` místo `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

-   Pokud je potřeba připojit k prostředí Azure-SSIS IR s vlastním nastavením k virtuální síti, se podporuje jenom virtuální sítí Azure Resource Manageru. Klasické virtuální sítě se nepodporuje.

-   Sdílení správy se aktuálně nepodporuje v prostředí Azure-SSIS IR.

## <a name="prerequisites"></a>Požadavky

K přizpůsobení prostředí Azure-SSIS IR, budete potřebovat následující věci:

-   [Předplatné Azure](https://azure.microsoft.com/)

-   [Server služby Azure SQL Database nebo spravované Instance](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Zřízení prostředí Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Účet služby Azure Storage](https://azure.microsoft.com/services/storage/). Pro vlastní nastavení nahrávat a ukládat vlastní instalační skript a jeho přidružené soubory v kontejneru objektů blob. Protokoly spuštění procesu vlastní instalace také nahraje do kontejneru objektů blob.

## <a name="instructions"></a>Pokyny

1.  Stáhněte a nainstalujte [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (verze 5.4 nebo novější).

1.  Připravte vlastní instalační skript a jeho přidružené soubory (například soubory .bat, .cmd, .exe, .dll, .msi nebo .ps1).

    1.  Musíte mít souboru skriptu s názvem `main.cmd`, což je vstupním bodem vašeho vlastního nastavení.

    1.  Pokud chcete další protokoly generované v jiných nástrojích (například `msiexec.exe`) k nahrání do kontejneru, zadejte proměnnou prostředí předdefinované `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako složky protokolů ve skriptech (například `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1.  Stáhnout, nainstalovat a spustit [Průzkumníka služby Azure Storage](http://storageexplorer.com/).

    1.  V části **(místní a připojené)**, stisknutém pravém tlačítku vyberte **účty úložiště** a vyberte **připojit ke službě Azure storage**.

       ![Připojení k úložišti Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    1.  Vyberte **použít název účtu úložiště a klíč** a vyberte **Další**.

       ![Použití názvu a klíče účtu úložiště](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    1.  Zadejte název účtu služby Azure Storage a klíč, vyberte **Další**a pak vyberte **připojit**.

       ![Zadejte název účtu úložiště a klíč](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    1.  V části připojení účtu služby Azure Storage, klikněte pravým tlačítkem na **kontejnery objektů Blob**vyberte **vytvořit kontejner objektů Blob**a pojmenujte nový kontejner.

       ![Vytvoření kontejneru objektů blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    1.  Vyberte nový kontejner a nahrát vlastní instalační skript a jeho přidružené soubory. Ujistěte se, že nahrajete `main.cmd` na nejvyšší úrovni kontejneru, nikoli v jakékoli složce. 

       ![Nahrání souborů do kontejneru objektů blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    1.  Klikněte pravým tlačítkem na kontejner a vyberte **získat sdílený přístupový podpis**.

       ![Získání sdíleného přístupového podpisu kontejneru](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    1.  Vytvořit identifikátor URI SAS pro kontejner s dostatečně dlouhý platnosti a číst + zapisovat + seznamu oprávnění. Budete potřebovat identifikátor URI SAS ke stažení a spuštění vlastní instalační skript a její související soubory pokaždé, když libovolného uzlu Azure-SSIS IR se restartuje nebo obnoví z Image. Budete potřebovat oprávnění k zápisu se nahrát protokoly spuštění instalace.

        > [!IMPORTANT]
        > Ujistěte se prosím, že identifikátor URI SAS, nemá prošlou platnost a vlastní nastavení prostředků jsou vždy k dispozici během celého životního cyklu prostředí Azure-SSIS IR, od vytvoření do okamžiku odstranění, zejména v případě, že pravidelně zastavení a spuštění prostředí Azure-SSIS IR během tohoto období.

       ![Vygenerovat sdílený přístupový podpis kontejneru](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    1.  Zkopírujte a uložte identifikátor URI SAS kontejneru.

       ![Zkopírujte a uložte sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    1.  Po zřízení nebo změnit konfiguraci prostředí Azure-SSIS IR s uživatelské rozhraní služby Data Factory, než začnete prostředí Azure-SSIS IR, zadejte identifikátor URI SAS kontejneru v příslušném poli **Upřesnit nastavení** panelu:

       ![Zadejte sdílený přístupový podpis](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

       Při zřízení nebo změnit konfiguraci prostředí Azure-SSIS IR pomocí Powershellu, před zahájením prostředí Azure-SSIS IR, spusťte `Set-AzureRmDataFactoryV2IntegrationRuntime` rutiny s identifikátorem URI SAS vašeho kontejneru jako hodnotu pro nové `SetupScriptContainerSasUri` parametru. Příklad:

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    1.  Po dokončení vlastní instalace a spuštění prostředí Azure-SSIS IR, můžete najít standardní výstup `main.cmd` a nepřihlásí další spuštění `main.cmd.log` složky vašeho kontejneru úložiště.

1.  Pokud chcete zobrazit další příklady vlastních nastavení, připojte se ke kontejneru ve verzi Public Preview pomocí Průzkumníka služby Azure Storage.

    a.  V části **(místní a připojené)**, klikněte pravým tlačítkem na **účty úložiště**vyberte **připojit ke službě Azure storage**vyberte **pomocí připojovacího řetězce nebo sdíleného přístupu podpis URI**a pak vyberte **Další**.

       ![Připojení k úložišti Azure pomocí sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Vyberte **použít identifikátor URI SAS** a zadejte následující identifikátor URI SAS pro kontejner ve verzi Public Preview. Vyberte **Další**a pak zaškrtněte **připojit**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Zadejte sdílený přístupový podpis kontejneru](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Vyberte připojené kontejner, ve verzi Public Preview a dvakrát klikněte `CustomSetupScript` složky. V této složce jsou následující položky:

       1. A `Sample` složky, která obsahuje vlastní instalační program a nainstalujte základní úlohy na každém uzlu Azure-SSIS IR. Úloha nemá žádný účinek ale z režimu spánku na několik sekund. Složka také obsahuje `gacutil` složky, která obsahuje `gacutil.exe`. Kromě toho `main.cmd` obsahuje komentáře k uchování přihlašovacích údajů pro přístup pro sdílené složky.

       1. A `UserScenarios` složky, která obsahuje některá vlastní nastavení pro skutečné uživatelské scénáře.

    ![Obsah kontejneru ve verzi public preview](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Dvakrát klikněte `UserScenarios` složky. V této složce jsou následující položky:

       1. A `.NET FRAMEWORK 3.5` složky, která obsahuje vlastní instalačního programu pro instalaci dřívější verzi rozhraní .NET Framework, může být nezbytný pro vlastní komponenty na každém uzlu Azure-SSIS IR.

       1. `AAS` Složky, která obsahuje vlastní instalační program a nainstalujte klientské knihovny na každém uzlu vašeho prostředí Azure-SSIS IR, které vaše úkoly služby Analysis Services pro připojení k instanci služby Azure Analysis Services (AAS) s použitím ověřování instančních objektů. Nejdřív stáhněte nejnovější **MSOLAP (amd64)** a **AMO** klientské knihovny/Windows instalačních programů – například `x64_15.0.900.108_SQL_AS_OLEDB.msi` a `x64_15.0.900.108_SQL_AS_AMO.msi` – od [tady](https://docs.microsoft.com/en-us/azure/analysis-services/analysis-services-data-providers), pak nahrávat všechny spolu s `main.cmd` do kontejneru.  

       1. A `BCP` složky, která obsahuje vlastní instalační program a nainstalujte nástroje příkazového řádku systému SQL Server (`MsSqlCmdLnUtils.msi`), včetně program hromadného kopírování (`bcp`), na každém uzlu Azure-SSIS IR.

       1. `EXCEL` Složky, která obsahuje vlastní nastavení pro instalaci sestavení open source (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`, a `ExcelDataReader.dll`) na každém uzlu Azure-SSIS IR.

       1. `MSDTC` Složky, která obsahuje vlastní nastavení k úpravě konfigurace sítě a zabezpečení pro službu Microsoft distribuované transakce koordinátor (MSDTC) na každém uzlu z vašeho prostředí Azure-SSIS IR. Aby bylo zajištěno, že je spuštěna služba MSDTC, přidejte prosím úloha spuštění procesu na začátku tok řízení v vaše balíčky ke spuštění následujícího příkazu: `%SystemRoot%\system32\cmd.exe /c powershell -Command "Start-Service MSDTC"` 

       1. `ORACLE ENTERPRISE` Složky, která obsahuje vlastní instalační skript (`main.cmd`) a konfigurační soubor bezobslužné instalace (`client.rsp`) instalace konektorů Oracle a OCI ovladač na každém uzlu vašeho prostředí Azure-SSIS IR Enterprise Edition. Toto nastavení umožňuje používat Správce připojení Oracle, zdroj a cíl. Nejdřív stáhněte v5.0 Connectors Microsoftu pro Oracle (`AttunitySSISOraAdaptersSetup.msi` a `AttunitySSISOraAdaptersSetup64.msi`) z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) a nejnovější klienta Oracle – například `winx64_12102_client.zip` – od [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), nahrajte je všechny spolu s `main.cmd` a `client.rsp` do kontejneru. Pokud používáte pro připojení k Oracle TNS, budete také muset stáhnout `tnsnames.ora`, upravit jej a nahrajte ho do kontejneru, takže je možné zkopírovat do instalační složky sady Oracle během instalace.

       1. `ORACLE STANDARD` Složky, která obsahuje vlastní instalační skript (`main.cmd`) Chcete-li nainstalovat ovladač Oracle ODP.NET na každém uzlu Azure-SSIS IR. Toto nastavení umožňuje používat Správce připojení ADO.NET, zdroj a cíl. Nejdřív stáhněte nejnovější ovladač Oracle ODP.NET – například `ODP.NET_Managed_ODAC122cR1.zip` – od [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)a pak nahrajte společně se službou `main.cmd` do kontejneru.

       1. `SAP BW` Složky, která obsahuje vlastní instalační skript (`main.cmd`) Chcete-li nainstalovat sestavení konektoru SAP .NET (`librfc32.dll`) na každém uzlu vašeho prostředí Azure-SSIS IR Enterprise Edition. Toto nastavení umožňuje používat Správce připojení k SAP BW, zdroj a cíl. Nejprve nahrát 64-bit nebo 32bitové verze `librfc32.dll` z instalační složky sady SAP do kontejneru, společně s `main.cmd`. Skript potom zkopíruje sestavení SAP do `%windir%\SysWow64` nebo `%windir%\System32` složky během instalace.

       1. A `STORAGE` složky, která obsahuje vlastní instalační program a nainstalujte Azure PowerShell na každém uzlu Azure-SSIS IR. Toto nastavení umožňuje nasazení a spuštění služby SSIS balíčky, která spustí [skripty Powershellu pro manipulaci s svého účtu Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Kopírování `main.cmd`, ukázka `AzurePowerShell.msi` (nebo nainstalujte nejnovější verzi) a `storage.ps1` do kontejneru. Použití PowerShell.dtsx jako šablona pro vaše balíčky. Kombinuje balíček šablony [úloha stažení objektů Blob v Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), které soubory ke stažení `storage.ps1` jako upravitelná skript prostředí PowerShell a [úloha spuštění procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) skript, který se spustí na každém uzlu.

       1. A `TERADATA` složky, která obsahuje vlastní instalační skript (`main.cmd`), jeho přidruženého souboru (`install.cmd`) a instalační balíčky (`.msi`). Tyto soubory instalace konektorů, rozhraní API TPT a ovladač ODBC Teradata na každém uzlu vašeho prostředí Azure-SSIS IR Enterprise Edition. Toto nastavení umožňuje používat Správce připojení Teradata, zdroj a cíl. Nejprve stáhněte soubor zip 15.x Teradata nástrojů a pomůcek (TTU) (například `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) z [Teradata](http://partnerintelligence.teradata.com)a pak ho nahrajte spolu s výše `.cmd` a `.msi` soubory do kontejneru.

    ![Složky ve složce uživatelské scénáře](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Pokud chcete vyzkoušet tyto ukázky vlastní nastavení, zkopírujte a vložte obsah z vybrané složky do kontejneru. Při zřízení nebo změnit konfiguraci prostředí Azure-SSIS IR pomocí prostředí PowerShell, spusťte `Set-AzureRmDataFactoryV2IntegrationRuntime` rutiny s identifikátorem URI SAS vašeho kontejneru jako hodnotu pro nové `SetupScriptContainerSasUri` parametru.

## <a name="next-steps"></a>Další postup

-   [Enterprise Edition prostředí Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Jak vyvíjet placené nebo licenci vlastní komponenty prostředí Azure-SSIS integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
