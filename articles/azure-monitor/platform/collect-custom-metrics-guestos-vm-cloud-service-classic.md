---
title: Odesílání klasických metrik cloudových služeb do databáze metrik Azure Monitoru
description: Popisuje proces odesílání metrik výkonu hostovaného operačního systému pro klasické cloudové služby Azure do úložiště metrik Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3b390ffa20cf3cf79b8fb6311ad05b2978bd5d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655785"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Odeslání metrik hostovaného operačního systému do klasického cloudového úložiště metrik Azure Monitoru 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pomocí rozšíření [Diagnostika monitorování](diagnostics-extension-overview.md)Azure můžete shromažďovat metriky a protokoly z hostovaného operačního systému (hostovaný operační systém) spuštěné jako součást virtuálního počítače, cloudové služby nebo clusteru Service Fabric. Rozšíření můžete odeslat telemetrie do [mnoha různých umístění.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Tento článek popisuje proces odesílání metrik výkonu hostovaného operačního systému pro klasické cloudové služby Azure do úložiště metrik Azure Monitor. Počínaje diagnostikou verze 1.11 můžete psát metriky přímo do úložiště metrik Azure Monitoru, kde se už shromažďují metriky standardní platformy. 

Jejich umístění v tomto umístění umožňuje přístup ke stejným akcím, které můžete pro metriky platformy. Akce zahrnují téměř v reálném čase upozorňování, vytváření grafů, směrování, přístup z rozhraní REST API a další.  V minulosti rozšíření Diagnostika zapisoval o Azure Storage, ale ne do úložiště dat Azure Monitor.  

Proces, který je popsáno v tomto článku funguje pouze pro čítače výkonu v Cloud Services Azure. Nefunguje pro jiné vlastní metriky. 

## <a name="prerequisites"></a>Požadavky

- V rámci předplatného Azure musíte být [správcem služby nebo spolusprávcem.](../../cost-management-billing/manage/add-change-subscription-administrator.md) 

- Vaše předplatné musí být zaregistrováno u [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Musíte mít nainstalovaný [Buď Azure PowerShell](/powershell/azure) nebo [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview)

- Cloudová služba musí být v [oblasti, která podporuje vlastní metriky](metrics-custom-overview.md#supported-regions).

## <a name="provision-a-cloud-service-and-storage-account"></a>Zřízení účtu cloudové služby a úložiště 

1. Vytvořte a nasaďte klasickou cloudovou službu. Ukázkovou klasickou aplikaci a nasazení cloudových služeb najdete na [webu Začínáme s Cloudovými službami Azure a ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Můžete použít existující účet úložiště nebo nasadit nový účet úložiště. Je to nejlepší, pokud je účet úložiště ve stejné oblasti jako klasická cloudová služba, kterou jste vytvořili. Na webu Azure Portal přejděte na okno **prostředků prostředků úložiště** a vyberte **Klíče**. Poznamenejte si název účtu úložiště a klíč účtu úložiště. Tyto informace budete potřebovat v pozdějších krocích.

   ![Klíče účtu úložiště](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu 

Vytvořte princip služby v tenantovi Služby Azure Active Directory pomocí pokynů na [webu Use Portal k vytvoření aplikace a instancí Azure Active Directory, které mají přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Všimněte si následujícího postupu při procházení tímto procesem: 

- Můžete vložit libovolnou adresu URL přihlašovací adresy URL.  
- Vytvořte nový tajný klíč klienta pro tuto aplikaci.  
- Uložte klíč a ID klienta pro použití v pozdějších krocích.  

Udělte aplikaci vytvořené v předchozím kroku oprávnění *vydavatele metrik monitorování* k prostředku, proti jehož vyzařování metrik. Pokud máte v plánu použít aplikaci k vyzařování vlastní metriky proti mnoha prostředkům, můžete udělit tato oprávnění na úrovni skupiny prostředků nebo předplatného.  

> [!NOTE]
> Rozšíření Diagnostika používá instanční objekt k ověření proti Azure Monitor a vyzařují metriky pro cloudovou službu.

## <a name="author-diagnostics-extension-configuration"></a>Konfigurace rozšíření Diagnostika autora 

Připravte konfigurační soubor rozšíření Diagnostika. Tento soubor určuje, které protokoly a čítače výkonu rozšíření Diagnostika by měla shromažďovat pro cloudovou službu. Následuje ukázkový konfigurační soubor diagnostiky:  

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

V části "SinksConfig" v diagnostickém souboru definujte nový jímka Azure Monitoru: 

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

V části konfiguračního souboru, kde zobrazíte seznam čítačů výkonu, které chcete shromažďovat, přidejte jímky Azure Monitoru. Tato položka zajišťuje, že všechny čítače výkonu, které jste zadali jsou směrovány do Azure Monitoru jako metriky. Čítače výkonu můžete přidávat nebo odebírat podle svých potřeb. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Nakonec v privátní konfiguraci přidejte část *účtu Azure Monitor.* Zadejte ID klienta instančního objektu a tajný klíč, který jste vytvořili dříve. 

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Nasazení rozšíření Diagnostika do cloudové služby 

Spusťte PowerShell a přihlaste se do Azure. 

```powershell
Login-AzAccount 
```

Pomocí následujících příkazů můžete uložit podrobnosti o účtu úložiště, který jste vytvořili dříve. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Podobně nastavte cestu k souboru diagnostiky na proměnnou pomocí následujícího příkazu:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Nasazení rozšíření Diagnostika do cloudové služby pomocí diagnostického souboru s jímkou Azure Monitor nakonfigurované pomocí následujícího příkazu:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Je stále povinné poskytnout účet úložiště jako součást instalace rozšíření Diagnostika. Všechny protokoly nebo čítače výkonu, které jsou zadány v diagnostickém konfiguračním souboru, jsou zapsány do zadaného účtu úložiště.  

## <a name="plot-metrics-in-the-azure-portal"></a>Vykreslení metrik na webu Azure Portal 

1. Přejděte na web Azure Portal. 

   ![Metriky Portál Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. V levé nabídce vyberte **Sledovat.**

3. V okně **Monitor** vyberte kartu **Náhled metrik.**

4. V rozevírací nabídce zdrojů vyberte klasickou cloudovou službu.

5. V rozevírací nabídce Obory názvů vyberte **azure.vm.windows.guest**. 

6. V rozevírací nabídce metriky vyberte **možnost Paměť\Počet potvrzených bajtů v použití**. 

Možnosti filtrování a rozdělení dimenzí slouží k zobrazení celkové paměti, která se používá v konkrétní roli nebo instanci role. 

 ![Metriky Portál Azure](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [vlastních metrikách](metrics-custom-overview.md).

