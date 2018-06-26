---
title: Přizpůsobení nastavení pro modul runtime integrace Azure SSIS | Microsoft Docs
description: Tento článek popisuje, jak k instalaci dalších součástí nebo změnit nastavení použít rozhraní vlastní instalace pro modul runtime integrace Azure SSIS
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 76308bbb06d6bf1cdc9147258f7c26babae371a9
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750481"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Přizpůsobení nastavení pro modul runtime integrace Azure SSIS

Vlastní instalace rozhraní pro modul Runtime integrace Azure SSIS poskytuje rozhraní pro přidání kroků vlastní instalační program během zřizování nebo rekonfigurace vaší Azure SSIS infračerveného signálu. Vlastní instalace vám umožňuje změnit výchozí operační konfigurace nebo prostředí (například spustit další služby systému Windows nebo zachovat přihlašovací údaje pro sdílené složky) nebo nainstalovat další součásti (například sestavení, ovladače nebo rozšíření) na každém uzlu vaší Azure SSIS infračerveného signálu.

Konfigurujete vlastní instalace a příprava skript a jeho přidružené soubory a uložte je do kontejneru objektů blob v účtu úložiště Azure. Zadejte sdíleného přístupového podpisu (SAS) identifikátor URI (Uniform Resource) pro váš kontejner při zřízení nebo změnit konfiguraci vašeho Azure SSIS infračerveného signálu. Každý uzel vaší Azure SSIS IR potom stáhne skript a jeho přidružené soubory z vašeho kontejneru a spustí vlastní instalace se zvýšenými oprávněními. Po dokončení vlastní instalace ukládání každý uzel ve standardním výstupu spouštění a další protokoly do vašeho kontejneru.

Můžete nainstalovat součásti volné nebo bez licence i placené nebo licencovanou součástí. Pokud jste nezávislý dodavatel softwaru, přečtěte si téma [způsob vývoje placené nebo licenci součásti služby SSIS Azure IR](how-to-develop-azure-ssis-ir-licensed-components.md).


## <a name="current-limitations"></a>Aktuální omezení

-   Pokud chcete použít `gacutil.exe` instalace sestavení v globální mezipaměti sestavení (GAC), je třeba zadat `gacutil.exe` jako součást vlastní instalaci nebo použití kopie poskytnuté v kontejneru verzi Public Preview.

-   Pokud chcete odkazovat na podsložku ve vašem skriptu, `msiexec.exe` nepodporuje `.\` zápis tak, aby odkazovaly do kořenové složky. Použijte příkaz jako `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` místo `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

-   Pokud potřebujete připojení k vaší IR Azure SSIS s vlastní instalace k virtuální síti, podporuje se jenom virtuální síť Azure Resource Manager. Klasické virtuální sítě není podporován.

-   Sdílená složka pro správu není aktuálně podporována u na infračerveného signálu Azure SSIS.

## <a name="prerequisites"></a>Požadavky

Chcete-li přizpůsobit vaší Azure SSIS reakcí na Incidenty, potřebujete následující věci:

