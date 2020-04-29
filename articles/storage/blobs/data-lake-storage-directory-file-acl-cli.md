---
title: Použití rozhraní příkazového řádku Azure pro soubory & seznamů ACL v Azure Data Lake Storage Gen2 (Preview)
description: Pomocí Azure CLI můžete spravovat adresáře a seznamy řízení přístupu (ACL) souborů a adresářů v účtech úložiště, které mají hierarchický obor názvů.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77486130"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Použití Azure CLI ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 (Preview)

V tomto článku se dozvíte, jak pomocí [rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají hierarchický obor názvů. 

> [!IMPORTANT]
> `storage-preview` Rozšíření, které je vybrané v tomto článku, je aktuálně ve verzi Public Preview.

[Ukázka](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) |  | [mapování Gen1 na Gen2 pro](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)[poskytnutí zpětné vazby](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.
> * Verze `2.0.67` Azure CLI nebo vyšší

## <a name="install-the-storage-cli-extension"></a>Instalace rozšíření Storage CLI

1. Otevřete [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)nebo pokud máte rozhraní příkazového řádku Azure místně [nainstalované](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , otevřete konzolovou aplikaci, například Windows PowerShell.

2. Pomocí následujícího příkazu ověřte, že verze rozhraní příkazového řádku `2.0.67` Azure, která je nainstalovaná, je nebo vyšší.

   ```azurecli
    az --version
   ```
   Pokud je vaše verze rozhraní příkazového řádku Azure `2.0.67`nižší než, nainstalujte novější verzi. Viz [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Nainstalujete rozšíření `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Připojit k účtu

1. Pokud používáte Azure CLI místně, spusťte příkaz Login.

   ```azurecli
   az login
   ```

   Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure.

   V opačném případě otevřete stránku prohlížeče [https://aka.ms/devicelogin](https://aka.ms/devicelogin) na adrese a zadejte autorizační kód zobrazený v terminálu. Pak se přihlaste pomocí přihlašovacích údajů k účtu v prohlížeči.

   Další informace o různých metodách ověřování najdete v tématu Přihlášení pomocí Azure CLI.

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický Web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete ho vytvořit pomocí `az storage container create` příkazu. 

Tento příklad vytvoří systém souborů s názvem `my-file-system`.

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář pomocí `az storage blob directory create` příkazu. 

Tento příklad přidá adresář s názvem `my-directory` do systému souborů s názvem `my-file-system` , který je umístěn v účtu s `mystorageaccount`názvem.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Zobrazit vlastnosti adresáře

Vlastnosti adresáře lze vytisknout do konzoly pomocí `az storage blob show` příkazu.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář pomocí `az storage blob directory move` příkazu.

V tomto příkladu se přejmenuje adresář z názvu `my-directory` na název `my-new-directory`.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář pomocí `az storage blob directory delete` příkazu.

Tento příklad odstraní adresář s názvem `my-directory`. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Zjistit, jestli adresář existuje

Zjistěte, zda v systému souborů existuje konkrétní adresář pomocí `az storage blob directory exist` příkazu.

Tento příklad ukáže, zda adresář s `my-directory` názvem existuje v `my-file-system` systému souborů. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Stažení z adresáře

Stáhněte si soubor z adresáře pomocí `az storage blob directory download` příkazu.

Tento příklad stáhne soubor s názvem `upload.txt` z adresáře s názvem `my-directory`. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Tento příklad stáhne celý adresář.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Seznamte se s obsahem adresáře pomocí `az storage blob directory list` příkazu.

Tento příklad vypíše obsah adresáře s názvem `my-directory` , který se nachází v systému `my-file-system` souborů účtu úložiště s názvem. `mystorageaccount` 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nahrajte soubor do adresáře pomocí `az storage blob directory upload` příkazu.

Tento příklad nahraje soubor s názvem `upload.txt` do adresáře s názvem. `my-directory` 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Tento příklad nahraje celý adresář.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Zobrazit vlastnosti souboru

Vlastnosti souboru můžete vytisknout do konzoly pomocí `az storage blob show` příkazu.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Přejmenování nebo přesunutí souboru

Přejmenujte nebo přesuňte soubor pomocí `az storage blob move` příkazu.

Tento příklad přejmenuje soubor z názvu `my-file.txt` na název. `my-file-renamed.txt`

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Odstranění souboru

Odstraňte soubor pomocí `az storage blob delete` příkazu.

Tento příklad odstraní soubor s názvem`my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Spravovat oprávnění

Můžete získat, nastavit a aktualizovat přístupová oprávnění adresářů a souborů.

> [!NOTE]
> Pokud k autorizaci příkazů používáte Azure Active Directory (Azure AD), ujistěte se, že je vašemu objektu zabezpečení přiřazená [role vlastníka dat objektu BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Pokud se chcete dozvědět víc o tom, jak se používají oprávnění seznamu ACL, a důsledky jejich změny, přečtěte si téma [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Získat oprávnění pro adresáře a soubory

Získat seznam řízení přístupu k **adresáři** pomocí `az storage blob directory access show` příkazu.

Tento příklad získá seznam ACL adresáře a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Pomocí `az storage blob access show` příkazu Získejte přístupová oprávnění k **souboru** . 

Tento příklad získá seznam řízení přístupu k souboru a poté vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Následující obrázek ukazuje výstup po získání seznamu ACL adresáře.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

V tomto příkladu má vlastnící uživatel oprávnění ke čtení, zápisu a spouštění. Vlastnící skupina má pouze oprávnění ke čtení a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Nastavení oprávnění adresáře a souboru

Pomocí `az storage blob directory access set` příkazu nastavte seznam řízení přístupu (ACL) **adresáře**. 

Tento příklad nastavuje seznam řízení přístupu v adresáři pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Tento příklad nastaví *výchozí* seznam řízení přístupu v adresáři pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

K nastavení `az storage blob access set` seznamu ACL **souboru**použijte příkaz. 

Tento příklad nastavuje seznam řízení přístupu pro soubor pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Následující obrázek ukazuje výstup po nastavení seznamu ACL souboru.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

V tomto příkladu mají vlastnící uživatel a vlastnící skupina jenom oprávnění ke čtení a zápisu. Všichni ostatní uživatelé mají oprávnění k zápisu a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Aktualizovat oprávnění adresáře a souboru

Dalším způsobem, jak toto oprávnění nastavit, je použití `az storage blob directory access update` příkazu `az storage blob access update` nebo. 

Aktualizujte seznam řízení přístupu (ACL) adresáře nebo souboru `-permissions` nastavením parametru na krátký tvar seznamu ACL.

Tento příklad aktualizuje seznam řízení přístupu k **adresáři**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Tento příklad aktualizuje seznam řízení přístupu k **souboru**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Vlastnícího uživatele a skupinu adresáře nebo souboru můžete také aktualizovat nastavením parametrů `--owner` nebo `group` na ID entity nebo hlavní název uživatele (UPN) uživatele. 

Tento příklad změní vlastníka adresáře. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Tento příklad změní vlastníka souboru. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Správa uživatelsky definovaných metadat

Do souboru nebo adresáře můžete přidat uživatelsky definovaná metadata pomocí `az storage blob directory metadata update` příkazu s jednou nebo více páry název-hodnota.

Tento příklad přidá uživatelsky definovanou metadata pro adresář s názvem `my-directory` Directory.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Tento příklad ukazuje všechna uživatelsky definovaná metadata pro adresář s `my-directory`názvem.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Viz také

* [Ukázka](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mapování Gen1 na Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Váš názor](https://github.com/Azure/azure-cli-extensions/issues)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Zdrojový kód](https://github.com/Azure/azure-cli-extensions/tree/master/src)

