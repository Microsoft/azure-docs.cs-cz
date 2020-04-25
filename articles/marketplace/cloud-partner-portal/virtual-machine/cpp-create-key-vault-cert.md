---
title: Vytvoření certifikátu Azure Key Vault | Azure Marketplace
description: Vysvětluje, jak zaregistrovat virtuální počítač z virtuálního pevného disku nasazeného v Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9981f8eda174bbe04b54933528d20d270d360824
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82148256"
---
# <a name="create-certificates-for-azure-key-vault"></a>Vytvořit certifikáty pro Azure Key Vault

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek virtuálních počítačů Azure do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Pokud chcete spravovat migrované nabídky, postupujte podle pokynů v tématu [certifikace imagí virtuálních počítačů Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) .

Tento článek vysvětluje, jak zřídit certifikáty podepsané svým držitelem, které jsou potřeba k navázání připojení Vzdálená správa systému Windows (WinRM) k virtuálnímu počítači hostovanému v Azure (VM). Tento proces se skládá ze tří kroků:

1.    Vytvořte certifikát zabezpečení. 
2.    Vytvořte Azure Key Vault pro uložení tohoto certifikátu. 
3.    Uložte certifikáty do tohoto trezoru klíčů. 

Pro tuto práci můžete použít buď novou, nebo existující skupinu prostředků Azure.  Předchozí přístup se používá v následujícím vysvětlení.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Vytvoření certifikátu

Úpravou a spuštěním následujícího skriptu Azure PowerShellu vytvořte soubor certifikátu (. pfx) v místní složce.  Je potřeba nahradit hodnoty pro následující parametry:

|  **Ukazatele**        |   **Popis**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Místní složka, do které se uloží soubor. pfx  |
| `$location`    | Jedno ze standardních zeměpisných míst Azure  |
| `$vmName`      | Název plánovaného virtuálního počítače Azure   |
| `$certname`    | Název certifikátu; musí odpovídat plně kvalifikovanému názvu domény plánovaného virtuálního počítače.  |
| `$certpassword` | Heslo pro certifikáty se musí shodovat s heslem použitým pro plánovaný virtuální počítač.  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> Během těchto kroků nechejte aktivní stejnou relaci konzoly PowerShell, aby se zachovaly hodnoty různých parametrů.

> [!WARNING]
> Pokud tento skript uložíte, uložte ho pouze do zabezpečeného umístění, protože obsahuje informace o zabezpečení (heslo).


## <a name="create-the-key-vault"></a>Vytvoření trezoru klíčů

Zkopírujte obsah [šablony nasazení trezoru klíčů](./cpp-key-vault-deploy-template.md) do souboru na místním počítači. (v následujícím příkladu skriptu je `C:\certLocation\keyvault.json`tento prostředek.)  Pokud chcete vytvořit instanci Azure Key Vault a přidruženou skupinu prostředků, upravte a spusťte následující skript Azure PowerShellu.  Je potřeba nahradit hodnoty pro následující parametry:

|  **Ukazatele**        |   **Popis**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Libovolný číselný řetězec připojený k identifikátorům nasazení                     |
| `$rgName`             | Název skupiny prostředků Azure (RG), která se má vytvořit                                        |
|  `$location`          | Jedno ze standardních zeměpisných míst Azure                                  |
| `$kvTemplateJson`     | Cesta souboru (Trezor klíčů. JSON), který obsahuje šablonu Správce prostředků pro Trezor klíčů |
| `$kvname`             | Název nového trezoru klíčů                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>Uložení certifikátu

Certifikáty, které jsou obsaženy v souboru. pfx, teď můžete ukládat do nového trezoru klíčů spuštěním následujícího skriptu. 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Další kroky

Dále [nasadíte virtuální počítač z uživatelské image virtuálního](./cpp-deploy-vm-user-image.md)počítače.
