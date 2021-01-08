---
title: Řešení potíží s bránou virtuální sítě Azure a připojeními – Azure CLI
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak používat Azure Network Watcher řešení potíží s rozhraním příkazového řádku Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d04ba6cc5f8527f8c05fbeb323c65230447e1e2e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019699"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Řešení potíží s Virtual Network bránou a připojením pomocí Azure Network Watcher Azure CLI

> [!div class="op_single_selector"]
> - [Azure Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher poskytuje řadu možností, které se týkají porozumění síťovým prostředkům v Azure. Jednou z těchto funkcí je řešení potíží s prostředky. Řešení potíží s prostředky je možné volat prostřednictvím portálu, PowerShellu, rozhraní příkazového řádku nebo REST API. Při volání Network Watcher zkontroluje stav Virtual Network brány nebo připojení a vrátí své závěry.

K provedení kroků v tomto článku potřebujete [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři se předpokládá, že už jste postupovali podle kroků v části [vytvoření Network Watcher](network-watcher-create.md) k vytvoření Network Watcher.

Seznam podporovaných typů bran najdete v části [podporované typy bran](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Přehled

Řešení potíží s prostředky poskytuje možnost řešit problémy, které vznikají Virtual Network Branch a připojeních. Při odstraňování potíží s prostředky se protokoly dotazují a kontrolují. Po dokončení kontroly se vrátí výsledky. Požadavky na řešení potíží s prostředky jsou dlouho běžící požadavky, což může trvat několik minut, než se výsledek vrátí. Protokoly z řešení potíží se ukládají do kontejneru v účtu úložiště, který je určený.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Načtení připojení Virtual Network brány

V tomto příkladu Probíhá odstraňování potíží s prostředky v připojení. Můžete ji také předat Virtual Network bránu. Následující rutina zobrazuje seznam připojení VPN v rámci skupiny prostředků.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Jakmile budete mít název připojení, můžete spustit tento příkaz a získat jeho ID prostředku:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Řešení potíží s prostředky vrátí data o stavu prostředku, ale také uloží protokoly do účtu úložiště, který se má zkontrolovat. V tomto kroku vytvoříme účet úložiště, pokud už existuje existující účet úložiště, který můžete použít.

1. Vytvoření účtu úložiště

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Získání klíčů účtu úložiště

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Vytvoření kontejneru

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Řešení potíží se spuštěním Network Watcher prostředků

Pomocí rutiny můžete řešit problémy s prostředky `az network watcher troubleshooting` . Rutinu předáte skupinu prostředků, název Network Watcher, ID připojení, ID účtu úložiště a cestu k objektu blob, do kterého se uloží výsledky řešení potíží.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Po spuštění rutiny Network Watcher zkontroluje prostředek a ověří stav. Vrátí výsledky do prostředí a uloží protokoly výsledků v zadaném účtu úložiště.

## <a name="understanding-the-results"></a>Porozumění výsledkům

Text akce poskytuje obecné pokyny k vyřešení tohoto problému. Pokud je možné provést akci pro daný problém, je k dispozici odkaz s dalšími pokyny. V případě, že nejsou k dispozici žádné další doprovodné materiály, odpověď poskytne adresu URL pro otevření případu podpory.  Další informace o vlastnostech odpovědi a o tom, co je zahrnuto, najdete v tématu [Network Watcher řešení potíží – přehled](network-watcher-troubleshoot-overview.md)

Pokyny ke stahování souborů z účtů Azure Storage najdete v tématu [Začínáme s úložištěm objektů BLOB v Azure pomocí .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Průzkumník služby Storage se dá použít jiný nástroj. Další informace o Průzkumník služby Storage najdete na následujícím odkazu: [Průzkumník služby Storage](https://storageexplorer.com/)

## <a name="next-steps"></a>Další kroky

Pokud se změnila nastavení, která zastavují připojení k síti VPN, přečtěte si téma [Správa skupin zabezpečení](../virtual-network/manage-network-security-group.md) sítě a sledujte skupinu zabezpečení sítě a pravidla zabezpečení, která mohou být v dané otázce.