---
title: Odeslání metrik hostovaného operačního systému k úložišti dat monitorování Azure pro Windows virtuální počítač (klasický)
description: Odeslání metrik hostovaného operačního systému k úložišti dat monitorování Azure pro Windows virtuální počítač (klasický)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: 9f13e0783a53b096da47f3afe3f830d24cc281a4
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53325991"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Odeslání metrik hostovaného operačního systému k úložišti dat monitorování Azure pro Windows virtuální počítač (klasický)

Azure Monitor [diagnostické rozšíření](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (označují se termínem "WAD" nebo "Diagnostika") umožňuje shromažďovat metriky a protokoly z hostovaný operační systém (Guest OS) spuštěná jako virtuální počítač, cloudovou službu nebo Service Fabric cluster. Rozšíření mohla odesílat telemetrii na [různými umístěními.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Tento článek popisuje postup pro odesílání metriky výkonu hostovaného operačního systému pro virtuální počítače s Windows (classic) do úložiště metrik Azure monitoru. Spouští se s diagnostikou verze 1.11, můžete napsat metriky přímo do Azure monitoru ukládat metriky, kde již shromažďuje metriky na standard platformy. 

Ukládání v tomto umístění umožňuje přístup ke stejné akce jako u platformy metriky. Akce zahrnují téměř v reálném čase výstrahy, grafů, směrování, přístup z rozhraní REST API a další. Diagnostické rozšíření v minulosti zapsala do služby Azure Storage, ale ne k úložišti dat monitorování Azure. 

Proces, který je popsaný v tomto článku funguje pouze v klasických virtuálních počítačů, na kterých běží operační systém Windows.

## <a name="prerequisites"></a>Požadavky

- Musí být [Správce služeb nebo spolupracující správce](../../billing/billing-add-change-azure-subscription-administrator.md) v rámci předplatného Azure. 

- Předplatné musí být zaregistrovaná s [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal). 

- Musíte mít buď [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) nebo [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) nainstalované.

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Vytvoření klasického virtuálního počítače a účtu úložiště

1. Vytvoření klasického virtuálního počítače pomocí webu Azure portal.
   ![Vytvoření klasického virtuálního počítače](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Při vytváření tohoto virtuálního počítače, zvolte možnost pro vytvoření nového účtu klasického úložiště. V dalších krocích používáme tento účet úložiště.

1. Na webu Azure Portal, přejděte **účty úložiště** okno prostředků. Vyberte **klíče**a poznamenejte si název účtu úložiště a klíč účtu úložiště. Tyto informace v následujících krocích budete potřebovat.
   ![Přístupové klíče k úložišti](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Vytvořit instanční objekt ve vašem tenantovi Azure Active Directory pomocí pokynů na adrese [vytvoření instančního objektu](../../active-directory/develop/howto-create-service-principal-portal.md). Mějte na paměti následující při procházení tohoto procesu: 
- Vytvořte nový tajný kód klienta pro tuto aplikaci.
- Uložte klíč a ID klienta pro použití v dalších krocích.

Udělte oprávnění ke "Monitorování metrik vydavatele" k prostředku, kterou chcete vygenerovat metrik pro tuto aplikaci. Můžete použít skupinu prostředků nebo celé předplatné.  

> [!NOTE]
> Diagnostické rozšíření používá instanční objekt k ověřování na základě Azure Monitor a generovat metriky pro klasický virtuální počítač.

## <a name="author-diagnostics-extension-configuration"></a>Konfigurace diagnostického rozšíření autora

1. Příprava vašich diagnostického rozšíření konfiguračního souboru. Tento soubor Určuje, které protokoly a čítače výkonu diagnostické rozšíření mají shromažďovat pro klasický virtuální počítač. Tady je příklad:

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
1. V části "SinksConfig" souboru diagnostiky definujte nové jímka Azure monitoru, následujícím způsobem:

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

1. V oddílu konfiguračního souboru, kde je uveden seznam čítačů výkonu, které se mají shromažďovat směrujte jímka Azure monitoru "AzMonSink" čítače výkonu.

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. V privátní konfigurace definujte účtu Azure Monitor. Pak přidejte informací o instančním objektu služby používat k emitování metriky.

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

1. Uložte soubor místně.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Nasazení rozšíření diagnostiky ke cloudové službě

1. Spusťte PowerShell a přihlaste se.

    ```powershell
    Login-AzureRmAccount
    ```

1. Začněte tím, že nastavíte kontext pro klasický virtuální počítač.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Nastavte kontext účtu klasického úložiště, který byl vytvořen s virtuálním Počítačem.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Nastavte cestu k souboru diagnostiky na proměnnou pomocí následujícího příkazu:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Příprava aktualizace pro klasický virtuální počítač se souborem diagnostiku, která má nakonfigurované jímka Azure monitoru.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Aktualizace nasaďte do svého virtuálního počítače spuštěním následujícího příkazu:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Je stále povinné a použijte účet úložiště jako součást instalace diagnostického rozšíření. Všechny protokoly nebo čítače výkonu, které jsou uvedeny v souboru konfigurace diagnostiky se zapíšou do zadaný účet úložiště.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Vykreslení metrik na webu Azure Portal

1.  Přejděte na web Azure Portal. 

1.  V nabídce vlevo vyberte **monitorování.**

1.  Na **monitorování** okně vyberte **metriky**.

    ![Přejděte metriky](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. V rozevírací nabídce prostředků vyberte Klasický virtuální počítač.

1. V rozevírací nabídce obory názvů, vyberte **azure.vm.windows.guest**.

1. V rozevírací nabídce metrik vyberte **mezi hodnotami paměť\svěřené bajty**.
   ![Zobrazit metriky](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Další postup
- Další informace o [vlastní metriky](metrics-custom-overview.md).
