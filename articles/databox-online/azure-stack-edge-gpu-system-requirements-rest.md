---
title: Požadavky na úložiště objektů BLOB v Microsoft Azure Stack Edge | Microsoft Docs
description: Přečtěte si o podporovaných verzích rozhraní API, sadách SDK a klientských knihovnách pro úložiště objektů blob Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 7deba32eb9d0e098b75f98cc81fac2c01b8bb7f8
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567249"
---
# <a name="azure-stack-edge-blob-storage-requirements"></a>Požadavky na úložiště objektů BLOB v Azure Stack Edge

Tento článek obsahuje seznam verzí rozhraní API Azure, klientských knihoven Azure a nástrojů podporovaných úložištěm objektů blob Azure Stack Edge. Úložiště objektů BLOB v Azure Stack Edge poskytuje funkce správy objektů BLOB s sémantikou konzistentní s Azure. Tento článek obsahuje také souhrn známých rozdílů v úložišti objektů BLOB v Azure Stack hraničních zařízeních od Azure Storage Services.

Doporučujeme pečlivě zkontrolovat informace, než se připojíte k úložišti objektů blob Azure Stack Edge, a pak se na něj v případě potřeby odkazuje zpátky.

## <a name="storage-differences"></a>Rozdíly v úložišti

|     Funkce                                             |     Azure Storage                                     |     Azure Stack úložiště objektů BLOB Edge |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure File Storage                                   |    Podporované cloudové sdílené složky SMB              |    Nepodporováno      |
|    Typ účtu úložiště                                 |    Účty úložiště pro obecné účely a Azure Blob Storage    |    Jenom pro obecné účely v1|
|    Název objektu blob                                            |    1 024 znaků (2 048 bajtů)                     |    880 znaků (1 760 bajtů)|
|    Maximální velikost objektu blob bloku                              |    4,75 TB (100 MB X 50 000 bloků)                   |    4,75 TB (100 MB x 50 000 bloků) pro Azure Stack Edge|
|    Maximální velikost objektu blob stránky                               |    8 TB                                               |    1 TB                   |
|    Velikost stránky objektu blob stránky                                  |    512 bajtů                                          |    4 kB                   |

## <a name="supported-api-versions"></a>Podporované verze rozhraní API

Pro úložiště objektů BLOB v Azure Stack Edge se podporují následující verze rozhraní Azure Storage Service API.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 a vyšší

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>Podporované klientské knihovny Azure

U Azure Stackového úložiště objektů BLOB jsou k dispozici konkrétní klientské knihovny a specifické požadavky na příponu koncových bodů. Koncové body úložiště objektů blob Azure Stack Edge nemají úplnou paritu s nejnovější verzí služby Azure Blob Storage REST API; Podívejte se na [podporované verze rozhraní API pro Azure Stack Edge](#supported-api-versions). Pro klientské knihovny pro úložiště je potřeba znát verzi, která je kompatibilní s REST API.

### <a name="azure-stack-edge-2113772170-onwards"></a>Azure Stack Edge 2.1.1377.2170 a vyšší

Následující verze klientské knihovny Azure jsou podporovány pro úložiště objektů blob Azure Stack Edge.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-the-php-client-via-composer---current"></a>Instalace klienta PHP prostřednictvím skladatele – aktuální

Instalace klienta PHP prostřednictvím skladatele:

1. V kořenovém adresáři projektu vytvořte soubor s názvem composer.jss následujícím kódem (příklad používá službu Azure Storage Blob).

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Stáhněte `composer.phar` do kořenového adresáře projektu.

3. Spuštění: instalace PHP skladatele. phar.


## <a name="endpoint-declaration"></a>Deklarace koncového bodu

V sadě SDK pro úložiště objektů BLOB v Azure Stack Edge určuje přípona koncového bodu `<device serial number>.microsoftdatabox.com` Azure Stack hraniční doménu. Další informace o koncovém bodu služby BLOB Service najdete v části [přenos dat prostřednictvím účtů úložiště s grafickým procesorem Azure Stack Edge pro](./azure-stack-edge-gpu-deploy-add-storage-accounts.md).


## <a name="examples"></a>Příklady

### <a name="net"></a>.NET

V případě Azure Stackho úložiště objektů BLOB Edge je přípona koncového bodu zadaná v `app.config` souboru:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

V případě Azure Stackho úložiště objektů BLOB Edge je přípona koncového bodu určená v nastavení připojovacího řetězce:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

V případě Azure Stackho úložiště objektů BLOB Edge je přípona koncového bodu zadaná v instanci deklarace:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

V případě Azure Stackho úložiště objektů BLOB Edge je přípona koncového bodu určená v nastavení připojovacího řetězce:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

V případě Azure Stackho úložiště objektů BLOB Edge je přípona koncového bodu určená v nastavení připojovacího řetězce:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

V případě Azure Stackho úložiště objektů BLOB Edge je přípona koncového bodu zadaná v instanci deklarace:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

V případě Azure Stackho úložiště objektů BLOB Edge je přípona koncového bodu určená v nastavení připojovacího řetězce:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Další kroky

* [Příprava na nasazení Azure Stack Edge pro pomocí GPU](azure-stack-edge-gpu-deploy-prep.md)