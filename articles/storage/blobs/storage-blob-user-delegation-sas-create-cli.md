---
title: Vytvoření SAS pro delegování uživatelů pro kontejner nebo objekt BLOB pomocí Azure CLI (Preview) – Azure Storage
description: Naučte se, jak vytvořit SAS delegování uživatelů pomocí Azure Active Directory přihlašovacích údajů v Azure Storage pomocí Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: ef51a1b130323a8799d5334d8d043fda08fcc7ef
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69896968"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli-preview"></a>Vytvoření SAS delegování uživatele pro kontejner nebo objekt BLOB pomocí rozhraní příkazového řádku Azure (Preview)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

V tomto článku se dozvíte, jak používat přihlašovací údaje pro Azure Active Directory (Azure AD) k vytvoření SAS pro delegování uživatelů pro kontejner nebo objekt BLOB pomocí Azure CLI (Preview).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Instalace nejnovější verze rozhraní příkazového řádku Azure

Pokud chcete pomocí Azure CLI zabezpečit SAS pomocí přihlašovacích údajů Azure AD, nejdřív se ujistěte, že máte nainstalovanou nejnovější verzi Azure CLI. Další informace o instalaci rozhraní příkazového řádku Azure najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sign-in-with-azure-ad-credentials"></a>Přihlášení pomocí přihlašovacích údajů Azure AD

Přihlaste se k Azure CLI pomocí vašich přihlašovacích údajů Azure AD. Další informace najdete v tématu [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Přiřazení oprávnění s RBAC

K vytvoření SAS delegování uživatele z Azure PowerShell musí být účet služby Azure AD použitý k přihlášení do Azure CLI přiřazen roli, která zahrnuje akci **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Toto oprávnění umožňuje účtu Azure AD požádat o *klíč delegování uživatele*. Klíč pro delegování uživatelů se používá k podepsání SAS delegování uživatele. Role, která poskytuje akci **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** , musí být přiřazená na úrovni účtu úložiště, skupiny prostředků nebo předplatného.

Pokud nemáte dostatečná oprávnění k přiřazení rolí RBAC k objektu zabezpečení služby Azure AD, může být nutné požádat vlastníka nebo správce účtu, aby přiřadil potřebná oprávnění.

Následující příklad přiřadí roli **Přispěvatel dat objektu BLOB úložiště** , která zahrnuje akci **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Role je vymezená na úrovni účtu úložiště.

Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Další informace o předdefinovaných rolích, které zahrnují akci **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** , najdete v tématu [předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Použití přihlašovacích údajů Azure AD k zabezpečení SAS

Když vytvoříte SAS delegování uživatelů pomocí Azure CLI, vytvoří se klíč delegování uživatele, který se používá k podepsání SAS, implicitně. Čas spuštění a čas vypršení platnosti, který zadáte pro SAS, se používá také jako počáteční čas a čas vypršení platnosti klíče pro delegování uživatele.

Vzhledem k tomu, že maximální interval, po který je klíč delegování uživatele platný, je 7 dní od počátečního data, měli byste zadat čas vypršení platnosti pro SAS, který je do 7 dnů od času spuštění. Podpis SAS je po vypršení platnosti klíče pro delegování uživatele neplatný, takže platnost SAS s časem vypršení platnosti delší než 7 dní bude platit i pro 7 dní.

Při vytváření SAS `--auth-mode login` delegování uživatele jsou vyžadovány a `--as-user parameters` . Zadejte *přihlašovací* údaje pro `--auth-mode` parametr, aby byly požadavky na Azure Storage autorizovány pomocí vašich přihlašovacích údajů Azure AD. `--as-user` Zadejte parametr, který označuje, že vrácený SAS by měl být SAS delegování uživatele.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Vytvoření SAS pro delegování uživatelů pro kontejner

Pokud chcete vytvořit přidružení zabezpečení delegování uživatelů pro kontejner pomocí Azure CLI, zavolejte příkaz [AZ Storage Container Generate-SAS](/cli/azure/storage/container#az-storage-container-generate-sas) .

K dispozici jsou podporovaná oprávnění pro SAS uživatelů na kontejneru, včetně přidat, vytvořit, odstranit, zobrazit, číst a zapisovat. Oprávnění lze zadat jednotlivě nebo v kombinaci. Další informace o těchto oprávněních najdete v tématu [Vytvoření SAS uživatele pro delegování](/rest/api/storageservices/create-user-delegation-sas).

Následující příklad vrátí token SAS pro delegování uživatele pro kontejner. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Vrácený token SAS pro delegování uživatelů bude vypadat přibližně takto:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Vytvoření SAS pro delegování uživatelů pro objekt BLOB

K vytvoření SAS delegování uživatele pro objekt BLOB pomocí rozhraní příkazového řádku Azure volejte příkaz [AZ Storage BLOB Generate-SAS](/cli/azure/storage/blob#az-storage-blob-generate-sas) .

K dispozici jsou podporovaná oprávnění pro SAS uživatele v objektu blob, včetně přidat, vytvořit, odstranit, číst a zapisovat. Oprávnění lze zadat jednotlivě nebo v kombinaci. Další informace o těchto oprávněních najdete v tématu [Vytvoření SAS uživatele pro delegování](/rest/api/storageservices/create-user-delegation-sas).

Následující syntaxe vrátí SAS delegování uživatele pro objekt BLOB. Příklad určuje `--full-uri` parametr, který vrátí identifikátor URI objektu BLOB s připojením tokenu SAS. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

Vrácený identifikátor URI SAS pro delegování uživatele bude vypadat přibližně takto:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> SAS delegování uživatele nepodporuje definování oprávnění s uloženými zásadami přístupu.

## <a name="next-steps"></a>Další kroky

- [Vytvoření SAS delegování uživatele (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Získat operaci klíče delegování uživatele](/rest/api/storageservices/get-user-delegation-key)
