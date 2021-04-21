---
title: Přesunout Azure Analysis Services do jiné oblasti | Microsoft Docs
description: Popisuje, jak přesunout prostředek Azure Analysis Services do jiné oblasti.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 2b698ffaddb4bc818eaabda34022ab58ff05fe5f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786346"
---
# <a name="move-analysis-services-to-a-different-region"></a>Přesunout Analysis Services do jiné oblasti

Tento článek popisuje, jak přesunout prostředek Analysis Services serveru do jiné oblasti Azure. Server můžete přesunout do jiné oblasti z řady důvodů, například pokud chcete využít výhod oblasti Azure, která se přiblíží uživatelům, používat plány služeb podporované pouze v konkrétních oblastech nebo splnit požadavky na interní zásady a požadavky zásad správného řízení. 

V tomto a přidruženém propojeném článku se dozvíte, jak:

> [!div class="checklist"]
> 
> * Zálohujte databázi modelů zdrojového serveru do [úložiště objektů BLOB](../storage/blobs/storage-blobs-introduction.md).
> * Exportujte [šablonu prostředků](../azure-resource-manager/templates/overview.md)zdrojového serveru.
> * Získání [sdíleného přístupového podpisu (SAS)](../storage/common/storage-sas-overview.md)úložiště
> * Upravte šablonu prostředků.
> * Nasaďte šablonu pro vytvoření nového cílového serveru.
> * Obnovte databázi modelu na novém cílovém serveru.
> * Ověřte nový cílový server a databázi.
> * Odstraňte zdrojový server.

Tento článek popisuje použití šablony prostředků k migraci jednoho Analysis Services serveru se **základní konfigurací** do jiné oblasti *a* skupiny prostředků ve stejném předplatném. Použití šablony uchovává nakonfigurované vlastnosti serveru, které zajišťují, aby byl cílový server nakonfigurován se stejnými vlastnostmi, kromě oblastí a skupin prostředků, jako zdrojový server. Tento článek nepopisuje přesunutí přidružených prostředků, které mohou být součástí stejné skupiny prostředků, jako je zdroj dat, úložiště a prostředky brány. 

Před přesunutím serveru do jiné oblasti se doporučuje vytvořit podrobný plán. Zvažte další prostředky, jako jsou brány a úložiště, které je potřeba přesunout taky. S jakýmkoli plánem je důležité provést jednu nebo více zkušebních operací přesunu pomocí testovacích serverů před přesunutím provozního serveru.

> [!IMPORTANT]
> Klientské aplikace a připojovací řetězce se připojují k Analysis Services pomocí úplného názvu serveru, což je identifikátor URI, který obsahuje oblast, ve které se server nachází. Například, `asazure://westcentralus.asazure.windows.net/advworks01`. Při přesunu serveru do jiné oblasti budete efektivně vytvářet nové prostředky serveru v jiné oblasti, která bude mít v identifikátoru URI názvu serveru jinou oblast. Klientské aplikace a připojovací řetězce používané ve skriptech se musí připojit k novému serveru pomocí nového identifikátoru URI názvu serveru. Použití [aliasu názvu serveru](analysis-services-server-alias.md) může zmírnit počet míst, po které se má změnit identifikátor URI názvu serveru, ale musí se implementovat před přesunem oblasti.

> [!IMPORTANT]
> Oblasti Azure používají různé rozsahy IP adres. Pokud máte výjimky brány firewall nakonfigurované pro oblast, ve které je server nebo účet úložiště v, může být nutné nakonfigurovat jiný rozsah IP adres. Další informace najdete v tématu [Nejčastější dotazy týkající se Analysis Services připojení k síti](analysis-services-network-faq.md).

> [!NOTE]
> Tento článek popisuje obnovení zálohy databáze na cílový server z kontejneru úložiště v oblasti zdrojového serveru. V některých případech může mít obnovení záloh z jiné oblasti špatný výkon, zejména pro velké databáze. Pro dosažení nejlepšího výkonu při obnovení databáze migrujte nebo vytvořte nový kontejner úložiště v oblasti cílového serveru. Před obnovením databáze na cílový Server zkopírujte záložní soubory. ABF z kontejneru úložiště zdrojové oblasti do kontejneru úložiště cílové oblasti. Mimo rozsah tohoto článku, v některých případech, zejména u velmi rozsáhlých databází, skriptování z databáze ze zdrojového serveru, opětovné vytvoření a zpracování na cílovém serveru pro načtení databázových dat může být mnohem cenově výhodnější než použití zálohování a obnovení.

