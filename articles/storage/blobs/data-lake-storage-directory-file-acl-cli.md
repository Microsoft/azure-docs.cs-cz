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
ms.openlocfilehash: 596f8334b647daf6fe3a15521f7caeecb0c0e303
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462603"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Použití Azure CLI ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 (Preview)

V tomto článku se dozvíte, jak pomocí [rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají hierarchický obor názvů. 

> [!IMPORTANT]
> Rozšíření `storage-preview`, které je vybrané v tomto článku, je momentálně ve verzi Public Preview.

[Ukázka](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Gen1 na mapování Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [poskytnutí zpětné vazby](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.
> * Verze Azure CLI `2.0.67` nebo vyšší.

## <a name="install-the-storage-cli-extension"></a>Instalace rozšíření Storage CLI

1. Otevřete [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)nebo pokud máte rozhraní příkazového řádku Azure místně [nainstalované](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , otevřete konzolovou aplikaci, například Windows PowerShell.

2. Ověřte, jestli je verze rozhraní příkazového řádku Azure, která je nainstalovaná, `2.0.67` nebo vyšší, pomocí následujícího příkazu.

   ```azurecli
    az --version
   ```
   Pokud je vaše verze rozhraní příkazového řádku Azure nižší než `2.0.67`, nainstalujte novější verzi. Viz [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

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

   V opačném případě otevřete stránku prohlížeče na [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadejte autorizační kód zobrazený v terminálu. Pak se přihlaste pomocí přihlašovacích údajů k účtu v prohlížeči.

   Další informace o různých metodách ověřování najdete v tématu přihlášení pomocí Azure CLI.

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický Web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Nahraďte hodnotu zástupného symbolu `<subscription-id>` číslem ID vašeho předplatného.

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete ho vytvořit pomocí příkazu `az storage container create`. 

Tento příklad vytvoří systém souborů s názvem `my-file-system`.

```azurecli
az storage container create --name my-file-system
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na adresář pomocí příkazu `az storage blob directory create`. 

Tento příklad přidá adresář s názvem `my-directory` do systému souborů s názvem `my-file-system`, který je umístěn v účtu s názvem `mystorageaccount`.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Zobrazit vlastnosti adresáře

Vlastnosti adresáře lze vytisknout do konzoly pomocí příkazu `az storage blob show`.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte adresář pomocí příkazu `az storage blob directory move`.

Tento příklad přejmenuje adresář z názvu `my-directory` na název `my-new-directory`.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář pomocí příkazu `az storage blob directory delete`.

Tento příklad odstraní adresář s názvem `my-directory`. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Zjistit, jestli adresář existuje

Určete, zda konkrétní adresář v systému souborů existuje pomocí příkazu `az storage blob directory exist`.

Tento příklad ukáže, zda adresář s názvem `my-directory` existuje v `my-file-system` systému souborů. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Stažení z adresáře

Stáhněte si soubor z adresáře pomocí příkazu `az storage blob directory download`.

Tento příklad stáhne soubor s názvem `upload.txt` z adresáře s názvem `my-directory`. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Tento příklad stáhne celý adresář.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Seznamte se s obsahem adresáře pomocí příkazu `az storage blob directory list`.

Tento příklad vypíše obsah adresáře s názvem `my-directory`, který se nachází v `my-file-system` systému souborů účtu úložiště s názvem `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nahrajte soubor do adresáře pomocí příkazu `az storage blob directory upload`.

Tento příklad nahraje soubor s názvem `upload.txt` do adresáře s názvem `my-directory`. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Tento příklad nahraje celý adresář.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Zobrazit vlastnosti souboru

Vlastnosti souboru můžete vytisknout do konzoly pomocí příkazu `az storage blob show`.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Přejmenování nebo přesunutí souboru

Přejmenujte nebo přesuňte soubor pomocí příkazu `az storage blob move`.

Tento příklad přejmenuje soubor z názvu `my-file.txt` na název `my-file-renamed.txt`.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Odstranění souboru

Odstraňte soubor pomocí příkazu `az storage blob delete`.

Tento příklad odstraní soubor s názvem `my-file.txt`

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Správa oprávnění

Můžete získat, nastavit a aktualizovat přístupová oprávnění adresářů a souborů.

### <a name="get-directory-and-file-permissions"></a>Získat oprávnění pro adresáře a soubory

Získejte seznam ACL **adresáře** pomocí příkazu `az storage blob directory access show`.

Tento příklad získá seznam ACL adresáře a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Pomocí příkazu `az storage blob access show` získat přístupová oprávnění k **souboru** . 

Tento příklad získá seznam řízení přístupu k souboru a poté vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Následující obrázek ukazuje výstup po získání seznamu ACL adresáře.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

V tomto příkladu má vlastnící uživatel oprávnění ke čtení, zápisu a spouštění. Vlastnící skupina má pouze oprávnění ke čtení a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Nastavení oprávnění adresáře a souboru

Pomocí příkazu `az storage blob directory access set` nastavte seznam řízení přístupu (ACL) **adresáře**. 

Tento příklad nastavuje seznam řízení přístupu v adresáři pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

K nastavení seznamu ACL **souboru**použijte příkaz `az storage blob access set`. 

Tento příklad nastavuje seznam řízení přístupu pro soubor pro vlastnícího uživatele, vlastnící skupinu nebo jiné uživatele a pak vytiskne seznam řízení přístupu do konzoly.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Následující obrázek ukazuje výstup po nastavení seznamu ACL souboru.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

V tomto příkladu mají vlastnící uživatel a vlastnící skupina jenom oprávnění ke čtení a zápisu. Všichni ostatní uživatelé mají oprávnění k zápisu a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Aktualizovat oprávnění adresáře a souboru

Dalším způsobem, jak toto oprávnění nastavit, je použít příkaz `az storage blob directory access update` nebo `az storage blob access update`. 

Aktualizujte seznam řízení přístupu pro adresář nebo soubor nastavením parametru `-permissions` na krátký tvar seznamu ACL.

Tento příklad aktualizuje seznam řízení přístupu k **adresáři**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Tento příklad aktualizuje seznam řízení přístupu k **souboru**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Můžete také aktualizovat vlastnícího uživatele a skupinu adresáře nebo souboru nastavením parametrů `--owner` nebo `group` na ID entity nebo hlavní název uživatele (UPN) uživatele. 

Tento příklad změní vlastníka adresáře. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Tento příklad změní vlastníka souboru. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Správa uživatelsky definovaných metadat

Do souboru nebo adresáře můžete přidat uživatelsky definovaná metadata pomocí příkazu `az storage blob directory metadata update` s jednou nebo více páry název-hodnota.

Tento příklad přidá uživatelsky definovanou metadata pro adresář s názvem `my-directory` Directory.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Tento příklad ukazuje všechna uživatelsky definovaná metadata pro adresář s názvem `my-directory`.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Další informace najdete v tématech

* [Ukázka](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mapování Gen1 na Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Váš názor](https://github.com/Azure/azure-cli-extensions/issues)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Zdrojový kód](https://github.com/Azure/azure-cli-extensions/tree/master/src)

