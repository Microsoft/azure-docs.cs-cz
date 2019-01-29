---
title: Rozšíření virtuálního počítače výkonu Azure Diagnostics pro Windows | Dokumentace Microsoftu
description: Představuje rozšíření virtuálního počítače Diagnostika výkonu Azure pro Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 3430ff2b292a3e5fe675c3a5f332a12a88d4bfbf
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096785"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Rozšíření virtuálního počítače výkonu Azure Diagnostics pro Windows

Rozšíření Azure VM Diagnostika výkonu umožňuje shromažďovat diagnostická data výkonu z virtuálních počítačů Windows. Rozšíření provádí analýzu a poskytuje sestavy s výsledky a doporučení k identifikaci a řešení potíží s výkonem na virtuálním počítači. Toto rozšíření nainstaluje nástroje pro odstraňování potíží volá [PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
> Pokud chcete spustit diagnostiku na vašem virtuálním počítači z portálu Azure portal, pro který není klasický virtuální počítače, se doporučuje použít nové prostředí. Další informace najdete v tématu [výkonu diagnostiky pro virtuální počítače Azure](performance-diagnostics.md) 

## <a name="prerequisites"></a>Požadavky

Toto rozšíření lze nainstalovat na Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows serveru 2016. Můžete ho taky nainstalovat na Windows 8.1 a Windows 10.

## <a name="extension-schema"></a>Schéma rozšíření
Následující kód JSON ukazuje schématu pro rozšíření virtuálního počítače Diagnostika výkonu Azure. Toto rozšíření vyžaduje název a klíč účtu úložiště pro ukládání diagnostický výstup a sestavy. Tyto hodnoty jsou citlivé. Klíč účtu úložiště by měla být uložena v chráněných nastavení konfigurace. Azure VM nastavení rozšíření chráněná data zašifrují a je dešifrovat jenom na cílovém virtuálním počítači. Všimněte si, že **storageAccountName** a **storageAccountKey** jsou malá a velká písmena. Další požadované parametry jsou uvedeny v následující části.

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

|   **Název**   |**Hodnota / příklad**|       **Popis**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|Verze rozhraní API.
|vydavatele|Microsoft.Azure.Performance.Diagnostics|Obor názvů vydavatele pro rozšíření.
|type|AzurePerformanceDiagnostics|Typ rozšíření virtuálního počítače.
|typeHandlerVersion|1.0|Verze rozšíření obslužné rutiny.
|performanceScenario|Basic|Výkon scénář, pro které se mají zachytávat data. Platné hodnoty jsou: **základní**, **vmslow**, **azurefiles**, a **vlastní**.
|traceDurationInSeconds|300|Doba trvání trasování, pokud vyberete některou z možností trasování.
|perfCounterTrace|p|Možnost povolit trasování čítače výkonu. Platné hodnoty jsou **p** nebo prázdnou hodnotu. Pokud nechcete k zaznamenání trasování, ponechte hodnotu jako prázdný.
|networkTrace|n|Možnost povolit trasování sítě. Platné hodnoty jsou **n** nebo prázdnou hodnotu. Pokud nechcete k zaznamenání trasování, ponechte hodnotu jako prázdný.
|xperfTrace|x|Možnost povolit trasování nástroje XPerf. Platné hodnoty jsou **x** nebo prázdnou hodnotu. Pokud nechcete k zaznamenání trasování, ponechte hodnotu jako prázdný.
|storPortTrace|s|Možnost povolit StorPort trasování. Platné hodnoty jsou **s** nebo prázdnou hodnotu. Pokud nechcete k zaznamenání trasování, ponechte hodnotu jako prázdný.
|srNumber|123452016365929|Číslo lístku podpory, pokud je k dispozici. Nechte zadanou hodnotu jako prázdný, pokud není nutné.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Aktuálním datem a časem ve formátu Utc. Pokud používáte portál pro toto rozšíření nainstalovat, není potřeba zadat tuto hodnotu.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|Jedinečný identifikátor virtuálního počítače.
|storageAccountName|mystorageaccount|Název účtu úložiště pro uložení diagnostických protokolů a výsledků.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Klíč pro účet úložiště.

## <a name="install-the-extension"></a>Instalace rozšíření

Postupujte podle těchto pokynů k instalaci rozšíření na virtuálních počítačích Windows:

1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).
2. Vyberte virtuální počítač, ve které chcete nainstalovat tato rozšíření.

    ![Snímek obrazovky Azure Portalu s virtuálními počítači zvýrazněnou](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Vyberte **rozšíření** okna a vyberte **přidat**.

    ![Rozšíření – snímek obrazovky okna s přidat zvýrazněný](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Vyberte **výkonu diagnostiky Azure**, přečtěte si podmínky a ujednání a vyberte **vytvořit**.

    ![Snímek obrazovky s novou obrazovku prostředků, se zvýrazněným Diagnostika výkonu Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Zadejte hodnoty parametrů pro instalaci a vyberte **OK** nainstalovat rozšíření. Další informace o podporovaných scénářích najdete v tématu [použití PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Dialogové okno snímek obrazovky Instalace rozšíření](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Když je instalace úspěšná, zobrazí zprávu s oznámením tento stav.

    ![Snímek obrazovky zřízení proběhlo úspěšně zprávy](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Rozšíření spustí po úspěšném zřízení. Má dvě minuty nebo méně k dokončení pro základní scénáře. Pro další scénáře spustí během trvání zadaly během instalace.

## <a name="remove-the-extension"></a>Odebrat rozšíření
Odebrat rozšíření z virtuálního počítače, postupujte podle těchto kroků:

1. Přihlaste se k [webu Azure portal](http://portal.azure.com), vyberte virtuální počítač, ze kterého chcete odebrat toto rozšíření a pak vyberte **rozšíření** okno. 
2. Vyberte (**...** ) pro rozšíření diagnostiky výkonu položku ze seznamu a vyberte **odinstalovat**.

    ![Rozšíření – snímek obrazovky okna s odinstalovat zvýrazněnou](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Můžete také vybrat položku rozšíření a vyberte **odinstalovat** možnost.

## <a name="template-deployment"></a>Nasazení šablon
Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Schéma JSON, které jsou podrobně popsané v předchozí části lze použít v šabloně Azure Resource Manageru. To spustí rozšíření Azure VM Diagnostika výkonu při nasazení šablony Azure Resource Manageru. Tady je ukázka šablony:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="powershell-deployment"></a>Nasazení pomocí Powershellu
`Set-AzureRmVMExtension` Příkaz lze použít k nasazení rozšíření virtuálního počítače výkon diagnostiky Azure do existujícího virtuálního počítače.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Informace o data zaznamenaná
Nástroj PerfInsights shromáždí různé protokoly, konfigurace a diagnostických dat, v závislosti na vybraném scénáři. Další informace najdete v tématu [PerfInsights dokumentaci](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Zobrazit a sdílet výsledky

Výstup z rozšíření najdete v souboru zip, který nahráli do účtu úložiště zadaný během instalace a je sdíleno po dobu 30 dnů pomocí [sdílených přístupových podpisů (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Tento soubor zip obsahuje diagnostické protokoly a sestavy s výsledky a doporučení. Odkaz SAS k výstupnímu souboru zip najdete uvnitř textový soubor s názvem *zipfilename*_saslink.txt ve složce **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics \\ \<verze >**. Každý, kdo má tento odkaz je možné stáhnout soubor zip.

Jako pomoc pracovníka podpory pracující na vašeho lístku podpory, může Microsoft pomocí tohoto odkazu SAS ke stažení diagnostická data.

Chcete-li zobrazit sestavu, extrahování souboru zip a otevřete **PerfInsights Report.html** souboru.

Je třeba také stáhnout soubor zip přímo z portálu vyberte rozšíření.

![Snímek obrazovky diagnostiky výkonu podrobný stav](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> V některých případech nemusí fungovat odkaz SAS zobrazí na portálu. To může být způsobeno poškozenou adresu URL během operace kódování a dekódování. Místo toho můžete získat odkaz přímo ze souboru *_saslink.txt z virtuálního počítače.

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

- Stav nasazení rozšíření (v oznamovací oblasti) se můžou zobrazovat "Probíhá nasazení" i v případě, že rozšíření je úspěšně zřízený.

    Tento problém můžete ignorovat, dokud se stav rozšíření označuje, že rozšíření je úspěšně zřízený.
- Pomocí rozšíření protokoly, abyste mohli vyřešit některé problémy během instalace. Rozšíření provádění výstup je zaznamenán soubory nalezené v následujícím adresáři:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
