---
title: Rozšíření virtuálního počítače Azure Performance Diagnostics pro Windows | Microsoft Docs
description: Zavádí rozšíření virtuálního počítače Azure Performance Diagnostics pro Windows.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 9edba575b35613abb8bc3081964a37b838bb358b
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656591"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Rozšíření virtuálních počítačů pro Windows pro diagnostiku výkonu Azure

Rozšíření virtuálních počítačů Azure Performance Diagnostics pomáhá shromažďovat diagnostická data o výkonu z virtuálních počítačů s Windows. Rozšíření provádí analýzu a poskytuje zprávu o zjištěních a doporučeních pro identifikaci a řešení problémů s výkonem na virtuálním počítači. Toto rozšíření nainstaluje nástroj pro řešení potíží s názvem [PerfInsights](./how-to-use-perfinsights.md).

> [!NOTE]
> Pokud chcete na svém VIRTUÁLNÍm počítači spustit diagnostiku z Azure Portal pro jiné než klasické virtuální počítače, doporučujeme použít nové prostředí. Další informace najdete v tématu [Diagnostika výkonu pro virtuální počítače Azure](performance-diagnostics.md) . 

## <a name="prerequisites"></a>Požadavky

Toto rozšíření se dá nainstalovat na
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows 10
* Windows 8.1
* Windows 8

## <a name="extension-schema"></a>Schéma rozšíření
Následující JSON ukazuje schéma pro rozšíření virtuálního počítače Azure Performance Diagnostics. Toto rozšíření vyžaduje název a klíč účtu úložiště pro uložení výstupu a sestavy diagnostiky. Tyto hodnoty jsou citlivé. Klíč účtu úložiště by měl být uložený v konfiguraci chráněného nastavení. Data nastavení chráněná rozšířením virtuálního počítače Azure jsou šifrovaná a v cílovém virtuálním počítači se dešifrují jenom. Všimněte si, že **storageAccountName** a **storageAccountKey** rozlišují velká a malá písmena. Další požadované parametry jsou uvedeny v následující části.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "storageAccountName": "[parameters('storageAccountName')]",
          "performanceScenario": "[parameters('performanceScenario')]",
          "traceDurationInSeconds": "[parameter('traceDurationInSeconds')]",
          "perfCounterTrace": "[parameters('perfCounterTrace')]",
          "networkTrace": "[parameters('networkTrace')]",
          "xperfTrace": "[parameters('xperfTrace')]",
          "storPortTrace": "[parameters('storPortTrace')]",
          "srNumber": "[parameters('srNumber')]",
          "requestTimeUtc":  "[parameters('requestTimeUtc')]",
          "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota/příklad | Popis |
|--|--|--|
| apiVersion | 2015-06-15 | Verze rozhraní API. |
| vydavatel | Microsoft. Azure. Performance. Diagnostics | Obor názvů vydavatele pro rozšíření. |
| typ | AzurePerformanceDiagnostics | Typ rozšíření virtuálního počítače |
| typeHandlerVersion | 1.0 | Verze obslužné rutiny rozšíření. |
| performanceScenario | Basic | Scénář výkonu, pro který mají být zachycena data. Platné hodnoty jsou: **Basic**, **vmslow**, **azurefiles** a **Custom**. |
| traceDurationInSeconds | 300 | Doba trvání trasování, pokud je vybrána kterákoli z možností trasování. |
| perfCounterTrace | p | Možnost pro povolení trasování čítače výkonu. Platné hodnoty jsou hodnota **p** nebo prázdná. Pokud toto trasování nechcete zachytit, ponechte hodnotu prázdnou. |
| networkTrace | n | Možnost pro povolení trasování sítě. Platné hodnoty jsou **n** nebo prázdná hodnota. Pokud toto trasování nechcete zachytit, ponechte hodnotu prázdnou. |
| xperfTrace | x | Možnost, která povolí trasování XPerf. Platné hodnoty jsou **x** nebo prázdná hodnota. Pokud toto trasování nechcete zachytit, ponechte hodnotu prázdnou. |
| storPortTrace | s | Možnost pro povolení trasování StorPort. Platné hodnoty mají hodnotu **s** nebo je prázdná. Pokud toto trasování nechcete zachytit, ponechte hodnotu prázdnou. |
| srNumber | 123452016365929 | Číslo lístku podpory, pokud je k dispozici. Pokud ho nemáte, nechte tuto hodnotu prázdnou. |
| requestTimeUtc | 2017-09-28T22:08:53.736 Z | Aktuální datum a čas ve standardu UTC. Pokud používáte portál k instalaci tohoto rozšíření, nemusíte tuto hodnotu zadávat. |
| resourceId | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName} | Jedinečný identifikátor virtuálního počítače |
| storageAccountName | mystorageaccount | Název účtu úložiště, do kterého se mají ukládat diagnostické protokoly a výsledky. |
| storageAccountKey | lDuVvxuZB28NNP... hAiRF3voADxLBTcc = = | Klíč pro účet úložiště |

## <a name="install-the-extension"></a>Instalace rozšíření

