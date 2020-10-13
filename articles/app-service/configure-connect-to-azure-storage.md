---
title: Přidat Azure Storage (kontejner)
description: Přečtěte si, jak připojit vlastní sdílenou síťovou složku v aplikaci s kontejnery v Azure App Service. Sdílet soubory mezi aplikacemi, spravovat statický obsah vzdáleně a přistupovat místně atd.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 18e3f29a90ae2c6c66858e14ee91fb447a4a0045
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968646"
---
# <a name="access-azure-storage-as-a-network-share-from-a-container-in-app-service"></a>Přístup ke službě Azure Storage jako sdílené síťové složce z kontejneru ve službě App Service

::: zone pivot="container-windows"

V této příručce se dozvíte, jak připojit Azure Storage soubory jako sdílenou síťovou složku pro kontejner Windows v App Service. Podporují se jenom [sdílené složky souborů Azure](../storage/files/storage-how-to-use-files-cli.md) a [sdílené složky Premium](../storage/files/storage-how-to-create-premium-fileshare.md) . Mezi výhody patří zabezpečený obsah, přenositelnost obsahu, přístup k více aplikacím a více metod přenosu.

::: zone-end

::: zone pivot="container-linux"

Tato příručka ukazuje, jak připojit Azure Storage k App Service kontejneru Linux. K výhodám patří zabezpečený obsah, přenositelnost obsahu, trvalé úložiště, přístup k více aplikacím a více metod přenosu.

::: zone-end

## <a name="prerequisites"></a>Předpoklady

::: zone pivot="container-windows"

- [Existující aplikace s kontejnerem Windows v Azure App Service](quickstart-custom-container.md)
- [Vytvoření sdílené složky Azure](../storage/files/storage-how-to-use-files-cli.md)
- [Nahrání souborů do sdílené složky Azure](../storage/files/storage-files-deployment-guide.md)

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

- Azure Storage v App Service jsou **ve verzi Preview** a **nepodporují** se v **produkčních scénářích**.
- Azure Storage v App Service se v současné době **nepodporují** pro scénáře přináší vlastní kód (aplikace pro Windows bez kontejneru).
- Azure Storage v App Service v důsledku omezení infrastruktury **nepodporuje** použití konfigurace **brány firewall úložiště** .
- Azure Storage s App Service vám umožní zadat **až pět** přípojných bodů na jednu aplikaci.
- Azure Storage připojená k aplikaci není přístupná prostřednictvím koncových bodů App Service FTP/FTPs. Použijte [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- Azure Storage v App Service je **ve verzi Preview** pro App Service v systémech Linux a Web App for Containers. Nepodporují **not supported** se v **produkčních scénářích**.
- Azure Storage v App Service podporuje připojování **kontejnerů souborů Azure** (čtení a zápis) a **kontejnerů objektů blob Azure** (jen pro čtení).
- Azure Storage v App Service umožňuje zadat **až pět** přípojných bodů na jednu aplikaci.
- Azure Storage připojená k aplikaci není přístupná prostřednictvím koncových bodů App Service FTP/FTPs. Použijte [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Propojení úložiště s aplikací

::: zone pivot="container-windows"

Po vytvoření [účtu Azure Storage, sdílené složky a adresáře](#prerequisites)teď můžete aplikaci nakonfigurovat pomocí Azure Storage.

Pokud chcete připojit sdílenou složku souborů Azure do adresáře ve vaší aplikaci App Service, použijte [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) příkaz. Typ úložiště musí být AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

To byste měli udělat pro všechny ostatní adresáře, které chcete propojit se sdílenou složkou souborů Azure.

::: zone-end

::: zone pivot="container-linux"

Po vytvoření [účtu Azure Storage, sdílené složky a adresáře](#prerequisites)teď můžete aplikaci nakonfigurovat pomocí Azure Storage.

Pokud chcete účet úložiště připojit k adresáři v aplikaci App Service, použijte [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) příkaz. Typ úložiště může být Azureblobu nebo AzureFiles. V tomto příkladu se používá AzureFiles. Nastavení cesty připojení odpovídá složce, kterou chcete připojit z Azure Storage. Nastavení na/připojí celou Azure Storage.


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
