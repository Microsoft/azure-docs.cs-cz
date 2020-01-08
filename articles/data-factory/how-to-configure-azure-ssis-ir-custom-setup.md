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
ms.date: 12/23/2019
ms.openlocfilehash: ccf7ba2fd27dabdb090be87c5438ad68471996da
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497048"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Přizpůsobení nastavení pro prostředí Azure-SSIS Integration runtime

Vlastní nastavení rozhraní Azure-SSIS Integration Runtime poskytuje rozhraní pro přidání vlastních kroků instalace během zřizování nebo opětovné konfigurace Azure-SSIS IR. 

Vlastní instalace umožňuje změnit výchozí konfiguraci a prostředí (například spustit další služby systému Windows nebo zachovat přístupová pověření pro sdílené složky) nebo nainstalovat další součásti (například sestavení, ovladače nebo rozšíření). v každém uzlu Azure-SSIS IR.

Existují dva způsoby, jak v Azure-SSIS IR provádět vlastní nastavení: Expresní vlastní nastavení bez skriptů a standardní instalační nastavení se skripty.

Pomocí expresních vlastních nastavení můžete spustit některé běžné konfigurace systému nebo příkazy systému Windows nebo nainstalovat některé oblíbené/Doporučené dodatečné součásti bez použití skriptů.  

Se standardními vlastními nastaveními musíte připravit skript a jeho přidružené soubory a všechny je nahrát do kontejneru objektů BLOB v účtu Azure Storage. Po zřízení nebo překonfigurování Azure-SSIS IR můžete zadat identifikátor URI (Uniform Resource Identifier) sdíleného přístupového podpisu (SAS) pro váš kontejner. Každý uzel vašeho Azure-SSIS IR pak stáhne skript a jeho přidružené soubory z vašeho kontejneru a spustí vlastní instalaci se zvýšenými oprávněními. Po dokončení vlastní instalace odešle každý uzel standardní výstup spuštění a další protokoly do kontejneru.

