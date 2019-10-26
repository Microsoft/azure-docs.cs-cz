---
title: Použijte úložiště objektů BLOB pro služby IIS a úložiště tabulek pro události v Azure Monitor | Microsoft Docs
description: Azure Monitor může číst protokoly pro služby Azure, které zapisují diagnostiku do tabulkového úložiště nebo do protokolů IIS zapsaných do úložiště objektů BLOB.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 04/12/2017
ms.openlocfilehash: 8f70ecc96269783c29c566fb89bd617f034316b1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932674"
---
# <a name="collect-azure-diagnostic-logs-from-azure-storage"></a>Shromažďovat diagnostické protokoly Azure z Azure Storage

Azure Monitor může číst protokoly pro následující služby, které zapisují diagnostiku do tabulkového úložiště nebo do protokolů IIS zapsaných do úložiště objektů BLOB:

* Service Fabric clustery (Preview)
* Služba Virtual Machines
* Webové a pracovní role

Aby mohla Azure Monitor shromažďovat data do pracovního prostoru Log Analytics pro tyto prostředky, musí být povolená Diagnostika Azure.

Po povolení diagnostiky můžete pomocí Azure Portal nebo PowerShellu nakonfigurovat pracovní prostor pro shromáždění protokolů.

Azure Diagnostics je rozšíření Azure, které umožňuje shromažďovat diagnostická data z role pracovního procesu, webové role nebo virtuálního počítače spuštěného v Azure. Data jsou uložená v účtu úložiště Azure a je možné je shromažďovat pomocí Azure Monitor.

Pro Azure Monitor shromažďování těchto protokolů Azure Diagnostics musí být protokoly v následujících umístěních:

| Typ protokolu | Typ prostředku | Umístění |
| --- | --- | --- |
| Protokoly IIS |Služba Virtual Machines <br> Webové role <br> Role pracovního procesu |WAD – IIS – soubory protokolu (Blob Storage) |
| Syslog |Služba Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric provozní události |Uzly Service Fabric |WADServiceFabricSystemEventTable |
| Service Fabric spolehlivých událostí objektu actor |Uzly Service Fabric |WADServiceFabricReliableActorEventTable |
| Service Fabric spolehlivých událostí služby |Uzly Service Fabric |WADServiceFabricReliableServiceEventTable |
| Protokoly událostí systému Windows |Uzly Service Fabric <br> Služba Virtual Machines <br> Webové role <br> Role pracovního procesu |WADWindowsEventLogsTable (Table Storage) |
| Protokoly ETW systému Windows |Uzly Service Fabric <br> Služba Virtual Machines <br> Webové role <br> Role pracovního procesu |WADETWEventTable (Table Storage) |

> [!NOTE]
> Protokoly služby IIS z Azure websites se momentálně nepodporují.
>
>

U virtuálních počítačů máte možnost nainstalovat [agenta Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) do svého virtuálního počítače, abyste mohli povolit další přehledy. Kromě toho, že je možné analyzovat protokoly služby IIS a protokoly událostí, můžete provádět další analýzy, včetně sledování změn konfigurace, posouzení SQL a posouzení aktualizací.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Povolení diagnostiky Azure na virtuálním počítači pro protokol událostí a shromažďování protokolů služby IIS

Pomocí následujícího postupu můžete povolit diagnostiku Azure ve virtuálním počítači pro protokol událostí a kolekci protokolů IIS pomocí portál Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Povolení diagnostiky Azure na virtuálním počítači s Azure Portal

1. Při vytváření virtuálního počítače nainstalujte agenta virtuálního počítače. Pokud virtuální počítač již existuje, ověřte, zda je agent virtuálního počítače již nainstalován.

   * V Azure Portal přejděte na virtuální počítač, vyberte **volitelná konfigurace**, potom na **diagnostiku** a nastavte **stav** **na zapnuto**.

     Po dokončení má virtuální počítač nainstalované a běžící rozšíření Azure Diagnostics. Toto rozšíření zodpovídá za shromažďování diagnostických dat.
2. Povolí monitorování a konfiguraci protokolování událostí na stávajícím virtuálním počítači. Diagnostiku můžete povolit na úrovni virtuálního počítače. Pokud chcete povolit diagnostiku a potom nakonfigurovat protokolování událostí, proveďte následující kroky:

   1. Vyberte virtuální počítač.
   2. Klikněte na **monitorování**.
   3. Klikněte na **Diagnostika**.
   4. Nastavte **stav** na **zapnuto**.
   5. Vyberte každý diagnostický protokol, který chcete shromáždit.
   6. Klikněte na **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Povolení diagnostiky Azure ve webové roli pro shromažďování protokolů a událostí služby IIS

