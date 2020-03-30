---
title: Odeslání klasických metrik virtuálních počítačů s Windows do databáze metrik Azure Monitoru
description: Odeslání metrik hostovaného operačního systému do úložiště dat Azure Monitor pro virtuální počítač s Windows (klasický)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 65bb1a3915ece384974da12b4e7a1ad0c1e08133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655803"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Odeslání metrik hostovaného operačního systému do databáze metrik Azure Monitoru pro virtuální počítač s Windows (klasický)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Rozšíření Diagnostika monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) Azure (označované jako "WAD" nebo "Diagnostika") umožňuje shromažďovat metriky a protokoly z hostovaného operačního systému (hostovaný operační systém) spuštěné jako součást virtuálního počítače, cloudové služby nebo clusteru Service Fabric. Rozšíření můžete odeslat telemetrie do [mnoha různých umístění.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Tento článek popisuje proces odesílání metrik výkonu hostovaného operačního systému pro virtuální počítač s Windows (klasický) do databáze metrik Azure Monitor. Počínaje diagnostikou verze 1.11 můžete psát metriky přímo do úložiště metrik Azure Monitoru, kde se už shromažďují metriky standardní platformy. 

Jejich umístění v tomto umístění umožňuje přístup ke stejným akcím jako u metrik platformy. Akce zahrnují téměř v reálném čase upozorňování, vytváření grafů, směrování, přístup z rozhraní REST API a další. V minulosti rozšíření Diagnostika zapisoval o Azure Storage, ale ne do úložiště dat Azure Monitor. 

Proces, který je popsán v tomto článku funguje pouze na klasické virtuální počítače, které jsou spuštěny operační systém Windows.

## <a name="prerequisites"></a>Požadavky

- V rámci předplatného Azure musíte být [správcem služby nebo spolusprávcem.](../../cost-management-billing/manage/add-change-subscription-administrator.md) 

- Vaše předplatné musí být zaregistrováno u [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Musíte mít nainstalovaný [Buď Azure PowerShell](/powershell/azure) nebo [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview)

- Prostředek virtuálního počítače musí být v [oblasti, která podporuje vlastní metriky](metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Vytvoření klasického účtu virtuálního počítače a úložiště

1. Vytvořte klasický virtuální počítač pomocí portálu Azure.
   ![Vytvoření klasického virtuálního virtuálního montovna](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Při vytváření tohoto virtuálního virtuálního účtu zvolte možnost vytvoření nového účtu klasického úložiště. Tento účet úložiště používáme v pozdějších krocích.

1. Na webu Azure Portal přejděte na okno **prostředků prostředků úložiště.** Vyberte **Klíče**a poznamenejte si název účtu úložiště a klíč účtu úložiště. Tyto informace potřebujete v pozdějších krocích.
   ![Přístupové klíče úložiště](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Vytvořte princip služby v tenantovi služby Azure Active Directory pomocí pokynů na [webu Vytvořit instanční objekt](../../active-directory/develop/howto-create-service-principal-portal.md). Všimněte si následujícího procesu: 
- Vytvořte nový tajný klíč klienta pro tuto aplikaci.
- Uložte klíč a ID klienta pro použití v pozdějších krocích.

Udělte této aplikaci oprávnění "Sledování metrik vydavatele" k prostředku, který chcete vyzařovat metriky proti. Můžete použít skupinu prostředků nebo celé předplatné.  

> [!NOTE]
> Rozšíření Diagnostika používá instanční objekt k ověření proti Azure Monitor a vyzařují metriky pro váš klasický virtuální počítač.

## <a name="author-diagnostics-extension-configuration"></a>Konfigurace rozšíření Diagnostika autora

1. Připravte konfigurační soubor rozšíření Diagnostika. Tento soubor určuje, které protokoly a čítače výkonu rozšíření diagnostiky by měla shromažďovat pro klasický virtuální počítač. Tady je příklad:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. V části "SinksConfig" v diagnostickém souboru definujte nový jímka Azure Monitoru takto:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. V části konfiguračního souboru, kde je uveden seznam čítačů výkonu, které mají být shromažďovány, směrovat čítače výkonu do jímky Azure Monitor "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. V privátní konfiguraci definujte účet Azure Monitor. Pak přidejte informace o instančním objektu, které chcete použít k vyzařují metriky.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Uložte tento soubor místně.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Nasazení rozšíření Diagnostika do cloudové služby

1. Spusťte PowerShell a přihlaste se.

    ```powershell
    Login-AzAccount
    ```

1. Začněte nastavením kontextu pro klasický virtuální počítač.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Nastavte kontext klasického účtu úložiště, který byl vytvořen pomocí virtuálního účtu.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Pomocí následujícího příkazu nastavte cestu k souboru Diagnostika na proměnnou:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Připravte aktualizaci pro klasický virtuální počítač pomocí diagnostického souboru, který má nakonfigurované jímky Azure Monitoru.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Nasaďte aktualizaci do virtuálního počítače spuštěním následujícího příkazu:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Je stále povinné poskytnout účet úložiště jako součást instalace rozšíření Diagnostika. Všechny protokoly nebo čítače výkonu zadané v konfiguračním souboru Diagnostika budou zapsány do zadaného účtu úložiště.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Vykreslení metrik na webu Azure Portal

1.  Přejděte na web Azure Portal. 

1.  V levé nabídce vyberte **Sledovat.**

1.  V okně **Monitor** vyberte **metriky**.

    ![Navigace v metrikách](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. V rozevírací nabídce prostředků vyberte klasický virtuální počítač.

1. V rozevírací nabídce Obory názvů vyberte **azure.vm.windows.guest**.

1. V rozevírací nabídce metriky vyberte **možnost Paměť\Počet potvrzených bajtů v použití**.
   ![Metriky vykreslení](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastních metrikách](metrics-custom-overview.md).

