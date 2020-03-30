---
title: Použití sady .NET SDK pro úlohy Microsoft Azure StorSimple Data Manager
description: Naučte se používat stesku .NET SDK ke spuštění úloh Správce dat StorSimple
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b7cf1d3b9d4a9d751348c4792f904062b00ac104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76270728"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Použití sady .NET SDK k zahájení transformace dat

## <a name="overview"></a>Přehled

Tento článek vysvětluje, jak můžete použít funkci transformace dat v rámci služby StorSimple Data Manager k transformaci dat zařízení StorSimple. Transformovaná data se pak spotřebovávají jinými službami Azure v cloudu.

Úlohu transformace dat můžete spustit dvěma způsoby:

- Použití sady .NET SDK
- Použití runbooku Azure Automation
 
  Tento článek podrobně popisuje, jak vytvořit ukázkovou aplikaci konzoly .NET k zahájení úlohy transformace dat a sledovat ji pro dokončení. Další informace o tom, jak zahájit transformaci dat prostřednictvím automatizace, přejděte na [spuštění úloh transformace dat pomocí runbooku Azure Automation](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte:
*   Počítač se systémem:

    - Visual Studio 2012, 2013, 2015 nebo 2017.

    - Azure Powershell. [Stáhnout Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Správně nakonfigurovaná definice úlohy ve Správci dat StorSimple v rámci skupiny prostředků.
*   Všechny požadované dlls. Stáhněte si tyto dll z [úložiště GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)z úložiště GitHub.

## <a name="step-by-step-procedure"></a>Postup krok za krokem

Pomocí spuštění úlohy transformace dat proveďte následující kroky.

1. Chcete-li načíst parametry konfigurace, postupujte takto:
    1. Stáhněte `Get-ConfigurationParams.ps1` si skript úložiště GitHub v `C:\DataTransformation` umístění.
    1. Spusťte `Get-ConfigurationParams.ps1` skript z úložiště GitHub. Zadejte následující příkaz:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Můžete předat libovolné hodnoty pro ActiveDirectoryKey a AppName.

2. Tento skript vyvodí následující hodnoty:
    * ID klienta
    * ID tenanta
    * Klíč služby Active Directory (stejný jako ten, který byl zadán výše)
    * ID předplatného

        ![Výstup skriptu konfiguračních parametrů](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Pomocí Sady Visual Studio 2012, 2013 nebo 2015 vytvořte konzolovou aplikaci C# .NET.

    1. Spuštění **Sady Visual Studio 2012/2013/2015**.
    1. Vyberte **Soubor > Nový > Projekt**.

        ![Vytvoření projektu 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Vyberte **nainstalované šablony > > aplikaci Visual C# > Console .**
    3. Zadejte **DataTransformationApp** pro **název**.
    4. Vyberte **c:\DataTransformation** pro **umístění**.
    6. Kliknutím na tlačítko **OK** vytvořte projekt.

        ![Vytvoření projektu 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Nyní přidejte všechny dlls přítomné ve [složce dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) jako **odkazy** v projektu, který jste vytvořili. Chcete-li přidat soubory dll, proveďte následující kroky:

   1. V sadě Visual Studio přejděte na **zobrazit > Průzkumníka řešení**.
   2. Klikněte na šipku vlevo od projektu aplikace transformace dat. Klepněte na **odkaz a** potom klepnutím pravým tlačítkem myši **na položku Přidat odkaz**.
    
       ![Přidat dlls 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Přejděte do umístění složky balíčky, vyberte všechny dll s dll a klepněte na **tlačítko Přidat**a klepněte na tlačítko **OK**.

       ![Přidat dlls 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Do zdrojového souboru (Program.cs) v projektu přidejte následující příkazy **using**.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Následující kód inicializuje instanci úlohy transformace dat. Přidejte tuto metodu **main .** Nahraďte hodnoty konfiguračních parametrů, jak byly získány dříve. Zapojte hodnoty **název skupiny prostředků** a **název_prostředku**. **ResourceGroupName** je přidružený správce dat StorSimple, na kterém byla nakonfigurována definice úlohy. **ResourceName** je název služby StorSimple Data Manager.

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
   
7. Zadejte parametry, se kterými má být definice úlohy spuštěna.

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (NEBO)

    Pokud chcete změnit parametry definice úlohy za běhu, přidejte následující kód:

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

8. Po inicializaci přidejte následující kód, který spustí úlohu transformace dat v definici úlohy. Připojte příslušný **název definice úlohy**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Po vložení kódu vytvořte řešení. Tady je snímek obrazovky fragmentu kódu pro inicializaci instance úlohy transformace dat.

   ![Fragment kódu pro inicializaci úlohy transformace dat](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Tato úloha transformuje data, která odpovídají kořenovému adresáři a filtrům souborů v rámci svazku StorSimple, a vloží je do zadané sdílené složky kontejneru nebo souboru. Při transformaci souboru je zpráva přidána do fronty úložiště (ve stejném účtu úložiště jako kontejner nebo sdílená složka) se stejným názvem jako definice úlohy. Tuto zprávu lze použít jako aktivační událost k zahájení dalšího zpracování souboru.

10. Jakmile je úloha spuštěna, můžete použít následující kód ke sledování úlohy pro dokončení. Není nutné přidat tento kód pro spuštění úlohy.

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
    Zde je snímek obrazovky celé ukázky kódu, která slouží ke spuštění úlohy pomocí rozhraní .NET.

    ![Úplný úryvek kódu pro spuštění úlohy .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Další kroky

[Pomocí ui Správce dat StorSimple pro transformaci dat](storsimple-data-manager-ui.md).
