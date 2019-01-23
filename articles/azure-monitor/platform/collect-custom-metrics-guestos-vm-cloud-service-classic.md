---
title: Odeslání metrik hostovaného operačního systému do Azure monitoru metriky uložit klasické cloudové služby
description: Odeslání metrik hostovaného operačního systému do Azure monitoru metriky ukládání cloudových služeb
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e971414cfcd069a4aa8a212f64927407be83ad39
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470932"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Odeslání metrik hostovaného operačního systému do Azure monitoru metriky uložit klasické cloudové služby 

Díky nástroji Azure Monitor [diagnostické rozšíření](diagnostics-extension-overview.md), můžete shromažďovat metriky a protokoly z hostovaný operační systém (Guest OS) spuštěná jako virtuální počítač, cloudovou službu nebo clusteru Service Fabric. Rozšíření mohla odesílat telemetrii na [různými umístěními.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Tento článek popisuje postup pro odesílání metriky výkonu hostovaného operačního systému pro Azure classic cloudové služby k úložišti metrik Azure monitoru. Spouští se s diagnostikou verze 1.11, můžete napsat metriky přímo do Azure monitoru ukládat metriky, kde již shromažďuje metriky na standard platformy. 

Ukládání v tomto umístění umožňuje přístup k stejné akce, které můžou pro metriky platformy. Akce zahrnují téměř v reálném čase výstrahy, grafů, směrování, přístup z rozhraní REST API a další.  Diagnostické rozšíření v minulosti zapsala do služby Azure Storage, ale ne k úložišti dat monitorování Azure.  

Proces, který je popsaný v tomto článku platí jenom pro čítače výkonu v cloudových službách Azure. Nebude fungovat pro další vlastní metriky. 

## <a name="prerequisites"></a>Požadavky

- Musí být [Správce služeb nebo spolupracující správce](~/articles/billing/billing-add-change-azure-subscription-administrator.md) v rámci předplatného Azure. 

- Předplatné musí být zaregistrovaná s [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal). 

- Musíte mít buď [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) nebo [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) nainstalované.

## <a name="provision-a-cloud-service-and-storage-account"></a>Zřizování cloudové služby a úložiště účtu 

1. Vytvoření a nasazení cloudovou službou modelu classic. Ukázka klasické aplikace Cloud Services a nasazení najdete v [Začínáme s Azure Cloud Services a ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Můžete použít existující účet úložiště nebo nasadit nový účet úložiště. Je vhodné, pokud je účet úložiště ve stejné oblasti jako klasický Cloudová služba, kterou jste vytvořili. Na webu Azure Portal, přejděte **účty úložiště** okno prostředku a pak vyberte **klíče**. Poznamenejte si název účtu úložiště a klíč účtu úložiště. Tyto informace v následujících krocích budete potřebovat.

   ![Klíče účtu úložiště](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu 

Vytvořit instanční objekt ve vašem tenantovi Azure Active Directory pomocí pokynů na adrese [použití portálu k vytvoření aplikace Azure Active Directory a instančního objektu, který má přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Když budete na tomto procesu, mějte na paměti následující: 

- Můžete vložit libovolnou adresu URL pro adresu URL přihlášení.  
- Vytvořte nový tajný kód klienta pro tuto aplikaci.  
- Uložte klíč a ID klienta pro použití v dalších krocích.  

Dejte aplikaci vytvořenou v předchozím kroku *monitorování metrik vydavatele* oprávnění ke generování metrik pro požadovaný prostředek. Pokud máte v plánu používat aplikace a vygenerovat vlastní metriky s mnoha prostředky, můžete udělit tato oprávnění na úrovni skupiny nebo předplatného resource.  

> [!NOTE]
> Diagnostické rozšíření používá instanční objekt k ověřování na základě Azure Monitor a generovat metriky pro cloudovou službu.

## <a name="author-diagnostics-extension-configuration"></a>Konfigurace diagnostického rozšíření autora 

Příprava vašich diagnostického rozšíření konfiguračního souboru. Tento soubor Určuje, které protokoly a čítače výkonu diagnostické rozšíření mají shromažďovat pro cloudovou službu. Následuje ukázkový soubor konfigurace diagnostiky:  

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

V části "SinksConfig" souboru diagnostiky definujte nové jímka Azure monitoru: 

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

V oddílu konfiguračního souboru, ve kterém můžete seznam čítačů výkonu k získání přidejte jímka Azure monitoru. Tato položka se zajistí, že všechny čítače výkonu, které jste zadali jsou směrovány do Azure monitoru jako metriky. Můžete přidat nebo odebrat čítače výkonu podle vašich potřeb. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Nakonec v privátní konfigurace, přidejte *účet služby Azure Monitor* oddílu. Zadejte ID klienta instančního objektu služby a tajný klíč, který jste vytvořili dříve. 

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

Uložte tento soubor diagnostiky místně.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Nasazení rozšíření diagnostiky ke cloudové službě 

Spusťte PowerShell a připojte se k Azure. 

```PowerShell
Login-AzureRmAccount 
```

Pomocí následujících příkazů ukládat podrobnosti o účtu úložiště, který jste vytvořili dříve. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Podobně nastavte diagnostiky cestu k souboru na proměnnou pomocí následujícího příkazu:

```PowerShell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Nasazení rozšíření diagnostiky do cloudové služby pomocí diagnostiky souboru s jímka Azure monitoru, který je nakonfigurovaný pomocí následujícího příkazu:  

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Je stále povinné a použijte účet úložiště jako součást instalace diagnostického rozšíření. Všechny protokoly nebo čítače výkonu, které jsou uvedeny v souboru konfigurace diagnostiky se zapisují do zadaný účet úložiště.  

## <a name="plot-metrics-in-the-azure-portal"></a>Vykreslení metrik na webu Azure Portal 

1. Přejděte na web Azure Portal. 

   ![Metriky Azure portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. V nabídce vlevo vyberte **monitorování.**

3. Na **monitorování** okno, vyberte **metrik ve verzi Preview** kartu.

4. V rozevírací nabídce prostředků vyberte vaši cloudovou službou modelu classic.

5. V rozevírací nabídce obory názvů, vyberte **azure.vm.windows.guest**. 

6. V rozevírací nabídce metrik vyberte **mezi hodnotami paměť\svěřené bajty**. 

Pomocí dimenzí, filtrování a rozdělování možnosti zobrazíte celkové paměti, který používá konkrétní role nebo role instance. 

 ![Metriky Azure portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Další postup

- Další informace o [vlastní metriky](metrics-custom-overview.md).

