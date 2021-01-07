---
title: Vytvoření a načtení atributů klíče v Azure Key Vault – Azure CLI
description: Rychlý Start ukazující, jak nastavit a načíst klíč z Azure Key Vault pomocí Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8da5e86362f41322102c3b5316df5743e0c2458f
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976979"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Rychlý Start: nastavení a načtení klíče z Azure Key Vault pomocí rozhraní příkazového řádku Azure

V tomto rychlém startu vytvoříte Trezor klíčů v Azure Key Vault pomocí Azure CLI. Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md). Azure CLI slouží k vytváření a správě prostředků Azure pomocí příkazů nebo skriptů. Po dokončení této akce uložíte klíč.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.4 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *ContosoResourceGroup* v umístění *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

Dále ve skupině prostředků vytvořené v předchozím kroku vytvoříte službu Key Vault. Budete muset zadat několik informací:

- V tomto rychlém startu používáme **Contoso-vault2**. Pro své testování musíte zadat jedinečný název.
- Název skupiny prostředků **ContosoResourceGroup**
- Umístění **USA – východ**

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

Výstup této rutiny zobrazuje vlastnosti nově vytvořené služby Key Vault. Poznamenejte si hodnoty dvou vlastností uvedených níže:

- **Název trezoru:** V tomto příkladu je to **Contoso-Vault2**. Tento název budete používat pro další příkazy Key Vault.
- **Identifikátor URI trezoru:** V tomto příkladu je to https://contoso-vault2.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-key-to-key-vault"></a>Přidat klíč pro Key Vault

Pokud chcete do trezoru přidat klíč, stačí provést několik dalších kroků. Tento klíč může používat aplikace. 

Zadáním následujících příkazů vytvořte klíč s názvem **ExampleKey** :

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

Nyní můžete odkazovat na tento klíč, který jste přidali do Azure Key Vault pomocí jeho identifikátoru URI. K získání aktuální verze použijte ' **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey '** . 

Postup zobrazení dříve uloženého klíče:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

Nyní jste vytvořili Key Vault, uložili klíč a načetli jste ho.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group). Prostředky můžete odstranit následujícím způsobem:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a v něm jste uložili klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Podívejte se na referenční informace pro [Azure CLI AZ klíčů trezor](/cli/azure/keyvault?view=azure-cli-latest) .
- Přečtěte si [Přehled zabezpečení Key Vault](../general/security-overview.md)
