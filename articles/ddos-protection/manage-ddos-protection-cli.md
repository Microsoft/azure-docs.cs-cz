---
title: Vytvoření a konfigurace plánu Azure DDoS Protection pomocí Azure CLI
description: Naučte se vytvořit plán DDoS Protection pomocí Azure CLI.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 8a8da50dc703d59dc16b5cb6253d39aeb33fd76d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777630"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>Rychlý Start: vytvoření a konfigurace Azure DDoS Protection Standard pomocí Azure CLI

Začněte s Azure DDoS Protection Standard pomocí Azure CLI. 

Plán DDoS Protection definuje sadu virtuálních sítí, které mají povolený Standard DDoS Protection v rámci předplatných. Můžete nakonfigurovat jeden plán DDoS Protection pro vaši organizaci a propojit virtuální sítě s několika předplatnými do stejného plánu. 

V tomto rychlém startu vytvoříte plán DDoS Protection a propojíte ho s virtuální sítí. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Rozhraní příkazového řádku Azure je nainstalované místně nebo Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-ddos-protection-plan"></a>Vytvoření plánu DDoS Protection

V Azure přidělíte související prostředky skupině prostředků. Můžete použít buď existující skupinu prostředků, nebo vytvořit novou.

Chcete-li vytvořit skupinu prostředků, použijte příkaz [AZ Group Create](/cli/azure/group#az_group_create). V tomto příkladu pojmenujte naši skupinu prostředků _MyResourceGroup_ a použijeme _východní USA_ umístění:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

Teď vytvořte plán DDoS Protection s názvem _MyDdosProtectionPlan_:

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Povolení ochrany DDoS pro virtuální síť

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Povolit DDoS Protection pro novou virtuální síť

Při vytváření virtuální sítě můžete povolit DDoS Protection. V tomto příkladu pojmenujte naši virtuální síť _MyVnet_: 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

Virtuální síť nejde přesunout do jiné skupiny prostředků nebo předplatného, pokud je DDoS standard pro virtuální síť povolený. Pokud potřebujete přesunout virtuální síť se zapnutou DDoS standardem, zakažte nejprve DDoS Standard, přesuňte virtuální síť a pak povolte DDoS Standard. Po přesunu se automaticky vyladěné prahové hodnoty zásad pro všechny chráněné veřejné IP adresy ve virtuální síti resetují.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Povolit ochranu DDoS pro existující virtuální síť

Při [vytváření plánu DDoS Protection](#create-a-ddos-protection-plan)můžete k plánu přidružit jednu nebo více virtuálních sítí. Chcete-li přidat více než jednu virtuální síť, jednoduše uveďte názvy nebo ID, které jsou odděleny mezerami. V tomto příkladu přidáme _MyVnet_:

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

Alternativně můžete povolit ochranu DDoS pro danou virtuální síť:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>Ověřit a otestovat

Nejdřív si Projděte podrobnosti svého plánu DDoS Protection:

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

Ověřte, že příkaz vrací správné podrobnosti vašeho plánu DDoS Protection.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Své prostředky můžete uchovávat pro další kurz. Pokud už je nepotřebujete, odstraňte skupinu prostředků _MyResourceGroup_ . Když odstraníte skupinu prostředků, odstraní se i plán DDoS Protection a všechny související prostředky. 

Pokud chcete odstranit skupinu prostředků, použijte [AZ Group Delete](/cli/azure/group#az_group_delete):

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

Aktualizace dané virtuální sítě, aby se zakázala ochrana DDoS:

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

Pokud chcete odstranit plán DDoS Protection, musíte nejdřív z něj oddělit všechny virtuální sítě. 

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak zobrazit a nakonfigurovat telemetrii pro svůj plán DDoS Protection, přejděte k kurzům.

> [!div class="nextstepaction"]
> [Zobrazení a konfigurace telemetrie ochrany před útoky DDoS](telemetry.md)
