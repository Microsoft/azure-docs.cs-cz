---
title: Přidání vlastního úložiště (kontejner Windows)
description: Přečtěte si, jak připojit vlastní sdílenou síťovou složku ve vlastním kontejneru Windows ve službě Azure App Service. Sdílejte soubory mezi aplikacemi, spravujte statický obsah vzdáleně a přistupujte místně atd.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120683"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Konfigurace souborů Azure v kontejneru Windows ve službě App Service

> [!NOTE]
> Tento článek se vztahuje na vlastní kontejnery systému Windows. Informace o nasazení ve službě App Service na _Linuxu_najdete v [tématu Serve Content from Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

Tato příručka ukazuje, jak přistupovat k Azure Storage v kontejnerech Windows. Podporovány jsou pouze [sdílené složky souborů Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) a sdílené [složky premium.](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) V tomto návodu se používá sdílené složky Azure Files Share. Mezi výhody patří zabezpečený obsah, přenositelnost obsahu, přístup k více aplikacím a více způsobů přenosu.

## <a name="prerequisites"></a>Požadavky

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 nebo novější).
- [Existující aplikace Windows Container ve službě Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Vytvoření sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Nahrání souborů do sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Soubory Azure je jiné než výchozí úložiště a účtuje se samostatně, není součástí webové aplikace. Nepodporuje použití konfigurace brány firewall z důvodu omezení infrastruktury.
>

## <a name="limitations"></a>Omezení

- Azure Storage v kontejnerech Windows je **ve verzi preview** a není **podporována** pro **produkční scénáře**.
- Azure Storage v kontejnerech Windows podporuje jenom přistupující **kontejnery Azure Files** (čtení a zápis).
- Azure Storage v kontejnerech Windows není momentálně **podporované** pro doručování vlastních scénářů kódu v plánech služby Windows App Service.
- Azure Storage v kontejnerech Windows **nepodporuje** použití konfigurace **brány firewall úložiště** z důvodu omezení infrastruktury.
- Azure Storage v kontejnerech Windows umožňuje zadat **až pět** přípojné body na aplikaci.
- Azure Storage připojené k aplikaci není přístupný prostřednictvím koncových bodů FTP/FTP služby App Service. Použijte [Průzkumník a průzkumník azure.](https://azure.microsoft.com/features/storage-explorer/)
- Azure Storage se účtuje nezávisle a **není součástí** vaší webové aplikace. Přečtěte si další informace o [cenách Azure Storage](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Propojení úložiště s webovou aplikací (preview)

 Chcete-li připojit sdílení souborů Azure do adresáře v [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) aplikaci App Service, použijte příkaz. Typ úložiště musí být AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Měli byste to udělat pro všechny ostatní adresáře, které chcete propojit se sdílenou sponou Soubory Azure.

## <a name="verify"></a>Ověřit

Jakmile je sdílená složka Azure Files propojena s webovou aplikací, můžete to ověřit spuštěním následujícího příkazu:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Další kroky

- [Migrujte ASP.NET aplikaci do služby Azure App Service pomocí kontejneru windows (Preview).](app-service-web-tutorial-windows-containers-custom-fonts.md)
