---
title: Konfigurace předvolby směrování pro veřejnou IP adresu pomocí Azure CLI
titlesuffix: Azure Virtual Network
description: Naučte se vytvářet veřejné IP adresy s prioritou směrování internetových přenosů pomocí Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 39514f8a34e85525e2a8900a8e6e3cc8656fb84a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329291"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Konfigurace předvolby směrování pro veřejnou IP adresu pomocí Azure CLI

V tomto článku se dozvíte, jak nakonfigurovat předvolby směrování prostřednictvím sítě poskytovatele internetových služeb (možnost**Internet** ) pro veřejnou IP adresu pomocí Azure CLI. Po vytvoření veřejné IP adresy ji můžete přidružit k následujícím prostředkům Azure pro příchozí a odchozí provoz do Internetu:

* Virtuální počítač
* Škálovací sada virtuálních počítačů
* Azure Kubernetes Service (AKS)
* Internetový nástroj pro vyrovnávání zatížení
* Application Gateway
* Brána Azure Firewall

Ve výchozím nastavení je předvolby směrování pro veřejnou IP adresu nastavená na globální síť Microsoft pro všechny služby Azure a může být přidružená k jakékoli službě Azure.

> [!IMPORTANT]
> Předvolby směrování jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte použít Azure CLI verze 2.0.49 nebo novější. Pokud chcete najít nainstalovanou verzi, spusťte příkaz `az --version` . Informace o instalaci nebo upgradu najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) .

## <a name="register-the-feature-for-your-subscription"></a>Registrace funkce pro vaše předplatné
Funkce předvolby směrování je aktuálně ve verzi Preview. Zaregistrujte funkci pro vaše předplatné následujícím způsobem:
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Následující příklad vytvoří skupinu prostředků v **východní USA** oblasti Azure:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Vytvořte veřejnou IP adresu s preferovaným směrováním typu "Internet" pomocí příkazu [AZ Network Public-IP Create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)s následujícím formátem, jak je uvedeno níže.

Následující příkaz vytvoří novou veřejnou IP adresu s upřednostněním **internetového** směrování v oblasti **východní USA** Azure.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  V současné době předvolby směrování podporuje jenom veřejné IP adresy IPV4.

Výše vytvořenou veřejnou IP adresu můžete přidružit k virtuálnímu počítači s [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Použijte oddíl CLI na stránce kurzu: [přidružte veřejnou IP adresu k virtuálnímu počítači](associate-public-ip-address-vm.md#azure-cli) a PŘIDRUŽTE veřejnou IP adresu k vašemu virtuálnímu počítači. Můžete také přidružit veřejnou IP adresu vytvořenou výše s [Azure Load Balancer](../load-balancer/load-balancer-overview.md), a to tak, že ji přiřadíte ke konfiguraci **front-endu** nástroje pro vyrovnávání zatížení. Veřejná IP adresa slouží jako virtuální IP adresa (VIP) s vyrovnáváním zatížení.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [předvolbách směrování ve veřejných IP adresách](routing-preference-overview.md). 
- [Nakonfigurujte předvolby směrování pro virtuální počítač pomocí Azure CLI](configure-routing-preference-virtual-machine-cli.md).