-   [Předplatné Azure](https://azure.microsoft.com/)

-   [Server služby Azure SQL Database nebo spravované Instance](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Zřídit vaší IR Azure SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Účet úložiště Azure](https://azure.microsoft.com/services/storage/). Pro vlastní instalaci nahrát a ukládat vlastní instalační skript a jeho přidružené soubory v kontejneru objektů blob. Protokoly spuštění vlastního instalačního procesu také ukládání do kontejneru objektů blob.

## <a name="instructions"></a>Pokyny

2.  Stáhněte a nainstalujte [prostředí Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (verze 5.4 nebo novější).

3.  Připravte vlastní instalační skript a jeho přidružené soubory (například soubory .bat, .cmd, .exe, .dll, .msi nebo .ps1).

    1.  Musíte mít soubor skriptu s názvem `main.cmd`, což je vstupní bod vaše vlastní nastavení.

    2.  Pokud chcete další protokolů generovaných v jiných nástrojích (například `msiexec.exe`) k odeslání do vašeho kontejneru, zadejte proměnnou prostředí předdefinované `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako složka protokolu ve skriptech (například `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

4.  Stáhnout, nainstalovat a spustit [Azure Storage Explorer](http://storageexplorer.com/).

    1.  V části **(místní a připojené)**, vyberte práva **účty úložiště** a vyberte **připojit k úložišti Azure**.

       ![Připojení k úložišti Azure](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  Vyberte **použít název účtu úložiště a klíč** a vyberte **Další**.

       ![Použití názvu a klíče účtu úložiště](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  Zadejte název účtu úložiště Azure a klíč, vyberte **Další**a potom vyberte **Connect**.

       ![Zadejte název účtu úložiště a klíč](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  V rámci účtu Azure Storage připojené klikněte pravým tlačítkem na **kontejnery objektů Blob**, vyberte **vytvořit kontejner objektů Blob**a pojmenujte nový kontejner.

       ![Vytvoření kontejneru objektů blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  Vyberte nový kontejner a nahrát vlastní instalační skript a jeho přidružené soubory. Ujistěte se, že nahrajete `main.cmd` na nejvyšší úrovni kontejneru, není v libovolné složky. 

       ![Nahrání souborů do kontejneru objektů blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  Klikněte pravým tlačítkem na kontejner a vyberte **sdíleného přístupového podpisu**.

       ![Získání sdíleného přístupového podpisu kontejneru](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  Vytvořit identifikátor URI SAS pro váš kontejner s dobou vypršení platnosti dostatečně dlouhý a čtení + zápis + seznamu oprávnění. Budete potřebovat identifikátor URI SAS ke stažení a spuštění vlastní instalační skript a jeho přidružené soubory vždy, když je libovolného uzlu vaší Azure SSIS IR obnovit z Image nebo restartovat. Potřebujete oprávnění nahrát protokoly spuštění instalace zapisovat.

        > [!IMPORTANT]
        > Ujistěte se, že identifikátor URI SAS, nemá prošlou platnost a vlastní instalace prostředky jsou vždy k dispozici během celý životní cyklus vaší Azure SSIS IR, od vytvoření k odstranění, zejména v případě, že pravidelně zastavení a spuštění vaší Azure SSIS IR během této doby.

       ![Vygenerování sdíleného přístupového podpisu kontejneru](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  Zkopírujte a uložte identifikátor URI SAS vašeho kontejneru.

       ![Zkopírujte a uložte sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  Při zřízení nebo překonfigurujte vaší IR Azure SSIS pomocí prostředí PowerShell, než začnete vaší IR Azure SSIS, spusťte `Set-AzureRmDataFactoryV2IntegrationRuntime` rutiny s identifikátor URI SAS vašeho kontejneru jako hodnotu pro nové `SetupScriptContainerSasUri` parametr. Příklad:

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  Po dokončení vlastní instalace a spuštění vaší IR Azure SSIS, můžete najít ve standardním výstupu z `main.cmd` a dalších provádění přihlásí `main.cmd.log` složky vašeho kontejneru úložiště.

2.  Chcete-li zobrazit další příklady vlastní instalace připojit ke kontejneru ve verzi Public Preview s Azure Storage Explorer.

    a.  V části **(místní a připojené)**, klikněte pravým tlačítkem na **účty úložiště**, vyberte **připojit k úložišti Azure**, vyberte **použít připojovací řetězec nebo sdíleného přístupu podpis URI**a potom vyberte **Další**.

       ![Připojit k úložišti Azure pomocí sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Vyberte **používání identifikátoru URI SAS** a zadejte následující identifikátor URI SAS pro kontejner verzi Public Preview. Vyberte **Další**a vyberte položku **Connect**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Zadejte sdíleného přístupového podpisu kontejneru](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Vyberte kontejner připojené verzi Public Preview a dvakrát klikněte na `CustomSetupScript` složky. V této složce jsou následující položky:

       1. A `Sample` složky, která obsahuje vlastní instalační program a nainstalujte základní úkolů na každém uzlu vaší Azure SSIS infračerveného signálu. Úloha se nic nestane. ale režimu spánku na několik sekund. Složka také obsahuje `gacutil` složky, která obsahuje `gacutil.exe`. Kromě toho `main.cmd` obsahuje komentáře k uchování přihlašovací údaje pro sdílené složky.

       2. A `UserScenarios` složky, která obsahuje některá vlastní nastavení pro reálný uživatel scénáře.

    ![Obsah kontejneru verzi public preview](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Dvakrát klikněte `UserScenarios` složky. V této složce jsou následující položky:

       1. A `.NET FRAMEWORK 3.5` složky, která obsahuje vlastní instalační program nainstalovat dřívější verzi rozhraní .NET Framework, který může být nezbytný pro vlastní součásti na každý uzel vaší Azure SSIS infračerveného signálu.

       2. A `BCP` složky, která obsahuje vlastní instalační program a nainstalujte nástroje příkazového řádku systému SQL Server (`MsSqlCmdLnUtils.msi`), včetně program hromadného kopírování (`bcp`), na každém uzlu vaší Azure SSIS infračerveného signálu.

       3. `EXCEL` Složky, která obsahuje vlastní instalační program a nainstalujte open-source sestavení (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`, a `ExcelDataReader.dll`) na každém uzlu vaší Azure SSIS infračerveného signálu.

       4. `MSDTC` Složky, která obsahuje vlastní instalace k úpravě konfigurace sítě a zabezpečení pro službu Microsoft Distributed Transaction Coordinator služba MSDTC () na každém uzlu vaší Azure SSIS infračerveného signálu. Aby se zajistilo, že je spuštěna služba MSDTC, přidejte úlohy spustit proces na začátku toku řízení ve vašich balíčcích na spusťte následující příkaz: `%SystemRoot%\system32\cmd.exe /c powershell -Command "Start-Service MSDTC"` 

       5. `ORACLE ENTERPRISE` Složky, která obsahuje vlastní instalační skript (`main.cmd`) a konfigurační soubor bezobslužné instalace (`client.rsp`) k instalaci ovladačů Oracle OCI na každém uzlu vaší Azure SSIS IR Enterprise Edition. Tato instalace vám umožní používat Správce připojení Oracle, zdroj a cíl. Nejdřív, například stáhnout nejnovější verzi klienta Oracle - `winx64_12102_client.zip` – [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) a nahrajte ji společně s `main.cmd` a `client.rsp` do vašeho kontejneru. Používáte-li se připojit k Oracle TNS, budete také muset stáhnout `tnsnames.ora`, upravovat a nahrajte ho do kontejneru, takže je možné zkopírovat do instalační složky Oracle během instalace.

       6. `ORACLE STANDARD` Složky, která obsahuje vlastní instalační skript (`main.cmd`) k instalaci ovladačů Oracle ODP.NET na každém uzlu vaší Azure SSIS infračerveného signálu. Tato instalace vám umožní používat Správce připojení ADO.NET, zdroj a cíl. Nejprve stáhnout nejnovější ovladač Oracle ODP.NET – například `ODP.NET_Managed_ODAC122cR1.zip` – [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)a nahrajte ji společně s `main.cmd` do vašeho kontejneru.

       7. `SAP BW` Složky, která obsahuje vlastní instalační skript (`main.cmd`) k instalaci konektoru sestavení SAP .NET (`librfc32.dll`) na každém uzlu vaší Azure SSIS IR Enterprise Edition. Tato instalace vám umožní používat Správce připojení SAP BW, zdroj a cíl. Nejprve nahrát 64bitovou nebo 32bitovou verzi `librfc32.dll` z instalační složky SAP do kontejneru, společně s `main.cmd`. Skript pak zkopíruje sestavení SAP do `%windir%\SysWow64` nebo `%windir%\System32` složky během instalace.

       8. A `STORAGE` složky, která obsahuje vlastní instalační program a nainstalujte prostředí Azure PowerShell v každém uzlu vaší Azure SSIS infračerveného signálu. Tato instalace vám umožňuje nasazení a spuštění služby SSIS balíčky, které používají [skriptů prostředí PowerShell k manipulaci s účtu úložiště Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Kopírování `main.cmd`, ukázku `AzurePowerShell.msi` (nebo nainstalujte nejnovější verzi) a `storage.ps1` do vašeho kontejneru. Použijte PowerShell.dtsx jako šablonu pro balíčky. Kombinuje šabloně balíček [úloh stáhnout Azure Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), které stahování `storage.ps1` jako skript prostředí PowerShell upravitelnými a [úlohy spustit proces](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , která se spouští skript na každém uzlu.

       9. A `TERADATA` složky, která obsahuje vlastní instalační skript (`main.cmd)`, jeho přidružený soubor (`install.cmd`) a instalační balíčky (`.msi`). Tyto soubory instalace konektorů, rozhraní API TPT a ovladač ODBC Teradata na každém uzlu vaší Azure SSIS IR Enterprise Edition. Tato instalace vám umožní používat Správce připojení Teradata, zdroj a cíl. Nejprve stáhněte soubor zip 15.x Teradata nástroje a nástroje (TTU) (například `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) z [Teradata](http://partnerintelligence.teradata.com)a odešlete ji spolu s výše `.cmd` a `.msi` soubory do vašeho kontejneru.

    ![Složky ve složce uživatelské scénáře](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Pokud chcete vyzkoušet tyto ukázky vlastní instalace, zkopírujte a vložte obsah z vybrané složky do vašeho kontejneru. Při zřízení nebo změnit konfiguraci vašeho IR Azure SSIS pomocí prostředí PowerShell, spusťte `Set-AzureRmDataFactoryV2IntegrationRuntime` rutiny s identifikátor URI SAS vašeho kontejneru jako hodnotu pro nové `SetupScriptContainerSasUri` parametr.

## <a name="next-steps"></a>Další postup

-   [Enterprise Edition modulu runtime integrace Azure SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Jak vyvíjet placené nebo licencovaná vlastní komponenty pro modul runtime integrace Azure SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
