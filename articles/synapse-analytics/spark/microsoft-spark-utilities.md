---
title: Seznámení s nástroji Microsoft Spark
description: 'Kurz: MSSparkutils v poznámkových blocích Azure synapse Analytics'
author: ruxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: fee7e244e26a59a773a5d5c9c74e23acd4113ee6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446698"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Seznámení s nástroji Microsoft Spark

Sady nástrojů Microsoft Spark (MSSparkUtils) jsou předplatným balíčkem, který usnadňuje snadné provádění běžných úloh. Pomocí MSSparkUtils můžete pracovat se systémy souborů, získat proměnné prostředí a pracovat s tajnými kódy. MSSparkUtils jsou k dispozici v `PySpark (Python)` `Scala` `.NET Spark (C#)` poznámkových blocích, a a v kanálech synapse.

## <a name="pre-requisites"></a>Požadavky

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Konfigurace přístupu k Azure Data Lake Storage Gen2 

Synapse poznámkové bloky používají předávací službu Azure Active Directory (Azure AD) pro přístup k účtům ADLS Gen2. Pro přístup k účtu ADLS Gen2 (nebo složce) musíte být **přispěvatelem BLOB Storage** . 

Kanály synapse používají k přístupu k účtům úložiště identitu pracovního prostoru (MSI). Pokud chcete ve svých aktivitách kanálu používat MSSparkUtils, musí být identita pracovního prostoru **BLOB Storage Přispěvatel** pro přístup k účtu adls Gen2 (nebo složce).

