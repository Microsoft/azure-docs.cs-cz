---
title: Obsluha obsahu z Azure Storage do kontejnerů Linux
description: Naučte se, jak připojit vlastní sdílenou síťovou složku ke kontejneru Linux v Azure App Service. Sdílet soubory mezi aplikacemi, spravovat statický obsah vzdáleně a přistupovat místně atd.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 0a1e811787a43be76f94b13a6ec9886510c47d1d
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866963"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Obsluha obsahu z Azure Storage v App Service v systému Linux

V této příručce se dozvíte, jak připojit Azure Storage k App Service v systému Linux. K výhodám patří zabezpečený obsah, přenositelnost obsahu, trvalé úložiště, přístup k více aplikacím a více metod přenosu.


> [!IMPORTANT]
> Azure Storage v App Service na Linux je funkce ve **verzi Preview** . Tato funkce není **podporována v produkčních scénářích**.
>

## <a name="prerequisites"></a>Požadavky

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 nebo novější).
- Existující [App Service v aplikaci pro Linux](https://docs.microsoft.com/azure/app-service/containers/).
- [Účet Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Sdílená složka Azure a adresář](https://docs.microsoft.com/azure/storage/common/storage-azure-cli#create-and-manage-file-shares).


## <a name="limitations-of-azure-storage-with-app-service"></a>Omezení Azure Storage s App Service

- Azure Storage s App Service je **ve verzi Preview** pro App Service v systémech Linux a Web App for Containers. Nepodporují se v **produkčních scénářích**.
- Azure Storage s App Service podporuje připojování **kontejnerů souborů Azure** (čtení a zápis) a **kontejnerů objektů blob Azure** (jen pro čtení).
- Azure Storage s App Service v důsledku omezení infrastruktury **nepodporuje** použití konfigurace **brány firewall úložiště** .
- Azure Storage s App Service vám umožní zadat **až pět** přípojných bodů na jednu aplikaci.
- Azure Storage není **součástí** vaší webové aplikace a účtuje se samostatně. Přečtěte si další informace o [cenách Azure Storage](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> Konfigurace App Service s využitím Azure Blob Storage se načtou jenom v únoru 2020. [Další informace](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Konfigurace aplikace pomocí Azure Storage

Po vytvoření [účtu Azure Storage, sdílené složky a adresáře](#prerequisites)teď můžete aplikaci nakonfigurovat pomocí Azure Storage.

Pokud chcete účet úložiště připojit k adresáři v aplikaci App Service, použijte příkaz [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) . Typ úložiště může být Azureblobu nebo AzureFiles. V tomto příkladu se používá AzureFiles.


> [!CAUTION]
> Adresář zadaný jako cesta pro připojení ve vaší webové aplikaci by měl být prázdný. Veškerý obsah uložený v tomto adresáři bude odstraněn při přidání externího připojení. Pokud migrujete soubory pro existující aplikaci, vytvořte před zahájením zálohování své aplikace a jejího obsahu.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

To byste měli udělat pro všechny ostatní adresáře, které chcete propojit s účtem úložiště.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Ověřit Azure Storage odkaz na webovou aplikaci

Jakmile je kontejner úložiště propojený s webovou aplikací, můžete to ověřit spuštěním následujícího příkazu:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Použití Azure Storage v Docker Compose

Azure Storage můžete připojit s aplikacemi s více kontejnery pomocí vlastního identifikátoru. Chcete-li zobrazit název vlastního ID, spusťte příkaz [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

V souboru *Docker-Compose. yml* namapujte možnost `volumes` na `custom-id`. Příklad:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Další kroky

- [Nakonfigurujte webové aplikace v Azure App Service](../configure-common.md).

