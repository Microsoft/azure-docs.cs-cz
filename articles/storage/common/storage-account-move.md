---
title: Přesunutí účtu Azure Storage do jiné oblasti | Microsoft Docs
description: Ukazuje, jak přesunout Azure Storage účet do jiné oblasti.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 64d987ff01e596eefa98e8086788546c465e2d83
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195399"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Přesunutí účtu Azure Storage do jiné oblasti

Pokud chcete přesunout účet úložiště, vytvořte kopii účtu úložiště v jiné oblasti. Pak přesuňte data do tohoto účtu pomocí AzCopy nebo jiného nástroje podle vašeho výběru.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> 
> * Exportujte šablonu.
> * Upravte šablonu přidáním cílové oblasti a názvu účtu úložiště.
> * Nasaďte šablonu pro vytvoření nového účtu úložiště.
> * Nakonfigurujte nový účet úložiště.
> * Přesuňte data do nového účtu úložiště.
> * Odstraňte prostředky ve zdrojové oblasti.

## <a name="prerequisites"></a>Požadavky

- Zajistěte, aby služby a funkce používané vaším účtem byly podporovány v cílové oblasti.

- V případě funkcí verze Preview se ujistěte, že je vaše předplatné na seznamu povolených pro cílovou oblast.

<a id="prepare"></a>

## <a name="prepare"></a>Příprava

Začněte tím, že vyexportujete a pak upravíte šablonu Správce prostředků. 

### <a name="export-a-template"></a>Exportovat šablonu

Tato šablona obsahuje nastavení, která popisují váš účet úložiště. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Export šablony pomocí Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **všechny prostředky** a pak vyberte svůj účet úložiště.

3. Vyberte > **Nastavení**  >  **Exportovat šablonu**.

4. V okně **Exportovat šablonu** vyberte **Stáhnout** .

5. Vyhledejte soubor. zip, který jste stáhli z portálu, a rozbalte tento soubor do složky podle vašeho výběru.

   Tento soubor zip obsahuje soubory. JSON, které tvoří šablonu a skripty pro nasazení šablony.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Export šablony pomocí prostředí PowerShell:

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, který chcete přesunout.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportujte šablonu svého zdrojového účtu úložiště. Tyto příkazy uloží šablonu JSON do aktuálního adresáře.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>Úprava šablony 

Upravte šablonu tak, že změníte název a oblast účtu úložiště.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Nasazení šablony pomocí Azure Portal:

1. Na webu Azure Portal vyberte **Vytvořit prostředek**.

2. Do pole **Hledat na Marketplace** zadejte **template deployment** a stiskněte **ENTER**.

3. Vyberte **Template deployment**.

    ![Knihovna šablon Azure Resource Manageru](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Vyberte **Vytvořit**.

5. Vyberte **Vytvořit vlastní šablonu v editoru**.

6. Vyberte **načíst soubor**a pak podle pokynů načtěte soubor **template. JSON** , který jste stáhli v poslední části.

7. V souboru **template. JSON** pojmenujte cílový účet úložiště nastavením výchozí hodnoty názvu účtu úložiště. V tomto příkladu se nastaví výchozí hodnota názvu účtu úložiště na `mytargetaccount` .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód oblasti je název oblasti bez mezer, **střed USA**  =  **centralus**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nasazení šablony pomocí prostředí PowerShell:

1. V souboru **template. JSON** pojmenujte cílový účet úložiště nastavením výchozí hodnoty názvu účtu úložiště. V tomto příkladu se nastaví výchozí hodnota názvu účtu úložiště na `mytargetaccount` .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Upravte vlastnost **umístění** v souboru **template. JSON** do cílové oblasti. Tento příklad nastaví cílovou oblast na `eastus` .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Kódy oblastí můžete získat spuštěním příkazu [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) .

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Přesunout

Nasaďte šablonu pro vytvoření nového účtu úložiště v cílové oblasti. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Uložte soubor **template. JSON** .

2. Zadejte nebo vyberte hodnoty vlastností:

- **Předplatné**: vyberte předplatné Azure.

- **Skupina prostředků**: Vyberte **Vytvořit novou** a zadejte název nové skupiny prostředků.

- **Umístění**: vyberte umístění Azure.

3. Klikněte na **Souhlasím s výše uvedenými podmínkami a ujednáními** a pak klikněte na tlačítko **Vybrat nákup** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Získejte ID předplatného, do kterého chcete nasadit cílovou veřejnou IP adresu pomocí [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. K nasazení šablony použijte tyto příkazy:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Konfigurace nového účtu úložiště

Některé funkce nebudou exportovány do šablony, takže je budete muset přidat do nového účtu úložiště. 

Následující tabulka uvádí tyto funkce spolu s pokyny pro jejich přidání do nového účtu úložiště.

| Příznak    | Doprovodné materiály    |
|--------|-----------|
| **Zásady správy životního cyklu** | [Správa životního cyklu úložiště objektů blob v Azure](../blobs/storage-lifecycle-management-concepts.md) |
| **Statické weby** | [Hostování statického webu v Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Odběry událostí** | [Reakce na události služby Blob Storage](../blobs/storage-blob-event-overview.md) |
| **Výstrahy** | [Vytváření, zobrazování a správa výstrah protokolu aktivit pomocí Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Použití Azure CDN pro přístup k objektům blob s vlastními doménami přes HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Pokud nastavíte CDN pro zdrojový účet úložiště, stačí změnit původ vaší stávající sítě CDN na primární koncový bod služby BLOB Service (nebo na primární koncový bod statického webu) vašeho nového účtu. 

### <a name="move-data-to-the-new-storage-account"></a>Přesunout data do nového účtu úložiště

AzCopy je preferovaný nástroj pro přesun vašich dat. Je optimalizovaná pro výkon.  Jedním ze způsobů, jak je to rychlejší, je to, že se data zkopírují přímo mezi servery úložiště, takže AzCopy nepoužívá šířku pásma sítě vašeho počítače. Použijte AzCopy na příkazovém řádku nebo jako součást vlastního skriptu. Viz Začínáme [s AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

K přesunu dat můžete použít také Azure Data Factory. Poskytuje intuitivní uživatelské rozhraní. Pokud chcete použít Azure Data Factory, přečtěte si některý z těchto odkazů:. 

  - [Kopírování dat do nebo z úložiště objektů BLOB v Azure pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Kopírování dat do nebo z Azure Data Lake Storage Gen2 pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopírování dat z nebo do Azure File Storage pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Kopírování dat z a do úložiště tabulek v Azure pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Zahodit nebo vyčistit

Pokud po nasazení chcete začít znovu, můžete cílový účet úložiště odstranit a postup opakovat postupem popsaným v části [Příprava](#prepare) a [Přesun](#move) v tomto článku.

Pokud chcete potvrdit změny a dokončit přesun účtu úložiště, odstraňte zdrojový účet úložiště.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Postup odebrání účtu úložiště pomocí Azure Portal:

1. V Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a výběrem možnosti **účty úložiště** zobrazte seznam účtů úložiště.

2. Vyhledejte cílový účet úložiště, který chcete odstranit, a klikněte pravým tlačítkem myši na tlačítko **Další** (**...**) na pravé straně seznamu.

3. Vyberte **Odstranit**a potvrďte.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli účet Azure Storage z jedné oblasti na jiný a vyčistili zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
