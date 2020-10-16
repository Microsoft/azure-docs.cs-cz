---
title: 'REST API: operace systému souborů v Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Použití rozhraní REST API WebHDFS k provádění operací systému souborů na Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 51e0fb2ffa7b573ecfeda163d9ad99597ff735a2
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109200"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operace systému souborů při Azure Data Lake Storage Gen1 pomocí REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

V tomto článku se dozvíte, jak používat rozhraní REST API WebHDFS a Data Lake Storage Gen1 rozhraní REST API k provádění operací systému souborů v Azure Data Lake Storage Gen1. Pokyny k provádění operací správy účtů na Data Lake Storage Gen1 pomocí REST API najdete v tématu [operace správy účtů na data Lake Storage Gen1 pomocí REST API](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Předpoklady
* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Data Lake Storage Gen1**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-get-started-portal.md).

* **[kudrlinkou](https://curl.haxx.se/)**. V tomto článku se pomocí oblé předvádí způsob, jak REST API volání na účet Data Lake Storage Gen1.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Jak můžu ověřovat pomocí služby Azure Active Directory?
Ověřování pomocí služby Azure Active Directory můžete provádět dvěma přístupy.

* Pro ověřování koncových uživatelů vaší aplikace (interaktivní) si přečtěte téma [ověřování koncových uživatelů s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Pro ověřování služeb u vaší aplikace (neinteraktivní), přečtěte si téma [ověřování služba-služba s Data Lake Storage Gen1 pomocí sady .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Vytváření složek
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Použijte následující příkaz cURL. Nahraďte **\<yourstorename>** názvem vašeho účtu Data Lake Storage Gen1.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'
```

V předchozím příkazu nahraďte položku \<`REDACTED`\> autorizačním tokenem, který jste získali dříve. Tento příkaz vytvoří v kořenové složce účtu Data Lake Storage Gen1 adresář s názvem **MyTempDir** .

Pokud se operace úspěšně dokončí, měla by se zobrazit odpověď podobná následujícímu fragmentu kódu:

```output
{"boolean":true}
```

## <a name="list-folders"></a>Výpis složek
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Použijte následující příkaz cURL. Nahraďte **\<yourstorename>** názvem vašeho účtu Data Lake Storage Gen1.

```console
curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'
```

V předchozím příkazu nahraďte položku \<`REDACTED`\> autorizačním tokenem, který jste získali dříve.

Pokud se operace úspěšně dokončí, měla by se zobrazit odpověď podobná následujícímu fragmentu kódu:

```output
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
```

## <a name="upload-data"></a>Nahrání dat
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Použijte následující příkaz cURL. Nahraďte **\<yourstorename>** názvem vašeho účtu Data Lake Storage Gen1.

```console
curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'
```

V předchozí syntaxi je parametr **-T** umístění nahrávaného souboru.

Výstup je podobný následujícímu fragmentu kódu:
   
```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
...
Content-Length: 0

HTTP/1.1 100 Continue

HTTP/1.1 201 Created
...
```

## <a name="read-data"></a>Čtení dat
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Čtení dat z Data Lake Storage Gen1 účtu je proces se dvěma kroky.

* Nejdřív odešlete požadavek GET na koncový bod `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Toto volání vrátí umístění, na které je nutné odeslat další požadavek GET.
* Potom odešlete požadavek GET na koncový bod `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Toto volání zobrazí obsah souboru.

Vstupní parametry v prvním a druhém kroku se ale nijak neliší, a proto můžete parametr `-L` použít k odeslání prvního požadavku. Možnost `-L` v podstatě kombinuje dva požadavky do jednoho. Výsledkem je, že cURL zopakuje požadavek na novém umístění. Nakonec se zobrazí výstup ze všech volání požadavků, který bude vypadat přibližně jako v následujícím fragmentu kódu. Nahraďte **\<yourstorename>** názvem vašeho účtu Data Lake Storage Gen1.

```console
curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'
```

Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
...

HTTP/1.1 200 OK
...

Hello, Data Lake Store user!
```

## <a name="rename-a-file"></a>Přejmenování souboru
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Pokud chcete přejmenovat soubor, použijte následující příkaz cURL. Nahraďte **\<yourstorename>** názvem vašeho účtu Data Lake Storage Gen1.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'
```

Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="delete-a-file"></a>Odstranění souboru
Tato operace je založená na volání rozhraní REST API WebHDFS, které je definované [tady](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Pokud chcete odstranit soubor, použijte následující příkaz cURL. Nahraďte **\<yourstorename>** názvem vašeho účtu Data Lake Storage Gen1.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'
```

Zobrazený výstup by měl vypadat asi takto:

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="next-steps"></a>Další kroky
* [Operace správy účtů na data Lake Storage Gen1 pomocí REST API](data-lake-store-get-started-rest-api.md)

## <a name="see-also"></a>Viz také
* [Odkaz na Azure Data Lake Storage Gen1 REST API](/rest/api/datalakestore/)
* [Open Source aplikace pro velké objemy dat kompatibilní s Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)