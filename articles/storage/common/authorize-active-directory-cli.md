---
title: Spuštění příkazů rozhraní příkazového řádku Azure s přihlašovacími údaji Azure AD pro přístup k datům objektů BLOB nebo Queue
titleSuffix: Azure Storage
description: Azure CLI podporuje přihlašování pomocí přihlašovacích údajů Azure AD ke spouštění příkazů v Azure Storage dat objektů BLOB a front. K dispozici je přístupový token pro relaci a slouží k autorizaci operací volání. Oprávnění závisí na roli RBAC přiřazené k objektu zabezpečení Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4133c081823f1cc319480c1bc847b672df0374d4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660610"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Spuštění příkazů rozhraní příkazového řádku Azure s přihlašovacími údaji Azure AD pro přístup k datům objektů BLOB nebo Queue

Azure Storage poskytuje rozšíření pro rozhraní příkazového řádku Azure, které vám umožní přihlásit se a spouštět příkazy skriptování s přihlašovacími údaji Azure Active Directory (Azure AD). Když se přihlásíte k rozhraní příkazového řádku Azure CLI pomocí přihlašovacích údajů Azure AD, vrátí se přístupový token OAuth 2,0. Tento token se automaticky používá v Azure CLI k autorizaci následných operací s daty v úložišti objektů BLOB nebo Queue. U podporovaných operací už nemusíte předávat klíč účtu nebo token SAS pomocí příkazu.

Pomocí řízení přístupu na základě role (RBAC) můžete přiřadit k objektu zabezpečení služby Azure AD oprávnění k objektům blob a frontám. Další informace o rolích RBAC v Azure Storage najdete v tématu [Správa přístupových práv k datům Azure Storage pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Podporované operace

Rozšíření Azure Storage jsou podporovaná pro operace s daty objektů BLOB a front. Operace, které můžete volat, závisí na oprávněních udělených objektu zabezpečení služby Azure AD, se kterým se přihlašujete ke službě Azure CLI. Oprávnění pro Azure Storage kontejnery nebo fronty jsou přiřazena prostřednictvím RBAC. Pokud jste například přiřadili roli **čtečky dat objektů BLOB** , můžete spustit skriptovací příkazy, které čtou data z kontejneru nebo fronty. Pokud jste přiřadili roli **Přispěvatel dat objektu BLOB** , můžete spustit skriptovací příkazy, které čtou, zapisují nebo odstraňují kontejner nebo frontu nebo data, která obsahují.

Podrobnosti o oprávněních potřebných pro jednotlivé operace Azure Storage v kontejneru nebo frontě najdete v tématu [volání operací úložiště s tokeny OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Volání příkazů rozhraní příkazového řádku Azure pomocí přihlašovacích údajů Azure AD

Rozhraní příkazového řádku Azure podporuje parametr `--auth-mode` pro operace s daty BLOB a Queue:

- Nastavte parametr `--auth-mode`, aby se `login` přihlásil pomocí objektu zabezpečení Azure AD.
- Pokud nejsou k dispozici žádné parametry ověřování pro účet, nastavte parametr `--auth-mode` na hodnotu Legacy `key`, která se má pokusit zadat dotaz na klíč účtu.

Následující příklad ukazuje, jak vytvořit kontejner v novém účtu úložiště z Azure CLI pomocí vašich přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

1. Ujistěte se, že máte nainstalovanou verzi Azure CLI 2.0.46 nebo novější. Spusťte `az --version` a ověřte nainstalovanou verzi.

1. Spusťte `az login` a ověřte v okně prohlížeče:

    ```azurecli
    az login
    ```

1. Zadejte požadované předplatné. Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Vytvořte účet úložiště v rámci této skupiny prostředků pomocí [AZ Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_ZRS \
        --encryption-services blob
    ```

1. Než vytvoříte kontejner, přiřaďte roli [Přispěvatel dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) sami sobě. I když jste vlastníkem účtu, potřebujete explicitní oprávnění k provádění operací s daty v účtu úložiště. Další informace o přiřazování rolí RBAC najdete [v tématu udělení přístupu k datům objektů blob Azure a frontě pomocí RBAC v Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Rozšiřování přiřazení rolí RBAC může trvat několik minut.

1. Zavolejte pomocí příkazu [AZ Storage Container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) s parametrem `--auth-mode` nastaveným na `login` k vytvoření kontejneru pomocí přihlašovacích údajů Azure AD:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

Proměnná prostředí přidružená k parametru `--auth-mode` je `AZURE_STORAGE_AUTH_MODE`. Můžete zadat vhodnou hodnotu v proměnné prostředí, abyste se nemuseli zahrnout do každého volání operace Azure Storage data.

## <a name="next-steps"></a>Další kroky

- [Přiřazení role RBAC pro přístup k datům objektů BLOB a front pomocí Azure CLI](storage-auth-aad-rbac-cli.md)
- [Autorizace přístupu k datům BLOB a Queue pomocí spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md)
