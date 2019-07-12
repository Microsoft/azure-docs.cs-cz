---
title: Konfigurace úložiště pomocí služby soubory Azure
description: Postup při konfiguraci a připojení k Azure Files v kontejneru Windows ve službě App Service.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu-msft
ms.openlocfilehash: ea6555e8459b8f372a73d7f943e9a96523d4c791
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789043"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Konfigurace Azure souborů v kontejneru Windows ve službě App Service

> [!NOTE]
> Tento článek se týká vlastní kontejnery Windows. Nasazení do služby App Service v _Linux_, naleznete v tématu [poskytování obsahu ze služby Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

Tato příručka ukazuje, jak získat přístup k Azure Storage v kontejnerech Windows. Pouze [sdílených složek Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) a [sdílených složek Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) jsou podporovány. V tomto návodu použijete sdílených složek Azure. Mezi výhody patří zabezpečené obsahu, obsah přenositelnost, přístup k více aplikací a přenosech několik metod.

## <a name="prerequisites"></a>Požadavky

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 nebo novější).
- [Existující aplikaci kontejner Windows ve službě Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Vytvoření sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Nahrání souborů do sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Služba soubory Azure je jiné než výchozí úložiště a účtují samostatně, nejsou zahrnuty s webovou aplikací. Nepodporuje použití konfigurace brány Firewall kvůli omezení infrastruktury.
>

## <a name="link-storage-to-your-web-app-preview"></a>Odkaz úložiště do vaší webové aplikace (preview)

 Připojení sdílené složky služby soubory Azure do jiného adresáře v aplikaci služby App Service, můžete použít [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) příkazu. AzureFiles musí být typem úložiště.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Byste měli udělat pro jiné adresáře, který chcete propojit s Azure Files sdílet.

## <a name="verify"></a>Ověřit

Jakmile sdílenou složku služby soubory Azure je propojený na webovou aplikaci, můžete to ověřit spuštěním následujícího příkazu:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Další postup

- [Migrace aplikace ASP.NET do služby Azure App Service pomocí kontejneru Windows (Preview)](app-service-web-tutorial-windows-containers-custom-fonts.md).