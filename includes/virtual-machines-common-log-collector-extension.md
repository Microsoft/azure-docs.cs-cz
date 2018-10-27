
Diagnostika potíží s cloudovou službou Microsoft Azure vyžaduje shromažďování souborů protokolu služby na virtuálních počítačích problémy vedly. AzureLogCollector rozšíření na vyžádání můžete provádět jednorázové shromažďování protokolů z jednoho nebo více cloudové služby virtuálních počítačů (z webové role a role pracovního procesu) a jeho přenosu shromážděných souborů do účtu služby Azure storage – vše bez vzdálené přihlášení k libovolné virtuálních počítačů.

> [!NOTE]
> Popis pro většinu zaznamenané informace lze nalézt v http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp.
> 
> 

Existují dva režimy kolekce závisí na typy souborů, které se mají shromažďovat.

* **Azure hostovaného agenta protokoly pouze (GA)**. Tento režim kolekce obsahuje všechny protokoly týkající se agentů hosta Azure a dalšími komponentami Azure.
* **Všechny protokoly (úplné)**. Tento režim kolekce shromáždí všechny soubory v režimu GA plus:
  
  * protokoly událostí systému a aplikací
  * Protokoly chyb HTTP
  * Protokoly IIS
  * Instalační protokoly
  * Další systémové protokoly

V obou režimech kolekce se dá nastavit složky shromažďování dalších dat pomocí kolekce následující strukturu:

* **Název**: název kolekce, použít jako název podsložky v souboru zip s shromážděných souborů.
* **Umístění**: cesta ke složce ve virtuálním počítači, kde jsou uložené soubory, které se mají shromažďovat.
* **SearchPattern**: vzor názvů souborů, které se mají shromažďovat. Výchozí hodnota je "\*"
* **Rekurzivní**: Pokud jsou soubory se mají shromažďovat rekurzivně nachází v rámci zadané umístění.

## <a name="prerequisites"></a>Požadavky
* Máte účet úložiště pro rozšíření, které chcete ukládat soubory generované zip.
* Pomocí rutiny Powershellu pro Azure v0.8.0 nebo vyšší. Další informace najdete v tématu [soubory ke stažení Azure](https://azure.microsoft.com/downloads/).

## <a name="add-the-extension"></a>Přidání rozšíření
Můžete použít [prostředí Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) rutiny nebo [rozhraní REST API správy služby](https://msdn.microsoft.com/library/ee460799.aspx) přidat AzureLogCollector rozšíření.

Pro cloudové služby, existující rutiny Azure Powershellu **Set-AzureServiceExtension**, slouží k povolení rozšíření na instance rolí cloudové služby. Pokaždé, když se toto rozšíření je povolená díky této rutiny, se aktivuje shromažďování protokolů na vybranou roli výskyty vybraných rolí.

Pro virtuální počítače, existující rutiny Azure Powershellu **Set-AzureVMExtension**, slouží k povolení rozšíření na virtuálních počítačích. Pokaždé, když se toto rozšíření je povolená díky rutiny, se aktivuje shromažďování protokolů pro každou instanci.

Toto rozšíření interně používá PublicConfiguration založenými na JSON a PrivateConfiguration. Následuje rozložení ukázkovému kódu JSON pro veřejné a privátní konfigurace.

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
> Toto rozšíření nevyžaduje **privateConfiguration**. Můžete jednoduše zadat prázdnou strukturou pro **– PrivateConfiguration** argument.
> 
> 

Postupujte podle jednoho ze dvou následujících kroků přidáte AzureLogCollector na jeden nebo víc instancí cloudové služby nebo virtuální počítač z vybrané role, který aktivuje kolekce na každý virtuální počítač ke spuštění a odeslání shromážděných souborů na zadaný účet Azure.

## <a name="adding-as-a-service-extension"></a>Přidání jako rozšíření služby
1. Postupujte podle pokynů pro připojení k vašemu předplatnému Azure Powershellu.
2. Zadejte název, slot, role a role instancí služby do kterých chcete přidat a povolit rozšíření AzureLogCollector.

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

3. Zadejte složku další data, pro kterou se budou shromažďovat soubory (Tento krok je volitelný).

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
   > Můžete použít token `%roleroot%` k určení kořenové jednotce role, protože nepoužívá pevný disk.
   > 
   > 
4. Zadejte název účtu služby Azure storage a klíč, na který se nahraje shromážděných souborů.

  ```powershell
  $StorageAccountName = 'YourStorageAccountName'
  $StorageAccountKey  = 'YourStorageAccountKey'
  ```

5. Následujícím způsobem volání SetAzureServiceLogCollector.ps1 (zahrnuty na konci článku) za účelem povolení rozšíření AzureLogCollector pro Cloudovou službu. Po dokončení provádění najdete nahraný soubor v části `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

  ```powershell
  .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
  ```

Níže je definice parametry předané do skriptu. (To je zkopírován pod stejně.)

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
* **Role**: seznam rolí, jako je například "WebRole1" nebo "WorkerRole1".
* **Instance**: seznam názvů instancí role, které jsou oddělené čárkou – použít zástupný znak řetězec ("*") pro všechny instance rolí.
* **Slot**: název slotu. "Produkční" nebo "Pracovní".
* **Režim**: režim kolekce. "Úplné" nebo "GA".
* **StorageAccountName**: Azure název účtu úložiště pro ukládání shromážděných dat.
* **StorageAccountKey**: název Azure klíč účtu úložiště.
* **AdditionalDataLocationList**: seznam následující strukturu:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Přidání jako rozšíření virtuálního počítače
Postupujte podle pokynů pro připojení k vašemu předplatnému Azure Powershellu.

1. Zadejte název služby, virtuální počítač a režim kolekce.

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
  
2. Zadejte název účtu služby Azure storage a klíč, na který se nahraje shromážděných souborů.

  ```powershell
  $StorageAccountName = 'YourStorageAccountName'
  $StorageAccountKey  = 'YourStorageAccountKey'
  ```

3. Následujícím způsobem volání SetAzureVMLogCollector.ps1 (zahrnuty na konci článku) za účelem povolení rozšíření AzureLogCollector pro Cloudovou službu. Po dokončení provádění najdete nahraný soubor v části `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

Níže je definice parametry předané do skriptu. (To je zkopírován pod stejně.)

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
* **Režim**: režim kolekce. "Úplné" nebo "GA".
* **StorageAccountName**: Azure název účtu úložiště pro ukládání shromážděných dat.
* **StorageAccountKey**: název Azure klíč účtu úložiště.
* **AdditionalDataLocationList**: seznam následující strukturu:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Soubory rozšíření skriptů prostředí PowerShell
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

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

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
Nyní můžete prozkoumat nebo vaše protokoly kopírovat z jednoho jednoduchého umístění.

