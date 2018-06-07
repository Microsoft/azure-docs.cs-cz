---
title: Kontrola využití prostředků Azure pro omezení | Microsoft Docs
description: Zjistěte, jak zkontrolovat vaše využití prostředků Azure s limity předplatného Azure.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: networking
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jdial
ms.openlocfilehash: 0c51f48576c665fbe67f2f18198d6422fe872895
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34811729"
---
# <a name="check-resource-usage-against-limits"></a>Kontrola využití prostředků omezení

V tomto článku se dozvíte, jak zjistit počet každý typ síťového prostředku, který jste nasadili ve vašem předplatném a co vaše [limity předplatného](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) jsou. Možnost zobrazení využití prostředků omezení je užitečné sledovat aktuální využití a plánování pro budoucí použití. Můžete použít [portálu Azure](#azure-portal), [prostředí PowerShell](#powershell), nebo [rozhraní příkazového řádku Azure](#azure-cli) ke sledování využití.

## <a name="azure-portal"></a>Azure Portal

1. Přihlaste se k Azure [portál](https://portal.azure.com).
2. V horní, v levém horním rohu portálu Azure, vyberte **všechny služby**.
3. Zadejte *odběry* v **filtru** pole. Když **odběry** se zobrazí ve výsledcích hledání, vyberte ho.
4. Vyberte název odběru, který chcete zobrazit informace o využití.
5. V části **nastavení**, vyberte **využití + kvóty**.
6. Můžete vybrat následující možnosti:
    - **Typy prostředků**: můžete vybrat všechny typy prostředků nebo vyberte konkrétní typy prostředků, které chcete zobrazit.
    - **Zprostředkovatelé**: můžete vybrat všech poskytovatelů prostředků nebo vyberte **výpočetní**, **sítě**, nebo **úložiště**.
    - **Umístění**: můžete vybrat všechny Azure umístění, nebo vybrat určitá umístění.
    - Můžete vybrat zobrazíte všechny prostředky nebo pouze prostředky, které se nasadí alespoň jeden.

    Příklad následující obrázek ukazuje všechny síťové prostředky s alespoň jeden prostředek nasazené ve východní USA:

        ![View usage data](./media/check-usage-against-limits/view-usage.png)

    Můžete řadit sloupce výběrem záhlaví sloupce. Mezní hodnoty uvedené jsou limity pro vaše předplatné. Pokud potřebujete zvyšte výchozí limit, vyberte **žádost o zvýšení**, dokončení a odeslat žádost o podporu. Všechny prostředky mít maximální limit uvedené v Azure [omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Pokud vaše aktuální limit je již na maximální počet, nelze zvýšit limit.

## <a name="powershell"></a>PowerShell

Můžete spouštět příkazy, které následují v [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Prostředí cloudové služby Azure je interaktivní prostředí volné. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte prostředí PowerShell z vašeho počítače, musíte *AzureRM* modul prostředí PowerShell, verze 6.0.1 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` na počítači s nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Login-AzureRmAccount` k přihlášení do Azure.

Zobrazit vaše použití omezení s [Get-AzureRmNetworkUsage](/powershell/module/azurerm.network/powershell/module/azurerm.network/get-azurermnetworkusage). Následující příklad získá využití prostředků, které se nasadí alespoň jeden prostředek v umístění východní USA:

```azurepowershell-interactive
Get-AzureRmNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Zobrazí se výstup naformátovaný stejná jako výstupu v následujícím příkladu:

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento článek vyžaduje Azure CLI verze 2.0.32 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` k přihlášení do Azure.

Zobrazit vaše použití omezení s [az sítě seznamu použití](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). Následující příklad získá využití prostředků v umístění východní USA:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Zobrazí se výstup naformátovaný stejná jako výstupu v následujícím příkladu:

```azurecli
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
