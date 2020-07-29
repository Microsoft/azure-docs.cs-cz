---
title: Testovací virtuální počítač nasazený z virtuálního počítače Azure Marketplace
description: Přečtěte si, jak otestovat a odeslat nabídku virtuálních počítačů na komerčním webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: 3d4ec077ac0e92d26cf82ba96593a76a21ed885f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324671"
---
# <a name="test-virtual-machine-vm-deployed-from-vhd"></a>Testovací virtuální počítač (VM) nasazený z VHD

Tento článek popisuje, jak nasadit a otestovat virtuální počítač Azure z generalizované image virtuálního pevného disku vytvořené v předchozí části ([Vytvoření technického prostředku Azure VM)](create-azure-vm-technical-asset.md) , abyste zajistili, že bitová kopie VHD splňuje Azure Marketplace požadavky na publikování.

Provedením těchto kroků vygenerujete zprávu o kompatibilitě, která potvrzuje, že je možné použít bitovou kopii VHD na Azure Marketplace.

1. Vytvořte a Nasaďte certifikát vyžadovaný pro vzdálenou správu virtuálních počítačů, aby se Azure Key Vault.
2. Nasaďte virtuální počítač Azure z generalizované image VHD vytvořené při [vytváření technického prostředku virtuálního počítače Azure](create-azure-vm-technical-asset.md).
3. Spusťte testy na nasazeném virtuálním počítači, aby bylo zajištěno, že bitová kopie VHD bude připravena k publikování a použita k nasazení virtuálních počítačů.

## <a name="running-scripts"></a>Spouštění skriptů

Tento článek obsahuje tři skripty, které se mají spustit v prostředí PowerShell. Prostředí PowerShell pro stolní počítače funguje nejlépe, ale Azure Cloud Shell lze také použít s vybranou možností PowerShellu (v levém horním rohu okna).

1. Ujistěte se, že je PowerShell nakonfigurovaný tak, aby spouštěl skripty.

    - Vždycky otevřete PowerShell s možností **Spustit jako správce** .
    - Ujistěte se, že můžete spouštět tyto skripty: `Set-ExecutionPolicy` a `RemoteSigned` .

2. [Nainstalovat rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Nainstalujte Azure PowerShell AZ Module.
    1. **Možnost A**: ještě nejsou nainstalované žádné moduly.
        - `Install-Module -Name Az -AllowClobber -Scope AllUsers`

        Další informace najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.2.0).

    2. **Možnost B**: aktuálně používá modul AzureRM.

        - Odinstalace – AzureRM
        - Instalace-modul-název AZ-AllowClobber-Scope AllUsers
        - Enable-AzureRmAlias-Scope CurrentUser

        Další informace najdete v tématu [migrace Azure PowerShell z AzureRM na az](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-4.2.0).

4. Uložte parametry relace.

Skripty v této části používají proměnné/parametry relace. Pokud relaci zavřete, parametry se vymažou. Pro spuštění všech skriptů doporučujeme použít jednu relaci, aby se předešlo chybám hodnot parametrů. Pokud to není možné, budete muset parametry znovu inicializovat při otevírání nové relace, zejména pro pozdější skripty.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Vytvoření a nasazení certifikátů pro Azure Key Vault

Tato část popisuje, jak vytvořit a nasadit certifikáty podepsané svým držitelem, které jsou nutné k nastavení připojení služby Vzdálená správa systému Windows (WinRM) k virtuálnímu počítači hostovanému v Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Vytvořit certifikáty pro Azure Key Vault

Tento proces se skládá ze tří kroků:

1. Vytvořte certifikát zabezpečení.
2. Vytvořte Azure Key Vault pro uložení certifikátu.
3. Certifikáty uložte do trezoru klíčů.

Pro tuto práci můžete použít buď novou, nebo existující skupinu prostředků Azure.

#### <a name="create-the-security-certificate"></a>Vytvořit certifikát zabezpečení

Spuštěním tohoto skriptu vytvořte soubor certifikátu (. pfx) v místní složce. Certifikát patří plánovanému virtuálnímu počítači Azure, který se nasadí z image VHD. Virtuální počítač bude potřebovat název, umístění a heslo, jak je uvedeno v parametrech skriptu. Upravte následující Azure PowerShell **skript pro vytváření certifikace** a zadejte správné hodnoty parametrů skriptu zobrazených v tabulce.

| **Parametr** | **Popis** |
| --- | --- |
| $certroopath | Místní složka, do které se uloží soubor. pfx |
| $location | Jedno ze standardních zeměpisných míst Azure. |
| $vmName | Název plánovaného virtuálního počítače Azure. |
| $certname | Název certifikátu; musí odpovídat plně kvalifikovanému názvu domény plánovaného virtuálního počítače. |
| $certpassword | Heslo pro certifikáty se musí shodovat s heslem použitým pro plánovaný virtuální počítač. |
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
> V průběhu těchto kroků nechejte stejnou relaci konzoly Azure PowerShell otevřít a spustit, aby se zachovaly hodnoty různých parametrů.

