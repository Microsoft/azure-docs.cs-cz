---
title: Přizpůsobení nastavení pro prostředí Azure-SSIS Integration runtime
description: Tento článek popisuje, jak použít vlastní rozhraní pro instalaci pro prostředí Azure-SSIS Integration runtime k instalaci dalších součástí nebo změny nastavení.
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
ms.date: 1/25/2019
ms.openlocfilehash: d80ff102648deebf63cc0752b2980274cb90aeb9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74922878"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Přizpůsobení nastavení pro prostředí Azure-SSIS Integration runtime

Vlastní nastavení rozhraní Azure-SSIS Integration Runtime poskytuje rozhraní pro přidání vlastních kroků instalace během zřizování nebo opětovné konfigurace Azure-SSIS IR. Vlastní instalace umožňuje změnit výchozí konfiguraci a prostředí (například spustit další služby systému Windows nebo zachovat přístupová pověření pro sdílené složky) nebo nainstalovat další součásti (například sestavení, ovladače nebo rozšíření). v každém uzlu Azure-SSIS IR.

Vlastní instalaci můžete nakonfigurovat přivoláním skriptu a přidružených souborů a jejich nahráním do kontejneru objektů BLOB v účtu Azure Storage. Identifikátor URI sdíleného přístupového podpisu (SAS) můžete zadat pro svůj kontejner při zřizování nebo překonfigurování Azure-SSIS IR. Každý uzel vaší Azure-SSIS IR pak stáhne skript a jeho přidružené soubory z vašeho kontejneru a spustí vlastní instalaci se zvýšenými oprávněními. Po dokončení vlastní instalace každý uzel nahraje standardní výstup spuštění a další protokoly do kontejneru.

