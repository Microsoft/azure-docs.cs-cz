---
title: Přesunutí účtu Úložiště Azure do jiné oblasti | Dokumenty společnosti Microsoft
description: Ukazuje, jak přesunout účet Azure Storage do jiné oblasti.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 8ce949ac997ba7ee38cb057752d89f4b4d22388f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838704"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Přesunutí účtu úložiště Azure do jiné oblasti

Pokud chcete přesunout účet úložiště, vytvořte kopii účtu úložiště v jiné oblasti. Potom přesuňte data do tohoto účtu pomocí AzCopy nebo jiného nástroje podle vašeho výběru.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> 
> * Exportovat šablonu.
> * Upravte šablonu přidáním cílové oblasti a názvu účtu úložiště.
> * Nasaďte šablonu a vytvořte nový účet úložiště.
> * Nakonfigurujte nový účet úložiště.
> * Přesuňte data do nového účtu úložiště.
> * Odstraňte prostředky ve zdrojové oblasti.

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že služby a funkce, které váš účet používá, jsou podporovány v cílové oblasti.

- U funkcí náhledu se ujistěte, že vaše předplatné je na seznamu povolených pro cílovou oblast.

<a id="prepare" />

## <a name="prepare"></a>Příprava

Chcete-li začít, exportujte a upravte šablonu Správce prostředků. 

### <a name="export-a-template"></a>Exportovat šablonu

Tato šablona obsahuje nastavení, která popisují váš účet úložiště. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Export šablony pomocí portálu Azure:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Vyberte **Všechny prostředky** a pak vyberte účet úložiště.

3. Vyberte > **nastavení** > **exportovat šablonu**.

4. V okně **Exportovat šablonu** zvolte **Stáhnout.**

5. Vyhledejte soubor ZIP, který jste stáhli z portálu, a rozbalte jej do složky podle vašeho výběru.

   Tento soubor zip obsahuje soubory JSON, které obsahují šablonu a skripty pro nasazení šablony.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Export šablony pomocí PowerShellu:

1. Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) a postupujte podle pokynů na obrazovce:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte aktivní předplatné na předplatné účtu úložiště, který chcete přesunout.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportujte šablonu svého účtu zdrojového úložiště. Tyto příkazy uloží šablonu json do aktuálního adresáře.

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

Upravte šablonu změnou názvu účtu úložiště a oblasti.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Nasazení šablony pomocí portálu Azure:

1. Na webu Azure Portal vyberte **Vytvořit prostředek**.

2. Do pole **Hledat na Marketplace** zadejte **template deployment** a stiskněte **ENTER**.

