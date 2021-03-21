---
title: Použití JavaScriptu (Node.js) k nastavení seznamů ACL v Azure Data Lake Storage Gen2
description: Pomocí Azure Storage Data Lake klientské knihovny pro JavaScript můžete spravovat seznamy řízení přístupu (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 21b4977102a484d8a3a680450a9cb6f77c7e3fbd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722748"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Použití sady JavaScript SDK v Node.js ke správě seznamů ACL v Azure Data Lake Storage Gen2

V tomto článku se dozvíte, jak pomocí Node.js získat, nastavit a aktualizovat seznamy řízení přístupu adresářů a souborů. 

[Balíček (Správce balíčků uzlů)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

- Verze Azure CLI `2.6.0` nebo vyšší

- Jedno z následujících oprávnění zabezpečení:

  - Zřízeným [objektem zabezpečení](../../role-based-access-control/overview.md#security-principal) služby Azure Active Directory (AD), kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového kontejneru, nadřazené skupiny prostředků nebo předplatného.  

  - Vlastnící uživatel cílového kontejneru nebo adresáře, pro který plánujete použít nastavení seznamu ACL. Chcete-li nastavit seznamy ACL rekurzivně, zahrnuje všechny podřízené položky v cílovém kontejneru nebo adresáři.
  
  - Klíč účtu úložiště...

## <a name="set-up-your-project"></a>Nastavení projektu

Nainstalujte Data Lake klientské knihovny pro JavaScript tak, že otevřete okno terminálu a pak zadáte následující příkaz.

```javascript
npm install @azure/storage-file-datalake
```

Importujte `storage-file-datalake` balíček umístěním tohoto příkazu v horní části souboru kódu. 

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Připojit k účtu

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Připojení pomocí Azure Active Directory (AD)

> [!NOTE]
> Pokud k autorizaci přístupu používáte Azure Active Directory (Azure AD), ujistěte se, že je vašemu objektu zabezpečení přiřazená [role vlastníka dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Další informace o tom, jak se používají oprávnění seznamu řízení přístupu a důsledky jejich změny, najdete  [v tématu model řízení přístupu v Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro js](https://www.npmjs.com/package/@azure/identity) .

Získejte ID klienta, tajný klíč klienta a ID tenanta. Pokud to chcete provést, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md). V rámci tohoto procesu budete muset k objektu zabezpečení přiřadit jednu z následujících rolí [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) . 

|Role|Funkce nastavení seznamu ACL|
|--|--|
|[Vlastník dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Všechny adresáře a soubory v účtu.|
|[Přispěvatel dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Pouze adresáře a soubory vlastněné objektem zabezpečení.|

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajného klíče klienta a ID tenanta.  

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
> Tato metoda autorizace funguje jenom pro Node.js aplikace. Pokud máte v úmyslu spustit kód v prohlížeči, můžete autorizovat pomocí Azure Active Directory (AD).

## <a name="get-and-set-a-directory-acl"></a>Získání a nastavení seznamu ACL adresáře

Tento příklad načte a potom nastaví seznam řízení přístupu k adresáři s názvem `my-directory` . Tento příklad uděluje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním přístup pro čtení.

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Azure Active Directory (Azure AD), ujistěte se, že se k objektu zabezpečení, který vaše aplikace používá k autorizaci přístupu, přiřadila [role vlastníka dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma  [řízení přístupu v Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

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

Můžete také získat a nastavit seznam ACL kořenového adresáře kontejneru. Chcete-li získat kořenový adresář, předejte prázdný řetězec ( `/` ) do metody **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="get-and-set-a-file-acl"></a>Získání a nastavení seznamu ACL souboru

Tento příklad načte a potom nastaví seznam řízení přístupu k souboru s názvem `upload-file.txt` . Tento příklad uděluje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním přístup pro čtení.

> [!NOTE]
> Pokud vaše aplikace autorizuje přístup pomocí Azure Active Directory (Azure AD), ujistěte se, že se k objektu zabezpečení, který vaše aplikace používá k autorizaci přístupu, přiřadila [role vlastníka dat objektu BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma  [řízení přístupu v Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

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

## <a name="see-also"></a>Viz také

- [Balíček (Správce balíčků uzlů)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [ukázky](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Poskytnout zpětnou vazbu](https://github.com/Azure/azure-sdk-for-java/issues)
- [Model řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Seznamy řízení přístupu (ACL) v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)