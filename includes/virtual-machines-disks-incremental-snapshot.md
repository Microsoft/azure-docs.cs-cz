---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a0325a7fd3aca3d27b24c193a9f131546a70d80b
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74566231"
---
Přírůstkové snímky (Preview) jsou zálohy v čase pro spravované disky, které se při pořízení skládají pouze ze všech změn od posledního snímku. Při pokusu o stažení nebo jiné použití přírůstkového snímku se použije úplný virtuální pevný disk. Tato nová funkce pro snímky spravovaných disků může potenciálně dovolit, aby bylo cenově výhodnější, protože už nebudete muset ukládat celý disk s každým snímkem, pokud se nerozhodnete. Stejně jako běžné snímky lze přírůstkové snímky použít k vytvoření úplného spravovaného disku, nebo k vytvoření normálního snímku.

Existuje několik rozdílů mezi přírůstkovým snímkem a běžným snímkem. Přírůstkové snímky budou vždy používat standardní úložiště HDD bez ohledu na typ úložiště disku, zatímco běžné snímky mohou používat Premium SSD. Pokud používáte běžné snímky na Premium Storage k horizontálnímu navýšení kapacity nasazení virtuálních počítačů, doporučujeme používat vlastní image ve službě Storage úrovně Standard v [galerii sdílených imagí](../articles/virtual-machines/linux/shared-image-galleries.md). Pomůže vám dosáhnout větší obrovské škály s nižšími náklady. Navíc přírůstkové snímky potenciálně nabízejí lepší spolehlivost pomocí [redundantního úložiště](../articles/storage/common/storage-redundancy-zrs.md) (ZRS) zóny. Pokud je ve vybrané oblasti k dispozici ZRS, přírůstkový snímek bude automaticky používat ZRS. Pokud ZRS není v této oblasti k dispozici, bude mít tento snímek výchozí [místně redundantní úložiště](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Toto chování můžete přepsat a vybrat ho ručně, ale nedoporučujeme to.

Přírůstkové snímky také nabízí rozdílovou možnost, která je jednoznačně dostupná pro spravované disky. Umožňují získat změny dvou přírůstkových snímků stejných spravovaných disků na úrovni bloku. Tuto možnost můžete použít ke snížení objemu dat při kopírování snímků napříč oblastmi.

Pokud jste se ještě nezaregistrovali ve verzi Preview a chcete začít používat přírůstkové snímky, pošlete nám e-mail na AzureDisks@microsoft.com, abyste získali přístup k verzi Public Preview.

## <a name="restrictions"></a>Omezení

- Přírůstkové snímky jsou v tuto chvíli dostupné jenom v Východní USA, Střed USA, Kanadě Central, Středozápadní USA a Severní Evropa.
- Přírůstkové snímky momentálně nelze vytvořit poté, co jste změnili velikost disku.
- Přírůstkové snímky se aktuálně nedají přesunout mezi předplatnými.
- V současné době můžete v daném okamžiku generovat pouze identifikátory URI SAS až na pět snímků konkrétní rodiny snímků.
- Pro určitý disk nemůžete vytvořit přírůstkový snímek mimo předplatné tohoto disku.
- Až sedm přírůstkových snímků na disk lze vytvořit každých pět minut.
- Pro jeden disk lze vytvořit celkem 200 přírůstkových snímků.

## <a name="powershell"></a>PowerShell

K vytvoření přírůstkového snímku můžete použít Azure PowerShell. Budete potřebovat nejnovější verzi Azure PowerShell, následující příkaz si buď nainstaluje nebo aktualizuje stávající instalaci na nejnovější:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Po nainstalování se přihlaste k relaci PowerShellu pomocí `az login`.

Chcete-li vytvořit přírůstkový snímek s Azure PowerShell, nastavte konfiguraci pomocí parametru [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) s parametrem `-Incremental` a pak jej předejte jako proměnnou do [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) prostřednictvím parametru `-Snapshot`.

Nahraďte `<yourDiskNameHere>`, `<yourResourceGroupNameHere>`a `<yourDesiredSnapShotNameHere>` hodnotami a pak můžete použít následující skript k vytvoření přírůstkového snímku:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Můžete identifikovat přírůstkové snímky ze stejného disku s `SourceResourceId` a `SourceUniqueId` vlastností snímků. `SourceResourceId` je ID prostředku Azure Resource Manager nadřazeného disku. `SourceUniqueId` je hodnota zděděná z vlastnosti `UniqueId` disku. Pokud byste chtěli odstranit disk a pak vytvořit nový disk se stejným názvem, změní se hodnota vlastnosti `UniqueId`.

Pomocí `SourceResourceId` a `SourceUniqueId` můžete vytvořit seznam všech snímků přidružených k určitému disku. Nahraďte `<yourResourceGroupNameHere>` hodnotou a pak můžete použít následující příklad k vypsání stávajících přírůstkových snímků:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="cli"></a>Rozhraní příkazového řádku

Můžete vytvořit přírůstkový snímek pomocí Azure CLI, budete potřebovat nejnovější verzi rozhraní příkazového řádku Azure CLI. 

V systému Windows následující příkaz buď nainstaluje nebo aktualizuje stávající instalaci na nejnovější verzi:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
V systému Linux se instalace rozhraní příkazového řádku liší v závislosti na verzi operačního systému.  Informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) pro konkrétní verzi systému Linux.

K vytvoření přírůstkového snímku použijte příkaz [AZ Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) s parametrem `--incremental`.

Následující příklad vytvoří přírůstkový snímek, nahraďte `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`,`<exampleDiskName>`a `<exampleLocation>` vlastními hodnotami a pak spusťte příklad:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Můžete identifikovat přírůstkové snímky ze stejného disku s `SourceResourceId` a `SourceUniqueId` vlastností snímků. `SourceResourceId` je ID prostředku Azure Resource Manager nadřazeného disku. `SourceUniqueId` je hodnota zděděná z vlastnosti `UniqueId` disku. Pokud byste chtěli odstranit disk a pak vytvořit nový disk se stejným názvem, změní se hodnota vlastnosti `UniqueId`.

Pomocí `SourceResourceId` a `SourceUniqueId` můžete vytvořit seznam všech snímků přidružených k určitému disku. Následující příklad zobrazí seznam všech přírůstkových snímků přidružených k určitému disku, ale vyžaduje instalaci.

V tomto příkladu se pro dotazování na data používá JQ. Chcete-li spustit příklad, musíte [nainstalovat JQ](https://stedolan.github.io/jq/download/).

Nahraďte `<yourResourceGroupNameHere>` a `<exampleDiskName>` hodnotami a pak můžete použít následující příklad k vypsání stávajících přírůstkových snímků, pokud jste také nainstalovali JQ:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
```

## <a name="resource-manager-template"></a>Šablona Resource Manageru

Můžete také použít šablony Azure Resource Manager k vytvoření přírůstkového snímku. Musíte se ujistit, že je apiVersion nastavené na **2019-03-01** a že vlastnost Increment je nastavená taky na true. Následující fragment kódu je příkladem vytvoření přírůstkového snímku pomocí šablon Správce prostředků:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Další kroky

1. Pokud jste se ještě nezaregistrovali ve verzi Preview a chcete začít používat přírůstkové snímky, pošlete nám e-mail na AzureDisks@microsoft.com, abyste získali přístup k verzi Public Preview. 

2. Prozkoumejte následující ukázky pro kopírování přírůstkových snímků v různých oblastech pomocí rozdílové možnosti   

    - [Používání sad Azure .NET SDK](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)
