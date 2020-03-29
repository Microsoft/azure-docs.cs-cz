---
title: Požadavky na úložiště objektů blob datové schránky Microsoft Azure| Dokumenty společnosti Microsoft
description: Informace o podporovaných verzích pro api, sady SDK a klientské knihovny pro úložiště objektů blob datové schránky Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436490"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Požadavky na úložiště objektů blob datové schránky Azure

Tento článek uvádí verze azure api, klientské knihovny Azure a nástroje podporované úložiště objektů blob datové schránky. Úložiště objektů blob datové schránky poskytuje funkce správy objektů blob s sémantikou konzistentní s Azure. Tento článek také shrnuje známé rozdíly v úložišti objektů blob datové hospovy Azure od služeb Azure Storage.

Doporučujeme, abyste si tyto informace před připojením k úložišti objektů blob datové schránky pečlivě prostudovali a podle potřeby se k ní vrátili.


## <a name="storage-differences"></a>Rozdíly v úložišti

|     Funkce                                             |     Azure Storage                                     |     Úložiště objektů blob Data Boxu |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Podporované sdílené složky SMB založené na cloudu              |    Nepodporuje se      |
|    Šifrování služby pro data v klidovém stavu                  |    256bitové šifrování AES                             |    256bitové šifrování AES |
|    Typ účtu úložiště                                 |    Účty úložiště objektů blob pro obecné účely a Azure    |    Pouze pro obecné účely v1|
|    Název objektu blob                                            |    1 024 znaků (2 048 bajtů)                     |    880 znaků (1 760 bajtů)|
|    Maximální velikost objektu blob bloku                              |    4,75 TB (100 MB X 50 000 bloků)                   |    4.75 TB (100 MB x 50 000 bloků) pro Azure Data Box v 1.8 a dále.|
|    Maximální velikost objektu blob stránky                               |    8 TB                                               |    1 TB                   |
|    Velikost stránky objektu blob                                  |    512 bajtů                                          |    4 kB                   |

## <a name="supported-api-versions"></a>Podporované verze rozhraní API

Následující verze api služby Azure Storage jsou podporované s úložištěm objektů blob datové schránky:

Azure Data Box 1.8 a dále

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)|
## <a name="supported-azure-client-libraries"></a>Podporované klientské knihovny Azure

Pro úložiště objektů blob datové schránky existují specifické klientské knihovny a požadavky na konkrétní příponu koncového bodu. Koncové body úložiště blob datové schránky nemají úplnou paritu s nejnovější verzí rozhraní REST rozhraní AZURE blob Storage, viz [podporované verze pro Azure Data Box 1.8 a dále](#supported-api-versions). Pro knihovny klienta úložiště, musíte být vědomi verze, která je kompatibilní s rozhraním REST API.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 a dále

| Klientská knihovna     |Verze podporovaná úložištěm datové schránky     | Odkaz   |     Specifikace koncového bodu      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Nuget balíček:https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>GitHub vydání:https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    soubor app.config                 |
|    Java                |    7.0.0                                           |    Balíček Maven:https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>GitHub vydání:https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Nastavení připojovacího řetězce         |
|    Node.js             |    2.8.3                                           |    NPM odkaz: https://www.npmjs.com/package/azure-storage (Běh: `npm install azure-storage@2.7.0`)   <br>GitHub vydání:https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Deklarace instance služby    |
|    C++                 |    5.2.0                                           |    Nuget balíček:https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>GitHub vydání:https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Nastavení připojovacího řetězce         |
|    PHP                 |    1.2.0                                           |    GitHub vydání:<br>Společné:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Blob:https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Instalace přes Composer (Další informace naleznete v následujících podrobnostech.)                                                                                                             |    Nastavení připojovacího řetězce         |
|    Python              |    1.1.0                                           |    GitHub vydání:<br>Společné:https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob:https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Deklarace instance služby    |
|    Ruby                |    1.0.1                                           |    Balíček RubyGems:<br>Společné:https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob:https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>GitHub vydání:<br>Společné:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob:https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Nastavení připojovacího řetězce         |



### <a name="install-php-client-via-composer---current"></a>Instalace KLIENTA PHP přes Composer - aktuální

Chcete-li nainstalovat přes Composer: (vezměte blob jako příklad).
1. Vytvořte soubor s názvem composer.json v kořenovém adresáři projektu s následujícím kódem:

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Stáhněte si `composer.phar` do kořenového adresáře projektu.

3. Běh: php composer.phar nainstalovat.

### <a name="endpoint-declaration"></a>Deklarace koncového bodu

Koncový bod úložiště objektů blob Azure Data Box obsahuje dvě části: název oblasti a domény datové schránky. V sada SDK úložiště objektů blob datové `\<serial no. of the device>.microsoftdatabox.com`schránky je výchozí koncový bod .  Další informace o koncovém bodě služby blob najdete v části [Připojení přes úložiště objektů blob datové schránky](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Příklady

### <a name="net"></a>.NET

Pro úložiště objektů blob datové schránky je v `app.config` souboru zadána přípona koncového bodu:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Pro úložiště objektů blob datové schránky je přípona koncového bodu určena v nastavení připojovacího řetězce:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Pro úložiště objektů blob datové schránky je přípona koncového bodu určena v instanci deklarace:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Pro úložiště objektů blob datové schránky je přípona koncového bodu určena v nastavení připojovacího řetězce:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Pro úložiště objektů blob datové schránky je přípona koncového bodu určena v nastavení připojovacího řetězce:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Pro úložiště objektů blob datové schránky je přípona koncového bodu určena v instanci deklarace:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Pro úložiště objektů blob datové schránky je přípona koncového bodu určena v nastavení připojovacího řetězce:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Další kroky

* [Nasazení datové schránky Azure](data-box-deploy-ordered.md)
