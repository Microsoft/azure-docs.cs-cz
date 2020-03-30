---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09c4420647043fccc408631fec75854667923721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085231"
---
Diagnostika problémů s cloudovou službou Microsoft Azure vyžaduje shromažďování souborů protokolu služby na virtuálních počítačích, jakmile nastanou problémy. Rozšíření AzureLogCollector na vyžádání můžete použít k provádění jednorázové kolekce protokolů z jednoho nebo více virtuálních účtů cloudové služby (z webových rolí i rolí pracovních procesů) a přenosu shromážděných souborů do účtu úložiště Azure – to vše bez vzdáleného přihlášení k libovolnému virtuálních měn.

> [!NOTE]
> Popisy většiny přihlášených informací naleznete na adresehttps://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/
> 
> 

Existují dva režimy kolekce v závislosti na typech souborů, které mají být shromažďovány.

* **Azure Host Agent protokoly pouze (GA)**. Tento režim kolekce zahrnuje všechny protokoly související s agenty hosta Azure a další součásti Azure.
* **Všechny protokoly (úplné)**. Tento režim kolekce shromažďuje všechny soubory v režimu GA plus:
  
  * protokoly událostí systému a aplikace
  * Protokoly chyb protokolu HTTP
  * Protokoly IIS
  * Instalační protokoly
  * jiné systémové protokoly

V obou režimech kolekce další složky kolekce dat lze zadat pomocí kolekce následující struktury:

* **Název**: Název kolekce, který se používá jako název podsložky uvnitř souboru zip se shromážděnými soubory.
* **Umístění**: Cesta ke složce ve virtuálním počítači, kde jsou umístěny soubory, které mají být shromažďovány.
* **SearchPattern**: Vzor názvy souborů, které mají být shromažďovány. Výchozí hodnota\*je " "
* **Rekurzivní**: pokud jsou soubory, které mají být shromažďovány, umístěny rekurzivně pod zadaným umístěním.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Mít účet úložiště pro rozšíření pro uložení generovaných souborů ZIP.
* Azure Powershell Pokyny [k instalaci najdete v tématu Instalace Azure PowerShellu](/powershell/azure/install-az-ps)] .

## <a name="add-the-extension"></a>Přidání rozšíření
K přidání rozšíření AzureLogCollector můžete použít rutiny [prostředí Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) nebo [rozhraní REST pro správu služeb.](https://msdn.microsoft.com/library/ee460799.aspx)

Pro cloudové služby lze existující rutinu Azure Powershell, **Set-AzureServiceExtension**, použít k povolení rozšíření v instancích role cloudové služby. Pokaždé, když je toto rozšíření povoleno prostřednictvím této rutiny, kolekce protokolů se aktivuje na vybrané instance rolí vybraných rolí.

Pro virtuální počítače existující rutina Azure Powershell, **Set-AzureVMExtension**, lze použít k povolení rozšíření na virtuálních počítačích. Pokaždé, když je toto rozšíření povoleno prostřednictvím rutin, kolekce protokolu se aktivuje v každé instanci.

Interně toto rozšíření používá PublicConfiguration založené na JSON a PrivateConfiguration. Následuje rozložení ukázkové jednotky JSON pro veřejnou a soukromou konfiguraci.

### <a name="publicconfiguration"></a>Veřejná konfigurace

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>Privátní konfigurace

```json
{

}
```

> [!NOTE]
> Toto rozšíření nepotřebuje **privateConfiguration**. Můžete pouze poskytnout prázdnou strukturu pro argument **-PrivateConfiguration.**
> 
> 

Můžete postupovat podle jednoho ze dvou následujících kroků k přidání AzureLogCollector do jedné nebo více instancí cloudové služby nebo virtuálního počítače vybraných rolí, který aktivuje kolekce na každém virtuálním počítači ke spuštění a odeslání shromážděných souborů na účet Azure zadané.

## <a name="adding-as-a-service-extension"></a>Přidání jako rozšíření služby
1. Podle pokynů připojte Azure PowerShell k předplatnému.
2. Zadejte název služby, patici, role a instance rolí, do kterých chcete přidat a povolit rozšíření AzureLogCollector.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'extensiontest2'

   #Specify the slot. 'Production' or 'Staging'
   $slot = 'Production'

   #Specified the roles on which the extension will be installed and enabled
   $roles = @("WorkerRole1","WebRole1")

   #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
   $instances = @("*")

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"
   ```

3. Zadejte další složku dat, pro kterou budou soubory shromažďovány (tento krok je volitelný).

   ```powershell
   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
   #more locations can be added....
   ```

   > [!NOTE]
   > Token `%roleroot%` můžete použít k určení kořenové jednotky role, protože nepoužívá pevnou jednotku.
   > 
   > 
4. Zadejte název účtu úložiště Azure a klíč, do kterého se budou nahrávat shromážděné soubory.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Volání SetAzureServiceLogCollector.ps1 (zahrnuty na konci článku) takto povolit rozšíření AzureLogCollector pro cloudovou službu. Po dokončení provádění najdete nahraný soubor pod`https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

Následuje definice parametrů předaných skriptu. (To je zkopírován níže také.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**: Název cloudové služby.
* **Role**: Seznam rolí, například "WebRole1" nebo "WorkerRole1".
* **Instance**: Seznam názvů instancí rolí oddělených čárkou -- použijte zástupný řetězec ("*") pro všechny instance role.
* **Slot**: Název slotu. "Výroba" nebo "Inscenace".
* **Režim**: Režim sběru. "Úplné" nebo "GA".
* **StorageAccountName**: Název účtu úložiště Azure pro ukládání shromážděných dat.
* **StorageAccountKey**: Název klíče účtu úložiště Azure.
* **AdditionalDataLocationList**: Seznam následující struktury:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Přidání jako rozšíření virtuálního virtuálního mě
Podle pokynů připojte Azure PowerShell k předplatnému.

1. Zadejte název služby, virtuální ho dispozičního systému a režim kolekce.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'YourCloudServiceName'

   #Specify the VM name
   $VMName = "'YourVMName'"

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"

   Specify the additional data folder for which files will be collected (this step is optional).

   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
        #more locations can be added....
   ```
  
2. Zadejte název účtu úložiště Azure a klíč, do kterého se budou nahrávat shromážděné soubory.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Volání SetAzureVMLogCollector.ps1 (zahrnuty na konci článku) takto povolit rozšíření AzureLogCollector pro cloudovou službu. Po dokončení provádění najdete nahraný soubor pod`https://YourStorageAccountName.blob.core.windows.net/vmlogs`

Následuje definice parametrů předaných skriptu. (To je zkopírován níže také.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**: Název cloudové služby.
* **VMName**: Název virtuálního soudu.
* **Režim**: Režim sběru. "Úplné" nebo "GA".
* **StorageAccountName**: Název účtu úložiště Azure pro ukládání shromážděných dat.
* **StorageAccountKey**: Název klíče účtu úložiště Azure.
* **AdditionalDataLocationList**: Seznam následující struktury:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Soubory skriptu prostředí PowerShell
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>Soubor SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>Další kroky
Nyní můžete zkontrolovat nebo zkopírovat protokoly z jednoho jednoduchého umístění.

