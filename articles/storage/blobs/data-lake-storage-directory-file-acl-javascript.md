---
title: Použití JavaScriptu ke správě dat v Azure Data Lake Storage Gen2
description: Pomocí Azure Storage Data Lake klientské knihovny pro JavaScript můžete spravovat adresáře a soubory v účtech úložiště s povoleným hierarchickým oborem názvů.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 8ce5df805ddce6cdb52e4225bb77e2d8dfa9b9b0
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650163"
---
# <a name="use-javascript-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Správa adresářů a souborů v Azure Data Lake Storage Gen2 pomocí JavaScriptu

V tomto článku se dozvíte, jak pomocí JavaScriptu vytvářet a spravovat adresáře a soubory v účtech úložiště, které mají hierarchický obor názvů.

Další informace o tom, jak získat, nastavit a aktualizovat seznamy řízení přístupu (ACL) adresářů a souborů, najdete v tématu [použití JavaScriptu ke správě seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-javascript.md).

[Balíček (Správce balíčků uzlů)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště s povoleným hierarchickým oborem názvů. Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

- Pokud tento balíček používáte v Node.js aplikaci, budete potřebovat Node.js 8.0.0 nebo vyšší.

## <a name="set-up-your-project"></a>Nastavení projektu

Nainstalujte Data Lake klientské knihovny pro JavaScript tak, že otevřete okno terminálu a pak zadáte následující příkaz.

```javascript
npm install @azure/storage-file-datalake
```

Importujte `storage-file-datalake` balíček umístěním tohoto příkazu v horní části souboru kódu. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Připojit k účtu 

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. 

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu. 

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
> Tato metoda autorizace funguje jenom pro Node.js aplikace. Pokud máte v úmyslu spustit kód v prohlížeči, můžete autorizovat pomocí Azure Active Directory (Azure AD).

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Připojení pomocí Azure Active Directory (Azure AD)

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro js](https://www.npmjs.com/package/@azure/identity) .

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajného klíče klienta a ID tenanta.  Pokud chcete získat tyto hodnoty, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md).

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro js](https://www.npmjs.com/package/@azure/identity) .

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner funguje jako systém souborů pro vaše soubory. Můžete ji vytvořit získáním instance **FileSystemClient** a následným voláním metody **FileSystemClient. Create** .

Tento příklad vytvoří kontejner s názvem `my-file-system` . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář získáním instance **DirectoryClient** a následným voláním metody **DirectoryClient. Create** .

Tento příklad přidá adresář s názvem `my-directory` do kontejneru. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář voláním metody **DirectoryClient. rename** . Předejte cestu k požadovanému adresáři do parametru. 

Tento příklad přejmenuje podadresář na název `my-directory-renamed` .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Tento příklad přesune adresář s názvem `my-directory-renamed` do podadresáře adresáře s názvem `my-directory-2` . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář voláním metody **DirectoryClient. Delete** .

Tento příklad odstraní adresář s názvem `my-directory` .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nejprve si přečtěte soubor. V tomto příkladu se používá `fs` modul Node.js. Pak vytvořte odkaz na soubor v cílovém adresáři tak, že vytvoříte instanci **klienta** souborů a potom zavoláte metodu **klient. Create** . Nahrajte soubor voláním metody **klient. Append** . Ujistěte se, že jste dokončí nahrávání voláním metody **klient. Flush** .

Tento příklad nahraje textový soubor do adresáře s názvem `my-directory` . '

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

## <a name="download-from-a-directory"></a>Stažení z adresáře

Nejprve vytvořte instanci **FileSystemClient** , která představuje soubor, který chcete stáhnout. K načtení souboru použijte metodu **FileSystemClient. Read** . Pak zapište soubor. V tomto příkladu se `fs` k tomu používá modul Node.js. 

> [!NOTE]
> Tato metoda stažení souboru funguje pouze pro Node.js aplikace. Pokud máte v úmyslu spustit kód v prohlížeči, přečtěte si soubor [Azure Storage soubor Data Lake klientské knihovny pro JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) Readme, kde najdete příklad toho, jak to provést v prohlížeči.

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

Tento příklad vytiskne názvy jednotlivých adresářů a souborů, které jsou umístěny v adresáři s názvem `my-directory` .

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

- [Balíček (Správce balíčků uzlů)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-java/issues)