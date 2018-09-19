---
title: 'Rozhraní REST API: Operace správy účtů v Azure Data Lake Storage Gen1 | Dokumentace Microsoftu'
description: Použití Azure Data Lake Storage Gen1 a WebHDFS REST API k provádění operací správy účtů v účtu Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 7f22fe7d1c3962e59922bc4e2795ed4f899e3eca
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121664"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operace správy účtů v Azure Data Lake Storage Gen1 pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

V tomto článku se dozvíte, jak provádět operace správy účtů v Azure Data Lake Storage Gen1 pomocí rozhraní REST API. Operace správy účtů patří vytvoření účtu Data Lake Storage Gen1, odstranění účtu Data Lake Storage Gen1 atd. Pokyny k provádění operací systému souborů ve službě Data Lake Storage Gen1 pomocí rozhraní REST API najdete v tématu [operace systému souborů v Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](http://curl.haxx.se/)**. Tento článek používá cURL k předvedení jak volat rozhraní REST API vůči účtu Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Jak můžu ověřovat pomocí služby Azure Active Directory?
Ověřování pomocí služby Azure Active Directory můžete provádět dvěma přístupy.

* Ověřování koncového uživatele pro vaši aplikaci (interaktivní), najdete v části [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Ověřování služba služba (neinteraktivní) aplikace, najdete v části [ověřování služba služba Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1
Tato operace je založená na volání rozhraní REST API, které je definované [tady](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Použijte následující příkaz cURL. Nahraďte  **\<yourstoragegen1name >** názvem Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Ve výše uvedeném příkazu nahraďte položku \<`REDACTED`\> autorizačním tokenem, který jste získali dříve. Datová část požadavku tohoto příkazu se nachází v souboru **input.json** poskytnutém pro parametr `-d` výše. Obsah souboru input.json vypadá přibližně jako následující fragment kódu:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Odstranění účtu Data Lake Storage Gen1
Tato operace je založená na volání rozhraní REST API, které je definované [tady](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Odstranění účtu Data Lake Storage Gen1 použijte následující příkaz cURL. Nahraďte  **\<yourstoragegen1name >** názvem svého účtu Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Další postup
* [Operace systému souborů v Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Reference k rozhraní API REST Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aplikace typu Open zdroj velké objemy dat kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

