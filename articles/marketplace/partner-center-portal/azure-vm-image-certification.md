---
title: Certifikace virtuálních strojů Azure – Azure Marketplace
description: Přečtěte si, jak otestovat a odeslat nabídku virtuálních strojů na komerčním trhu.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 9bd7e40855f30612b90cf28365c0b1410cd3e3d8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731131"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Certifikace image virtuálního počítače (VM) Azure

> [!NOTE]
> Přesouváme správu nabídek virtuálních virtuálních zařízení Azure z portálu partnerů cloudu do Centra partnerů. Dokud se vaše nabídky nebudou migrovat, pokračujte podle pokynů v tématu [Vytvoření certifikátů pro Azure Key Vault](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert) na portálu cloudových partnerů a spravujte své nabídky.

Tento článek popisuje, jak otestovat a odeslat image virtuálního počítače (VM) na komerčním trhu, aby bylo zajištěno, že splňuje nejnovější požadavky na publikování na Azure Marketplace.

Před odesláním nabídky virtuálního počítače proveďte tyto kroky:

1. Vytvářejte a nasazujte certifikáty.
2. Nasaďte virtuální počítač Azure pomocí zobecněné image.
3. Spusťte ověření.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Vytváření a nasazování certifikátů pro Azure Key Vault

Tato část popisuje, jak vytvořit a nasadit certifikáty podepsané svým držitelem, které jsou nutné k nastavení připojení Windows Remote Management (WinRM) k virtuálnímu počítači hostovanému v Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Vytvoření certifikátů pro Azure Key Vault

Tento proces se skládá ze tří kroků:

1. Vytvořte certifikát zabezpečení.
2. Vytvořte trezor klíčů Azure pro uložení certifikátu.
3. Uložte certifikáty do trezoru klíčů.

Pro tuto práci můžete použít novou nebo existující skupinu prostředků Azure.

#### <a name="create-the-security-certificate"></a>Vytvoření certifikátu zabezpečení

Chcete-li vytvořit soubor certifikátu (.pfx) v místní složce, upravte a spusťte následující skript Azure PowerShellu. Nahraďte hodnoty parametrů uvedených v následující tabulce.

| **Parametr** | **Popis** |
| --- | --- |
| $certroopath | Místní složka pro uložení souboru .pfx. |
| $location | Jedna ze standardních geografických umístění Azure. |
| $vmName | Název plánovaného virtuálního počítače Azure. |
| $certname | Název osvědčení; musí odpovídat plně kvalifikovaný název domény plánovaného virtuálního soudu. |
| $certpassword | Heslo pro certifikáty, musí odpovídat heslo použité pro plánovaný virtuální počítače. |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
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
> Zachovat stejnou relaci konzoly Azure PowerShell otevřené a spuštěné během těchto kroků zachovat hodnoty různých parametrů.

> [!WARNING]
> Pokud uložíte tento skript, uložte jej pouze na bezpečném místě, protože obsahuje informace o zabezpečení (heslo).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Vytvoření trezoru klíčů Azure pro uložení certifikátu

Zkopírujte obsah šablony níže do souboru v místním počítači. V ukázkovém skriptu `C:\certLocation\keyvault.json`níže je tento prostředek ).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

Upravte a spusťte následující skript Azure PowerShell u vytvořit Azure Key Vault a přidružené skupiny prostředků. Nahrazení hodnot parametrů uvedených v následující tabulce

| **Parametr** | **Popis** |
| --- | --- |
| $postfix | Náhodný číselný řetězec připojený k identifikátorům nasazení. |
| $rgName | Název skupiny prostředků Azure (RG), který chcete vytvořit. |
| $location | Jedna ze standardních geografických umístění Azure. |
| $kvTemplateJson | Cesta k souboru (keyvault.json) obsahující šablonu Správce prostředků pro trezor klíčů. |
| $kvname | Název nového trezoru klíčů.|
|   |   |

```PowerShell
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

#### <a name="store-the-certificates-to-the-key-vault"></a>Uložení certifikátů do trezoru klíčů

Certifikáty obsažené v souboru PFX uložte do nového trezoru klíčů pomocí tohoto skriptu:

```PowerShell
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

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Nasazení virtuálního počítače Azure pomocí zobecněné image

Tato část popisuje, jak nasadit zobecněnou image virtuálního pevného disku k vytvoření nového prostředku virtuálního počítače Azure. Pro tento proces použijeme dodanou šablonu Azure Resource Manager a skript Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Příprava šablony Azure Resource Manageru

Zkopírujte následující šablonu Správce prostředků Azure pro nasazení virtuálního pevného disku do místního souboru s názvem VHDtoImage.json. Další skript bude požadovat umístění v místním počítači používat tento JSON.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Chcete-li zadat hodnoty pro tyto parametry, upravte tento soubor:

