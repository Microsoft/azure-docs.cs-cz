---
title: 'Rychlý start: Nastavení a načtení tajného kódu ze služby Azure Key Vault'
description: Rychlý start ukazující nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 8559e0adf1adf549d5bf3933dbb243c02a6d3ad9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87494600"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí Azure CLI

V tomto rychlém startu vytvoříte Trezor klíčů v Azure Key Vault pomocí Azure CLI. Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md). Azure CLI slouží k vytváření a správě prostředků Azure pomocí příkazů nebo skriptů. Po jeho vytvoření uložíte tajný klíč.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

Další informace o možnostech přihlášení prostřednictvím rozhraní příkazového řádku si můžete prohlédnout v části [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

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

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, stačí provést několik dalších kroků. Toto heslo může používat aplikace. Heslo se bude jmenovat jako **ExamplePassword** a uloží do něj hodnotu **hVFkk965BuUv** .

Zadáním následujících příkazů vytvořte tajný klíč v Key Vault s názvem **ExamplePassword** , který bude ukládat hodnotu **hVFkk965BuUv** :

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "hVFkk965BuUv"
```

Nyní na toto heslo, které jste přidali do služby Azure Key Vault, můžete odkazovat pomocí jeho identifikátoru URI. Použijte **https://Contoso-Vault2.vault.azure.net/secrets/ExamplePassword** k získání aktuální verze. 

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

Právě jste vytvořili službu Key Vault, uložili jste tajný klíč a načetli jste ho.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group). Prostředky můžete odstranit následujícím způsobem:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a uložili do něj tajný klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Podívejte se na referenční informace pro [Azure CLI AZ klíčů trezor](/cli/azure/keyvault?view=azure-cli-latest) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)