> [!WARNING]
> Pokud tento skript uložíte, uložte ho pouze do zabezpečeného umístění, protože obsahuje informace o zabezpečení (heslo).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Vytvoření trezoru klíčů Azure pro uložení certifikátu

Zkopírujte obsah šablony níže do souboru na místním počítači. V následujícím příkladu skriptu je tento prostředek `C:\certLocation\keyvault.json` ).

```JSON
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

Úpravou a spuštěním následujícího skriptu Azure PowerShell vytvořte Azure Key Vault a přidruženou skupinu prostředků. Nahraďte hodnoty parametrů, které jsou uvedeny v následující tabulce.

| **Parametr** | **Popis** |
| --- | --- |
| $postfix | Náhodný numerický řetězec připojený k identifikátorům nasazení. |
| $rgName | Název skupiny prostředků Azure (RG), která se má vytvořit |
| $location | Jedno ze standardních zeměpisných míst Azure. |
| $kvTemplateJson | Cesta k souboru (keyvault.json), který obsahuje šablonu Správce prostředků pro Trezor klíčů. |
| $kvname | Název nového trezoru klíčů|
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
     az group create --name $rgName --location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys Decrypt,Encrypt,UnwrapKey,WrapKey,Verify,Sign,Get,List,Update,Create,Import,Delete,Backup,Restore,Recover,Purge -PermissionsToSecrets Get,List,Set,Delete,Backup,Restore,Recover,Purge

```

#### <a name="store-the-certificates-to-the-key-vault"></a>Uložení certifikátů do trezoru klíčů

Certifikáty obsažené v souboru. pfx uložte do nového trezoru klíčů pomocí tohoto skriptu:

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

## <a name="deploy-an-azure-vm-from-your-generalized-vhd-image"></a>Nasazení virtuálního počítače Azure z generalizované image VHD

Tato část popisuje, jak nasadit zobecněnou image virtuálního pevného disku pro vytvoření nového prostředku virtuálního počítače Azure. Pro tento proces budeme používat dodanou šablonu Azure Resource Manager a skript Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Příprava šablony Azure Resource Manager

Zkopírujte jednu z následujících šablon Azure Resource Manager pro nasazení VHD (buď pro Windows nebo Linux) do místního souboru s názvem VHDtoImage.jsv. Další skript požádá o umístění v místním počítači, aby se tento kód JSON použil.

#### <a name="for-windows-based-vms"></a>Pro virtuální počítače se systémem Windows

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

#### <a name="for-linux-based-vms"></a>Pro virtuální počítače se systémem Linux

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
            "defaultValue": "linux",
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
                        "enabled": false,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Zkopírujte a upravte následující skript, který poskytne hodnoty pro tyto parametry:

| **Parametr** | **Popis** |
| --- | --- |
| ResourceGroupName | Název existující skupiny prostředků Azure Obvykle používejte stejný RG jako Trezor klíčů. |
| TemplateFile | Úplná cesta k souboru VHDtoImage.jsv. |
| userStorageAccountName | Název účtu úložiště |
| sNameForPublicIP | Název DNS pro veřejnou IP adresu; musí být malými písmeny. |
| subscriptionId | Identifikátor předplatného Azure. |
| Umístění | Standardní geografické umístění skupiny prostředků v Azure. |
| vmName | Název virtuálního počítače |
| vaultName | Název trezoru klíčů |
| vaultResourceGroup | Skupina prostředků trezoru klíčů. |
| certificateUrl | Webová adresa (URL) certifikátu, včetně verze uložené v trezoru klíčů, například: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` . |
| vhdUrl | Webová adresa virtuálního pevného disku. |
| vmSize | Velikost instance virtuálního počítače |
| publicIPAddressName | Název veřejné IP adresy. |
| virtualNetworkName | Název virtuální sítě. |
| nicName | Název karty síťového rozhraní pro virtuální síť. |
| adminUserName | Uživatelské jméno účtu správce. |
| adminPassword | Heslo správce. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Nasazení virtuálního počítače Azure

Zkopírujte a upravte následující skript, který poskytne hodnoty pro `$storageaccount` proměnné a `$vhdUrl` . Spusťte ho k vytvoření prostředku virtuálního počítače Azure ze stávajícího zobecněného virtuálního pevného disku.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

# Full pathname to the file VHDtoImage.json. inserted these highlighted lines
$templateFile = "$certroopath\VHDtoImage.json"

# Size of the virtual machine instance.
$vmSize = "Standard_D2s_v3"

# Name of the public IP address.
$publicIPAddressName = "myPublicIP1"

# Name of the virtual network
$virtualNetworkName = "myVNET1"

# Name of the network interface card for the virtual network
$nicName = "myNIC1"

# Username of the administrator account
$adminUserName = "isv"

# The OS of the virtual machine
$osType = "windows"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl “$objAzureKeyVaultSecret.Id” -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName"myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword “$pwd"

```

