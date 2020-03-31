---
title: 'REST API: Operace souborového systému v Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft'
description: Použití rozhraní API WebHDFS REST k provádění operací systému souborů v síti Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 351c92f1e1a698893f61004d523ba79ebca253e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878779"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operace souborového systému v Azure Data Lake Storage Gen1 pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [Sada SDK rozhraní .NET](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [ROZHRANÍ API PRO ODPOČINEK](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

V tomto článku se dozvíte, jak používat rozhraní API WEBHDFS REST a rozhraní API Storage Storage 1 Data Lake k provádění operací systému souborů v síti Azure Data Lake Storage Gen1. Pokyny k provádění operací správy účtů v rozhraní Data Lake Storage Gen1 pomocí rozhraní REST API naleznete [v tématu Operace správy účtu v rozhraní Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Storage Gen1**. Postupujte podle pokynů na [webu Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure](data-lake-store-get-started-portal.md).

* **[cURL](https://curl.haxx.se/)**. Tento článek používá cURL k předvedení, jak provádět volání rozhraní REST API proti účtu Gen1 úložiště datového jezera.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Jak můžu ověřovat pomocí služby Azure Active Directory?
Ověřování pomocí služby Azure Active Directory můžete provádět dvěma přístupy.

* Ověření pro vaši aplikaci koncovým uživatelem (interaktivní) najdete [v tématu Ověřování koncových uživatelů pomocí funkce Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Ověřování mezi službami pro vaši aplikaci (neinteraktivní) najdete v [tématu Ověřování mezi službami pomocí služby Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Vytváření složek
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Použijte následující příkaz cURL. Nahraďte ** \<název svého>** s názvem účtu Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

V předchozím příkazu nahraďte položku \<`REDACTED`\> autorizačním tokenem, který jste získali dříve. Tento příkaz vytvoří adresář s názvem **mytempdir** pod kořenovou složkou účtu Data Lake Storage Gen1.

Pokud se operace úspěšně dokončí, měla by se zobrazit odpověď podobná následujícímu fragmentu kódu:

    {"boolean":true}

## <a name="list-folders"></a>Výpis složek
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Použijte následující příkaz cURL. Nahraďte ** \<název svého>** s názvem účtu Data Lake Storage Gen1.

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
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Použijte následující příkaz cURL. Nahraďte ** \<název svého>** s názvem účtu Data Lake Storage Gen1.

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
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Čtení dat z účtu Data Lake Storage Gen1 je dvoustupňový proces.

* Nejdřív odešlete požadavek GET na koncový bod `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Toto volání vrátí umístění, na které je nutné odeslat další požadavek GET.
* Potom odešlete požadavek GET na koncový bod `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Toto volání zobrazí obsah souboru.

Vstupní parametry v prvním a druhém kroku se ale nijak neliší, a proto můžete parametr `-L` použít k odeslání prvního požadavku. Možnost `-L` v podstatě kombinuje dva požadavky do jednoho. Výsledkem je, že cURL zopakuje požadavek na novém umístění. Nakonec se zobrazí výstup ze všech volání požadavků, který bude vypadat přibližně jako v následujícím fragmentu kódu. Nahraďte ** \<název svého>** s názvem účtu Data Lake Storage Gen1.

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
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Pokud chcete přejmenovat soubor, použijte následující příkaz cURL. Nahraďte ** \<název svého>** s názvem účtu Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Odstranění souboru
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Pokud chcete odstranit soubor, použijte následující příkaz cURL. Nahraďte ** \<název svého>** s názvem účtu Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Zobrazený výstup by měl vypadat asi takto:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Další kroky
* [Operace správy účtů na Data Lake Storage Gen1 pomocí rozhraní REST API](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Viz také
* [Azure Data Lake Storage Gen1 REST API Reference](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aplikace s velkým objemem dat s otevřeným zdrojovým kódem kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