Pokud chcete nainstalovat rozšíření na virtuální počítače s Windows, postupujte podle těchto pokynů:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte virtuální počítač, na který chcete nainstalovat toto rozšíření.

    ![Snímek obrazovky Azure Portal s zvýrazněnými virtuálními počítači](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Vyberte okno **rozšíření** a vyberte **Přidat**.

    ![Snímek obrazovky s oknem rozšíření se zvýrazněným příoknom přidat](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Vyberte **Azure Performance Diagnostics**, zkontrolujte podmínky a ujednání a vyberte **vytvořit**.

    ![Snímek obrazovky s novým prostředkem s zvýrazněnou diagnostikou výkonu Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Zadejte hodnoty parametrů pro instalaci a pro instalaci rozšíření vyberte **OK** . Další informace o podporovaných scénářích najdete v tématu [Jak používat PerfInsights](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![Snímek obrazovky dialogového okna Instalace rozšíření](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Po úspěšném dokončení instalace se zobrazí zpráva s informacemi o tomto stavu.

    ![Snímek obrazovky se zprávou o úspěšném zřízení](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Rozšíření se spustí po úspěšném zřízení. Pro základní scénář trvá více než dvě minuty. V ostatních scénářích se spouští během doby určené během instalace.

## <a name="remove-the-extension"></a>Odebrání rozšíření

K odebrání rozšíření z virtuálního počítače použijte tento postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com), vyberte virtuální počítač, ze kterého chcete odebrat toto rozšíření, a pak vyberte okno **rozšíření** . 
2. V seznamu vyberte (**...**) pro položku rozšíření pro diagnostiku výkonu a vyberte **odinstalovat**.

    ![Snímek obrazovky s oknem rozšíření se zvýrazněnou možností odinstalovat](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Můžete také vybrat položku rozšíření a vybrat možnost **odinstalovat** .

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Schéma JSON popsané v předchozí části se dá použít v šabloně Azure Resource Manager. Tím se během nasazování šablony Azure Resource Manager spustí rozšíření virtuálního počítače Azure Performance Diagnostics. Tady je Ukázková šablona:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring",
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring",
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
  "traceDurationInSeconds": {
    "type": "int",
    "defaultValue": 300
  },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "storageAccountName": "[parameters('storageAccountName')]",
          "performanceScenario": "[parameters('performanceScenario')]",
          "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
          "perfCounterTrace": "[parameters('perfCounterTrace')]",
          "networkTrace": "[parameters('networkTrace')]",
          "xperfTrace": "[parameters('xperfTrace')]",
          "storPortTrace": "[parameters('storPortTrace')]",
          "srNumber": "[parameters('srNumber')]",
          "requestTimeUtc":  "[parameters('requestTimeUtc')]",
          "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>Nasazení PowerShellu

`Set-AzVMExtension`Příkaz se dá použít k nasazení rozšíření virtuálního počítače Azure Performance Diagnostics do existujícího virtuálního počítače.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Informace o zachycených datech
Nástroj PerfInsights shromažďuje různé protokoly, konfiguraci a diagnostická data v závislosti na vybraném scénáři. Další informace najdete v [dokumentaci k PerfInsights](./how-to-use-perfinsights.md).

## <a name="view-and-share-the-results"></a>Zobrazení a sdílení výsledků

Výstup z rozšíření najdete v souboru zip, který jste nahráli do účtu úložiště zadaného během instalace a který se sdílí po dobu 30 dnů pomocí [sdílených přístupových podpisů (SAS)](../../storage/common/storage-sas-overview.md). Tento soubor zip obsahuje diagnostické protokoly a sestavu s výsledky a doporučeními. Odkaz SAS na výstupní soubor zip najdete v textovém souboru s názvem *zipfilename* _saslink.txt ve složce **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics \\ \<version>**. Kdokoli, kdo má tento odkaz, může stáhnout soubor zip.

Aby mohl pracovník podpory při práci na lístku podpory pracovat, mohl vám toto propojení SAS využít ke stažení diagnostických dat.

Chcete-li zobrazit sestavu, rozbalte soubor zip a otevřete soubor **PerfInsights Report.html** .

Soubor zip byste si měli také stáhnout přímo z portálu tak, že vyberete rozšíření.

![Snímek obrazovky s podrobným stavem diagnostiky výkonu](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Odkaz SAS zobrazený na portálu nemusí někdy fungovat. To může být způsobeno chybnou adresou URL během operací kódování a dekódování. Místo toho můžete získat odkaz přímo ze souboru * _saslink.txt z virtuálního počítače.

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

- Stav nasazení rozšíření (v oznamovací oblasti) může ukazovat "probíhá nasazení", i když je rozšíření úspěšně zřízené.

    Tento problém se dá bezpečně ignorovat, pokud stav rozšíření indikuje, že se rozšíření úspěšně zřídilo.
- Během instalace můžete vyřešit některé problémy pomocí protokolů rozšíření. Výstup spuštění rozšíření se protokoluje do souborů, které se nacházejí v následujícím adresáři:

    `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>`

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete kontaktovat odborníky na Azure na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).