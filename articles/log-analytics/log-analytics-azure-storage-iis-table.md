---
title: Používání úložiště blob pro službu IIS a tabulka úložiště pro události v Azure Log Analytics | Microsoft Docs
description: Analýzy protokolů můžete přečíst v protokolech služby Azure, které zápis diagnostiky table Storage nebo protokoly služby IIS zapisovat do úložiště objektů blob.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 8f923cc081ea652c8e32d4109225044c70c8767d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128737"
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-log-analytics"></a>Používat úložiště objektů blob v Azure pro službu IIS a Azure úložiště table pro události se analýzy protokolů

Analýzy protokolů můžete přečíst v protokolech u následujících služeb, které zapsat diagnostiky table Storage nebo protokoly služby IIS zapisovat do úložiště objektů blob:

* Service Fabric clusterů (Preview)
* Virtuální počítače
* Web/role pracovního procesu

Před analýzy protokolů můžete shromažďovat data pro tyto prostředky, musí být povolena Azure diagnostics.

Po diagnostiky jsou povolené, můžete použít portál Azure nebo PowerShell konfigurace analýzy protokolů shromažďovat protokoly.

Azure Diagnostics je rozšířením Azure, která umožňuje shromažďování diagnostických dat z role pracovního procesu, webovou roli nebo virtuální počítač spuštěný v Azure. Data je uložený v účtu úložiště Azure a můžete pak shromáždit pomocí analýzy protokolů.

Pro analýzy protokolů pro shromažďování těchto protokolů Azure Diagnostics musí být v protokolech v následujících umístěních:

| Typ protokolu | Typ prostředku | Umístění |
| --- | --- | --- |
| Protokoly IIS |Virtuální počítače <br> Webové role <br> Role pracovního procesu |wad logfiles služby iis (úložiště objektů Blob) |
| Syslog |Virtuální počítače |LinuxsyslogVer2v0 (tabulky úložiště) |
| Provozní události služby prostředků infrastruktury |Uzly Service Fabric |WADServiceFabricSystemEventTable |
| Události služby Fabric spolehlivé objektu Actor |Uzly Service Fabric |WADServiceFabricReliableActorEventTable |
| Události spolehlivé služby Service Fabric |Uzly Service Fabric |WADServiceFabricReliableServiceEventTable |
| Protokoly událostí Windows |Uzly Service Fabric <br> Virtuální počítače <br> Webové role <br> Role pracovního procesu |WADWindowsEventLogsTable (Table Storage) |
| Protokoly systému Windows trasování událostí pro Windows |Uzly Service Fabric <br> Virtuální počítače <br> Webové role <br> Role pracovního procesu |WADETWEventTable (Table Storage) |

> [!NOTE]
> Protokoly služby IIS z webů Azure nejsou aktuálně podporovány.
>
>

Pro virtuální počítače, máte možnost nainstalovat [analýzy protokolů agenta](log-analytics-azure-vm-extension.md) do virtuálního počítače povolit další statistiky. Kromě toho schopní analyzovat protokoly událostí a protokoly služby IIS, můžete provádět další analýzu, včetně sledování změn konfigurace, vyhodnocení SQL a vyhodnocení aktualizací.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Povolit Azure diagnostics ve virtuálním počítači pro protokol událostí a služby IIS protokolu kolekce
Pomocí následujícího postupu umožníte Azure diagnostics ve virtuálním počítači pro protokol událostí a služby IIS protokolu kolekci pomocí portálu Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Chcete-li povolit Azure diagnostics ve virtuálním počítači pomocí portálu Azure
1. Když vytvoříte virtuální počítač, nainstalujte agenta virtuálního počítače. Pokud virtuální počítač již existuje, ověřte, že je již nainstalován Agent virtuálního počítače.

   * Na portálu Azure přejděte do virtuálního počítače, vyberte **volitelné konfiguraci**, pak **diagnostiky** a nastavte **stav** k **na** .

     Po dokončení zpracování se má virtuální počítač Azure Diagnostics rozšíření nainstalovaná a spuštěná. Toto rozšíření je zodpovědná za shromažďování dat diagnostiky.
2. Povolí monitorování a konfigurace protokolování událostí na existující virtuální počítač. Můžete povolit diagnostiku na úrovni virtuálního počítače. Povolte diagnostiku a pak nastavte protokolování událostí, proveďte následující kroky:

   1. Vyberte virtuální počítač.
   2. Klikněte na tlačítko **monitorování**.
   3. Klikněte na tlačítko **diagnostiky**.
   4. Nastavte **stav** k **ON**.
   5. Vyberte každý diagnostiky protokolu, které chcete shromažďovat.
   6. Klikněte na **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Povolit Azure diagnostics ve webové roli pro shromažďování protokolů a událostí služby IIS
