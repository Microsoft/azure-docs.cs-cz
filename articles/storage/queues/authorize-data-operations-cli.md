---
title: Volba způsobu autorizace přístupu k Queue data pomocí Azure CLI
titleSuffix: Azure Storage
description: Určete, jak autorizovat datové operace s daty z fronty pomocí Azure CLI. Datové operace můžete autorizovat pomocí přihlašovacích údajů Azure AD, pomocí přístupového klíče účtu nebo pomocí tokenu sdíleného přístupového podpisu (SAS).
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2f7092d8ce184d7021774814e96935e46d1ffb56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363164"
---
# <a name="choose-how-to-authorize-access-to-queue-data-with-azure-cli"></a>Volba způsobu autorizace přístupu k Queue data pomocí Azure CLI

Azure Storage poskytuje rozšíření pro rozhraní příkazového řádku Azure, které vám umožní určit, jak chcete autorizovat operace s daty ve frontě. Datové operace můžete autorizovat následujícími způsoby:

- S objektem zabezpečení Azure Active Directory (Azure AD). Microsoft doporučuje použití přihlašovacích údajů Azure AD k zajištění vysokého zabezpečení a snadného použití.
- Pomocí přístupového klíče účtu nebo tokenu sdíleného přístupového podpisu (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Určete způsob, jakým jsou datové operace autorizovány.

Příkazy rozhraní příkazového řádku Azure pro čtení a zápis dat ve frontě zahrnují volitelný `--auth-mode` parametr. Zadáním tohoto parametru označíte, jak má být datová operace autorizována:

- Nastavte `--auth-mode` parametr tak, aby `login` se přihlásil pomocí objektu zabezpečení služby Azure AD (doporučeno).
- Nastavte `--auth-mode` parametr na starší `key` hodnotu pro pokus o načtení přístupového klíče účtu, který se má použít pro autorizaci. Pokud parametr vynecháte `--auth-mode` , Azure CLI se taky pokusí načíst přístupový klíč.

Pokud chcete použít `--auth-mode` parametr, ujistěte se, že máte nainstalované rozhraní příkazového řádku Azure CLI v 2.0.46 nebo novějším. Spusťte `az --version` a ověřte nainstalovanou verzi.

> [!NOTE]
> Když je účet úložiště zamčený s Azure Resource Manager zámek **jen pro čtení** , není pro tento účet úložiště povolená operace se [seznamem klíčů](/rest/api/storagerp/storageaccounts/listkeys) . **Seznam klíčů** je operace post a všechny operace post jsou znemožněny, když je pro tento účet nakonfigurován zámek **ReadOnly** . Z tohoto důvodu platí, že pokud je účet uzamčený pomocí zámku **jen pro čtení** , uživatelé, kteří ještě nemají klíče účtu, musí použít přihlašovací údaje Azure AD pro přístup k datům ve frontě.

> [!IMPORTANT]
> Pokud parametr vynecháte `--auth-mode` nebo ho nastavíte na `key` , pokusí se Azure CLI použít přístupový klíč k účtu pro autorizaci. V takovém případě společnost Microsoft doporučuje zadat přístupový klíč buď v příkazu, nebo v `AZURE_STORAGE_KEY` proměnné prostředí. Další informace o proměnných prostředí naleznete v části s názvem [nastavení proměnných prostředí pro parametry autorizace](#set-environment-variables-for-authorization-parameters).
>
> Pokud přístupový klíč nezadáte, pokusí se rozhraní příkazového řádku Azure volat poskytovatele prostředků Azure Storage a načíst ho pro každou operaci. Provádění mnoha operací s daty, které vyžadují volání poskytovatele prostředků, může způsobit omezení. Další informace o omezeních poskytovatele prostředků najdete v tématu [škálovatelnost a výkonnostní cíle pro poskytovatele prostředků Azure Storage](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autorizovat pomocí přihlašovacích údajů Azure AD

Když se přihlásíte k rozhraní příkazového řádku Azure CLI pomocí přihlašovacích údajů Azure AD, vrátí se přístupový token OAuth 2,0. Tento token je automaticky využíván rozhraním Azure CLI k autorizaci následných operací s daty proti Queue Storage. U podporovaných operací už nemusíte předávat klíč účtu nebo token SAS pomocí příkazu.

Pomocí řízení přístupu na základě role Azure (Azure RBAC) můžete přiřadit data do fronty k objektu zabezpečení služby Azure AD. Další informace o rolích Azure v Azure Storage najdete v tématu [Správa přístupových práv k datům Azure Storage pomocí Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Oprávnění pro volání operací s daty

Rozšíření Azure Storage jsou podporovaná pro operace s daty ve frontě. Operace, které můžete volat, závisí na oprávněních udělených objektu zabezpečení služby Azure AD, se kterým se přihlašujete ke službě Azure CLI. Oprávnění k frontám jsou přiřazena prostřednictvím Azure RBAC. Pokud jste například přiřadili roli **čtečky dat fronty úložiště** , můžete spustit skriptovací příkazy, které čtou data z fronty. Pokud jste přiřadili roli **Přispěvatel dat fronty úložiště** , můžete spustit skriptovací příkazy, které čtou, zapisují nebo odstraňují frontu nebo data, která obsahují.

Podrobnosti o oprávněních potřebných pro jednotlivé operace Azure Storage ve frontě najdete v tématu [volání operací úložiště s tokeny OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-queue-with-azure-ad-credentials"></a>Příklad: autorizovat operaci pro vytvoření fronty s přihlašovacími údaji Azure AD

Následující příklad ukazuje, jak vytvořit frontu z Azure CLI pomocí vašich přihlašovacích údajů Azure AD. Pokud chcete vytvořit frontu, budete se muset přihlásit do Azure CLI a budete potřebovat skupinu prostředků a účet úložiště.

1. Než vytvoříte frontu, přiřaďte roli [Přispěvatel dat fronty úložiště](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) sami sobě. I když jste vlastníkem účtu, potřebujete explicitní oprávnění k provádění operací s daty v účtu úložiště. Další informace o přiřazování rolí Azure najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům BLOB a front](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Rozšiřování přiřazení rolí Azure může trvat několik minut.

1. Voláním [`az storage queue create`](/cli/azure/storage/queue#az-storage-queue-create) příkazu s `--auth-mode` parametrem nastaveným na `login` vytvoříte frontu pomocí vašich přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

    ```azurecli
    az storage queue create \
        --account-name <storage-account> \
        --name sample-queue \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorizovat pomocí přístupového klíče účtu

Pokud máte klíč účtu, můžete zavolat jakoukoli datovou operaci Azure Storage. Obecně platí, že použití klíče účtu je méně zabezpečené. Pokud je klíč účtu ohrožený, může dojít k ohrožení zabezpečení všech dat ve vašem účtu.

Následující příklad ukazuje, jak vytvořit frontu pomocí přístupového klíče účtu. Zadejte klíč účtu a zadejte `--auth-mode` parametr s `key` hodnotou:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorizovat pomocí tokenu SAS

Máte-li token SAS, můžete volat operace s daty, které jsou povoleny pomocí SAS. Následující příklad ukazuje, jak vytvořit frontu pomocí tokenu SAS:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Nastavení proměnných prostředí pro autorizační parametry

Můžete zadat parametry autorizace v proměnných prostředí, aby se zabránilo jejich zahrnutí při každém volání operace Azure Storage data. Následující tabulka popisuje dostupné proměnné prostředí.

| Proměnná prostředí | Description |
|--|--|
| **AZURE_STORAGE_ACCOUNT** | Název účtu úložiště. Tato proměnná by se měla používat ve spojení s klíčem účtu úložiště nebo tokenem SAS. Pokud žádná není, Azure CLI se pokusí získat přístupový klíč účtu úložiště pomocí ověřeného účtu Azure AD. Pokud je spuštěný velký počet příkazů najednou, může být dosaženo limitu omezování Azure Storage poskytovatele prostředků. Další informace o omezeních poskytovatele prostředků najdete v tématu [škálovatelnost a výkonnostní cíle pro poskytovatele prostředků Azure Storage](../common/scalability-targets-resource-provider.md). |
| **AZURE_STORAGE_KEY** | Klíč účtu úložiště. Tato proměnná se musí používat ve spojení s názvem účtu úložiště. |
| **AZURE_STORAGE_CONNECTION_STRING** | Připojovací řetězec, který obsahuje klíč účtu úložiště nebo token SAS. Tato proměnná se musí používat ve spojení s názvem účtu úložiště. |
| **AZURE_STORAGE_SAS_TOKEN** | Token sdíleného přístupového podpisu (SAS). Tato proměnná se musí používat ve spojení s názvem účtu úložiště. |
| **AZURE_STORAGE_AUTH_MODE** | Režim ověřování, ve kterém se má příkaz Spustit. Povolené hodnoty jsou `login` (doporučeno) nebo `key` . Pokud zadáte `login` , rozhraní příkazového řádku Azure CLI použije přihlašovací údaje Azure AD k autorizaci operace s daty. Pokud zadáte starší `key` režim, pokusí se Azure CLI zadat dotaz na přístupový klíč účtu a ověřit příkaz pomocí klíče. |

## <a name="next-steps"></a>Další kroky

- [Přiřazení role Azure pro přístup k datům objektů BLOB a front pomocí Azure CLI](../common/storage-auth-aad-rbac-cli.md)
- [Autorizace přístupu k datům BLOB a Queue pomocí spravovaných identit pro prostředky Azure](../common/storage-auth-aad-msi.md)
