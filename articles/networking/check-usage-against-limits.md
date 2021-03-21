---
title: Kontrolovat využití prostředků Azure na základě limitů | Microsoft Docs
description: Naučte se kontrolovat využití prostředků Azure proti omezením předplatného Azure.
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
ms.openlocfilehash: 31eeb31fb78a4e9552e64121e0e85b5fd8d9b773
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210627"
---
# <a name="check-resource-usage-against-limits"></a>Kontrola využití prostředků s ohledem na limity

V tomto článku se dozvíte, jak zobrazit počet všech typů síťových prostředků, které jste nasadili ve vašem předplatném, a jaká jsou [omezení vašich předplatných](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) . Možnost Zobrazit využití prostředků proti limitům je užitečná ke sledování aktuálního využití a plánování budoucího použití. Pomocí webu [Azure Portal](#azure-portal), [PowerShellu](#powershell)nebo rozhraní příkazového [řádku Azure](#azure-cli) můžete sledovat využití.

## <a name="azure-portal"></a>Azure Portal

1. Přihlaste se k webu Azure [Portal](https://portal.azure.com).
2. V levém horním rohu Azure Portal vyberte **všechny služby**.
3. Do pole **Filtr** zadejte *odběry* . Když se ve výsledcích hledání zobrazí položka **Předplatná**, klikněte na ni.
4. Vyberte název předplatného, pro který chcete zobrazit informace o použití.
5. V části **Nastavení** vyberte **využití + kvóta**.
6. Můžete vybrat následující možnosti:
   - **Typy prostředků**: můžete vybrat všechny typy prostředků nebo vybrat konkrétní typy prostředků, které chcete zobrazit.
   - **Zprostředkovatelé**: můžete vybrat všechny poskytovatele prostředků nebo vybrat **výpočetní** prostředky, **síť** nebo **úložiště**.
   - **Umístění**: můžete vybrat všechna umístění Azure nebo vybrat konkrétní umístění.
   - Můžete vybrat možnost Zobrazit všechny prostředky nebo pouze prostředky, ve kterých je nasazen alespoň jeden prostředek.

     Příklad na následujícím obrázku zobrazuje všechny síťové prostředky s alespoň jedním nasazeným prostředkem v Východní USA:

       ![Zobrazit data o využití](./media/check-usage-against-limits/view-usage.png)

     Sloupce můžete seřadit výběrem záhlaví sloupce. Zobrazená omezení jsou omezení pro vaše předplatné. Pokud potřebujete zvýšit výchozí limit, vyberte **žádost zvýšení** a pak dokončete a odešlete žádost o podporu. Všechny prostředky mají maximální limit uvedený v [omezeních](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)Azure. Pokud je aktuální limit již na maximálním počtu, nelze tento limit zvýšit.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, verze 1.0.0 nebo novější. Pokud `Get-Module -ListAvailable Az` chcete najít nainstalovanou verzi, spusťte v počítači. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro přihlášení k Azure.

Podívejte se na použití omezení pomocí [Get-AzNetworkUsage](/powershell/module/az.network/get-aznetworkusage). Následující příklad získá využití pro prostředky, ve kterých je nasazen alespoň jeden prostředek v umístění Východní USA:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Výstup se zobrazuje stejně jako v následujícím příkladu výstupu:

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

Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento článek vyžaduje Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte spustit taky, `az login` abyste se přihlásili k Azure.

Zobrazte si použití omezení pomocí [AZ Network list-Usage](/cli/azure/network#az-network-list-usages). Následující příklad získá využití prostředků v umístění Východní USA:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Výstup se zobrazuje stejně jako v následujícím příkladu výstupu:

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