---
title: Azure Policy rozšíření konfigurace hosta
description: Přečtěte si o rozšíření, které se používá k auditování/konfiguraci nastavení v rámci virtuálních počítačů.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.openlocfilehash: 2fda3cc2cf9adc3a734780209a0c9cc06a04e7cf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368598"
---
# <a name="overview-of-the-azure-policy-guest-configuration-extension"></a>Přehled rozšíření konfigurace hosta Azure Policy

Rozšíření konfigurace hosta je součást Azure Policy, která provádí operace auditu a konfigurace v rámci virtuálních počítačů.
Zásady, jako jsou základní definice zabezpečení pro [Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) a [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) , nemůžou kontrolovat nastavení v počítačích, dokud se rozšíření nenainstaluje.

## <a name="prerequisites"></a>Požadavky

Aby se počítač mohl ověřit ve službě konfigurace hosta, musí mít počítač [spravovanou identitu přiřazenou systémem](../../active-directory/managed-identities-azure-resources/overview.md).
Požadavek identity na virtuálním počítači se splní, pokud je nastavená následující vlastnost.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>Operační systémy

Podpora rozšíření konfigurace hosta je stejná jako popsaná podpora operačního systému [pro koncové řešení](../../governance/policy/concepts/guest-configuration.md#supported-client-types).

### <a name="internet-connectivity"></a>Připojení k internetu

Agent instalovaný rozšířením konfigurace hosta musí mít přístup k balíčkům obsahu, které jsou uvedeny v přiřazení konfigurace hosta, a nahlásit stav službě konfigurace hosta.
Počítač se může připojit pomocí odchozího HTTPS přes port TCP 443, nebo pokud je připojení zajištěné prostřednictvím privátních sítí.
Další informace o privátních sítích najdete v následujících článcích:

- [Konfigurace hostů, komunikace prostřednictvím privátního propojení v Azure](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Použití privátních koncových bodů pro Azure Storage](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Jak můžu nainstalovat rozšíření?

Pokud chcete nasadit nejnovější verzi rozšíření ve velkém měřítku, včetně požadavků na identitu, přiřaďte Azure Policy, [Nasaďte požadavky pro povolení zásad konfigurace hostů na virtuálních počítačích](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json).
U jednotlivých počítačů se dá rozšíření nasadit pomocí Azure CLI, PowerShellu, Správce prostředků šablon nebo nástrojů třetích stran.

Název instance rozšíření musí být nastaven na "AzurePolicyforWindows" nebo "AzurePolicyforLinux", protože zásady odkazované výše vyžadují tyto konkrétní řetězce.

Ve výchozím nastavení se všechna nasazení aktualizují na nejnovější verzi. Pokud není uvedeno jinak, hodnota vlastnosti _autoUpgradeMinorVersion_ se nastaví na hodnotu true. Při vydávání nových verzí rozšíření nemusíte dělat starosti s aktualizací kódu.

### <a name="azure-cli"></a>Azure CLI

Nasazení rozšíření pro Linux:


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

Nasazení rozšíření pro Windows:

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

Nasazení rozšíření pro Linux:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

Nasazení rozšíření pro Windows:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Nasazení rozšíření pro Linux:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Nasazení rozšíření pro Windows:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

Nasazení rozšíření pro Linux:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforLinux"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforLinux"
  type_handler_version  = "1.0"
}
```

Nasazení rozšíření pro Windows:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforWindows"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforWindows"
  type_handler_version  = "1.0"
}
```

## <a name="settings"></a>Nastavení

V rozšíření není nutné zahrnout žádná nastavení ani vlastnosti chráněného nastavení.
Všechny tyto informace načte Agent z prostředků [přiřazení konfigurace hostů](/rest/api/guestconfiguration/guestconfigurationassignments) . Například vlastnosti [ConfigurationUri](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation), [Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode)a [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) jsou jednotlivé konfigurace spravované v jednotlivých KONFIGURACÍCH, nikoli na rozšíření virtuálního počítače.

## <a name="next-steps"></a>Další kroky

* Další informace o Azure Policy konfiguraci hostů najdete v tématu [vysvětlení konfigurace hostů Azure Policy](../../governance/policy/concepts/guest-configuration.md) .
* Další informace o fungování agenta a rozšíření pro Linux najdete v tématu [rozšíření a funkce virtuálních počítačů Azure pro Linux](features-linux.md).
* Další informace o tom, jak funguje Agent hosta a rozšíření systému Windows, najdete v tématu [rozšíření a funkce virtuálních počítačů Azure pro Windows](features-windows.md).  
* Informace o instalaci agenta hosta systému Windows najdete v tématu [Přehled agenta virtuálního počítače Azure s Windows](agent-windows.md).  
* Informace o instalaci agenta pro Linux najdete v tématu [Přehled agenta virtuálních počítačů Azure Linux](agent-linux.md).  
