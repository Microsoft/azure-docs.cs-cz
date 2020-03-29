---
title: Jak odstranit a exportovat osobní data z Azure DevTest Labs
description: Přečtěte si, jak odstranit a exportovat osobní údaje ze služby Azure DevLast Labs, abyste podpořili své povinnosti podle obecného nařízení o ochraně osobních údajů (GDPR).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169688"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Export nebo odstranění osobních dat z Azure DevTest Labs
Tento článek obsahuje postup pro odstranění a export osobních dat ze služby Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Jaké osobní údaje devTest Labs shromažďuje?
DevTest Labs shromažďuje od uživatele dvě hlavní osobní údaje. Jsou to: e-mailová adresa uživatele a ID objektu uživatele. Tyto informace jsou důležité pro službu poskytovat funkce v provozu pro správce testovacího prostředí a uživatele testovacího prostředí.

### <a name="user-email-address"></a>E-mailová adresa uživatele
DevTest Labs používá e-mailovou adresu uživatele k odesílání e-mailových oznámení automatického vypnutí uživatelům testovacího prostředí. E-mail upozorní uživatele jejich vypnutí počítače. Uživatelé mohou vypnutí odložit nebo přeskočit, pokud si to přejí. Nakonfigurujete e-mailovou adresu na úrovni testovacího prostředí nebo na úrovni virtuálního počítače.

**Nastavení e-mailu v laboratoři:**

![Nastavení e-mailu na úrovni laboratoře](./media/personal-data-delete-export/lab-user-email.png)

**Nastavení e-mailu na virtuálním domě:**

