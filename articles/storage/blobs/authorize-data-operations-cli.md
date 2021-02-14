---
title: Volba způsobu autorizace přístupu k datům objektů BLOB pomocí Azure CLI
titleSuffix: Azure Storage
description: Určete, jak autorizovat datové operace s daty objektů BLOB pomocí Azure CLI. Datové operace můžete autorizovat pomocí přihlašovacích údajů Azure AD, pomocí přístupového klíče účtu nebo pomocí tokenu sdíleného přístupového podpisu (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 06b37e8b25d932115384124a45156c801fb9708f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361668"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>Volba způsobu autorizace přístupu k datům objektů BLOB pomocí Azure CLI

Azure Storage poskytuje rozšíření pro rozhraní příkazového řádku Azure, které vám umožní určit, jak chcete autorizovat operace s daty objektů BLOB. Datové operace můžete autorizovat následujícími způsoby:

- S objektem zabezpečení Azure Active Directory (Azure AD). Microsoft doporučuje použití přihlašovacích údajů Azure AD k zajištění vysokého zabezpečení a snadného použití.
- Pomocí přístupového klíče účtu nebo tokenu sdíleného přístupového podpisu (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Určete způsob, jakým jsou datové operace autorizovány.

Příkazy rozhraní příkazového řádku Azure pro čtení a zápis dat objektů BLOB zahrnují volitelný `--auth-mode` parametr. Zadáním tohoto parametru označíte, jak má být datová operace autorizována:

- Nastavte `--auth-mode` parametr tak, aby `login` se přihlásil pomocí objektu zabezpečení služby Azure AD (doporučeno).
- Nastavte `--auth-mode` parametr na starší `key` hodnotu pro pokus o načtení přístupového klíče účtu, který se má použít pro autorizaci. Pokud parametr vynecháte `--auth-mode` , Azure CLI se taky pokusí načíst přístupový klíč.

Pokud chcete použít `--auth-mode` parametr, ujistěte se, že máte nainstalovanou verzi Azure CLI 2.0.46 nebo novější. Spusťte `az --version` a ověřte nainstalovanou verzi.

> [!NOTE]
> Když je účet úložiště zamčený s Azure Resource Manager zámek **jen pro čtení** , není pro tento účet úložiště povolená operace se [seznamem klíčů](/rest/api/storagerp/storageaccounts/listkeys) . **Seznam klíčů** je operace post a všechny operace post jsou znemožněny, když je pro tento účet nakonfigurován zámek **ReadOnly** . Z tohoto důvodu platí, že pokud je účet uzamčený pomocí zámku **jen pro čtení** , uživatelé, kteří ještě nemají klíče účtu, musí použít přihlašovací údaje Azure AD pro přístup k datům objektu BLOB.

> [!IMPORTANT]
> Pokud parametr vynecháte `--auth-mode` nebo ho nastavíte na `key` , pokusí se Azure CLI použít přístupový klíč k účtu pro autorizaci. V takovém případě společnost Microsoft doporučuje zadat přístupový klíč buď v příkazu, nebo v proměnné prostředí **AZURE_STORAGE_KEY** . Další informace o proměnných prostředí naleznete v části s názvem [nastavení proměnných prostředí pro parametry autorizace](#set-environment-variables-for-authorization-parameters).
>
> Pokud přístupový klíč nezadáte, pokusí se rozhraní příkazového řádku Azure volat poskytovatele prostředků Azure Storage a načíst ho pro každou operaci. Provádění mnoha operací s daty, které vyžadují volání poskytovatele prostředků, může způsobit omezení. Další informace o omezeních poskytovatele prostředků najdete v tématu [škálovatelnost a výkonnostní cíle pro poskytovatele prostředků Azure Storage](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autorizovat pomocí přihlašovacích údajů Azure AD

Když se přihlásíte k rozhraní příkazového řádku Azure CLI pomocí přihlašovacích údajů Azure AD, vrátí se přístupový token OAuth 2,0. Tento token se automaticky používá v Azure CLI k autorizaci následných operací s daty v úložišti objektů BLOB nebo Queue. U podporovaných operací už nemusíte předávat klíč účtu nebo token SAS pomocí příkazu.

Pomocí řízení přístupu založeného na rolích Azure (Azure RBAC) můžete přiřadit přístup k datům objektů BLOB k objektu zabezpečení Azure AD. Další informace o rolích Azure v Azure Storage najdete v tématu [Správa přístupových práv k datům Azure Storage pomocí Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Oprávnění pro volání operací s daty

Rozšíření Azure Storage jsou podporovaná pro operace s daty objektů BLOB. Operace, které můžete volat, závisí na oprávněních udělených objektu zabezpečení služby Azure AD, se kterým se přihlašujete ke službě Azure CLI. Oprávnění pro Azure Storage kontejnery jsou přiřazena prostřednictvím Azure RBAC. Pokud například přiřadíte roli **čtečky dat objektů BLOB úložiště** , můžete spustit skriptovací příkazy, které čtou data z kontejneru. Pokud jste přiřadili roli **Přispěvatel dat objektu BLOB úložiště** , můžete spustit skriptovací příkazy, které čtou, zapisují nebo odstraňují kontejner nebo data, která obsahuje.

Podrobnosti o oprávněních potřebných pro jednotlivé operace Azure Storage v kontejneru najdete v tématu [volání operací úložiště s tokeny OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Příklad: autorizovat operaci vytvoření kontejneru s přihlašovacími údaji Azure AD

Následující příklad ukazuje, jak vytvořit kontejner z Azure CLI pomocí vašich přihlašovacích údajů Azure AD. Pokud chcete vytvořit kontejner, budete se muset přihlásit do Azure CLI a budete potřebovat skupinu prostředků a účet úložiště. Informace o tom, jak vytvořit tyto prostředky, najdete v tématu [rychlý Start: vytvoření, stažení a výpis objektů BLOB pomocí Azure CLI](../blobs/storage-quickstart-blobs-cli.md).

1. Než vytvoříte kontejner, přiřaďte roli [Přispěvatel dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) sami sobě. I když jste vlastníkem účtu, potřebujete explicitní oprávnění k provádění operací s daty v účtu úložiště. Další informace o přiřazování rolí Azure najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům BLOB a front](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Rozšiřování přiřazení rolí Azure může trvat několik minut.

1. Zavolejte pomocí příkazu [AZ Storage Container Create](/cli/azure/storage/container#az-storage-container-create) s `--auth-mode` parametrem nastaveným na `login` , aby se vytvořil kontejner pomocí vašich přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorizovat pomocí přístupového klíče účtu

Pokud máte klíč účtu, můžete zavolat jakoukoli datovou operaci Azure Storage. Obecně platí, že použití klíče účtu je méně zabezpečené. Pokud je klíč účtu ohrožený, může dojít k ohrožení zabezpečení všech dat ve vašem účtu.

Následující příklad ukazuje, jak vytvořit kontejner pomocí přístupového klíče účtu. Zadejte klíč účtu a zadejte `--auth-mode` parametr s `key` hodnotou:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!IMPORTANT]
> Když je účet úložiště zamčený s Azure Resource Manager zámek **jen pro čtení** , není pro tento účet úložiště povolená operace se [seznamem klíčů](/rest/api/storagerp/storageaccounts/listkeys) . **Seznam klíčů** je operace post a všechny operace post jsou znemožněny, když je pro tento účet nakonfigurován zámek **ReadOnly** . Z tohoto důvodu, když je účet uzamčený pomocí zámku **jen pro čtení** , musí uživatelé přistupovat k datům pomocí přihlašovacích údajů Azure AD.

## <a name="authorize-with-a-sas-token"></a>Autorizovat pomocí tokenu SAS

Máte-li token SAS, můžete volat operace s daty, které jsou povoleny pomocí SAS. Následující příklad ukazuje, jak vytvořit kontejner pomocí tokenu SAS:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Nastavení proměnných prostředí pro autorizační parametry

Můžete zadat parametry autorizace v proměnných prostředí, aby se zabránilo jejich zahrnutí při každém volání operace Azure Storage data. Následující tabulka popisuje dostupné proměnné prostředí.

| Proměnná prostředí                  | Description                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    Název účtu úložiště. Tato proměnná by se měla používat ve spojení s klíčem účtu úložiště nebo tokenem SAS. Pokud žádná není, Azure CLI se pokusí získat přístupový klíč účtu úložiště pomocí ověřeného účtu Azure AD. Pokud se spustí velký počet příkazů najednou, může být dosaženo limitu omezování Azure Storage poskytovatele prostředků. Další informace o omezeních poskytovatele prostředků najdete v tématu [škálovatelnost a výkonnostní cíle pro poskytovatele prostředků Azure Storage](../common/scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    Klíč účtu úložiště. Tato proměnná se musí používat ve spojení s názvem účtu úložiště.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Připojovací řetězec, který obsahuje klíč účtu úložiště nebo token SAS. Tato proměnná se musí používat ve spojení s názvem účtu úložiště.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Token sdíleného přístupového podpisu (SAS). Tato proměnná se musí používat ve spojení s názvem účtu úložiště.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Režim ověřování, ve kterém se má příkaz Spustit. Povolené hodnoty jsou `login` (doporučeno) nebo `key` . Pokud zadáte `login` , rozhraní příkazového řádku Azure CLI použije přihlašovací údaje Azure AD k autorizaci operace s daty. Pokud zadáte starší `key` režim, pokusí se Azure CLI zadat dotaz na přístupový klíč účtu a ověřit příkaz pomocí klíče.    |

## <a name="next-steps"></a>Další kroky

- [Přiřazení role Azure pro přístup k datům objektů BLOB a front pomocí Azure CLI](../common/storage-auth-aad-rbac-cli.md)
- [Autorizace přístupu k datům BLOB a Queue pomocí spravovaných identit pro prostředky Azure](../common/storage-auth-aad-msi.md)