Odkazovat na [jak k povolení diagnostiky v cloudové službě](../cloud-services/cloud-services-dotnet-diagnostics.md) obecné pokyny k povolení Azure diagnostics. Níže uvedené pokyny použijte tyto informace a přizpůsobit pro použití s analýzy protokolů.

S Azure diagnostics povoleno:

* Protokoly služby IIS jsou uloženy ve výchozím nastavení, s přenést v intervalu scheduledTransferPeriod přenosu dat protokolu.
* Ve výchozím nastavení se nepřenesou protokoly událostí systému Windows.

### <a name="to-enable-diagnostics"></a>Povolí se Diagnostika
Chcete-li povolit protokol událostí systému Windows, nebo změnit scheduledTransferPeriod, konfigurovat Azure Diagnostics pomocí konfiguračního souboru XML (diagnostics.wadcfg), jak je znázorněno v [krok 4: vytvoření konfiguračního souboru diagnostiky a nainstalovat rozšíření](../cloud-services/cloud-services-dotnet-diagnostics.md)

Následující příklad konfiguračního souboru shromažďuje protokoly služby IIS a všechny události z protokolů systému a aplikace:

```
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

Zajistěte, aby vaše ConfigurationSettings určovala účtu úložiště, jako v následujícím příkladu:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

**AccountName** a **AccountKey** hodnoty se nacházejí v portálu Azure v řídicím panelu účet úložiště, v části Spravovat přístupové klíče. Protokol pro připojovací řetězec musí být **https**.

Jakmile aktualizované konfigurace diagnostiky se použije ke cloudové službě a diagnostiky ho je zápis do úložiště Azure, pak jste připraveni ke konfiguraci analýzy protokolů.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Pomocí portálu Azure ke shromažďování protokolů z Azure Storage
Na portálu Azure můžete použít ke konfiguraci analýzy protokolů pro shromažďování protokolů u následujících služeb Azure:

* Clustery Service Fabricu
* Virtuální počítače
* Web/role pracovního procesu

Na portálu Azure přejděte do pracovního prostoru analýzy protokolů a provádět následující úlohy:

1. Klikněte na tlačítko *protokoly účtů úložiště*
2. Klikněte *přidat* úloh
3. Vyberte účet úložiště, který obsahuje protokolů diagnostiky
   * Tento účet může být účet úložiště classic nebo účet úložiště Azure Resource Manager
4. Vyberte datový typ, který chcete shromáždit protokoly pro
   * Možnosti jsou protokoly služby IIS. Události; Syslog (Linux); Protokoly trasování událostí pro Windows. Události služby prostředků infrastruktury
5. Hodnota pro zdroj je automaticky vyplněno na základě typu dat a nedá se změnit
6. Kliknutím na tlačítko OK uložte konfiguraci

Opakujte kroky 2 až 6 pro další účty úložiště a datové typy, které chcete pro shromažďování protokolů analýzy.

V přibližně 30 minut budete moci zobrazit data z účtu úložiště v analýzy protokolů. Zobrazí se pouze data, která je zapsán do úložiště po použití konfigurace. Analýzy protokolů číst existující data z účtu úložiště.

> [!NOTE]
> Na portálu neověřuje, zda zdroj existuje v účtu úložiště nebo pokud probíhá zápis nová data.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Povolit Azure diagnostics ve virtuálním počítači pro protokol událostí a služby IIS protokolu kolekce pomocí prostředí PowerShell
Postupujte podle kroků v [konfigurace analýzy protokolů Azure diagnostics indexování](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) číst z Azure diagnostiky, které jsou napsané table Storage pomocí prostředí PowerShell.

Pomocí Azure PowerShell přesněji můžete události, které se zapisují do služby Azure Storage.
Další informace najdete v tématu [povolení diagnostiky v Azure Virtual Machines](../virtual-machines-dotnet-diagnostics.md).

Můžete povolit a aktualizovat Azure diagnostics pomocí následujícího skriptu prostředí PowerShell.
Tento skript můžete také použít s vlastní konfiguraci protokolování.
Upravte skript tak, aby nastavení účtu úložiště, název služby a název virtuálního počítače.
Tento skript využívá rutiny pro klasické virtuální počítače.

Zkontrolujte následující ukázka skriptu, zkopírujte jej, upravte ho požadovaným způsobem, uložit ukázku jako soubor skriptu prostředí PowerShell a spusťte skript.

```
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

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Další postup
* [Shromažďovat protokoly a metriky pro služby Azure](log-analytics-azure-storage.md) podporovaných službami Azure.
* [Povolit řešení](log-analytics-add-solutions.md) zajistit přehled o data.
* [Použijte vyhledávací dotazy](log-analytics-log-searches.md) analyzovat data.
