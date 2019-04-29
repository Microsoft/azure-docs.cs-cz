---
title: Použití sady .NET SDK pro úlohy Microsoft Azure StorSimple Data Manager | Dokumentace Microsoftu
description: Další informace o použití sady .NET SDK ke spuštění úlohy správce dat StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 80f01a926b94deebab59f8ef91bfc36a4600b5f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632298"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Použití sady .NET SDK k zahájení transformace dat

## <a name="overview"></a>Přehled

Tento článek vysvětluje, jak můžete pomocí funkce transformace dat ve službě StorSimple Data Manageru k transformaci dat zařízení StorSimple. Transformovaná data je pak spotřebované jinými službami Azure v cloudu.

Můžete spustit úlohu transformace dat dvěma způsoby:

- Použití sady .NET SDK
- Použití runbooku Azure Automation
 
  Tento článek podrobně popisuje, jak vytvořit ukázková Konzolová aplikace .NET pro zahájení úlohu transformace dat a pak je sledovat pro dokončení. Další informace o tom, jak inicializovat transformace dat pomocí automatizace, přejděte na [runbook Azure Automation. použijte k aktivaci úloh transformace dat](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte:
*   Počítač se systémem:

    - Visual Studio 2012, 2013, 2015 nebo 2017.

    - Prostředí Azure Powershell. [Stáhněte si prostředí Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Správně nakonfigurovanou definice úlohy StorSimple Data Manageru v rámci skupiny prostředků.
*   Všechny požadované knihovny DLL. Stáhněte si tyto knihovny DLL z [úložiště GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) skript z úložiště GitHub.

## <a name="step-by-step-procedure"></a>Podrobný postup

Proveďte následující kroky a spusťte úlohu transformace dat pomocí .NET.

1. Pokud chcete načíst parametry konfigurace, proveďte následující kroky:
    1. Stáhněte si `Get-ConfigurationParams.ps1` ze skriptu úložiště Githubu v `C:\DataTransformation` umístění.
    1. Spustit `Get-ConfigurationParams.ps1` skript z úložiště GitHub. Zadejte následující příkaz:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Můžete předat všechny hodnoty pro ActiveDirectoryKey a AppName.

2. Skript vypíše následující hodnoty:
    * ID klienta
    * ID tenanta
    * Active Directory klíč (stejný jako ten výše)
    * ID předplatného

        ![Výstup skriptu parametry konfigurace](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Pomocí sady Visual Studio 2012, 2013 nebo 2015, vytvořit C# konzolovou aplikaci .NET.

    1. Spuštění **Visual Studio 2012/2013/2015**.
    1. Vyberte **Soubor > Nový > Projekt**.

        ![Vytvoření projektu 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Vyberte **nainstalované > šablony > Visual C# > konzolovou aplikaci**.
    3. Zadejte **DataTransformationApp** pro **název**.
    4. Vyberte **C:\DataTransformation** pro **umístění**.
    6. Projekt vytvoříte kliknutím na **OK**.

        ![Vytvoření projektu 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Teď přidejte všechny knihovny DLL v [složky knihoven DLL](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) jako **odkazy** v projektu, který jste vytvořili. Pokud chcete přidat soubory dll, postupujte takto:

   1. V sadě Visual Studio, přejděte na **zobrazení > Průzkumník řešení**.
   2. Klikněte na šipku nalevo od datové transformace aplikace project. Klikněte na tlačítko **odkazy** a potom klikněte pravým tlačítkem myši na **přidat odkaz**.
    
       ![Přidání knihoven DLL 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Přejděte do umístění složky packages, vyberte všechny knihovny DLL a klikněte na tlačítko **přidat**a potom klikněte na tlačítko **OK**.

       ![Přidání knihoven DLL 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Do zdrojového souboru (Program.cs) v projektu přidejte následující příkazy **using**.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Následující kód inicializuje instanci úlohy transformace data. Přidejte to **metodu Main**. Nahraďte hodnoty parametrů konfigurace jste dříve získali. Zařadit hodnoty **název skupiny prostředků** a **ResourceName**. **ResourceGroupName** je spojen s StorSimple Data Manager, na kterém byl nakonfigurován definice úlohy. **ResourceName** je název vaší služby Správce dat StorSimple.

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
   
7. Zadejte parametry, se kterými potřeba spustit definice úloh

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

8. Po inicializaci přidejte následující kód k aktivaci úlohy transformace dat v definici úlohy. Zapojte do příslušné **název definice úlohy**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Po vložení kódu sestavte řešení. Zde je snímek obrazovky fragment kódu k inicializaci instance úlohy transformace data.

   ![Fragment kódu k inicializaci úloha transformace dat](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Tuto úlohu transformace dat, která odpovídá do kořenového adresáře a souboru filtrů v rámci svazek StorSimple a umístí jej do zadaného kontejneru/sdílené složky. Při transformaci souboru se přidá zprávu do fronty úložiště (v rámci stejného účtu úložiště jako sdílenou složku kontejneru nebo souboru) se stejným názvem jako definice úlohy. Tuto zprávu můžete použít jako trigger pro zahájení dalšího zpracování souboru.

10. Jakmile se úloha spustila, můžete použít následující kód ke sledování úloh pro dokončení. Není to povinné pro přidání tento kód pro spuštění úlohy.

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
    Zde je snímek obrazovky ukázkové celý kód použít k aktivaci úlohy pomocí .NET.

    ![Úplné fragment kódu k aktivaci úlohy .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Další postup

[Použití StorSimple Data Manager uživatelského rozhraní pro transformaci dat](storsimple-data-manager-ui.md).
