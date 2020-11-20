---
title: Povolení rozšíření virtuálního počítače pomocí rozhraní příkazového řádku Azure
description: Tento článek popisuje, jak nasadit rozšíření virtuálních počítačů na servery s podporou ARC Azure běžícími v hybridních cloudových prostředích pomocí Azure CLI.
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3fa8273b15518c182aefa038e67d85773d500b30
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991447"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Povolení rozšíření virtuálních počítačů Azure pomocí Azure CLI

V tomto článku se dozvíte, jak nasadit a odinstalovat rozšíření virtuálního počítače, které podporují servery s podporou ARC Azure, na hybridní počítač se systémem Linux nebo Windows pomocí Azure CLI.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Příkazy ConnectedMachine se neodesílají jako součást rozhraní příkazového řádku Azure CLI. Před použitím rozhraní příkazového řádku Azure ke správě rozšíření virtuálních počítačů na hybridním serveru spravovaném servery s podporou ARC musíte načíst rozšíření ConnectedMachine. Spusťte následující příkaz, který načte:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Povolit rozšíření

Pokud chcete na serveru s povoleným ARC povolit rozšíření virtuálního počítače, použijte příkaz [AZ connectedmachine Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) s `--machine-name` `--extension-name` parametry,, `--location` , `--type` , `settings` a `--publisher` .

Následující příklad povolí Log Analytics rozšíření virtuálního počítače na serveru Linux s povoleným ARC:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Následující příklad povoluje rozšíření vlastních skriptů na serveru s povoleným ARC:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Následující příklad povoluje rozšíření Key Vault VM (Preview) na serveru s povoleným ARC:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Nainstalovaná rozšíření seznamu

Pokud chcete získat seznam rozšíření virtuálních počítačů na serveru s povoleným ARC, použijte příkaz [AZ connectedmachine Extension List](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) s `--machine-name` `--resource-group` parametry a.

Příklad:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Ve výchozím nastavení je výstup příkazů Azure CLI ve formátu JSON (JavaScript Object Notation). Chcete-li změnit výchozí výstup na seznam nebo tabulku, použijte například [AZ Configure--Output](/cli/azure/reference-index). Můžete také přidat `--output` do libovolného příkazu pro jednorázovou změnu ve formátu výstupu.

Následující příklad ukazuje částečný výstup JSON z `az connectedmachine extension -list` příkazu:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Odebrání nainstalovaného rozšíření

Pokud chcete na serveru s povoleným obloukem odebrat nainstalované rozšíření virtuálního počítače, použijte příkaz [AZ connectedmachine Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) s `--extension-name` `--machine-name` `--resource-group` parametry a.

Chcete-li například odebrat rozšíření virtuálního počítače Log Analytics pro Linux, spusťte následující příkaz:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

- Rozšíření virtuálních počítačů můžete nasadit, spravovat a odebírat pomocí [Azure PowerShell](manage-vm-extensions-powershell.md), ze šablon [Azure Portal](manage-vm-extensions-portal.md)nebo [Azure Resource Manager](manage-vm-extensions-template.md).

- Informace o řešení potíží najdete v [Průvodci pro řešení potíží s rozšířeními virtuálních počítačů](troubleshoot-vm-extensions.md).

- Další informace o příkazech najdete v článku [Přehled](/cli/azure/ext/connectedmachine/connectedmachine/extension) rozšíření virtuálních počítačů Azure CLI.
