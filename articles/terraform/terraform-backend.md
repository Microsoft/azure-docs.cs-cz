---
title: Použití služby Azure Storage jako back-end Terraformu
description: Úvod k ukládání stavu Terraformu v Azure Storage.
services: terraform
author: neilpeterson
ms.service: terraform
ms.topic: article
ms.date: 09/13/2018
ms.author: nepeters
ms.openlocfilehash: 2bee9f73f430e18fe159eed142b265cc1934860e
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984962"
---
# <a name="store-terraform-state-in-azure-storage"></a>Stav Terraformu Store ve službě Azure Storage

Terraform stav se používá ke sjednocení nasazených prostředků s využitím Terraformu konfiguracemi. Použití stavu, Terraform ví, které prostředky Azure chcete přidat, aktualizovat nebo odstranit. Ve výchozím nastavení, Terraform stav je uložený místně při spuštění *použijte Terraform*. Tato konfigurace není ideální z několika důvodů:

- Stav místní nefunguje dobře v týmu nebo prostředí pro spolupráci
- Terraform stavu může obsahovat citlivé informace
- Ukládání stavu místně se zvyšuje riziko neúmyslných odstranění

Terraform zahrnuje koncept stavu back-endu, což je vzdálené úložiště pro stav Terraformu. Při použití back-endu. stav, stav soubor je uložen v úložišti dat, jako je Azure Storage. Tento dokument podrobně popisuje, jak nakonfigurovat a používat služby Azure Storage jako Terraformu stavu back-endu.

## <a name="configure-storage-account"></a>Konfigurace účtu úložiště

Než začnete používat Azure Storage jako back-endu, musí být vytvořený účet úložiště. Účet úložiště může být vytvořen pomocí na webu Azure portal, Powershellu, rozhraní příkazového řádku Azure nebo Terraformu, samotného. Jak nakonfigurovat účet úložiště pomocí Azure CLI, použijte následující ukázku.

```azurecli-interactive
#!/bin/bash

RESOURCE_GROUP_NAME=tfstatestorage
STORAGE_ACCOUNT_NAME=tfstatestorage$RANDOM
CONTAINER_NAME=tfstatestorage

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

Poznamenejte si název účtu úložiště, název kontejneru a přístupový klíč k úložišti. Tyto hodnoty jsou potřeba při konfiguraci vzdáleného stavu.

## <a name="configure-state-backend"></a>Konfigurace stavu back-endu

Při spouštění se nakonfigurovalo Terraformu stavu back-endu *Terraformu init*. Aby bylo možné konfigurovat stav back-endu, jsou vyžadována následující data.

- název_účtu_úložiště – název účtu služby Azure Storage.
- container_name – název kontejneru objektů blob.
- klíč – název stavu uložit soubor, který se má vytvořit.
- access_key - přístupový klíč úložiště.

Každá z těchto hodnot se dá nastavit v konfiguračním souboru Terraformu nebo na příkazovém řádku, ale doporučuje se použít pro proměnné prostředí `access_key`. Použití proměnné prostředí zabrání zapisovaná klíč na disk.

Vytvořte proměnnou prostředí s názvem `ARM_ACCESS_KEY` s hodnotou přístupový klíč Azure Storage.

```console
export ARM_ACCESS_KEY=<storage access key>
```

Dalším stupněm ochrany přístupový klíč účtu úložiště Azure, ukládat ve službě Azure Key Vault. Proměnná prostředí pak dá nastavit pomocí příkazu, který je podobný následujícímu. Další informace o službě Azure Key Vault najdete v tématu [dokumentace ke službě Azure Key Vault][azure-key-vault].

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Nakonfigurujte Terraform používat back-end, zahrňte *back-endu* konfigurace s typem *azurerm* uvnitř konfigurace Terraformu. Přidat *název_účtu_úložiště*, *container_name*, a *klíč* hodnoty konfigurace bloku.

Následující příklad nakonfiguruje Terraformu back-endu. a vytvoří a skupinu prostředků Azure.

```json
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate"
    container_name        = "tfstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Teď se inicializovat konfiguraci s *Terraformu init* a pak spusťte konfigurací s *použijte Terraform*. Po dokončení najdete v Azure Storage Blob do souboru stavu.

## <a name="state-locking"></a>Zamykání stavu

Při použití Azure Storage Blob pro úložiště stavu, objekt blob automatickým zamčením před jakoukoli operaci, která zapíše stavu. Tato konfigurace zabraňuje více souběžná stát operací, které mohou způsobit poškození. Další informace najdete v tématu [zamykání stavu] [ terraform-state-lock] o Terraformu dokumentaci.

Zámek uvidíte při zkoumání objektu blob i když na webu Azure portal nebo jiné nástroje pro správu Azure.

![Azure blob se zámek](media/terraform-backend/lock.png)

## <a name="next-steps"></a>Další postup

Další informace o konfiguraci Terraformu vztahuje na [dokumentace ke službě back-endu Terraformu][terraform-backend].

<!-- LINKS - external -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
