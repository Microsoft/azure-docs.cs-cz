---
title: Najít další segment s Azure sítě sledovacích procesů další segment - 2.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Tento článek popisuje, jak můžete najít, co je typ dalšího směrování a ip adresu pomocí dalšího přechodu pomocí rozhraní příkazového řádku Azure.
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: ''
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 873bd0f8c98cfa67c77841df8aa53eb2b895cd54
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2018
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-20"></a>Zjistěte, co typ dalšího směrování je pomocí funkce další směrování v sledovací proces sítě Azure pomocí Azure CLI 2.0

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Rozhraní API Azure REST](network-watcher-check-next-hop-rest.md)

Další směrování je funkce sledovací proces sítě, která poskytuje možnost get typ dalšího směrování a IP adresy, které jsou založené na zadaný virtuální počítač. Tato funkce je užitečná při určování, zda prochází odchozího provozu z virtuálního počítače brány, internet nebo virtuální sítě získat do cíle.

Tento článek používá naší nové generace rozhraní příkazového řádku pro model nasazení prostředků management, Azure CLI 2.0, která je dostupná pro Windows, Mac a Linux.

Chcete-li provést kroky v tomto článku, je potřeba [nainstalovat rozhraní příkazového řádku Azure pro Mac, Linux a Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Než začnete

V tomto scénáři budete používat rozhraní příkazového řádku Azure se najít typ dalšího směrování a IP adresu.

Tento scénář předpokládá, že už jste udělali kroky v [vytvořit sledovací proces sítě](network-watcher-create.md) vytvořit sledovací proces sítě. Tento scénář také předpokládá, že skupina prostředků se platný virtuální počítač existuje má být použit.

## <a name="scenario"></a>Scénář

Scénář popsaná v tomto článku používá další směrování, funkce sledovací proces sítě, který vyhledá typ dalšího směrování a IP adresu pro prostředek. Další informace o další směrování, navštivte [další směrování přehled](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Získat další směrování

Chcete-li získat další segment říkáme `az network watcher show-next-hop` rutiny. Jsme předat rutinu skupině prostředků sledovací proces sítě, NetworkWatcher, virtuální počítač Id, zdrojové IP adresy a cílové IP adresy. V tomto příkladu je cílovou IP adresu pro virtuální počítač v jiné virtuální síti. Mezi dvě virtuální sítě je bránu virtuální sítě.

Pokud nebyly dosud, nainstalovat a nakonfigurovat nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure účet pomocí [az přihlášení](/cli/azure/reference-index#az_login). Spusťte následující příkaz:

```azurecli
az network watcher show-next-hop --resource-group <resourcegroupName> --vm <vmNameorID> --source-ip <source-ip> --dest-ip <destination-ip>

```

> [!NOTE]
Pokud virtuální počítač má několik síťových adaptérů a předávání IP je povolena na žádném síťové karty, pak parametr síťový adaptér (-i síťový adaptér id) musí být zadán. V opačném případě je volitelný.

## <a name="review-results"></a>Kontrola výsledků

Po dokončení, jsou uvedené výsledky. IP adresa dalšího směrování je vrácen a také typ prostředku, který je.

```azurecli
{
    "nextHopIpAddress": null,
    "nextHopType": "Internet",
    "routeTableId": "System Route"
}
```

V následujícím seznamu jsou aktuálně dostupné hodnoty NextHopType:

**Typ dalšího směrování**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Žádný

## <a name="next-steps"></a>Další postup

Zjistěte, jak zkontrolovat nastavení skupiny zabezpečení sítě prostřednictvím kódu programu, navštivte stránky [NSG auditování s sledovací proces sítě](network-watcher-nsg-auditing-powershell.md)
