---
title: Jak používat diagnostiku Azure (.NET) s Cloud Services (Classic) | Microsoft Docs
description: Pomocí diagnostiky Azure můžete shromažďovat data z cloudových služeb Azure pro účely ladění, měření výkonu, monitorování, analýzy provozu a dalších.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 97e68d338580132b6927c4cc8b206db60fe93ba2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703503"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services-classic"></a>Povolení Azure Diagnostics v Azure Cloud Services (Classic)

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Další informace najdete v tématu [přehled Azure Diagnostics](../azure-monitor/agents/diagnostics-extension-overview.md) pro pozadí Azure Diagnostics.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Postup povolení diagnostiky v roli pracovního procesu
Tento návod popisuje, jak implementovat roli pracovního procesu Azure, která generuje data telemetrie pomocí třídy EventSource .NET. Azure Diagnostics se používá ke shromažďování dat telemetrie a jejich uložení v účtu úložiště Azure. Při vytváření role pracovního procesu Visual Studio automaticky povolí diagnostiku 1,0 jako součást řešení v sadách Azure SDK pro .NET 2,4 a starší. Následující pokyny popisují proces vytvoření role pracovního procesu, zakázání diagnostiky 1,0 z řešení a nasazení diagnostiky 1,2 nebo 1,3 do role pracovního procesu.

### <a name="prerequisites"></a>Předpoklady
V tomto článku se předpokládá, že máte předplatné Azure a používáte sadu Visual Studio se sadou Azure SDK. Pokud předplatné Azure nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi][Free Trial]. Nezapomeňte [nainstalovat a nakonfigurovat Azure PowerShell verze 0.8.7 nebo novější][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Krok 1: vytvoření role pracovního procesu
1. Spusťte **Visual Studio**.
2. Vytvořte projekt **cloudové služby Azure** ze šablony **cloudu** , která se zaměřuje na .NET Framework 4,5.  Pojmenujte projekt "WadExample" a klikněte na tlačítko OK.
3. Vyberte **role pracovního procesu** a klikněte na OK. Projekt se vytvoří.
4. V **Průzkumník řešení** dvakrát klikněte na soubor vlastností **WorkerRole1** .
5. Na kartě **Konfigurace** zrušte kontrolu **Povolení diagnostiky** pro zakázání diagnostiky 1,0 (Azure SDK 2,4 a starší).
6. Sestavte řešení, abyste ověřili, že nedošlo k chybám.

### <a name="step-2-instrument-your-code"></a>Krok 2: instrumentace kódu
Nahraďte obsah role pracovního procesu. cs následujícím kódem. Třída SampleEventSourceWriter zděděná z [třídy EventSource][EventSource Class]implementuje čtyři metody protokolování: **SendEnums**, **MessageMethod**, **SetOther** a **HighFreq**. První parametr metody **WriteEvent** definuje ID příslušné události. Metoda Run implementuje nekonečnou smyčku, která volá každou metodu protokolování implementovanou do třídy **SampleEventSourceWriter** každých 10 sekund.

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


### <a name="step-3-deploy-your-worker-role"></a>Krok 3: nasazení role pracovního procesu

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Nasaďte svoji pracovní roli do Azure ze sady Visual Studio tak, že vyberete projekt **WadExample** v nabídce Průzkumník řešení pak **publikujete** z nabídky **sestavit** .
2. Zvolte vaše předplatné.
3. V dialogovém okně **Microsoft Azure nastavení publikování** vyberte **vytvořit novou...**.
4. V dialogu **vytvořit cloudovou službu a účet úložiště** zadejte **název** (například "WadExample") a vyberte oblast nebo skupinu vztahů.
5. Nastavte **prostředí** na **fázování**.
6. Podle potřeby upravte všechna další **Nastavení** a klikněte na **publikovat**.
7. Po dokončení nasazení ověřte v Azure Portal, že je vaše cloudová služba ve **spuštěném** stavu.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Krok 4: vytvoření konfiguračního souboru diagnostiky a instalace rozšíření
1. Stáhněte si definici schématu veřejného konfiguračního souboru spuštěním následujícího příkazu PowerShellu:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Kliknutím pravým tlačítkem myši na projekt **WorkerRole1** a výběrem **Přidat** novou položku přidejte do projektu **WorkerRole1** soubor XML  ->  **.** -> **Položky**  ->  Visual C# **Data**  ->  **Soubor XML**. Pojmenujte soubor WadExample.xml.

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Přidružte soubor WadConfig. xsd ke konfiguračnímu souboru. Ujistěte se, že je okno Editor WadExample.xml aktivním oknem. Stisknutím klávesy **F4** otevřete okno **vlastnosti** . V okně **vlastnosti** klikněte na vlastnost **schémata** . Klikněte na **...** ve vlastnosti **schemas** . Klikněte na tlačítko **Přidat...** a přejděte do umístění, kam jste uložili soubor XSD, a vyberte soubor WadConfig. xsd. Klikněte na **OK**.

4. Obsah konfiguračního souboru WadExample.xml nahraďte následujícím kódem XML a uložte soubor. Tento konfigurační soubor definuje několik čítačů výkonu, které se mají shromáždit: jeden pro využití CPU a jeden pro využití paměti. Konfigurace pak definuje čtyři události odpovídající metodám ve třídě SampleEventSourceWriter.

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

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Krok 5: instalace diagnostiky do role pracovního procesu
Rutiny PowerShellu pro správu diagnostiky na webu nebo v roli pracovního procesu jsou: set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension a Remove-AzureServiceDiagnosticsExtension.

1. Otevřete Azure PowerShell.
2. Spusťte skript pro instalaci diagnostiky do role pracovního procesu (nahraďte *StorageAccountKey* klíčem účtu úložiště pro účet úložiště wadexample a *config_path* cestou k souboru *WadExample.xml* ):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Krok 6: Prohlédněte si data telemetrie
V **Průzkumník serveru** sady Visual Studio přejděte do účtu úložiště wadexample. Po spuštění cloudové služby přibližně o pěti (5) minutách byste měli vidět tabulky **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** a **WADSetOtherTable**. Dvojitým kliknutím na jednu z tabulek zobrazíte shromážděnou telemetrii.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schéma konfiguračního souboru
Konfigurační soubor diagnostiky definuje hodnoty, které se použijí k inicializaci nastavení konfigurace diagnostiky při spuštění agenta diagnostiky. Platné hodnoty a příklady najdete v [nejnovějším referenčním schématu](../azure-monitor/agents/diagnostics-extension-versions.md) .

## <a name="troubleshooting"></a>Poradce při potížích
Pokud máte problémy, přečtěte si téma [řešení potíží s Azure Diagnostics](../azure-monitor/agents/diagnostics-extension-troubleshooting.md) , kde najdete nápovědu k běžným problémům.

## <a name="next-steps"></a>Další kroky
Pokud chcete změnit shromažďovaná data, [Podívejte se na seznam souvisejících diagnostických článků Azure Virtual-Machine](../azure-monitor/agents/diagnostics-extension-overview.md) , které vám pomohou při řešení potíží nebo další informace o diagnostice obecně.

[EventSource Class]: /dotnet/api/system.diagnostics.tracing.eventsource

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: /previous-versions/azure/gg433048(v=azure.100)
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: /powershell/azure/