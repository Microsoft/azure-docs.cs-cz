---
title: Kontrola šablon pro Azure Stack pomocí nástroje pro ověření šablony | Dokumentace Microsoftu
description: Kontrola šablon pro nasazení do služby Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 6bf84d7ecf2d436bdc00839699b150466b9de3ca
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247302"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Kontrola vašich šablon pro Azure Stack pomocí nástroje pro ověření šablony

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete použít nástroj ověření šablony a zkontrolujte, zda váš správce prostředků Azure [šablony](azure-stack-arm-templates.md) jsou připraveny k nasazení do služby Azure Stack. Nástroj ověření šablony je k dispozici jako součást nástrojů pro Azure Stack. Stáhněte si nástroje Azure Stack pomocí kroků popsaných v [nástroje stáhnout z webu GitHub](azure-stack-powershell-download.md) článku.

## <a name="overview"></a>Přehled

Ověření šablony, budete muset funkcí pro sestavení v cloudu první soubor a potom spustit nástroj ověření. Můžete používat následující moduly Powershellu z nástroje pro Azure Stack:

- V **CloudCapabilities** složky:<br>         `AzureRM.CloudCapabilities.psm1` Vytvoří soubor cloudové možnosti JSON představující služby a verze do cloudu Azure Stack.
- V **TemplateValidator** složky:<br>
`AzureRM.TemplateValidator.psm1` používá soubor JSON možnosti cloudu pro testování šablony pro nasazení ve službě Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Vytvoření souboru možnosti cloudu

Než použijete validátor šablony, spusťte **AzureRM.CloudCapabilities** modul PowerShell pro vytvoření souboru JSON.

>[!NOTE]
>Je-li aktualizovat integrovaného systému nebo přidat všechny nové služby nebo virtuální rozšíření, byste měli znovu spustit tento modul.

1. Ujistěte se, že jste připojeni ke službě Azure Stack. Tyto kroky můžete provést z hostitele služby Azure Stack development kit, nebo můžete použít [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) připojit z pracovní stanice.
2. Import **AzureRM.CloudCapabilities** modul prostředí PowerShell:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Použití `Get-CloudCapabilities` rutina pro načtení verzí služby a vytvořte soubor JSON možnosti cloudu. Pokud nezadáte **- OutputPath**, soubor AzureCloudCapabilities.Json se vytvoří v aktuálním adresáři. Použijte skutečné umístění:

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Ověření šablony

Pomocí těchto kroků můžete ověřit pomocí šablony **AzureRM.TemplateValidator** modul prostředí PowerShell. Můžete použít vlastní šablony nebo ověřit [šabloně QuickStart pro Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Import **AzureRM.TemplateValidator.psm1** modul prostředí PowerShell:

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

Šablona ověření upozornění a chyby jsou protokolovány konzoly Powershellu a soubor HTML ve zdrojovém adresáři. Následující snímek obrazovky ukazuje příklad sestavy ověření:

![Sestava ověření šablony](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parametry

Ověření šablony podporuje následující parametry.

| Parametr | Popis | Požaduje se |
| ----- | -----| ----- |
| TemplatePath | Určuje cestu k rekurzivnímu hledání šablon Azure Resource Manageru | Ano | 
| TemplatePattern | Určuje název souboru šablony tak, aby odpovídaly. | Ne |
| CapabilitiesPath | Určuje cestu k souboru JSON možnosti cloudu | Ano | 
| IncludeComputeCapabilities | Zahrnuje zkušební prostředků IaaS jako velikosti virtuálních počítačů a rozšíření virtuálních počítačů | Ne |
| IncludeStorageCapabilities | Zahrnuje zkušební prostředky úložiště, jako jsou typy SKU | Ne |
| Sestava | Určuje název generované sestavě HTML | Ne |
| Podrobný | Protokoluje chyby a upozornění do konzoly | Ne|

### <a name="examples"></a>Příklady

V tomto příkladu ověřuje všechny [šabloně QuickStart pro Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) stáhnou do místního úložiště. V příkladu také ověří rozšíření pro Azure Stack Development Kit možnosti a velikosti virtuálních počítačů:

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Další postup

- [Nasazení šablony do služby Azure Stack](azure-stack-arm-templates.md)
- [Vývoj šablon pro Azure Stack](azure-stack-develop-templates.md)
