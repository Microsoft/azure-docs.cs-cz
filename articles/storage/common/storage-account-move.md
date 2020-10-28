---
title: Přesunutí účtu Azure Storage do jiné oblasti | Microsoft Docs
description: Ukazuje, jak přesunout Azure Storage účet do jiné oblasti.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: b70beb90fae794eb5512cb8b466524169c4c7b53
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792986"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Přesunutí účtu Azure Storage do jiné oblasti

Pokud chcete přesunout účet úložiště, vytvořte jeho kopii v jiné oblasti. Pak přesuňte data do tohoto účtu pomocí AzCopy nebo jiného nástroje podle vašeho výběru.

V tomto článku se naučíte:

> [!div class="checklist"]
> 
> * Exportujte šablonu.
> * Upravte šablonu přidáním cílové oblasti a názvu účtu úložiště.
> * Nasaďte šablonu pro vytvoření nového účtu úložiště.
> * Nakonfigurujte nový účet úložiště.
> * Přesuňte data do nového účtu úložiště.
> * Odstraňte prostředky ve zdrojové oblasti.

## <a name="prerequisites"></a>Předpoklady

- Ujistěte se, že cílová oblast podporuje služby a funkce, které váš účet využívá.

- V případě funkcí Preview se ujistěte, že je vaše předplatné v cílové oblasti uvedené na seznamu povolených.

<a id="prepare"></a>

## <a name="prepare"></a>Příprava

Začněte tím, že vyexportujete a pak upravíte šablonu Správce prostředků. 

### <a name="export-a-template"></a>Export šablony

Tato šablona obsahuje nastavení, která popisují váš účet úložiště. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Export šablony pomocí webu Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **všechny prostředky** a pak vyberte svůj účet úložiště.

3. Vyberte > **Nastavení**  >  **Exportovat šablonu** .

4. V okně **Exportovat šablonu** vyberte **Stáhnout** .

5. Vyhledejte soubor. zip, který jste stáhli z portálu, a rozbalte tento soubor do složky podle vašeho výběru.

   Tento soubor zip obsahuje soubory. JSON, které tvoří šablonu a skripty pro nasazení šablony.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Export šablony pomocí prostředí PowerShell:

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a postupujte podle pokynů na obrazovce:

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

Upravte šablonu tím, že změníte název a oblast účtu úložiště.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Nasazení šablony pomocí Azure Portal:

1. V Azure Portal vyberte **vytvořit prostředek** .

2. V **části Hledat na Marketplace** zadejte **šablonu Deployment** a potom stiskněte **ENTER** .