![Nastavení e-mailu na úrovni virtuálního soudu](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID objektu uživatele
DevTest Labs používá ID objektu uživatele k zobrazení měsíčních trendů nákladů a nákladů podle informací o prostředcích správcům testovacího prostředí. To jim umožňuje sledovat náklady a spravovat prahové hodnoty pro jejich lab. 

**Odhadovaný trend nákladů pro aktuální kalendářní měsíc:**
![Odhadovaný trend nákladů pro aktuální kalendářní měsíc](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Odhadované náklady od měsíce podle zdroje:**
![Odhadované náklady od měsíce podle zdroje](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Proč tyto osobní údaje potřebujeme?
Služba DevTest Labs používá osobní údaje pro provozní účely. Tato data jsou velmi důležitá pro poskytování klíčových funkcí služby. Pokud nastavíte zásady uchovávání informací na e-mailovou adresu uživatele, uživatelé testovacího prostředí neobdrží včasná e-mailová oznámení automatického vypnutí po odstranění e-mailové adresy z našeho systému. Podobně správce testovacího prostředí nemůže zobrazit měsíční náklady a náklady podle prostředků pro počítače v jejich testovacích prostředích, pokud jsou id objektu uživatele odstraněny na základě zásad uchovávání informací. Proto tato data musí být zachována tak dlouho, dokud prostředek uživatele je aktivní v testovacím prostředí.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Jak mohu mít systém, který zapomene mé osobní údaje?
Jako uživatel testovacího prostředí, pokud chcete mít tyto osobní údaje odstraněny, můžete tak učinit odstraněním odpovídající prostředek v laboratoři. Služba DevTest Labs anonymizuje smazané osobní údaje 30 dní po jejich smazání uživatelem.

Pokud například odstraníte virtuální počítač nebo odeberete e-mailovou adresu, služba DevTest Labs tato data anonymizuje 30 dní po odstranění prostředku. 30denní zásady uchovávání informací po odstranění je ujistěte se, že poskytujeme přesné měsíc-přes měsíc projekce nákladů na správce testovacího prostředí.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Jak mohu požádat o export svých osobních údajů?
Data o využití osobních a testovacích prostředí můžete exportovat pomocí portálu Azure nebo PowerShellu. Data jsou exportována jako dva různé soubory CSV:

- **disks.csv** - obsahuje informace o discích používaných různými virtuálními počítači
- **virtualmachines.csv** – obsahuje informace o virtuálních počítačích v testovacím prostředí.

### <a name="azure-portal"></a>portál Azure
Jako uživatel testovacího prostředí můžete požádat o export osobních dat, které ukládá služba DevTest Labs. Chcete-li požádat o export, přejděte na stránce **Přehled** testovacího prostředí na stránce **Osobní údaje.** Vyberte tlačítko **Žádost o export** spustí vytvoření souboru excelu ke stažení v účtu úložiště správce lab. Tato data pak můžete zobrazit na správce testovacího prostředí.

1. V levé nabídce vyberte **Možnost Osobní údaje.** 

    ![Stránka S osobními údaji](./media/personal-data-delete-export/personal-data-page.png)
2. Vyberte **skupinu prostředků,** která obsahuje testovací prostředí.

    ![Výběr skupiny prostředků](./media/personal-data-delete-export/select-resource-group.png)
3. Vyberte **účet úložiště** ve skupině prostředků.
4. Na stránce **Účet úložiště** vyberte **Objekty blob**.

    ![Vybrat dlaždici Objektů blob](./media/personal-data-delete-export/select-blobs-tile.png)
5. Vyberte kontejner s názvem **labresourceusage** v seznamu kontejnerů.

    ![Výběr kontejneru objektů blob](./media/personal-data-delete-export/select-blob-container.png)
6. Vyberte **složku** pojmenovanou po testovacím prostředí. Soubory **CSV** pro **disky** a **virtuální počítače** najdete v testovacím prostředí v této složce. Můžete si stáhnout tyto soubory csv, filtrovat obsah pro uživatele testovacího prostředí, který požaduje přístup, a sdílet je s nimi.

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

Klíčové součásti výše uvedeného vzorku jsou:

- Příkaz Invoke-AzureRmResourceaction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Dva parametry akce
    - **blobStorageAbsoluteSasUri** – identifikátor URI účtu úložiště s tokenem s sdíleným přístupovým podpisem (SAS). Ve skriptu Prostředí PowerShell může být tato hodnota předána místo klíče úložiště.
    - **usageStartDate** - Počáteční datum pro vyžádat data, koncové datum je aktuální datum, kdy je akce provedena. Členitost je na úrovni dne, takže i když přidáte informace o čase, bude ignorována.

### <a name="exported-data---a-closer-look"></a>Exportovaná data - bližší pohled
Nyní se podívejme blíže na exportovaná data. Jak již bylo zmíněno dříve, jakmile jsou data úspěšně exportována, budou existovat dva soubory CSV. 

**VirtualMachines.csv** obsahuje následující datové sloupce:

| Název sloupce | Popis |
| ----------- | ----------- | 
| SubscriptionId | Identifikátor předplatného, ve které existuje testovací prostředí. |
| LabUId | Jedinečný identifikátor IDENTIFIKÁTOR GUID pro testovací prostředí |
| Název lab | Jméno laboratoře. |
| LabResourceId | Plně kvalifikované ID prostředků testovacího prostředí. |
| ResourceGroupName | Název skupiny prostředků, která obsahuje virtuální ms | 
| ResourceId | Plně kvalifikované ID prostředků pro virtuální ho svícen. |
| ResourceUId | Identifikátor GUID pro virtuální hod |
| Name (Název) | Název virtuálního počítače. |
| CreatedTime | Datum, kdy byl virtuální virtuální ms vytvořen. |
| Odstraněné datum | Datum, kdy byl virtuální montovna odstraněn. Pokud je prázdný, odstranění ještě nedošlo. |
| Vlastník zdroje | Vlastník virtuálního soudu. Pokud je hodnota prázdná, pak je to nárokovatelný virtuální ms nebo vytvořený instančníobjekt. |
| Cenové ocejení | Cenová úroveň virtuálního mísy |
| ResourceStatus | Stav dostupnosti virtuálního soudu. Aktivní, pokud stále existuje nebo neaktivní, pokud byl virtuální virtuální hotel odstraněn. |
| Id výpočetního prostředku | Plně kvalifikovaný identifikátor výpočetního prostředku virtuálního počítače. |
| Nárokovatelné | Nastaveno na hodnotu true, pokud je virtuální ms nárokovatelný virtuální ms | 
| EnvironmentId | Identifikátor prostředku prostředí, ve kterém byl virtuální počítač vytvořen. Je prázdný, když virtuální virtuální byl vytvořen jako součást prostředku prostředí. |
| Datum vypršení platnosti | Datum vypršení platnosti pro virtuální ho dispozice. Je nastavena na prázdnou, pokud nebylo nastaveno datum vypršení platnosti.
| GalerieImageReferenceVersion |  Verze základní image virtuálního virtuálního mě. |
| GalleryImageReferenceOffer | Nabídka základní image virtuálního virtuálního mě. |
| GalleryImageReferencePublisher | Vydavatel základní image virtuálního virtuálního her. |
| GalerieImageReferenceSku | Sku základní image virtuálního virtuálního mísa |
| GalerieImageReferenceOsType | Typ operačního systému základní bitové kopie virtuálního montovana |
| CustomImageId | Plně kvalifikované ID vlastní image základny virtuálního mě virtuálního mě. |

Sloupce dat obsažené v **souboru disks.csv** jsou uvedeny níže:

| Název sloupce | Popis | 
| ----------- | ----------- | 
| SubscriptionId | ID předplatného, které obsahuje testovací prostředí |
| LabUId | IDENTIFIKÁTOR GUID pro laboratoř |
| Název lab | Název laboratoře | 
| LabResourceId | Plně kvalifikované ID prostředků pro testovací prostředí | 
| ResourceGroupName | Název skupiny prostředků, která obsahuje testovací prostředí | 
| ResourceId | Plně kvalifikované ID prostředků pro virtuální ho svícen. |
| ResourceUId | Identifikátor GUID pro virtuální hod |
 |Name (Název) | Název připojeného disku |
| CreatedTime |Datum a čas vytvoření datového disku. |
| Odstraněné datum | Datum a čas, kdy byl datový disk odstraněn. |
| ResourceStatus | Stav zdroje. Aktivní, pokud prostředek existuje. Neaktivní, když je odstraněn. |
| Název diskblob | Název objektu blob pro datový disk. |
| Velikost disku GB | Velikost datového disku. |
| DiskType | Typ datového disku. 0 pro standard, 1 pro premium. |
| PronajatéByVmId | ID prostředku virtuálního počítače, ke kterému byl datový disk připojen. |


> [!NOTE]
> Pokud máte co do činění s více testovacích prostředí a chcete získat celkové informace, dva klíčové sloupce jsou **LabUID** a **ResourceUId**, které jsou jedinečné ID napříč odběry.

Exportovaná data lze manipulovat a vizualizovat pomocí nástrojů, jako je SQL Server, Power BI atd. Tato funkce je užitečná zejména v případě, že chcete ohlásit využití testovacího prostředí vašemu týmu pro správu, který nemusí používat stejné předplatné Azure jako vy.

## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Nastavení zásad pro testovací prostředí](devtest-lab-get-started-with-lab-policies.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
