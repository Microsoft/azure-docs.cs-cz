---
title: 'Rozhraní REST API: Operace systému souborů v Azure Data Lake Storage Gen1 | Dokumentace Microsoftu'
description: Použití rozhraní REST API WebHDFS k provádění operací systému souborů ve službě Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 62ecf3b1983853629f6bc5fd594231188aa67bcd
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391602"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operace systému souborů v Azure Data Lake Storage Gen1 pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

V tomto článku se dozvíte, jak k provádění operací systému souborů ve službě Azure Data Lake Storage Gen1 pomocí REST API WebHDFS a rozhraní REST API služby Data Lake Storage Gen1. Pokyny, jak provádět operace správy účtů v Data Lake Storage Gen1 pomocí rozhraní REST API najdete v tématu [operace správy účtů v Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Storage Gen1**. Postupujte podle pokynů na adrese [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure portal](data-lake-store-get-started-portal.md).

* **[cURL](http://curl.haxx.se/)**. Tento článek používá cURL k předvedení jak volat rozhraní REST API vůči účtu Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Jak můžu ověřovat pomocí služby Azure Active Directory?
Ověřování pomocí služby Azure Active Directory můžete provádět dvěma přístupy.

* Ověřování koncového uživatele pro vaši aplikaci (interaktivní), najdete v části [ověřování koncového uživatele s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Ověřování služba služba (neinteraktivní) aplikace, najdete v části [ověřování služba služba Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Vytváření složek
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Použijte následující příkaz cURL. Nahraďte  **\<yourstorename >** názvem svého účtu Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

V předchozím příkazu nahraďte položku \<`REDACTED`\> autorizačním tokenem, který jste získali dříve. Tento příkaz vytvoří adresář s názvem **mytempdir** v kořenové složce vašeho účtu Data Lake Storage Gen1.

Pokud se operace úspěšně dokončí, měla by se zobrazit odpověď podobná následujícímu fragmentu kódu:

    {"boolean":true}

## <a name="list-folders"></a>Výpis složek
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Použijte následující příkaz cURL. Nahraďte  **\<yourstorename >** názvem svého účtu Data Lake Storage Gen1.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

V předchozím příkazu nahraďte položku \<`REDACTED`\> autorizačním tokenem, který jste získali dříve.

Pokud se operace úspěšně dokončí, měla by se zobrazit odpověď podobná následujícímu fragmentu kódu:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Nahrání dat
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Použijte následující příkaz cURL. Nahraďte  **\<yourstorename >** názvem svého účtu Data Lake Storage Gen1.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

V předchozí syntaxi je parametr **-T** umístění nahrávaného souboru.

Výstup je podobný následujícímu fragmentu kódu:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Čtení dat
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Čtení dat ze služby Data Lake Storage Gen1 účtu je dvoustupňový proces.

* Nejdřív odešlete požadavek GET na koncový bod `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Toto volání vrátí umístění, na které je nutné odeslat další požadavek GET.
* Potom odešlete požadavek GET na koncový bod `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Toto volání zobrazí obsah souboru.

Vstupní parametry v prvním a druhém kroku se ale nijak neliší, a proto můžete parametr `-L` použít k odeslání prvního požadavku. Možnost `-L` v podstatě kombinuje dva požadavky do jednoho. Výsledkem je, že cURL zopakuje požadavek na novém umístění. Nakonec se zobrazí výstup ze všech volání požadavků, který bude vypadat přibližně jako v následujícím fragmentu kódu. Nahraďte  **\<yourstorename >** názvem svého účtu Data Lake Storage Gen1.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Přejmenování souboru
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Pokud chcete přejmenovat soubor, použijte následující příkaz cURL. Nahraďte  **\<yourstorename >** názvem svého účtu Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Odstranění souboru
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Pokud chcete odstranit soubor, použijte následující příkaz cURL. Nahraďte  **\<yourstorename >** názvem svého účtu Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Zobrazený výstup by měl vypadat asi takto:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Další postup
* [Operace správy účtů v Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Reference k rozhraní API REST Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aplikace typu Open zdroj velké objemy dat kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

