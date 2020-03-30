---
title: Kontrola využití prostředků Azure podle limitů | Dokumenty společnosti Microsoft
description: Přečtěte si, jak zkontrolovat využití prostředků Azure v porovnání s limity předplatného Azure.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: cffa5677c5531f3887639c049998523d7d07586a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455558"
---
# <a name="check-resource-usage-against-limits"></a>Kontrola využití prostředků podle limitů

V tomto článku se dozvíte, jak zobrazit počet jednotlivých typů síťových prostředků, které jste nasadili v předplatném a jaké jsou [limity předplatného.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) Možnost zobrazit využití prostředků proti omezení je užitečné sledovat aktuální využití a plánovat pro budoucí použití. Ke sledování využití můžete použít [Portál Azure](#azure-portal), [PowerShell](#powershell)nebo [Azure CLI.](#azure-cli)

## <a name="azure-portal"></a>Azure Portal

1. Přihlaste se k [portálu](https://portal.azure.com)Azure .
2. V levém horním rohu portálu Azure vyberte **Všechny služby**.
3. Do pole **Filtr** zadejte *Odběry.* Když se ve výsledcích hledání zobrazí položka **Předplatná**, klikněte na ni.
4. Vyberte název předplatného, pro které chcete zobrazit informace o využití.
5. V části **NASTAVENÍ**vyberte **Možnost Využití + kvóta**.
6. Můžete vybrat následující možnosti:
   - **Typy zdrojů**: Můžete vybrat všechny typy prostředků nebo vybrat konkrétní typy zdrojů, které chcete zobrazit.
   - **Zprostředkovatelé**: Můžete vybrat všechny zprostředkovatele prostředků nebo vybrat **Výpočetní ,** **Síť**nebo **Úložiště**.
   - **Umístění**: Můžete vybrat všechna umístění Azure nebo vybrat konkrétní umístění.
   - Můžete vybrat zobrazení všech prostředků nebo pouze prostředků, kde je nasazenalespoň jeden.

     Příklad na následujícím obrázku ukazuje všechny síťové prostředky s alespoň jedním prostředkem nasazeným v USA – východ:

       ![Zobrazit údaje o využití](./media/check-usage-against-limits/view-usage.png)

     Sloupce můžete seřadit výběrem záhlaví sloupce. Zobrazené limity jsou limity pro vaše předplatné. Pokud potřebujete zvýšit výchozí limit, vyberte **Požádat o zvýšení**, pak vyplňte a odešlete žádost o podporu. Všechny prostředky mají maximální limit uvedený v [azure limity](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Pokud je aktuální limit již na maximálním počtu, nelze jej zvýšit.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním Prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatná interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, verze 1.0.0 nebo novější. Spuštěním `Get-Module -ListAvailable Az` v počítači vyhledejte nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, musíte taky `Login-AzAccount` spustit pro přihlášení do Azure.

Zobrazení využití proti omezení s [Get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage). Následující příklad získá využití pro prostředky, kde je nasazen alespoň jeden prostředek v umístění usa – východ:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Obdržíte výstup formátován stejně jako následující příklad výstupu:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Pokud pomocí příkazů rozhraní Příkazového řádku Azure (CLI) k dokončení úloh v tomto článku, buď spustit příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento článek vyžaduje Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte taky `az login` spustit pro přihlášení do Azure.

Zobrazení využití proti omezení s [az sítě seznam-použití](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). Následující příklad získá využití prostředků v umístění východní USA:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Obdržíte výstup formátován stejně jako následující příklad výstupu:

```output
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
