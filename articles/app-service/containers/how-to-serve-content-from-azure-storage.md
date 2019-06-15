---
title: Poskytování obsahu z Azure Storage na platformě Linux - App Service
description: Jak nakonfigurovat a poskytování obsahu z Azure Storage ve službě Azure App Service v Linuxu.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu-msft
ms.openlocfilehash: 6b4e145a693aabbf1a00d732e2fd602e7c887a03
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956011"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Poskytování obsahu z Azure Storage ve službě App Service v Linuxu

Tato příručka ukazuje, jak poskytovat statický obsah ve službě App Service v Linuxu pomocí [služby Azure Storage](/azure/storage/common/storage-introduction). Mezi výhody patří zabezpečené obsahu, obsah přenositelnost, přístup k více aplikací a přenosech několik metod. 

## <a name="prerequisites"></a>Požadavky

- Existující webové aplikace (App Service v Linuxu a Web App for Containers).
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 nebo novější).

## <a name="create-azure-storage"></a>Vytvoření služby Azure Storage

> [!NOTE]
> Azure Storage je jiné než výchozí úložiště a účtují samostatně, nejsou zahrnuty s webovou aplikací.
>

Vytvoření Azure [účtu služby Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Nahrání souborů do služby Azure Storage

K nahrání do místního adresáře do účtu úložiště, můžete použít [ `az storage blob upload-batch` ](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) příkaz jako v následujícím příkladu:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Odkaz úložiště do vaší webové aplikace (preview)

> [!CAUTION]
> Propojení existujícího adresáře ve webové aplikaci na účet úložiště odstraníte obsah adresáře. Pokud se migrace souborů pro existující aplikace, vytvořte zálohu vaší aplikace a její obsah před zahájením.
>

Připojení účtu úložiště do jiného adresáře v aplikaci služby App Service, můžete použít [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) příkazu. Azurebloblocation nebo AzureFiles může být typem úložiště. Pro tento kontejner použijete AzureBlob.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Byste měli udělat pro jiné adresáře, který chcete propojit na účet úložiště.

## <a name="verify"></a>Ověřit

Jakmile se kontejner úložiště je propojená do webové aplikace, můžete to ověřit spuštěním následujícího příkazu:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Používání vlastního úložiště v Docker Compose

Azure Storage je možné připojit s vícekontejnerové aplikace pomocí id vlastní. Chcete-li zobrazit název vlastní id, spusťte [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

Ve vaší *docker-compose.yml* souborů, mapování `volumes` umožňuje `custom-id`. Příklad:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Další postup

- [Konfigurace webových aplikací ve službě Azure App Service](../configure-common.md).
