---
title: Správa účtu Azure Data Lake Storage Gen1 s REST
description: Pomocí REST API WebHDFS můžete provádět operace správy účtů na účtu Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 13467a51b2a06dbc0ca0ec5eadd139fde8b82ad0
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103488"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operace správy účtů u Azure Data Lake Storage Gen1 pomocí REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

V tomto článku se dozvíte, jak provádět operace správy účtů na Azure Data Lake Storage Gen1 pomocí REST API. Mezi operace správy účtů patří vytvoření účtu Data Lake Storage Gen1, odstranění účtu Data Lake Storage Gen1 atd. Pokyny k provádění operací systému souborů v Data Lake Storage Gen1 pomocí REST API naleznete v tématu [operace systému souborů v Data Lake Storage Gen1 pomocí REST API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Předpoklady
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[kudrlinkou](https://curl.haxx.se/)**. V tomto článku se pomocí oblé předvádí způsob, jak REST API volání na účet Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Jak můžu ověřovat pomocí služby Azure Active Directory?
Ověřování pomocí služby Azure Active Directory můžete provádět dvěma přístupy.

* Pro ověřování koncových uživatelů vaší aplikace (interaktivní) si přečtěte téma [ověřování koncových uživatelů s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Pro ověřování služeb u vaší aplikace (neinteraktivní), přečtěte si téma [ověřování služba-služba s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1
Tato operace je založená na volání rozhraní REST API, které je definované [tady](/rest/api/datalakestore/accounts/create).

Použijte následující příkaz cURL. Nahraďte **\<yourstoragegen1name>** názvem Data Lake Storage Gen1.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"
```

Ve výše uvedeném příkazu nahraďte položku \<`REDACTED`\> autorizačním tokenem, který jste získali dříve. Datová část požadavku tohoto příkazu se nachází v souboru **input.json** poskytnutém pro parametr `-d` výše. Obsah souboru input.json vypadá přibližně jako následující fragment kódu:

```json
{
"location": "eastus2",
"tags": {
    "department": "finance"
    },
"properties": {}
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Odstranit účet Data Lake Storage Gen1
Tato operace je založená na volání rozhraní REST API, které je definované [tady](/rest/api/datalakestore/accounts/delete).

K odstranění účtu Data Lake Storage Gen1 použijte následující příkaz složeného příkazu. Nahraďte **\<yourstoragegen1name>** názvem vašeho účtu Data Lake Storage Gen1.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview
```

Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

```output
HTTP/1.1 200 OK
...
...
```

## <a name="next-steps"></a>Další kroky
* [Operace systému souborů při Data Lake Storage Gen1 pomocí REST API](data-lake-store-data-operations-rest-api.md)

## <a name="see-also"></a>Viz také
* [Odkaz na Azure Data Lake Storage Gen1 REST API](/rest/api/datalakestore/)
* [Open Source aplikace pro velké objemy dat kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)