Můžete nainstalovat bezplatné i nelicencované komponenty a placené nebo licencované součásti. Pokud jste nezávislý výrobce softwaru, přečtěte si téma [postup vývoje placených nebo licencovaných komponent pro Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Uzly v2-Series Azure-SSIS IR nejsou vhodné pro vlastní instalaci, proto použijte místo toho uzly V3-Series.  Pokud už používáte uzly v2-Series, přepněte prosím na použití uzlů V3-Series co nejdříve.

## <a name="current-limitations"></a>Aktuální omezení

-   Pokud chcete použít `gacutil.exe` k instalaci sestavení v globální mezipaměti sestavení (GAC), je třeba zadat `gacutil.exe` jako součást vlastního nastavení nebo použít kopii poskytnutou v kontejneru Public Preview.

-   Pokud chcete odkazovat na podsložku ve skriptu, `msiexec.exe` nepodporuje zápis `.\`, aby odkazoval na kořenovou složku. Místo `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`použijte příkaz, jako je `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`.

-   Pokud potřebujete připojit Azure-SSIS IR k virtuální síti vlastním instalačním programem, je podporována pouze Azure Resource Manager virtuální síť. Klasická virtuální síť není podporovaná.

-   Sdílená složka pro správu se v Azure-SSIS IR v tuto chvíli nepodporuje.

-   Ovladač IBM iSeries Access ODBC není podporován na Azure-SSIS IR. Při vlastní instalaci se může zobrazit chyba instalace. Pokud potřebujete pomoc, obraťte se prosím na podporu společnosti IBM.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K přizpůsobení Azure-SSIS IR budete potřebovat následující věci:

-   [Předplatné Azure](https://azure.microsoft.com/)

-   [Server Azure SQL Database nebo spravované instance](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Zřízení Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Účet Azure Storage](https://azure.microsoft.com/services/storage/). Pro vlastní instalaci nahrajete a uložíte svůj vlastní instalační skript a jeho přidružené soubory do kontejneru objektů BLOB. Vlastní proces instalace také nahraje své protokoly spuštění do stejného kontejneru objektů BLOB.

## <a name="instructions"></a>Pokyny

1. Stáhněte a nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).

1. Připravte vlastní instalační skript a jeho přidružené soubory (například. bat,. cmd,. exe,. dll,. msi nebo. ps1).

   1.  Musíte mít soubor skriptu s názvem `main.cmd`, který je vstupním bodem vlastní instalace.

   1.  Je potřeba se ujistit, že se skript dá spustit Tichě, doporučuje se nejdřív otestovat skript na místním počítači.

   1.  Pokud chcete, aby byly do kontejneru nahrány další protokoly generované jinými nástroji (například `msiexec.exe`), zadejte předdefinovanou proměnnou prostředí, `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako složku protokolu ve skriptech (například `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. Stáhněte, nainstalujte a spusťte [Průzkumník služby Azure Storage](https://storageexplorer.com/).

   1. V části **(místní a připojené)** klikněte pravým tlačítkem na **účty úložiště** a vyberte **připojit k Azure Storage**.

      ![Připojení k úložišti Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Vyberte **použít název a klíč účtu úložiště** a pak vyberte **Další**.

      ![Použití názvu a klíče účtu úložiště](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Zadejte název účtu Azure Storage a klíč, klikněte na tlačítko **Další**a pak vyberte **připojit**.

      ![Zadejte název a klíč účtu úložiště.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. V části připojený účet Azure Storage klikněte pravým tlačítkem na **kontejnery objektů BLOB**, vyberte **vytvořit kontejner objektů BLOB**a pojmenujte nový kontejner.

      ![Vytvoření kontejneru objektů blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Vyberte nový kontejner a nahrajte vlastní instalační skript a jeho přidružené soubory. Zajistěte, abyste nahráli `main.cmd` na nejvyšší úrovni kontejneru, ne do žádné složky. Ujistěte se prosím, že kontejner obsahuje jenom nezbytné vlastní instalační soubory, takže je stahujete do Azure-SSIS IR později nebude trvat dlouhou dobu. Maximální doba pro vlastní instalaci je v současnosti nastavená na 45 minut před vypršením časového limitu a ta zahrnuje čas ke stažení všech souborů z vašeho kontejneru a jejich instalaci na Azure-SSIS IR. Pokud je potřeba delší období, vyvolejte prosím lístek podpory.

      ![Nahrání souborů do kontejneru objektů BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Klikněte pravým tlačítkem na kontejner a vyberte **získat sdílený přístupový podpis**.

      ![Získání sdíleného přístupového podpisu pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Vytvořte identifikátor URI SAS pro váš kontejner s dostatečně dlouhou dobou vypršení platnosti a s oprávněním pro čtení + zápis + seznam. Identifikátor URI SAS potřebujete ke stažení a spuštění skriptu vlastní instalace a jeho přidružených souborů pokaždé, když se kterýkoli uzel Azure-SSIS IR obnoví nebo restartuje. Pro nahrání protokolů spuštění instalace potřebujete oprávnění k zápisu.

      > [!IMPORTANT]
      > Zajistěte, aby platnost identifikátoru URI SAS nevypršela, a vlastní prostředky instalace jsou vždy k dispozici během celého životního cyklu Azure-SSIS IR, od vytvoření po odstranění, zejména v případě, že během této doby pravidelně zastavíte a zahájíte Azure-SSIS IR.

      ![Generování sdíleného přístupového podpisu pro kontejner](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Zkopírujte a uložte identifikátor URI SAS vašeho kontejneru.

      ![Zkopírování a uložení sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. Když zřídíte nebo překonfigurujete Azure-SSIS IR s uživatelským rozhraním Data Factory, před spuštěním Azure-SSIS IR zadejte identifikátor URI SAS svého kontejneru do příslušného pole na panelu **Upřesnit nastavení** :

      ![Zadejte sdílený přístupový podpis.](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      Když zřídíte nebo překonfigurujete Azure-SSIS IR pomocí prostředí PowerShell, před spuštěním Azure-SSIS IR spusťte rutinu `Set-AzDataFactoryV2IntegrationRuntime` s identifikátorem URI SAS vašeho kontejneru jako s hodnotou pro nový parametr `SetupScriptContainerSasUri`. Například:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. Po dokončení vlastní instalace a spuštění Azure-SSIS IR můžete najít standardní výstup `main.cmd` a další protokoly spuštění ve složce `main.cmd.log` kontejneru úložiště.

1. Pokud chcete zobrazit další příklady vlastních nastavení, připojte se k kontejneru Public Preview pomocí Průzkumník služby Azure Storage.

   a.  V části **(místní a připojená)** klikněte pravým tlačítkem na **účty úložiště**, vyberte **připojit k Azure Storage**, vyberte **Použít připojovací řetězec nebo identifikátor URI sdíleného přístupového podpisu**a pak vyberte **Další**.

      ![Připojení ke službě Azure Storage pomocí sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Vyberte **použít identifikátor URI SAS** a zadejte následující identifikátor URI SAS pro kontejner Public Preview. Vyberte **Další**a vyberte **připojit**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Zadejte sdílený přístupový podpis pro kontejner.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Vyberte kontejner připojené Public Preview a dvakrát klikněte na složku `CustomSetupScript`. V této složce jsou následující položky:

      1. Složka `Sample`, která obsahuje vlastní instalaci pro instalaci základní úlohy do každého uzlu vašeho Azure-SSIS IR. Úkol neprovede žádnou akci, ale během několika sekund režim spánku. Složka také obsahuje složku `gacutil`, celý obsah, na který se (`gacutil.exe`, `gacutil.exe.config`a `1033\gacutlrc.dll`) dá zkopírovat do kontejneru. Kromě toho `main.cmd` obsahuje komentáře pro trvalé přihlašovací údaje ke sdíleným složkám souborů.

      1. Složka `UserScenarios`, která obsahuje několik vlastních nastavení pro scénáře reálných uživatelů.

   ![Obsah kontejneru verze Public Preview](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Dvakrát klikněte na složku `UserScenarios`. V této složce jsou následující položky:

      1. Složka `.NET FRAMEWORK 3.5`, která obsahuje vlastní instalaci pro instalaci starší verze .NET Framework, která může být vyžadována pro vlastní součásti na každém uzlu vašeho Azure-SSIS IR.

      1. Složka `BCP`, která obsahuje vlastní instalační program pro instalaci SQL Server nástrojů příkazového řádku (`MsSqlCmdLnUtils.msi`), včetně programu hromadného kopírování (`bcp`), na každém uzlu Azure-SSIS IR.

      1. Složka `EXCEL`, která obsahuje vlastní instalaci pro instalaci Open-Source sestavení (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`a `ExcelDataReader.dll`) na každém uzlu vašeho Azure-SSIS IR.

      1. `ORACLE ENTERPRISE` složky, která obsahuje skript vlastní instalace (`main.cmd`) a soubor bezobslužné instalace (`client.rsp`) pro instalaci konektorů Oracle a ovladače OCI do každého uzlu Azure-SSIS IR edice Enterprise. Tato instalace vám umožní používat Správce připojení Oracle, zdroj a cíl. Nejdřív Stáhněte konektory Microsoft Connectors v 5.0 pro Oracle (`AttunitySSISOraAdaptersSetup.msi` a `AttunitySSISOraAdaptersSetup64.msi`) z [webu Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) a nejnovějšího klienta Oracle – například `winx64_12102_client.zip` – od [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), a pak je nahrajte společně s `main.cmd` a `client.rsp` do svého kontejneru. Použijete-li TNS pro připojení k Oracle, budete také muset stáhnout `tnsnames.ora`, upravit ho a nahrát ho do kontejneru, takže se dá zkopírovat do instalační složky Oracle během instalace.

      1. `ORACLE STANDARD ADO.NET` složky, která obsahuje skript vlastní instalace (`main.cmd`) k instalaci ovladače Oracle ODP.NET do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní používat ADO.NET Správce připojení, zdroj a cíl. Nejdřív Stáhněte nejnovější ovladač Oracle ODP.NET, například `ODP.NET_Managed_ODAC122cR1.zip`-from [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), a pak ho nahrajte společně s `main.cmd` do svého kontejneru.
       
      1. `ORACLE STANDARD ODBC` složky, která obsahuje skript vlastní instalace (`main.cmd`) k instalaci ovladače Oracle ODBC a konfiguraci DSN v každém uzlu Azure-SSIS IR. Tato instalace umožňuje pro připojení k serveru Oracle použít Správce připojení ODBC/zdroj/cíl nebo Power Query Správce připojení a zdroj se zdrojem dat ODBC. Nejdřív si stáhněte nejnovějšího okamžitého klienta Oracle (základní balíček nebo základní balíček verze Lite) a balíček ODBC – například 64 balíčků (základní balíček: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, základní balíček [s příponou](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) lite: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, balíček odbc: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) nebo 32 balíčky z [tohoto místa](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (základní balíček: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, základní balíček Lite: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, balíček ODBC: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) a pak je nahrajte společně s `main.cmd` do svého kontejneru.

      1. `SAP BW` složky, která obsahuje skript vlastní instalace (`main.cmd`) k instalaci sestavení SAP .NET Connector (`librfc32.dll`) na každý uzel vaší Azure-SSIS IR edice Enterprise. Tato instalace umožňuje použít SAP BW Správce připojení, zdroj a cíl. Nejdřív nahrajte 64 nebo 32 verzi `librfc32.dll` z instalační složky SAP do svého kontejneru spolu s `main.cmd`. Skript pak zkopíruje sestavení SAP do složky `%windir%\SysWow64` nebo `%windir%\System32` během instalace.

      1. `STORAGE` složky, která obsahuje vlastní instalaci pro instalaci Azure PowerShell na každém uzlu Azure-SSIS IR. Tato instalace umožňuje nasadit a spustit balíčky SSIS, které spouštějí [skripty prostředí PowerShell, aby bylo možné manipulovat s vaším účtem Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Zkopírujte `main.cmd`, ukázkový `AzurePowerShell.msi` (nebo nainstalujte nejnovější verzi) a `storage.ps1` do svého kontejneru. Jako šablonu pro balíčky použijte PowerShell. dtsx. Šablona balíčku kombinuje [úlohu stažení objektu BLOB v Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), která stahuje `storage.ps1` jako upravitelný skript PowerShellu, a [úlohu spuštění procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , která spouští skript na každém uzlu.

      1. `TERADATA` složky, která obsahuje skript vlastní instalace (`main.cmd`), přidružený soubor (`install.cmd`) a instalační balíčky (`.msi`). Tyto soubory instalují konektory Teradata, rozhraní API TPT a ovladač ODBC do každého uzlu vaší Azure-SSIS IR Enterprise Edition. Tato instalace umožňuje použít Správce připojení Teradata, zdroj a cíl. Nejdřív si stáhněte soubor zip Tools a Utilities (TTU) 15. x (například `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) z [Teradata](http://partnerintelligence.teradata.com)a pak ho nahrajte společně s výše uvedenými `.cmd` a `.msi` soubory do svého kontejneru.

   ![Složky ve složce scénáře pro uživatele](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Chcete-li vyzkoušet tyto vlastní instalační ukázky, zkopírujte a vložte obsah z vybrané složky do kontejneru. Při zřizování nebo překonfigurování Azure-SSIS IR pomocí prostředí PowerShell spusťte rutinu `Set-AzDataFactoryV2IntegrationRuntime` s identifikátorem URI SAS vašeho kontejneru jako s hodnotou pro nový parametr `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Další kroky

-   [Edice Enterprise Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Jak vyvíjet placené nebo licencované vlastní komponenty pro prostředí Azure-SSIS Integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
