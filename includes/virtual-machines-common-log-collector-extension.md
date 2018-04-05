
Diagnostika problémů s cloudovou službou Microsoft Azure vyžaduje shromažďování souborů protokolů služby u virtuálních počítačů jako dojít k problémům. AzureLogCollector rozšíření na vyžádání můžete použít k provedení jednorázové shromažďování protokolů z jedné nebo více cloudové služby virtuálních počítačů (z webové role i role pracovního procesu) a přenosu shromážděných souborů na účet úložiště Azure – všechny bez vzdálené přihlášení k jakémukoli virtuálních počítačů.

> [!NOTE]
> Popisy pro většinu zaznamenané informace naleznete na adrese http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.
> 
> 

Existují dva režimy kolekce závisí na typy souborů, které se mají shromažďovat.

* **Azure hostovaného agenta protokoly pouze (GA)**. Tento režim kolekce zahrnuje všechny protokoly související s agenty Azure hostů a další součásti Azure.
* **Všechny protokoly (úplná záloha)**. Tento režim kolekce shromáždí všechny soubory v režimu GA plus:
  
  * protokoly událostí systému a aplikací
  * Protokoly chyb HTTP
  * Protokoly služby IIS
  * Protokoly instalace
  * Další systémové protokoly

V obou režimech kolekce složky kolekcí další data lze pomocí kolekce následující strukturou:

* **Název**: název kolekce, použít jako název podsložky v souboru zip s shromážděných souborů.
* **Umístění**: cesta ke složce na virtuálním počítači, kde jsou umístěny soubory, které se mají shromažďovat.
* **SearchPattern**: vzor názvů souborů, které se mají shromažďovat. Výchozí hodnota je "\*"
* **Rekurzivní**: Pokud jsou soubory, které se mají shromažďovat rekurzivně nachází v umístění zadané.

## <a name="prerequisites"></a>Požadavky
* Máte účet úložiště pro rozšíření, které chcete ukládat soubory generované zip.
* Použijte rutiny Azure PowerShell v0.8.0 nebo vyšší. Další informace najdete v tématu [Azure stáhne](https://azure.microsoft.com/downloads/).

## <a name="add-the-extension"></a>Přidání rozšíření
Můžete použít [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) rutiny nebo [rozhraní API REST pro správu služby](https://msdn.microsoft.com/library/ee460799.aspx) přidat AzureLogCollector rozšíření.

Pro cloudové služby, existující rutiny Azure Powershellu **Set-AzureServiceExtension**, slouží k povolení rozšíření u instancí role cloudové služby. Pokaždé, když je toto rozšíření povolené prostřednictvím této rutiny, aktivuje se v instancích vybranou roli v systému vybrané role shromáždění protokolů.

Pro virtuální počítače, existující rutiny Azure Powershellu **Set-AzureVMExtension**, slouží k povolení rozšíření na virtuálních počítačích. Pokaždé, když je toto rozšíření povolené prostřednictvím rutin, aktivuje se na každou instanci shromáždění protokolů.

Toto rozšíření interně používá na základě JSON PublicConfiguration a PrivateConfiguration. Toto je rozložení ukázku JSON pro veřejné a privátní konfigurace.

### <a name="publicconfiguration"></a>PublicConfiguration

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

### <a name="privateconfiguration"></a>PrivateConfiguration

```json
{

}
```

> [!NOTE]
> Toto rozšíření nevyžaduje **privateConfiguration**. Zadáte na prázdnou strukturou pro **– PrivateConfiguration** argument.
> 
> 

Můžete provést jednu z dvě následující kroky k přidání AzureLogCollector na jeden nebo více instancí cloudové služby nebo virtuálního počítače vybrané role, která aktivuje kolekce na každý virtuální počítač ke spuštění a odeslání shromážděné soubory pro zadaný účet Azure.

## <a name="adding-as-a-service-extension"></a>Přidání jako rozšíření služby
1. Postupujte podle pokynů k připojení k vašemu předplatnému Azure PowerShell.
2. Zadejte název, slotu, role a role instance služby pro které chcete přidat a povolit AzureLogCollector rozšíření.

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

3. Zadejte složku další data, pro které se budou shromažďovat soubory (Tento krok je volitelný).

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
   > Můžete použít token `%roleroot%` chcete určit kořenovou jednotku role, protože nepoužívá pevnou jednotku.
   > 
   > 
4. Zadejte název účtu úložiště Azure a klíč, ke kterému se nahraje shromážděných souborů.

  ```powershell
  $StorageAccountName = 'YourStorageAccountName'
  $StorageAccountKey  = 'YourStorageAccountKey'
  ```

5. Volání SetAzureServiceLogCollector.ps1 (zahrnutá na konci tohoto článku) následujícím způsobem povolit rozšíření AzureLogCollector pro cloudové služby. Po dokončení provádění můžete najít nahrávaný soubor v části `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

  ```powershell
  .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
  ```

Zde je definice parametry předané do skriptu. (To je zkopírovat níže také.)

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

* **ServiceName**: název vaší cloudové služby.
* **Role**: seznam rolí, například "WebRole1" nebo "WorkerRole1".
* **Instance**: seznam názvů instancí role oddělené čárkou – použít zástupný znak řetězec ("*") pro všechny instance role.
* **Slot**: název slotu. "Výroba" nebo "Přípravy".
* **Režim**: režim kolekce. "Úplná" nebo "GA".
* **StorageAccountName**: název Azure účet úložiště pro ukládání shromážděných dat.
* **StorageAccountKey**: název Azure klíč účtu úložiště.
* **AdditionalDataLocationList**: seznam následující strukturou:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Přidání jako rozšíření virtuálního počítače
Postupujte podle pokynů k připojení k vašemu předplatnému Azure PowerShell.

1. Zadejte název služby, virtuálních počítačů a režim kolekce.

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
  
2. Zadejte název účtu úložiště Azure a klíč, ke kterému se nahraje shromážděných souborů.

  ```powershell
  $StorageAccountName = 'YourStorageAccountName'
  $StorageAccountKey  = 'YourStorageAccountKey'
  ```

3. Volání SetAzureVMLogCollector.ps1 (zahrnutá na konci tohoto článku) následujícím způsobem povolit rozšíření AzureLogCollector pro cloudové služby. Po dokončení provádění můžete najít nahrávaný soubor v části `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

Zde je definice parametry předané do skriptu. (To je zkopírovat níže také.)

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

* **ServiceName**: název vaší cloudové služby.
* **VMName**: název virtuálního počítače.
* **Režim**: režim kolekce. "Úplná" nebo "GA".
* **StorageAccountName**: název Azure účet úložiště pro ukládání shromážděných dat.
* **StorageAccountKey**: název Azure klíč účtu úložiště.
* **AdditionalDataLocationList**: seznam následující strukturou:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Soubory skriptu prostředí PowerShell vytvořit vícejazyčný
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

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be seperated by ,
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
$context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
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

#we just provide a empty privateConfig object
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
#This is an optional step: generate a sasUri to the container so it can be shared with other people if nened
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

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
$context = New-AzureStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzureStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzureStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzureStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
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

Write-Output "PublicConfigurtion is: \r\n$publicConfigJSON"

#
#we just provide a empty privateConfig object
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
                          $ReadSasUri = New-AzureStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

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
Nyní můžete prozkoumat nebo kopírování protokolů z jednoho umístění jednoduché.

