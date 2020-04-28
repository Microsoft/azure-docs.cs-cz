---
title: Požadavky na úložiště objektů BLOB v Microsoft Azure Data Box | Microsoft Docs
description: Přečtěte si o podporovaných verzích rozhraní API, sadách SDK a klientských knihovnách pro Azure Data Box BLOB Storage.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "61436490"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box požadavky na úložiště objektů BLOB

V tomto článku jsou uvedené verze rozhraní API Azure, klientských knihoven Azure a nástrojů podporovaných úložištěm objektů BLOB v Data Box. Data Box BLOB Storage poskytuje funkce správy objektů BLOB s sémantikou konzistentní vzhledem k Azure. Tento článek obsahuje také souhrn známých Azure Data Box objektů BLOB Storage od Azure Storage služeb.

Doporučujeme, abyste pečlivě prostudovali informace, než se připojíte k úložišti objektů BLOB Data Box, a pak se na něj v případě potřeby odkazuje zpátky.


## <a name="storage-differences"></a>Rozdíly v úložišti

|     Funkce                                             |     Azure Storage                                     |     Úložiště objektů blob Data Boxu |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Podporované cloudové sdílené složky SMB              |    Nepodporuje se      |
|    Šifrování služby pro neaktivní neaktivní data                  |    256 šifrování AES                             |    256 šifrování AES |
|    Typ účtu úložiště                                 |    Účty úložiště pro obecné účely a Azure Blob Storage    |    Jenom pro obecné účely v1|
|    Název objektu blob                                            |    1 024 znaků (2 048 bajtů)                     |    880 znaků (1 760 bajtů)|
|    Maximální velikost objektu blob bloku                              |    4,75 TB (100 MB X 50 000 bloků)                   |    4,75 TB (100 MB x 50 000 bloků) pro Azure Data Box v 1,8 a vyšší.|
|    Maximální velikost objektu blob stránky                               |    8 TB                                               |    1 TB                   |
|    Velikost stránky objektu blob stránky                                  |    512 bajtů                                          |    4 kB                   |

## <a name="supported-api-versions"></a>Podporované verze rozhraní API

Pro úložiště objektů BLOB v Data Box jsou podporovány následující verze rozhraní Azure Storage Service API:

Azure Data Box 1,8 a vyšší

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Podporované klientské knihovny Azure

Pro Data Box BLOB Storage existují konkrétní klientské knihovny a specifické požadavky na příponu koncových bodů. Koncové body služby Data Box BLOB Storage nemají úplnou paritu s nejnovější verzí REST API Azure Blob Storage, přečtěte si [podporované verze pro Azure Data Box 1,8 a vyšší](#supported-api-versions). Pro klientské knihovny pro úložiště je potřeba znát verzi, která je kompatibilní s REST API.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1,8 a vyšší

| Klientská knihovna     |Verze podporované úložištěm objektů BLOB Data Box     | Odkaz   |     Specifikace koncového bodu      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Balíček NuGet:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>Verze GitHubu:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    soubor App. config                 |
|    Java                |    7.0.0                                           |    Balíček Maven:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Verze GitHubu:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Nastavení připojovacího řetězce         |
|    Node.js             |    2.8.3                                           |    Odkaz na NPM https://www.npmjs.com/package/azure-storage : (Run `npm install azure-storage@2.7.0`:)   <br>Verze GitHubu:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Deklarace instance služby    |
|    C++                 |    5.2.0                                           |    Balíček NuGet:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>Verze GitHubu:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Nastavení připojovacího řetězce         |
|    PHP                 |    1.2.0                                           |    Verze GitHubu:<br>Obecnýhttps://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Příznakyhttps://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Instalovat přes skladatele (Další informace najdete v podrobnostech níže)                                                                                                             |    Nastavení připojovacího řetězce         |
|    Python              |    1.1.0                                           |    Verze GitHubu:<br>Obecnýhttps://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Příznakyhttps://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Deklarace instance služby    |
|    Ruby                |    1.0.1                                           |    Balíček RubyGems:<br>Obecnýhttps://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Příznakyhttps://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Verze GitHubu:<br>Obecnýhttps://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Příznakyhttps://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Nastavení připojovacího řetězce         |



### <a name="install-php-client-via-composer---current"></a>Instalovat klienta PHP prostřednictvím skladatele – aktuální

Instalace prostřednictvím skladatele: (jako příklad Vezměte objekt BLOB jako příklad).
1. V kořenovém adresáři projektu vytvořte soubor s názvem skladatel. JSON s následujícím kódem:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Stáhněte `composer.phar` do kořenového adresáře projektu.

3. Spuštění: instalace PHP skladatele. phar.

### <a name="endpoint-declaration"></a>Deklarace koncového bodu

Koncový bod služby Azure Data Box BLOB Storage zahrnuje dvě části: název oblasti a doménu Data Box. V sadě Data Box BLOB Storage je `\<serial no. of the device>.microsoftdatabox.com`výchozím koncovým bodem.  Další informace o koncovém bodu služby BLOB Service najdete v pro [připojení prostřednictvím data box BLOB Storage](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Příklady

### <a name="net"></a>.NET

V případě úložiště objektů BLOB Data Box je v `app.config` souboru Zadaná přípona koncového bodu:

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