Obecné kroky pro povolení diagnostiky Azure najdete [v tématu Postup povolení diagnostiky v cloudové službě](../../cloud-services/cloud-services-dotnet-diagnostics.md) . Níže uvedené pokyny používají tyto informace a přizpůsobují ji pro použití s Log Analytics.

S povolenou službou Azure Diagnostics:

* Protokoly služby IIS se ve výchozím nastavení ukládají s daty protokolu přenesených v intervalu přenosu scheduledTransferPeriod.
* Protokoly událostí systému Windows se ve výchozím nastavení nepřenášejí.

### <a name="to-enable-diagnostics"></a>Postup povolení diagnostiky

Chcete-li povolit protokoly událostí systému Windows nebo změnit scheduledTransferPeriod, nakonfigurujte Azure Diagnostics pomocí konfiguračního souboru XML (Diagnostics. wadcfg), jak je znázorněno v [kroku 4: vytvoření konfiguračního souboru diagnostiky a instalace rozšíření.](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Následující ukázkový konfigurační soubor shromažďuje protokoly služby IIS a všechny události z aplikace a systémových protokolů:

```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Ujistěte se, že váš ConfigurationSettings Určuje účet úložiště, jako v následujícím příkladu:

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Hodnoty **účtu** a **AccountKey** se nacházejí v Azure Portal na řídicím panelu účtu úložiště v části Správa přístupových klíčů. Protokol pro připojovací řetězec musí být **https**.

Po použití aktualizované konfigurace diagnostiky pro vaši cloudovou službu, která zapisuje diagnostiku do Azure Storage, jste připraveni nakonfigurovat Log Analytics pracovní prostor.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Shromažďování protokolů z Azure Storage pomocí Azure Portal

Azure Portal můžete použít ke konfiguraci pracovního prostoru Log Analytics v Azure Monitor ke shromáždění protokolů pro následující služby Azure:

* Clustery Service Fabric
* Služba Virtual Machines
* Webové a pracovní role

V Azure Portal přejděte do pracovního prostoru Log Analytics a proveďte následující úlohy:

1. Klikněte na *protokoly účtů úložiště* .
2. Klikněte na *Přidat* úlohu.
3. Vyberte účet úložiště, který obsahuje diagnostické protokoly.
   * Tento účet může být buď klasický účet úložiště, nebo účet úložiště Azure Resource Manager.
4. Vyberte datový typ, pro který chcete shromáždit protokoly.
   * Možnosti jsou protokoly IIS; Událost Syslog (Linux); Protokoly ETW; Události Service Fabric
5. Hodnota pro zdroj se vyplní automaticky na základě datového typu a nedá se změnit.
6. Kliknutím na OK uložte konfiguraci.

Opakujte kroky 2-6 pro další účty úložiště a datové typy, které chcete shromáždit do pracovního prostoru.

Za přibližně 30 minut budete moci zobrazit data z účtu úložiště v pracovním prostoru Log Analytics. Po použití konfigurace se zobrazí pouze data zapsaná do úložiště. Pracovní prostor nečte již existující data z účtu úložiště.

> [!NOTE]
> Portál neověřuje, jestli zdroj existuje v účtu úložiště, nebo jestli se zapisují nová data.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Povolení diagnostiky Azure ve virtuálním počítači pro protokol událostí a shromažďování protokolů IIS pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Použijte postup v tématu [konfigurace Azure monitor k indexování diagnostiky Azure](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) pro použití PowerShellu ke čtení z diagnostiky Azure, která se zapsala do tabulkového úložiště.

Pomocí Azure PowerShell můžete přesněji zadat události, které jsou zapsány do Azure Storage.
Další informace najdete v tématu [Povolení diagnostiky v Azure Virtual Machines](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Diagnostiku Azure můžete povolit a aktualizovat pomocí následujícího skriptu PowerShellu.
Tento skript můžete také použít s vlastní konfigurací protokolování.
Upravte skript tak, aby se nastavil účet úložiště, název služby a název virtuálního počítače.
Skript používá rutiny pro klasické virtuální počítače.

Přečtěte si následující ukázkový skript, zkopírujte ho, podle potřeby ho upravte, uložte ukázku jako soubor skriptu PowerShellu a potom spusťte skript.

```powershell
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Další kroky

* [Shromážděte protokoly a metriky pro služby Azure](collect-azure-metrics-logs.md) pro podporované služby Azure.
* [Povolte řešení](../../azure-monitor/insights/solutions.md) a poskytněte vám tak přehled o datech.
* Data můžete analyzovat [pomocí vyhledávacích dotazů](../../azure-monitor/log-query/log-query-overview.md) .