---
title: Obsluha obsahu z Azure Storage do linuxových kontejnerů
description: Zjistěte, jak připojit vlastní sdílenou síťovou složku ke svému linuxovému kontejneru ve službě Azure App Service. Sdílejte soubory mezi aplikacemi, spravujte statický obsah vzdáleně a přistupujte místně atd.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297913"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Obsluha obsahu z Azure Storage ve službě App Service na Linuxu

> [!NOTE]
> Tento článek se vztahuje na linuxové kontejnery. Informace o nasazení do vlastních kontejnerů Windows najdete [v tématu Konfigurace souborů Azure v kontejneru Windows ve službě App Service](../configure-connect-to-azure-storage.md). Azure Storage ve službě App Service na Linuxu je funkce **náhledu.** Tato funkce **není podporována pro produkční scénáře**.
>

Tato příručka ukazuje, jak připojit Azure Storage ke službě App Service na Linuxu. Mezi výhody patří zabezpečený obsah, přenositelnost obsahu, trvalé úložiště, přístup k více aplikacím a více způsobů přenosu.

## <a name="prerequisites"></a>Požadavky

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 nebo novější).
- Existující [app service na Linuxu aplikace](https://docs.microsoft.com/azure/app-service/containers/).
- [Účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Sdílená složka a adresář azure souboru](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Omezení úložiště Azure pomocí služby App Service

- Azure Storage with App Service je **ve verzi** pro App Service na Linuxu a Web App pro kontejnery. Není **podporována** pro **produkční scénáře**.
- Azure Storage se službou App Service podporuje připojení **kontejnerů souborů Azure** (čtení a zápis) a **kontejnerů objektů Blob Azure** (jen pro čtení)
- Azure Storage se službou App Service **nepodporuje** použití konfigurace **brány firewall úložiště** z důvodu omezení infrastruktury.
- Azure Storage se službou App Service umožňuje zadat **až pět** přípojné body na aplikaci.
- Azure Storage připojené k aplikaci není přístupný prostřednictvím koncových bodů FTP/FTP služby App Service. Použijte [Průzkumník a průzkumník azure.](https://azure.microsoft.com/features/storage-explorer/)
- Azure Storage **není součástí** vaší webové aplikace a účtuje se samostatně. Přečtěte si další informace o [cenách Azure Storage](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> Konfigurace služby App Service pomocí azure blob storage se stanou jen pro čtení v únoru 2020. [Další informace](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Konfigurace aplikace pomocí Azure Storage

Po vytvoření účtu [Azure Storage, sdílené složky a adresáře](#prerequisites)teď můžete aplikaci nakonfigurovat pomocí Azure Storage.

Chcete-li připojit účet úložiště do adresáře v [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) aplikaci App Service, použijte příkaz. Typ úložiště může být AzureBlob nebo AzureFiles. AzureFiles se používá v tomto příkladu.


> [!CAUTION]
> Adresář zadaný jako cesta k připojení ve webové aplikaci by měl být prázdný. Veškerý obsah uložený v tomto adresáři bude odstraněn při přidání externího připojení. Pokud migrujete soubory pro existující aplikaci, vytvořte zálohu aplikace a jejího obsahu, než začnete.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Měli byste to udělat pro všechny ostatní adresáře, které chcete propojit s účtem úložiště.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Ověření odkazu úložiště Azure na webovou aplikaci

Jakmile je kontejner úložiště propojen s webovou aplikací, můžete to ověřit spuštěním následujícího příkazu:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Použití Azure Storage v Dockercomse

Azure Storage se dá namontovat pomocí aplikací s více kontejnery pomocí vlastního ID. Chcete-li zobrazit vlastní název [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)id, spusťte .

V souboru *docker-compose.yml* `volumes` namapujte možnost na `custom-id`. Například:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Další kroky

- [Konfigurace webových aplikací ve službě Azure App Service](../configure-common.md).