3. Vyberte **template Deployment** .

    ![Knihovna šablon Azure Resource Manageru](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Vyberte **Vytvořit** .

5. **V editoru vyberte vytvořit vlastní šablonu** .

6. Vyberte **načíst soubor** a potom podle pokynů načtěte **template.js** do souboru, který jste stáhli v poslední části.

7. V **template.jsv** souboru pojmenujte cílový účet úložiště nastavením výchozí hodnoty názvu účtu úložiště. V tomto příkladu se nastaví výchozí hodnota názvu účtu úložiště na `mytargetaccount` .
    
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
    Pokud chcete získat kódy umístění oblastí, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód oblasti je název oblasti bez mezer, **střed USA**  =  **centralus** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nasazení šablony pomocí prostředí PowerShell:

1. V **template.jsv** souboru pojmenujte cílový účet úložiště nastavením výchozí hodnoty názvu účtu úložiště. V tomto příkladu se nastaví výchozí hodnota názvu účtu úložiště na `mytargetaccount` .
    
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

2. Upravte vlastnost **Location** v **template.js** souboru do cílové oblasti. Tento příklad nastaví cílovou oblast na `eastus` .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Kódy oblastí můžete získat spuštěním příkazu [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Přesunout

Nasaďte šablonu a vytvořte nový účet úložiště v cílové oblasti. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Uložte **template.jsdo** souboru.

2. Zadejte nebo vyberte hodnoty vlastností:

- **Předplatné** : vyberte předplatné Azure.

- **Skupina prostředků** : Vyberte **Vytvořit novou** a zadejte název nové skupiny prostředků.

- **Umístění** : vyberte umístění Azure.

3. Klikněte na **Souhlasím s výše uvedenými podmínkami a ujednáními** a pak klikněte na tlačítko **Vybrat nákup** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Získejte ID předplatného, do kterého chcete nasadit cílovou veřejnou IP adresu pomocí [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

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

Některé funkce se do šablony neexportují, takže je budete muset přidat do nového účtu úložiště. 

Následující tabulka obsahuje seznam těchto funkcí společně s pokyny k jejich přidání do nového účtu úložiště.

| Doporučené    | Pokyny    |
|--------|-----------|
| **Zásady správy životního cyklu** | [Správa životního cyklu úložiště objektů blob v Azure](../blobs/storage-lifecycle-management-concepts.md) |
| **Statické weby** | [Hostování statického webu v Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Odběry událostí** | [Reakce na události služby Blob Storage](../blobs/storage-blob-event-overview.md) |
| **Výstrahy** | [Vytváření, zobrazování a správa výstrah protokolu aktivit pomocí Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Použití Azure CDN pro přístup k objektům blob s vlastními doménami přes HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Pokud nastavíte CDN pro zdrojový účet úložiště, stačí změnit původ vaší stávající sítě CDN na primární koncový bod služby BLOB Service (nebo na primární koncový bod statického webu) vašeho nového účtu. 

### <a name="move-data-to-the-new-storage-account"></a>Přesunutí dat do nového účtu úložiště

AzCopy je preferovaný nástroj pro přesun vašich dat. Je optimalizovaný na výkon.  Jedním z důvodů, proč je rychlejší, je skutečnost, že data se kopírují přímo mezi servery úložiště, takže AzCopy nevyužívá šířku pásma sítě vašeho počítače. AzCopy můžete použít na příkazovém řádku nebo jako součást vlastního skriptu. Viz Začínáme [s AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

K přesunu dat můžete použít také Azure Data Factory. Poskytuje intuitivní uživatelské rozhraní. Pokud chcete použít Azure Data Factory, přečtěte si některý z těchto odkazů:. 

  - [Kopírování dat do nebo z úložiště Azure Blob Storage pomocí služby Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
  - [Kopírování dat do nebo z Azure Data Lake Storage Gen2 pomocí služby Azure Data Factory](/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopírování dat z nebo do Azure File Storage pomocí služby Azure Data Factory](/azure/data-factory/connector-azure-file-storage)
  - [Kopírování dat do a z Azure Table Storage pomocí služby Azure Data Factory](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Zahození nebo vyčištění

Pokud po nasazení chcete začít znovu, můžete cílový účet úložiště odstranit a postup opakovat postupem popsaným v části [Příprava](#prepare) a [Přesun](#move) v tomto článku.

Pokud chcete potvrdit změny a dokončit přesun účtu úložiště, odstraňte zdrojový účet úložiště.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Účet úložiště odeberete pomocí webu Azure Portal takto:

1. V Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a výběrem možnosti **účty úložiště** zobrazte seznam účtů úložiště.

2. Vyhledejte cílový účet úložiště, který chcete odstranit, a klikněte pravým tlačítkem myši na tlačítko **Další** ( **...** ) na pravé straně seznamu.

3. Vyberte **Odstranit** a potvrďte.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli účet Azure Storage z jedné oblasti na jiný a vyčistili zdrojové prostředky.  Další informace o přesouvání prostředků mezi oblastmi a zotavení po havárii v Azure najdete tady:


- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Přesun virtuálních počítačů Azure do jiné oblasti](../../site-recovery/azure-to-azure-tutorial-migrate.md)