---
title: Používat nástroj šablony ověření ke kontrole šablony Azure zásobníku | Microsoft Docs
description: Zkontrolujte šablony pro nasazení do Azure zásobníku
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 1800db8cd25aa37bffb76adf00901e11c75d49fe
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604076"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Zkontrolujte vaše šablony zásobník Azure pomocí nástroje ověření šablony

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Nástroj ověření šablony můžete zaškrtněte, pokud správce Azure Resource Manager [šablony](azure-stack-arm-templates.md) jsou připraveny k nasazení do Azure zásobníku. Nástroj ověření šablony je k dispozici jako součást nástroje Azure zásobníku. Stažení nástroje Azure zásobníku pomocí kroků popsaných v [stáhnout z webu GitHub nástroje](azure-stack-powershell-download.md) článku.

## <a name="overview"></a>Přehled

Ověření šablony, budete muset sestavení cloudu možnosti první souboru a poté spusťte nástroj ověření. Můžete používat následující moduly Powershellu z nástroje Azure zásobníku:

- V **TemplateValidator** složky:<br>         AzureRM.CloudCapabilities.psm1 vytvoří soubor JSON cloudu možnosti představují služby a verze ve cloudu Azure zásobníku.
- V **CloudCapabilities** složky:<br>
AzureRM.TemplateValidator.psm1 používá soubor JSON možností cloudu k testování šablony pro nasazení v zásobníku Azure.

## <a name="build-the-cloud-capabilities-file"></a>Vytvoření souboru možností cloudu

Než použijete validátor šablony, spusťte modul AzureRM.CloudCapabilities PowerShell k vytvoření souboru JSON.

>[!NOTE]
>Pokud integrované systém aktualizovat nebo přidání jakékoli nové služby nebo rozšíření virtuálních, byste měli znovu spustit tohoto modulu.

1. Ujistěte se, že máte připojení k Azure zásobníku. Tyto kroky lze provést z hostitele zásobník Azure development kit, nebo můžete použít [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) pro připojení z pracovní stanice.
2. Naimportujte modul Powershellu AzureRM.CloudCapabilities:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Použijte rutinu Get-CloudCapabilities načtení verze aktualizace service a vytvořte soubor JSON možnosti cloudu. Pokud nezadáte **- OutputPath**, soubor AzureCloudCapabilities.Json je vytvořený v aktuálním adresáři. Použijte aktuálního umístění:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Ověření šablony

Tyto kroky použijte k ověření šablony s použitím modulu AzureRM.TemplateValidator PowerShell. Můžete použít vlastní šablony, nebo ověřit [šablony Azure Quickstart zásobníku](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Naimportujte modul Powershellu AzureRM.TemplateValidator.psm1:

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Spusťte validátor šablony:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Šablona ověření varování nebo chyby jsou protokolovány konzole prostředí PowerShell a soubor ve formátu HTML v zdrojový adresář. Následující snímek obrazovky ukazuje příklad sestavy ověření:

![Sestava ověření šablony](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parametry

Validátor šablony podporuje následující parametry.

| Parametr | Popis | Požaduje se |
| ----- | -----| ----- |
| TemplatePath | Určuje cestu k rekurzivnímu najít šablony Azure Resource Manager | Ano | 
| TemplatePattern | Určuje název šablony souborů tak, aby odpovídaly. | Ne |
| CapabilitiesPath | Určuje cestu k souboru JSON možnosti cloudu | Ano | 
| IncludeComputeCapabilities | Zahrnuje vyhodnocení prostředky infrastruktury jako velikosti virtuálních počítačů a rozšíření virtuálního počítače | Ne |
| IncludeStorageCapabilities | Zahrnuje vyhodnocení prostředků úložiště, jako jsou typy SKU | Ne |
| Sestava | Určuje název vygenerovanou sestavu ve formátu HTML | Ne |
| Podrobný | Protokoly chyb a varování do konzoly nástroje | Ne|

### <a name="examples"></a>Příklady

Tento příklad ověří všechny [šablony Azure Quickstart zásobníku](https://github.com/Azure/AzureStack-QuickStart-Templates) stáhne do místního úložiště. V příkladu také ověří velikostí virtuálních počítačů a rozšíření Azure zásobníku Development Kit možnostmi.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="next-steps"></a>Další postup

- [Nasazení šablon do Azure zásobníku](azure-stack-arm-templates.md)
- [Vývoj šablon pro Azure zásobníku](azure-stack-develop-templates.md)
