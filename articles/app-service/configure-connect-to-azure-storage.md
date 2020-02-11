---
title: Přidání vlastního úložiště (kontejner Windows)
description: Přečtěte si, jak připojit vlastní sdílenou síťovou složku ve vlastním kontejneru Windows v Azure App Service. Sdílet soubory mezi aplikacemi, spravovat statický obsah vzdáleně a přistupovat místně atd.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120683"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Konfigurace služby soubory Azure v kontejneru Windows na App Service

> [!NOTE]
> Tento článek se týká vlastních kontejnerů Windows. Pokud chcete nasadit nástroj na App Service v systému _Linux_, přečtěte si téma [obsluha obsahu z Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

Tato příručka ukazuje, jak získat přístup k Azure Storage v kontejnerech Windows. Podporují se jenom [sdílené složky souborů Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) a [sdílené složky Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) . V tomto postupu použijete sdílené složky Azure Files. Mezi výhody patří zabezpečený obsah, přenositelnost obsahu, přístup k více aplikacím a více metod přenosu.

## <a name="prerequisites"></a>Požadavky

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 nebo novější).
- [Existující aplikace s kontejnerem Windows v Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Vytvoření sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Nahrání souborů do sdílené složky Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Soubory Azure jsou jiné než výchozí úložiště a účtují se odděleně, nejsou součástí webové aplikace. V důsledku omezení infrastruktury nepodporuje konfiguraci brány firewall.
>

## <a name="limitations"></a>Omezení

- Azure Storage v kontejnerech Windows je **ve verzi Preview** a **nepodporuje** se v **produkčních scénářích**.
- Azure Storage v kontejnerech Windows podporuje pouze připojování **kontejnerů souborů Azure** (čtení a zápis).
- Azure Storage v kontejnerech Windows se v současné době **nepodporují** v plánech Windows App Service pro vlastní scénáře kódu.
- Azure Storage v kontejnerech Windows **nepodporuje** použití konfigurace **brány firewall úložiště** kvůli omezením infrastruktury.
- Azure Storage v kontejnerech Windows umožňuje zadat **až pět** přípojných bodů na jednu aplikaci.
- Azure Storage připojená k aplikaci není přístupná prostřednictvím koncových bodů App Service FTP/FTPs. Použijte [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
- Azure Storage se fakturuje nezávisle a **nejsou součástí** vaší webové aplikace. Přečtěte si další informace o [cenách Azure Storage](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Připojení úložiště k webové aplikaci (Preview)

 Pokud chcete připojit sdílenou složku souborů Azure do adresáře ve vaší aplikaci App Service, použijte příkaz [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) . Typ úložiště musí být AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

To byste měli udělat pro všechny ostatní adresáře, které chcete propojit se sdílenou složkou souborů Azure.

## <a name="verify"></a>Ověřit

Jakmile je sdílená složka souborů Azure propojená s webovou aplikací, můžete to ověřit spuštěním následujícího příkazu:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Další kroky

- [Migrujte aplikaci ASP.NET, aby se Azure App Service pomocí kontejneru Windows (Preview)](app-service-web-tutorial-windows-containers-custom-fonts.md).