| **Parametr** | **Popis** |
| --- | --- |
| ResourceGroupName | Název existující skupiny prostředků Azure. Obvykle použijte stejný RG jako trezor klíčů. |
| Soubor šablony | Úplný název cesty k souboru VHDtoImage.json. |
| userStorageAccountName | Název účtu úložiště. |
| sNameForPublicIP | Název DNS pro veřejnou IP adresu; musí být malá písmena. |
| subscriptionId | Identifikátor předplatného Azure. |
| Umístění | Standardní geografické umístění Azure skupiny prostředků. |
| vmName | Název virtuálního počítače. |
| název trezoru | Název trezoru klíčů. |
| vaultResourceGroup | Skupina prostředků trezoru klíčů |
| certificateUrl | Webová adresa (URL) certifikátu, včetně verze uložené v `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`trezoru klíčů, například: . |
| vhdUrl | Webová adresa virtuálního pevného disku. |
| vmSize | Velikost instance virtuálního počítače. |
| publicIPAddressName | Název veřejné IP adresy. |
| virtualNetworkName | Název virtuální sítě. |
| nicNázev | Název karty síťového rozhraní pro virtuální síť. |
| adminUserName | Uživatelské jméno účtu správce. |
| adminPassword | Heslo správce. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Nasazení virtuálního počítače Azure

Zkopírujte a upravte následující skript `$storageaccount` `$vhdUrl` a zadejte hodnoty pro proměnné a. Spusťte jej k vytvoření prostředku virtuálního počítače Azure z existujícího generalizovaného virtuálního pevného disku.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Spuštění ověření

Existují dva způsoby, jak spustit ověření na nasazené bitové kopie:

- Použití certifikačního testovacího nástroje pro certifikaci Azure Certified
- Použití rozhraní API pro vlastní testování

### <a name="download-and-run-the-certification-test-tool"></a>Stažení a spuštění certifikačního testovacího nástroje

Certifikační testovací nástroj pro Azure Certified běží na místním počítači s Windows, ale testuje virtuální počítač s Windows nebo Linux založený na Azure. Potvrzuje, že image virtuálního počítače uživatele lze použít s Microsoft Azure a že byly splněny pokyny a požadavky týkající se přípravy virtuálního pevného disku. Výstupem nástroje je zpráva o kompatibilitě, kterou nahrajete na portál Partnerského centra a požádáte o certifikaci virtuálních zařízení.

1. Stáhněte a nainstalujte nejnovější [certifikační testovací nástroj pro Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Otevřete certifikační nástroj a vyberte **Spustit nový test**.
3. Na obrazovce **Informace o testu** zadejte název **testu** pro testovací běh.
4. Vyberte **platformu** pro váš virtuální počítač, windows server nebo linux. Volba platformy ovlivní zbývající možnosti.
5. Pokud váš virtuální počítač používá tuto databázovou službu, zaškrtněte políčko **Testovat pro Azure SQL Database.**

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Připojení certifikačního nástroje k bitové kopii virtuálního zařízení

Nástroj se připojuje k virtuálním počítačům se systémem Windows pomocí [Azure PowerShellu](https://docs.microsoft.com/powershell/) a připojuje se k virtuálním počítačům s Linuxem prostřednictvím [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Připojení certifikačního nástroje k bitové kopii virtuálního počítače s Linuxem

1. Vyberte režim **ověřování SSH:** Ověřování heslem nebo ověřování souborů klíčů.
2. Pokud používáte ověřování pomocí hesla, zadejte hodnoty pro **název DNS virtuálního počítače**, uživatelské **jméno**a **heslo**. Můžete také změnit výchozí číslo **portu SSH.**

    ![Azure Certified Test Tool, ověřování heslem image virtuálního počítače s Linuxem](media/avm-cert2.png)

3. Pokud používáte ověřování pomocí souboru klíčů, zadejte hodnoty pro **název DNS virtuálního uživatele**, uživatelské **jméno**a umístění **soukromého klíče.** Můžete také zahrnout **přístupové heslo** nebo změnit výchozí číslo **portu SSH.**

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Připojení certifikačního nástroje k bitové kopii virtuálního zařízení založeného na Windows**

1. Zadejte plně kvalifikovaný **název DNS virtuálního zařízení** (například MyVMName.Cloudapp.net).
2. Zadejte hodnoty **uživatelského jména** a **hesla**.

    ![Azure Certified Test Tool, ověřování heslem image virtuálního počítače se systémem Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Spuštění certifikačního testu

Po zadaných hodnoty parametrů pro image virtuálního počítače v certifikačním nástroji, vyberte **Test připojení** k vytvoření platné připojení k virtuálnímu počítači. Po ověření připojení vyberte **další** a spusťte test. Po dokončení testu jsou výsledky testů zobrazeny v tabulce. Stav sloupec zobrazuje (Pass / Fail / Upozornění) pro každý test. Pokud některý z testů selže, vaše image _není_ certifikována. V takovém případě zkontrolujte požadavky a zprávy selhání, proveďte navrhované změny a spusťte test znovu.

Po dokončení automatického testu zadejte další informace o obrázku virtuálního počítače na dvou kartách obrazovky **Dotazník,** **Obecné hodnocení** a **Přizpůsobení jádra**a pak vyberte **Další**.

Poslední obrazovka umožňuje poskytnout další informace, jako jsou informace o přístupu SSH pro bitovou kopii virtuálního počítače s Linuxem a vysvětlení všech neúspěšných hodnocení, pokud hledáte výjimky.

Nakonec vyberte **generovat sestavu,** chcete-li stáhnout výsledky testů a soubory protokolu pro provedené testovací případy spolu s odpověďmi na dotazník. Uložte výsledky ve stejném kontejneru jako vaše Virtuální počítače.

## <a name="next-step"></a>Další krok

- [Generovat jednotné identifikátory prostředků (URI) pro každý virtuální pevný disk](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)
