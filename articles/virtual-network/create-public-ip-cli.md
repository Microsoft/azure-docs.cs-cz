---
title: Vytvoření veřejné IP adresy – Azure CLI
description: Naučte se vytvářet veřejné IP adresy pomocí Azure CLI.
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 4d0934dd30f9738e2a67d4aff23c96a48aaaa61b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89302003"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Rychlý Start: vytvoření veřejné IP adresy pomocí Azure CLI

V tomto článku se dozvíte, jak vytvořit prostředek veřejné IP adresy pomocí Azure CLI. Další informace o tom, k jakým prostředkům může být přidružen, rozdíl mezi základní a standardní SKU a dalšími souvisejícími informacemi, najdete v tématu [veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  V tomto příkladu se zaměříme jenom na adresy IPv4. Další informace o adresách IPv6 najdete v tématu [IPv6 pro virtuální síť Azure](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

## <a name="prerequisites"></a>Požadavky

- Rozhraní příkazového řádku Azure je nainstalované místně nebo Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) s názvem **myResourceGroup** v umístění **eastus2** .

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```
---
# <a name="standard-sku---using-zones"></a>[**Standardní SKU – používání zón**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Následující příkaz funguje pro rozhraní API verze 2020-08-01 nebo novější.  Další informace o aktuálně používané verzi rozhraní API najdete v tématu [poskytovatelé a typy prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Pomocí [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) vytvoříte standardní zónu – redundantní veřejnou IP adresu s názvem **myStandardZRPublicIP** v **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> U verzí rozhraní API starších než 2020-08-01 spusťte výše uvedený příkaz bez zadání parametru zóny pro vytvoření redundantní IP adresy zóny. 
>

Pokud chcete vytvořit veřejnou IP adresu Standard Zona v Zóna 2 s názvem **myStandardZonalPublicIP** v **myResourceGroup**, použijte následující příkaz:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Všimněte si, že výše uvedené možnosti pro zóny jsou pouze výběr platný v oblastech s [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**Standardní SKU – žádné zóny**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Následující příkaz funguje pro rozhraní API verze 2020-08-01 nebo novější.  Další informace o aktuálně používané verzi rozhraní API najdete v tématu [poskytovatelé a typy prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Pomocí [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) vytvořte standardní veřejnou IP adresu jako prostředek mimo oblast s názvem **myStandardPublicIP** v **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Tento výběr je platný ve všech oblastech a je výchozí volbou pro standardní veřejné IP adresy v oblastech bez [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-create-public-ip-basic)

Použijte [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) k vytvoření základní statické veřejné IP adresy s názvem **myBasicPublicIP** v **myResourceGroup**.  Základní veřejné IP adresy nemají koncept zón dostupnosti.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard
    --allocation-method Static
```
Pokud je přijatelné, aby se IP adresa měnila v čase, je možné vybrat **dynamické** přiřazení IP adresy změnou metody alokace na Dynamic.

---

## <a name="additional-information"></a>Další informace 

Další podrobnosti o jednotlivých proměnných uvedených výše najdete v tématu [Správa veřejných IP adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Další kroky
- Přidružte [veřejnou IP adresu k virtuálnímu počítači](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal).
- Přečtěte si další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).