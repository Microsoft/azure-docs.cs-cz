---
title: Rychlý start Azure – Nastavení a načtení tajného klíče ze služby Key Vault pomocí Azure CLI | Microsoft Docs
description: Rychlý start ukazující nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí Azure CLI
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4acc894f-fee0-4c2f-988e-bc0eceea5eda
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/08/2019
ms.author: barclayn
ms.openlocfilehash: 9b57dfcd6ecd00f9f1bb8ec752e0996ee52305db
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159039"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí rozhraní příkazového řádku Azure

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných klíčů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o službě Key Vault najdete v tématu [Přehled](key-vault-overview.md). Azure CLI slouží k vytváření a správě prostředků Azure pomocí příkazů nebo skriptů. V tomto rychlém startu vytvoříte trezor klíčů. Po jeho vytvoření uložíte tajný klíč.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

Pro přihlášení k Azure pomocí rozhraní příkazového řádku můžete zadat:

```azurecli
az login
```

Další informace o možnostech přihlášení přes rozhraní příkazového řádku se podívejte na [Přihlaste se pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *ContosoResourceGroup* v umístění *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

Dále ve skupině prostředků vytvořené v předchozím kroku vytvoříte službu Key Vault. Budete muset zadat několik informací:

- V tomto rychlém startu používáme **Contoso-vault2**. Pro své testování musíte zadat jedinečný název.
- Název skupiny prostředků **ContosoResourceGroup**
- Umístění **Východní USA**

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

Výstup této rutiny zobrazuje vlastnosti nově vytvořené služby Key Vault. Poznamenejte si hodnoty dvou vlastností uvedených níže:

- **Název trezoru**: V tomto příkladu je to **Contoso-Vault2**. Tento název budete používat pro další příkazy Key Vault.
- **Identifikátor URI trezoru**: V tomto příkladu je to https://contoso-vault2.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, stačí provést několik dalších kroků. Toto heslo může používat aplikace. Heslo se bude nazývat **ExamplePassword** a bude uchovávat hodnotu **Pa$$w0rd**.

Zadáním následujících příkazů vytvořte ve službě Key Vault tajný klíč **ExamplePassword**, který bude uchovávat hodnotu **Pa$$w0rd**:

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "Pa$$w0rd"
```

Nyní na toto heslo, které jste přidali do služby Azure Key Vault, můžete odkazovat pomocí jeho identifikátoru URI. Aktuální verzi získáte pomocí **https://ContosoVault.vault.azure.net/secrets/ExamplePassword**. 

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

Právě jste vytvořili službu Key Vault, uložili jste tajný klíč a načetli jste ho.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#delete). Prostředky můžete odstranit následujícím způsobem:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili službu Key Vault a uložili do ní tajný klíč. Další informace o službě Key Vault a jejím použití s vašimi aplikacemi najdete v kurzu pro webové aplikace pracující se službou Key Vault.

> [!div class="nextstepaction"]
> Informace o načtení tajného kódu ze služby Key Vault z webové aplikace s využitím spravovaných identit pro prostředky Azure najdete v následujícím kurzu [Konfigurace webové aplikace Azure pro čtení tajného kódu ze služby Key Vault](quick-create-net.md).
