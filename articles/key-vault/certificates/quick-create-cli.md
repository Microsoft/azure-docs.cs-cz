---
title: 'Úvodní příručka: Nastavení a načtení certifikátu z trezoru klíčů Azure'
description: Úvodní příručka ukazuje, jak nastavit a načíst certifikát z Azure Key Vault pomocí azure cli
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: d8107d26405423da6bb5d85ab79b83edce95d179
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423723"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Úvodní příručka: Nastavení a načtení certifikátu z Azure Key Vault pomocí azure cli

V tomto rychlém startu vytvoříte trezor klíčů v azure key vault u Azure Key Vault s Azure CLI. Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných klíčů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md). Azure CLI slouží k vytváření a správě prostředků Azure pomocí příkazů nebo skriptů. Jakmile to dokončíte, uložíte certifikát.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

Pokud se chcete přihlásit k Azure pomocí příkazového příkazového příkazu, můžete zadat:

```azurecli
az login
```

Další informace o možnostech přihlášení prostřednictvím příkazového příkazu k příkazu k registraci se podívejte na [přihlášení pomocí azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

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

## <a name="add-a-certificate-to-key-vault"></a>Přidání certifikátu do trezoru klíčů

Chcete-li přidat certifikát do trezoru, stačí provést několik dalších kroků. Tento certifikát může být použit aplikací. 

Zadejte níže uvedené příkazy a vytvořte certifikát podepsaný svým držitelem s výchozí zásadou nazvanou **ExampleCertificate** :

```azurecli
az keyvault certificate create --vault-name "Contoso-Vault2" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

Nyní můžete odkazovat na tento certifikát, který jste přidali do trezoru klíčů Azure pomocí jeho URI. Slouží **https://Contoso-Vault2.vault.azure.net/certificates/ExampleCertificate** k získání aktuální verze. 

Zobrazení dříve uloženého certifikátu:

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "Contoso-Vault2"
```

Nyní jste vytvořili trezor klíčů, uložili certifikát a načetli jej.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group). Prostředky můžete odstranit následujícím způsobem:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor klíčů a uložili do něj certifikát. Chcete-li se dozvědět více o trezoru klíčů a o tom, jak jej integrovat s aplikacemi, pokračujte v následujících článcích.

- Přečtěte si [přehled trezoru klíčů Azure](../general/overview.md)
- Podívejte se na odkaz na [příkazy Azure CLI az keyvault](/cli/azure/keyvault?view=azure-cli-latest)
- Kontrola [doporučených postupů azure key vaultu](../general/best-practices.md)
