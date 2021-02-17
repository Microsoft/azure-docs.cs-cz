---
title: Odeslání metriky pro klasický Cloud Services do Azure Monitor databáze metrik
description: Popisuje proces odeslání metrik výkonu hostovaného operačního systému pro Azure Classic Cloud Services do úložiště metrik Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 971a3063ff86e2a6b7d1b11f72ff0a257f459da0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609097"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Odeslat metriky hostovaného operačního systému do Azure Monitor klasický Cloud Services úložiště metrik 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pomocí rozšíření Azure Monitor [Diagnostics](../agents/diagnostics-extension-overview.md)můžete shromažďovat metriky a protokoly z hostovaného operačního systému (hostovaného operačního systému) spuštěného jako součást virtuálního počítače, cloudové služby nebo clusteru Service Fabric. Rozšíření může odesílat telemetrii do [mnoha různých umístění.](../platform/data-platform.md?toc=/azure/azure-monitor/toc.json)

Tento článek popisuje proces odeslání metrik výkonu hostovaného operačního systému pro Azure Classic Cloud Services do úložiště metrik Azure Monitor. Počínaje diagnostikou verze 1,11 můžete zapisovat metriky přímo do úložiště metrik Azure Monitor, kde jsou již shromažďovány standardní metriky platforem. 

Ukládání do tohoto umístění vám umožní přístup ke stejným akcím, které můžete použít pro metriky platforem. Akce zahrnují upozorňování na téměř v reálném čase, vytváření grafů, směrování, přístup z REST API a další.  Diagnostické rozšíření v minulosti vytvořilo Azure Storage, ale ne do Azure Monitorho úložiště dat.  

Proces, který je popsaný v tomto článku, funguje jenom pro čítače výkonu v Azure Cloud Services. Nefunguje pro jiné vlastní metriky. 

## <a name="prerequisites"></a>Požadavky

- Musíte být [správcem služeb nebo spolusprávcem](../../cost-management-billing/manage/add-change-subscription-administrator.md) svého předplatného Azure. 

- Vaše předplatné musí být zaregistrované ve službě [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Musíte mít nainstalované buď [Azure PowerShell](/powershell/azure) , nebo [Azure Cloud Shell](../../cloud-shell/overview.md) .

- Vaše cloudová služba musí být v [oblasti, která podporuje vlastní metriky](../platform/metrics-custom-overview.md#supported-regions).

## <a name="provision-a-cloud-service-and-storage-account"></a>Zřízení cloudové služby a účtu úložiště 

1. Vytvořte a nasaďte klasickou cloudovou službu. Ukázkovou klasickou aplikaci Cloud Services a nasazení najdete v části Začínáme [s Azure Cloud Services a ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Můžete použít existující účet úložiště nebo nasadit nový účet úložiště. Je to nejlepší, pokud je účet úložiště ve stejné oblasti jako klasická cloudová služba, kterou jste vytvořili. V Azure Portal otevřete okno prostředek **účty úložiště** a pak vyberte **klíče**. Poznamenejte si název účtu úložiště a klíč účtu úložiště. Tyto informace budete potřebovat v dalších krocích.

   ![Klíče účtu úložiště](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu 

Vytvořte instanční objekt ve vašem Azure Active Directorym tenantovi pomocí pokynů na webu [použití portálu k vytvoření Azure Active Directory aplikace a instančního objektu, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md). Během tohoto procesu mějte na paměti následující: 

- Pro přihlašovací adresu URL můžete umístit libovolnou adresu URL.  
- Vytvoří nový tajný klíč klienta pro tuto aplikaci.  
- Uložte klíč a ID klienta pro použití v pozdějších krocích.  

Poskytněte aplikaci vytvořenou v předchozím kroku *monitorováním oprávnění Vydavatel metrik* pro prostředek, se kterým chcete metriky vygenerovat. Pokud plánujete použití aplikace k vygenerování vlastních metrik pro mnoho prostředků, můžete tato oprávnění udělit na úrovni skupiny prostředků nebo předplatného.  

> [!NOTE]
> Diagnostické rozšíření používá instanční objekt k ověřování proti Azure Monitor a vygeneruje metriky pro cloudovou službu.

## <a name="author-diagnostics-extension-configuration"></a>Vytvořit konfiguraci rozšíření diagnostiky 

Připravte konfigurační soubor s diagnostickým rozšířením. Tento soubor určuje, které protokoly a čítače výkonu má diagnostické rozšíření shromáždit pro vaši cloudovou službu. Následuje ukázkový konfigurační soubor diagnostiky:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

V části "SinksConfig" souboru diagnostiky definujte novou jímku Azure Monitor: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

V části konfiguračního souboru, kde jsou vypsány čítače výkonu, které mají být shromažďovány, přidejte Azure Monitor jímka. Tato položka zajišťuje, aby všechny čítače výkonu, které jste zadali, byly směrovány na Azure Monitor jako metriky. Čítače výkonu můžete přidat nebo odebrat podle vašich potřeb. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Nakonec v části privátní konfigurace přidejte *Azure monitor účet* . Zadejte ID a tajný klíč klienta instančního objektu, který jste vytvořili dříve. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Uložte tento diagnostický soubor místně.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Nasazení diagnostického rozšíření do cloudové služby 

Spusťte PowerShell a přihlaste se k Azure. 

```powershell
Login-AzAccount 
```

Pomocí následujících příkazů uložte podrobnosti účtu úložiště, který jste vytvořili dříve. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Podobně nastavte cestu souboru diagnostiky na proměnnou pomocí následujícího příkazu:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Nasaďte diagnostické rozšíření do cloudové služby pomocí diagnostického souboru s Azure Monitorovou jímkou nakonfigurovanou pomocí následujícího příkazu:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> V rámci instalace diagnostického rozšíření je stále nutné poskytnout účet úložiště. Všechny protokoly nebo čítače výkonu, které jsou zadány v konfiguračním souboru diagnostiky, jsou zapsány do zadaného účtu úložiště.  

## <a name="plot-metrics-in-the-azure-portal"></a>Vykreslení metrik v Azure Portal 

1. Přejděte na Azure Portal. 

   ![Snímek obrazovky zobrazuje Azure Portal s monitorováním, vybrané metriky.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. V nabídce vlevo vyberte **monitor.**

3. V okně **monitor** vyberte kartu **Náhled metrik** .

4. V rozevírací nabídce prostředky vyberte klasickou cloudovou službu.

5. V rozevírací nabídce obory názvů vyberte **Azure. VM. Windows. Host**. 

6. V rozevírací nabídce metriky vyberte svěřené bajtů, které **se používají**. 

K zobrazení celkové paměti, kterou používá konkrétní role nebo instance role, můžete použít funkce filtrování a rozdělování dimenzí. 

 ![Snímek obrazovky zobrazuje data metrik.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [vlastních metrikách](../platform/metrics-custom-overview.md).
