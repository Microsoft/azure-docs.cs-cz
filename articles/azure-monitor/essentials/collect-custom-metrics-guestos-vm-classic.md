---
title: Odeslání metriky pro klasický virtuální počítač s Windows do Azure Monitor databáze metrik
description: Odeslání metriky hostovaného operačního systému do úložiště dat Azure Monitor pro virtuální počítač s Windows (Classic)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: f0c97d128837e77325e68ae28161a98938510ff8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723278"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Odeslání metriky hostovaného operačního systému do databáze Azure Monitor metriky pro virtuální počítač s Windows (Classic)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Rozšíření diagnostika](../agents/diagnostics-extension-overview.md) Azure monitor (označované jako "wad" nebo "Diagnostika") umožňuje shromažďovat metriky a protokoly ze hostovaného operačního systému (hostovaného operačního systému) spuštěného jako součást virtuálního počítače, cloudové služby nebo clusteru Service Fabric. Rozšíření může odesílat telemetrii do [mnoha různých umístění.](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Tento článek popisuje proces odeslání metrik výkonu hostovaného operačního systému pro virtuální počítač s Windows (Classic) do databáze metriky Azure Monitor. Počínaje diagnostikou verze 1,11 můžete zapisovat metriky přímo do úložiště metrik Azure Monitor, kde jsou již shromažďovány standardní metriky platforem. 

Ukládání do tohoto umístění vám umožní přístup ke stejným akcím, jako u metrik platforem. Akce zahrnují upozorňování na téměř v reálném čase, vytváření grafů, směrování, přístup z REST API a další. Diagnostické rozšíření v minulosti vytvořilo Azure Storage, ale ne do Azure Monitorho úložiště dat. 

Proces, který je popsaný v tomto článku, funguje jenom na klasických virtuálních počítačích, na kterých běží operační systém Windows.

## <a name="prerequisites"></a>Požadavky

- Musíte být [správcem služeb nebo spolusprávcem](../../cost-management-billing/manage/add-change-subscription-administrator.md) svého předplatného Azure. 

- Vaše předplatné musí být zaregistrované ve službě [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Musíte mít nainstalované buď [Azure PowerShell](/powershell/azure) , nebo [Azure Cloud Shell](../../cloud-shell/overview.md) .

- Prostředek virtuálního počítače musí být v [oblasti, která podporuje vlastní metriky](./metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Vytvoření klasického virtuálního počítače a účtu úložiště

1. Pomocí Azure Portal vytvořte klasický virtuální počítač.
   ![Vytvoření klasického virtuálního počítače](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Když vytváříte tento virtuální počítač, vyberte možnost pro vytvoření nového účtu klasického úložiště. Tento účet úložiště používáme v pozdějších krocích.

1. V Azure Portal otevřete okno prostředek **účty úložiště** . Vyberte **klíče** a poznamenejte si název účtu úložiště a klíč účtu úložiště. Tyto informace budete potřebovat v pozdějších krocích.
   ![Přístupové klíče k úložišti](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Pomocí pokynů v tématu [Vytvoření instančního objektu](../../active-directory/develop/howto-create-service-principal-portal.md)vytvořte instanční objekt ve svém tenantovi Azure Active Directory. Při procházení tohoto procesu Pamatujte na následující: 
- Vytvoří nový tajný klíč klienta pro tuto aplikaci.
- Uložte klíč a ID klienta pro použití v pozdějších krocích.

Udělte této aplikaci oprávnění vydavatele metrik monitorování pro prostředek, se kterým chcete metriky vygenerovat. Můžete použít skupinu prostředků nebo celé předplatné.  

> [!NOTE]
> Diagnostické rozšíření používá instanční objekt k ověřování proti Azure Monitor a generuje metriky pro klasický virtuální počítač.

## <a name="author-diagnostics-extension-configuration"></a>Vytvořit konfiguraci rozšíření diagnostiky

1. Připravte konfigurační soubor s diagnostickým rozšířením. Tento soubor určuje, které protokoly a čítače výkonu má diagnostické rozšíření shromažďovat pro váš klasický virtuální počítač. Tady je příklad:

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
1. V části "SinksConfig" souboru diagnostiky definujte novou Azure Monitor jímka, a to následujícím způsobem:

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

1. V části konfiguračního souboru, kde je uveden seznam čítačů výkonu, které mají být shromážděny, směrují čítače výkonu do Azure Monitor jímky "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. V části soukromá konfigurace Definujte účet Azure Monitor. Pak přidejte informace o instančních objektech, které se použijí k vygenerování metrik.

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Nasazení diagnostického rozšíření do cloudové služby

1. Spusťte PowerShell a přihlaste se.

    ```powershell
    Login-AzAccount
    ```

1. Začněte nastavením kontextu pro klasický virtuální počítač.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Nastavte kontext klasického účtu úložiště, který se vytvořil pomocí virtuálního počítače.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Nastavte cestu souboru diagnostiky na proměnnou pomocí následujícího příkazu:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Připravte aktualizaci pro klasický virtuální počítač s diagnostickým souborem, který má nakonfigurovanou jímku Azure Monitor.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Nasaďte aktualizaci na svůj virtuální počítač spuštěním následujícího příkazu:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> V rámci instalace diagnostického rozšíření je stále nutné poskytnout účet úložiště. Všechny protokoly nebo čítače výkonu, které jsou zadány v konfiguračním souboru diagnostiky, budou zapsány do zadaného účtu úložiště.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Vykreslit metriky v Azure Portal

1.  Přejděte na Azure Portal. 

1.  V nabídce vlevo vyberte **monitor.**

1.  V okně **monitor** vyberte **metriky**.

    ![Přejít na metriky](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. V rozevírací nabídce prostředky vyberte klasický virtuální počítač.

1. V rozevírací nabídce obory názvů vyberte **Azure. VM. Windows. Host**.

1. V rozevírací nabídce metriky vyberte svěřené bajtů, které **se používají**.
   ![Metriky grafu](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [vlastních metrikách](./metrics-custom-overview.md).