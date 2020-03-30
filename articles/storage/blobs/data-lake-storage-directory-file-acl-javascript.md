---
title: Použití JavaScriptu pro soubory & Seznamů ACV v Azure Data Lake Storage Gen2
description: Pomocí klientské knihovny Azure Storage Data Lake pro JavaScript můžete spravovat adresáře a seznamy řízení přístupu k souborům a adresářům (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061545"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí JavaScriptu

Tento článek ukazuje, jak pomocí jazyka JavaScript vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS). 

[Ukázky](https://www.npmjs.com/package/@azure/storage-file-datalake) |  | balíčku (Správce balíčků uzlů)[poskytují zpětnou vazbu](https://github.com/Azure/azure-sdk-for-java/issues) [Samples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolen hierarchický obor názvů (HNS). [Chcete-li](data-lake-storage-quickstart-create-account.md) jej vytvořit, postupujte podle těchto pokynů.
> * Pokud používáte tento balíček v aplikaci Node.js, budete potřebovat Node.js 8.0.0 nebo vyšší.

## <a name="set-up-your-project"></a>Nastavení projektu

Nainstalujte klientskou knihovnu Data Lake pro JavaScript otevřením okna terminálu a zadáním následujícího příkazu.

```javascript
npm install @azure/storage-file-datalake
```

Importujte `storage-file-datalake` balíček umístěním tohoto příkazu do horní části souboru kódu. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Připojení k účtu 

Chcete-li použít výstřižky v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient,** která představuje účet úložiště. 

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob připojení k účtu. 

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Tento způsob autorizace funguje pouze pro aplikace Node.js. Pokud máte v plánu spustit kód v prohlížeči, můžete autorizovat pomocí Služby Azure Active Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí služby Azure Active Directory (AD)

Klientská [knihovna identity Azure pro JS](https://www.npmjs.com/package/@azure/identity) můžete použít k ověření vaší aplikace pomocí Azure AD.

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajný klíč klienta a ID klienta.  Pokud chcete získat tyto hodnoty, najdete v článku [Získání tokenu z Azure AD pro autorizaci požadavků z klientské aplikace](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Další příklady najdete v [knihovně klienta identity Azure pro](https://www.npmjs.com/package/@azure/identity) dokumentaci JS.

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete ji vytvořit tak, že získáte instanci **FileSystemClient** a pak zavoláte metodu **FileSystemClient.Create.**

Tento příklad vytvoří systém `my-file-system`souborů s názvem . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář získáním instance **DirectoryClient** a následným voláním metody **DirectoryClient.create.**

Tento příklad přidá `my-directory` adresář s názvem do systému souborů. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář voláním metody **DirectoryClient.rename.** Předá cestu požadovaného adresáře parametr. 

Tento příklad přejmenuje podadresář na `my-directory-renamed`název .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Tento příklad přesune `my-directory-renamed` adresář s názvem do podadresáře adresáře s názvem `my-directory-2`. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář voláním metody **DirectoryClient.delete.**

Tento příklad odstraní adresář `my-directory`s názvem .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Správa seznamu ACL adresáře

Tento příklad získá a potom nastaví `my-directory`seznam ACL adresáře s názvem . Tento příklad poskytuje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, poskytuje vlastnící skupině pouze oprávnění ke čtení a spouštění a poskytuje všem ostatním přístup ke čtení.

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Služby Azure Active Directory (Azure AD), ujistěte se, že objekt zabezpečení, který vaše aplikace používá k autorizaci přístupu, byl přiřazen [roli vlastníka dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Další informace o použití oprávnění seznamu ACL a jejich efektech najdete [v tématu Řízení přístupu v azure datovém úložišti.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nejprve si přečtěte soubor. Tento příklad používá modul Node.js. `fs` Potom vytvořte odkaz na soubor v cílovém adresáři vytvořením instance **FileClient** a voláním metody **FileClient.create.** Nahrajte soubor voláním metody **FileClient.append.** Nezapomeňte dokončit nahrávání voláním metody **FileClient.flush.**

Tento příklad nahraje textový soubor `my-directory`do adresáře s názvem .'

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Správa souboru ACL

Tento příklad získá a potom nastaví `upload-file.txt`acl souboru s názvem . Tento příklad poskytuje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, poskytuje vlastnící skupině pouze oprávnění ke čtení a spouštění a poskytuje všem ostatním přístup ke čtení.

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Služby Azure Active Directory (Azure AD), ujistěte se, že objekt zabezpečení, který vaše aplikace používá k autorizaci přístupu, byl přiřazen [roli vlastníka dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Další informace o použití oprávnění seznamu ACL a jejich efektech najdete [v tématu Řízení přístupu v azure datovém úložišti.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>Stažení z adresáře

Nejprve vytvořte instanci **FileSystemClient,** která představuje soubor, který chcete stáhnout. Ke čtení souboru použijte metodu **FileSystemClient.read.** Potom napište soubor. Tento příklad k tomu používá `fs` modul Node.js. 

> [!NOTE]
> Tento způsob stahování souboru funguje pouze pro aplikace Node.js. Pokud máte v plánu spustit kód v prohlížeči, podívejte se na [azure storage file data lake klientská knihovna pro](https://www.npmjs.com/package/@azure/storage-file-datalake) soubor Readme JavaScript pro příklad, jak to udělat v prohlížeči. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

V tomto příkladu vytiskne názvy jednotlivých adresářů a souborů umístěných v adresáři s názvem `my-directory`.

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Viz také

* [Balíček (Správce balíčků uzlů)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-sdk-for-java/issues)