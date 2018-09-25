---
title: Odeslat hostovaného operačního systému metriky do úložiště dat monitorování Azure pro Windows virtuální počítač (classic)
description: Odeslat hostovaného operačního systému metriky do úložiště dat monitorování Azure pro Windows virtuální počítač (classic)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: cb803450f7765ae62292ff3afb7f32209b437f78
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978912"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Odeslat hostovaného operačního systému metriky do úložiště dat monitorování Azure pro Windows virtuální počítač (classic)

Azure Monitor [rozšíření Windows Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (WAD) umožňuje shromažďovat metriky a protokoly spuštění hostovaný operační systém (guest OS) jako součást clusteru virtuální počítač, Cloudovou službu nebo Service Fabric. Rozšíření mohla odesílat telemetrii do mnoha různých umístěních uvedených v dříve odkazovaném článku.

Tento článek popisuje postup na metriky výkonu operačního systému hosta odeslání pro Windows virtuální počítač (klasický) do úložiště metrik Azure monitoru. Spouští se s využitím WAD verze 1.11, můžete napsat metriky přímo k úložišti Azure Monitor metrik, kde již shromažďuje metriky na standard platformy. Ukládání v tomto umístění umožňuje přístup ke stejné akce k dispozici pro platformy metriky.  Akce zahrnují téměř v reálném čase výstrahy, grafů, směrování, přístup k rozhraní REST API a další.  V minulosti rozšíření WAD zapsáno do služby Azure Storage, ale ne úložiště dat Azure Monitor. 

Proces popsaný v tomto článku funguje jenom klasické virtuální počítače s operačním systémem Windows.

## <a name="pre-requisites"></a>Požadavky

- Musí být [Správce služeb nebo spolupracující správce](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) v rámci předplatného Azure 

- Předplatné musí být zaregistrovaná s [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Musíte mít buď [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) nainstalovaná, nebo můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Vytvoření klasického virtuálního počítače a účtu úložiště

1. Vytvoření klasického virtuálního počítače pomocí webu Azure portal ![vytvořit virtuální počítač nesprávně](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png)

1. Při vytváření tohoto virtuálního počítače, můžete vytvořit nový účet klasického úložiště. V dalších krocích používáme tento účet úložiště.

1. Na webu Azure Portal, přejděte do okna účet úložiště prostředků a zvolte **klíče** a poznamenejte si název účtu úložiště a klíč účtu úložiště. Je třeba tyto klíče v dalších krocích ![přístupových klíčů k úložišti](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Vytvořit instanční objekt ve vašem tenantovi Azure Active Directory pomocí pokynů uvedených v [vytvoření instančního objektu](../azure-resource-manager/resource-group-create-service-principal-portal.md). Mějte na paměti následující při procházení tohoto procesu: 
- Vytvořit nový tajný kód klienta pro tuto aplikaci  
- Uložte klíč a id klienta pro použití v dalších krocích.

Udělte oprávnění ke "Monitorování metrik vydavatele" k prostředku, kterou chcete vygenerovat metrik pro tuto aplikaci. Můžete použít skupinu prostředků nebo celé předplatné.  

> [!NOTE]
> Rozšíření diagnostiky pomocí instančního objektu ověřování na základě Azure Monitor a generovat metriky pro klasický virtuální počítač.

## <a name="author-diagnostics-extension-configuration"></a>Konfigurace diagnostického rozšíření autora

1. Příprava WAD diagnostického rozšíření konfiguračního souboru. Tento soubor Určuje, které protokoly a čítače výkonu pro klasický virtuální počítač mají shromažďovat diagnostické rozšíření. Zde je příklad.

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
1. V části "SinksConfig" souboru diagnostiky definujte nové jímka Azure monitoru:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide your Classic VM’s Resource ID </ResourceId>
                <Region>Region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. V oddílu konfiguračního souboru, kde je uveden seznam čítačů výkonu, které se mají shromažďovat směrujte čítačů výkonu Azure Monitor jímky "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. V privátní konfigurace definování účtu Azure Monitor a přidání informací o instančním objektu služby používat k emitování metriky.

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

## <a name="deploy-diagnostics-extension-to-your-cloud-service"></a>Nasazení rozšíření diagnostiky ke cloudové službě

1. Spusťte PowerShell a přihlaste se

    ```powershell
    Login-AzureRmAccount
    ```

1. Začněte tím, že nastavení kontextu na klasickém virtuálním počítači

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Nastavte kontext účtu klasického úložiště, který byl vytvořen s virtuálním Počítačem.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Nastavit cestu k souboru diagnostiky do proměnných pomocí následující příkaz.

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Připravit aktualizace klasického virtuálního počítače pomocí souboru diagnostiky s nakonfigurované jímka Azure monitoru

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Spuštěním následujícího příkazu Nasaďte aktualizaci k vašemu virtuálnímu počítači

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Je stále povinné a použijte účet úložiště jako součást instalace diagnostického rozšíření. Protokoly a/nebo čítače výkonu zadaný v konfigurační soubor diagnostiky se zapíšou do zadaný účet úložiště.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Vykreslení metrik na webu Azure Portal

1.  Přejděte na web Azure Portal

1.  V nabídce vlevo klikněte na monitorování

1.  V okně monitorování klikněte na **metriky**
   ![přejděte metriky](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png)

1. V rozevíracím seznamu prostředků vyberte Klasický virtuální počítač

1. V oborech názvů rozevíracího seznamu vyberte **azure.vm.windows.guest**

1. Metriky rozevírací seznam, zaškrtněte **mezi hodnotami paměť\svěřené bajty**
   ![vykreslení metrik](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png)


## <a name="next-steps"></a>Další postup
- Další informace o [vlastní metriky](metrics-custom-overview.md).
