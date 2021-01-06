---
title: Rychlý Start – vytvoření Azure Key Vault pomocí Azure CLI
description: Rychlý Start ukazující, jak vytvořit Azure Key Vault pomocí Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e29a692e3fdad1bea7132b3bed50444c7398ba46
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936307"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Rychlý Start: vytvoření trezoru klíčů pomocí Azure CLI

Azure Key Vault je cloudová služba, která poskytuje zabezpečené úložiště klíčů, [tajných](../secrets/index.yml) [kódů](../keys/index.yml)a [certifikátů](../certificates/index.yml). Další informace o Key Vault najdete v tématu [o Azure Key Vault](overview.md). Další informace o tom, co je možné uložit v trezoru klíčů, najdete v tématu [informace o klíčích, tajných klíčích a certifikátech](about-keys-secrets-certificates.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.4 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *ContosoResourceGroup* v umístění *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů.

Ve skupině prostředků z předchozího kroku vytvořte Key Vault. Budete muset zadat několik informací:

- Název trezoru klíčů: řetězec na 3 až 24 znaků, který může obsahovat jenom číslice (0-9), písmena (A-z, A-Z) a spojovníky (-).

  > [!Important]
  > Každý Trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <název trezoru klíčů jedinečných> s názvem vašeho trezoru klíčů.

- Název skupiny prostředků: **myResourceGroup**.
- Umístění: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

Výstup této rutiny zobrazuje vlastnosti nově vytvořeného trezoru klíčů. Poznamenejte si hodnoty dvou vlastností uvedených níže:

- **Název trezoru**: název, který jste zadali do parametru--name výše.
- **Identifikátor URI trezoru**: v tomto příkladu se jedná o https://a &lt; jedinečné úložiště klíčů – název &gt; . Vault.Azure.NET/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít příkaz Azure CLI [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a odstranili jste ho. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](overview.md)
- Přečtěte si [Přehled zabezpečení Azure Key Vault](security-overview.md)
- Podívejte se na referenční informace pro [Azure CLI AZ klíčů trezor](/cli/azure/keyvault) .