Pomocí vlastních nastavení Express nebo Standard můžete nainstalovat bezplatné i nelicencované a licencované komponenty. Pokud jste nezávislý výrobce softwaru, přečtěte si naši dokumentaci, [jak vyvíjet placené nebo licencované komponenty pro Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Uzly v2-Series Azure-SSIS IR nejsou vhodné pro vlastní instalaci, proto použijte místo toho uzly V3-Series.  Pokud už používáte uzly v2-Series, přepněte prosím na použití uzlů V3-Series co nejdříve.

## <a name="current-limitations"></a>Aktuální omezení

Následující omezení platí pouze pro standardní vlastní nastavení:

- Chcete-li ve svém skriptu použít `gacutil.exe` k instalaci sestavení v globální mezipaměti sestavení (GAC), je nutné zadat `gacutil.exe` jako součást vlastního nastavení, nebo použijte kopii uvedenou v našem Public Preview kontejneru níže.

- Pokud chcete odkazovat na podsložku ve skriptu, `msiexec.exe` nepodporuje zápis `.\`, aby odkazoval na kořenovou složku. Místo `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`použijte příkaz, jako je `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`.

- Sdílené složky pro správu, tj. skryté sdílené síťové složky automaticky vytvořené systémem Windows, nejsou aktuálně podporovány v Azure-SSIS IR.

- Ovladač IBM iSeries Access ODBC není podporován na Azure-SSIS IR. Při vlastní instalaci se může zobrazit chyba instalace. Pokud potřebujete pomoc, obraťte se prosím na podporu společnosti IBM.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K přizpůsobení Azure-SSIS IR budete potřebovat následující věci:

- [Předplatné Azure](https://azure.microsoft.com/)

- [Zřizování Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Účet Azure Storage](https://azure.microsoft.com/services/storage/). Nepožaduje se pro expresní vlastní nastavení. Pro standardní vlastní nastavení nahráváte a ukládáte vlastní instalační skript a jeho přidružené soubory do kontejneru objektů BLOB. Vlastní proces instalace také nahraje své protokoly spuštění do stejného kontejneru objektů BLOB.

## <a name="instructions"></a>Pokyny

1. Pokud chcete zřídit nebo znovu nakonfigurovat Azure-SSIS IR pomocí PowerShellu, Stáhněte a nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps). Pro expresní vlastní nastavení přejděte na krok 4.

1. Připravte vlastní instalační skript a jeho přidružené soubory (například. bat,. cmd,. exe,. dll,. msi nebo. ps1).

   1. Musíte mít soubor skriptu s názvem `main.cmd`, který je vstupním bodem vlastní instalace.

   1. Je potřeba se ujistit, že se skript dá spustit Tichě, doporučuje se nejdřív otestovat skript na místním počítači.

   1. Pokud chcete, aby byly do kontejneru nahrány další protokoly generované jinými nástroji (například `msiexec.exe`), zadejte předdefinovanou proměnnou prostředí, `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako složku protokolu ve skriptech (například `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

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

1. Když zřídíte nebo překonfigurujete Azure-SSIS IR s uživatelským rozhraním Data Factory, můžete přidat nebo odebrat vlastní nastavení tak, že v části **Upřesnit nastavení** v instalačním panelu prostředí Integration runtime zaškrtnete políčko **přizpůsobit svůj Azure-SSIS Integration runtime pomocí dalších systémových konfigurací/instalací součástí** . 

   Pokud chcete přidat standardní vlastní nastavení, zadejte identifikátor URI SAS kontejneru do pole **vlastní nastavení URI kontejneru SAS** . 
   
   Pokud chcete přidat expresní vlastní nastavení, vyberte **Nový** , abyste otevřeli panel **Přidat vlastní nastavení Express** a pak v rozevírací nabídce **typ rychlé vlastní instalace** vybrali všechny typy:

   1. Když vyberete typ **příkazu Spustit** účet, můžete zachovat přihlašovací údaje pro sdílené složky nebo soubory Azure na Azure-SSIS IR tím, že do polí **/Add**, **/User**a **/Pass** zadáte cílový název počítače nebo domény, název účtu/uživatelské jméno a klíč/heslo účtu. To se podobá spuštění příkazu Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) na místním počítači.
   
   1. Pokud vyberete možnost **Přidat typ proměnné prostředí** , můžete přidat proměnné prostředí systému Windows, které budou použity v balíčcích, které jsou spuštěny na Azure-SSIS IR, zadáním názvu proměnné prostředí a hodnoty do polí **název proměnné** a **hodnota proměnné** v uvedeném pořadí. To se podobá spuštění příkazu Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) na místním počítači.

   1. Pokud vyberete možnost **instalovat licencovaný typ součásti** , pak můžete vybrat všechny integrované komponenty od partnerů ISV v rozevírací nabídce **název součásti** :

      1. Pokud vyberete součást **Task Factory pro SentryOne** , můžete do svého Azure-SSIS IR nainstalovat sadu [úloh Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) komponent z SentryOne, a to tak, že do pole **License Key** zakoupíte licenční klíč produktu, který jste si z nich koupili. Aktuální integrovaná verze je **2019.4.3**.

      1. Pokud vyberete **OH22'S HEDDA. V/** v součást, můžete nainstalovat [HEDDA. Kvalita/čisticí data Azure-SSIS IR v/](https://hedda.io/ssis-component/) v oh22 po zakoupení služby Aktuální integrovaná verze je **1.0.13**.

      1. Pokud vyberete komponentu **oh22's SQLPhonetics.NET** , můžete nainstalovat součást [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) Data Quality/Matching z oh22 na svou Azure-SSIS IR zadáním licenčního kódu produktu, který jste si z nich koupili v poli **licenční klíč** . Aktuální integrovaná verze je **1.0.43**.
   
   Vaše přidané expresní vlastní nastavení se zobrazí v části **Upřesnit nastavení** . Pokud je chcete odebrat, můžete zaškrtnout políčka a pak vybrat **Odstranit**.

   ![Rozšířená nastavení s vlastními nastaveními](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

   Při zřizování nebo překonfigurování Azure-SSIS IR pomocí prostředí PowerShell můžete přidat nebo odebrat vlastní nastavení spuštěním rutiny `Set-AzDataFactoryV2IntegrationRuntime` před spuštěním Azure-SSIS IR.
   
   V případě standardních vlastních nastavení můžete identifikátor URI SAS svého kontejneru zadat jako hodnotu parametru `SetupScriptContainerSasUri`. Příklad:

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                         -Name $MyAzureSsisIrName `
                                         -ResourceGroupName $MyResourceGroupName `
                                         -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                           -Name $MyAzureSsisIrName `
                                           -ResourceGroupName $MyResourceGroupName
   ```
   
   Po dokončení standardní vlastní instalace a spuštění Azure-SSIS IR můžete najít standardní výstup `main.cmd` a další protokoly spuštění ve složce `main.cmd.log` kontejneru úložiště.

1. Pokud chcete zobrazit některé ukázky standardních vlastních nastavení, připojte se k našemu Public Preview kontejneru pomocí Průzkumník služby Azure Storage.

   1. V části **(místní a připojená)** klikněte pravým tlačítkem na **účty úložiště**, vyberte **připojit k Azure Storage**, vyberte **Použít připojovací řetězec nebo identifikátor URI sdíleného přístupového podpisu**a pak vyberte **Další**.

      ![Připojení ke službě Azure Storage pomocí sdíleného přístupového podpisu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. Vyberte **použít identifikátor URI SAS** a zadejte následující identifikátor URI SAS pro kontejner Public Preview. Vyberte **Další**a pak vyberte **připojit**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Zadejte sdílený přístupový podpis pro kontejner.](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. Vyberte kontejner připojené Public Preview a dvakrát klikněte na složku `CustomSetupScript`. V této složce jsou následující položky:

      1. Složka `Sample`, která obsahuje vlastní instalaci pro instalaci základní úlohy do každého uzlu vašeho Azure-SSIS IR. Úkol neprovede žádnou akci, ale během několika sekund režim spánku. Složka také obsahuje složku `gacutil`, celý obsah, na který se (`gacutil.exe`, `gacutil.exe.config`a `1033\gacutlrc.dll`) dá zkopírovat do kontejneru.

      1. Složka `UserScenarios`, která obsahuje několik vlastních ukázek instalace ze scénářů reálných uživatelů.

      ![Obsah kontejneru verze Public Preview](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. Dvojím kliknutím na složku `UserScenarios` vyhledejte následující položky:

      1. Složka `.NET FRAMEWORK 3.5`, která obsahuje vlastní instalaci pro instalaci starší verze .NET Framework, která může být vyžadována pro vlastní součásti na každém uzlu vašeho Azure-SSIS IR.

      1. Složka `BCP`, která obsahuje vlastní instalační program pro instalaci SQL Server nástrojů příkazového řádku (`MsSqlCmdLnUtils.msi`), včetně programu hromadného kopírování (`bcp`), na každém uzlu Azure-SSIS IR.

      1. `EXCEL` složky, která obsahuje skript vlastní instalace (`main.cmd`) k instalaci C# sestavení nebo knihoven, které lze použít v úlohách skriptu k dynamickému čtení a zápisu souborů aplikace Excel do každého uzlu vašeho Azure-SSIS IR. Nejdřív Stáhněte `ExcelDataReader.dll` [odsud a `DocumentFormat.OpenXml.dll` z tohoto](https://www.nuget.org/packages/ExcelDataReader/) místa [a pak](https://www.nuget.org/packages/DocumentFormat.OpenXml/)je nahrajte společně s `main.cmd` do svého kontejneru. Případně, pokud chcete pouze použít standardního Správce připojení aplikace Excel nebo zdroj/cíl, je již v Azure-SSIS IR předinstalován požadovaný přístup pro distribuci, takže nepotřebujete žádnou vlastní instalaci.
      
      1. `MYSQL ODBC` složky, která obsahuje skript vlastní instalace (`main.cmd`), který nainstaluje ovladače MySQL ODBC do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní připojit se k serveru MySQL pomocí Správce připojení ODBC/zdroj/cíl. Nejdřív Stáhněte nejnovější verzi 64 a 32 instalačních programů ovladačů MySQL ODBC, například `mysql-connector-odbc-8.0.13-winx64.msi` a `mysql-connector-odbc-8.0.13-win32.msi`-from [MySQL](https://dev.mysql.com/downloads/connector/odbc/), a pak je nahrajte společně s `main.cmd` do svého kontejneru.

      1. `ORACLE ENTERPRISE` složky, která obsahuje skript vlastní instalace (`main.cmd`) a soubor bezobslužné instalace (`client.rsp`) pro instalaci konektorů Oracle a ovladače OCI do každého uzlu Azure-SSIS IR edice Enterprise. Tato instalace vám umožní připojit se k serveru Oracle pomocí Správce připojení Oracle/zdroj/cíl. Nejdřív Stáhněte konektory Microsoft Connectors v 5.0 pro Oracle (`AttunitySSISOraAdaptersSetup.msi` a `AttunitySSISOraAdaptersSetup64.msi`) z [webu Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) a nejnovějšího klienta Oracle – například `winx64_12102_client.zip` – od [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), a pak je nahrajte společně s `main.cmd` a `client.rsp` do svého kontejneru. Použijete-li TNS pro připojení k Oracle, budete také muset stáhnout `tnsnames.ora`, upravit ho a nahrát ho do kontejneru, takže se dá zkopírovat do instalační složky Oracle během instalace.

      1. `ORACLE STANDARD ADO.NET` složky, která obsahuje skript vlastní instalace (`main.cmd`) k instalaci ovladače Oracle ODP.NET do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní připojit se k serveru Oracle pomocí Správce připojení ADO.NET/zdroj/cíl. Nejdřív Stáhněte nejnovější ovladač Oracle ODP.NET, například `ODP.NET_Managed_ODAC122cR1.zip`-from [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), a nahrajte ho společně s `main.cmd` do svého kontejneru.
       
      1. `ORACLE STANDARD ODBC` složky, která obsahuje skript vlastní instalace (`main.cmd`) k instalaci ovladače Oracle ODBC a konfiguraci DSN v každém uzlu Azure-SSIS IR. Tato instalace umožňuje pro připojení k serveru Oracle použít Správce připojení ODBC/zdroj/cíl nebo Power Query Správce připojení a zdroj se zdrojem dat ODBC. Nejdřív si stáhněte nejnovějšího okamžitého klienta Oracle (základní balíček nebo základní balíček verze Lite) a balíček ODBC – například 64 [balíčků (základní](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) balíček: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, základní balíček s příponou lite: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, balíček odbc: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) nebo 32 balíčky z [tohoto místa](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (základní balíček: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, základní balíček: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, balíček ODBC: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) a pak je nahrajte společně s `main.cmd` do svého kontejneru.

      1. `ORACLE STANDARD OLEDB` složky, která obsahuje skript vlastní instalace (`main.cmd`) k instalaci ovladače Oracle OLEDB do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní připojit se k serveru Oracle pomocí Správce připojení OLEDB/zdroj/cíl. Nejdřív Stáhněte nejnovější ovladač Oracle OLEDB, například `ODAC122010Xcopy_x64.zip`-from [Oracle](https://www.oracle.com/partners/campaign/index-090165.html), a nahrajte ho společně pomocí `main.cmd` do svého kontejneru.

      1. `POSTGRESQL ODBC` složky, která obsahuje skript vlastní instalace (`main.cmd`), který nainstaluje ovladače PostgreSQL ODBC do každého uzlu vašeho Azure-SSIS IR. Tato instalace vám umožní připojit se k PostgreSQL serveru pomocí Správce připojení ODBC/zdroj/cíl. Nejdřív Stáhněte nejnovější 64 a 32 PostgreSQL instalačních ovladačů ovladačů ODBC – například `psqlodbc_x64.msi` a `psqlodbc_x86.msi`-from [PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/)a pak je nahrajte společně s `main.cmd` do svého kontejneru.

      1. `SAP BW` složky, která obsahuje skript vlastní instalace (`main.cmd`) k instalaci sestavení SAP .NET Connector (`librfc32.dll`) na každý uzel vaší Azure-SSIS IR edice Enterprise. Tato instalace umožňuje použít SAP BW Správce připojení/zdroj/cíl pro připojení k serveru SAP BW. Nejdřív nahrajte 64 nebo 32 verzi `librfc32.dll` z instalační složky SAP společně s `main.cmd` do svého kontejneru. Skript pak zkopíruje sestavení SAP do složky `%windir%\SysWow64` nebo `%windir%\System32` během instalace.

      1. `STORAGE` složky, která obsahuje vlastní instalaci pro instalaci Azure PowerShell na každém uzlu Azure-SSIS IR. Tato instalace umožňuje nasadit a spustit balíčky SSIS, které spouštějí [skripty prostředí PowerShell, aby bylo možné manipulovat s vaším účtem Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Zkopírujte `main.cmd`, ukázkový `AzurePowerShell.msi` (nebo použijte nejnovější verzi) a `storage.ps1` do svého kontejneru. Jako šablonu pro balíčky použijte PowerShell. dtsx. Šablona balíčku kombinuje [úlohu stažení objektu BLOB v Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), která stahuje `storage.ps1` jako upravitelný skript PowerShellu, a [úlohu spuštění procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , která spouští skript na každém uzlu.

      1. `TERADATA` složky, která obsahuje skript vlastní instalace (`main.cmd`), přidružený soubor (`install.cmd`) a instalační balíčky (`.msi`). Tyto soubory instalují konektory Teradata, rozhraní TPT API a ovladač ODBC na každý uzel vaší Azure-SSIS IR Enterprise Edition. Tato instalace umožňuje použít Správce připojení Teradata/zdroj/cíl pro připojení k serveru Teradata. Nejdřív si stáhněte soubor zip Tools a Utilities 15. x (například `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) z [Teradata](http://partnerintelligence.teradata.com)a pak ho nahrajte společně s výše uvedenými `.cmd` a `.msi` soubory do svého kontejneru.

      1. `ZULU OPENJDK` složky, která obsahuje skript vlastní instalace (`main.cmd`) a soubor PowerShell (`install_openjdk.ps1`) pro instalaci Zulu OpenJDK do každého uzlu Azure-SSIS IR. Tato instalace umožňuje použít Azure Data Lake Store nebo flexibilní konektory souborů ke zpracování souborů ORC/Parquet. Další informace najdete [tady](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) . Nejdřív Stáhněte nejnovější Zulu OpenJDK – například `zulu8.33.0.1-jdk8.0.192-win_x64.zip` [– odsud a](https://www.azul.com/downloads/zulu/zulu-windows/)nahrajte ho společně s `main.cmd` a `install_openjdk.ps1` do svého kontejneru.

      ![Složky ve složce scénáře pro uživatele](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. Chcete-li vyzkoušet tyto vlastní instalační ukázky, zkopírujte a vložte obsah z vybrané složky do kontejneru.
   
      Když zřídíte nebo překonfigurujete Azure-SSIS IR s uživatelským rozhraním Data Factory, zaškrtněte políčko **přizpůsobit Azure-SSIS Integration runtime ostatním konfiguracím systému/instalací součástí** v části **Upřesnit nastavení** a zadejte identifikátor URI SAS kontejneru do pole **vlastní nastavení URI kontejneru SAS** .
   
      Při zřizování nebo překonfigurování Azure-SSIS IR pomocí prostředí PowerShell spusťte rutinu `Set-AzDataFactoryV2IntegrationRuntime` s identifikátorem URI SAS vašeho kontejneru jako hodnotu parametru `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Další kroky

-   [Edice Enterprise Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Jak vyvíjet placené nebo licencované vlastní komponenty pro prostředí Azure-SSIS Integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
