---
title: Odstranění a export osobních údajů z Azure DevTest Labs
description: Naučte se, jak odstranit a exportovat osobní data ze služby Azure DevLast Labs, abyste mohli podporovat vaše závazky v rámci Obecné nařízení o ochraně osobních údajů (GDPR).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2c44b2f3aa6f2dfad18ed53804842a5dad8bd94a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483512"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Export nebo odstranění osobních údajů z Azure DevTest Labs
Tento článek popisuje kroky pro odstranění a export osobních údajů ze služby Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Jaké osobní údaje shromažďuje DevTest Labs?
DevTest Labs shromažďuje ze uživatele dvě hlavní části osobních údajů. Jsou to: e-mailová adresa uživatele a ID objektu uživatele. Tyto informace jsou klíčové pro službu, která poskytuje funkce služby pro správce testovacího prostředí a uživatele testovacího prostředí.

### <a name="user-email-address"></a>E-mailová adresa uživatele
DevTest Labs používá e-mailovou adresu uživatele k odesílání e-mailových oznámení automatického vypnutí uživatelům testovacího prostředí. E-mail upozorňuje uživatele na vypnutí počítače. Uživatelé můžou vypnutí odložit nebo přeskočit, pokud to chtějí. E-mailovou adresu můžete nakonfigurovat na úrovni testovacího prostředí nebo na úrovni virtuálního počítače.

**Nastavení e-mailu v testovacím prostředí:**

![Nastavení e-mailu na úrovni testovacího prostředí](./media/personal-data-delete-export/lab-user-email.png)

**Nastavení e-mailu na virtuálním počítači:**

