---
title: Použití azure cli pro soubory & seznamů ACL v Azure Data Lake Storage Gen2 (preview)
description: Pomocí rozhraní příkazového příkazového nastavení Azure můžete spravovat adresáře a seznamy řízení přístupu k souborům a adresářům (ACL) v účtech úložiště, které mají hierarchický obor názvů.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: ce2b4200496938e6cffb935207df8c7027eaf37a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486130"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Použití azure cli ke správě adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 (preview)

Tento článek ukazuje, jak pomocí [rozhraní Příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) vytvořit a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají hierarchický obor názvů. 

> [!IMPORTANT]
> Rozšíření, `storage-preview` které je uvedeno v tomto článku je aktuálně ve verzi Public Preview.

[Ukázka](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | mapování | [Gen1 až Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)[Poskytnout zpětnou vazbu](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolen hierarchický obor názvů (HNS). [Chcete-li](data-lake-storage-quickstart-create-account.md) jej vytvořit, postupujte podle těchto pokynů.
> * Verze příkazového `2.0.67` příkazového příkazu Azure nebo vyšší.

## <a name="install-the-storage-cli-extension"></a>Instalace rozšíření cli úložiště

1. Otevřete [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)nebo pokud jste [nainstalovali](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) azure CLI místně, otevřete aplikaci konzoly příkazů, jako je Windows PowerShell.

2. Ověřte, zda je verze rozhraní `2.0.67` příkazového příkazu Azure, které mají nainstalované nebo vyšší pomocí následujícího příkazu.

   ```azurecli
    az --version
   ```
   Pokud je vaše verze azure `2.0.67`cli nižší než , nainstalujte novější verzi. Viz [Instalace příkazového příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Nainstalujete rozšíření `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Připojení k účtu

1. Pokud používáte Azure CLI místně, spusťte příkaz pro přihlášení.

   ```azurecli
   az login
   ```

   Pokud příkazové příkazové příkazy můžete otevřít výchozí prohlížeč, bude tak učinit a načíst přihlašovací stránku Azure.

   V opačném případě otevřete stránku prohlížeče a [https://aka.ms/devicelogin](https://aka.ms/devicelogin) zadejte autorizační kód zobrazený ve vašem terminálu. Poté se přihlaste pomocí přihlašovacích údajů k účtu v prohlížeči.

   Další informace o různých metodách ověřování najdete v tématu Přihlášení pomocí Azure CLI.

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický web.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Nahraďte `<subscription-id>` zástupnou hodnotu ID předplatného.

## <a name="create-a-file-system"></a>Vytvoření systému souborů

Systém souborů funguje jako kontejner pro vaše soubory. Můžete vytvořit pomocí příkazu. `az storage container create` 

Tento příklad vytvoří systém `my-file-system`souborů s názvem .

```azurecli
az storage container create --name my-file-system --account-name mystorageaccount
```

## <a name="create-a-directory"></a>Vytvoření adresáře

Vytvořte odkaz na `az storage blob directory create` adresář pomocí příkazu. 

Tento příklad přidá `my-directory` adresář s názvem `my-file-system` do systému souborů `mystorageaccount`s názvem s názvem v účtu s názvem .

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Zobrazit vlastnosti adresáře

Vlastnosti adresáře můžete vytisknout do konzoly pomocí příkazu. `az storage blob show`

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Přejmenování nebo přesunutí adresáře

Přejmenujte nebo přesuňte `az storage blob directory move` adresář pomocí příkazu.

Tento příklad přejmenuje adresář `my-directory` z názvu `my-new-directory`na název .

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Odstranění adresáře

Odstraňte adresář pomocí `az storage blob directory delete` příkazu.

Tento příklad odstraní adresář `my-directory`s názvem . 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Kontrola existence adresáře

Pomocí `az storage blob directory exist` příkazu určete, zda v systému souborů existuje určitý adresář.

Tento příklad ukazuje, zda `my-directory` v systému `my-file-system` souborů existuje pojmenovaný adresář. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Stažení z adresáře

Stáhněte soubor z adresáře `az storage blob directory download` pomocí příkazu.

Tento příklad stáhne `upload.txt` soubor pojmenovaný `my-directory`z adresáře s názvem . 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Tento příklad stáhne celý adresář.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Výpis obsahu adresáře

Seznam obsahu adresáře pomocí `az storage blob directory list` příkazu.

V tomto příkladu je `my-directory` uveden obsah adresáře s názvem, který je umístěn v systému `my-file-system` souborů účtu úložiště s názvem `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Nahrání souboru do adresáře

Nahrajte soubor do adresáře `az storage blob directory upload` pomocí příkazu.

Tento příklad odešle `upload.txt` soubor s `my-directory`názvem do adresáře s názvem . 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Tento příklad nahraje celý adresář.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Zobrazit vlastnosti souboru

Vlastnosti souboru můžete vytisknout do konzoly pomocí příkazu. `az storage blob show`

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Přejmenování nebo přesunutí souboru

Přejmenujte nebo přesuňte `az storage blob move` soubor pomocí příkazu.

Tento příklad přejmenuje soubor `my-file.txt` z názvu `my-file-renamed.txt`na název .

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
> Pokud používáte Azure Active Directory (Azure AD) k autorizaci příkazů, ujistěte se, že váš objekt zabezpečení byl přiřazen [roli vlastníka dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Další informace o použití oprávnění seznamu ACL a jejich efektech najdete [v tématu Řízení přístupu v azure datovém úložišti.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

### <a name="get-directory-and-file-permissions"></a>Získání oprávnění k adresáři a souborům

Získejte seznam ACL **adresáře** pomocí příkazu. `az storage blob directory access show`

Tento příklad získá seznam ACL adresáře a potom vytiskne seznam ACL do konzoly.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Získejte přístupová oprávnění **souboru** pomocí příkazu. `az storage blob access show` 

Tento příklad získá acl souboru a potom vytiskne acl do konzoly.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Následující obrázek znázorňuje výstup po získání seznamu ACL adresáře.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

V tomto příkladu má vlastnící uživatel oprávnění ke čtení, zápisu a spouštění. Vlastnící skupina má pouze oprávnění ke čtení a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Nastavení oprávnění k adresáři a souborům

Pomocí `az storage blob directory access set` příkazu nastavte seznam ACL **adresáře**. 

Tento příklad nastaví seznam ACL v adresáři pro vlastnící uživatele, vlastnící skupinu nebo jiné uživatele a potom vytiskne seznam ACL do konzoly.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Tento příklad nastaví *výchozí* seznam ACL v adresáři pro vlastnící uživatele, vlastnící skupinu nebo jiné uživatele a potom vytiskne seznam ACL do konzoly.

```azurecli
az storage blob directory access set -a "default:user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Pomocí `az storage blob access set` příkazu nastavte acl **souboru**. 

Tento příklad nastaví acl na soubor pro vlastnící uživatele, vlastnící skupiny nebo jiných uživatelů a potom vytiskne acl do konzoly.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
Následující obrázek znázorňuje výstup po nastavení acl souboru.

![Získat výstup ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

V tomto příkladu vlastnící uživatel a vlastnící skupina mají pouze oprávnění ke čtení a zápisu. Všichni ostatní uživatelé mají oprávnění k zápisu a spouštění. Další informace o seznamech řízení přístupu najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Aktualizace oprávnění k adresáři a souborům

Dalším způsobem, jak nastavit toto oprávnění, je použití příkazu `az storage blob directory access update` nebo. `az storage blob access update` 

Aktualizujte seznam ACL adresáře nebo `-permissions` souboru nastavením parametru na krátkou formu seznamu Řízení řízení.

Tento příklad aktualizuje seznam ACL **adresáře**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Tento příklad aktualizuje acl **souboru**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Můžete také aktualizovat vlastnící uživatele a skupinu adresáře `--owner` `group` nebo souboru nastavením parametrů nebo na ID entity nebo uživatelské hlavní jméno (UPN) uživatele. 

Tento příklad změní vlastníka adresáře. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Tento příklad změní vlastníka souboru. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Správa metadat definovaných uživatelem

Uživatelem definovaná metadata můžete do souboru `az storage blob directory metadata update` nebo adresáře přidat pomocí příkazu s jedním nebo více dvojicemi název-hodnota.

Tento příklad přidá uživatelem definovaná `my-directory` metadata pro adresář s názvem adresář.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Tento příklad zobrazuje všechna uživatelem definovaná metadata pro adresář s názvem `my-directory`.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Viz také

* [Ukázka](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mapování Gen1 až Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Váš názor](https://github.com/Azure/azure-cli-extensions/issues)
* [Známé problémy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Zdrojový kód](https://github.com/Azure/azure-cli-extensions/tree/master/src)

