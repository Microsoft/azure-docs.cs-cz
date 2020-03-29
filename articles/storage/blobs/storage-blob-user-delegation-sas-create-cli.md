---
title: Použití rozhraní příkazového příkazu Azure k vytvoření uživatelského delegování SAS pro kontejner nebo objekt blob
titleSuffix: Azure Storage
description: Zjistěte, jak vytvořit delegování uživatele SAS s přihlašovacími údaji služby Azure Active Directory pomocí rozhraní příkazového příkazového příkazu Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371985"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Vytvoření uživatelskédelegování SAS pro kontejner nebo objekt blob s rozhraním příkazového příkazu Azure

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Tento článek ukazuje, jak používat přihlašovací údaje azure active directory (Azure AD) k vytvoření uživatelskédelegování SAS pro kontejner nebo objekt blob s Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Instalace nejnovější verze Azure CLI

Pokud chcete pomocí příkazového příkazového příkazu Azure použít k zabezpečení SAS pomocí přihlašovacích údajů služby Azure AD, nejprve se ujistěte, že jste nainstalovali nejnovější verzi příkazového příkazového příkazu Azure. Další informace o instalaci příkazového příkazu k azure najdete [v tématu instalace příkazového příkazového příkazu k Azure](/cli/azure/install-azure-cli).

Chcete-li vytvořit delegování uživatele SAS pomocí rozhraní příkazového příkazu Konto Azure, ujistěte se, že jste nainstalovali verzi 2.0.78 nebo novější. Chcete-li zkontrolovat nainstalovanou `az --version` verzi, použijte příkaz.

## <a name="sign-in-with-azure-ad-credentials"></a>Přihlášení pomocí přihlašovacích údajů Azure AD

Přihlaste se k azure cli pomocí přihlašovacích údajů Azure AD. Další informace najdete v tématu [Přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Přiřazení oprávnění pomocí RBAC

Chcete-li vytvořit uživatelské delegování SAS z Azure PowerShell, účet Azure AD slouží k přihlášení do rozhraní příkazového příkazu Azure musí být přiřazena role, která zahrnuje **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** akce. Toto oprávnění umožňuje, aby účet Azure AD požádat *o klíč delegování uživatele*. Klíč delegování uživatele se používá k podepsání uživatelské delegování SAS. Role poskytující akci **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** musí být přiřazena na úrovni účtu úložiště, skupiny prostředků nebo předplatného.

Pokud nemáte dostatečná oprávnění k přiřazení rolí RBAC k objektu zabezpečení Azure AD, budete muset požádat vlastníka účtu nebo správce o přiřazení potřebných oprávnění.

Následující příklad přiřadí roli **přispěvatele dat objektů blob úložiště,** která zahrnuje akci **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Role je vymezena na úrovni účtu úložiště.

Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Další informace o předdefinovaných rolích, které zahrnují akci **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** najdete [v tématu Předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Použití přihlašovacích údajů Azure AD k zabezpečení SAS

Když vytvoříte uživatelské delegování SAS s Azure CLI, klíč delegování uživatele, který se používá k podpisu SAS se vytvoří pro vás implicitně. Čas zahájení a čas vypršení platnosti, které zadáte pro SAS se také používají jako čas zahájení a čas vypršení platnosti pro klíč delegování uživatele.

Vzhledem k tomu, že maximální interval, po který je platný klíč delegování uživatele, je 7 dní od počátečního data, měli byste zadat čas vypršení platnosti pro SAS, který je do 7 dnů od počátečního času. SAS je neplatný po vypršení platnosti klíče delegování uživatele, takže SAS s dobou vypršení platnosti delší než 7 dní bude stále platný po dobu 7 dnů.

Při vytváření uživatelské delegování `--auth-mode login` `--as-user parameters` SAS, a jsou požadovány. Zadejte *přihlášení* pro `--auth-mode` parametr tak, aby požadavky na Azure Storage jsou autorizovány s přihlašovacími údaji Azure AD. Zadejte `--as-user` parametr označující, že vrácené SAS by měla být uživatelské delegování SAS.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Vytvoření uživatelskédelegování SAS pro kontejner

Chcete-li vytvořit uživatelské delegování SAS pro kontejner s rozhraním příkazového příkazu Azure CLI, zavolejte příkaz [generování kontejneru úložiště az sas.](/cli/azure/storage/container#az-storage-container-generate-sas)

Podporovaná oprávnění pro delegování uživatele SAS v kontejneru zahrnují přidat, vytvořit, odstranit, seznam, číst a psát. Oprávnění lze zadat singly nebo kombinovat. Další informace o těchto oprávněních naleznete [v tématu Vytvoření uživatelskédelegace SAS](/rest/api/storageservices/create-user-delegation-sas).

Následující příklad vrátí token SAS delegování uživatele pro kontejner. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Vrácený token SAS delegování uživatele bude podobný:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Vytvoření uživatelskédelegování SAS pro objekt blob

Chcete-li vytvořit uživatelské delegování SAS pro objekt blob s Rozhraní matné rozhraní domény Azure, zavolejte příkaz [generování objektů blob úložiště az sas.](/cli/azure/storage/blob#az-storage-blob-generate-sas)

Podporovaná oprávnění pro delegování uživatele SAS na objekt blob patří Přidat, Vytvořit, Odstranit, Číst a zapsat. Oprávnění lze zadat singly nebo kombinovat. Další informace o těchto oprávněních naleznete [v tématu Vytvoření uživatelskédelegace SAS](/rest/api/storageservices/create-user-delegation-sas).

Následující syntaxe vrátí delegování uživatele SAS pro objekt blob. Příklad určuje `--full-uri` parametr, který vrátí identifikátor URI objektu BLOB s připojeným tokenem SAS. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami:

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

Vrácené identifikátory URI delegování uživatele SAS budou podobné:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Uživatelské delegování SAS nepodporuje definování oprávnění s uloženou zásadou přístupu.

## <a name="revoke-a-user-delegation-sas"></a>Odvolání uživatelského delegování SAS

Chcete-li odvolat uživatelské delegování SAS z rozhraní příkazu KOD Azure, zavolejte příkaz [revoke-delegation-keys účtu az.](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) Tento příkaz odvolá všechny klíče delegování uživatelů přidružené k zadanému účtu úložiště. Všechny sdílené přístupové podpisy přidružené k těmto klíčům jsou zneplatněny.

Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Klíč delegování uživatele a přiřazení rolí RBAC jsou uloženy do mezipaměti služby Azure Storage, takže může být zpoždění mezi zahájením procesu odvolání a když existující uživatel delegování SAS stane neplatným.

## <a name="next-steps"></a>Další kroky

- [Vytvoření uživatelskédelegace SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Získat operaci klíče delegování uživatele](/rest/api/storageservices/get-user-delegation-key)
