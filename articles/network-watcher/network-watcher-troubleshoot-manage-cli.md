---
title: Řešení potíží s brány virtuální sítě Azure a připojení - rozhraní příkazového řádku Azure 2.0 | Microsoft Docs
description: Tato stránka vysvětluje, jak používat Azure sledovací proces sítě řešení potíží s Azure CLI 2.0
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 81a31365a222cde8e57258ff47d8a64af53c05c2
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli-20"></a>Řešení potíží s brány virtuální sítě a připojení pomocí Azure sítě sledovacích procesů Azure CLI 2.0

> [!div class="op_single_selector"]
> - [Azure Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Sledovací proces sítě obsahuje řadu funkcí ve vztahu k pochopení síťovým prostředkům v Azure. Jeden z těchto funkcí je prostředek řešení potíží. Řešení potíží s prostředků je možné volat prostřednictvím portálu, prostředí PowerShell, rozhraní příkazového řádku nebo REST API. Při volání, sledovací proces sítě kontroluje stav bránu virtuální sítě nebo připojení a vrátí nalezených výsledcích.

Tento článek používá naší nové generace rozhraní příkazového řádku pro model nasazení prostředků management, Azure CLI 2.0, která je dostupná pro Windows, Mac a Linux.

Chcete-li provést kroky v tomto článku, je potřeba [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Než začnete

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě.

Seznam podporovaných brány typy návštěvě [typy podporované brány](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Přehled

Řešení potíží s prostředků nabízí možnost odstraňování problémů, které nastat u brány virtuální sítě a připojení. Po odeslání žádosti k prostředku, řešení potíží s protokoly Probíhá dotaz a prověřovány. Po dokončení kontroly budou vráceny výsledky. Prostředek, který řešení problémů s požadavky jsou dlouho spuštěný žádosti, což může trvat několik minut vrácení výsledku. Protokoly z řešení potíží se ukládají v kontejneru v účtu úložiště, který je zadán.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Načtení připojení brány virtuální sítě

V tomto příkladu je se řešení potíží s prostředků spustili na připojení. Můžete také předat ji bránu virtuální sítě. Následující rutiny uvádí připojení vpn ve skupině prostředků.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Jakmile máte název připojení, můžete spustit tento příkaz získat jeho Id prostředku:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Řešení potíží s prostředků vrátí data o stavu prostředku, také ukládá protokoly na účet úložiště mají být zkontrolovány. V tomto kroku vytvoříme účet úložiště, pokud existuje stávající účet úložiště můžete ji použít.

1. Vytvořit účet úložiště

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. Získání klíče účtu úložiště

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. Vytvoření kontejneru

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Spustit Poradce při potížích sledovací proces sítě prostředků

Řešení potíží s prostředky s `az network watcher troubleshooting` rutiny. Jsme předat rutinu skupinu prostředků, název sledovací proces sítě, Id připojení, Id účtu úložiště a výsledkem cesta k objektu blob do úložiště řešení.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Po spuštění rutiny sledovací proces sítě zkontroluje prostředek ověření stavu. Se vrátí výsledky do prostředí a ukládá protokoly výsledky v zadaný účet úložiště.

## <a name="understanding-the-results"></a>Seznámení s výsledky

Text akce obsahuje obecné pokyny k vyřešení problému. Pokud může být akce pro tento problém, je k dispozici odkaz s další pokyny. V případě, že tam, kde není žádná další pokyny, odpověď obsahuje adresu url pro otevření případu podpory.  Další informace o vlastnostech odpovědi a co je součástí najdete v článku [řešení sledovací proces sítě – přehled](network-watcher-troubleshoot-overview.md)

Pokyny ke stahování souborů z účty azure storage, najdete v části [Začínáme s Azure Blob storage pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Jiný nástroj, který je možné je Storage Explorer. Další informace o Storage Explorer naleznete zde na následující odkaz: [Storage Explorer](http://storageexplorer.com/)

## <a name="next-steps"></a>Další postup

Pokud nastavení bylo změněno tohoto připojení VPN zastavit, přečtěte si téma [spravovat skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md) sledovat pravidla zabezpečení sítě skupiny a zabezpečení, které může být nejistá.
