---
title: Požadavky na úložiště objektů BLOB v Microsoft Azure Data Box | Microsoft Docs
description: Přečtěte si o podporovaných verzích rozhraní API, sadách SDK a klientských knihovnách pro Azure Data Box BLOB Storage.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: ac5f2de383066d6ee399dac3b0ad8c365b2e72bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91744086"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box požadavky na úložiště objektů BLOB

V tomto článku jsou uvedené verze rozhraní API Azure, klientských knihoven Azure a nástrojů podporovaných úložištěm objektů BLOB v Data Box. Data Box BLOB Storage poskytuje funkce správy objektů BLOB s sémantikou konzistentní vzhledem k Azure. Tento článek obsahuje také souhrn známých Azure Data Box objektů BLOB Storage od Azure Storage služeb.

Doporučujeme, abyste pečlivě prostudovali informace, než se připojíte k úložišti objektů BLOB Data Box, a pak se na něj v případě potřeby odkazuje zpátky.


## <a name="storage-differences"></a>Rozdíly v úložišti

|     Funkce                                             |     Azure Storage                                     |     Úložiště objektů blob Data Boxu |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Podporované cloudové sdílené složky SMB              |    Nepodporováno      |
|    Šifrování služby pro neaktivní neaktivní data                  |    256 šifrování AES                             |    256 šifrování AES |
|    Typ účtu úložiště                                 |    Účty úložiště pro obecné účely a Azure Blob Storage    |    Jenom pro obecné účely v1|
|    Název objektu blob                                            |    1 024 znaků (2 048 bajtů)                     |    880 znaků (1 760 bajtů)|
|    Maximální velikost objektu blob bloku                              |    4,75 TB (100 MB X 50 000 bloků)                   |    4,75 TB (100 MB x 50 000 bloků) pro Azure Data Box v 3,0 a vyšší.|
|    Maximální velikost objektu blob stránky                               |    8 TB                                               |    1 TB                   |
|    Velikost stránky objektu blob stránky                                  |    512 bajtů                                          |    4 kB                   |

## <a name="supported-api-versions"></a>Podporované verze rozhraní API

Pro úložiště objektů BLOB v Data Box jsou podporovány následující verze rozhraní API služby Azure Storage.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0 a vyšší

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Podporované klientské knihovny Azure

Pro Data Box BLOB Storage existují konkrétní klientské knihovny a specifické požadavky na příponu koncových bodů. Koncové body služby Data Box BLOB Storage nemají úplnou paritu s nejnovější verzí služby Azure Blob Storage REST API; Podívejte se na [podporované verze pro Azure Data Box 3,0 a vyšší](#supported-api-versions). Pro klientské knihovny pro úložiště je potřeba znát verzi, která je kompatibilní s REST API.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3,0 a vyšší

Pro Data Box BLOB Storage se podporují následující verze klientských knihoven Azure.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-php-client-via-composer---current"></a>Instalovat klienta PHP prostřednictvím skladatele – aktuální

Instalace prostřednictvím skladatele: (jako příklad Vezměte objekt BLOB jako příklad).
1. V kořenovém adresáři projektu vytvořte soubor s názvem composer.jss následujícím kódem:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Stáhněte `composer.phar` do kořenového adresáře projektu.

3. Spuštění: instalace PHP skladatele. phar.

### <a name="endpoint-declaration"></a>Deklarace koncového bodu

V sadě Data Box BLOB Storage SDK přípona koncového bodu `<device serial number>.microsoftdatabox.com` identifikuje doménu data box. Další informace o koncovém bodu služby BLOB Service najdete v části [připojení prostřednictvím data box BLOB Storage](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Příklady

### <a name="net"></a>.NET

V případě úložiště objektů BLOB Data Box je v souboru Zadaná přípona koncového bodu `app.config` :

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

V případě úložiště objektů BLOB Data Box je přípona koncového bodu určená v nastavení připojovacího řetězce:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

V případě úložiště objektů BLOB Data Box je přípona koncového bodu zadaná v instanci deklarace:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

V případě úložiště objektů BLOB Data Box je přípona koncového bodu určená v nastavení připojovacího řetězce:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

V případě úložiště objektů BLOB Data Box je přípona koncového bodu určená v nastavení připojovacího řetězce:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

V případě úložiště objektů BLOB Data Box je přípona koncového bodu zadaná v instanci deklarace:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

V případě úložiště objektů BLOB Data Box je přípona koncového bodu určená v nastavení připojovacího řetězce:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Další kroky

* [Nasazení Azure Data Box](data-box-deploy-ordered.md)
