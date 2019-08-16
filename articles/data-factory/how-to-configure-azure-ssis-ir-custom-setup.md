---
title: Přizpůsobení nastavení pro prostředí Azure-SSIS Integration runtime | Microsoft Docs
description: Tento článek popisuje, jak použít vlastní rozhraní pro instalaci pro prostředí Azure-SSIS Integration runtime k instalaci dalších součástí nebo změny nastavení.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 1/25/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 4962070d69af98d0c7b10dc6f931612766529dce
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515705"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Přizpůsobení nastavení pro prostředí Azure-SSIS Integration runtime

Vlastní nastavení rozhraní pro Azure-SSIS Integration Runtime poskytuje rozhraní pro přidání vlastních kroků nastavení během zřizování nebo opětovné konfigurace prostředí Azure-SSIS IR. Vlastní instalace umožňuje změnit výchozí konfiguraci a prostředí (například spustit další služby systému Windows nebo zachovat přístupová pověření pro sdílené složky) nebo nainstalovat další součásti (například sestavení, ovladače nebo rozšíření). v každém uzlu prostředí Azure-SSIS IR.

Vlastní instalaci můžete nakonfigurovat přivoláním skriptu a přidružených souborů a jejich nahráním do kontejneru objektů BLOB v účtu Azure Storage. Identifikátor URI sdíleného přístupového podpisu (SAS) můžete zadat pro svůj kontejner při zřizování nebo překonfigurování Azure-SSIS IR. Každý uzel prostředí Azure-SSIS IR pak stáhne skript a jeho přidružené soubory z vašeho kontejneru a spustí vlastní instalaci se zvýšenými oprávněními. Po dokončení vlastní instalace každý uzel nahraje standardní výstup spuštění a další protokoly do kontejneru.

