---
title: Použití sady .NET SDK pro Microsoft Azure StorSimple Data Manager úlohy | Microsoft Docs
description: Další informace o použití sady .NET SDK ke spuštění úlohy Data Manager zařízení StorSimple
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
ms.openlocfilehash: d15a5cbda2f0c2a363b40e94c38fed6631aa81b5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27928232"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Zahájit transformaci dat pomocí .net SDK

## <a name="overview"></a>Přehled

Tento článek vysvětluje, jak můžete použít funkci transformaci dat v rámci služby StorSimple Manager dat k transformaci dat zařízení StorSimple. Transformovaná data se pak spotřebované jinými službami Azure v cloudu.

Můžete spustit úlohu transformace datového dvěma způsoby:

 - Použití sady .NET SDK
 - Použít runbook automatizace Azure.
 
 Tento článek podrobně popisují vytvoření konzolové aplikace .NET ukázka zahájit úlohu transformace dat a pak ji sledovat pro dokončení. Další informace o tom, jak zahájit transformaci dat pomocí automatizace, přejděte na [runbook automatizace Azure. použijte k aktivační události úlohy transformace datového](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, zda máte:
*   Počítač se systémem:

    - Visual Studio 2012, 2013, 2015 nebo 2017.

    - Azure Powershell. [Stáhnout prostředí Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Správně nakonfigurována definice úlohy StorSimple Data Manager ve skupině prostředků.
*   Všechny požadované knihovny DLL. Stáhněte si tyto knihovny DLL z [úložiště GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)skript z úložiště Githubu.

## <a name="step-by-step-procedure"></a>Podrobný postup

Proveďte následující kroky spusťte úlohu transformace dat pomocí rozhraní .NET.

1. Chcete-li načíst konfigurační parametry, proveďte následující kroky:
    1. Stažení `Get-ConfigurationParams.ps1` ze skriptu úložiště GitHub v `C:\DataTransformation` umístění.
    1. Spustit `Get-ConfigurationParams.ps1` skript z úložiště Githubu. Zadejte následující příkaz:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Abyste mohli předávat žádné hodnoty pro ActiveDirectoryKey a AppName.

2. Skript vypíše následující hodnoty:
    * ID klienta
    * ID tenanta
    * Klíčů služby Active Directory (stejný jako ten, který je zadaný výše)
    * ID předplatného

        ![Výstup skriptu parametry konfigurace](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Pomocí sady Visual Studio 2012, 2013 nebo 2015, vytvořte konzolovou aplikaci C# .NET.

    1. Spusťte **Visual Studio 2012/2013 nebo 2015**.
    1. Vyberte **soubor > Nový > projekt**.

        ![Vytvoření projektu 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Vyberte **nainstalován > šablony > Visual C# > Konzolová aplikace**.
    3. Zadejte **DataTransformationApp** pro **název**.
    4. Vyberte **C:\DataTransformation** pro **umístění**.
    6. Projekt vytvoříte kliknutím na **OK**.

        ![Vytvoření projektu 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4.  Nyní přidejte všechny knihovny DLL v [složky knihoven DLL](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) jako **odkazy** v projektu, který jste vytvořili. Chcete-li přidat soubory knihoven dll, postupujte takto:

    1. V sadě Visual Studio, přejděte na **zobrazení > Průzkumníku řešení**.
    2. Klikněte na šipku nalevo od projekt aplikace transformaci dat. Klikněte na tlačítko **odkazy** , klikněte pravým tlačítkem na **přidat odkaz na**.
    
        ![Přidání knihovny DLL 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

    3. Přejděte do umístění složky balíčků, vyberte všechny knihovny DLL a klikněte na tlačítko **přidat**a potom klikněte na **OK**.

        ![Přidání knihovny DLL 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Do zdrojového souboru (Program.cs) v projektu přidejte následující příkazy **using**.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Následující kód inicializuje instance úlohy transformace data. Přidejte tuto v **metodu Main**. Nahraďte hodnoty parametrů konfigurace jako dříve získali. Zařadit hodnoty **název skupiny prostředků** a **ResourceName**. **ResourceGroupName** je spojen s StorSimple Data Manager, na kterém byl nakonfigurován definici úlohy. **ResourceName** je název služby StorSimple Data Manager.

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
   
7. Zadejte parametry, pomocí kterých je potřeba spustit definici úlohy

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (OR)

    Pokud chcete změnit parametry definice úlohy během doby běhu, přidejte následující kód:

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

8. Po inicializaci přidejte následující kód k aktivaci úlohy transformace dat v definici úlohy. Zařadit do příslušné **název definice úlohy**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    Jakmile se vloží kód, sestavte řešení. Zde je snímek obrazovky fragmentu kódu k chybě při inicializaci instance úlohy transformace data.

   ![Fragment kódu k chybě při inicializaci úloha transformace dat](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Tato úloha transformace dat, která odpovídá kořenového adresáře a souboru filtrů v rámci svazku zařízení StorSimple a vloží je do zadaného kontejneru nebo sdílené složky. Při transformaci souboru se přidá zprávu do fronty úložiště (ve stejném účtu úložiště jako kontejner nebo sdílené složky) se stejným názvem jako definici úlohy. Tato zpráva slouží jako trigger k zahájení dalšího zpracování souboru.

10. Jakmile úloha byla spuštěna, můžete použít následující kód se sledovat úlohu pro dokončení. Není to povinné přidat tento kód pro spuštění úlohy.

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
 Zde je snímek obrazovky ukázkové celý kód používá k aktivaci úlohy pomocí rozhraní .NET.

 ![Úplné fragment kódu k aktivaci úlohy rozhraní .NET](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Další kroky

[Data Manager zařízení StorSimple pomocí uživatelského rozhraní pro transformaci dat](storsimple-data-manager-ui.md).
