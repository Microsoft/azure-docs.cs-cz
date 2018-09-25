---
title: Odeslání metrik operačního systému hosta metriky Azure Monitor uložit klasické cloudové služby
description: Odeslání metrik operačního systému hosta metriky Azure Monitor uložit klasické cloudové služby
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986910"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>Odeslání metrik operačního systému hosta metriky Azure Monitor uložit klasické cloudové služby

Azure Monitor [rozšíření Windows Azure Diagnostics](azure-diagnostics.md) (WAD) vám umožní shromažďovat metriky a protokoly z hostovaného operačního systému (guestOS) spuštěna jako součást clusteru virtuální počítač, Cloudovou službu nebo Service Fabric.  Rozšíření mohla odesílat telemetrii do mnoha různých umístěních uvedených v dříve odkazovaném článku.  

Tento článek popisuje proces na metriky výkonu operačního systému hosta odeslání pro Azure classic cloudové služby, které metrik úložiště Azure Monitor. Spouští se s využitím WAD verze 1.11, můžete napsat metriky přímo k úložišti Azure Monitor metrik, kde již shromažďuje metriky na standard platformy. Ukládání v tomto umístění umožňuje přístup ke stejné akce k dispozici pro platformy metriky.  Akce zahrnují téměř v reálném čase výstrahy, grafů, směrování, přístup k rozhraní REST API a další.  V minulosti rozšíření WAD zapsáno do služby Azure Storage, ale ne úložiště dat Azure Monitor.  

Proces popsaný v tomto článku funguje jenom pro čítače výkonu v Azure Cloud Services. Nefunguje pro další vlastní metriky. 
   

## <a name="pre-requisites"></a>Požadavky

- Musí být [Správce služeb nebo spolupracující správce](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) v rámci předplatného Azure 

- Předplatné musí být zaregistrovaná s [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Musíte mít buď [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) nainstalovaná, nebo můžete použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="provision-cloud-service-and-storage-account"></a>Zřizování cloudové služby a účet úložiště 

1. Vytvořit a nasadit Cloudovou službu Classic (Ukázková aplikace služby classic cloud a nasazení najdete [tady](../cloud-services/cloud-services-dotnet-get-started.md). 

2. Můžete použít existující účet úložiště nebo nasadit nový účet úložiště. Je vhodné, pokud je účet úložiště ve stejné oblasti jako klasický cloudové služby, které jste právě vytvořili. Na webu Azure Portal, přejděte do okna účet úložiště prostředků a zvolte **klíče**. Poznamenejte si klíč účtu úložiště a jeho název účtu, budete potřebovat v dalších krocích.

   ![Klíče účtu úložiště](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>Vytvoření instančního objektu 

Vytvořit instanční objekt ve vašem tenantovi Azure Active Directory pomocí pokynů uvedených v... / azure/azure-resource-manager/resource-group-create-service-principal-portal. Mějte na paměti následující při procházení tohoto procesu: 
  - Můžete vložit libovolnou adresu URL pro adresu URL přihlašování.  
  - Vytvořit nový tajný kód klienta pro tuto aplikaci  
  - Uložte klíč a id klienta pro použití v dalších krocích.  

Dejte aplikaci vytvořenou v předchozím kroku *monitorování metrik vydavatele* oprávnění chcete generovat metrik pro prostředek. Pokud máte v plánu používat aplikace a vygenerovat vlastní metriky s mnoha prostředky, můžete udělit tato oprávnění na úrovni skupiny nebo předplatného resource.  

> [!NOTE]
> Rozšíření diagnostiky instanční objekt služby používá k ověřování na základě Azure Monitor a generovat metriky pro cloudové služby 

## <a name="author-diagnostics-extension-configuration"></a>Konfigurace diagnostického rozšíření autora 

Příprava WAD diagnostického rozšíření konfiguračního souboru. Tento soubor Určuje, které protokoly a čítače výkonu diagnostické rozšíření mají shromažďovat pro cloudovou službu. Následuje ukázkový soubor konfigurace diagnostiky.  

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

V oddílu konfiguračního souboru, kde seznam výkonu čítače, které se mají shromažďovat přidejte jímka Azure monitoru. Tato položka zajišťuje všechny čítače výkonu zadaný jsou směrovány do Azure monitoru jako metriky. Můžete přidat nebo odebrat čítače výkonu podle vašich potřeb. 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
Nakonec v privátní konfigurace, přidejte *účet služby Azure Monitor* oddílu. Zadejte ID klienta instančního objektu služby a tajný klíč, které byly vytvořeny v předchozím kroku. 

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

Spusťte PowerShell a přihlášení do Azure 

```PowerShell
Login-AzureRmAccount 
```

Store podrobné informace o podrobnosti účet úložiště vytvořený v předchozím kroku do proměnných pomocí následujících příkazů. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
Podobně, nastavit cestu k souboru diagnostiky do proměnných pomocí následující příkaz. 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
Nasazení rozšíření diagnostiky do cloudové služby pomocí diagnostiky souboru nakonfigurovat pomocí následujícího příkazu jímka Azure monitoru 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> Je stále povinné a použijte účet úložiště jako součást instalace diagnostického rozšíření. Protokoly a/nebo čítače výkonu zadaný v konfigurační soubor diagnostiky se zapíšou do zadaný účet úložiště.  

## <a name="plot-metrics-in-the-azure-portal"></a>Vykreslení metrik na webu Azure Portal 

Přejděte na web Azure Portal 

 ![Metriky Azure portal](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. V nabídce vlevo klikněte na monitorování 

1. V okně monitorování klikněte na kartu metrik ve verzi Preview 

1. V prostředku rozevíracího seznamu vyberte cloudové služby Classic 

1. V oborech názvů rozevíracího seznamu vyberte **azure.vm.windows.guest** 

1. Metriky rozevírací seznam, zaškrtněte *mezi hodnotami paměť\svěřené bajty* 

Můžete zobrazit celkové paměti používané podle konkrétní roli a všechny instance rolí pomocí filtrování dimenze a rozdělení funkcí. 

 ![Metriky Azure portal](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>Další postup
- Další informace o [vlastní metriky](metrics-custom-overview.md).



