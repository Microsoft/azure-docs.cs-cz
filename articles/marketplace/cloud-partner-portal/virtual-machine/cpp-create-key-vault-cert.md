---
title: Vytvoření certifikátu trezoru klíčů Azure | Azure Marketplace
description: Vysvětluje, jak zaregistrovat virtuální počítač z virtuálního pevného disku nasazeného v Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 09e82b9905104df9b1902b0f64f6cfdf812aabb8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274000"
---
# <a name="create-certificates-for-azure-key-vault"></a>Vytvoření certifikátů pro Azure Key Vault

> [!IMPORTANT]
> dubna 2020 začneme přesouvat správu nabídek virtuálního počítače Azure do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Podle pokynů v [certifikaci image virtuálního počítače Azure](https://aks.ms/CertifyVMimage) spravujte migrované nabídky.

Tento článek vysvětluje, jak zřídit certifikáty podepsané svým držitelem potřebné k vytvoření připojení Windows Remote Management (WinRM) k virtuálnímu počítači (VM) hostovanému v Azure. Tento proces se skládá ze tří kroků:

1.    Vytvořte certifikát zabezpečení. 
2.    Vytvořte trezor klíčů Azure pro uložení tohoto certifikátu. 
3.    Uložte certifikáty do tohoto trezoru klíčů. 

Pro tuto práci můžete použít novou nebo existující skupinu prostředků Azure.  První přístup se používá v následujícím vysvětlení.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Vytvoření certifikátu

Chcete-li vytvořit soubor certifikátu (.pfx) v místní složce, upravte a spusťte následující skript Azure Powershellu.  Budete muset nahradit hodnoty pro následující parametry:

|  **Parametr**        |   **Popis**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Místní složka pro uložení souboru .pfx  |
| `$location`    | Jedna ze standardních geografických lokalit Azure  |
| `$vmName`      | Název plánovaného virtuálního počítače Azure   |
| `$certname`    | Název osvědčení; musí odpovídat plně kvalifikovanému názvu domény plánovaného virtuálního mísu.  |
| `$certpassword` | Heslo pro certifikáty, musí odpovídat heslo použité pro plánovaný virtuální mk.  |
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
> Ponechte stejnou relaci konzoly prostředí PowerShell aktivní během těchto kroků, aby byly zachovány hodnoty různých parametrů.

> [!WARNING]
> Pokud uložíte tento skript, uložte jej pouze na bezpečném místě, protože obsahuje informace o zabezpečení (heslo).


## <a name="create-the-key-vault"></a>Vytvoření trezoru klíčů

Zkopírujte obsah [šablony nasazení trezoru klíčů](./cpp-key-vault-deploy-template.md) do souboru v místním počítači. (v ukázkovém skriptu `C:\certLocation\keyvault.json`níže je tento zdroj .)  Upravte a spusťte následující skript Azure Powershell u vytvořit instanci Azure Key Vault a přidruženou skupinu prostředků.  Budete muset nahradit hodnoty pro následující parametry:

|  **Parametr**        |   **Popis**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Libovolný číselný řetězec připojený k identifikátorům nasazení                     |
| `$rgName`             | Název skupiny prostředků Azure (RG) k vytvoření                                        |
|  `$location`          | Jedna ze standardních geografických lokalit Azure                                  |
| `$kvTemplateJson`     | Cesta k souboru (keyvault.json) obsahující šablonu Správce prostředků pro trezor klíčů |
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

Nyní můžete uložit certifikáty obsažené v souboru .pfx do nového trezoru klíčů spuštěním následujícího skriptu. 

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

Dále [nasadíte virtuální počítač z image virtuálního počítače uživatele](./cpp-deploy-vm-user-image.md).
