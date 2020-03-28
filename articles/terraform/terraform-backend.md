---
title: Kurz – ukládání stavu Terraform v úložišti Azure Storage
description: Úvod do ukládání terraform stavu ve službě Azure Storage.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 1cc475e5070b21a7ea96585f2183c07d258acdc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75708420"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Kurz: Ukládání stavu Terraform v úložišti Azure Storage

Terraform state se používá k odsouhlasení nasazených prostředků s konfiguracemi Terraform. Stav umožňuje Terraform vědět, jaké prostředky Azure přidat, aktualizovat nebo odstranit. Ve výchozím nastavení je stav Terraform uložen `terraform apply` místně při spuštění příkazu. Tato konfigurace není ideální z následujících důvodů:

- Místní stav nefunguje dobře v týmu nebo prostředí spolupráce.
- Terraform stav může obsahovat citlivé informace.
- Ukládání stavu místně zvyšuje pravděpodobnost neúmyslného odstranění.

Terraform podporuje zachování stavu ve vzdáleném úložišti. Jeden takový podporovaný back-end je Azure Storage. Tento dokument ukazuje, jak nakonfigurovat a používat Azure Storage pro tento účel.

## <a name="configure-storage-account"></a>Konfigurace účtu úložiště

Než použijete Azure Storage jako back-end, musíte vytvořit účet úložiště. Účet úložiště můžete vytvořit pomocí portálu Azure, PowerShell, Azure CLI nebo Terraform sám. Následující ukázka slouží ke konfiguraci účtu úložiště pomocí rozhraní příkazového příkazu k řešení Azure.

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

Poznamenejte si název účtu úložiště, název kontejneru a přístupový klíč úložiště. Tyto hodnoty jsou potřebné při konfiguraci vzdáleného stavu.

## <a name="configure-state-back-end"></a>Konfigurace back-endu stavu

Back-end stavu Terraform je nakonfigurován při spuštění příkazu. `terraform init` Ke konfiguraci back-endu stavu jsou potřeba následující data:

- **storage_account_name**: Název účtu Azure Storage.
- **container_name**: Název kontejneru objektů blob.
- **klíč**: Název souboru úložiště stavu, který má být vytvořen.
- **access_key**: Přístupový klíč úložiště.

Každá z těchto hodnot může být zadána v konfiguračním souboru Terraform nebo na příkazovém řádku. Doporučujeme použít proměnnou prostředí `access_key` pro hodnotu. Použití proměnné prostředí zabraňuje zápisu klíče na disk.

Vytvořte proměnnou `ARM_ACCESS_KEY` prostředí s názvem s hodnotou přístupového klíče Úložiště Azure.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Chcete-li dále chránit přístupový klíč účtu Azure Storage, uložte ho do azure key vaultu. Proměnnou prostředí lze potom nastavit pomocí příkazu podobného následujícímu. Další informace o Azure Key Vault najdete v [dokumentaci k úložišti klíčů Azure](../key-vault/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Chcete-li nakonfigurovat Terraform tak, aby používal back-end, je třeba provést následující kroky:
- Zahrnout `backend` konfigurační blok `azurerm`s typem .
- Přidejte `storage_account_name` hodnotu do konfiguračního bloku.
- Přidejte `container_name` hodnotu do konfiguračního bloku.
- Přidejte `key` hodnotu do konfiguračního bloku.

Následující příklad konfiguruje back-end Terraform a vytvoří skupinu prostředků Azure.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
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

Inicializovat konfiguraci následujícím postupem:

1. Spusťte příkaz `terraform init`.
1. Spusťte příkaz `terraform apply`.

Teď můžete najít soubor stavu v objektu blob úložiště Azure.

## <a name="state-locking"></a>Uzamčení stavu

Objekty BLOB služby Azure Storage se automaticky uzamknou před každou operací, která zapisuje stav. Tento vzor zabraňuje souběžné operace stavu, které mohou způsobit poškození. 

Další informace naleznete v [tématu uzamčení stavu](https://www.terraform.io/docs/state/locking.html) v dokumentaci Terraform.

Zámek uvidíte, když prozkoumáte objekt blob prostřednictvím portálu Azure nebo jiných nástrojů pro správu Azure.

![Objekt blob Azure se zámkem](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Data uložená v objektu blob Azure je šifrována před trvalým. V případě potřeby Terraform načte stav ze zadního konce a uloží jej do místní paměti. Pomocí tohoto vzoru je stav nikdy zapsán na místní disk.

Další informace o šifrování Azure Storage najdete v tématu [šifrování služby Azure Storage pro data v klidovém stavu](../storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Další informace o používání Terraform v Azure](/azure/terraform)
