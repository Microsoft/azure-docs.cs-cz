---
title: Autorizace přístupu k datům objektů blob nebo fronty pomocí azure CLI
titleSuffix: Azure Storage
description: Určete, jak autorizovat datové operace proti datům objektů blob nebo fronty pomocí azure CLI. Datové operace můžete autorizovat pomocí přihlašovacích údajů Azure AD, pomocí přístupového klíče účtu nebo pomocí tokenu sdíleného přístupového podpisu (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207690"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Autorizace přístupu k datům objektů blob nebo fronty pomocí azure CLI

Azure Storage poskytuje rozšíření pro Azure CLI, které umožňují určit, jak chcete autorizovat operace na datech objektů blob nebo fronty. Datové operace můžete autorizovat následujícími způsoby:

- S objektzabezpečení Azure Active Directory (Azure AD). Společnost Microsoft doporučuje používat přihlašovací údaje Azure AD pro lepší zabezpečení a snadné použití.
- Pomocí přístupového klíče účtu nebo tokenu sdíleného přístupového podpisu (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Určení způsobu autorizace datových operací

Příkazy Rozhraní příkazového řádku Azure pro čtení a `--auth-mode` zápis dat objektů blob a fronty zahrnují volitelný parametr. Zadejte tento parametr, který označuje, jak má být operace dat autorizována:

- Nastavte `--auth-mode` parametr `login` na přihlášení pomocí objektu zabezpečení Azure AD (doporučeno).
- Nastavte `--auth-mode` parametr na `key` starší hodnotu a pokuste se načíst přístupový klíč účtu, který chcete použít pro autorizaci. Pokud parametr vynesete, `--auth-mode` pokusí se rozhraní příkazového řádku Azure také načíst přístupový klíč.

Chcete-li `--auth-mode` použít parametr, ujistěte se, že jste nainstalovali Azure CLI verze 2.0.46 nebo novější. Spuštěním `az --version` zkontrolujte nainstalovanou verzi.

> [!IMPORTANT]
> Pokud `--auth-mode` parametr vynesete nebo `key`nastavíte na , pokusí se rozhraní příkazového řádku Azure použít klíč pro přístup k účtu pro autorizaci. V takovém případě společnost Microsoft doporučuje zadat přístupový klíč příkazem nebo v proměnné **prostředí AZURE_STORAGE_KEY.** Další informace o proměnných prostředí naleznete v části [set proměnné prostředí pro parametry autorizace](#set-environment-variables-for-authorization-parameters).
>
> Pokud nezadáte přístupový klíč, pak Azure CLI pokusí volání zprostředkovatele prostředků Úložiště Azure načíst pro každou operaci. Provádění mnoha datových operací, které vyžadují volání zprostředkovatele prostředků může mít za následek omezení. Další informace o omezeních zprostředkovatele prostředků najdete v tématu [Škálovatelnost a cíle výkonu pro poskytovatele prostředků úložiště Azure](scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autorizace pomocí přihlašovacích údajů Azure AD

Když se přihlásíte k Azure CLI pomocí přihlašovacích údajů Azure AD, vrátí se přístupový token OAuth 2.0. Tento token se automaticky používá azure CLI k autorizaci následných datových operací proti úložišti objektů Blob nebo Fronty. U podporovaných operací již nemusíte s příkazem předavat klíč účtu nebo token SAS.

Data objektů blob a fronty můžete přiřadit k datům objektů blob a fronty k objektu zabezpečení Azure AD prostřednictvím řízení přístupu založeného na rolích (RBAC). Další informace o rolích RBAC ve službě Azure Storage najdete v [tématu Správa přístupových práv k datům služby Azure Storage pomocí RBAC](storage-auth-aad-rbac.md).

### <a name="permissions-for-calling-data-operations"></a>Oprávnění pro volání datových operací

Rozšíření úložiště Azure jsou podporované pro operace s daty objektů blob a fronty. Operace, které můžete volat, závisí na oprávněních udělených objektu zabezpečení Azure AD, pomocí kterého se přihlásíte k azure cli. Oprávnění k kontejnerům nebo frontám úložiště Azure se přiřazují prostřednictvím RBAC. Pokud je například přiřazena role **Čtečka dat objektů Blob,** můžete spustit skriptovací příkazy, které čtou data z kontejneru nebo fronty. Pokud je vám přiřazena role **Přispěvatel dat objektů Blob,** můžete spustit skriptovací příkazy, které čtou, zapisují nebo odstraňují kontejner nebo frontu nebo data, která obsahují.

Podrobnosti o oprávněních požadovaných pro každou operaci úložiště Azure v kontejneru nebo frontě najdete v [tématu Volání operací úložiště s tokeny OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Příklad: Autorizace operace k vytvoření kontejneru s přihlašovacími údaji Azure AD

Následující příklad ukazuje, jak vytvořit kontejner z Azure CLI pomocí přihlašovacích údajů Azure AD. Pokud chcete vytvořit kontejner, budete se muset přihlásit k azure cli a budete potřebovat skupinu prostředků a účet úložiště. Informace o tom, jak tyto prostředky vytvořit, najdete [v tématu Úvodní příručka: Vytvoření, stažení a seznam objektů BLOB pomocí rozhraní příkazového příkazového příkazového příkazu k andazure](../blobs/storage-quickstart-blobs-cli.md).

1. Před vytvořením kontejneru přiřaďte roli [přispěvatele dat objektů blob úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) sami sobě. I když jste vlastníkem účtu, budete potřebovat explicitní oprávnění k provádění operací dat proti účtu úložiště. Další informace o přiřazování rolí RBAC najdete [v tématu Udělení přístupu k datům objektů blob Azure a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Přiřazení rolí RBAC může trvat několik minut k šíření.

1. Volání [az kontejner úložiště](/cli/azure/storage/container#az-storage-container-create) vytvořit `--auth-mode` příkaz `login` s parametrem nastaveným pro vytvoření kontejneru pomocí přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorizace pomocí přístupového klíče účtu

Pokud máte klíč účtu, můžete volat libovolnou operaci dat Azure Storage. Obecně platí, že použití klíče účtu je méně bezpečné. Pokud dojde k ohrožení zabezpečení klíče účtu, mohou být ohrožena všechna data ve vašem účtu.

Následující příklad ukazuje, jak vytvořit kontejner pomocí přístupového klíče účtu. Zadejte klíč účtu a `--auth-mode` zadejte `key` parametr s hodnotou:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorizace pomocí tokenu SAS

Pokud máte token SAS, můžete volat operace dat, které jsou povoleny SAS. Následující příklad ukazuje, jak vytvořit kontejner pomocí tokenu SAS:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Nastavení proměnných prostředí pro parametry autorizace

Můžete zadat parametry autorizace v proměnných prostředí, aby se zabránilo jejich zahrnutí při každém volání operace dat Azure Storage. Následující tabulka popisuje dostupné proměnné prostředí.

| Proměnná prostředí                  | Popis                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    Název účtu úložiště. Tato proměnná by měla být použita ve spojení s klíčem účtu úložiště nebo tokenem SAS. Pokud ani nejsou k dispozici, Azure CLI pokusí načíst přístupový klíč účtu úložiště pomocí ověřeného účtu Azure AD. Pokud se spouští velký počet příkazů najednou, může být dosaženo omezení omezení zprostředkovatele úložiště Azure. Další informace o omezeních zprostředkovatele prostředků najdete v tématu [Škálovatelnost a cíle výkonu pro poskytovatele prostředků úložiště Azure](scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    Klíč účtu úložiště. Tato proměnná musí být použita ve spojení s názvem účtu úložiště.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Připojovací řetězec, který obsahuje klíč účtu úložiště nebo token SAS. Tato proměnná musí být použita ve spojení s názvem účtu úložiště.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Token sdíleného přístupového podpisu (SAS). Tato proměnná musí být použita ve spojení s názvem účtu úložiště.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Režim autorizace, se kterým chcete spustit příkaz. Povolené hodnoty `login` jsou (doporučeny) nebo `key`. Pokud zadáte `login`, Azure CLI používá vaše přihlašovací údaje Azure AD k autorizaci operace dat. Pokud zadáte starší `key` režim, Azure CLI pokusí dotaz na klíč přístupu k účtu a autorizovat příkaz s klíčem.    |

## <a name="next-steps"></a>Další kroky

- [Použití azure cli k přiřazení role RBAC pro přístup k datům objektů blob a fronty](storage-auth-aad-rbac-cli.md)
- [Autorizace přístupu k datům objektů blob a fronty se spravovanými identitami pro prostředky Azure](storage-auth-aad-msi.md)
