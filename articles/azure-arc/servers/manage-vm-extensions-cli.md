---
title: Povolení rozšíření virtuálního počítače pomocí rozhraní příkazového řádku Azure
description: Tento článek popisuje, jak nasadit rozšíření virtuálních počítačů na servery s podporou ARC Azure běžícími v hybridních cloudových prostředích pomocí Azure CLI.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 8f09914f246635f07b3c51c682bd67591c706732
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462906"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Povolení rozšíření virtuálních počítačů Azure pomocí Azure CLI

V tomto článku se dozvíte, jak nasadit a odinstalovat rozšíření virtuálních počítačů Azure, které podporují servery s podporou ARC Azure, na hybridní počítač se systémem Linux nebo Windows pomocí Azure CLI.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="prerequisites"></a>Požadavky

[Nainstalujte rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Před použitím rozhraní příkazového řádku Azure ke správě rozšíření virtuálních počítačů na hybridním serveru, který spravuje servery s podporou ARC, je potřeba nainstalovat `ConnectedMachine` rozšíření CLI. Na serveru s povoleným ARC spusťte následující příkaz:

```azurecli
az extension add connectedmachine
```

Po dokončení instalace se vrátí následující zpráva:

`The installed extension `connectedmachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Povolit rozšíření

Pokud chcete na serveru s povoleným ARC povolit rozšíření virtuálního počítače, použijte příkaz [AZ connectedmachine Machine-Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) pomocí `--machine-name` `--extension-name` parametrů,, `--location` , `--type` , `settings` a `--publisher` .

Následující příklad povolí Log Analytics rozšíření virtuálního počítače na serveru Linux s povoleným ARC:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Následující příklad povoluje rozšíření vlastních skriptů na serveru s povoleným ARC:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

## <a name="list-extensions-installed"></a>Nainstalovaná rozšíření seznamu

Pokud chcete získat seznam rozšíření virtuálních počítačů na serveru s povoleným ARC, použijte příkaz [AZ connectedmachine Machine-Extension List](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) s `--machine-name` `--resource-group` parametry a.

Příklad:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Ve výchozím nastavení je výstup příkazů Azure CLI ve formátu JSON (JavaScript Object Notation). Chcete-li změnit výchozí výstup na seznam nebo tabulku, použijte například [AZ Configure--Output](/cli/azure/reference-index). Můžete také přidat `--output` do libovolného příkazu pro jednorázovou změnu ve formátu výstupu.

Následující příklad ukazuje částečný výstup JSON z `az connectedmachine machine-extension -list` příkazu:

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

Pokud chcete na serveru s povoleným obloukem odebrat nainstalované rozšíření virtuálního počítače, použijte příkaz [AZ connectedmachine Machine-Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) s `--extension-name` `--machine-name` `--resource-group` parametry a.

Chcete-li například odebrat rozšíření virtuálního počítače Log Analytics pro Linux, spusťte následující příkaz:

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

- Rozšíření virtuálních počítačů můžete nasadit, spravovat a odebírat pomocí [PowerShellu](manage-vm-extensions-powershell.md), z [Azure Portal](manage-vm-extensions-portal.md)nebo [šablon Azure Resource Manager](manage-vm-extensions-template.md).

- Informace o řešení potíží najdete v [Průvodci pro řešení potíží s rozšířeními virtuálních počítačů](troubleshoot-vm-extensions.md).
