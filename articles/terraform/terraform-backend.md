---
title: Použití Azure Storage jako back-endu Terraformu
description: Úvod k ukládání stavu Terraformu do Azure Storage.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: e9b447f4f4dc9d0ee090da9729e483cc17ac7c15
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169943"
---
# <a name="store-terraform-state-in-azure-storage"></a>Uložit stav Terraformu v Azure Storage

Stav terraformu se používá pro sjednocení nasazených prostředků s konfiguracemi Terraformu. Při použití State Terraformu ví, co prostředky Azure přidávají, aktualizují nebo odstraňují. Ve výchozím nastavení je stav Terraformu uložen místně při spuštění *terraformu použít*. Tato konfigurace není ideální z několika důvodů:

- Místní stav nefunguje dobře v týmu nebo spolupráci v prostředí.
- Terraformu stav může zahrnovat citlivé informace.
- Stav ukládání místně zvyšuje pravděpodobnost nechtěného odstranění.

Terraformu zahrnuje pojem stavového back-endu, který je vzdáleným úložištěm pro stav Terraformu. Při použití back-endu stavu je soubor stavu uložený v úložišti dat, jako je Azure Storage. Tento dokument popisuje, jak nakonfigurovat a používat Azure Storage jako back-end stavu Terraformu.

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

Back-end stavu Terraformu je nakonfigurován při spuštění *terraformu init*. Aby bylo možné konfigurovat stav back-endu, je třeba zadat následující data.

- storage_account_name – název účtu Azure Storage.
- container_name – název kontejneru objektů BLOB.
- klíč – název souboru úložiště stavu, který se má vytvořit.
- access_key – přístupový klíč k úložišti

Každou z těchto hodnot lze zadat v konfiguračním souboru Terraformu nebo na příkazovém řádku, ale doporučuje se použít proměnnou prostředí pro `access_key`. Použití proměnné prostředí zabrání v zápisu klíče na disk.

Vytvořte proměnnou prostředí s názvem `ARM_ACCESS_KEY` s hodnotou přístupového klíče Azure Storage.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Pokud chcete dál chránit přístupový klíč účtu Azure Storage, uložte ho do Azure Key Vault. Proměnnou prostředí lze následně nastavit pomocí příkazu podobného následujícímu. Další informace o Azure Key Vault najdete v dokumentaci k [Azure Key Vault][azure-key-vault].

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Pokud chcete nakonfigurovat Terraformu pro použití back-endu, zahrňte do konfigurace Terraformu konfiguraci *back-endu* s typem *azurerm* . Přidejte hodnoty *storage_account_name*, *container_name*a *Key* do konfiguračního bloku.

Následující příklad nakonfiguruje back-end Terraformu a vytvoří skupinu prostředků Azure. Hodnoty nahraďte hodnotami z vašeho prostředí.

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

Nyní inicializujte konfiguraci pomocí *terraformu init* a pak spusťte konfiguraci pomocí *terraformu použít*. Po dokončení můžete najít soubor stavu v Azure Storage Blob.

## <a name="state-locking"></a>Uzamykání stavu

Při použití Azure Storage Blob pro úložiště stavu je objekt BLOB automaticky uzamčen před jakoukoliv operací, která zapisuje stav. Tato konfigurace zabraňuje více souběžným stavovým operacím, což může způsobit poškození. Další informace najdete v tématu [uzamykání stavu][terraform-state-lock] v dokumentaci k terraformu.

Zámek se dá zobrazit při prozkoumávání objektu BLOB prostřednictvím Azure Portal nebo jiných nástrojů pro správu Azure.

![Objekt blob Azure s zámkem](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Ve výchozím nastavení jsou data uložená v objektu blob Azure zašifrovaná, aby se zachovala v infrastruktuře úložiště. Když Terraformu potřebuje stav, načte se z back-endu a uloží do paměti ve vývojovém systému. V této konfiguraci je stav zabezpečený Azure Storage a nezapisuje na místní disk.

Další informace o šifrování Azure Storage najdete v části [Azure Storage šifrování služby pro][azure-storage-encryption]neaktivní neaktivní data.

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci back-endu Terraformu najdete v [dokumentaci k back-endu terraformu][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
