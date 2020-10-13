---
title: Kurz – použití Azure Key Vault s virtuálním počítačem v Pythonu | Microsoft Docs
description: V tomto kurzu nakonfigurujete virtuální počítač s aplikací Python pro čtení tajného klíče z vašeho trezoru klíčů.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 8980505ac34e32a29403060a7cf3cfaec077d8af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336696"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Kurz: použití Azure Key Vault s virtuálním počítačem v Pythonu

Azure Key Vault pomáhá chránit klíče, tajné klíče a certifikáty, jako jsou klíče rozhraní API a databázové připojovací řetězce.

V tomto kurzu nastavíte aplikaci v Pythonu pro čtení informací z Azure Key Vault pomocí spravovaných identit pro prostředky Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte trezor klíčů.
> * Uložení tajného kódu v Key Vault
> * Vytvoření virtuálního počítače se systémem Azure Linux
> * Povolit [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) pro virtuální počítač
> * Udělte aplikaci konzoly požadovaná oprávnění ke čtení dat z Key Vault
> * Načtení tajného kódu z Key Vault

Než začnete, přečtěte si téma [Key Vault Basic koncepty](basic-concepts.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

Pro Windows, Mac a Linux:
  * [Git](https://git-scm.com/downloads)
  * Tento kurz vyžaduje, abyste spouštěli Azure CLI místně. Musíte mít nainstalovanou verzi Azure CLI 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Naplnění trezoru klíčů pomocí tajného klíče

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač s názvem **myVM** pomocí jedné z následujících metod:

| Linux | Windows |
|--|--|
| [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure Portal](../../virtual-machines/linux/quick-create-portal.md) | [Azure Portal](../../virtual-machines/windows/quick-create-portal.md) |

Pokud chcete vytvořit virtuální počítač se systémem Linux pomocí Azure CLI, použijte příkaz [AZ VM Create](/cli/azure/vm) .  Následující příklad přidá uživatelský účet s názvem *azureuser*. `--generate-ssh-keys`Parametr slouží k automatickému generování klíče SSH a jeho vložení do výchozího umístění klíče (*~/.ssh*). 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Všimněte si hodnoty `publicIpAddress` ve výstupu.

## <a name="assign-an-identity-to-the-vm"></a>Přiřazení identity k virtuálnímu počítači

Vytvořte pro virtuální počítač identitu přiřazenou systémem pomocí příkazu Azure CLI [AZ VM identity Assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) :

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Poznamenejte si identitu přiřazenou systémem, která se zobrazí v následujícím kódu. Výstup předchozího příkazu by byl: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Přiřazení oprávnění k identitě virtuálního počítače

Nyní můžete k trezoru klíčů přiřadit dříve vytvořená oprávnění identity spuštěním následujícího příkazu:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Přihlaste se k virtuálnímu počítači.

Pokud se chcete přihlásit k virtuálnímu počítači, postupujte podle pokynů v tématu [připojení a přihlášení k virtuálnímu počítači Azure se systémem Linux](../../virtual-machines/linux/login-using-aad.md) nebo se [připojte k virtuálnímu počítači Azure s Windows a přihlaste se](../../virtual-machines/windows/connect-logon.md).


Pokud se chcete přihlásit k virtuálnímu počítači se systémem Linux, můžete použít příkaz SSH s " <publicIpAddress> " uvedeným v kroku [Vytvoření virtuálního počítače](#create-a-virtual-machine) :

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Nainstalovat knihovny Pythonu na virtuálním počítači

Na virtuálním počítači nainstalujte dvě knihovny Pythonu, které budeme používat v našem skriptu Pythonu: `azure-keyvault-secrets` a `azure.identity` .  

V případě virtuálního počítače se systémem Linux můžete tyto součásti nainstalovat pomocí nástroje `pip3` :

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>Vytvoření a úprava ukázkového skriptu v jazyce Python

Na virtuálním počítači vytvořte soubor Pythonu s názvem **Sample.py**. Upravte soubor tak, aby obsahoval následující kód, a nahraďte řetězec "<jedinečného klíče-trezoru>" názvem vašeho trezoru klíčů:

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>Spuštění ukázkové aplikace v Pythonu

Nakonec spusťte **Sample.py**. Pokud vše vypadá dobře, měla by vrátit hodnotu vašeho tajného kódu:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte virtuální počítač a trezor klíčů.  To můžete rychle provést pouhým odstraněním skupiny prostředků, do které patří:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Další kroky

[Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
