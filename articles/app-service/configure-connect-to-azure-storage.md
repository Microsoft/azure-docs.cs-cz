---
title: Přidat Azure Storage (kontejner)
description: Přečtěte si, jak připojit vlastní sdílenou síťovou složku v aplikaci s kontejnery v Azure App Service. Sdílet soubory mezi aplikacemi, spravovat statický obsah vzdáleně a přistupovat místně atd.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f844b5b413cda2cb16f9701970857be65fe6d91d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777612"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Přístup ke službě Azure Storage (Preview) jako sdílené síťové složce z kontejneru ve službě App Service

::: zone pivot="container-windows"

V této příručce se dozvíte, jak připojit Azure Storage soubory jako sdílenou síťovou složku pro kontejner Windows v App Service. Podporují se jenom [sdílené složky souborů Azure](../storage/files/storage-how-to-use-files-cli.md) a [sdílené složky Premium](../storage/files/storage-how-to-create-file-share.md) . Mezi výhody patří zabezpečený obsah, přenositelnost obsahu, přístup k více aplikacím a více metod přenosu.

> [!NOTE]
>Azure Storage v App Service jsou **ve verzi Preview** a **nepodporují** se v **produkčních scénářích**.

::: zone-end

::: zone pivot="container-linux"

Tato příručka ukazuje, jak připojit Azure Storage k App Service kontejneru Linux. K výhodám patří zabezpečený obsah, přenositelnost obsahu, trvalé úložiště, přístup k více aplikacím a více metod přenosu.

> [!NOTE]
>Azure Storage v App Service je **ve verzi Preview** pro App Service v systémech Linux a Web App for Containers. Nepodporují  se v **produkčních scénářích**.

::: zone-end

## <a name="prerequisites"></a>Požadavky

::: zone pivot="container-windows"

- [Existující aplikace s kontejnerem Windows v Azure App Service](quickstart-custom-container.md)
- [Vytvoření sdílené složky Azure](../storage/files/storage-how-to-use-files-cli.md)
- [Nahrání souborů do sdílené složky Azure](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Existující [App Service v aplikaci pro Linux](index.yml).
- [Účet Azure Storage](../storage/common/storage-account-create.md?tabs=azure-cli)
- [Sdílená složka Azure a adresář](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Soubory Azure jsou jiné než výchozí úložiště a účtují se odděleně, nejsou součástí webové aplikace. V důsledku omezení infrastruktury nepodporuje konfiguraci brány firewall.
>

## <a name="limitations"></a>Omezení

::: zone pivot="container-windows"

- Azure Storage v App Service se v současné době **nepodporují** pro scénáře přináší vlastní kód (aplikace pro Windows bez kontejneru).
- Azure Storage v App Service v důsledku omezení infrastruktury **nepodporuje** použití konfigurace **brány firewall úložiště** .
- Azure Storage s App Service vám umožní zadat **až pět** přípojných bodů na jednu aplikaci.
- Azure Storage připojená k aplikaci není přístupná prostřednictvím koncových bodů App Service FTP/FTPs. Použijte [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Azure Storage v App Service podporuje připojování **kontejnerů souborů Azure** (čtení a zápis) a **kontejnerů objektů blob Azure** (jen pro čtení).
- Azure Storage v App Service umožňuje zadat **až pět** přípojných bodů na jednu aplikaci.
- Azure Storage připojená k aplikaci není přístupná prostřednictvím koncových bodů App Service FTP/FTPs. Použijte [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Propojení úložiště s aplikací

::: zone pivot="container-windows"

Po vytvoření [účtu Azure Storage, sdílené složky a adresáře](#prerequisites)teď můžete aplikaci nakonfigurovat pomocí Azure Storage.

Pokud chcete připojit sdílenou složku souborů Azure do adresáře ve vaší aplikaci App Service, použijte [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) příkaz. Typ úložiště musí být AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

To byste měli udělat pro všechny ostatní adresáře, které chcete propojit se sdílenou složkou souborů Azure.

::: zone-end

::: zone pivot="container-linux"

Po vytvoření [účtu Azure Storage, sdílené složky a adresáře](#prerequisites)teď můžete aplikaci nakonfigurovat pomocí Azure Storage.

Pokud chcete účet úložiště připojit k adresáři v aplikaci App Service, použijte [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) příkaz. Typ úložiště může být Azureblobu nebo AzureFiles. V tomto příkladu se používá AzureFiles. Nastavení cesty připojení odpovídá složce v kontejneru, který chcete připojit k Azure Storage. Nastavením na '/' připojí celý kontejner k Azure Storage.


> [!CAUTION]
> Adresář zadaný jako cesta pro připojení ve vaší webové aplikaci by měl být prázdný. Veškerý obsah uložený v tomto adresáři bude odstraněn při přidání externího připojení. Pokud migrujete soubory pro existující aplikaci, vytvořte před zahájením zálohování své aplikace a jejího obsahu.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

To byste měli udělat pro všechny ostatní adresáře, které chcete propojit s účtem úložiště.

::: zone-end

## <a name="verify-linked-storage"></a>Ověřit propojené úložiště

Jakmile je sdílená složka propojená s aplikací, můžete to ověřit spuštěním následujícího příkazu:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Další kroky

::: zone pivot="container-windows"

- [Migrace vlastního softwaru na Azure App Service pomocí vlastního kontejneru](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Konfigurace vlastního kontejneru](configure-custom-container.md?pivots=platform-linux).

::: zone-end