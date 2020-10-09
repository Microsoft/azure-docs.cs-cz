---
title: Přístup ke službě Azure Blob Storage s využitím služeb Azure Databricks a Azure Key Vault
description: V tomto kurzu se dozvíte, jak získat přístup k Azure Blob Storage z Azure Databricks pomocí tajného kódu uloženého v Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: bb574bb3dd000682090c6c3f861e885761753e19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588513"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Kurz: přístup k Azure Blob Storage pomocí Azure Databricks a Azure Key Vault

V tomto kurzu se dozvíte, jak získat přístup k Azure Blob Storage z Azure Databricks pomocí tajného kódu uloženého v Azure Key Vault. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu úložiště a kontejneru objektů BLOB pomocí Azure CLI
> * Vytvoření Key Vault a nastavení tajného klíče
> * Vytvoření pracovního prostoru Azure Databricks a přidání rozsahu Key Vault tajného klíče
> * Přístup k kontejneru objektů BLOB z pracovního prostoru Azure Databricks

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Než začnete s tímto kurzem, nainstalujte rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Vytvoření účtu úložiště a kontejneru objektů BLOB pomocí Azure CLI

Abyste mohli objekty blob používat, musíte nejdřív vytvořit účet úložiště pro obecné účely. Pokud [skupinu prostředků](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)nemáte, vytvořte ji před spuštěním příkazu. Následující příkaz vytvoří a zobrazí metadata kontejneru úložiště. Zkopírujte **ID**.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Výstup na konzole výše uvedeného příkazu ID je zvýrazněné zeleně pro koncového uživatele.](../media/databricks-command-output-1.png)

Než budete moct vytvořit kontejner pro nahrání objektu blob do, budete muset roli [přispěvatele dat objektů BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) přiřadit sami sobě. V tomto příkladu se role přiřadí k účtu úložiště, který jste provedli dříve.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Teď, když přiřadíte roli k účtu úložiště, můžete vytvořit kontejner pro svůj objekt BLOB.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

Po vytvoření kontejneru můžete do tohoto kontejneru nahrát objekt BLOB (soubor podle vašeho výběru). V tomto příkladu se nahraje soubor. txt s HelloWorld.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Seznam objektů BLOB v kontejneru, aby bylo možné ověřit, zda má kontejner.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Výstup na konzole výše uvedeného příkazu Zobrazuje soubor, který byl právě uložen v kontejneru.](../media/databricks-command-output-2.png)

Pomocí následujícího příkazu Získejte hodnotu **klíč1** kontejneru úložiště. Zkopírujte hodnotu dolů.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Výstup na konzole výše uvedeného příkazu Hodnota klíč1 je zvýrazněna zeleným polem.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Vytvoření Key Vault a nastavení tajného klíče

Pomocí následujícího příkazu vytvoříte Key Vault. Tento příkaz zobrazí i metadata Key Vault. Zkopírujte **ID** a **vaultUri**.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![](../media/databricks-command-output-4.png)
 ![ Výstup konzoly obrázků výše uvedeného příkazu ID a vaultUri jsou zvýrazněné zeleně, aby se uživatel mohl podívat.](../media/databricks-command-output-5.png)

Tajný klíč vytvoříte pomocí následujícího příkazu. Nastavte hodnotu tajného kódu na hodnotu **klíč1** z vašeho účtu úložiště.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Vytvoření pracovního prostoru Azure Databricks a přidání rozsahu Key Vault tajného klíče

Tuto část nelze dokončit prostřednictvím příkazového řádku. Postupujte podle tohoto [Průvodce](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). Budete potřebovat přístup k [Azure Portal](https://ms.portal.azure.com/#home) k těmto akcím:

1. Vytvoření prostředku Azure Databricks
1. Spustit pracovní prostor
1. Vytvoření oboru tajného klíče, který je zálohovaný Key Vault

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Přístup k kontejneru objektů BLOB z pracovního prostoru Azure Databricks

Tuto část nelze dokončit prostřednictvím příkazového řádku. Postupujte podle tohoto [Průvodce](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). Pracovní prostor Azure Databricks budete muset použít k těmto akcím:

1. Vytvořit **nový cluster**
1. Vytvoření **nového poznámkového bloku**
1. Vyplňte příslušná pole ve skriptu Pythonu.
1. Spuštění skriptu Python

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Další kroky

Ujistěte se, že je vaše Key Vault obnovitelné:
> [!div class="nextstepaction"]
> [Vyčištění prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)