## <a name="run-tests-on-the-deployed-vm"></a>Spuštění testů na nasazeném virtuálním počítači

### <a name="download-and-run-the-certification-test-tool"></a>Stažení a spuštění nástroje certifikace test

Nástroj certifikace pro certifikaci pro Azure Certified je nástroj pro samočinný test, který běží na místním počítači s Windows, ale testuje virtuální počítač se systémem Windows nebo Linux na platformě Azure. Potvrzuje, že vaše uživatelská image virtuálního počítače se dá používat s Microsoft Azure a že se splnily pokyny a požadavky týkající se přípravy vašeho virtuálního pevného disku. Tento nástroj zajistí, že je váš virtuální počítač připravený k publikování na základě Azure Marketplace požadavků. "

1. Stáhněte a nainstalujte si nejnovější [Nástroj pro testování certifikace pro certifikaci Azure](https://www.microsoft.com/download/details.aspx?id=44299).
2. Otevřete nástroj certifikace a pak vyberte **Spustit nový test**.
3. Na obrazovce **informace o testu** zadejte **název testu** testovacího běhu.
4. Vyberte **platformu** pro váš virtuální počítač, buď Windows Server nebo Linux. Volba vaší platformy má vliv na zbývající možnosti.
5. Pokud váš virtuální počítač používá tuto databázovou službu, zaškrtněte políčko **test pro Azure SQL Database** .

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Připojení certifikačního nástroje k imagi virtuálního počítače

Nástroj se připojuje k virtuálním počítačům s Windows pomocí [Azure PowerShell](https://docs.microsoft.com/powershell/) a připojuje se k virtuálním počítačům Linux prostřednictvím [SSH.NET](https://www.ssh.com/ssh/protocol/). Vyberte jednu z následujících dvou možností, buď Linux, nebo Windows.

#### <a name="option-1-connect-the-certification-tool-to-a-linux-vm-image"></a>Možnost 1: připojení certifikačního nástroje k imagi virtuálního počítače se systémem Linux

1. Vyberte režim **ověřování SSH** : ověřování hesla nebo ověření souboru klíče.
2. Pokud používáte ověřování na základě hesla, zadejte hodnoty pro **název DNS virtuálního počítače**, **uživatelské jméno**a **heslo**. Můžete také změnit výchozí číslo **portu SSH** .

    ![Nástroj pro testování Azure Certified, ověřování hesla pro image virtuálního počítače se systémem Linux](media/avm-cert2.png)

3. Pokud používáte ověřování na základě klíčového souboru, zadejte hodnoty pro **název DNS virtuálního počítače**, **uživatelské jméno**a umístění **privátního klíče** . Můžete také zahrnout **heslo** nebo změnit výchozí číslo **portu SSH** .

#### <a name="option-2-connect-the-certification-tool-to-a-windows-based-vm-image"></a>Možnost 2: připojení certifikačního nástroje k imagi virtuálního počítače se systémem Windows

1. Zadejte plně kvalifikovaný **název DNS virtuálního počítače** (například MyVMName.cloudapp.NET).
2. Zadejte hodnoty **uživatelského jména** a **hesla**.

    ![Nástroj pro testování Azure Certified, ověřování hesla bitové kopie virtuálního počítače se systémem Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Spustit test certifikace

Po zadání hodnot parametrů pro vaši image virtuálního počítače v nástroji certifikace vyberte **Test připojení** a vytvořte platné připojení k vašemu virtuálnímu počítači. Po ověření připojení vyberte **Další** a spusťte test. Po dokončení testu jsou výsledky testu zobrazeny v tabulce. U každého testu se ve sloupci Stav zobrazuje (úspěch/chyba/upozornění). Pokud některý z testů selže, váš obrázek _není certifikovaný._ V takovém případě zkontrolujte zprávy o požadavcích a chybách, proveďte navrhované změny a spusťte test znovu.

Po dokončení automatizovaného testu zadejte další informace o imagi virtuálního počítače na dvou kartách obrazovky **dotazníku** , **Obecné vyhodnocení** a **přizpůsobení jádra**a pak vyberte **Další**.

Poslední obrazovka vám umožní zadat další informace, například informace o přístupu SSH pro image virtuálního počítače se systémem Linux, a vysvětlení všech neúspěšných posouzení, pokud hledáte výjimky.

Nakonec vyberte možnost **Generovat sestavu** pro stažení výsledků testů a souborů protokolu pro provedené testovací případy spolu s vašimi odpověďmi na dotazník. Výsledky uložte do stejného kontejneru jako vaše virtuální pevné disky.

## <a name="next-step"></a>Další krok

- [Běžné problémy s identifikátorem URI SAS a opravy](common-sas-uri-issues.md)