> [!NOTE]
> Pokud k připojení ke zdrojům dat používáte místní bránu dat, musíte taky přesunout prostředek brány do oblasti cílového serveru. Další informace najdete v tématu [instalace a konfigurace místní brány dat](analysis-services-gateway-install.md).

## <a name="prerequisites"></a>Požadavky

- **Účet úložiště Azure**: vyžaduje se pro uložení záložního souboru. ABF.
- **SQL Server Management Studio (SSMS)**: vyžaduje se pro zálohování a obnovení databází modelu.
- **Azure PowerShell.** Požadováno jenom v případě, že se rozhodnete tuto úlohu dokončit pomocí PowerShellu.

## <a name="prepare"></a>Příprava

### <a name="backup-model-databases"></a>Databáze modelu zálohování

Pokud **Nastavení úložiště** pro zdrojový server ještě nejsou nakonfigurovaná, postupujte podle kroků v části [Konfigurace nastavení úložiště](analysis-services-backup.md#configure-storage-settings).

Pokud jsou nakonfigurovaná nastavení úložiště, pomocí kroků v části [zálohování](analysis-services-backup.md#backup) vytvořte zálohu modelu. ABF v kontejneru úložiště. Později obnovíte zálohu. ABF na nový cílový server.


### <a name="export-template"></a>Export šablony

Šablona obsahuje konfigurační vlastnosti zdrojového serveru.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Export šablony pomocí webu Azure Portal:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Vyberte **všechny prostředky** a potom vyberte Server Analysis Services.

3. Vyberte > **Nastavení**  >  **Exportovat šablonu**.

4. V okně **Exportovat šablonu** vyberte **Stáhnout** .

5. Vyhledejte soubor. zip, který jste stáhli z portálu, a potom tento soubor rozbalte do složky.

   Soubor zip obsahuje soubory. JSON, které tvoří šablonu a parametry nezbytné k nasazení nového serveru.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Export šablony pomocí prostředí PowerShell:

1. Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a postupujte podle pokynů na obrazovce:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na odběr prostředku serveru, který chcete přesunout.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportujte šablonu zdrojového serveru. Tyto příkazy uloží šablonu JSON s ResourceGroupName jako názvem souboru do aktuálního adresáře.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Získání sdíleného přístupového podpisu úložiště (SAS)

Při nasazení cílového serveru ze šablony se vyžaduje token SAS pro delegování uživatele (jako identifikátor URI) k určení kontejneru úložiště obsahujícího zálohu databáze.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Získání sdíleného přístupového podpisu pomocí portálu:

1. Na portálu vyberte účet úložiště, který se použije k zálohování databáze serveru.

2. Vyberte **Průzkumník služby Storage** a potom rozbalte **kontejnery objektů BLOB**. 

3. Klikněte pravým tlačítkem na kontejner úložiště a pak vyberte **získat sdílený přístupový podpis**.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="Získání sdíleného přístupového podpisu":::

4. V případě **sdíleného přístupového podpisu** vyberte **vytvořit**. Ve výchozím nastavení vyprší platnost SAS za 24 hodin.

5. Zkopírujte a uložte **identifikátor URI**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete získat sdílený přístupový podpis pomocí PowerShellu, postupujte podle kroků v části [Vytvoření SAS delegování uživatelů pro kontejner nebo objekt BLOB s PowerShellem](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob).

---

### <a name="modify-the-template"></a>Úprava šablony

Textový editor slouží k úpravě template.jssouboru, který jste exportovali, ke změně oblasti a vlastností kontejneru objektů BLOB. 

Úprava šablony:

1. V textovém editoru zadejte do vlastnosti **Location** novou cílovou oblast. Do vlastnosti **backupBlobContainerUri** vložte identifikátor URI kontejneru úložiště s klíčem SAS. 

    Následující příklad nastaví cílovou oblast pro server advworks1 na `South Central US` a specifikuje identifikátor URI kontejneru úložiště se sdíleným přístupovým podpisem: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Uložte šablonu.

#### <a name="regions"></a>Oblasti

Pokud chcete získat oblasti Azure, přečtěte si téma [umístění Azure](https://azure.microsoft.com/global-infrastructure/locations/). Pokud chcete získat oblasti pomocí PowerShellu, spusťte příkaz [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Přesunout

K nasazení nového prostředku serveru v jiné oblasti použijete **template.jspro** soubor, který jste exportovali a upravili v předchozích částech.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Na portálu vyberte **vytvořit prostředek**.

2. V **části Hledat na Marketplace** zadejte **šablonu Deployment** a potom stiskněte **ENTER**.

3. Vyberte **template Deployment**.

4. Vyberte **Vytvořit**.

5. **V editoru vyberte vytvořit vlastní šablonu**.

6. Vyberte **načíst soubor** a potom podle pokynů načtěte **template.js** soubor, který jste exportovali a upravili.

7. Ověřte, že editor šablon zobrazuje správné vlastnosti nového cílového serveru.

8. Vyberte **Uložit**.

9. Zadejte nebo vyberte hodnoty vlastností:

    - **Předplatné:** Vyberte předplatné Azure.
    
    - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte název skupiny prostředků. Můžete vybrat existující skupinu prostředků, která už neobsahuje Analysis Services Server se stejným názvem.
    
    - **Umístění**: Vyberte stejnou oblast, kterou jste zadali v šabloně.

10. Vyberte možnost **zkontrolovat a vytvořit**.

11. Přečtěte si informace o pojmech a základech a pak vyberte **vytvořit**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

K nasazení šablony použijte tyto příkazy:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Získat identifikátor URI cílového serveru

Aby bylo možné se připojit k novému cílovému serveru z SSMS a obnovit databázi modelu, je třeba získat nový identifikátor URI cílového serveru.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Postup získání identifikátoru URI serveru na portálu:

1. Na portálu přejdete na nový prostředek cílového serveru.

2. Na stránce **Přehled** zkopírujte identifikátor URI **názvu serveru** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li získat identifikátor URI serveru pomocí prostředí PowerShell, použijte následující příkazy:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Zkopírujte **ServerFullName** z výstupu.

---

### <a name="restore-model-database"></a>Obnovit databázi modelu

Postupujte podle kroků popsaných v části [obnovení](analysis-services-backup.md#restore) a obnovte zálohu modelové databáze. ABF na nový cílový server.

Volitelné: po obnovení databáze modelu zpracujte model a tabulky, které aktualizují data ze zdrojů dat. Postup zpracování modelu a tabulky pomocí SSMS:

1. V SSMS klikněte pravým tlačítkem na databázi modelu > **procesní databáze**.

2. Rozbalte položku **tabulky**, klikněte pravým tlačítkem myši na tabulku. V **tabulce procesu**, vyberte všechny tabulky a pak vyberte **OK**.

## <a name="verify"></a>Ověření

1. Na portálu přejdete na nový cílový server.

2. Na stránce Přehled v části **modely na serveru Analysis Services** ověřte, zda se zobrazí obnovené modely.

3. K připojení k modelu na novém serveru použijte klientskou aplikaci, jako je Power BI nebo Excel. Ověřte, zda se zobrazí objekty modelu, jako jsou tabulky, míry, hierarchie. 

4. Spusťte všechny skripty pro automatizaci. Ověřte, že se úspěšně provedly.

Volitelné: [Alm Toolkit](http://alm-toolkit.com/) je *Open Source* Nástroj pro porovnávání a správu Power BI datových sad *a* Analysis Services databází tabelárních modelů. Pomocí sady nástrojů se připojte ke zdrojovému i cílovému serveru databáze a porovnejte je. Pokud je migrace databáze úspěšná, objekty modelu budou stejnou definicí. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po ověření, že se klientské aplikace můžou připojit k novému serveru a správně se spouštějí nějaké skripty pro automatizaci, odstraňte zdrojový server. 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Odstranění zdrojového serveru z portálu:

Na stránce **Přehled** na zdrojovém serveru vyberte **Odstranit**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete zdrojový server odstranit pomocí PowerShellu, použijte příkaz Remove-AzAnalysisServicesServer.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Po dokončení přesunu oblasti se doporučuje, aby nový cílový server používal kontejner úložiště ve stejné oblasti pro zálohování místo kontejneru úložiště v oblasti zdrojového serveru.
