---
title: Poradce při potížích s bránou virtuální sítě Azure a připojeními – rozhraní příkazového příkazu Azure
titleSuffix: Azure Network Watcher
description: Tato stránka vysvětluje, jak používat Azure Network Watcher řešení potíží Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: dc0aa8e6099a7ec017aead2fe0f16e9712e17936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840719"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Poradce při potížích s bránou virtuální sítě a připojenípomocí Azure Azure Cli služby Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portál](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [ROZHRANÍ API PRO ODPOČINEK](network-watcher-troubleshoot-manage-rest.md)

Network Watcher poskytuje mnoho funkcí, pokud jde o pochopení vašich síťových prostředků v Azure. Jednou z těchto možností je řešení potíží s prostředky. Řešení potíží s prostředky lze volat prostřednictvím portálu, prostředí PowerShell, ROZHRANÍ API nebo rozhraní REST API. Při volání, Network Watcher zkontroluje stav brány virtuální sítě nebo připojení a vrátí jeho zjištění.

Chcete-li provést kroky v tomto článku, je třeba [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (CLI).](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že jste již postupovali podle kroků v [části Vytvoření sledovacího programu sítě](network-watcher-create.md) k vytvoření sledovacího programu sítě.

Seznam podporovaných typů bran naleznete na stránce [Podporované typy bran](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Přehled

Řešení potíží s prostředky poskytuje možnost řešení problémů, které vznikají s bránami virtuální sítě a připojení. Při požadavku na řešení potíží s prostředky protokoly jsou dotazovány a kontrolovány. Po dokončení kontroly jsou vráceny výsledky. Požadavky na řešení potíží s prostředky jsou dlouho běžící požadavky, které může trvat několik minut vrátit výsledek. Protokoly z řešení potíží jsou uloženy v kontejneru na účet úložiště, který je zadán.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Načtení připojení brány virtuální sítě

V tomto příkladu je v připojení spuštěno řešení potíží s prostředky. Můžete také předat virtuální síť brány. V následující rutině jsou uvedena síťvpn připojení ve skupině prostředků.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Jakmile budete mít název připojení, můžete spustit tento příkaz získat jeho ID prostředku:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Řešení potíží s prostředky vrátí data o stavu prostředku, také uloží protokoly do účtu úložiště, který má být zkontrolován. V tomto kroku vytvoříme účet úložiště, pokud existuje existující účet úložiště, můžete ho použít.

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

## <a name="run-network-watcher-resource-troubleshooting"></a>Spuštění řešení potíží s prostředky sledování sítě

Řešení potíží s `az network watcher troubleshooting` prostředky s rutinou. Přejdeme rutina skupiny prostředků, název sledovacího programu sítě, Id připojení, Id účtu úložiště a cestu k objektu blob pro uložení výsledků řešení potíží.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Po spuštění rutiny, Network Watcher zkontroluje prostředek k ověření stavu. Vrátí výsledky do prostředí a uloží protokoly výsledků v zadaném účtu úložiště.

## <a name="understanding-the-results"></a>Pochopení výsledků

Text akce obsahuje obecné pokyny, jak problém vyřešit. Pokud lze pro tento problém přijmout akci, je k dispozici odkaz s dalšími pokyny. V případě, že neexistuje žádné další pokyny, odpověď poskytuje adresu URL otevřít případ podpory.  Další informace o vlastnostech odpovědi a o tom, co je zahrnuto, naleznete v [přehledu řešení potíží s sledováním sítě.](network-watcher-troubleshoot-overview.md)

Pokyny ke stahování souborů z účtů azure storage najdete v článku [Začínáme s úložištěm objektů blob Azure pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Dalším nástrojem, který lze použít, je Průzkumník úložiště. Další informace o Průzkumníku úložiště naleznete zde na následujícím odkazu: [Průzkumník úložiště](https://storageexplorer.com/)

## <a name="next-steps"></a>Další kroky

Pokud byla změněna nastavení, která zastaví připojení k síti VPN, [přečtěte si](../virtual-network/manage-network-security-group.md) informace o sledování skupiny zabezpečení sítě a pravidel zabezpečení, která se mohou dít, v tématu Správa skupin zabezpečení sítě.