Můžete nainstalovat bezplatné i nelicencované komponenty a placené nebo licencované součásti. Pokud jste nezávislý výrobce softwaru, přečtěte si téma [vývoj placených nebo licencovaných komponent pro Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Uzly v2-Series prostředí Azure-SSIS IR nejsou vhodné pro vlastní instalaci, proto použijte místo toho uzly V3-Series.  Pokud už používáte uzly v2-Series, přepněte prosím na použití uzlů V3-Series co nejdříve.

## <a name="current-limitations"></a>Aktuální omezení

-   Chcete-li použít `gacutil.exe` pro instalaci sestavení v globální mezipaměti sestavení (GAC), je nutné zadat `gacutil.exe` jako součást vlastního nastavení nebo použít kopii poskytnutou v kontejneru Public Preview.

-   Pokud chcete odkazovat na podsložku ve vašem skriptu, `msiexec.exe` `.\` nepodporuje zápis pro odkaz na kořenovou složku. Použijte příkaz, jako `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`místo.

-   Pokud se potřebujete připojit k Azure-SSIS IR pomocí vlastního nastavení do virtuální sítě, podporuje se jenom Azure Resource Manager virtuální síť. Klasická virtuální síť není podporovaná.

-   Sdílená složka pro správu se v Azure-SSIS IR v tuto chvíli nepodporuje.

-   V prostředí Azure-SSIS IR není podporován ovladač IBM iSeries Access ODBC. Při vlastní instalaci se může zobrazit chyba instalace. Pokud potřebujete pomoc, obraťte se prosím na podporu společnosti IBM.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K přizpůsobení prostředí Azure-SSIS IR potřebujete tyto věci:

-   [Předplatné Azure](https://azure.microsoft.com/)

-   [Server Azure SQL Database nebo spravované instance](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Zřízení Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Účet Azure Storage](https://azure.microsoft.com/services/storage/). Pro vlastní instalaci nahrajete a uložíte svůj vlastní instalační skript a jeho přidružené soubory do kontejneru objektů BLOB. Vlastní proces instalace také nahraje své protokoly spuštění do stejného kontejneru objektů BLOB.

## <a name="instructions"></a>Instrukce

1. Stáhněte a nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).

1. Připravte vlastní instalační skript a jeho přidružené soubory (například. bat,. cmd,. exe,. dll,. msi nebo. ps1).

   1.  Musíte mít soubor skriptu s názvem `main.cmd`, který je vstupním bodem vlastní instalace.

   1.  Pokud chcete, aby se do kontejneru nahrály další protokoly generované `msiexec.exe`jinými nástroji (například), zadejte předdefinovanou `CUSTOM_SETUP_SCRIPT_LOG_DIR` proměnnou prostředí jako složku protokolu ve `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`skriptech (například).

1. Stáhněte, nainstalujte a spusťte [Průzkumník služby Azure Storage](https://storageexplorer.com/).

   1. V části **(místní a připojené)** klikněte pravým tlačítkem na **účty úložiště** a vyberte **připojit k Azure Storage**.

      ![Připojení k úložišti Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Vyberte **použít název a klíč účtu úložiště** a pak vyberte **Další**.

      ![Použití názvu a klíče účtu úložiště](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Zadejte název účtu Azure Storage a klíč, klikněte na tlačítko **Další**a pak vyberte **připojit**.

      ![Zadejte název a klíč účtu úložiště.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. V části připojený účet Azure Storage klikněte pravým tlačítkem na **kontejnery objektů BLOB**, vyberte **vytvořit kontejner objektů BLOB**a pojmenujte nový kontejner.

      ![Vytvoření kontejneru objektů blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Vyberte nový kontejner a nahrajte vlastní instalační skript a jeho přidružené soubory. Ujistěte se, že jste `main.cmd` nahráli na nejvyšší úrovni kontejneru, ne do žádné složky. Ujistěte se prosím, že váš kontejner obsahuje jenom nezbytné vlastní instalační soubory, takže je budete muset stáhnout do Azure-SSIS IR později nebude trvat delší dobu. Maximální doba pro vlastní instalaci je v současnosti nastavená na 45 minut před vypršením časového limitu a ta zahrnuje čas ke stažení všech souborů z vašeho kontejneru a jejich instalaci do Azure-SSIS IR. Pokud je potřeba delší období, vyvolejte prosím lístek podpory.

      ![Nahrání souborů do kontejneru objektů BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Klikněte pravým tlačítkem na kontejner a vyberte **získat sdílený přístupový podpis**.

      ![Získání sdíleného přístupového podpisu pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Vytvořte identifikátor URI SAS pro váš kontejner s dostatečně dlouhou dobou vypršení platnosti a s oprávněním pro čtení + zápis + seznam. Identifikátor URI SAS potřebujete ke stažení a spuštění vlastního instalačního skriptu a jeho přidružených souborů vždy, když se kterýkoli uzel prostředí Azure-SSIS IR obnoví z Image nebo restartuje. Pro nahrání protokolů spuštění instalace potřebujete oprávnění k zápisu.

      > [!IMPORTANT]
      > Ujistěte se prosím, že identifikátor URI SAS nevyprší a vlastní prostředky instalace jsou vždycky dostupné během celého životního cyklu Azure-SSIS IR, od vytvoření po odstranění, obzvláště když v této době pravidelně zastavíte a zahájíte Azure-SSIS IR.

      ![Generování sdíleného přístupového podpisu pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Zkopírujte a uložte identifikátor URI SAS vašeho kontejneru.

      ![Zkopírování a uložení sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. Když zřizujete nebo překonfigurujete Azure-SSIS IR pomocí Data Factory uživatelského rozhraní, před spuštěním prostředí Azure-SSIS IR zadejte identifikátor URI SAS svého kontejneru do příslušného pole na panelu **Upřesnit nastavení** :

      ![Zadejte sdílený přístupový podpis.](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      Když zřizujete nebo znovu konfigurujete Azure-SSIS IR pomocí PowerShellu, před spuštěním prostředí Azure-SSIS IR spusťte `Set-AzDataFactoryV2IntegrationRuntime` rutinu s identifikátorem URI SAS vašeho kontejneru jako hodnotu pro nový `SetupScriptContainerSasUri` parametr. Příklad:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. Po dokončení vlastní instalace a spuštění Azure-SSIS IR můžete najít standardní výstup `main.cmd` a další protokoly spuštění `main.cmd.log` ve složce kontejneru úložiště.

1. Pokud chcete zobrazit další příklady vlastních nastavení, připojte se k kontejneru Public Preview pomocí Průzkumník služby Azure Storage.

   a.  V části **(místní a připojená)** klikněte pravým tlačítkem na **účty úložiště**, vyberte **připojit k Azure Storage**, vyberte **Použít připojovací řetězec nebo identifikátor URI sdíleného přístupového podpisu**a pak vyberte **Další**.

      ![Připojení ke službě Azure Storage pomocí sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Vyberte **použít identifikátor URI SAS** a zadejte následující identifikátor URI SAS pro kontejner Public Preview. Vyberte **Další**a vyberte **připojit**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Zadejte sdílený přístupový podpis pro kontejner.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Vyberte kontejner připojené Public Preview a dvakrát klikněte `CustomSetupScript` na složku. V této složce jsou následující položky:

      1. `Sample` Složka, která obsahuje vlastní instalaci pro instalaci základní úlohy do každého uzlu prostředí Azure-SSIS IR. Úkol neprovede žádnou akci, ale během několika sekund režim spánku. Složka také obsahuje `gacutil` složku, jejíž obsah lze zkopírovat (`gacutil.exe`, `gacutil.exe.config` `1033\gacutlrc.dll`a) do kontejneru. Kromě toho `main.cmd` obsahuje komentáře k trvalému přístupu k přihlašovacím údajům pro sdílení souborů.

      1. `UserScenarios` Složka, která obsahuje několik vlastních nastavení pro scénáře reálných uživatelů.

   ![Obsah kontejneru verze Public Preview](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Dvakrát klikněte `UserScenarios` na složku. V této složce jsou následující položky:

      1. `.NET FRAMEWORK 3.5` Složka, která obsahuje vlastní instalaci pro instalaci starší verze .NET Framework, která může být vyžadována pro vlastní komponenty v každém uzlu prostředí Azure-SSIS IR.

      1. Složka, která obsahuje vlastní instalační program pro instalaci SQL Server nástrojů příkazového řádku (`MsSqlCmdLnUtils.msi`), včetně programu hromadného kopírování (`bcp`), v každém uzlu prostředí Azure-SSIS IR. `BCP`

      1. Složka, která obsahuje vlastní instalaci pro instalaci Open Source sestavení (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` `ExcelDataReader.dll`a) na každém uzlu prostředí Azure-SSIS IR. `EXCEL`

      1. Složka, která obsahuje skript vlastního nastavení (`main.cmd`) a bezobslužný instalační soubor (`client.rsp`) pro instalaci konektoru Oracle a ovladače OCI do každého uzlu vaší Azure-SSIS IR Enterprise Edition. `ORACLE ENTERPRISE` Tato instalace vám umožní používat Správce připojení Oracle, zdroj a cíl. Nejdřív Stáhněte konektory Microsoft Connectors v 5.0 pro`AttunitySSISOraAdaptersSetup.msi` Oracle `AttunitySSISOraAdaptersSetup64.msi`(a) z [webu Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) a `winx64_12102_client.zip` nejnovějšího klienta Oracle – třeba od [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)a pak je nahrajte společně s `main.cmd` a`client.rsp` do svého kontejneru. Pokud se k připojení k Oracle použije TNS, musíte ho také stáhnout `tnsnames.ora`, upravit a nahrát do svého kontejneru, takže ho můžete během instalace zkopírovat do instalační složky Oracle.

      1. Složka, která obsahuje skript vlastního nastavení (`main.cmd`) pro instalaci ovladače Oracle ODP.NET do každého uzlu prostředí Azure-SSIS IR. `ORACLE STANDARD ADO.NET` Tato instalace vám umožní používat ADO.NET Správce připojení, zdroj a cíl. Nejdřív Stáhněte nejnovější ovladač Oracle ODP.NET – třeba `ODP.NET_Managed_ODAC122cR1.zip` – od [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), a pak ho `main.cmd` nahrajte do svého kontejneru.
       
      1. Složka, která obsahuje skript vlastního nastavení (`main.cmd`) pro instalaci ovladače Oracle ODBC a konfiguraci DSN v každém uzlu prostředí Azure-SSIS IR. `ORACLE STANDARD ODBC` Tato instalace umožňuje pro připojení k serveru Oracle použít Správce připojení ODBC/zdroj/cíl nebo Power Query Správce připojení a zdroj se zdrojem dat ODBC. Nejdřív Stáhněte nejnovější okamžitý klient Oracle (základní balíček nebo základní balíček verze Lite) a balíček ODBC – například 64 balíčků ( [](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) základní balíček: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, základní balíček Lite: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, balíček ODBC: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) nebo 32 balíčků (základní balíček: [](https://www.oracle.com/technetwork/topics/winsoft-085727.html) `instantclient-basic-nt-18.3.0.0.0dbru.zip`, Basic Lite Package: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, ODBC Package: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) a pak je `main.cmd` nahrajte do kontejneru.

      1. Složka, která obsahuje skript vlastního nastavení (`main.cmd`) pro instalaci sestavení SAP .NET Connector (`librfc32.dll`) na každý uzel vaší Azure-SSIS IR Enterprise Edition. `SAP BW` Tato instalace umožňuje použít SAP BW Správce připojení, zdroj a cíl. Nejdřív nahrajte 64 nebo 32 verzi `librfc32.dll` z instalační složky SAP do svého kontejneru společně s. `main.cmd` Skript pak zkopíruje sestavení SAP do `%windir%\SysWow64` složky nebo `%windir%\System32` během instalace.

      1. `STORAGE` Složka, která obsahuje vlastní instalaci pro instalaci Azure PowerShell na každém uzlu prostředí Azure-SSIS IR. Tato instalace umožňuje nasadit a spustit balíčky SSIS, které spouštějí [skripty prostředí PowerShell, aby bylo možné manipulovat s vaším účtem Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Kopírovat `main.cmd`, ukázku `AzurePowerShell.msi` (nebo nainstalovat nejnovější verzi) a `storage.ps1` do kontejneru. Jako šablonu pro balíčky použijte PowerShell. dtsx. Šablona balíčku kombinuje [úlohu stažení objektu BLOB v Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), která se `storage.ps1` stáhne jako upravitelný skript PowerShellu, a [úlohu spuštění procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , která spustí skript na každém uzlu.

      1. Složka, která obsahuje skript vlastního nastavení (`main.cmd`), přidružený soubor (`install.cmd`) a instalační balíčky (`.msi`). `TERADATA` Tyto soubory instalují konektory Teradata, rozhraní API TPT a ovladač ODBC do každého uzlu vaší Azure-SSIS IR Enterprise Edition. Tato instalace umožňuje použít Správce připojení Teradata, zdroj a cíl. Nejdřív si stáhněte soubor zip Tools a Utilities (TTU) 15. `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`x (například) z [Teradata](http://partnerintelligence.teradata.com)a pak ho nahrajte společně s výše `.cmd` a `.msi` soubory do svého kontejneru.

   ![Složky ve složce scénáře pro uživatele](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Chcete-li vyzkoušet tyto vlastní instalační ukázky, zkopírujte a vložte obsah z vybrané složky do kontejneru. Když zřizujete nebo překonfigurujete Azure-SSIS IR pomocí PowerShellu, spusťte `Set-AzDataFactoryV2IntegrationRuntime` rutinu s identifikátorem URI SAS vašeho kontejneru jako s hodnotou pro `SetupScriptContainerSasUri` nový parametr.

## <a name="next-steps"></a>Další postup

-   [Edice Enterprise systému Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Jak vyvíjet placené nebo licencované vlastní komponenty pro prostředí Azure-SSIS Integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
