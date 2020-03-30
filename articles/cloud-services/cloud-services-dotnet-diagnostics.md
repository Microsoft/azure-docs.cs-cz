---
title: Jak používat diagnostiku Azure (.NET) s cloudovými službami | Dokumenty společnosti Microsoft
description: Pomocí diagnostiky Azure shromažďujte data z cloudových služeb Azure pro ladění, měření výkonu, monitorování, analýzu provozu a další.
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: tagore
ms.openlocfilehash: 1e49a0935a70a2470267e5458fa1f55e3059e965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469761"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Povolení diagnostiky Azure v Cloudových službách Azure
Najdete [v tématu Přehled diagnostiky Azure](../azure-diagnostics.md) na pozadí diagnostiky Azure.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Jak povolit diagnostiku v roli pracovního procesu
Tento návod popisuje, jak implementovat roli pracovního procesu Azure, která vydává telemetrická data pomocí třídy .NET EventSource. Azure Diagnostics se používá ke shromažďování telemetrických dat a jejich ukládání v účtu úložiště Azure. Při vytváření role pracovního procesu Visual Studio automaticky povolí diagnostiku 1.0 jako součást řešení v sadách Azure SDK pro .NET 2.4 a starší. Následující pokyny popisují proces vytváření role pracovního procesu, zakázání diagnostiky 1.0 z řešení a nasazení diagnostiky 1.2 nebo 1.3 do role pracovního procesu.

### <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte předplatné Azure a používáte Visual Studio s Azure SDK. Pokud nemáte předplatné Azure, můžete se zaregistrovat k [bezplatné zkušební verzi][Free Trial]. Nezapomeňte [nainstalovat a nakonfigurovat Azure PowerShell verze 0.8.7 nebo novější][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Krok 1: Vytvoření role pracovního procesu
1. Spusťte **Visual Studio**.
2. Vytvořte projekt **Cloudové služby Azure** ze šablony **Cloud,** která cílí na rozhraní .NET Framework 4.5.  Pojmenujte projekt "WadExample" a klepněte na tlačítko Ok.
3. Vyberte **Role pracovního procesu** a klikněte na Ok. Projekt bude vytvořen.
4. V **Průzkumníku řešení**poklepejte na soubor vlastností **WorkerRole1.**
5. Na kartě **Konfigurace** zrušit zaškrtnutí **políčka Povolit diagnostiku** zakázat diagnostiku 1.0 (Azure SDK 2.4 a starší).
6. Sestavte si řešení a ověřte, že nemáte žádné chyby.

### <a name="step-2-instrument-your-code"></a>Krok 2: Nástroj váš kód
Nahraďte obsah WorkerRole.cs následujícím kódem. Třída SampleEventSourceWriter zděděná z [třídy EventSource][EventSource Class]implementuje čtyři metody protokolování: **SendEnums**, **MessageMethod**, **SetOther** a **HighFreq**. První parametr metody **WriteEvent** definuje ID pro příslušnou událost. Metoda Run implementuje nekonečnou smyčku, která volá každou metodu protokolování implementovanou ve třídě **SampleEventSourceWriter** každých 10 sekund.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Krok 3: Nasazení role pracovního procesu

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Nasazení role pracovního procesu do Azure z visual studia výběrem projektu **WadExample** v Průzkumníku řešení pak **publikovat** z nabídky **Sestavení.**
2. Zvolte vaše předplatné.
3. V dialogovém okně **Nastavení publikování Microsoft Azure** vyberte Vytvořit **nový...**.
4. V dialogovém **okně Vytvořit cloudovou službu a účet úložiště** zadejte **název** (například "WadExample") a vyberte skupinu oblastí nebo spřažení.
5. Nastavte **prostředí** na **pracovní .**
6. Podle potřeby upravte všechna další **nastavení** a klepněte na **tlačítko Publikovat**.
7. Po dokončení nasazení ověřte na webu Azure Portal, že vaše cloudová služba je ve **stavu Spuštěno.**

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Krok 4: Vytvoření konfiguračního souboru Diagnostika a instalace přípony
1. Stáhněte si definici schématu veřejného konfiguračního souboru spuštěním následujícího příkazu Prostředí PowerShell:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Přidejte soubor XML do projektu **WorkerRole1** kliknutím pravým tlačítkem myši na projekt **WorkerRole1** a vyberte **Přidat** -> **novou položku...** -> **Položky** -> Visual C#**Datový** -> **soubor XML**. Pojmenujte soubor "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Přidružte soubor WadConfig.xsd ke konfiguračnímu souboru. Ujistěte se, že okno editoru WadExample.xml je aktivní okno. Stisknutím **klávesy F4** otevřete okno **Vlastnosti.** Klepněte na vlastnost **Schémata** v okně **Vlastnosti.** Klikněte na **...** ve vlastnosti **Schémata.** Klikněte na tlačítko **Přidat...** a přejděte do umístění, kam jste uložili soubor XSD, a vyberte soubor WadConfig.xsd. Klikněte na tlačítko **OK**.

4. Nahraďte obsah konfiguračního souboru WadExample.xml následujícím xml a uložte soubor. Tento konfigurační soubor definuje několik čítačů výkonu pro shromažďování: jeden pro využití procesoru a jeden pro využití paměti. Konfigurace pak definuje čtyři události odpovídající metodám ve třídě SampleEventSourceWriter.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Krok 5: Instalace diagnostiky v roli pracovního procesu
Rutiny prostředí PowerShell pro správu diagnostiky na webu nebo roli pracovního procesu jsou: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension a Remove-AzureServiceDiagnosticsExtension.

1. Otevřete Azure PowerShell.
2. Spusťte skript pro instalaci diagnostiky na roli pracovního procesu (nahraďte *StorageAccountKey* klíčem účtu úložiště pro váš účet úložiště wadexample a *config_path* s cestou k souboru *WadExample.xml):*

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Krok 6: Podívejte se na telemetrická data
V **Průzkumníku serveru**Visual Studio přejděte na účet úložiště wadexample. Po cloudové služby běží asi pět (5) minut, měli byste vidět tabulky **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** a **WADSetOtherTable**. Poklepáním na jednu z tabulek zobrazíte telemetrická data, která byla shromážděna.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schéma konfiguračního souboru
Konfigurační soubor Diagnostika definuje hodnoty, které se používají k inicializaci nastavení konfigurace diagnostiky při spuštění diagnostického agenta. Platné hodnoty a příklady naleznete v [nejnovějším odkazu na schéma.](/azure/azure-monitor/platform/diagnostics-extension-schema)

## <a name="troubleshooting"></a>Řešení potíží
Pokud máte potíže, [najdete v tématu řešení potíží diagnostiky Azure](../azure-diagnostics-troubleshooting.md) pro pomoc s běžnými problémy.

## <a name="next-steps"></a>Další kroky
[Podívejte se na seznam souvisejících článků diagnostiky virtuálních strojů Azure,](../azure-monitor/platform/diagnostics-extension-overview.md) kde můžete změnit shromaždišující data, řešit problémy nebo se obecně dozvědět další informace o diagnostice.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/



