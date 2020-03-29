---
title: 'REST API: Operace správy účtů v Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft'
description: Použití rozhraní Azure Data Lake Storage Gen1 a rozhraní REST WebHDFS k provádění operací správy účtů v účtu Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877100"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operace správy účtů na Azure Data Lake Storage Gen1 pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [Sada SDK rozhraní .NET](data-lake-store-get-started-net-sdk.md)
> * [ROZHRANÍ API PRO ODPOČINEK](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

V tomto článku se dozvíte, jak provádět operace správy účtů na Azure Data Lake Storage Gen1 pomocí rozhraní REST API. Operace správy účtu zahrnují vytvoření účtu Data Lake Storage Gen1, odstranění účtu Data Lake Storage Gen1 atd. Pokyny k provádění operací systému souborů v rozhraní Data Lake Storage Gen1 pomocí rozhraní REST API naleznete [v tématu Operace souborového systému v zařízení Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. Tento článek používá cURL k předvedení, jak provádět volání rozhraní REST API proti účtu Gen1 úložiště datového jezera.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Jak můžu ověřovat pomocí služby Azure Active Directory?
Ověřování pomocí služby Azure Active Directory můžete provádět dvěma přístupy.

* Ověření pro vaši aplikaci koncovým uživatelem (interaktivní) najdete [v tématu Ověřování koncových uživatelů pomocí funkce Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Ověřování mezi službami pro vaši aplikaci (neinteraktivní) najdete v [tématu Ověřování mezi službami pomocí služby Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1
Tato operace je založená na volání rozhraní REST API, které je definované [tady](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Použijte následující příkaz cURL. Nahraďte ** \<>storagegen1name** názvem Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Ve výše uvedeném příkazu nahraďte položku \<`REDACTED`\> autorizačním tokenem, který jste získali dříve. Datová část požadavku tohoto příkazu se nachází v souboru **input.json** poskytnutém pro parametr `-d` výše. Obsah souboru input.json vypadá přibližně jako následující fragment kódu:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Odstranění účtu Gen1 úložiště datového jezera
Tato operace je založená na volání rozhraní REST API, které je definované [tady](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Pomocí následujícího příkazu cURL odstraňte účet Data Lake Storage Gen1. Nahraďte ** \<>vašeho úložištěgen1name** názvem názvem vašeho účtu Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Další kroky
* [Operace souborového systému na Data Lake Storage Gen1 pomocí ROZHRANÍ REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Viz také
* [Azure Data Lake Storage Gen1 REST API Reference](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aplikace s velkým objemem dat s otevřeným zdrojovým kódem kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