3. Vyberte **Template deployment**.

    ![Knihovna šablon Azure Resource Manageru](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Vyberte **Vytvořit**.

5. Vyberte **Vytvořit vlastní šablonu v editoru**.

6. Vyberte **Načíst soubor**a podle pokynů načtěte soubor **template.json,** který jste stáhli v poslední části.

7. V souboru **template.json** pojmenujte cílový účet úložiště nastavením výchozí hodnoty názvu účtu úložiště. Tento příklad nastaví výchozí hodnotu `mytargetaccount`názvu účtu úložiště na .
    
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
    Kódy umístění oblasti najdete v [tématu Umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Kód pro oblast je název oblasti bez mezer, **centrální US** = **centralus**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nasazení šablony pomocí PowerShellu:

1. V souboru **template.json** pojmenujte cílový účet úložiště nastavením výchozí hodnoty názvu účtu úložiště. Tento příklad nastaví výchozí hodnotu `mytargetaccount`názvu účtu úložiště na .
    
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

2. Upravte vlastnost **umístění** v souboru **template.json** do cílové oblasti. Tento příklad nastaví `eastus`cílovou oblast na .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Kódy oblastí můžete získat spuštěním příkazu [Get-AzLocation.](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Přesunout

Nasaďte šablonu a vytvořte nový účet úložiště v cílové oblasti. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Uložte soubor **template.json.**

2. Zadejte nebo vyberte hodnoty vlastností:

- **Předplatné**: Vyberte předplatné Azure.

- **Skupina prostředků**: Vyberte **Vytvořit novou** a zadejte název nové skupiny prostředků.

- **Umístění**: Vyberte umístění Azure.

3. Klikněte na zaškrtávací **políčko Souhlasím s výše uvedenými podmínkami** a potom klikněte na tlačítko **Vybrat nákup.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Získejte ID předplatného, kde chcete nasadit cílovou veřejnou IP adresu pomocí [služby Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

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

Některé funkce se do šablony exportují, takže je budete muset přidat do nového účtu úložiště. 

V následující tabulce jsou uvedeny tyto funkce spolu s pokyny pro jejich přidání do nového účtu úložiště.

| Funkce    | Doprovodné materiály    |
|--------|-----------|
| **Zásady správy životního cyklu** | [Správa životního cyklu úložiště objektů blob v Azure](../blobs/storage-lifecycle-management-concepts.md) |
| **Statické weby** | [Hostování statického webu ve službě Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Odběry událostí** | [Reakce na události služby Blob Storage](../blobs/storage-blob-event-overview.md) |
| **Výstrahy** | [Vytváření, zobrazení a správa výstrah protokolu aktivit pomocí Azure Monitoru](../../azure-monitor/platform/alerts-activity-log.md) |
| **Síť pro doručování obsahu (CDN)** | [Použití Azure CDN pro přístup k objektům BLOB s vlastními doménami přes PROTOKOL HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Pokud nastavíte CDN pro účet zdrojového úložiště, stačí změnit původ existujícího cdn na primární koncový bod služby objektů blob (nebo primární statický koncový bod webu) nového účtu. 

### <a name="move-data-to-the-new-storage-account"></a>Přesunutí dat do nového účtu úložiště

Zde je několik způsobů, jak přesunout data.

:heavy_check_mark: **Průzkumník úložišť Azure**

  Je snadno použitelný a vhodný pro malé datové sady. Můžete zkopírovat kontejnery a sdílené složky a pak je vložit do cílového účtu.

  Viz [Průzkumník úložišť Azure](https://azure.microsoft.com/features/storage-explorer/);

:heavy_check_mark: **AzCopy**

  Toto je upřednostňovaný přístup. Je optimalizován pro výkon.  Jedním ze způsobů, že je rychlejší, je, že data jsou kopírována přímo mezi servery úložiště, takže AzCopy nepoužívá šířku pásma sítě vašeho počítače. Použijte AzCopy na příkazovém řádku nebo jako součást vlastního skriptu.

  Viz [Začínáme s AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: **Azure Data Factory** 

  Tento nástroj použijte pouze v případě, že potřebujete funkce, které nejsou v aktuální verzi AzCopy podporovány. Například v aktuální verzi AzCopy nelze kopírovat objekty BLOB mezi účty, které mají hierarchický obor názvů. Také AzCopy nezachová seznamy řízení přístupu k souborům nebo časová razítka souborů (například: vytvořit a upravit časová razítka). 

  Podívejte se na tyto odkazy:
  - [Kopírování dat do úložiště objektů blob Azure nebo z nich pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Kopírování dat do nebo z Azure Data Lake Storage Gen2 pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopírování dat z úložiště souborů Azure nebo do služby Azure pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Kopírování dat do úložiště Azure Table a z nich pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Zlikvidovat nebo vyčistit

Po nasazení, pokud chcete začít znovu, můžete odstranit cílový účet úložiště a opakujte kroky popsané v [části Příprava](#prepare) a [přesunutí](#move) tohoto článku.

Chcete-li potvrdit změny a dokončit přesunutí účtu úložiště, odstraňte účet zdrojového úložiště.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Odebrání účtu úložiště pomocí portálu Azure:

1. Na webu Azure Portal rozbalte nabídku na levé straně a otevřete nabídku služeb a zvolte **Účty úložiště,** chcete-li zobrazit seznam účtů úložiště.

2. Vyhledejte cílový účet úložiště, který chcete odstranit, a klikněte pravým tlačítkem myši na tlačítko **Další** (**...**) na pravé straně výpisu.

3. Vyberte **Odstranit**a potvrďte.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [Remove-AzStorageAccount:](/powershell/module/az.resources/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli účet úložiště Azure z jedné oblasti do druhé a vyčistili zdrojové prostředky.  Další informace o přesunu prostředků mezi oblastmi a zotavení po havárii v Azure najdete v tématu:


- [Přesun prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Přesun virtuálních počítačů Azure do jiné oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
