---
title: Začínáme s Azure zásobníku úložiště vývojové nástroje | Microsoft Docs
description: Pokyny, jak začít s pomocí nástrojů pro vývoj Azure zásobník úložiště
services: azure-stack
author: mabriggs
ms.author: mabrigg
ms.date: 05/21/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 0ceda393412f8217a893a347ec5f3a9ac03efa3d
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604473"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Začínáme s Azure zásobníku úložiště vývojové nástroje

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Zásobník Microsoft Azure poskytuje sadu služby úložiště, která obsahuje objekt blob, table a queue storage.

Abyste mohli začít používat Azure zásobníku úložiště vývojové nástroje, použijte tento článek jako vodítko. Podrobnější informace a ukázkový kód můžete najít v odpovídající kurzech úložiště Azure.

> [!NOTE]  
> Existují známé rozdíly mezi zásobník Azure storage a Azure storage, včetně specifické požadavky pro každou platformu. Například existují určité klientské knihovny a konkrétní koncový bod příponu požadavky pro Azure zásobníku. Další informace najdete v tématu [Azure zásobníku úložiště: rozdíly a aspekty](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Knihovny klienta Azure

Podporované verze rozhraní REST API pro Azure zásobníku úložiště jsou 2017-04-17, 2016-05-31, 2015-12-11, 2015-07-08, 2015-04-05 1802 aktualizace nebo novější verze a 2015-04-05 pro předchozí verze. Koncové body Azure zásobníku nemají úplná parita s nejnovější verzi rozhraní API REST Azure storage. Pro knihovny klienta úložiště musíte znát verze, která je kompatibilní s rozhraním REST API.

### <a name="1802-update-or-newer-versions"></a>1802 aktualizace nebo novější verze

| Klientská knihovna | Azure zásobníku podporovaná verze | Odkaz | Koncový bod specifikace |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Balíček Nuget:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | soubor App.config |
| Java | 6.1.0 | Balíček maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Nastavení připojení řetězce |
| Node.js | 2.7.0 | NPM odkaz:<br>https://www.npmjs.com/package/azure-storage<br>(Spustit: `npm install azure-storage@2.7.0`)<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Deklarace instance služby |
| C++ | 3.1.0 | Balíček Nuget:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Nastavení připojení řetězce |
| PHP | 1.0.0 | Verze Githubu:<br>Běžné: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Objekt BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Fronty:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tabulka: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Nainstalovat prostřednictvím autora (Další informace, [najdete v níže uvedené podrobnosti](#install-php-client-via-composer---current).) | Nastavení připojení řetězce |
| Python | 1.0.0 | Verze Githubu:<br>Běžné:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Objekt BLOB:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Fronty:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Deklarace instance služby |
| Ruby | 1.0.1 | Balíček RubyGems:<br>Běžné:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Objekt BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Fronty: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tabulka: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Verze Githubu:<br>Běžné: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Objekt BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Fronty: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tabulka: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Nastavení připojení řetězce |

#### <a name="install-php-client-via-composer---current"></a>Instalace klienta PHP prostřednictvím autora - aktuální

Chcete-li nainstalovat prostřednictvím autora: (blob proveďte jako příklad).

1. Vytvořte soubor s názvem **composer.json** v kořenu projektu s následujícím kódem:

  ```php
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```

2. Stáhněte si [composer.phar](http://getcomposer.org/composer.phar) do kořenového adresáře projektu.
3. Spustit: `php composer.phar install`.

### <a name="previous-versions"></a>Předchozí verze

|Klientská knihovna|Azure zásobníku podporovaná verze|Odkaz|Koncový bod specifikace|
|---------|---------|---------|---------|
|.NET     |6.2.0|Balíček Nuget:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Verze Githubu:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|soubor App.config|
|Java|4.1.0|Balíček maven:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Verze Githubu:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Nastavení připojení řetězce|
|Node.js     |1.1.0|NPM odkaz:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(spustit: `npm install azure-storage@1.1.0)`<br><br>Verze Githubu:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Deklarace instance služby||C++|2.4.0|Balíček Nuget:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Verze Githubu:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Nastavení připojení řetězce|
|C++|2.4.0|Balíček Nuget:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Verze Githubu:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Nastavení připojení řetězce|
|PHP|0.15.0|Verze Githubu:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Nainstalovat prostřednictvím autora (viz níže podrobnosti)|Nastavení připojení řetězce|
|Python     |0.30.0|Balíček PIP:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Spustit: `pip install -v azure-storage==0.30.0)`<br><br>Verze Githubu:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Deklarace instance služby|
|Ruby|0.12.1<br>Preview|Balíček RubyGems:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Verze Githubu:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Nastavení připojení řetězce|

#### <a name="install-php-client-via-composer---previous"></a>Instalace klienta PHP prostřednictvím autora - předchozí

Chcete-li nainstalovat prostřednictvím autora:

1. Vytvořte soubor s názvem **composer.json** v kořenu projektu s následujícím kódem:

  ```php
    {
          "require":{
          "Microsoft/azure-storage":"0.15.0"
          }
    }
  ```

2. Stáhněte si [composer.phar](http://getcomposer.org/composer.phar) do kořenu projektu.
3. Spustit: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Koncový bod deklarace

Koncový bod Azure Stack zahrnuje dvě části: název, oblast a doména Azure zásobníku.
V sadě Azure zásobníku Development Kit je výchozí koncový bod **local.azurestack.external**.
Pokud si nejste jistí o váš koncový bod, obraťte se na správce cloudu.

## <a name="examples"></a>Příklady

### <a name="net"></a>.NET

Pro Azure zásobníku zadána přípona koncový bod v souboru app.config:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Pro Azure zásobníku zadána přípona koncového bodu v nastavení připojovacího řetězce:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Pro Azure zásobníku zadána přípona koncového bodu v deklaraci instance:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Pro Azure zásobníku zadána přípona koncového bodu v nastavení připojovacího řetězce:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Pro Azure zásobníku zadána přípona koncového bodu v nastavení připojovacího řetězce:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Pro Azure zásobníku zadána přípona koncového bodu v deklaraci instance:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Pro Azure zásobníku zadána přípona koncového bodu v nastavení připojovacího řetězce:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

Následující kurzy úložiště objektů Blob v Azure se vztahují na Azure zásobníku. Poznámka: požadavek příponu konkrétní koncový bod Azure zásobníku popsané v předchozí [příklady](#examples) části.

* [Začínáme s úložištěm Azure Blob pomocí rozhraní .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Používání úložiště Blob z Javy](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Používání úložiště Blob z Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Používání úložiště Blob z jazyka C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Používání úložiště Blob z PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Jak používat Azure Blob storage z Pythonu](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Používání úložiště Blob z Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Následující kurzy Azure Queue storage se vztahují na Azure zásobníku. Poznámka: požadavek příponu konkrétní koncový bod Azure zásobníku popsané v předchozí [příklady](#examples) části.

* [Začínáme s úložištěm Azure Queue pomocí rozhraní .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Používání úložiště Queue z Javy](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Používání úložiště Queue z Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Postup používání úložiště Queue z jazyka C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Používání úložiště Queue z PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Používání úložiště Queue z Pythonu](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Používání úložiště Queue z Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)

## <a name="table-storage"></a>Úložiště Table

Následující kurzy úložiště Azure Table se vztahují na Azure zásobníku. Poznámka: požadavek příponu konkrétní koncový bod Azure zásobníku popsané v předchozí [příklady](#examples) části.

* [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Používání úložiště Table z Javy](../../cosmos-db/table-storage-how-to-use-java.md)
* [Používání tabulkového úložiště Azure z Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Postup používání úložiště Table z jazyka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Používání úložiště Table z PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Postup používání úložiště Table v Pythonu](../../cosmos-db/table-storage-how-to-use-python.md)
* [Používání úložiště Table z Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Další postup

* [Úvod do Microsoft Azure storage](../../storage/common/storage-introduction.md)