---
title: Začínáme s Azure Stack nástroje pro vývoj úložišť | Dokumentace Microsoftu
description: Pokyny, které vám umožní začít pomocí nástroje pro vývoj úložišť Azure Stack
services: azure-stack
author: mattbriggs
ms.author: mabrigg
ms.date: 12/03/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 071d58f53389367833df6379c68c27ecc4771fa1
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238818"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Začínáme s Azure Stack nástroje pro vývoj úložišť

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Microsoft Azure Stack nabízí sadu služeb úložiště, který obsahuje objekt blob, table a queue storage.

Použijte tento článek jako vodítko a začněte využívat nástroje pro vývoj úložišť Azure Stack. Podrobnější informace a ukázky kódu najdete v odpovídající kurzy služby Azure storage.

> [!NOTE]
> Jsou známy rozdíly mezi úložiště služby Azure Stack a Azure storage, včetně specifické požadavky pro jednotlivé platformy. Například existují konkrétní klientské knihovny a určitého koncového bodu přípona požadavky pro Azure Stack. Další informace najdete v tématu [úložiště služby Azure Stack: Rozdíly a aspekty](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Klientské knihovny Azure

Pro klientské knihovny pro úložiště mějte na paměti, která je kompatibilní s rozhraním REST API verze. Ve vašem kódu, musíte zadat také koncový bod služby Azure Stack.

### <a name="1811-update-or-newer-versions"></a>1811 update nebo novější verze

| Klientská knihovna | Podporovaná verze služby Azure Stack | Odkaz | Koncový bod specifikace |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Balíček Nuget:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | souboru app.config |
| Java | 6.1.0 | Maven balíček:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Nastavení připojovací řetězce |
| Node.js | 2.7.0 | Odkaz na NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Spuštění: `npm install azure-storage@2.7.0`)<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Deklarace instance služby |
| C++ | 3.1.0 | Balíček Nuget:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Verze Githubu:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Nastavení připojovací řetězce |
| PHP | 1.0.0 | Verze Githubu:<br>Běžné: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Objekt BLOB: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Fronta:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tabulka: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Instalace přes Composer (Další informace, [viz podrobnosti níže](#install-php-client-via-composer---current).) | Nastavení připojovací řetězce |
| Python | 1.0.0 | Verze Githubu:<br>Běžné:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Objekt BLOB:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Fronta:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Deklarace instance služby |
| Ruby | 1.0.1 | Balíček RubyGems:<br>Běžné:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Objekt BLOB: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Fronta: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tabulka: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Verze Githubu:<br>Běžné: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Objekt BLOB: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Fronta: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tabulka: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Nastavení připojovací řetězce |

#### <a name="install-php-client-via-composer---current"></a>Instalace klienta PHP prostřednictvím autora – aktuální

Chcete-li nainstalovat prostřednictvím autora: (trvat objektů blob jako příklad).

1. Vytvořte soubor s názvem **composer.json** v kořenovém adresáři projektu s následujícím kódem:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Stáhněte si [composer.phar](http://getcomposer.org/composer.phar) do kořenového adresáře projektu.
3. Spustit: `php composer.phar install`.

### <a name="previous-versions-1802-to-1809-update"></a>Předchozí verze (aktualizace 1802 k 1809)

|Klientská knihovna|Podporovaná verze služby Azure Stack|Odkaz|Koncový bod specifikace|
|---------|---------|---------|---------|
|.NET     |6.2.0|Balíček Nuget:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Verze Githubu:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|souboru app.config|
|Java|4.1.0|Maven balíček:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Verze Githubu:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Nastavení připojovací řetězce|
|Node.js     |1.1.0|Odkaz na NPM:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(run: `npm install azure-storage@1.1.0)`<br><br>Verze Githubu:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Deklarace instance služby||C++|2.4.0|Balíček Nuget:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Verze Githubu:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Nastavení připojovací řetězce|
|C++|2.4.0|Balíček Nuget:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Verze Githubu:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Nastavení připojovací řetězce|
|PHP|0.15.0|Verze Githubu:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Instalace přes Composer (viz podrobnosti níže)|Nastavení připojovací řetězce|
|Python     |0.30.0|Balíček PIP:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Spuštění: `pip install -v azure-storage==0.30.0)`<br><br>Verze Githubu:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Deklarace instance služby|
|Ruby|0.12.1<br>Preview|Balíček RubyGems:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Verze Githubu:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Nastavení připojovací řetězce|

#### <a name="install-php-client-via-composer---previous"></a>Instalace klienta PHP prostřednictvím autora - předchozí

Chcete-li nainstalovat prostřednictvím autora: (take blob jako příklad).

1. Vytvořte soubor s názvem **composer.json** v kořenovém adresáři projektu s následujícím kódem:

  ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```

2. Stáhněte si [composer.phar](http://getcomposer.org/composer.phar) do kořenového adresáře projektu.
3. Spustit: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Koncový bod deklarace

Koncový bod služby Azure Stack zahrnuje dvě části: název oblasti a doméně služby Azure Stack.
V Azure Stack Development Kit, je výchozí koncový bod **local.azurestack.external**.
Pokud si nejste jisti o váš koncový bod, obraťte se na správce cloudu.

## <a name="examples"></a>Příklady

### <a name="net"></a>.NET

Pro Azure Stack, která je zadaná přípona koncového bodu v souboru app.config:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Pro službu Azure Stack přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Pro službu Azure Stack je přípona koncového bodu podle instance deklarace:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Pro službu Azure Stack přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Pro službu Azure Stack přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```php
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Pro službu Azure Stack je přípona koncového bodu podle instance deklarace:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Pro službu Azure Stack přípona koncového bodu je zadán v nastavení připojovacího řetězce:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Blob Storage

V následujících kurzech úložiště objektů Blob v Azure se vztahují na služby Azure Stack. Poznámka: požadavek přípona určitého koncového bodu pro službu Azure Stack je popsáno v předchozí [příklady](#examples) oddílu.

* [Začínáme s úložištěm Azure Blob pomocí rozhraní .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Používání úložiště Blob z Javy](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Používání úložiště Blob z Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Používání úložiště Blob z jazyka C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Používání úložiště Blob z PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Jak používat Azure Blob storage z Pythonu](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Používání úložiště Blob z Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Queue Storage

Následující kurzy Azure Queue storage se vztahují na služby Azure Stack. Poznámka: požadavek přípona určitého koncového bodu pro službu Azure Stack je popsáno v předchozí [příklady](#examples) oddílu.

* [Začínáme s úložištěm Azure Queue pomocí rozhraní .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Používání úložiště Queue z Javy](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Používání úložiště Queue z Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Používání úložiště Queue z jazyka C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Používání úložiště Queue z PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Používání úložiště Queue z Pythonu](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Používání úložiště Queue z Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)

## <a name="table-storage"></a>Úložiště Table

Následující kurzy Azure Table storage se vztahují na služby Azure Stack. Poznámka: požadavek přípona určitého koncového bodu pro službu Azure Stack je popsáno v předchozí [příklady](#examples) oddílu.

* [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Používání úložiště Table z Javy](../../cosmos-db/table-storage-how-to-use-java.md)
* [Používání tabulkového úložiště Azure z Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Používání úložiště Table z jazyka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Používání úložiště Table z PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Jak používat úložiště tabulek v Pythonu](../../cosmos-db/table-storage-how-to-use-python.md)
* [Používání úložiště Table z Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Další postup

* [Úvod do Microsoft Azure storage](../../storage/common/storage-introduction.md)
