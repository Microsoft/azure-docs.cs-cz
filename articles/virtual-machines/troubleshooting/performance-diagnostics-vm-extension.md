---
title: Rozšíření virtuálního počítače Azure Performance Diagnostics pro Windows| Dokumenty společnosti Microsoft
description: Představuje rozšíření virtuálního počítače Azure Performance Diagnostics pro Windows.
services: virtual-machines-windows'
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
ms.openlocfilehash: 6f104fc6513874bfef5f4bf9fe7f536c3e3d69cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71057542"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Rozšíření virtuálních počítačů pro Windows pro diagnostiku výkonu Azure

Rozšíření virtuálních aplikací Azure Performance Diagnostics pomáhá shromažďovat diagnostická data výkonu z virtuálních počítačů s Windows. Rozšíření provádí analýzu a poskytuje zprávu o zjištění a doporučení k identifikaci a řešení problémů s výkonem na virtuálním počítači. Toto rozšíření nainstaluje nástroj pro odstraňování potíží s názvem [PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
> Pokud chcete spustit diagnostiku na vašem virtuálním počítači z portálu Azure pro neklasické virtuální počítače, doporučujeme použít nové prostředí. Další informace najdete [v tématu Diagnostika výkonu pro virtuální počítače Azure.](performance-diagnostics.md) 

## <a name="prerequisites"></a>Požadavky

Toto rozšíření lze nainstalovat do systému Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Může být také nainstalován na Windows 8.1 a Windows 10.

## <a name="extension-schema"></a>Schéma rozšíření
Následující JSON zobrazuje schéma pro rozšíření virtuálního počítače diagnostiky výkonu Azure. Toto rozšíření vyžaduje název a klíč pro účet úložiště pro uložení výstupu diagnostiky a sestavy. Tyto hodnoty jsou citlivé. Klíč účtu úložiště by měl být uložen uvnitř konfigurace chráněného nastavení. Data nastavení chráněné rozšířením virtuálního počítače Azure jsou šifrovaná a dešifrují se jenom na cílovém virtuálním počítači. Všimněte si, že **storageAccountName** a **storageAccountKey** rozlišují malá a velká písmena. Další požadované parametry jsou uvedeny v následující části.

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
```

### <a name="property-values"></a>Hodnoty vlastností

|   **Název**   |**Hodnota / Příklad**|       **Popis**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Verze rozhraní API.
|vydavatel|Diagnostika Microsoft.Azure.Performance.Diagnostics|Obor názvů vydavatele pro rozšíření.
|type|AzurePerformanceDiagnostics|Typ rozšíření virtuálního soudu.
|typeHandlerVersion|1.0|Verze obslužné rutiny rozšíření.
|performanceScenario|Základní|Scénář výkonu, pro který chcete zachytit data. Platné hodnoty jsou: **základní**, **vmslow**, **azurefiles**a **vlastní**.
|traceDurationInSeconds|300|Doba trvání trasování, pokud jsou vybrány některé z možností trasování.
|perfCounterTrace|p|Možnost povolit trasování čítače výkonu. Platné hodnoty jsou **p** nebo prázdná hodnota. Pokud nechcete zachytit toto trasování, ponechte hodnotu jako prázdnou.
|networkTrace|n|Možnost povolit trasování v síti. Platné hodnoty jsou **n** nebo prázdná hodnota. Pokud nechcete zachytit toto trasování, ponechte hodnotu jako prázdnou.
|xperfTrace|x|Možnost povolit trasování XPerf. Platné hodnoty jsou **x** nebo prázdná hodnota. Pokud nechcete zachytit toto trasování, ponechte hodnotu jako prázdnou.
|trasování storPort|s|Možnost povolit trasování StorPort. Platné hodnoty jsou **s** nebo prázdná hodnota. Pokud nechcete zachytit toto trasování, ponechte hodnotu jako prázdnou.
|srČíslo|123452016365929|Číslo lístku podpory, pokud je k dispozici. Pokud ji nemáte, ponechejte hodnotu prázdnou.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Aktuální čas data v utc. Pokud používáte portál k instalaci tohoto rozšíření, není nutné zadat tuto hodnotu.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|Jedinečný identifikátor virtuálního soudu.
|storageAccountName|mystorageaccount|Název účtu úložiště pro uložení protokolů diagnostiky a výsledků.
|storageAccountKey|lDuVvxuZB28NNP... hAiRF3voADxLBTcc==|Klíč pro účet úložiště.

## <a name="install-the-extension"></a>Instalace rozšíření

Podle následujících pokynů nainstalujte rozšíření na virtuální počítače s Windows:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte virtuální počítač, do kterého chcete toto rozšíření nainstalovat.

    ![Snímek obrazovky s portálem Azure se zvýrazněnou možností Virtuální počítače](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Vyberte okno **Rozšíření** a vyberte **Přidat**.

    ![Snímek obrazovky s možností rozšíření se zvýrazněnou možností Přidat](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Vyberte **Diagnostika výkonu Azure**, zkontrolujte podmínky a vyberte **Vytvořit**.

    ![Snímek obrazovky Nový prostředek se zvýrazněnou možností Diagnostika výkonu Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Zadejte hodnoty parametrů pro instalaci a kliknutím na **ok** nainstalujte rozšíření. Další informace o podporovaných scénářích naleznete v [tématu Použití perfinsights](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![Snímek obrazovky s dialogovým oknem Instalovat rozšíření](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Po úspěšné instalaci se zobrazí zpráva otomto stavu.

    ![Snímek obrazovky s oznámením o zřizování proběhlo úspěšně](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Rozšíření se spustí, když zřizování proběhlo úspěšně. Trvá dvě minuty nebo méně pro základní scénář. Pro jiné scénáře prochází dobu trvání zadanou během instalace.

## <a name="remove-the-extension"></a>Odebrání rozšíření
Pokud chcete rozšíření odebrat z virtuálního počítače, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com), vyberte virtuální počítač, ze kterého chcete odebrat toto rozšíření a pak vyberte okno **Rozšíření.** 
2. Vyberte položku (**...**) pro položku rozšíření diagnostiky výkonu ze seznamu a vyberte **odinstalovat**.

    ![Snímek obrazovky s možností rozšíření se zvýrazněnou možností Odinstalovat](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Můžete také vybrat položku rozšíření a vybrat možnost **Odinstalovat.**

## <a name="template-deployment"></a>Nasazení šablon
Rozšíření virtuálních automatů Azure se dá nasadit pomocí šablon Azure Resource Manager. Schéma JSON popsané v předchozí části lze použít v šabloně Azure Resource Manager. To spustí rozšíření virtuálního počítače Diagnostika výkonu Azure během nasazení šablony Azure Resource Manager. Zde je ukázková šablona:

```
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
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
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
Příkaz `Set-AzVMExtension` se dá použít k nasazení rozšíření virtuálního počítače Azure Performance Diagnostics do existujícího virtuálního počítače.

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

## <a name="information-on-the-data-captured"></a>Informace o zachycených údajích
Nástroj PerfInsights shromažďuje různé protokoly, konfigurace a diagnostická data, v závislosti na vybraném scénáři. Další informace naleznete v [dokumentaci perfInsights](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Zobrazení a sdílení výsledků

Výstup z rozšíření lze nalézt v souboru ZIP, který byl odeslán do účtu úložiště určeného během instalace a je sdílen po dobu 30 dnů pomocí [sdílených přístupových podpisů (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) Tento soubor zip obsahuje diagnostické protokoly a zprávu s nálezy a doporučeními. Odkaz SAS na výstupní soubor ZIP lze nalézt uvnitř textového souboru s názvem *zipfilename*_saslink.txt pod složkou **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics.AzurePerformanceDiagnostics\\\<verze>**. Každý, kdo má tento odkaz je schopen stáhnout soubor zip.

Chcete-li pomoci pracovníka podpory při práci na lístku podpory, může společnost Microsoft použít tento odkaz SAS ke stažení diagnostických dat.

Chcete-li sestavu zobrazit, extrahujte soubor zip a otevřete soubor **PerfInsights Report.html.**

Měli byste být také schopni stáhnout soubor zip přímo z portálu výběrem rozšíření.

![Snímek obrazovky s podrobným stavem Diagnostiky výkonu](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Odkaz SAS zobrazený na portálu nemusí někdy fungovat. To může být způsobeno poškozeným url během operací kódování a dekódování. Místo toho můžete získat odkaz přímo ze souboru *_saslink.txt z virtuálního soudu.

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

- Stav nasazení rozšíření (v oznamovací oblasti) může zobrazit "Nasazení probíhá" i v případě, že rozšíření je úspěšně zřídit.

    Tento problém lze bezpečně ignorovat, tak dlouho, dokud stav rozšíření označuje, že rozšíření je úspěšně zřízena.
- Některé problémy můžete vyřešit během instalace pomocí protokolů rozšíření. Výstup spuštění rozšíření je zaznamenán do souborů nalezených v následujícím adresáři:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte Získat **podporu**. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
