---
title: Kurz – uložení stavu Terraformu do Azure Storage
description: Úvod k ukládání stavu Terraformu do Azure Storage.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: cb85897e0a7d281eca4ad3f42e8ef28c9e3fdb7b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833532"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Kurz: uložení stavu Terraformu do Azure Storage

Stav terraformu se používá pro sjednocení nasazených prostředků s konfiguracemi Terraformu. Stav umožňuje Terraformu vědět, jaké prostředky Azure mají přidat, aktualizovat nebo odstranit. Ve výchozím nastavení je stav Terraformu uložen místně při spuštění příkazu `terraform apply`. Tato konfigurace není ideální z následujících důvodů:

- Místní stav nefunguje dobře v týmu nebo spolupráci v prostředí.
- Terraformu stav může zahrnovat citlivé informace.
- Stav ukládání místně zvyšuje pravděpodobnost nechtěného odstranění.

Terraformu podporuje zachování stavu ve vzdáleném úložišti. Jeden takový podporovaný back-end je Azure Storage. Tento dokument ukazuje, jak nakonfigurovat a používat Azure Storage pro tento účel.

## <a name="configure-storage-account"></a>Konfigurace účtu úložiště

Před použitím Azure Storage jako back-endu je potřeba vytvořit účet úložiště. Účet úložiště se dá vytvořit pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo samotného Terraformu. Pomocí následující ukázky můžete nakonfigurovat účet úložiště pomocí Azure CLI.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Poznamenejte si název účtu úložiště, název kontejneru a přístupový klíč úložiště. Tyto hodnoty jsou potřeba při konfiguraci vzdáleného stavu.

## <a name="configure-state-backend"></a>Konfigurace stavu back-endu

Back-end stavu Terraformu je nakonfigurován při spuštění příkazu `terraform init`. ke konfiguraci stavu back-endu se vyžadují tato data:

- storage_account_name – název Azure Storage účtu.
- container_name – název kontejneru objektů BLOB.
- klíč – název souboru úložiště stavu, který se má vytvořit.
- access_key – přístupový klíč k úložišti

Každá z těchto hodnot se dá zadat v konfiguračním souboru Terraformu nebo na příkazovém řádku, ale doporučuje se pro `access_key`použít proměnnou prostředí. Použití proměnné prostředí zabrání v zápisu klíče na disk.

Vytvořte proměnnou prostředí s názvem `ARM_ACCESS_KEY` s hodnotou přístupového klíče Azure Storage.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Pokud chcete dál chránit přístupový klíč účtu Azure Storage, uložte ho do Azure Key Vault. Proměnnou prostředí lze následně nastavit pomocí příkazu podobného následujícímu. Další informace o Azure Key Vault najdete v dokumentaci k [Azure Key Vault](../key-vault/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Pokud chcete nakonfigurovat Terraformu pro použití back-endu, je potřeba provést tyto kroky:
- Zahrňte `backend` konfigurační blok s typem `azurerm`.
- Přidejte `storage_account_name`ovou hodnotu do konfiguračního bloku.
- Přidejte `container_name`ovou hodnotu do konfiguračního bloku.
- Přidejte `key`ovou hodnotu do konfiguračního bloku.

Následující příklad nakonfiguruje back-end Terraformu a vytvoří skupinu prostředků Azure.

```hcl
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Inicializujte konfiguraci pomocí následujících kroků:

1. Spusťte příkaz `terraform init`.
1. Spusťte příkaz `terraform apply`.

Stavový soubor teď můžete najít v Azure Storage Blob.

## <a name="state-locking"></a>Uzamykání stavu

Objekty bloby Azure Storage jsou automaticky uzamčené před všemi operacemi, které zapisují stav. Tento model zabraňuje souběžným stavovým operacím, což může způsobit poškození. 

Další informace najdete v tématu [uzamykání stavu](https://www.terraform.io/docs/state/locking.html) v dokumentaci k terraformu.

Zámek se dá zobrazit při prozkoumávání objektu BLOB prostřednictvím Azure Portal nebo jiných nástrojů pro správu Azure.

![Objekt blob Azure s zámkem](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Data uložená v objektu blob Azure se před trvalým zašifrováním šifrují. V případě potřeby Terraformu načte stav z back-endu a uloží ho do místní paměti. Při použití tohoto modelu se stav nikdy nezapíše na místní disk.

Další informace o šifrování Azure Storage najdete v části [Azure Storage šifrování služby pro](../storage/common/storage-service-encryption.md)neaktivní neaktivní data.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Další informace o používání Terraformu v Azure](/azure/terraform)