![Nastavení e-mailu na úrovni virtuálního počítače](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID objektu uživatele
DevTest Labs pomocí ID objektu uživatele zobrazuje trendy nákladů na měsíc a náklady podle informací o zdroji pro správce testovacího prostředí. Umožňuje jim sledovat náklady a spravovat prahové hodnoty pro své testovací prostředí. 

**Trend odhadovaných nákladů pro aktuální kalendářní měsíc:** 
 ![ Trend odhadovaných nákladů pro aktuální kalendářní měsíc](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Odhadované náklady na měsíc na data podle prostředku:** 
 ![ Odhadované náklady na měsíc na data podle prostředku](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Proč potřebujeme tyto osobní údaje?
Služba DevTest Labs používá pro provozní účely osobní údaje. Tato data jsou kritická, aby služba poskytovala klíčové funkce. Pokud v e-mailové adrese uživatele nastavíte zásady uchovávání informací, uživatelé testovacího prostředí neobdrží včas e-mailová oznámení automatického vypnutí po odstranění své e-mailové adresy z našeho systému. Podobně správce testovacího prostředí nemůže při odstraňování ID uživatelských objektů na základě zásady uchovávání dat zobrazit trendy a náklady na měsíční náklady podle prostředků pro počítače ve svých cvičeních. Proto je potřeba uchovat tato data po dobu, po kterou je prostředek uživatele aktivní v testovacím prostředí.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Jak můžu systému zapomenout svoje osobní údaje?
Pokud chcete, aby se tato osobní data odstranila jako uživatel testovacího prostředí, můžete to udělat tak, že odstraníte odpovídající prostředek v testovacím prostředí. Služba DevTest Labs zajistila jejich anonymita odstraněné osobní údaje po dobu 30 dnů od odstranění uživatelem.

Pokud například odstraníte virtuální počítač nebo odeberete svoji e-mailovou adresu, služba DevTest Labs zajistila jejich anonymita tato data 30 dní od odstranění prostředku. 30denní zásady uchovávání informací po odstranění se ujistěte, že poskytujeme přesné promítání měsíčních nákladů na měsíc pro správce testovacího prostředí.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Jak můžu požádat o export na moje osobní údaje?
Data o využití osobních a testovacích prostředí můžete exportovat pomocí Azure Portal nebo PowerShellu. Data se exportují ve formě dvou různých souborů CSV:

- **disks.csv** – obsahuje informace o discích používaných různými virtuálními počítači.
- **virtualmachines.csv** – obsahuje informace o virtuálních počítačích v testovacím prostředí.

### <a name="azure-portal"></a>portál Azure
Jako uživatel testovacího prostředí můžete požádat o export na osobní údaje, které ukládá služba DevTest Labs. Pokud chcete požádat o export, přejděte na možnost **osobní údaje** na stránce **Přehled** v testovacím prostředí. Kliknutím na tlačítko **Export žádosti** vyhájíte vytváření excelového souboru ke stažení v účtu úložiště pro správce laboratoře. Pak můžete požádat správce testovacího prostředí o zobrazení těchto dat.

1. V nabídce vlevo vyberte **osobní údaje** . 

    ![Stránka osobní údaje](./media/personal-data-delete-export/personal-data-page.png)
2. Vyberte **skupinu prostředků** , která obsahuje testovací prostředí.

    ![Výběr skupiny prostředků](./media/personal-data-delete-export/select-resource-group.png)
3. Ve skupině prostředků vyberte **účet úložiště** .
4. Na stránce **účet úložiště** vyberte **objekty blob**.

    ![Dlaždice pro výběr objektů BLOB](./media/personal-data-delete-export/select-blobs-tile.png)
5. V seznamu kontejnerů vyberte kontejner s názvem **labresourceusage** .

    ![Vybrat kontejner objektů BLOB](./media/personal-data-delete-export/select-blob-container.png)
6. Vyberte **složku** s názvem po vašem testovacím prostředí. V této složce najdete soubory **CSV** pro **disky** a **virtuální počítače** v testovacím prostředí. Tyto soubory CSV si můžete stáhnout, filtrovat obsah pro uživatele testovacího prostředí, který požaduje přístup, a sdílet ho s nimi.

    ![Stáhnout soubor CSV](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

Mezi klíčové komponenty v ukázce výše patří:

- Příkaz Invoke-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Dva parametry akce
    - **blobStorageAbsoluteSasUri** – identifikátor URI účtu úložiště s tokenem sdíleného přístupového podpisu (SAS). Ve skriptu PowerShellu by se tato hodnota dala předat místo klíče úložiště.
    - **usageStartDate** – počáteční datum, kdy se mají načíst data, s koncovým datem, ke kterému se má akce provést. Členitost je na úrovni dne, takže i když přidáváte informace o čase, bude ignorována.

### <a name="exported-data---a-closer-look"></a>Exportovaná data – lepší vzhled
Teď se podíváme na exportovaná data. Jak bylo zmíněno dříve, po úspěšném exportu dat budou k dispozici dva soubory CSV. 

**virtualmachines.csv** obsahuje následující sloupce dat:

| Název sloupce | Popis |
| ----------- | ----------- | 
| SubscriptionId | Identifikátor předplatného, ve kterém testovací prostředí existuje. |
| LabUId | Jedinečný identifikátor GUID pro testovací prostředí |
| LabName | Název testovacího prostředí. |
| LabResourceId | Plně kvalifikované ID prostředku testovacího prostředí. |
| ResourceGroupName | Název skupiny prostředků, která obsahuje virtuální počítač | 
| ResourceId | Plně kvalifikované ID prostředku pro virtuální počítač. |
| ResourceUId | Identifikátor GUID virtuálního počítače |
| Name | Název virtuálního počítače |
| CreatedTime | Datum a čas, kdy byl virtuální počítač vytvořen. |
| DeletedDate | Datum a čas odstranění virtuálního počítače. Pokud je prázdný, odstranění ještě neproběhlo. |
| ResourceOwner | Vlastník virtuálního počítače Pokud je hodnota prázdná, je to buď vynucený virtuální počítač, nebo vytvořený instančním objektem. |
| PricingTier | Cenová úroveň virtuálního počítače |
| ResourceStatus | Stav dostupnosti virtuálního počítače. Aktivní, pokud pořád existuje, nebo neaktivní, pokud byl virtuální počítač odstraněný. |
| ComputeResourceId | Plně kvalifikovaný identifikátor prostředků COMPUTE virtuálních počítačů. |
| Jako nárokovatelný | Nastavte na hodnotu true, pokud se jedná o virtuální počítač s vynuceným nárokem. | 
| EnvironmentId | Identifikátor prostředku prostředí, ve kterém se virtuální počítač vytvořil. Pokud se virtuální počítač nevytvořil jako součást prostředku prostředí, je prázdný. |
| ExpirationDate | Datum vypršení platnosti virtuálního počítače. Pokud není datum vypršení platnosti nastaveno, je nastaveno na hodnotu Empty.
| GalleryImageReferenceVersion |  Verze základní image virtuálního počítače |
| GalleryImageReferenceOffer | Nabídka základní image virtuálního počítače |
| GalleryImageReferencePublisher | Vydavatel základní image virtuálního počítače |
| GalleryImageReferenceSku | SKU základní image virtuálního počítače |
| GalleryImageReferenceOsType | Typ operačního systému pro základní bitovou kopii virtuálního počítače |
| CustomImageId | Plně kvalifikované ID základní vlastní image virtuálního počítače |

Datové sloupce obsažené v **disks.csv** jsou uvedeny níže:

| Název sloupce | Popis | 
| ----------- | ----------- | 
| SubscriptionId | ID předplatného, které obsahuje testovací prostředí |
| LabUId | Identifikátor GUID testovacího prostředí |
| LabName | Název testovacího prostředí | 
| LabResourceId | Plně kvalifikované ID prostředku pro testovací prostředí | 
| ResourceGroupName | Název skupiny prostředků, která obsahuje testovací prostředí | 
| ResourceId | Plně kvalifikované ID prostředku pro virtuální počítač. |
| ResourceUId | Identifikátor GUID virtuálního počítače |
 |Name | Název připojeného disku |
| CreatedTime |Datum a čas vytvoření datového disku. |
| DeletedDate | Datum a čas odstranění datového disku. |
| ResourceStatus | Stav prostředku Aktivní, pokud prostředek existuje. Neaktivní, když je odstraněn. |
| DiskBlobName | Název objektu BLOB pro datový disk |
| DiskSizeGB | Velikost datového disku. |
| DiskType | Typ datového disku. 0 pro standard, 1 pro Premium |
| LeasedByVmId | ID prostředku virtuálního počítače, ke kterému je připojený datový disk |


> [!NOTE]
> Pokud pracujete s více cvičeními a chcete získat celkové informace, jsou tyto dva klíčové sloupce **LabUID** a **ResourceUId**, které jsou jedinečnými identifikátory v rámci předplatných.

Exportovaná data je možné manipulovat a vizuálně pomocí nástrojů, jako jsou SQL Server, Power BI atd. Tato funkce je užitečná hlavně v případě, že chcete k vašemu týmu pro správu ohlásit použití testovacího prostředí, které nepoužívá stejné předplatné Azure.

## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Nastavení zásad pro testovací prostředí](devtest-lab-set-lab-policy.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
