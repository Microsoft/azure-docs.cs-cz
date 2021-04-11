---
title: Použití jazyka Java k nastavení seznamů ACL v Azure Data Lake Storage Gen2
description: Pomocí knihoven Azure Storage pro Java můžete spravovat seznamy řízení přístupu (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e7d6156fe5cd8ab32ff159bda64e0c06cfbac406
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654091"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Správa seznamů ACL v Azure Data Lake Storage Gen2 pomocí jazyka Java

V tomto článku se dozvíte, jak pomocí jazyka Java získat, nastavit a aktualizovat seznamy řízení přístupu adresářů a souborů. 

Dědičnost seznamů ACL je již k dispozici pro nové podřízené položky, které jsou vytvořeny v nadřazeném adresáři. Můžete ale také přidat, aktualizovat a odebrat seznamy ACL pro existující podřízené položky nadřazeného adresáře, aniž by bylo nutné provádět tyto změny jednotlivě pro každou podřízenou položku. 

[Balíček (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  Reference k rozhraní [API](/java/api/overview/azure/storage-file-datalake-readme)  |  Mapování Gen1 na [Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](create-data-lake-storage-account.md) pokynů.

- Verze Azure CLI `2.6.0` nebo vyšší

- Jedno z následujících oprávnění zabezpečení:

  - Zřízeným [objektem zabezpečení](../../role-based-access-control/overview.md#security-principal) služby Azure Active Directory (AD), kterému byla přiřazena role [vlastníka dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) v oboru cílového kontejneru, nadřazené skupiny prostředků nebo předplatného.  

  - Vlastnící uživatel cílového kontejneru nebo adresáře, pro který plánujete použít nastavení seznamu ACL. Chcete-li nastavit seznamy ACL rekurzivně, zahrnuje všechny podřízené položky v cílovém kontejneru nebo adresáři.
  
  - Klíč účtu úložiště

## <a name="set-up-your-project"></a>Nastavení projektu

Začněte tím, že otevřete [tuto stránku](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) a získáte nejnovější verzi knihovny Java. Pak otevřete soubor *pom.xml* v textovém editoru. Přidejte element závislosti, který odkazuje na tuto verzi.

Pokud plánujete ověřování klientské aplikace pomocí Azure Active Directory (AD), přidejte závislost do klientské knihovny tajného kódu Azure. Viz  [Přidání balíčku tajné klientské knihovny do projektu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Připojení pomocí Azure Active Directory (Azure AD)

K ověření vaší aplikace v Azure AD můžete použít [klientskou knihovnu Azure identity pro jazyk Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

Získejte ID klienta, tajný klíč klienta a ID tenanta. Pokud to chcete provést, přečtěte si téma [získání tokenu z Azure AD pro autorizaci žádostí z klientské aplikace](../common/storage-auth-aad-app.md). V rámci tohoto procesu budete muset k objektu zabezpečení přiřadit jednu z následujících rolí [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md) . 

|Role|Funkce nastavení seznamu ACL|
|--|--|
|[Vlastník dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Všechny adresáře a soubory v účtu.|
|[Přispěvatel dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Pouze adresáře a soubory vlastněné objektem zabezpečení.|

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí ID klienta, tajného klíče klienta a ID tenanta.  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Další příklady najdete v dokumentaci ke [klientské knihovně Azure identity pro jazyk Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="connect-by-using-an-account-key"></a>Připojení pomocí klíče účtu

Toto je nejjednodušší způsob, jak se připojit k účtu. 

Tento příklad vytvoří instanci **DataLakeServiceClient** pomocí klíče účtu.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Nastavení seznamů ACL

Když *nastavíte* seznam řízení přístupu (ACL), **NAHRADÍTE** celý seznam ACL včetně všech jeho položek. Pokud chcete změnit úroveň oprávnění objektu zabezpečení nebo přidat nový objekt zabezpečení do seznamu ACL, aniž by to ovlivnilo jiné existující položky, měli byste místo toho *aktualizovat* seznam ACL. Chcete-li aktualizovat seznam řízení přístupu (ACL) místo jeho nahrazení, přečtěte si část [aktualizace seznamů ACL](#update-acls) tohoto článku.  

Pokud se rozhodnete *nastavit* seznam řízení přístupu (ACL), musíte přidat položku pro vlastnícího uživatele, položku pro vlastnící skupinu a položku pro všechny ostatní uživatele. Další informace o vlastnícím uživateli, vlastnící skupině a všech ostatních uživatelích najdete v tématu [Uživatelé a identity](data-lake-storage-access-control.md#users-and-identities).

V této části se dozvíte, jak:

- Nastavení seznamu ACL adresáře
- Nastavení seznamu ACL souboru
- Rekurzivní nastavení seznamů ACL 

### <a name="set-the-acl-of-a-directory"></a>Nastavení seznamu ACL adresáře

Tento příklad načte a potom nastaví seznam řízení přístupu k adresáři s názvem `my-directory` . Tento příklad uděluje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním přístup pro čtení.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Můžete také získat a nastavit seznam ACL kořenového adresáře kontejneru. Chcete-li získat kořenový adresář, předejte prázdný řetězec ( `""` ) do metody **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="set-the-acl-of-a-file"></a>Nastavení seznamu ACL souboru

Tento příklad načte a potom nastaví seznam řízení přístupu k souboru s názvem `upload-file.txt` . Tento příklad uděluje vlastnícímu uživateli oprávnění ke čtení, zápisu a spouštění, dává vlastnící skupině pouze oprávnění číst a spouštět a poskytuje všem ostatním přístup pro čtení.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>Rekurzivní nastavení seznamů ACL

Nastavení seznamů ACL rekurzivně voláním metody **DataLakeDirectoryClient. setAccessControlRecursive** . Předá tuto metodu [seznam](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) objektů [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Každý [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definuje položku seznamu ACL.

Pokud chcete nastavit **výchozí** položku seznamu řízení přístupu (ACL), můžete zavolat metodu **setDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) a předat hodnotu **true**. 

Tento příklad nastaví seznam řízení přístupu k adresáři s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda se má nastavit výchozí seznam řízení přístupu. Tento parametr se používá v každém volání metody **SetDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Položky seznamu řízení přístupu poskytují oprávnění ke čtení, zápisu a spouštění vlastnícího uživatele, přidělí skupině jenom oprávnění ke čtení a spouštění a všem ostatním uživatelům se neudělí žádný přístup. Poslední položka seznamu ACL v tomto příkladu poskytuje konkrétního uživatele s ID objektu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" oprávnění číst a spustit.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>Aktualizace seznamů ACL

Když *aktualizujete* seznam řízení přístupu (ACL), místo nahrazení seznamu ACL ho upravte. Do seznamu ACL můžete například přidat nový objekt zabezpečení, aniž by to ovlivnilo jiné objekty zabezpečení uvedené v seznamu ACL.  Chcete-li nahradit seznam ACL místo aktualizace, přečtěte si část [set ACL](#set-acls) v tomto článku.

V této části se dozvíte, jak:

- Aktualizace seznamu ACL
- Rekurzivní aktualizace seznamů ACL

### <a name="update-an-acl"></a>Aktualizace seznamu ACL

Nejprve získejte seznam ACL adresáře voláním metody [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) . Zkopírujte seznam položek seznamu ACL do nového objektu **seznamu** typu [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Pak vyhledejte položku, kterou chcete aktualizovat, a nahraďte ji v seznamu. Nastavte seznam ACL voláním metody [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Tento příklad aktualizuje seznam řízení přístupu k adresáři s názvem `my-parent-directory` nahrazením položky pro všechny ostatní uživatele. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

Můžete také získat a nastavit seznam ACL kořenového adresáře kontejneru. Chcete-li získat kořenový adresář, předejte prázdný řetězec ( `""` ) do metody **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="update-acls-recursively"></a>Rekurzivní aktualizace seznamů ACL

Chcete-li seznam ACL aktualizovat rekurzivně, vytvořte nový objekt ACL s položkou seznamu ACL, kterou chcete aktualizovat, a pak tento objekt použijte v operaci aktualizovat seznam ACL. Nezískejte existující seznam ACL, stačí poskytnout položky seznamu ACL, které se mají aktualizovat.

Rekurzivní aktualizace seznamů ACL voláním metody **DataLakeDirectoryClient. updateAccessControlRecursive** .  Předá tuto metodu [seznam](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) objektů [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Každý [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definuje položku seznamu ACL. 

Pokud chcete aktualizovat **výchozí** položku seznamu řízení přístupu (ACL), můžete **setDefaultScope** metodu [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) a předat hodnotu **true**. 

Tento příklad aktualizuje položku seznamu řízení přístupu (ACL) s oprávněním k zápisu. Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda se má aktualizovat výchozí seznam ACL. Tento parametr se používá při volání metody **SetDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>Odebrat položky seznamu řízení přístupu

Můžete odebrat jednu nebo více položek seznamu řízení přístupu. V této části se dozvíte, jak:

- Odebrat položku seznamu řízení přístupu
- Rekurzivní odebrání položek seznamu ACL

### <a name="remove-an-acl-entry"></a>Odebrat položku seznamu řízení přístupu

Nejprve získejte seznam ACL adresáře voláním metody [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) . Zkopírujte seznam položek seznamu ACL do nového objektu **seznamu** typu [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Pak vyhledejte položku, kterou chcete odebrat, a zavolejte metodu **Remove** objektu **list** . Nastavte aktualizovaný seznam řízení přístupu voláním metody [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Rekurzivní odebrání položek seznamu ACL

Chcete-li odebrat položky seznamu ACL rekurzivně, vytvořte nový objekt ACL pro položku seznamu ACL, která se má odebrat, a pak tento objekt použijte v operaci odebrat seznam ACL. Nezískávat existující seznam ACL, stačí zadat položky seznamu ACL, které se mají odebrat.

Odeberte položky seznamu ACL voláním metody **DataLakeDirectoryClient. removeAccessControlRecursive** . Předá tuto metodu [seznam](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) objektů [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Každý [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definuje položku seznamu ACL. 

Pokud chcete odebrat **výchozí** položku seznamu řízení přístupu (ACL), můžete **setDefaultScope** metodu [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) a předat hodnotu **true**.  

Tento příklad odebere položku seznamu řízení přístupu z seznamu řízení přístupu (ACL) adresáře s názvem `my-parent-directory` . Tato metoda přijímá logický parametr s názvem `isDefaultScope` , který určuje, zda má být položka odebrána z výchozího seznamu ACL. Tento parametr se používá při volání metody **SetDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Obnovit po selhání

Může dojít k chybám za běhu nebo oprávnění. V případě chyb běhového prostředí restartujte proces od začátku. K chybám oprávnění může dojít v případě, že objekt zabezpečení nemá dostatečná oprávnění pro úpravu seznamu ACL adresáře nebo souboru, který je v upravované hierarchii adresáře. Vyřešte problém s oprávněním a pak zvolte, že chcete proces obnovit z bodu selhání pomocí tokenu pro pokračování, nebo spusťte proces znovu od začátku. Nemusíte používat token pro pokračování, pokud dáváte přednost restartování od začátku. Položky ACL můžete znovu použít bez jakýchkoli negativních dopadů.

Tento příklad vrátí token pro pokračování v případě selhání. Aplikace může tuto ukázkovou metodu zavolat znovu poté, co byla tato chyba adresována, a předejte token pro pokračování. Pokud je tato ukázková metoda volána poprvé, může aplikace předat hodnotu `null` parametru tokenu pokračování.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

Pokud chcete, aby byl proces dokončen bez přerušení pomocí chyb oprávnění, můžete zadat.

Chcete-li zajistit, že proces bude dokončen bez přerušení, zavolejte metodu **setContinueOnFailure** objektu [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) a předejte hodnotu **true**.

Tento příklad nastavuje rekurzivní položky seznamů ACL. Pokud tento kód narazí na chybu oprávnění, zaznamená toto selhání a pokračuje v provádění. Tento příklad vytiskne počet selhání konzoly.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Viz také

- [Referenční dokumentace k rozhraní API](/java/api/overview/azure/storage-file-datalake-readme)
- [Balíček (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [ukázky](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Mapování Gen1 na Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Sdělte nám svůj názor](https://github.com/Azure/azure-sdk-for-java/issues)
- [Model řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Seznamy řízení přístupu (ACL) v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)