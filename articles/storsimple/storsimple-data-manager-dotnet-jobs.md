---
title: Použití sady .NET SDK pro úlohy Microsoft Azure StorSimple Data Manager
description: Naučte se používat sadu .NET SDK v rámci služby StorSimple Data Manager k transformaci dat zařízení StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b18627d2806662d6d966af95d51873d5623b2393
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961120"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Zahájení transformace dat s využitím sady .NET SDK

## <a name="overview"></a>Přehled

Tento článek vysvětluje, jak můžete pomocí funkce transformace dat v rámci služby StorSimple Data Manager transformovat data zařízení StorSimple. Transformovaná data pak spotřebují jiné služby Azure v cloudu.

Úlohu transformace dat můžete spustit dvěma způsoby:

- Použití sady .NET SDK
- Použití Azure Automation Runbook
 
  Tento článek podrobně popisuje, jak vytvořit ukázkovou konzolovou aplikaci .NET pro zahájení úlohy transformace dat a pak ji sledovat pro dokončení. Další informace o tom, jak iniciovat transformaci dat pomocí automatizace, najdete v tématu [použití Azure Automation Runbook k aktivaci úloh transformace dat](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:
*   Počítač se systémem:

    - Visual Studio 2012, 2013, 2015 nebo 2017.

    - Prostředí Azure PowerShell. [Stáhněte si Azure PowerShell](/powershell/azure/).
*   Správně nakonfigurovaná definice úlohy v StorSimple Data Manager v rámci skupiny prostředků.
*   Všechny požadované knihovny DLL. Stáhněte si tyto knihovny DLL z [úložiště GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) skript z úložiště GitHubu.

## <a name="step-by-step-procedure"></a>Podrobný postup

Provedením následujících kroků spusťte úlohu transformace dat pomocí rozhraní .NET.

1. Chcete-li načíst parametry konfigurace, proveďte následující kroky:
    1. Stáhněte si `Get-ConfigurationParams.ps1` ze skriptu úložiště GitHub v `C:\DataTransformation` umístění.
    1. Spusťte `Get-ConfigurationParams.ps1` skript z úložiště GitHub. Zadejte následující příkaz:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Můžete předat libovolné hodnoty pro ActiveDirectoryKey a AppName.

2. Tento skript vytvoří výstup následujících hodnot:
    * ID klienta
    * ID tenanta
    * Klíč služby Active Directory (stejný jako ten, který jste zadali výše)
    * ID předplatného

        ![Výstup skriptu parametrů konfigurace](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Pomocí sady Visual Studio 2012, 2013 nebo 2015 vytvořte konzolovou aplikaci C# .NET.

    1. Spusťte **Visual Studio 2012/2013/2015**.
    1. Vyberte **Soubor > Nový > Projekt**.

        ![Vytvořit projekt 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Vyberte **nainstalované > šablony > Visual C# > Konzolová aplikace**.
    3. Jako **název** zadejte **DataTransformationApp** .
    4. Jako **umístění** vyberte **C:\DataTransformation** .
    6. Kliknutím na tlačítko **OK** vytvořte projekt.

        ![Vytvoření projektu 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Nyní přidejte všechny knihovny DLL přítomné ve [složce DLL](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) jako **odkazy** v projektu, který jste vytvořili. Chcete-li přidat soubory DLL, proveďte následující:

   1. V aplikaci Visual Studio se podívejte na **zobrazení > Průzkumník řešení**.
   2. Klikněte na šipku nalevo od projektu aplikace transformace dat. Klikněte na **odkazy** a potom klikněte pravým tlačítkem na **Přidat odkaz**.
    
       ![Přidat knihovny DLL 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Přejděte do umístění složky balíčky, vyberte všechny knihovny DLL a klikněte na tlačítko **Přidat** a poté klikněte na tlačítko **OK**.

       ![Přidat knihovny DLL 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Do zdrojového souboru (Program.cs) v projektu přidejte následující příkazy **using**.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Následující kód Inicializuje instanci úlohy transformace dat. Přidejte ho do **metody Main**. Nahraďte hodnoty parametrů konfigurace, které byly získány dříve. Připojte hodnoty **název skupiny prostředků** a **resourceName**. **ResourceGroupName** je přidružená k StorSimple data Manager, na které byla definice úlohy nakonfigurovaná. **ResourceName** je název vaší služby StorSimple data Manager.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Zadejte parametry, pomocí kterých je třeba spustit definici úlohy.

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    ANI

    Pokud chcete změnit parametry definice úlohy během běhu, přidejte následující kód:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Po inicializaci přidejte následující kód, který aktivuje úlohu transformace dat v definici úlohy. Připojte příslušný **název definice úlohy**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Po vložení kódu Sestavte řešení. Zde je snímek obrazovky fragmentu kódu pro inicializaci instance úlohy transformace dat.

   ![Fragment kódu pro inicializaci úlohy transformace dat](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Tato úloha transformuje data, která odpovídají kořenovým adresářům a filtrům souborů ve svazku StorSimple, a umístí je do zadaného kontejneru nebo sdílené složky souborů. Když se soubor transformuje, do fronty úložiště (ve stejném účtu úložiště jako kontejner/sdílená složka) se přidá zpráva se stejným názvem jako má definice úlohy. Tuto zprávu lze použít jako aktivační událost k zahájení dalšího zpracování souboru.

10. Po aktivaci úlohy můžete použít následující kód ke sledování úlohy k dokončení. Pro spuštění úlohy není nutné tento kód přidat.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    Zde je snímek obrazovky s celou ukázkou kódu, který se používá ke spuštění úlohy pomocí .NET.

    ![Úplný fragment kódu pro aktivaci úlohy .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Další kroky

[K transformaci dat použijte StorSimple data Manager uživatelské rozhraní](storsimple-data-manager-ui.md).