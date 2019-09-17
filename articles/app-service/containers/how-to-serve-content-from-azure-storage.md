---
title: Obsluha obsahu z Azure Storage v systému Linux – App Service
description: Postup konfigurace a obsluhy obsahu z Azure Storage v Azure App Service v systému Linux.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu
ms.openlocfilehash: 97c03ad294bba1f8a0285fff4595991ca0acc8b5
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018276"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Obsluha obsahu z Azure Storage v App Service v systému Linux

V této příručce se dozvíte, jak ve službě App Service v systému Linux zpracovat statický obsah pomocí [Azure Storage](/azure/storage/common/storage-introduction). K výhodám patří zabezpečený obsah, přenositelnost obsahu, trvalé úložiště, přístup k více aplikacím a více metod přenosu.

## <a name="prerequisites"></a>Požadavky

- Existující webová aplikace (App Service v systému Linux nebo Web App for Containers).
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) (2.0.46 nebo novější).

## <a name="create-azure-storage"></a>Vytvořit Azure Storage

> [!NOTE]
> Azure Storage je jiné než výchozí úložiště a účtuje se samostatně, není součástí webové aplikace.
>
> Využití vlastního úložiště v důsledku omezení infrastruktury nepodporuje použití konfigurace brány firewall úložiště.
>

Vytvořte [účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)Azure Azure.

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Nahrání souborů do Azure Storage

Pokud chcete nahrát místní adresář do účtu úložiště, použijte [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) příkaz podobný následujícímu příkladu:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Připojení úložiště k webové aplikaci (Preview)

> [!CAUTION]
> Když propojíte stávající adresář ve webové aplikaci k účtu úložiště, odstraní se obsah adresáře. Pokud migrujete soubory pro existující aplikaci, vytvořte před zahájením zálohování své aplikace a jejího obsahu.
>

Pokud chcete účet úložiště připojit k adresáři v aplikaci App Service, použijte [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) příkaz. Typ úložiště může být Azureblobu nebo AzureFiles. Pro tento kontejner použijete Azureblobu.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

To byste měli udělat pro všechny ostatní adresáře, které chcete propojit s účtem úložiště.

## <a name="verify"></a>Ověření

Jakmile je kontejner úložiště propojený s webovou aplikací, můžete to ověřit spuštěním následujícího příkazu:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Použití vlastního úložiště v Docker Compose

Azure Storage můžete připojit s aplikacemi s více kontejnery pomocí vlastního identifikátoru. Chcete-li zobrazit název vlastního ID, spusťte [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)příkaz.

V souboru *Docker-Compose. yml* namapujte `volumes` možnost na `custom-id`. Příklad:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Další kroky

- [Nakonfigurujte webové aplikace v Azure App Service](../configure-common.md).