Pomocí těchto kroků se ujistěte, že váš soubor MSI služby Azure AD a pracovní prostor má přístup k účtu ADLS Gen2:
1. Otevřete [Azure Portal](https://portal.azure.com/) a účet úložiště, ke kterému chcete získat přístup. Můžete přejít ke konkrétnímu kontejneru, ke kterému chcete získat přístup.
2. Na levém panelu vyberte **řízení přístupu (IAM)** .
3. Přiřaďte **účet služby Azure AD** a **identitu svého pracovního prostoru** (stejné jako název vašeho pracovního prostoru) do role **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště, pokud ještě není přiřazená. 
4. Vyberte **Uložit**.

K datům v ADLS Gen2 pomocí synapse Spark můžete přistupovat pomocí následující adresy URL:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Konfigurace přístupu k Azure Blob Storage 

Synapse využívá **sdílený přístupový podpis (SAS)** pro přístup k Azure Blob Storage. Aby nedocházelo k vystavování klíčů SAS v kódu, doporučujeme vytvořit novou propojenou službu v pracovním prostoru synapse do účtu Azure Blob Storage, ke kterému chcete získat přístup.

Pomocí následujících kroků přidejte novou propojenou službu pro účet Azure Blob Storage:

1. Otevřete [Azure synapse Studio](https://web.azuresynapse.net/).
2. Na levém panelu vyberte **Spravovat** a v části **externí připojení** vyberte **propojené služby** .
3. Hledejte v **Azure Blob Storage** na pravé straně panelu **propojených služeb** .
4. Vyberte **Pokračovat**.
5. Vyberte účet Azure Blob Storage, pro který chcete získat přístup a nakonfigurovat název propojené služby. Navrhněte použití **klíče účtu** pro **metodu ověřování**.
6. Vyberte možnost **Test připojení** , aby se ověřilo, že jsou nastavení správná.
7. Vyberte **vytvořit** jako první a kliknutím na **publikovat vše** uložte změny. 

K datům v Azure Blob Storage pomocí synapse Sparku můžete přistupovat pomocí následující adresy URL:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Tady je příklad kódu:


:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end
 
###  <a name="configure-access-to-azure-key-vault"></a>Konfigurace přístupu k Azure Key Vault

Můžete přidat Azure Key Vault jako propojenou službu pro správu přihlašovacích údajů v synapse. Pomocí těchto kroků přidáte Azure Key Vault jako propojenou službu synapse:
1. Otevřete [Azure synapse Studio](https://web.azuresynapse.net/).
2. Na levém panelu vyberte **Spravovat** a v části **externí připojení** vyberte **propojené služby** .
3. Na pravé straně panelu na **nové propojené služby** hledejte **Azure Key Vault** .
4. Vyberte účet Azure Key Vault, pro který chcete získat přístup, a nakonfigurujte název propojené služby.
5. Vyberte možnost **Test připojení** , aby se ověřilo, že jsou nastavení správná.
6. Vyberte **vytvořit** jako první a kliknutím na **publikovat vše** uložte změnu. 

Synapse poznámkové bloky využívají předávací službu Azure Active Directory (Azure AD) pro přístup k Azure Key Vault. Synapse kanály k přístupu k Azure Key Vault používají identitu pracovního prostoru (MSI). Abyste se ujistili, že váš kód funguje v poznámkovém bloku i v kanálu synapse, doporučujeme udělit oprávnění ke tajnému přístupu pro účet služby Azure AD a identitu pracovního prostoru.

Pomocí těchto kroků udělíte tajný přístup k identitě svého pracovního prostoru:
1. Otevřete [Azure Portal](https://portal.azure.com/) a Azure Key Vault, ke kterým chcete získat přístup. 
2. Na levém panelu vyberte **zásady přístupu** .
3. Vyberte **Přidat zásady přístupu**: 
    - Jako šablonu konfigurace vyberte **klíč, tajný klíč & Správa certifikátů** .
    - Vyberte **svůj účet Azure AD** a **identitu pracovního prostoru** (stejné jako název vašeho pracovního prostoru) v poli vybrat objekt zabezpečení nebo se ujistěte, že je už přiřazený. 
4. Vyberte **Vybrat** a **Přidat**.
5. Kliknutím na tlačítko **Uložit** potvrďte změny.  

## <a name="file-system-utilities"></a>Nástroje systému souborů

`mssparkutils.fs` poskytuje nástroje pro práci s různými systémy souborů, včetně Azure Data Lake Storage Gen2 (ADLS Gen2) a Azure Blob Storage. Ujistěte se, že jste správně nakonfigurovali přístup k [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) a [Azure Blob Storage](#configure-access-to-azure-blob-storage) .

Pro přehled dostupných metod spusťte následující příkazy:

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Výsledky:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Zobrazení souborů
Seznamte se s obsahem adresáře.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Zobrazit vlastnosti souboru
Vrátí vlastnosti souboru, včetně názvu souboru, cesty k souboru, velikosti souboru a toho, jestli se jedná o adresář a soubor.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Vytvořit nový adresář

Vytvoří daný adresář, pokud neexistuje, a všechny nezbytné nadřazené adresáře.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Kopírovat soubor

Zkopíruje soubor nebo adresář. Podporuje kopírování mezi systémy souborů.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Náhled obsahu souboru

Vrátí až první bajty ' maxBytes ' daného souboru jako řetězec kódovaný v kódování UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Přesunout soubor

Přesune soubor nebo adresář. Podporuje přesun mezi systémy souborů.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Zapsat soubor

Zapíše daný řetězec do souboru kódovaný v kódování UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Připojit obsah k souboru

Připojí daný řetězec k souboru kódovanému v kódování UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Odstranit soubor nebo adresář

Odebere soubor nebo adresář.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>Nástroje pro přihlašovací údaje

Pomocí nástrojů pro přihlašovací údaje MSSparkUtils můžete získat přístupové tokeny propojených služeb a spravovat tajné klíče v Azure Key Vault. 

Spusťte následující příkaz, který vám umožní získat přehled dostupných metod:

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

Získat výsledek:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Získat token

Vrátí token Azure AD pro danou cílovou skupinu, název (volitelné). V tabulce níže najdete seznam všech dostupných typů cílových skupin: 

|Typ cílové skupiny|Klíč cílové skupiny|
|--|--|
|Typ řešení cílové skupiny|Osoby|
|Prostředek cílové skupiny úložiště|Pamì|
|Prostředek cílové skupiny datového skladu|DW|
|Prostředek Data Lake cílovou skupinu|'AzureManagement'|
|Prostředek skupiny trezorů|DataLakeStore|
|Prostředek cílové skupiny Azure OSSDB|'AzureOSSDB'|
|Prostředek Azure synapse|'Synapse'|
|Prostředek Azure Data Factory|ADF|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>Ověřit token

Vrátí hodnotu true, pokud platnost tokenu nevypršela.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Získání připojovacího řetězce nebo přihlašovacích údajů pro propojenou službu

Vrátí připojovací řetězec nebo přihlašovací údaje pro propojenou službu. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Získání tajného klíče pomocí identity pracovního prostoru

Vrátí tajný klíč Azure Key Vault pro daný název Azure Key Vault, název tajného kódu a název propojené služby pomocí identity pracovního prostoru. Ujistěte se, že jste správně nakonfigurovali přístup k [Azure Key Vault](#configure-access-to-azure-key-vault) .

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Získání tajného klíče pomocí přihlašovacích údajů uživatele

Vrátí tajný klíč Azure Key Vault pro daný název Azure Key Vault, název tajného kódu a název propojené služby pomocí přihlašovacích údajů uživatele. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

### <a name="put-secret-using-workspace-identity"></a>Vložení tajného kódu pomocí identity pracovního prostoru

Vloží Azure Key Vault tajný klíč pro daný název Azure Key Vault, název tajného kódu a název propojené služby pomocí identity pracovního prostoru. Ujistěte se, že jste správně nakonfigurovali přístup k [Azure Key Vault](#configure-access-to-azure-key-vault) .

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="put-secret-using-user-credentials"></a>Vložení tajného kódu pomocí přihlašovacích údajů uživatele

Vloží Azure Key Vault tajný klíč pro daný název Azure Key Vault, název tajného kódu a název propojené služby pomocí přihlašovacích údajů uživatele. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


## <a name="environment-utilities"></a>Nástroje pro prostředí 

Spusťte následující příkazy, které vám pomohou získat přehled dostupných metod:

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

Získat výsledek:
```
getUserName(): returns user name
getUserId(): returns unique user id
getJobId(): returns job id
getWorkspaceName(): returns workspace name
getPoolName(): returns Spark pool name
getClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Získat uživatelské jméno

Vrátí aktuální uživatelské jméno.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>Získat ID uživatele

Vrátí ID aktuálního uživatele.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>Získat ID úlohy

Vrátí ID úlohy.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>Získat název pracovního prostoru

Vrátí název pracovního prostoru.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>Získat název fondu

Vrátí název fondu Spark.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>Získat ID clusteru

Vrátí aktuální ID clusteru.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>Další kroky

- [Podívejte se na ukázkové poznámkové bloky synapse](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Rychlý Start: Vytvoření fondu Apache Spark ve službě Azure synapse Analytics pomocí nástrojů pro web](../quickstart-apache-spark-notebook.md)
- [Co je Apache Spark ve službě Azure synapse Analytics](apache-spark-overview.md)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)