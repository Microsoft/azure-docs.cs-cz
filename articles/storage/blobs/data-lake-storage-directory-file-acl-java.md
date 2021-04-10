---
title: Správa dat v Azure Data Lake Storage Gen2 pomocí jazyka Java
description: Pomocí knihoven Azure Storage pro jazyk Java můžete spravovat adresáře a soubory v účtech úložiště s povoleným hierarchickým oborem názvů.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 10debe7bb870ddd9f8711e73ccb4b690d7011b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650181"
---
# <a name="use-java-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Správa adresářů a souborů v Azure Data Lake Storage Gen2 pomocí jazyka Java

V tomto článku se dozvíte, jak pomocí jazyka Java vytvářet a spravovat adresáře a soubory v účtech úložiště, které mají hierarchický obor názvů.

Další informace o tom, jak získat, nastavit a aktualizovat seznamy řízení přístupu (ACL) adresářů a souborů, najdete v tématu [použití. Java pro správu seznamů ACL v Azure Data Lake Storage Gen2](data-lake-storage-acl-java.md).

[Balíček (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  Reference k rozhraní [API](/java/api/overview/azure/storage-file-datalake-readme)  |  Mapování Gen1 na [Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště s povoleným hierarchickým oborem názvů. Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

## <a name="set-up-your-project"></a>Nastavení projektu

Začněte tím, že otevřete [tuto stránku](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) a získáte nejnovější verzi knihovny Java. Pak otevřete soubor *pom.xml* v textovém editoru. Přidejte element závislosti, který odkazuje na tuto verzi.

Pokud plánujete ověřování klientské aplikace pomocí služby Azure Active Directory (Azure AD), přidejte závislost do klientské knihovny tajného kódu Azure. Viz  [Přidání balíčku tajné klientské knihovny do projektu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Dále přidejte tyto příkazy import do souboru kódu.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Připojit k účtu

Pokud chcete používat fragmenty kódu v tomto článku, budete muset vytvořit instanci **DataLakeServiceClient** , která představuje účet úložiště. 

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu. 

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Připojení pomocí Azure Active Directory (Azure AD)

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro jazyk Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajného klíče klienta a ID tenanta.  Pokud chcete získat tyto hodnoty, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro jazyk Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner funguje jako systém souborů pro vaše soubory. Můžete jej vytvořit zavoláním metody **DataLakeServiceClient. createFileSystem** .

Tento příklad vytvoří kontejner s názvem `my-file-system` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář voláním metody **DataLakeFileSystemClient. createDirectory** .

Tento příklad přidá adresář s názvem `my-directory` do kontejneru a následně přidá podadresář s názvem `my-subdirectory` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář voláním metody **DataLakeDirectoryClient. rename** . Předejte cestu k požadovanému adresáři do parametru. 

Tento příklad přejmenuje podadresář na název `my-subdirectory-renamed` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

Tento příklad přesune adresář s názvem `my-subdirectory-renamed` do podadresáře adresáře s názvem `my-directory-2` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář voláním metody **DataLakeDirectoryClient. deleteWithResponse** .

Tento příklad odstraní adresář s názvem `my-directory` .   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nejprve vytvořte odkaz na soubor v cílovém adresáři vytvořením instance třídy **DataLakeFileClient** . Nahrajte soubor voláním metody **DataLakeFileClient. Append** . Ujistěte se, že jste dokončí nahrávání voláním metody **DataLakeFileClient. FlushAsync** .

Tento příklad nahraje textový soubor do adresáře s názvem `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> Pokud je velikost souboru velká, váš kód bude muset provést více volání metody **DataLakeFileClient. Append** . Místo toho zvažte použití metody **DataLakeFileClient. uploadFromFile** . Tímto způsobem můžete nahrát celý soubor v jednom volání. 
>
> Příklad najdete v další části.

## <a name="upload-a-large-file-to-a-directory"></a>Nahrání velkého souboru do adresáře

Pro nahrání velkých souborů použijte metodu **DataLakeFileClient. uploadFromFile** , aniž byste museli provádět více volání metody **DataLakeFileClient. Append** .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Stažení z adresáře

Nejprve vytvořte instanci **DataLakeFileClient** , která představuje soubor, který chcete stáhnout. K načtení souboru použijte metodu **DataLakeFileClient. Read** . K uložení bajtů z datového proudu do souboru použijte libovolné rozhraní API pro zpracování souborů .NET. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Tento příklad vytiskne názvy jednotlivých souborů, které jsou umístěny v adresáři s názvem `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Viz také

* [Referenční dokumentace k rozhraní API](/java/api/overview/azure/storage-file-datalake-readme)
* [Balíček (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapování Gen1 na Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-java/issues)