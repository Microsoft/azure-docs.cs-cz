---
title: Vytvořte certifikát služby Azure Key Vault | Dokumentace Microsoftu
description: Vysvětluje postup při registraci virtuálního počítače z VHD nasazení Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 4223f9ec3bfaeacf7843508b13b5b5d81474311f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196698"
---
# <a name="create-certificates-for-azure-key-vault"></a>Vytvoření certifikátů pro Azure Key Vault

Tento článek vysvětluje, jak zřídit certifikáty podepsané svým držitelem, které jsou potřebné k navázání připojení vzdálené správy Windows (WinRM) k virtuálnímu počítači hostovaných v Azure (VM). Tento proces se skládá ze tří kroků:

1.  Vytvořte certifikát zabezpečení. 
2.  Vytvoření Azure Key Vault pro uložení tohoto certifikátu. 
3.  Store certifikáty, které se tento trezor klíčů. 

Nový nebo existující skupiny prostředků Azure můžete použít pro tuto práci.  Dřívější přístup se používá v následujících vysvětlení.


## <a name="create-the-certificate"></a>Vytvoření certifikátu

Upravit a spustit následující skript prostředí Azure Powershell k vytvoření souboru certifikátu (.pfx) v místní složce.  Budete muset nahradit hodnoty následujících parametrů:

|  **Parametr**        |   **Popis**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Místní složku pro uložení souboru PFX  |
| `$location`    | Jedno z Azure standardní zeměpisné umístění  |
| `$vmName`      | Název plánovaný virtuální počítač Azure   |
| `$certname`    | Název certifikátu; musí odpovídat názvu plně kvalifikované domény plánovaný virtuální počítač  |
| `$certpassword` | Heslo pro certifikáty, musí odpovídat heslo použité pro plánovaný virtuální počítač  |
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
> Zachovejte stejné relace prostředí PowerShell konzoly aktivní při provádění těchto kroků tak, aby hodnoty různých parametrů se zachovají.

> [!WARNING]
> Pokud uložíte tento skript, uložte pouze v zabezpečeném umístění vzhledem k tomu, že obsahuje informace o zabezpečení (heslo).


## <a name="create-the-key-vault"></a>Vytvoření služby key vault

Zkopírujte obsah [šablonu nasazení služby key vault](./cpp-key-vault-deploy-template.md) do souboru na místním počítači. (v příkladu níže uvedeného skriptu, tento prostředek je `C:\certLocation\keyvault.json`.)  Upravit a spustit následující skript prostředí Azure Powershell k vytvoření instance služby Azure Key Vault a v přidružené skupině prostředků.  Budete muset nahradit hodnoty následujících parametrů:

|  **Parametr**        |   **Popis**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Připojeno k nasazení identifikátory libovolného číselných řetězců                     |
| `$rgName`             | Název skupiny (RG) prostředků Azure k vytvoření                                        |
|  `$location`          | Jedno z Azure standardní zeměpisné umístění                                  |
| `$kvTemplateJson`     | Cesta k souboru (keyvault.json), který obsahuje šablonu Resource Manageru pro službu key vault |
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
        $myobjectId=(Get-AzureRmADUser -Mail $id)[0].Id
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
    New-AzureRmResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>Store certifikátu

Teď můžete uložit certifikáty, obsažené v souboru PFX do nové služby key vault spuštěním následujícího skriptu. 

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
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Další postup

Dále se [nasazení virtuálního počítače z uživatelské image virtuálního počítače](./cpp-deploy-vm-user-image.md).
